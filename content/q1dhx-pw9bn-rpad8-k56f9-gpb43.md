# notes on helm

so I'm looking at ways to let's-encrypt ingress in Kubernetes, and they both involve installing components via helm, so I'm looking into understanding helm.

One thing I'll say is, hot damn how many levels of abstraction around system state do we freaking need? so there's Containers, which go in Pods, which go in Services, and then there's this Helm thing above that? Like, I thought it was enough to just kubectl apply constructions that are aggregated in some kind of yaml document, why are there so many more levels of template system

and what about kustomize? aren't they both just ways to apply a template configuration to the yaml doc?

https://helm.sh/docs/

## looking at the Helm Hub

interesting how the vendor/repo thing works

https://hub.helm.sh/charts/jetstack/cert-manager hmm, weird that it does a kubectl apply *before* the helm install
