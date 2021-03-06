# Understanding the Post-Docker Container Landscape

The basic functions of Docker don't need a daemon:

- https://servicesblog.redhat.com/2019/10/09/say-hello-to-buildah-podman-and-skopeo/
- https://mkdev.me/en/posts/dockerless-part-1-which-tools-to-replace-docker-with-and-why
- more background: https://thenewstack.io/red-hat-buildah-provides-a-way-to-build-containers-without-the-docker-daemon/
  - oh, of course jessfraz was working on something like this
    - hey, I should really be looking at this, too

At the level you want a daemon, you also want all the stuff Kubernetes manages

## more examples of rootless containers

- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/managing_containers/finding_running_and_building_containers_with_podman_skopeo_and_buildah
  - the list of features podman doesn't support is illuminating

## more resources about this toolset

- http://redhatgov.io/workshops/containers_101/

## related pages

- [Understanding the Runtime Side](zjbpr-vc1rp-cr9j6-vdqav-h61r3)
  - you know, the OCI runtime-spec
- [Specific notes about the Image stuff](0rrck-8xa2r-avakd-n4wm3-j26ry)
