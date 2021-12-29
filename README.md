# minio-install
- Intall Minio
  ``` 
   helm template argo-artifacts minio/minio --set service.type=LoadBalancer --set fullnameOverride=argo-artifacts -n minio  > minio.yaml
   kubectl creaete ns minio 
   kubectl apply -f minio.yaml -n minio
   ACCESS_KEY=$(kubectl get secret argo-artifacts --namespace minio -o jsonpath="{.data.accesskey}" | base64 --decode)
   SECRET_KEY=$(kubectl get secret argo-artifacts --namespace minio -o jsonpath="{.data.secretkey}" | base64 --decode)
 
  ```
- Login by Port forwarding
  ```
    kubectl port-forward -n minio service/argo-artifacts 9090:9000
    http://localhost:9090/ 
  ```
- Create bucket as my-bucket 
- Udpate workflow-controller-configmap with minio bucket information as following data 
  ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: workflow-controller-configmap
  data:
    config: |
      containerRuntimeExecutor: k8sapi
      artifactRepository:
        s3:
          bucket: my-bucket
          keyPrefix: prefix/in/bucket     #optional
          endpoint: my-minio-endpoint.default:9000        #AWS => s3.amazonaws.com; GCS => storage.googleapis.com
          insecure: true                  #omit for S3/GCS. Needed when minio runs without TLS
          accessKeySecret:                #omit if accessing via AWS IAM
            name: my-minio-cred
            key: accesskey
          secretKeySecret:                #omit if accessing via AWS IAM
            name: my-minio-cred
            key: secretkey
  ```
 
- Create Secret with access key and secret key 
  ```
  kubectl create secret generic my-minio-cred --from-literal=accessKey=${ACCESS_KEY} --from-literal=secretKey=${SECRET_KEY} -n argo-workflow
  ```
