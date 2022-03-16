---
title: Rust 中的静态变量
date: 2022-03-09 01:01:50
category:
    - 开发杂谈
---

摸索 rCore 的用户栈实现的时候发现的一些好玩东西。

<!-- more -->

# 会莫名段错误的内存写入

用户栈本身不难实现：

{% codeblock lang:rust Rust %}
const STACK_SIZE: usize = 1024;

struct Stack {
    data: [usize; STACK_SIZE],
}

static STACK: Stack = Stack {
    data: [0; STACK_SIZE],
};

impl Stack {
    fn get_sp(&self) -> usize {
        self.data.as_ptr() as usize + STACK_SIZE
    }

    fn push_context(&self, cx: usize) -> usize {
        let cx_ptr = (self.get_sp() - core::mem::size_of::<usize>()) as *mut usize;
        unsafe {
            *cx_ptr = cx;
        }
        cx_ptr as usize
    }
}

fn main() {
    println!("origin sp: {}", STACK.get_sp());
    let cx: usize = 0;
    println!("context: {}", cx);
    println!("returned sp: {}", STACK.push_context(cx));
    println!("processed sp: {}", STACK.get_sp());
}
{% endcodeblock %}

这段代码是会报错的，而且很容易定位到是 `push_context` 方法中 `unsafe` 块触发了段错误。

# 静态变量的不可变性


