Cloud Pak for AIOps (CP4AIOps) のライセンスには、CP4AIOps の稼働のために利用することができる OpenShift Container Storage (OCS) の entitlementが付帯します。
また、トライアル環境においては、Local Storage を利用しての環境も構成することが可能です。

## 1. Local Storage の構成
検証のためにトライアル環境を構成する場合には、Local Storageを利用して構成することが可能です。  
本番環境では Local Storage はサポートされないため、　別途 Openshift Container Storageの構成、または別ストレージ利用を検討ください。

### 1. Local Storage Operatorの導入

1. OpenShiftの環境にログインし、Red Hatが提供する Local Storage Operatorを選択します。
![image](https://user-images.githubusercontent.com/22209835/141883405-66dddd1e-5184-4258-8c62-296316f5a187.png)
1. インストールをクリックします。
![image](https://user-images.githubusercontent.com/22209835/141883508-631c36e2-aac8-4f41-ab8b-fe926ca36587.png)
1. デフォルトの設定のまま導入します。
![image](https://user-images.githubusercontent.com/22209835/141924552-2b748426-1db5-4549-978a-b41bc50bc4e0.png)
1. 正常に導入されたことを確認します
![image](https://user-images.githubusercontent.com/22209835/141883734-d02c0f6f-12d3-43b9-9f88-353fdfe80bce.png)

### 2. Local Storage の作成

1. Local Storage として利用するディスクのパスを確認します。
    ```
    ~ $ oc get nodes
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
1. BlockStorage用 LocalVolume の作成します。Local Storage Operator を開き、Local Volume の作成をクリックします。
![image](https://user-images.githubusercontent.com/22209835/141884695-b89a1014-405a-4053-a1a5-482cb532b12c.png)
1. ひとつ前の手順で確認した デバイス・パスを指定し、任意の Storage Class Nameを指定します。
![image](https://user-images.githubusercontent.com/22209835/141885093-a0c8786a-a1c6-4df8-987b-745d76b1f77a.png)
1. Local Volumeが作成され Available となったことを確認します。
![image](https://user-images.githubusercontent.com/22209835/141885145-735e670f-1e8f-409d-929a-0b49733e42f6.png)
1. StorageClass が作成されていることを確認します
![image](https://user-images.githubusercontent.com/22209835/141885342-5c6ed64a-8da4-4bab-a40f-362b3bb19e3d.png)
1. 永続ボリュームが作成されていることを確認します。
![image](https://user-images.githubusercontent.com/22209835/142358877-7d91f796-8502-4963-9cf1-cbbdef356336.png)

### 3. CP4AIOps EventManagerが利用する Storage Class および Persistent Volume を作成します。
繰り返しになりますが、Local Storageの構成は本番環境ではサポートされません。

1. [このリンク](https://www.ibm.com/support/pages/node/6245752) から CP４AIOps EventManager(Netcool Operational Insight)用に StorageClassおよびPersitentVolume を定義するスクリプトをダウンロードします。
1. ターミナルから OpenShift環境に入り、`createStorageAllNode.sh`を実行します。
ホスト名が長く、Kubernetesリソース命名規則の64文字を超えてしまう場合などは、スクリプト内のリソース名を適宜変更して実行ください。 
この環境では、local-storage を ls に置き換えて実行しています。
    ```
    ./createStorageAllNodes.sh
    This script will create local Storage Classes, Persistant Volumes and directories required by Netcool Operations Insight
    Enter y to continue: y

    storageclass.storage.k8s.io/ls-cassandra created
    storageclass.storage.k8s.io/ls-cassandra-bak created
    storageclass.storage.k8s.io/ls-kafka created
    storageclass.storage.k8s.io/ls-zookeeper created
    storageclass.storage.k8s.io/ls-couchdb created
    storageclass.storage.k8s.io/ls-db2 created
    storageclass.storage.k8s.io/ls-impactgui created
    storageclass.storage.k8s.io/ls-ncobackup created
    storageclass.storage.k8s.io/ls-ncoprimary created
    storageclass.storage.k8s.io/ls-openldap created
    storageclass.storage.k8s.io/ls-scala created
    storageclass.storage.k8s.io/ls-nciserver created
    storageclass.storage.k8s.io/ls-elastic created
    storageclass.storage.k8s.io/ls-observer created
    storageclass.storage.k8s.io/ls-topology-cassandra created
    storageclass.storage.k8s.io/ls-topology-cassandra-bak created
    storageclass.storage.k8s.io/ls-topology-elastic created
    persistentvolume/noi-ls-cassandra-worker0.itzocp-3100008gyq-uqq62uc2.cp.fyre.ibm.com created 
    （後略）
    ```
1. 作成されたストレージクラスを確認します。
    ```
     $ oc get sc
    NAME                        PROVISIONER                                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
    ls-cassandra                kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-cassandra-bak            kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-couchdb                  kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-db2                      kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-elastic                  kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-impactgui                kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-kafka                    kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-nciserver                kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-ncobackup                kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-ncoprimary               kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-observer                 kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-openldap                 kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-scala                    kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ls-topology-cassandra       kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-topology-cassandra-bak   kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-topology-elastic         kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  69m
    ls-zookeeper                kubernetes.io/no-provisioner                    Delete          WaitForFirstConsumer   false                  70m
    ```

## 2. OpenShift Container Storage の導入

### 1. Openshift Container Storage Operatorの導入
1. OperatorHub より OpenShift Container Storage Operatorを選択します。
![image](https://user-images.githubusercontent.com/22209835/141885574-3cee8e6b-19ae-4710-aee8-56838707a095.png)
1. インストールをクリックします。
![image](https://user-images.githubusercontent.com/22209835/141885641-85da113b-7e8d-457f-8f99-ff04ea7af92b.png)
1. デフォルトの設定のまま、インストールをクリックします。
![image](https://user-images.githubusercontent.com/22209835/141885720-d337bf45-a8b2-45cb-9369-d282a72acecd.png)

### 2. Storage Clusterの構成
1. StorageClusterオペランドの構成が求められますので、作成してきます。
![image](https://user-images.githubusercontent.com/22209835/141886104-83ba1997-38b2-40d1-899f-8664fde5fd3e.png)
1. Internal attachedDevices を選択し、先程作成した LocalStorage の StorageClass を指定します。展開するノードは適宜判断下さい。
![image](https://user-images.githubusercontent.com/22209835/141886497-89696537-a5d9-4dc6-8b55-0bc619089b81.png)
1. セキュリティおよびネットワークの構成は、デフォルトのまま次へをクリックします。お客様要件で ストレージの暗号化が求められている場合には、暗号の有効化を選択してください。
![image](https://user-images.githubusercontent.com/22209835/141886581-0b1a52d3-0b9b-45d3-88d1-3be72f866a7c.png)
1. 内容を確認して、`作成`をクリックします。
![image](https://user-images.githubusercontent.com/22209835/141886643-5896c1b6-d530-408a-a63c-9d8f81a5e16c.png)
1. Storage Cluster が Ready となったことを確認します。
![image](https://user-images.githubusercontent.com/22209835/141926702-e7887cb4-030f-4a63-a2cf-98bb3641a721.png)
