# Actually installing OpenEBS

So, I just rebooted to make sure open-iscsi was in, but I just realized: if I install OpenEBS, I'm gonna want the disks to be de-partitioned

even though I could probably reuse that disk with the install image to expand the nodes... I'll do that later under sturling

## clearing the partition tables

https://www.golinuxhub.com/2018/05/how-to-cleardelete-partition-table-disk-linux/

after checking `lsblk` to make sure these were the right disks:

```
studtop:~ # wipefs -a -f /dev/sda
/dev/sda: 8 bytes were erased at offset 0x00000200 (gpt): 45 46 49 20 50 41 52 54
/dev/sda: 8 bytes were erased at offset 0x1d1bf0ffe00 (gpt): 45 46 49 20 50 41 52 54
/dev/sda: 2 bytes were erased at offset 0x000001fe (PMBR): 55 aa
studtop:~ # wipefs -a -f /dev/sdb
/dev/sdb: 5 bytes were erased at offset 0x00008001 (iso9660): 43 44 30 30 31
/dev/sdb: 2 bytes were erased at offset 0x000001fe (dos): 55 aa
/dev/sdb: 8 bytes were erased at offset 0xe6b7ffe00 (gpt): 45 46 49 20 50 41 52 54
```

`partprobe` and looks good: they're now just two flat disks

## anyway

setting up an `openebs` namespace [per docs suggested name](https://docs.openebs.io/docs/next/installation.html#installation-through-helm)

adding the https://openebs.github.io/charts/ repo for openebs, but you know what, this time I'm adding it into the openebs namespace only. Wonder if that helps perf-wise; thinking about going back and doing it for kubernetes-dashboard

## customizing the values

leaving `apiserver.sparse.enabled` false because I'm actually kind of planning on destroying most of the default classes, I'd rather not even import them initially really

but I'm not turning off `defaultStorageConfig.enabled` because I'm a coward. ultimately I change no values

```
The OpenEBS has been installed. Check its status by running:
$ kubectl get pods -n openebs

For dynamically creating OpenEBS Volumes, you can either create a new StorageClass or
use one of the default storage classes provided by OpenEBS.

Use `kubectl get sc` to see the list of installed OpenEBS StorageClasses. A sample
PVC spec using `openebs-jiva-default` StorageClass is given below:"

---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: demo-vol-claim
spec:
  storageClassName: openebs-jiva-default
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5G
---

Please note that, OpenEBS uses iSCSI for connecting applications with the
OpenEBS Volumes and your nodes should have the iSCSI initiator installed.

For more information, visit our Slack at https://openebs.io/community or view the documentation online at http://docs.openebs.io/.
```

## okay, making this not suck

hmm, okay, so, the server has discovered the hard drive, the flash drive, and... all the components of the internal disk.

tempted to `wipefs -a -f /dev/mmcblk0 && partprobe` but I'm not 100% sure ndm can handle that?

ah what the heck, live dangerously. let's go ahead and delete the ndm pod

hmm, even `kubectl delete pods '-l=component in (ndm,ndm-operator)' -n openebs` isn't bringing it back

ah hell, whatever. let's just delete the two records for the onboard boot sectors, keep the one for overall onboard storage, then use the remaining two for pools

saving the resources I'm writing this way into stubernetes-setup/resources

might use https://docs.openebs.io/docs/next/uglocalpv-device.html#optional-block-device-tagging to tag bulk disks at some point

since I'm planning on setting up a "small" reservation on the hard drive for Grafana, I'm gonna use cStor for the bulk class right now as well. can figure out something smarter later

they both look pretty much like this:

```
apiVersion: openebs.io/v1alpha1
kind: StoragePoolClaim
metadata:
  name: cstor-work-pool
  annotations:
    cas.openebs.io/config: |
      - name: PoolResourceRequests
        value: |-
            memory: 100Mi
      - name: PoolResourceLimits
        value: |-
            memory: 4Gi
spec:
  name: cstor-work-pool
  type: disk
  poolSpec:
    poolType: striped
  blockDevices:
    blockDeviceList:
    - blockdevice-50bfc93f4a03ff39b6219fed5965fe7b
```

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: openebs-work
  annotations:
    openebs.io/cas-type: cstor
    cas.openebs.io/config: |
      - name: StoragePoolClaim
        value: "cstor-work-pool"
      - name: ReplicaCount
        value: "1"
provisioner: openebs.io/provisioner-iscsi
```

I figured 100Mi of RAM per storage class per node was probably appropriate

to save an example for later didactic purposes: `kubectl get storageclasses -o yaml > state/openebs-default-scs-plus-ours.yaml`

I'm now upgrading openebs to disable defaultStorageConfig

at this point I realize I can use kubeapps's ServiceAccount to log in to Dashboard, so I'm using that instead of setting up a dedicated dashboard superuser, as a hack until proper auth is in place

used that to delete the other 4 StorageClasses
