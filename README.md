# Задача
Развернуть отказоустойчивый и масштабируемый кластер RabbitMQ в среде k8s

# Disclaimer
Поскольку задача не накладывает ограничений на используемые версии RabbitMQ и на версию k8s, то здесь и далее:

  - RabbitMQ 3.8
  - Kubernetes 1.17

# Requirements
- Установленный менеджер пакетов helm на рабочей станции инженера

# Решение
 - Для работы механизма peer discovery в среде kubernetes будет использоваться нативный плагин [rabbitmq_peer_discovery_k8s];
 - Для развертывания в среде Kubernetes, вместо "изобретения велосипеда", используем helm chart [stable/rabbitmq-ha];
 - Для активации механизма HA создадим файл ha.yaml, который переопределит узел definitions.policies файла values.yaml, поставляемого вместе с чартом

```yaml
definitions:
  policies: |-
    {
        "name": "ha",
        "pattern": ".*",
        "vhost": "/",
        "definition": {
            "ha-mode": "all",
            "ha-sync-mode": "automatic",
            "ha-sync-batch-size": 1
        }
    }
```

# Запуск

Запуск осществляется командой, приведенной ниже с указанием пути к файлу ha.yaml, который был создан ранее

```sh
$ helm install --name mbox-test-rabbit-ha -f path/to/ha.yaml stable/rabbitmq-ha
```

[rabbitmq_peer_discovery_k8s]: <https://github.com/rabbitmq/rabbitmq-peer-discovery-k8s>
[stable/rabbitmq-ha]: <https://github.com/helm/charts/tree/master/stable/rabbitmq-ha>
