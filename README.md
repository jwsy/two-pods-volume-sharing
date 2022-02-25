Two pods volume sharing
=======================

This is a basic example of how to share a volume between two pods 

Prerequisites
-------------
Rancher desktop: https://rancherdesktop.io/ (tested on v1.0.1)

Config
------
1. Update the `hostPath.path` in `two-pods-share-pv.yaml` must be a valid path in your local file system. In my example on my mac, it's in `/Users/$HOME/...`

    Per Rancher Desktop Slack https://rancher-users.slack.com/archives/C0200L1N1MM/p1642010476123800?thread_ts=1642010197.123500&cid=C0200L1N1MM

    > Jan Dubois:rancher_employee:  1 month ago
    > By default the `/tmp/rancher-desktop` and the `$HOME` directories are shared between host and guest (via sshfs). Note that there can be delays in syncing directory information between the 2 systems.
    >
    > Note that `$HOME` here means `/Users/$USER` on the host. The `$HOME` inside the guest expands to `/home/$USER.linux` (and `$USER` inside the guest might be lima if the host username is not a valid Linux username)

Steps to build this app
-----------------------
1. Create the PersistentVolume (PV). Specifically create a unique `storageClassName` and a valid `spec.hostPath`
2. Create the PersistentVolumeClaim (PVC) named `two-pods-pvc` so that it looks for the `storageClassName` of the PV created. Note that the `spec.accessModes` are fine as `ReadWriteOnce` since this local Rancher Desktop only has ONE node, but this may have to change on a cluster with more nodes.
3. Create the pods. I created two containers in the pod, (#1) to serve the data from an nginx web server and (#2) to use as a debugger and a data creator to serve. The key here is that both use the same `spec.containers.volumeMounts.name`, in this example it's `shared-data` and that's a volume that uses the PVC created earlier `two-pods-pvc`
4. Create the service & ingress as in this simplest example https://itnext.io/simplest-minimal-k8s-app-tutorial-with-rancher-desktop-in-5-min-5481edb9a4a5

HOWTO Deploy
------------
``` bash
# Install Rancher Desktop
git clone https://github.com/jwsy/two-pods-volume-sharing.git
cd two-pods-volume-sharing
# Configure `two-pods-share-pv.yaml`
kubectl create ns namespace-name
kubectl -n namespace-name apply -f . 
```