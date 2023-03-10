# AKS:具有现有存储帐户的永久卷

> 原文：<https://dev.to/cmendibl3/aks-persistent-volume-with-existing-storage-account-8i0>

为了使用现有存储帐户在 AKS 群集中部署永久卷，您应该采取以下步骤:

1.  创建一个引用存储帐户的**存储类**。
2.  使用用于访问存储帐户的凭据创建一个**秘密**。
3.  创建一个引用存储类、机密和文件共享的**持久卷**。
4.  创建一个**持久卷声明**，通过名称引用该卷。

使用下面的 **yaml** 作为上述资源的模板。保存内容为**aks-existing-storage-account-PV . YAML**:

```
--------
# Create a StorageClass object pointing to the existing Storage Account
# Remember: that the Storage account must be in the same Resource Group where the AKS cluster is deployed
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
parameters:
  storageAccount: <storage account name>
  location: <storage account location>

--------
# Create a Secret to hold the name and key of the Storage Account
# Remember: values are base64 encoded
apiVersion: v1
kind: Secret
metadata:
  name: azurefile-secret
type: Opaque
data:
  azurestorageaccountname: <base64 encoded storage account name>
  azurestorageaccountkey: <base64 encoded storage account key>

--------
# Create a persistent volume, with the corresponding StorageClass and the reference to the Azure File secret.
# Remember: Create the share in the storage account otherwise the pods will fail with a "No such file or directory"
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nginx-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
  - ReadWriteOnce
  storageClassName: azurefile
  azureFile:
    secretName: azurefile-secret
    shareName: <Share Name (must already exist in the storage account)>
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000

--------
# Create a PersistentVolumeClaim referencing the StorageClass and the volume
# Remember: this is a static scenario. The volume was created in the previous step.
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteOnce  
  resources:
    requests:
      storage: 5Gi
  storageClassName: azurefile
  volumeName: nginx-pv 
```

Enter fullscreen mode Exit fullscreen mode

部署到您的集群并验证私有卷声明状态是否为**绑定** :

```
kubectl apply -f aks-existing-storage-account-pv.yaml
kubectl get pvc 
```

Enter fullscreen mode Exit fullscreen mode

结果应该类似于:

```
NAME STATUS VOLUME CAPACITY ACCESS MODES STORAGECLASS AGE
nginx-pvc Bound nginx-pv 5Gi RWO azurefile ... 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在，您可以在引用私有卷声明的容器中挂载一个卷，如下所示:

```
--------
# Deploy an nginx mounting a volume and referencing the persisten volume claim
# Remember: using pvc decouples your deployment from the volume implementations
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:  
  template:
    metadata:
      labels:
        app: nginx-storage
    spec:
      containers:
      - name: nginx-pod
        image: nginx:1.15.5
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        volumeMounts:
        - mountPath: "/mnt/azure"
          name: volume
      volumes:
        - name: volume
          persistentVolumeClaim:
            claimName: nginx-pvc 
```

Enter fullscreen mode Exit fullscreen mode

希望有帮助。

请在此下载所有代码和文件[。](https://github.com/cmendible/kubernetes.samples/tree/master/16.aks-pv-with-existing-storage-account)