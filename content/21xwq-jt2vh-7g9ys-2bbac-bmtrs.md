# Remaking Studtop Again

Running the installation again just like [From the Top with Studtop, 2020-06-26](cgje3-6wzw1-2s90p-5n7e7-tqqdb). The `ignition-dracut` package doesn't show up as installable in the initial installer, I checked.

I should really put the public key for Stushiba on the Ignition drive

Did `transactional-update pkg in ignition-dracut` and rebooted with the ignition drive, which worked

## experiments in zeroing

Okay, so, I zeroed the first 8 gigs of the 64 GiB drive before going "there's no way this would be the problem"

Then I'm getting some weird results from the hard drive: plugged into the hub, it erases for five seconds, then freezes in an unkillable state until I forcibly remove the drive from the machine.

Unplugged from the hub and plugged directly into the machine, I get this:

```
studtop:~ # dd if=/dev/zero of=/dev/sdc status=progress
1823135744 bytes (1.8 GB, 1.7 GiB) copied, 22 s, 82.9 MB/s
dd: writing to '/dev/sdc': No space left on device
3619665+0 records in
3619664+0 records out
1853267968 bytes (1.9 GB, 1.7 GiB) copied, 22.3659 s, 82.9 MB/s
studtop:~ # dd if=/dev/zero of=/dev/sdc status=progress
1818563584 bytes (1.8 GB, 1.7 GiB) copied, 22 s, 82.7 MB/s
dd: writing to '/dev/sdc': No space left on device
3619665+0 records in
3619664+0 records out
1853267968 bytes (1.9 GB, 1.7 GiB) copied, 22.4222 s, 82.7 MB/s
```

Bringing it over to Stushiba, it works just fine (though it writes at 20mb/s which is... weird)

I'm starting to consider just... not using these disks

I zero the first nine-and-a-half gigs or so

I try moving around the USB configuration and now it won't read DHCP from the Ethernet adapter??

## here we go again

I *immediately reinstall the system again*. This time, I make sure the Ethernet adapter is set up to use DHCP in installation

`transactional-update pkg in ignition-dracut` and reboot with ignition again to reconfigure

I start to run `kubeadm init`:

```
studtop:~ # kubeadm init
W0627 08:50:11.726541    2219 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.18.3
[preflight] Running pre-flight checks
	[WARNING Hostname]: hostname "studtop" could not be reached
	[WARNING Hostname]: hostname "studtop": lookup studtop on 192.168.0.1:53: no such host
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
^C
studtop:~ # hostnamectl
   Static hostname: studtop.nodes.403.stuartpb.com
Transient hostname: studtop
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: e799f3f577e3487f948280856751c25d
           Boot ID: 7f1d58216643408da76f1fd7955ec1cd
  Operating System: openSUSE MicroOS
       CPE OS Name: cpe:/o:opensuse:microos:20200622
            Kernel: Linux 5.7.2-1-default
      Architecture: x86-64
```

?!

I do `hostnamectl set-hostname studtop.nodes.403.stuartpb.com`, but I'm not sure why this happened? I think the router maybe does echo hostnames via DHCP, and this has to be done to prevent it? anyway, fingers crossed that this maybe fixes the openebs issue

## just something to consider

TODO: Move this into the "cluster discovery plan" notes

https://doc.opensuse.org/documentation/leap/reference/html/book.opensuse.reference/cha-network.html#sec-network-yast-netcard-global

> In the DHCP Client Options configure options for the DHCP client. The DHCP Client Identifier must be different for each DHCP client on a single network. If left empty, it defaults to the hardware address of the network interface. However, if you are running several virtual machines using the same network interface and, therefore, the same hardware address, specify a unique free-form identifier here.

https://doc.opensuse.org/documentation/leap/reference/html/book.opensuse.reference/cha-network.html#sec-network-yast-change-host

>  If you are using DHCP to get an IP address, the host name of your computer will be automatically set by the DHCP server. You should disable this behavior if you connect to different networks, because they may assign different host names and changing the host name at runtime may confuse the graphical desktop. To disable using DHCP to get an IP address deactivate Change Hostname via DHCP.
>
> Assign Hostname to Loopback IP associates your host name with 127.0.0.2 (loopback) IP address in /etc/hosts. This is a useful option if you want to have the host name resolvable at all times, even without active network.

ofc 127.0.0.2 probably won't work inside Kubernetes

## anyway

kubeadm init finished, doing the config copy and reconfiguring stushiba

since I just want to go ahead and kick off with everything that can and will go wrong, after creating the stubernetes-system namespace and installing stubernetes-core, I `kubectl create -f openebs.yaml` first

```
[stuart@stushiba storage]$ kubectl create -f bulk.yaml -f work.yaml
storageclass.storage.k8s.io/bulk created
storageclass.storage.k8s.io/work created
unable to recognize "bulk.yaml": no matches for kind "StoragePoolClaim" in version "openebs.io/v1alpha1"
unable to recognize "work.yaml": no matches for kind "StoragePoolClaim" in version "openebs.io/v1alpha1"
```

oh hurr durr, nothing's created yet

I go ahead and delete those SCs and then `kubectl taint nodes --all node-role.kubernetes.io/master-`

after waiting a bit for everything to go ahead from that, I `kubectl create -f bulk.yaml -f work.yaml`

okay, looks like the system hasn't collapsed yet... `kubectl create -f prometheus-operator.yaml`, let's see if that does it

hmm, system stil looks intact... `kubectl create -f metallb.yaml`

```
[stuart@stushiba helmreleases]$ kubectl create -f metallb.yaml
helmrelease.helm.fluxcd.io/metallb created
Error from server: error when creating "metallb.yaml": etcdserver: request timed out
```

I'm starting to realize that I maybe should have a dedicated master node for this.

Anyway, it looks like it did eventually create that?

## setting us up for the next stuff

doing `kubectl edit node studtop.nodes.403.stuartpb.com` to add the annotation for household system stuff:

```
    st8s.stuartpb.com/zone: "403"
```

now let's go ahead and create the `household-ingress` with ``kubectl apply -f household-ingress.yaml``

inspecting `kubectl describe hr household-ingress -n stubernetes-system`:

derp, I screwed up the selector value. Fixed and `kubectl apply -f household-ingress.yaml` (this took me multiple tries to get right because nodeSelector takes a map of label pairs)
