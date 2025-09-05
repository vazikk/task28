# task28
1) <b> Сначала запустил инстансы и запустил k8s через kubespray, (скачал HELM) </b> <br>



`curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash` <br>

<br>
<br>

<img width="901" height="48" alt="image" src="https://github.com/user-attachments/assets/bacf09bb-be47-4880-af6f-9e82fe2d4f80" /> <br>



<img width="1553" height="253" alt="image" src="https://github.com/user-attachments/assets/3b59a520-aca3-43a9-9922-58ced0bde94a" /> <br>

<br>
<br>

Установил StorageClass: local-path <br>


`kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.32/deploy/local-path-storage.yaml` <br>

<br>


Потом сделал его дефолтным: <br>

`kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}`

<br>
<br>


2) <b> Нашел на habor процесс усатновки мониторинга </b> <br>
<br>

1. Добавил репозиторий чартов <br>
`helm repo add grafana https://grafana.github.io/helm-charts` <br>
<br>

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

<br>

Создал namespace и установил чарт указав файл values: <br>

`kubectl create namespace monitoring` <br>

`helm install -n monitoring --values values.yaml loki-stack grafana/loki-stack` <br>

<br>

Убедился что всё установилось: <br>
<img width="1460" height="637" alt="image" src="https://github.com/user-attachments/assets/3ca14ee8-c761-4334-a56d-b37a0cc8f7e4" /> <br>

<img width="1558" height="249" alt="image" src="https://github.com/user-attachments/assets/fed0b2d9-798a-48aa-86cd-2feba09b6f51" /> <br>

<img width="1601" height="290" alt="image" src="https://github.com/user-attachments/assets/be1bc24b-0567-481f-b69c-d615e631501a" /> <br>

<br>
Потом запустил nginx и apache: <br>

<img width="918" height="307" alt="image" src="https://github.com/user-attachments/assets/82bef824-405d-4f41-9db4-05fbda179e6b" /> <br>

<img width="745" height="253" alt="image" src="https://github.com/user-attachments/assets/28d8ce3b-7ec1-45cd-9562-10289f727676" /> <br>

<br>

Ну и собственно дашборды: <br>

<img width="936" height="314" alt="image" src="https://github.com/user-attachments/assets/ab2f1528-8e94-4779-a365-759c3db997c9" /> <br>

<img width="919" height="320" alt="image" src="https://github.com/user-attachments/assets/8be80a52-83fd-48f7-bb7f-b79cb6ad94c0" /> <br>

<img width="920" height="290" alt="image" src="https://github.com/user-attachments/assets/266b9cbe-355d-4e11-81c8-a363010587a0" /> <br>

<br>

Логи apache: <br>


<img width="1247" height="252" alt="image" src="https://github.com/user-attachments/assets/54e6aad6-ea01-419c-acf1-dc9fc057bb5d" /> <br>

<br>
Логи nginx: <br>

<img width="1606" height="246" alt="image" src="https://github.com/user-attachments/assets/0db5d542-dbb3-4225-a9d7-70cece51ce53" /> <br>


3) Создание алерта в слак <br>

Добавил в grafana направление для алерта: <br>

<img width="892" height="622" alt="image" src="https://github.com/user-attachments/assets/54892ada-a020-4cf1-bdf7-c28dce4a0ebd" /> <br>

<br>

Проверил: <br>
<img width="474" height="331" alt="image" src="https://github.com/user-attachments/assets/8e8204d1-729b-4869-8489-907a6f716540" /> <br>

<br>

Создал 1 дашборд для проверки работы node и настроил Alert rule для оповещения о падении: <br>

<img width="939" height="387" alt="image" src="https://github.com/user-attachments/assets/40396c11-8472-4e9d-8ce1-9de7a186f4f8" /> <br>

<img width="1273" height="647" alt="image" src="https://github.com/user-attachments/assets/c273aa6c-6010-4bb6-bef8-5ded2990d6ec" /> <br>

Проверка работы: <br>

<img width="988" height="311" alt="image" src="https://github.com/user-attachments/assets/e3bcaa4b-b2d5-440b-b535-9da9a44b83c3" /> <br>

<img width="859" height="343" alt="image" src="https://github.com/user-attachments/assets/73cdf198-dfcb-401a-9abd-891efb0c783a" /> <br>






