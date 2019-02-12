When using ceph rbd in kubernetes, we met 2 problems:
1. Kubernetes node does't load rbd module.
2. System shutdown process will be blocked while trying to unmount rbd directories.Because when kernel unload filesystem, the ceph client shut down simultaneously and fail to response umount.
 
So I created a k8srbd.service, make it with an ordering dependency between docker.service, to perform a modbrobe rbd during system boot.
And make another ordering dependency between filesystem, to ensure we can clean the rbd mount before kernel umounts all filesystems.
The above steps are validated under CentOS 7(kernel 4.20.7).

Put rbdunmapall in /usr/local/sbin/ and give it a chmod +x.
Put k8srbd.service in /usr/lib/systemd/system/, then systemctl daemon-reload && systemctl enable k8srbd
