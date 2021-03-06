# Tensorflow / Machine Learning Aggregate

- [Teachable Machine](https://teachablemachine.withgoogle.com/)
  - Libraries linked there:
    - https://p5js.org/ (not a TF thing)
    - https://ml5js.org/
- [Edge TPU Announcement](https://blog.tensorflow.org/2019/03/build-ai-that-works-offline-with-coral.html)
- [Frigate](https://github.com/blakeblackshear/frigate)
  - Object detection over realtime RTSP webcam video streams
  - [has a Helm chart](https://hub.helm.sh/charts/billimek/frigate)
    - Note how it just uses the HostPath? this seems straightforward enough

## Project writeups

- [Coral Examples](https://coral.ai/examples/)
  - [Teachable Sorter](https://coral.ai/projects/teachable-sorter/)
  - [Keyword Spotter](https://github.com/google-coral/project-keyword-spotter)
    - ie. a voice hotword detection system
- "Partner" examples
  - [MediaPipe](https://mediapipe.readthedocs.io/en/latest/)
- [Containerized project](https://cxlabs.sap.com/2019/10/07/containerizing-a-tensorflow-lite-edge-tpu-ml-application-with-hardware-access-on-raspbian/)

## Specifically for Kubernetes

- https://github.com/kkohtaka/edgetpu-device-plugin looks toward hooking up the Edge TPU device
  - https://github.com/kkohtaka/edgetpu-device-plugin/issues/1 specifically

## Somewhat to completely unrelated

https://inaccel.com/fpgas-goes-serverless-on-kubernetes/

## See Also

- Intel Neural Compute Stick 2?
  - You don't hear about this one as much
  - Not that much more expensive
  - says it supports TensorFlow as well?
  - Talks about some "OpenVINO" thing
