## Диаграмма развертывания

```mermaid
flowchart LR
%% ================= Внешний периметр =================
subgraph INTERNET[Корпоративный периметр / Интернет]
CRM[CRM-система<br/>1 инстанс, SaaS]
AD_PLATFORMS[Платформы размещения рекламы<br/>множество инстансов, внешние API]
BILLING[Биллинг / ERP<br/>1 инстанс]
DWH_BI[DWH / BI-платформа<br/>кластер, ETL/BI]
LLM[LLM-сервисы<br/>внешний провайдер, облако]
end

%% ================= Корпоративное облако / ДЦ =================
subgraph DC[Корпоративное облако / ДЦ]

%% ---- Интеграционный слой ----
subgraph INTEGRATION[Интеграционная платформа]
APIGW[API Gateway<br/>2 инстанса, Nginx/Kong]
ESB[ESB / iPaaS<br/>2 инстанса, Java/Spring Integration]
EVENTBUS[Event Bus<br/>3 брокера, Kafka]
end

%% ---- Кластер приложений ----
subgraph APP_CLUSTER[Кластер приложений Kubernetes / VM]

subgraph NS_MD[Namespace: master-deal]
MD_API[MasterDeal API<br/>3 pod'а, Java/Spring Boot]
MD_WORKER[MasterDeal Worker<br/>2 pod'а, Java/Spring Boot]
end

subgraph NS_ADAPTERS[Namespace: integration-adapters]
ADAPTER_CRM[Adapter CRM<br/>2 pod'а, Java/Spring Boot]
ADAPTER_BILL[Adapter Billing<br/>2 pod'а, Java/Spring Boot]
ADAPTER_AD[Adapter Ad Platforms<br/>2 pod'а, Java/Spring Boot]
ADAPTER_DWH[Adapter DWH/BI<br/>2 pod'а, Java/Spring Boot]
end

subgraph NS_LLM[Namespace: llm-services]
LLM_ORCH[LLM Orchestrator<br/>2 pod'а, Node.js/NestJS]
end

end

%% ---- Хранилища ----
subgraph DB_CLUSTER[Кластер БД]
MD_DB[(MasterDeal Operational DB<br/>2 узла, PostgreSQL)]
INT_DB[(Integration DB<br/>2 узла, PostgreSQL)]
end

subgraph CACHE_CLUSTER[Кластер кэша]
REDIS[(Redis Cluster<br/>3 узла, Redis)]
end

%% ---- Наблюдаемость ----
subgraph OBS[Мониторинг и логирование]
METRICS[Metrics & Alerts<br/>Prometheus]
LOGS[Централизованный логинг<br/>ELK/Loki]
AUDIT[Аудит действий<br/>1 инстанс, Audit-service]
end

end

%% ================= Связи с внешними системами (в одну сторону) =================
CRM -- "HTTPS/REST" --> APIGW
BILLING -- "HTTPS/REST" --> APIGW
AD_PLATFORMS -- "HTTPS/REST/Webhook" --> APIGW
DWH_BI -- "JDBC/ETL" --> ADAPTER_DWH
LLM_ORCH -- "HTTPS/REST" --> LLM

%% ================= Внутренние связи (основные, однонаправленные) =================
%% Внешние вызовы к MasterDeal
APIGW -- "HTTPS/REST" --> MD_API

%% Доступ к БД и кэшу
MD_API -- "JDBC (PostgreSQL)" --> MD_DB
MD_WORKER -- "JDBC (PostgreSQL)" --> MD_DB

MD_API -- "JDBC (PostgreSQL)" --> INT_DB
MD_WORKER -- "JDBC (PostgreSQL)" --> INT_DB

MD_API -- "RESP/TCP" --> REDIS
MD_WORKER -- "RESP/TCP" --> REDIS

%% Событийное взаимодействие (prod/consume)
MD_API -- "Kafka (produce)" --> EVENTBUS
MD_WORKER -- "Kafka (consume)" --> EVENTBUS

ADAPTER_CRM -- "HTTPS/REST, SOAP" --> ESB
ADAPTER_BILL -- "HTTPS/REST" --> ESB
ADAPTER_AD -- "HTTPS/REST" --> ESB

ESB -- "Kafka (produce)" --> EVENTBUS
ADAPTER_CRM -- "Kafka (produce/consume)" --> EVENTBUS
ADAPTER_BILL -- "Kafka (produce/consume)" --> EVENTBUS
ADAPTER_AD -- "Kafka (produce/consume)" --> EVENTBUS
ADAPTER_DWH -- "Kafka (produce/consume)" --> EVENTBUS

%% Вызов LLM через оркестратор
MD_API -- "HTTPS/REST" --> LLM_ORCH

%% ================= Наблюдаемость (push-модель, тоже однонаправленно) =================
MD_API -- "Metrics (HTTP/Prometheus)" --> METRICS
MD_WORKER -- "Metrics (HTTP/Prometheus)" --> METRICS
APIGW -- "Metrics (HTTP/Prometheus)" --> METRICS
EVENTBUS -- "Metrics (HTTP/Prometheus)" --> METRICS

MD_API -- "Logs (TCP/HTTP)" --> LOGS
MD_WORKER -- "Logs (TCP/HTTP)" --> LOGS
APIGW -- "Logs (TCP/HTTP)" --> LOGS
ESB -- "Logs (TCP/HTTP)" --> LOGS
ADAPTER_CRM -- "Logs (TCP/HTTP)" --> LOGS
ADAPTER_BILL -- "Logs (TCP/HTTP)" --> LOGS
ADAPTER_AD -- "Logs (TCP/HTTP)" --> LOGS
ADAPTER_DWH -- "Logs (TCP/HTTP)" --> LOGS

APIGW -- "Audit events (HTTPS/REST)" --> AUDIT
MD_API -- "Audit events (HTTPS/REST)" --> AUDIT


```

### 1. Снаружи (INTERNET)
* Здесь показаны **внешние системы**: CRM, биллинг/ERP, рекламные платформы, DWH/BI и LLM-сервисы.
* Они обращаются в нашу систему через **API Gateway (APIGW)**.

### 2. Корпоративное облако / ДЦ (DC)
   Внутри DC — несколько логических узлов:

* Интеграционная платформа (INTEGRATION)
    * `API Gateway` — единая входная точка HTTP-запросов.
    * `ESB/iPaaS` — оркестрация и трансформация сообщений.
    * `Event Bus` — шина событий (Kafka) для обмена доменными и интеграционными событиями.

* Кластер приложений (APP_CLUSTER)
    * `Namespace master-deal`:
        * `MasterDeal API` — основной сервис, обрабатывает запросы, CRUD по master_deal / media_plan / contract / campaign.
        * `MasterDeal Worker` — фоновые задачи, синхронизация, агрегации.
    * `Namespace integration-adapters`: адаптеры к CRM, Billing, Ad Platforms, DWH/BI.
    * `Namespace llm-services`: `LLM Orchestrator` — шлюз к LLM-сервисам.

* Кластер БД (DB_CLUSTER)
    * `MasterDeal Operational DB` — операционная БД мета-домена (master_deal, media_plan, contract, campaign).
    * `Integration DB` — вспомогательные данные интеграций.

* Кластер кэша (CACHE_CLUSTER)
  * `Redis Cluster` — кэш медиапланов, сессий, чекпоинтов процессов.

* Мониторинг и логирование (OBS)
  * `Metrics & Alerts`, `Logs`, `Audit` — метрики, логи и аудит действий и интеграций.

### 3. Основные потоки
* Внешние системы → `API Gateway` → `MasterDeal API`.
* `MasterDeal API` и `Worker` работают с **БД** и **Redis**, публикуют/читают события через **Event Bus**.
* `MasterDeal API` вызывает `LLM Orchestrator` → внешний LLM для генерации медиапланов и КП.
* Все ключевые компоненты шлют **логи**, **метрики** и **аудит** в OBS-блоки.