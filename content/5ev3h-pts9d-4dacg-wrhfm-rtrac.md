# the plan now

spent March 21 doing research and planning.

first thing is that it turns out basically everything I wrote [around internal DNS here](sspt5-0dztv-42a3t-9hx3y-t7708) was busted. I was wrong about the way k8s_external works, and I pretty much would need a custom image to do the kind of exposition I was describing - the next best option would be along the lines of setting up an external-dns container alongside a single-node etcd and a new instance of CoreDNS, and, just, oof

I was bummed about this, but then I realized: with the MetalLB, I don't actually need an Ingress server for internal traffic - I can actually host internal services on different internal IPs! And it's not like the Let's Encrypt stuff was gonna do me much good

Yes, there are optimizations I could make to this design, but this would be enough to accomplish the number-one thing I want right now: access to the Dashboard and Kubeapps without needing to run kubectl proxy, use the API proxy, or make an entry in /etc/hosts.

## first scheduled change

- set up stubernetes.internal exposing nameserver
  - was briefly thinking of calling the zone "stube.internal" for simplicity
  - nah tho
- replace ingress for kubeapps with a service record / whatever
  - I don't think I can set domains this way, but I can still allocate an IP that the "external" DNS can expose

more thoughts around the DNS:

- I'd like to have it so I could set up good `.internal` names for internal services via Ingress or Service records
  - I'd settle for adding manual CNAME records to the CoreDNS config pointing to a service name defined as service.namespace.stubernetes.internal though

test suite:

- go to kubeapps.kubeapps.stube.internal

## next change

- Undo "default" nature of Ingress: should need to specify "traefik-exposed" class (future-facing, in case internal ingress comes back)
- Make sure redeployment is disabled in k3s after tweaking
- Set up SSH credentials
- Add SSH port forwarding pod with affinity for forwarding the exposed-traffic server

## next

- authn proxy server around Kubernetes Dashboard and Kubeapps
