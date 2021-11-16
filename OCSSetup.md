
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
    
        ```
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
        ```
1. BlockStorage用 LocalVolume の作成
![image](https://user-images.githubusercontent.com/22209835/141884695-b89a1014-405a-4053-a1a5-482cb532b12c.png)
![image](https://user-images.githubusercontent.com/22209835/141885093-a0c8786a-a1c6-4df8-987b-745d76b1f77a.png)
![image](https://user-images.githubusercontent.com/22209835/141885145-735e670f-1e8f-409d-929a-0b49733e42f6.png)
    1. StorageClass が作成されていることを確認します
    ![image](https://user-images.githubusercontent.com/22209835/141885342-5c6ed64a-8da4-4bab-a40f-362b3bb19e3d.png)
    
1. OpenShift Container Storage の導入
![image](https://user-images.githubusercontent.com/22209835/141885574-3cee8e6b-19ae-4710-aee8-56838707a095.png)
![image](https://user-images.githubusercontent.com/22209835/141885641-85da113b-7e8d-457f-8f99-ff04ea7af92b.png)
![image](https://user-images.githubusercontent.com/22209835/141885720-d337bf45-a8b2-45cb-9369-d282a72acecd.png)

![image](https://user-images.githubusercontent.com/22209835/141886104-83ba1997-38b2-40d1-899f-8664fde5fd3e.png)
![image](https://user-images.githubusercontent.com/22209835/141886497-89696537-a5d9-4dc6-8b55-0bc619089b81.png)
![image](https://user-images.githubusercontent.com/22209835/141886581-0b1a52d3-0b9b-45d3-88d1-3be72f866a7c.png)
![image](https://user-images.githubusercontent.com/22209835/141886643-5896c1b6-d530-408a-a63c-9d8f81a5e16c.png)




