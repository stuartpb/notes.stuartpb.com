# Stubernetes System Chart Initial Development Log

These are my notes as I write up the Charts

- https://helm.sh/docs/topics/
- https://helm.sh/docs/topics/charts/
- https://helm.sh/docs/chart_best_practices/
- https://helm.sh/docs/chart_template_guide/getting_started/

chugging along, reading through the Rabbit Path and putting changes that reflect it into the stubernetes repos in appropriate locations

## first hitch

I get to the part with CoreDNS: how am I going to model the "templatey" way I want coredns's config et al to work

I've decided to just fork the upstream chart locally, fuck it

I decided to take the time and do the Service ports spec thing right, which took a lot of bouncing around learning golang templating

ran into problems not realizing that `.` was remapped in a range block calling a thing

Also ran into problems with `set . "udpOnly" true` being mutable, or `merge . (dict "udpOnly" true)` instead of `merge (dict "udpOnly" true) .`

still can't figure out how to boolify

```
{{- $maySplit := .Values.service.type | default "" | eq "LoadBalancer" | and (default true .Values.service.splitLoadBalancer)}}
{{- $shouldSplit := and $tcpPorts $udpPorts | or false | and $maySplit | not | not -}}
```

without the dance at the end, not sure why (it seems like it should be taking a boolean value)

## coming back later / other notes on the templating

oh, it's because piping to `and $maySplit` uses whatever value's being piped in as the result when `$maySplit` is truthy, which here would be the value of `$udpPorts`

fixed as `$shouldSplit := $maySplit | and (and $tcpPorts $udpPorts | or false)`

wait, that `or false` is gonna have the same problem.

`and $maySplit (set (pick . "Values") "udpOnly" true | include "coredns.servicePorts")` is designed to short-circuit before having to do the "udpOnly" evaluation - it could be written as `set (pick . "Values") "udpOnly" true | include "coredns.servicePorts" | and $maySplit`, but it looks like it'd do the evaluation first this way. Not sure if the underlying structure is the same or not but I feel like going with my gut for now

`helm template --dry-run . --generate-name --set serviceType=LoadBalancer --debug` verifies this all works

## herp derp

writing the ConfigMap part I realize, lol, all the complexity I spent on making the helper function work doesn't matter, since I'm throwing out the "servers" list anyway

oh well. When I tackle revisiting this for upstream, I'll probably change the approach anyway so it returns a dict with TCP and UDP ports, as well as a bool saying if any ports *had both* (so it won't auto-split if there are TCP and UDP ports defined on separate interfaces)

or, is that the level Kubernetes blocks at? or does it reject any service that has both, even if they're on different ports? worth reviewing later

anyway, either way shouldSplit can be determined within the helper function and returned in a dict

I searched for `Values.servers` to confirm the helpers are the only places they're used outside the ConfigMap, and then I searched `containerPorts` to see I'd also have to hard-code port 53 into the Deployment. sure whatever

## ah geez

another snag, following through my rabbit-path recap to set up the rebuild for inhouse-dns:

> ## Installing Bitnami's external-dns chart
>
> - (snip)
> - setting coredns.etcdEndpoints to "http://household-dns-etcd.household-system.svc.cluster.local:2379"
> - annotationFilter: `st8s.stuartpb.com/internal-name=coredns`
> - setting `triggerLoopOnEvent` to `true` and `policy` to `sync`
> - setting `registry` to `noop`

- how can I set `coredns.etcdEndpoints`
  - screw it, it's late in the day, I'm gonna punt and have it just be something set manually in the values of the household-dns release
    - I could probably hack this with dict manipulation in _helpers.tpl at the root but, like, why do that to myself

Helm supports arbitrary hierarchy under `templates`, right? so when I go for merging this, I can just move all the subchart templates into subfolders, and then merging the values space is my problem

## okay so

having gone through the [Rabbit Path](b3ntz-v91vx-pt9ry-qzj7t-hy1rr) in linear order, at this point the only missing pieces to get back to the state I was in before are:

- resources for the namespaces stubernetes-system will deploy to, if that's a thing
- Helm charts/releases for the OpenEBS storage profile

I can find out if the former will be a problem pretty easily, and as for the latter, I think that'll have to wait until I've successfully prototyped storage with raw documents first

so, tomorrow I'll make sure all this is committed and pushed, then I'll go ahead with the reinstall.

[A Few More Thoughts Before Starting the Rollout](qv3aj-73gzz-g5atz-vjfse-fs0c9)
