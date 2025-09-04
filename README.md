# task28
1) Сначала запустил инстансы и запустил k8s через kubespray, (скачал HELM) <br>


<br>


`curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash` <br>

<br>

<img width="901" height="48" alt="image" src="https://github.com/user-attachments/assets/bacf09bb-be47-4880-af6f-9e82fe2d4f80" /> <br>


<img width="1553" height="253" alt="image" src="https://github.com/user-attachments/assets/3b59a520-aca3-43a9-9922-58ced0bde94a" /> <br>

Установил StorageClass: local-path <br>

`kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.32/deploy/local-path-storage.yaml` <br>

Потом сделал его дефолтным: <br>

kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

2) Нашел на habor процесс усатновки мониторинга <br>

1. Добавил репозиторий чартов <br>
`helm repo add grafana https://grafana.github.io/helm-charts` <br>


Создал файл values.yaml, где укажем Helm установить помимо Loki и Promtail также Grafana и Prometheus: <br>
```
prometheus:
  enabled: true
  isDefault: false
  url: http://{{ include "prometheus.fullname" .}}:{{ .Values.prometheus.server.service.servicePort }}{{ .Values.prometheus.server.prefixURL }}
  datasource:
    jsonData: "{}"

loki:
  enabled: true
  isDefault: true
  url: http://{{(include "loki.serviceName" .)}}:{{ .Values.loki.service.port }}
  readinessProbe:
    httpGet:
      path: /ready
      port: http-metrics
    initialDelaySeconds: 45
  livenessProbe:
    httpGet:
      path: /ready
      port: http-metrics
    initialDelaySeconds: 45
  datasource:
    jsonData: "{}"
    uid: ""

promtail:
  enabled: true
  config:
    logLevel: info
    serverPort: 3101
    clients:
      - url: http://{{ .Release.Name }}:3100/loki/api/v1/push

grafana:
  enabled: true 
  sidecar:
    datasources:
      label: ""
      labelValue: ""
      enabled: true
      maxLines: 1000
  image:
    tag: 10.0.2
```

Создал namespace и установил чарт указав файл values: <br>
`kubectl create namespace monitoring` <br>
`helm install -n monitoring --values values.yaml loki-stack grafana/loki-stack` <br>

Убедился что всё установилось: <br>
<img width="1460" height="637" alt="image" src="https://github.com/user-attachments/assets/3ca14ee8-c761-4334-a56d-b37a0cc8f7e4" /> <br>
<img width="1558" height="249" alt="image" src="https://github.com/user-attachments/assets/fed0b2d9-798a-48aa-86cd-2feba09b6f51" /> <br>
<img width="1601" height="290" alt="image" src="https://github.com/user-attachments/assets/be1bc24b-0567-481f-b69c-d615e631501a" /> <br>

Потом запустил nginx и apache: <br>
<img width="918" height="307" alt="image" src="https://github.com/user-attachments/assets/82bef824-405d-4f41-9db4-05fbda179e6b" /> <br>
<img width="745" height="253" alt="image" src="https://github.com/user-attachments/assets/28d8ce3b-7ec1-45cd-9562-10289f727676" /> <br>

Ну и собственно дашборды: <br>
<img width="936" height="314" alt="image" src="https://github.com/user-attachments/assets/ab2f1528-8e94-4779-a365-759c3db997c9" /> <br>






