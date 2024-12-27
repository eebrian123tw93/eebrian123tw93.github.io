---
title: Webrtc視訊流
categories: []
date: '2024-12-26 16:17:00'
updated: '2024-12-27 02:31:03'
description: ''
abbrlink: efd237b7
tags:
  - WebRTC
  - android
  - java
---
# 開啟相機主要類別(以安卓為例)
![](/images/20241226150748.png)
# frame的流向
![](/images/20241226150839.png)
攝影機擷取的視訊幀資料會被傳遞到 VideoBroadcaster 這個類別進行處理，而擷取過程也到此為止。從 VideoBroadcaster 的名稱就不難發現，幀資料接下來會被以廣播的形式發送給各個訂閱者，也就是說後續的渲染、編碼（和發送）過程是並行處理的。
 <!-- more -->
 # 選染以及編碼和發送
![](/images/20241226150927.png)
本地渲染 和 編碼發送 有透過 VideoTrack.addSink(sink) 向 broadcaser 註冊 OnFrame callback 事件
- 本地渲染 透過 SurfaceViewRenderer 收到 frame 再將帧数据渲染到了 EGL
- 編碼發送 
	1. 这里的编码器是 LibvpxVp8Encoder，但换成其他继承自 `webrtc::VideoEncoder` 的子类都是可以的 
	2. RtpPacket 入队之后，将由 `webrtc::ProcessThreadImpl::Process` 进行处理，严格意义上已经不算是调用栈了，但读者也可以将其理解为 RtpPacket 的处理流程。
```text
webrtc::VideoStreamEncoder::OnFrame
  → webrtc::VideoStreamEncoder::MaybeEncodeVideoFrame
  → webrtc::VideoStreamEncoder::EncodeVideoFrame
  → webrtc::LibvpxVp8Encoder::Encode #1
  → webrtc::LibvpxVp8Encoder::GetEncodedPartitions
  → webrtc::VideoStreamEncoder::OnEncodedImage
  → webrtc::internal::VideoSendStreamImpl::OnEncodedImage
  → webrtc::RtpVideoSender::OnEncodedImage
  → webrtc::RTPSenderVideo::SendEncodedImage
  → webrtc::RTPSenderVideo::SendVideo
  → webrtc::RTPSenderVideo::LogAndSendToNetwork
  → webrtc::RTPSender::EnqueuePackets
  → webrtc::PacedSender::EnqueuePackets
  → webrtc::PacingController::EnqueuePacket
  → webrtc::PacingController::EnqueuePacketInternal
  → webrtc::PacedSender::Process #2
  → webrtc::PacingController::ProcessPackets
  → webrtc::PacedSender::SendRtpPacket
  → webrtc::ModuleRtpRtcpImpl2::TrySendPacket
  → webrtc::RtpSenderEgress::SendPacket
  → webrtc::RtpSenderEgress::SendPacketToNetwork
  → cricket::WebRtcVideoChannel::SendRtp
  → cricket::MediaChannel::SendPacket
  → cricket::MediaChannel::DoSendPacket
  → cricket::VideoChannel::SendPacket
  → webrtc::DtlsSrtpTransport::SendRtpPacket #3
```
# 添加濾鏡
由於需要即時預覽濾鏡效果，所以必須在渲染開始之前添加濾鏡。好在 WebRTC 已經提供了 VideoProcessor 這個介面類，可以對擷取到的幀資料進行預處理，呼叫 VideoSource.setVideoProcessor(processor) 即可設定：
```java
public class VideoSource extends MediaSource {
  // other definitions...

  private final CapturerObserver capturerObserver = new CapturerObserver() {
    // other definitions...

    @Override
    public void onFrameCaptured(VideoFrame frame) {
      final VideoProcessor.FrameAdaptationParameters parameters =
          nativeAndroidVideoTrackSource.adaptFrame(frame);
      synchronized (videoProcessorLock) {
        if (videoProcessor != null) {
          videoProcessor.onFrameCaptured(frame, parameters);          
          return;
        }
      }

      // body method...
    }
  };

  // other definitions...
}
```
VideoProcessor 的簡單實現
```java
public final class Example implements VideoProcessor {

  @Nullable private VideoSink mVideoSink;

  @Override
  public void onCapturerStarted(boolean success) {
    // DO SOMETHING IF YOU WANT.
  }

  @Override
  public void onCapturerStopped() {
    // DO SOMETHING IF YOU WANT.
  }

  @Override
  public void setSink(@Nullable VideoSink sink) {
    // 需要持有 WebRTC 传入的 VideoSink 对象
    mVideoSink = sink;
  }

  @Override
  public void onFrameCaptured(@NonNull VideoFrame frame) {
    VideoFrame newFrame = yourVideoFilter(frame);

    // 会调用 NativeAndroidVideoTrackSource 将新的帧数据传递给 Native 层
    if (mVideoSink != null) mVideoSink.onFrame(frame);
  }
}
```
# 參考
- [视频推流过程](https://webrtc.mthli.com/media/video-outbound)
- [WebRTC Native 源码导读（一）：安卓相机采集实现分析](https://blog.piasy.com/2017/07/24/WebRTC-Android-Camera-Capture/index.html)
