## Диаграмма развертывания

```mermaid
flowchart LR
  %% ================= Внешний периметр =================
  subgraph INTERNET[Корпоративный периметр / Интернет]
    CRM[CRM-система]
    AD_PLATFORMS[Платформы размещения рекламы<br/>'Ad Platforms']
    BILLING[Биллинг / ERP]
    DWH_BI[DWH / BI-платформа]
    LLM[LLM-сервисы<br/>'генерация медиапланов, КП']
  end

  %% ================= Корпоративное облако / ДЦ =================
  subgraph DC[Корпоративное облако / ДЦ]

    %% ---- Интеграционный слой ----
    subgraph INTEGRATION[Интеграционная платформа]
      APIGW[API Gateway]
      ESB[ESB / iPaaS<br/>'оркестрация, трансформации']
      EVENTBUS[Event Bus<br/>'Kafka / RabbitMQ']
    end

    %% ---- Кластер приложений ----
    subgraph APP_CLUSTER[Кластер приложений 'Kubernetes / VM']

      subgraph NS_MD[Namespace: master-deal]
        MD_API[MasterDeal API<br/>'REST / GraphQL']
        MD_WORKER[MasterDeal Worker<br/>'асинхронные обработки']
      end

      subgraph NS_ADAPTERS[Namespace: integration-adapters]
        ADAPTER_CRM[Adapter CRM]
        ADAPTER_BILL[Adapter Billing]
        ADAPTER_AD[Adapter Ad Platforms]
        ADAPTER_DWH[Adapter DWH/BI]
      end

      subgraph NS_LLM[Namespace: llm-services]
        LLM_ORCH[LLM Orchestrator<br/>'шлюз к LLM']
      end

    end

    %% ---- Хранилища ----
    subgraph DB_CLUSTER[Кластер БД]
      MD_DB[(MasterDeal Operational DB<br/>master_deal, media_plan,<br/>contract, campaign)]
      INT_DB[(Integration DB)]
    end

    subgraph CACHE_CLUSTER[Кластер кэша]
      REDIS[(Redis Cluster)]
    end

    %% ---- Наблюдаемость ----
    subgraph OBS[Мониторинг и логирование]
      METRICS[Metrics & Alerts]
      LOGS[Централизованный логинг]
      AUDIT[Аудит действий]
    end

  end

  %% ================= Связи с внешними системами =================
  CRM --> APIGW
  APIGW --> CRM

  BILLING --> APIGW
  APIGW --> BILLING

  AD_PLATFORMS --> APIGW
  APIGW --> AD_PLATFORMS

  DWH_BI <---> ADAPTER_DWH

  LLM_ORCH --> LLM

  %% ================= Внутренние связи (основные) =================
  APIGW --> MD_API
  MD_API --> APIGW

  MD_API --> MD_DB
  MD_WORKER --> MD_DB

  MD_API --> REDIS
  MD_WORKER --> REDIS

  MD_API --> EVENTBUS
  MD_WORKER --> EVENTBUS

  ADAPTER_CRM <---> ESB
  ESB <---> CRM

  ADAPTER_CRM --> EVENTBUS
  ADAPTER_BILL --> EVENTBUS
  ADAPTER_AD --> EVENTBUS
  ADAPTER_DWH --> EVENTBUS

  MD_API --> LLM_ORCH

  %% ================= Наблюдаемость =================
  MD_API --> LOGS
  MD_WORKER --> LOGS
  APIGW --> LOGS

  MD_API --> METRICS
  MD_WORKER --> METRICS
  APIGW --> METRICS
  EVENTBUS --> METRICS

  APIGW --> AUDIT
```

### 1. Снаружи (INTERNET)
* Здесь показаны **внешние системы**: CRM, биллинг/ERP, рекламные платформы, DWH/BI и LLM-сервисы.
* Они обращаются в нашу систему через **API Gateway (APIGW)**.

### 2. Корпоративное облако / ДЦ (DC)
   Внутри DC — несколько логических узлов:

* Интеграционная платформа (INTEGRATION)
    * `API Gateway` — единая входная точка HTTP-запросов.
    * `ESB/iPaaS` — оркестрация и трансформация сообщений.
    * `Event Bus` — шина событий (Kafka/RabbitMQ) для обмена доменными и интеграционными событиями.

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