
1. Local Storage用 名前空間 openshift-local-storage を作成
    
1. Local Storage Operatorの導入
    1.  
    ![image](https://user-images.githubusercontent.com/22209835/141883405-66dddd1e-5184-4258-8c62-296316f5a187.png)
    ![image](https://user-images.githubusercontent.com/22209835/141883508-631c36e2-aac8-4f41-ab8b-fe926ca36587.png)
    1. デフォルトの設定のまま導入
    ![image](https://user-images.githubusercontent.com/22209835/141883589-7b2a26ca-e937-4e0b-adc6-8d534bc29a83.png)
    1. 正常導入を確認します
    ![image](https://user-images.githubusercontent.com/22209835/141883734-d02c0f6f-12d3-43b9-9f88-353fdfe80bce.png)
1. 利用可能な ノードのディスクを確認します。
    ``
    ~/Downloads $ oc get nodes
    NAME                                                 STATUS   ROLES    AGE   VERSION
    master0.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    master   86m   v1.21.1+a620f50
    master1.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    master   86m   v1.21.1+a620f50
    master2.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    master   86m   v1.21.1+a620f50
    worker0.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    worker   77m   v1.21.1+a620f50
    worker1.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    worker   77m   v1.21.1+a620f50
    worker2.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com   Ready    worker   77m   v1.21.1+a620f50
    ~/Downloads $ oc debug node/worker0.itzocp-3100008gyq-7ym9drl0.cp.fyre.ibm.com
    Starting pod/worker0itzocp-3100008gyq-7ym9drl0cpfyreibmcom-debug ...
    To use host binaries, run `chroot /host`
    Pod IP: 10.17.50.11
    If you don't see a command prompt, try pressing enter.
    sh-4.4# chroot /host
    sh-4.4# ls -l /dev/disk/by-path
    total 0
    lrwxrwxrwx. 1 root root  9 Nov 16 01:02 pci-0000:00:10.0 -> ../../vda
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 pci-0000:00:10.0-part1 -> ../../vda1
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 pci-0000:00:10.0-part2 -> ../../vda2
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 pci-0000:00:10.0-part3 -> ../../vda3
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 pci-0000:00:10.0-part4 -> ../../vda4
    lrwxrwxrwx. 1 root root  9 Nov 16 01:02 pci-0000:00:11.0 -> ../../vdb
    lrwxrwxrwx. 1 root root  9 Nov 16 01:02 virtio-pci-0000:00:10.0 -> ../../vda
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 virtio-pci-0000:00:10.0-part1 -> ../../vda1
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 virtio-pci-0000:00:10.0-part2 -> ../../vda2
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 virtio-pci-0000:00:10.0-part3 -> ../../vda3
    lrwxrwxrwx. 1 root root 10 Nov 16 01:02 virtio-pci-0000:00:10.0-part4 -> ../../vda4
    lrwxrwxrwx. 1 root root  9 Nov 16 01:02 virtio-pci-0000:00:11.0 -> ../../vdb
    sh-4.4# exit
    exit
    sh-4.4# exit
    exit

    Removing debug pod ...
    ``
