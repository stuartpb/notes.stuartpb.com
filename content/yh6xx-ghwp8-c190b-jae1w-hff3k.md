# Teardown, Reconfigure, and Rebuild 2020-10-04

I do `kubeadm reset` on each node, then the teardown scripts:

```
bash <(curl https://raw.githubusercontent.com/stuartpb/stubernetes/main/hack/teardown/weave-teardown.sh)
bash <(curl https://raw.githubusercontent.com/stuartpb/stubernetes/main/hack/teardown/rook-ceph-lvm2-teardown.sh)
```

This time I'm gonna go ahead and reboot before the next step

## rebuild speedrun

I ran through a couple bum rounds before I remembered there was one hack I'd rolled out kind of haphazardly that I wanted to replicate with kubeadm init:

`kubeadm init --image-repository k8s.gcr.io`

I do the file rollout in the right order, but this time I kind of rushed the rollout, and screwed things up by pushing the file that deploys stubernetes-system before waiting for all nodes to join the cluster, which may have overwhelmed the master node before those nodes had a chance to join... derp.

So yeah, it should really go "push weave-net and join, WAIT FOR ALL NODES, continue" in the spinup procedure

I re-run `kubeadm reset` and the teardown scripts and try again.

After a few go-rounds, I roll out weave-net, see that nodes aren't necessarily showing up as `Ready` even though weave-net is running, and since I'm an impatient fuck I just go ahead and reboot them all.

## round and round

I keep rebuilding the cluster... eventually it comes up, a couple pods [still have errors](p8wcc-mkhyw-039jw-zcnss-sf74k) and are CrashLoopBackOff, but deleting the pods is enough to make them come back healthy.
