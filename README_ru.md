# `cilium-s3`: экспорт флоу-логов Cilium в Yandex Cloud Object Storage

<img width="1081" alt="Снимок экрана 2021-10-23 в 20 40 23" src="https://user-images.githubusercontent.com/85429798/138566364-3f6beb5b-aab9-4bb3-8d14-c7f108aaa1d6.png">

<img width="607" alt="Снимок экрана 2021-10-23 в 20 38 08" src="https://user-images.githubusercontent.com/85429798/138566328-f1a32606-47aa-4a4d-bf68-a346d3c87a74.png">

<img width="607" alt="Снимок экрана 2021-10-23 в 20 38 08" src="https://user-images.githubusercontent.com/85429798/138566529-cf6aadb4-df28-4de1-83ce-360523a12588.png">



# Версия

**Версия 1.0**
- Чейнджлог:
    - Первая версия.
- Docker-образы:
    - `cr.yandex/sol/cilium-s3:1`
- Helm-чарт:
    - `cr.yandex/sol/cilium-s3-chart:0.1.0`

## Описание работы решения

Подключается через gRPC к компоненту `hubble-relay` и отправляет события NetFlow в Object Storage.
После этого можно извлечь эти события из Object Storage в любую SIEM-систему, используя [GeeseFS](https://yandex.cloud/en/docs/storage/tools/geesefs) или другие совместимые с AWS плагины.

В качестве альтернативы можно использовать готовые интеграции Object Storage в SIEM-системах:
- [Логи и события Object Storage в Splunk](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-Splunk).
- Флоу-логи Cilium в Elasticsearch (скоро будет доступно).

## Установка через Helm

#### Предварительные условия 

- :white_check_mark: Yandex Managed Service for Kubernetes® [с включенным Cilium CNI](https://yandex.cloud/en/docs/managed-kubernetes/quickstart#kubernetes-cluster-create).
- :white_check_mark: [Бакет Object Storage](https://yandex.cloud/en/docs/storage/quickstart).
- :white_check_mark: [Заранее созданные статические ключи для сервисного аккаунта](https://yandex.cloud/en/docs/iam/operations/sa/create-access-key).
- :white_check_mark: [Установленный клиент Helm](https://helm.sh/ru/docs/intro/install/).

#### Установите Helm-чарт 

Установите Helm-чарт, заменив значения на свои собственные (указанные в условиях для использования):

```Python
helm install cilium-s3-chart oci://cr.yandex/sol/cilium-s3-chart --version 0.1.0 --namespace cilium-s3 --create-namespace \
--set yandex.secretaccesskey=<your-secretaccesskey> \
--set yandex.bucket=<your-Bucket-name> \
--set yandex.accesskeyid=<your-accesskeyid> \
--set yandex.prefix=<your-secretaccesskey> (например:k8s-cilium-flow-logs/cluster-id-1232145gfg) 

```

```
Helm values:
yandex:
-    accesskeyid: ""  # yandex access key
-    secretaccesskey: ""  # yandex secret access key
-    bucket: ""  # Yandex storage, bucket name
-    hubble_url: "hubble-relay.kube-system.svc.cluster.local:80" # Hubble-url
-    prefix: "k8s-cilium-flow-logs/" # Prefix of bucket folder
-    region: "ru-central1" # region of S3
-    endpoint: "https://storage.yandexcloud.net" # endpoint of S3
```
