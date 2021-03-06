# 12-factor system architecture under Kubernetes

somewhere around [the Module Tree design notes](237wy-vyzdz-w39y5-va8ej-3r5g6)

anyway Plusku should probably be using something more like Dockerfiles anyway

also, thinking now, maybe there'll be something like a "CI plugin suite" that has a specified directory to read as the Git root, and all the app plugins

also, new naming paradigm for Plushu: all plugins, instead of being prefixed "plushu", should be prefixed by the "project" whose philosophy they live under. so like the app-image-creation-from-git-hooks ecosystem should be one component, and the deploy-built-image-to-cluster should be another project that, like, kind of incorporates that?

## okay so here's one thing that can probably be better over images

https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/

under Helm the right way to make a config/release would be to have each release rebuild the chart with the selected ENV (if you want to keep to 12-factor restrictions)

er, I mean, Releases are Deployments?

## so anyway

now that I've discovered Podman and Buildah and that whole family, the old Plusku system has a MUCH more sensible way forward, especially with, like, the ability to run Nginx on Podman

Environment variables can be tracked in a pod - or, oh snap, can they be read from a file?

We could even have "run in separate pods"

And we can have an "export pod as spec" script to package as a Helm chart (for example) and deploy to Kubernetes (and a hook to push the image using Scopeo)

And this would be a Kubernetes-compatible alternative for lightweight and simple app development - we can support all of Heroku's features except the actual clustering (at which point you want Kubernetes anyway)
