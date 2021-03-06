# Moving to ingress-nginx

After a while with Contour, when it came time to [reconsider my choice of Ingress controller](t7v0w-vp2jf-0gbwx-vc504-pzwre), I'm switching over to the mainline ingress-nginx implementation.

## Why?

Short answer: I want forward-auth annotations on my Ingresses as I roll out Pomerium with the Operator, and this is the path of least resistance to that.

## A little more on this

Ingress is a doomed API: every Ingress controller except the reference one has its own CRD, which they all implore the user to use in their docs instead of Ingress. Hopefully these will eventually be superseded by [Gateway / Routes](https://kubernetes-sigs.github.io/service-apis/concepts/)

The only changes between beta and release were [to standardize divergences that emerged during the beta period](https://kubernetes.io/blog/2020/04/02/improvements-to-the-ingress-api-in-kubernetes-1.18/)

so yeah, rather than wait for The Definitive Ingress Controller to emerge, I'm accepting that ingress-nginx *is* the definitive Ingress controller, and that everything else is vying to be a Gateway/HTTPRoute controller

## actually

After doing this
