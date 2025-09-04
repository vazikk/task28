# task28
1) Сначала запустил инстансы и запустил k8s через kubespray <br>
(скачал HELM) <br>
`curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash` <br>

<img width="901" height="48" alt="image" src="https://github.com/user-attachments/assets/bacf09bb-be47-4880-af6f-9e82fe2d4f80" /> <br>


<img width="1553" height="253" alt="image" src="https://github.com/user-attachments/assets/3b59a520-aca3-43a9-9922-58ced0bde94a" /> <br>

Установил StorageClass: local-path <br>

`kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.32/deploy/local-path-storage.yaml` <br>

Потом сделал его дефолтным: <br>

kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

2) Нашел на habor процесс усатновки мониторинга <br>

1. Добавил репозиторий чартов <br>
`helm repo add grafana https://grafana.github.io/helm-charts` <br>
``


