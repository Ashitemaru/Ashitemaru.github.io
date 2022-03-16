---
title: 操作系统课程大实验摸索实录
date: 2022-02-28 17:35:28
category:
    - 开发杂谈
---

本来就是为了躲避考试来做的大实验，感觉也会是挺有意思的一段经历，不妨记录一下。

<!-- more -->

# 配置 rCore 环境【2022.02.28】

在做大实验之前，需要先把五次小实验做完，那么第一件事就是去配置 rCore 环境。Rust 之前就配置好了，所以主要是配置 qemu。按照正常的流程，就是下载打包的 5.0.0 版本的 qemu 源码，并编译，但是很快就炸了个错：

{% codeblock lang:bash Bash %}
# ashitemaru @ LAPTOP-VQNVF4EV in ~/qemu-5.0.0 [17:38:32]
$ make -j$(nproc)
make[1]: Entering directory '/home/ashitemaru/qemu-5.0.0/slirp'
make[1]: Nothing to be done for 'all'.
make[1]: Leaving directory '/home/ashitemaru/qemu-5.0.0/slirp'
        CHK version_gen.h
  LINK    riscv32-softmmu/qemu-system-riscv32
  LINK    riscv64-softmmu/qemu-system-riscv64
/usr/bin/ld: /home/ashitemaru/qemu-5.0.0/slirp/libslirp.a(misc.o): in function `g_spawn_async_with_fds_slirp':
/home/ashitemaru/qemu-5.0.0/slirp/src/misc.c:173: undefined reference to `g_spawn_async_with_fds'
/usr/bin/ld: /home/ashitemaru/qemu-5.0.0/slirp/libslirp.a(misc.o): in function `g_spawn_async_with_fds_slirp':
/home/ashitemaru/qemu-5.0.0/slirp/src/misc.c:173: undefined reference to `g_spawn_async_with_fds'
collect2: error: ld returned 1 exit status
collect2: error: ld returned 1 exit status
make[1]: *** [Makefile:208: qemu-system-riscv64] Error 1
make[1]: *** [Makefile:208: qemu-system-riscv32] Error 1
make: *** [Makefile:527: riscv32-softmmu/all] Error 2
make: *** Waiting for unfinished jobs....
make: *** [Makefile:527: riscv64-softmmu/all] Error 2
{% endcodeblock %}

链接器找不到一个函数符号，找了一下各路文档，这个函数是 GNU libc 导出的一个符号。遇到这种 undefined 的问题，第一反应就是去重装一遍这个动态库，于是就去了 GNU libc 的官网，clone 了一份源码，并且开始编译。

但是这里构建的时候又炸了一个错，是找不到 `libiconv` 符号。

这里就开始感到不对了，因为他不是报找不到动态库，而是找不到符号。而且我当时还特意去 `/usr/local/lib` 底下用下述命令确认了这个符号确实导出了：

{% codeblock lang:bash Bash %}
nm -C libiconv-2.0.so | grep libiconv
{% endcodeblock %}

这个时候我想起来之前做计组的时候实际上是在 WSL 里装过一个低版本的 qemu 的，当时安装的时候报错找不到 libiconv 的库（不是找不到符号，而是找不到动态库），于是我自己 clone 了一份源码，在 `/usr/local/lib` 底下装了一份 libiconv。

这个时候我已经差不多反应过来了，因为 libiconv 应该是系统自己有的一个动态库，如果我再装一份的话，很有可能因为冲突出现问题。

随后就去翻 GNU libc 的构建 config 文件，发现其找寻 libiconv 是先搜寻系统自带的动态库目录，随后去 `/usr/local/lib` 下找。那这样的话，其很有可能是定位到了系统自带的 libiconv 库。然而计组的 qemu 似乎是先寻找他自带的一个动态库目录和 `/usr/local/lib`，并且不去寻找系统自带的动态库目录。然而这也是合理的，因为当时计组推荐的就是无论在什么系统上，只要下载他的一个压缩包，一切东西都不要配。但是可能是打包失误之类，libiconv 意外没有打包进来。

那这样这个问题就已经差不多明白了，就是系统的 libiconv 出现了问题（我猜测是 C 和 C++ 函数签名的问题，C++ 编译出来的函数签名会有前缀）。而计组当时没有出问题的原因是他在构建的时候就没有去找系统自带的动态库目录，所以缺失了库文件。但是 OS 装 qemu 的时候找到了系统的 libiconv，但是这是有问题的一个动态库，用不了，连带着 GNU libc 没法用，所以最后就炸了。

解决方式，就是重装 WSL。系统出问题还不重装系统？

结果从重装，配置 C 环境，配置 Python 环境，配置终端美化插件，配置 Vim 到最后装完 qemu 跑起来 rCore，一共没有用到两小时。

# 分时多任务【2022.03.07】

第一个小作业，目标是实现基于 stride 算法的进程调度。不过在具体工作之前，我们还是需要弄明白框架里一部分功能是如何实现的。

## 特权级切换

目前我们只考虑在 U 态下出发 Trap 到 S 态，那么在处理这个 Trap 的时候，硬件需要分别完成：

- 修改相应的 CSR 寄存器相关字段
    - `sstatus` 的 `SPP` 字段置为 CPU 当前特权级
    - `sepc` 置为处理完 Trap 后接着要处理的指令位置
    - `scause` 和 `stval` 存储 Trap 的原因和相关信息
- CPU 跳转到 `stvec` 指定的 Trap 处理程序入口地址并切换特权级
- 切换到内核栈，在此处保存原先上下文
    - 上下文一般包括此处所有的通用寄存器以及 `sstatus` 和 `sepc` 寄存器
- 处理 Trap
- 恢复上下文，释放分配的内核栈空间并切换到用户栈
- 执行 `sret` 命令回到原先环境
    - 根据 `sstatus` 的 `SPP` 字段重置特权级
    - 跳转到 `sepc` 指向的地址继续执行

这个实验之中为了简化，`stvec` 寄存器处于 Direct 模式，该模式下 Trap 处理程序入口地址计算较为简单。

基于这样的处理流程，我们需要在 OS 启动初始化的时候就将标记 Trap 处理程序起始地址的符号 `__alltraps` 事先引入到 `stvec` 寄存器中：

{% codeblock lang:rust Rust %}
// ch2 /trap/mod.rs
core::arch::global_asm!(include_str!("trap.S"));

pub fn init() {
    extern "C" {
        fn __alltraps();
    }
    unsafe {
        stvec::write(__alltraps as usize, TrapMode::Direct);
    }
}
{% endcodeblock %}

`__alltrap` 使用汇编代码实现，其负责了保存上下文并切换到 Trap 处理程序的任务。这段汇编代码中唯一要注意的是 `sscratch` 寄存器的含义，该寄存器在 U 态下保存的是内核栈栈顶位置，我们可以利用该寄存器实现换栈。

---

`__alltrap` 的最后会 `call trap_handler` 触发 Rust 编写的 Trap 处理程序。

Trap 处理程序会分情况处理：

- 如果仅仅是触发了系统调用，那么 Trap 处理程序申请系统调用后返回到 `__alltrap` 下面一行，即 `__restore` 对应的代码段
- 如果是当前用户应用有不可恢复的错误，则打印提示信息，调用 `run_next_app` 切换到下一个用户应用
- 如果是未知错误，OS 立刻停止

这里 `__restore` 负责的任务则是恢复上下文，换回用户栈并在最后调用 `sret` 回到 U 态。

在第一种情况下，Trap 处理程序会直接调用 `__restore`。第二种情况下，其会调用 `run_next_app`，该函数会间接调用 `__restore`：

{% codeblock lang:rust Rust %}
// ch2 /batch.rs

pub fn run_next_app() -> ! {
    let mut app_manager = APP_MANAGER.exclusive_access();
    let current_app = app_manager.get_current_app();
    unsafe {
        app_manager.load_app(current_app);
    }
    app_manager.move_to_next_app();
    drop(app_manager);
    // before this we have to drop local variables related to resources manually
    // and release the resources
    extern "C" {
        fn __restore(cx_addr: usize);
    }
    unsafe {
        __restore(KERNEL_STACK.push_context(TrapContext::app_init_context(
            APP_BASE_ADDRESS,
            USER_STACK.get_sp(),
        )) as *const _ as usize);
    }
    panic!("Unreachable in batch::run_current_app!");
}
{% endcodeblock %}

前面一段就是加载下一个用户应用的代码，最主要关注其如何调用 `__restore`。显然其在内核栈中压入了新的 Trap 上下文，并且将压入后的内核栈顶地址交给了 `__restore`（使用 `a0` 寄存器传递）。那这样就需要关注 `app_init_context` 的实现：

{% codeblock lang:rust Rust %}
// ch2 /trap/context.rs

pub fn app_init_context(entry: usize, sp: usize) -> Self {
    let mut sstatus = sstatus::read();
    sstatus.set_spp(SPP::User);
    let mut cx = Self {
        x: [0; 32],
        sstatus,
        sepc: entry,
    };
    cx.set_sp(sp);
    cx
}
{% endcodeblock %}

这个函数接受程序入口地址和栈顶指针作为参数，构造了一个用于初始化用户程序运行的 Trap 上下文。

将这个上下文压入内核栈并调用 `__restore`，就可以完成用户程序的初始化。尽管异常处理后的恢复和用户程序初始化并不是同一种需求，但是其处理逻辑类似，所以完全可以复用同一份代码。

## 任务间切换

现在我们需要实现多个用户程序之间的切换。虽然 Trap 处理也会涉及到用户程序间切换，但是一般的任务间切换不涉及到特权级切换，这是与 Trap 处理不同的。

这里使用汇编编写了任务切换需要的代码，入口符号为 `__switch`。处理逻辑为：

- 保存其实环境运行上下文，不过这里只需要保存 12 个调用者保存寄存器和 `ra` 及 `sp` 寄存器即可
- 恢复目标环境运行上下文，并且调用 `ret` 以返回到指定的地址运行目标环境

这里就可以注意到保存 `ra` 寄存器的必要性了，其作用就是让 `__switch` 返回的时候能正确跳转到目标环境的地址。

## 多任务管理

我们时常会遇到需要等待外设才能继续执行的任务，这种时候如果保持等待则可能导致资源浪费，因为我们完全可以在等待的时候将 CPU 资源交给其他任务。为了实现灵活的多任务切换，我们需要了解两个系统调用。

在 U 态下触发 `sys_yield` 系统调用就会将 CPU 运行权限交出，由 OS 授予另外一个应用占用 CPU。而如果触发 `sys_exit` 系统调用也会交出 CPU 运行权限，但是 `sys_yield` 表示该任务暂未结束，只不过为了避免浪费暂且停止，`sys_exit` 则表明任务结束，将来也不会取回运行权限。

同样，OS 有权限将 CPU 运行权限交予一个暂停的任务。

每一个任务都有未初始化、准备运行、运行中和终结四种状态。在上述的管理原则下，这些状态的转移图为：

![](/uploads/os-exp/1.png)

## 初始化运行环境

初始化流程中最重要的就是任务管理器 `TASK_MANAGER` 的初始化：

{% codeblock lang:rust Rust %}
// ch3 /task/mod.rs

lazy_static! {
    pub static ref TASK_MANAGER: TaskManager = {
        let num_app = get_num_app();
        let mut tasks = [TaskControlBlock {
            task_cx: TaskContext::zero_init(),
            task_status: TaskStatus::UnInit,
        }; MAX_APP_NUM];
        for (i, t) in tasks.iter_mut().enumerate().take(num_app) {
            t.task_cx = TaskContext::goto_restore(init_app_cx(i));
            t.task_status = TaskStatus::Ready;
        }
        TaskManager {
            num_app,
            inner: unsafe {
                UPSafeCell::new(TaskManagerInner {
                    tasks,
                    current_task: 0,
                })
            },
        }
    };
}
{% endcodeblock %}

这里主要注意各个 `TaskControlBlock` 的 `task_cx` 属性是如何初始化的。首先调用 `init_app_cx` 来创建用户程序上下文，其作用是在对应用户程序的内核栈内压入一个初始化的 Trap 上下文并返回此时的内核栈栈顶。

之后 `goto_restore` 构建 Task 上下文，该上下文中 12 个通用寄存器暂时置零，`ra` 置 `__restore` 的地址，`sp` 置刚刚得到的内核栈栈顶地址。

---

这样完成所有的初始化后，OS 通过 `run_first_task` 开始运行用户程序。其最主要的代码是这样的一段：

{% codeblock lang:rust Rust %}
// ch3 /task/mod.rs

let mut _unused = TaskContext::zero_init();
// before this, we should drop local variables that must be dropped manually
unsafe {
    __switch(&mut _unused as *mut TaskContext, next_task_cx_ptr);
}
panic!("unreachable in run_first_task!");
{% endcodeblock %}

可以看到我们这里手动调用了 `__switch`，并且将第一个任务的 Task 上下文作为目标地址传入。由于目前所有任务的 Task 上下文中保存的 `ra` 均为 `__restore` 的地址，这样就保证了最后 `__switch` 返回到 `__restore` 处，完成任务的初始化后通过 `sret` 跳转到用户代码处开始执行任务。

## 时钟中断

现代的 OS 在面对很少主动触发 `sys_yield` 的用户程序的时候，往往是强制收回 CPU 使用权以保证硬件利用率。这意味着，在这样的条件下，所有应用随时都有可能被强制停止，这样的调度方式就是**抢占式调度**。

实现 OS 强制收回使用权的方式就是，设置一个计时器，每间隔一个固定的时间（比如 10 毫秒）就强制暂停当前任务并开启下一个任务。

我们可以通过 `time::read()` 方法来获取 `mtime` 寄存器的值，该寄存器保存了 OS 从上电到现在一共经过了多少个 **OS 时钟周期**（和 CPU 主频时钟不同）。当其大于 `mtimecmp` 寄存器的时候触发时钟中断，重置 `mtimecmp` 开始下一轮计时。

## Rust 的静态变量

大概是在摸索 rCore 里面用户栈实现的时候，将其中一部分代码写了一个简化版，结果无意中触发了段错误。后面大致摸索明白了原因，这里也就写一个简单的记录，见 [这篇博客](/2022/03/09/static-var-in-rust)。