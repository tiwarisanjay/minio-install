# minio-install
- Intall Minio
  ``` 
  kustomize build github.com/minio/operator/?ref=v4.3.9 | kubectl apply --filename - 
  ```
  Check newer version of minio before installing. 
- Port forward for UI 
  ```
  kubectl port-forward -n minio-operator service/console 9090:9090
  ```
- To Login at minio using URL[http://localhost:9090/]
- Get JWT with following command 
  ```
  kubectl -n minio-operator get secret $(kubectl -n minio-operator get serviceaccount console-sa -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
  ````
  Make sure you have base64 lib installed on the machine to decode the token. 
  
