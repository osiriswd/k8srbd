When using ceph rbd in kubernetes, we met 2 problems:
1. Kubernetes node does't load rbd module.
2. System shutdown process will be blocked while trying to unmount rbd directories.Because when kernel unloading network/filesystem, the ceph client shut down simultaneously and fails to umount.
 
So I created a k8srbd.service, make it with an ordering dependency between docker.service, to perform a modbrobe rbd during system boot.
And make another ordering dependency between network/filesystem, to ensure we may clean rbd mount before kernel umounts all filesystems.

Usage:
Put rbdunmapall in /usr/local/sbin/ and give it a chmod +x.
Put k8srbd.service in /usr/lib/systemd/system/, and systemctl daemon-reload && systemctl enable k8srbd

The above steps are validated under CentOS 7.5(kernel 4.20.7).
