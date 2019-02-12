When using ceph rbd in kubernetes, we met 2 problems:
1. Kubernetes node does't load rbd module.
2. System shutdown process will be blocked while trying to unmount rbd directories.Because the ceph client fail to response umount.
 
So I created a k8srbd.service, make it with an ordering dependency between docker.service, to perform a modbrobe rbd during system boot.
And make another ordering dependency between filesystem, to ensure we can clean the rbd mount before kernel umount all filesystems.
