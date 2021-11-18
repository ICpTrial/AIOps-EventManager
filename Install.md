1. OpenShift 環境へのログイン
1. 名前空間　cp4aiops の作成
``oc new-project cp4aiops``
1. Entitilement Key の取得
https://myibm.ibm.com/products-services/containerlibrary にアクセスし取得
```
export entitilment_key=xxxxxxxxxxxxxxxxx
```
1. PullSecret の作成
```
export entitilment_key=xxxxxxxxxxxxxxxxx
oc create secret docker-registry noi-registry-secret --docker-username=cp --docker-password=$entitilment_key --docker-server=cp.icr.io --namespace=cp4aiops
```
1. 外部から EventManagerへのアクセスを許可
```
if [ $(oc get ingresscontroller default -n openshift-ingress-operator -o jsonpath='{.status.endpointPublishingStrategy.type}') = "HostNetwork" ]; then oc patch namespace default --type=json -p '[{"op":"add","path":"/metadata/labels","value":{"network.openshift.io/policy-group":"ingress"}}]'; fi
```
1. CaseToolのダウンロード
cloudctl case save --case ibm-netcool-prod --outputdir /tmp/cases --repo https://raw.githubusercontent.com/IBM/cloud-pak/master/repo/case

1. カタログ・ソースの作成
```
/tmp/cases $ export LANG=C
/tmp/cases $ cloudctl version
Client Version: v3.7.1+2066.48bc12004306cf3d8b0ca5ef5f914cad5a7c6247
/tmp/cases $ cloudctl case launch --case ibm-netcool-prod --namespace cp4aiops  --inventory noiOperatorSetup --action install-catalog
Welcome to the CASE launcher
Attempting to retrieve and extract the CASE from the specified location
[✓] CASE has been retrieved and extracted
Attempting to validate the CASE
[✓] CASE has been successfully validated
Attempting to locate the launch inventory item, script, and action in the specified CASE
[✓] Found the specified launch inventory item, action, and script for the CASE
Attempting to check the cluster and machine for required prerequisites for launching the item
Checking for required prereqs...

Prerequisite                                                                      Result
Cluster Kubernetes version must be >=1.14.6                                       true
Kubernetes node resource must match a set of expressions defined in prereqs.yaml  true
openshift Kubernetes version must be >=1.14.6                                     true
Client has oc version 4.4.0 or greater                                            true
Client has cloudctl version v3.4.0 or greater                                     true

Required prereqs result: OK
Checking user permissions...
No user rules specified.
[✓] Cluster and Client Prerequisites have been met for the CASE
Running the CASE noiOperatorSetup launch script with the following action context: installCatalog
Executing inventory item noiOperatorSetup, action installCatalog : launch.sh
Checking arguments for install catalog action
-------------Installing catalog source-------------
catalogsource.operators.coreos.com/ibm-noi-catalog created
done
[✓] CASE launch script completed successfully
OK
```
1. Event Manager Operatorの構成

```
[✓] Found the specified launch inventory item, action, and script for the CASE
Attempting to check the cluster and machine for required prerequisites for launching the item
Checking for required prereqs...

Prerequisite                                                                      Result
Cluster Kubernetes version must be >=1.14.6                                       true
Kubernetes node resource must match a set of expressions defined in prereqs.yaml  true
 Kubernetes version must be                                                       false
openshift Kubernetes version must be >=1.14.6                                     true

Required prereqs result: OK
Checking user permissions...

Kubernetes RBAC Prerequisite                            Verbs                               Result  Reason
rbac.authorization.k8s.io.clusterroles/*                get,list,watch,create,patch,update  true
apiextensions.k8s.io.customresourcedefinitions/v1beta1  get,list,watch,create,patch,update  true
security.openshift.io.securitycontextconstraints/       get,list,watch,create,patch,update  true

User permissions result: OK
[✓] Cluster and Client Prerequisites have been met for the CASE
Running the CASE noiOperatorSetup launch script with the following action context: installOperator
Executing inventory item noiOperatorSetup, action installOperator : launch.sh
Checking install arguments for install
check for any existing operator group in cp4aiops ...
no existing operator group found
------------- Installing operator group for cp4aiops -------------
apiVersion: operators.coreos.com/v1alpha2
kind: OperatorGroup
metadata:
  name: ibm-noi-catalog-group
  namespace: cp4aiops
spec:
  targetNamespaces:
  - cp4aiops
operatorgroup.operators.coreos.com/ibm-noi-catalog-group created
done
-------------Installing via OLM-------------
serviceaccount/noi-service-account created
serviceaccount/noi-service-account patched
checking if catalog source exists ...
Error from server (NotFound): catalogsources.operators.coreos.com "ibm-noi-catalog" not found
NAME              DISPLAY           TYPE   PUBLISHER   AGE
ibm-noi-catalog   ibm-noi-catalog   grpc   IBM         6m49s
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: ibm-noi-catalog-subscription
  namespace: cp4aiops
spec:
  channel: v1.5
  name: noi
  installPlanApproval: Automatic
  source: ibm-noi-catalog
  sourceNamespace: cp4aiops
subscription.operators.coreos.com/ibm-noi-catalog-subscription created
[✓] CASE launch script completed successfully
OK
```
![image](https://user-images.githubusercontent.com/22209835/141952791-ee1b2a12-79ac-4a32-85a8-13f73312235b.png)

1. EventManager (NOI)の構成
![image](https://user-images.githubusercontent.com/22209835/141953254-82339c0c-8798-4d45-8896-12b18670aaa3.png)

1. ![image](https://user-images.githubusercontent.com/22209835/142089673-890f1b8b-06a9-419a-9266-a510417c53a1.png)

1. `cp4aiops` の名前空間にて、 Pending や CrashBackoff 状態のPodが存在しないことを確認
![image](https://user-images.githubusercontent.com/22209835/142089756-d6c573ae-8833-41fc-a035-f26ee3fbf552.png)

1. 以下のコマンドで Event Manager への ルートを確認します。
```
$ oc get routes -n cp4aiops | grep evtmanager-ibm-hdm-common-ui
```
EventManager GUIが開きます。
![image](https://user-images.githubusercontent.com/22209835/142336759-9c300d2a-e9f9-4454-b44b-0b147cc4afa6.png)

1. ログインユーザーは icpadmin です。 以下のコマンドで、自動生成された icpadmin の パスワードを取得します。
```
oc get secret evtmanager-icpadmin-secret -o json -n cp4aiops | grep ICP_ADMIN_PASSWORD  | cut -d : -f2 | cut -d '"' -f2 | base64 -d;echo
```
1. Netcool Operational Insight の画面を確認します。
![image](https://user-images.githubusercontent.com/22209835/142355493-f1ec1f99-47d6-4622-aab1-c350533adf0f.png)

