---
title: 云游戏相关知识杂谈笔记
date: 2023-09-20 18:07:07
mathjax: true
category:
    - 【学习笔记】计算机
---

在正式加入云游戏相关项目之前，学长还是扔了一些学习材料过来让我补一下基础，这里就正常开一个笔记来记录一下学习过程了。大概先从显示器的一些工作原理和 WebRTC 相关知识开始吧。

<!-- more -->

# 垂直同步与 G-sync

https://www.youtube.com/watch?v=dnsPyyaNCWc&list=PL7mmImi_1wpMVhVpBWr3Bob7kdchdDEoX&index=23

主要分析了游戏之中画面撕裂的问题成因和相关的解决方案，这里也就主要关注显卡和显示器的配合问题。

首先需要明确的是，在游戏过程中，每一帧都是显卡负责绘制的，而显卡每秒绘制出的帧的数目就是**帧率**，简称 **FPS**。而显示器在接收到显卡所提供的帧的时候，需要在屏幕上绘制帧。当前所有的显示器采用的绘制方法都是**逐行扫描**，即光标从屏幕左上角开始向右向下逐个扫过像素点绘制帧，在扫描到右下角的时候，一帧绘制完成。绘制完一帧之后，显示器将光标从右下角重置到左上角的过程称为 **VBlank**。此外，显示器一秒内能够在屏幕上逐行扫描出的帧的数量称为**刷新率**。

这里有两个比较重要的概念，就是帧率和刷新率。需要注意的是，帧率描述显卡绘制帧的性能，刷新率描述显示器扫描帧的性能。

在真实的游戏场景中，由于游戏场景的复杂性不同，显卡所承受的渲染负载不同，显卡的帧率常常会波动。然而相对应地，显示器的刷新率一般是不波动的。这意味着，我们需要某种机制平衡波动的帧率和固定的刷新率。一般而言，常见的系统会采用**帧缓冲**机制来完成这一任务。

帧缓冲机制中一般会设置一个前缓冲区和一个后缓冲区，显示器仅仅会读取前缓冲区之中存放的帧，显卡只会修改后缓冲区中的帧。在这个机制之下，将后缓冲区中的帧传递到前缓冲区的操作称为**帧传递**。显然，何时进行帧传递就构成了这个系统的核心机制。

如果刷新率和帧率一致，那么我们只需要在显卡绘制完毕之后进行帧传递即可，此时显示器也应当恰好刷新完毕进入 VBlank，恰好准备绘制新帧。然而如果秉持着显卡绘制完毕即帧传递的策略，在帧率比刷新率更高的时候，后缓冲区中已经形成新的帧的时候显示器还没有进入 VBlank，此时将新帧传递入前缓冲区，显示器上则会出现上半为旧帧而下半为新帧的情况，即所谓的画面撕裂。另外，帧率低于刷新率的时候，显示器已经进入 VBlank 的时候后缓冲区依然没有触发帧传递，所以显示器只能重新绘制先前帧，此时新帧由显卡绘制完毕，帧传递后会出现显示器上上半为旧帧而下半为新帧的情况，同样也是画面撕裂。

总之，在这一策略之下，只要刷新率不等于帧率就会出现画面撕裂问题：

![](/uploads/note-of-media/1.png)

这里显示的是显示器角度的时间轴，A 到 F 各个色块则显示各个帧在前缓冲区中存续的时间。可以看到，无论是帧率高于刷新率还是低于刷新率，显示器所显示的画面都会有撕裂现象。

## 垂直同步

所谓的垂直同步策略，是在帧率高于刷新率的时候，强制令显卡空转等待 VBlank 到来后才触发帧交换。简而言之，就是强制令显卡等待显示器：

![](/uploads/note-of-media/2.png)

这里 G 区域就是强制令显卡等待的时间块。

而在帧率低于刷新率的时候，则强制令显示器再显示一次旧帧，若显示完显卡依然没有绘制完毕则一直显示，直到某一次显示的时候显卡绘制完了。此时显卡空转等待 VBlank 触发帧交换。简而言之，就是等效降低显示器刷新率，将问题化归为帧率高于刷新率的问题，再强制令显卡等待显示器：

![](/uploads/note-of-media/3.png)

这里 G 区域就是强制令显卡等待的时间块。

然而垂直同步的弊端也是显而易见的，即实际帧率一定会被锁定在显示器刷新率以下，事实上是一种卡死显卡性能换画面完整的交易。

在这个基础上，Nvidia 推出了**自适应垂直同步**方案，事实上就是在垂直同步系统之前加一个是否开启垂直同步的判断器。当显卡帧率高于刷新率的时候，该系统打开垂直同步令显卡空转等待显示器，而显卡帧率低于刷新率的时候，则关闭垂直同步，立即触发帧传递而不让显示器重复绘制先前帧。

但是无论如何改进，依然无法改变垂直同步具有延迟的缺陷，即 MTP latency 过高。简而言之，当场景变化较大的时候，即使显卡已经快速绘制出来还是有可能因为垂直同步被迫稍晚传递给显示器，导致用户操作和屏幕反应之间存在较大延迟。这对实时性高的游戏（如 FPS、Moba）是致命的。

## 三重缓冲

为了解决垂直同步的延迟问题，有一种方式是将一般的前缓冲区和后缓冲区设计中加入新的中缓冲区构成三重缓冲。简单而言就是再设置一个缓冲区来存放显卡提前绘制好的帧，可以一定程度缓解显卡帧率突涨的问题。然而这是治标不治本的，也就是说当显卡性能保持在高位过长时间后，后缓冲区和中缓冲区都已经满了，此时显卡依然不得不空转等待显示器。

Nvidia 为了解决三重缓冲这种治标不治本的问题，提出了**快速垂直同步（无阻塞垂直同步）**方案，即允许中缓冲区和后缓冲区交换，当中后缓冲区全满的时候再次交换中后缓冲区，显卡用新的帧覆盖掉本应该交给显示器绘制的帧。此时中缓冲区始终有一个可以交给前缓冲区，即显示器绘制的帧，而显卡也始终没有等待显示器。

无阻塞垂直同步虽然做到了显示器和显卡都处于满负荷状态，但是两者之间不同步的差距则代偿给了帧丢失，也就是先前说的，如果中后缓冲区都满了，就会用新帧覆盖掉原本交给显示器的帧，从而导致实际的丢帧。

## G-sync 与 Freesync

实际上上述所有方案都有治标不治本的问题。为了从根源上解决画面撕裂又不产生过分的延迟，Nvidia 直接从显示器一侧尝试解决问题，即 G-sync 方案（该方案核心为**自适应显示器刷新率**，即 VRR，所以有时也称为 VRR 方案）。该方案的核心是令显示器可以调节 VBlank 时长，从而在显卡帧率较低的时候通过长 VBlank 时间来让“显示器等显卡”。

也就是说，垂直同步核心是显卡空转等显示器，而 G-sync 则是显示器空转等显卡。

然而在实际情况下，即使通过 VRR 也不可能让刷新率超越额定刷新率，所以当显卡帧率高于刷新率的时候依然会出现画面撕裂（也就是所说的 VRR 只能解决显卡帧率低于刷新率的情况）。所以厂家会推荐打开 G-sync 的时候同时打开垂直同步。

另外一个角度，过长时间的 VBlank 有时也不现实，因为显示器主板也有最低刷新率要求。这意味着如果帧率显著低于刷新率，打开 G-sync 甚至可能因刷新率锚定帧率导致更严重的后果。这个时候只能打开**低帧补偿（Low Frame Compensation / LFC）**，即通过多次扫描显示一帧，等效倍增显示器刷新率。然而 LFC 需要预测显卡的性能，因为毕竟需要提前决定到底下一帧需要几次扫描，一旦预测失误，依然会遇到问题。

所以 VRR 技术即使可以做到在一定范围内让刷新率锚定帧率，但是这依然需要帧率相对稳定。这时候就有个有趣的矛盾，既然能做到帧率相对稳定，那么即使画面撕裂也都会出现在屏幕上下方贴近边框的地方，那我为什么不能直接接受垂直同步之类的方案呢？

所以现今的解决方案中就是不能在低延迟、无撕裂上面做到兼顾，G-sync 也只是众多权衡中的一种。

最后，说明一下 Freesync。事实上 G-sync 是 Nvidia 授权部分显示器型号内置 Nvidia 芯片来达成的技术，是一种需要 Nvidia 官方认证才能采用的技术。而 Freesync 是开源的技术标准，所有显示器均可以使用，目前由 AMD 主要采用。而两者的技术框架几乎一致，仅仅在细节上有一定差别。

# WebRTC Kalman filter render time estimation

参考文章：https://www.jianshu.com/p/0bc6a4998b32

由于这篇文章已经是 2020 年的文章了，感觉 WebRTC 源码也已经做了很大的更新，所以还是以 2023.09 的最新源码为准，文章终究是参考。

## 形成帧的过程中播放延迟的传递

这篇文章主要介绍的是 WebRTC playout delay 的计算原理。WebRTC 源码之中 playout delay 表示为两个 `double` 浮点数构成的元组，分别表示最小播放延迟和最大播放延迟。一般而言，playout delay 会携带在 RTC 扩展头之中，如果没有携带，则使用默认初始化。

源码上的体现是：

{% codeblock lang:c++ C++ %}
// video_timing.h
class RTC_EXPORT VideoPlayoutDelay {
public:
    // Maximum supported value for the delay limit.
    static constexpr TimeDelta kMax = TimeDelta::Millis(10) * 0xFFF;

    // ...

private:
    TimeDelta min_ = TimeDelta::Zero();
    TimeDelta max_ = kMax;
};

// rtp_video_header.h
struct RTPVideoHeader {
private:
    absl::optional<VideoPlayoutDelay> playout_delay;
    // ...
};

// encoded_image.h
class RTC_EXPORT EncodedImage {
private:
    absl::optional<VideoPlayoutDelay> playout_delay_;
    // ...
};

RtpFrameObject::RtpFrameObject(/* ... */)
    : first_seq_num_(first_seq_num),
      last_seq_num_(last_seq_num),
      last_packet_receive_time_(last_packet_receive_time),
      times_nacked_(times_nacked) {
    // ...
    SetPlayoutDelay(rtp_video_header_.playout_delay);
    // ...
}
{% endcodeblock %}

这里主要观察最后一个构造函数的逻辑，在 `PacketBuffer` 模块将数据包排列组合出一帧的最后会创建一个 `RtpFrameObject` 实例来管理这一帧，此时会使用 `SetPlayoutDelay` 函数设定播放延迟。同时可以注意到，`RTPVideoHeader` 结构体中如果没有设定播放延迟，则默认为 `{0, +Infinity}`，也就是说创建帧对象的时候如果没有设定播放延迟则默认设定为 `{0, +Infinity}`。

在帧构建完成之后会调用 `VideoReceiveStream2::OnCompleteFrame` 函数将新帧插入帧队列：

{% codeblock lang:c++ C++ %}
void VideoReceiveStream2::OnCompleteFrame(
    std::unique_ptr<video_encoding::EncodedFrame> frame,
) {
    // ...

    if (absl::optional<VideoPlayoutDelay> playout_delay =
        frame->EncodedImage().PlayoutDelay()) {
        frame_minimum_playout_delay_ = playout_delay->min();
        frame_maximum_playout_delay_ = playout_delay->max();
        UpdatePlayoutDelays();
    }

    // ...
}
{% endcodeblock %}

这里涉及到了 `absl::optional` 的隐式类型转换，观察一下库函数就有：

{% codeblock lang:c++ C++ %}
// optional::operator bool()
//
// Returns false if and only if the `optional` is empty.
//
//   if (opt) {
//      // do something with *opt or opt->;
//   } else {
//      // opt is empty.
//   }
//
constexpr explicit operator bool() const noexcept { return this->engaged_; }
{% endcodeblock %}

也就是说只有这个 `absl::optional` 容器内部没有存放任何值的时候才会返回 `false`，这也就对应着播放延迟没有在 RTC 扩展头之中携带。

这里可以注意当 `playout_delay_` 是非默认值的时候会做两件事：

- 更新 `VideoReceiveStream2` 之中的 `frame_minimum_playout_delay_ms_` 等成员变量
- 调用 `UpdatePlayoutDelays` 函数更新 `VCMTiming` 类中的数据

具体的源码为：

{% codeblock lang:c++ C++ %}
void VideoReceiveStream2::UpdatePlayoutDelays() const {
    RTC_DCHECK_RUN_ON(&worker_sequence_checker_);
    const std::initializer_list<absl::optional<TimeDelta>> min_delays = {
        frame_minimum_playout_delay_,
        base_minimum_playout_delay_,
        syncable_minimum_playout_delay_};

    // Since nullopt < anything, this will return the largest of the minumum
    // delays, or nullopt if all are nullopt.
    absl::optional<TimeDelta> minimum_delay = std::max(min_delays);
    if (minimum_delay) {
        auto num_playout_delays_set =
            absl::c_count_if(min_delays, [](auto opt) { return opt.has_value(); });
        if (num_playout_delays_set > 1 &&
            timing_->min_playout_delay() != minimum_delay) {
            RTC_LOG(LS_WARNING)
                << "Multiple playout delays set. Actual delay value set to "
                << *minimum_delay << " frame min delay="
                << OptionalDelayToLogString(frame_minimum_playout_delay_)
                << " base min delay="
                << OptionalDelayToLogString(base_minimum_playout_delay_)
                << " sync min delay="
                << OptionalDelayToLogString(syncable_minimum_playout_delay_);
        }
        timing_->set_min_playout_delay(*minimum_delay);
        if (frame_minimum_playout_delay_ == TimeDelta::Zero() &&
            frame_maximum_playout_delay_ > TimeDelta::Zero()) {
            // TODO(kron): Estimate frame rate from video stream.
            constexpr Frequency kFrameRate = Frequency::Hertz(60);
            // Convert playout delay in ms to number of frames.
            int max_composition_delay_in_frames =
                std::lrint(*frame_maximum_playout_delay_ * kFrameRate);
            // Subtract frames in buffer.
            max_composition_delay_in_frames =
                std::max(max_composition_delay_in_frames - buffer_->Size(), 0);
            timing_->SetMaxCompositionDelayInFrames(max_composition_delay_in_frames);
        }
    }

    if (frame_maximum_playout_delay_) {
        timing_->set_max_playout_delay(*frame_maximum_playout_delay_);
    }
}
{% endcodeblock %}

这一段看起来有一个前提，就是 `min_delays` 列表之中最多有一个有效值，而这个有效值将会被处理为真实的播放延迟。可以看到，在第 25 行，这里调用了 `timimg_->set_min_playout_delay` 函数，而这个函数的作用就是将播放延迟设定到 `VCMTiming` 类之中。这里 `timing_` 的类型为 `std::unique_ptr<VCMTiming>`。

再具体展开：

{% codeblock lang:c++ C++ %}
void VCMTiming::set_min_playout_delay(TimeDelta min_playout_delay) {
    MutexLock lock(&mutex_);
    if (min_playout_delay_ != min_playout_delay) {
        CheckDelaysValid(min_playout_delay, max_playout_delay_);
        min_playout_delay_ = min_playout_delay;
    }
}

void VCMTiming::set_max_playout_delay(TimeDelta max_playout_delay) {
    MutexLock lock(&mutex_);
    if (max_playout_delay_ != max_playout_delay) {
        CheckDelaysValid(min_playout_delay_, max_playout_delay);
        max_playout_delay_ = max_playout_delay;
    }
}
{% endcodeblock %}

至少这里看起来是相当普通的一个 setter 代码。按这里的代码的话，`VCMTiming` 的作用在于维护帧级别的播放延迟，并作为最终存放点用于后续的延迟估计工作。

## 帧插入过程中延迟的估计

在形成新的一帧之后，需要通过帧插入操作将新的帧加入到帧缓冲区之中供后续解码。承接上述说明，在 `VideoReceiveStream2::OnCompleteFrame` 函数之后，会通过 `VideoStreamBufferController::InsertFrame` 函数将新帧压入缓冲区，而这个函数则进一步调用了 `FrameBuffer::InsertFrame` 函数：

{% codeblock lang:c++ C++ %}
absl::optional<int64_t> VideoStreamBufferController::InsertFrame(std::unique_ptr<EncodedFrame> frame) {
    RTC_DCHECK_RUN_ON(&worker_sequence_checker_);
    FrameMetadata metadata(*frame);
    int complete_units = buffer_->GetTotalNumberOfContinuousTemporalUnits();
    if (buffer_->InsertFrame(std::move(frame))) {
        RTC_DCHECK(metadata.receive_time) << "Frame receive time must be set!";
        if (!metadata.delayed_by_retransmission && metadata.receive_time &&
            (field_trials_.IsDisabled("WebRTC-IncomingTimestampOnMarkerBitOnly") ||
            metadata.is_last_spatial_layer)) {
            timing_->IncomingTimestamp(metadata.rtp_timestamp, *metadata.receive_time);
        }
        if (complete_units < buffer_->GetTotalNumberOfContinuousTemporalUnits()) {
            stats_proxy_->OnCompleteFrame(metadata.is_keyframe, metadata.size, metadata.contentType);
            MaybeScheduleFrameForRelease();
        }
    }

    return buffer_->LastContinuousFrameId();
}
{% endcodeblock %}

这里 `FrameBuffer::InsertFrame` 函数具体细节不再阐述，基本上就是管理缓冲区的一些操作，我们主要看后续有关延迟的计算方式。首先，第 7 行的判定表示，如果该帧不是重传帧则进入分支内部。在此基础上，该函数会调用 `VCMTiming::IncomingTimestamp` 并立刻跳转到 `TimestampExtrapolator::Update`。在这个函数内部，框架将会根据当前帧的 RTP 时间戳估计当前帧的期望接收时间，并对 Kalman gain 进行校准（内心 OS：这一句话真的是没看懂啊，不过参考文献说后面会具体说明这个函数的目的，这里就先不深究了吧）。

---

首先说明一帧从完整构成到显示到屏幕上所需要经历的阶段。在帧组装完毕之后，首先会送往解码器解码，解码完毕之后则送给渲染器渲染。

这里我们关注一个函数，即 `VCMTiming::MaxWaitingTime`。这个函数的作用是给出帧组装所能容忍的最大时长，也就是说如果在给定的阈值内没有能够从网络上获取足够的数据包将这一帧组装完毕，则这一帧大概率来不及走完解码、渲染的流程并显示在屏幕上。事实上这个函数是一个预测性质的函数，用来预测当前条件下我们能等待网络延迟多长时间：

{% codeblock lang:c++ C++ %}
TimeDelta VCMTiming::MaxWaitingTime(
    Timestamp render_time,
    Timestamp now,
    bool too_many_frames_queued
) const {
    MutexLock lock(&mutex_);

    if (
        render_time.IsZero() &&
        zero_playout_delay_min_pacing_->us() > 0 &&
        min_playout_delay_.IsZero() &&
        max_playout_delay_ > TimeDelta::Zero()
    ) {
        // `render_time` == 0 indicates that the frame should be decoded and
        // rendered as soon as possible. However, the decoder can be choked if too
        // many frames are sent at once. Therefore, limit the interframe delay to
        // |zero_playout_delay_min_pacing_| unless too many frames are queued in
        // which case the frames are sent to the decoder at once.
        if (too_many_frames_queued) {
            return TimeDelta::Zero();
        }
        Timestamp earliest_next_decode_start_time =
            last_decode_scheduled_ + zero_playout_delay_min_pacing_;
        TimeDelta max_wait_time =
            now >= earliest_next_decode_start_time
                ? TimeDelta::Zero()
                : earliest_next_decode_start_time - now;
        return max_wait_time;
    }
    return render_time - now - EstimatedMaxDecodeTime() - render_delay_;
}
{% endcodeblock %}

这个函数中 `render_time == 0` 表示这一帧需要尽快显示，这里忽略这一分支。该函数接受的参数分别为：

- 要求渲染完成的时间戳 `render_time`，即我们的目标是在 `render_time` 之前渲染完毕这一帧
- 当前的时间戳 `now`

而返回值可以看到，首先我们用 `render_timer - now` 得到我们总共的容忍时长，之后减去预估的解码延迟 `EstimatedMaxDecodeTime()`，再减去预估的渲染延迟 `render_delay_`，即得到了剩余可以用来等待网络的时长。

---

这里我们看另外一个函数，即 `VCMTiming::RenderTimeInternal`：

{% codeblock lang:c++ C++ %}
Timestamp VCMTiming::RenderTimeInternal(uint32_t frame_timestamp, Timestamp now) const {
    if (UseLowLatencyRendering()) {
        // Render as soon as possible or with low-latency renderer algorithm.
        return Timestamp::Zero();
    }
    // Note that TimestampExtrapolator::ExtrapolateLocalTime is not a const
    // method; it mutates the object's wraparound state.
    Timestamp estimated_complete_time =
        ts_extrapolator_->ExtrapolateLocalTime(frame_timestamp).value_or(now);

    // Make sure the actual delay stays in the range of `min_playout_delay_`
    // and `max_playout_delay_`.
    TimeDelta actual_delay =
        current_delay_.Clamped(min_playout_delay_, max_playout_delay_);
    return estimated_complete_time + actual_delay;
}
{% endcodeblock %}

这里主要在预测某一帧何时应该被渲染完毕，事实上返回的就是上一个函数中的 `render_time` 时间戳参数。首先，判断是否是低延迟渲染模式，如果采用了低延迟模式，则直接返回零表示要求立刻渲染。

在此之后，首先会通过 `TimestampExtrapolator::ExtrapolateLocalTime` 函数，利用 Kalman filter 估计应当何时将这一帧送入渲染器队列。之后将 `current_delay_` 通过上下界 `[min_playout_delay_, max_playout_delay_]` 截断生成渲染过程耗时的估计，而这里的上下界则是上一阶段之中所获得的。`current_delay_` 本身的计算方式则在后面给出说明。

## Kalman filter



# WebRTC jitter buffer