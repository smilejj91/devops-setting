# spinnaker-setting

### How to setup spinnaker

- pre-cond : install k8s, minio (you should make 'spinnaker' bucket)
  - copy .kube folder to working dir
  - mkdir .hal folder in working dir

- Run docker container
```bash
$ docker compose up -d
```

- Connect halyard container to setup spinnaker
```bash
$ docker exec -it halyard bash
```
  - Provider setup (k8s)
```bash
# https://spinnaker.io/docs/setup/install/providers/kubernetes-v2/

$ hal config provider kubernetes enable
$ hal config provider kubernetes account add my-k8s-account --provider-version v2 --context $(kubectl config current-context)
$ hal config deploy edit --type distributed --account-name $ACCOUNT
```
  - Storage setup (Minio)
```bash
# https://spinnaker.io/docs/setup/install/storage/minio/

$ hal config storage s3 edit --access-key-id admin --endpoint http://{minio IP}:{minio port} --secret-access-key tmaxos123! --bucket spinnaker
$ hal config storage edit --type s3
```

  - Docker registry setup (Harbor) (current spinnaker not support harbor)
```bash
# https://spinnaker.io/docs/setup/install/providers/docker-registry/

$ hal config provider docker-registry enable
$ hal config provider docker-registry account add my-docker-registry --address https://{harbor IP}:{harbor port} --username admin --password 1234
``` 

  - Github setup
```bash
# https://spinnaker.io/docs/setup/other_config/artifacts/github/

$ hal config artifact github enable
$ echo $TOKEN > $TOKEN_FILE
$ ARTIFACT_ACCOUNT_NAME=my-github-artifact-account
$ hal config artifact github account add $ARTIFACT_ACCOUNT_NAME --token-file $TOKEN_FILE
``` 
  - Install spinnaker
```bash
# https://spinnaker.io/docs/setup/install/deploy/

# show version
$ hal version list

# install spinnaker
$ hal config version edit --version 1.29.0

# edit spin-deck, spin-gate service (if you use ingress, it does not need)
$ hal config security ui edit --override-base-url http://{k8s node IP}:{spin-deck port}
$ hal config security api edit --override-base-url http://{k8s node IP}:{spin-gate port}

$ hal deploy apply

# change spin-deck/spin-gate service port (ClusterIP to NodePort)
$ kubectl edit service spin-deck --namespace spinnaker
$ kubectl edit service spin-gate --namespace spinnaker
```

- Join spinnaker dashboard
```bash
$ curl http://{k8s node IP}:{spin-deck port}
```

