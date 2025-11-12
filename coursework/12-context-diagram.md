### C4: System Context (L1)
```plantuml
@startuml MasterDeal_Context
' Подтянуть C4-PlantUML (контекстный уровень)
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml
LAYOUT_WITH_LEGEND()

' ==== Персоны ====
Person(sales, "Sales / Pre-sales", "Работа с лидами, подготовка КП/медиапланов")
Person(traffic, "Traffic / AdOps", "Запуск и ведение кампаний")
Person(fin, "Finance", "Счета, акты, сверки")
Person(am, "Account Manager", "Координация статусов, SLA, коммуникации")
Person(sec, "Security/Audit", "Политики доступа, аудит")

' ==== Система ====
System_Boundary(md, "Master-Deal") {
  System(masterdeal, "Master-Deal", "Единый источник истины по сделке: оркестрация стадий, каноническая модель, события")
}

' ==== Внешние системы ====
System_Ext(crm, "CRM/SFA", "Лиды/сделки/контакты")
System_Ext(dms, "DMS / e-Sign", "Документы, подписание")
System_Ext(ad, "Ad Platforms (DSP/SSP/AdServer)", "Кампании, статусы запуска, перформанс")
System_Ext(erp, "ERP/Billing", "Счета, платежи, закрывающие")
System_Ext(dwh, "DWH/BI", "Отчёты, витрины, SLA")
System_Ext(idm, "IDM/SSO", "Аутентификация/роли")
System_Ext(bus, "Event Bus / ESB", "Kafka/RabbitMQ")
System_Ext(llm, "LLM/AI Services", "Генерация КП/медиапланов, проверки качества")
System_Ext(notif, "Notifications", "Email/SMS/Chat")
System_Ext(extdata, "External Data", "Обогащение аккаунтов/прайсов")

' ==== Взаимосвязи (персоны ↔ система) ====
Rel(sales, masterdeal, "Создание/обновление Master-Deal, просмотр статусов")
Rel(traffic, masterdeal, "Запросы на запуск/паузу кампаний, SLA")
Rel(am, masterdeal, "Координация стадий, эскалации")
Rel(fin, masterdeal, "Финстатусы сделки, акты/счета")
Rel(sec, masterdeal, "Политики/аудит")

' ==== Интеграции (системы ↔ Master-Deal) ====
Rel(crm, masterdeal, "Лиды/сделки/стадии", "REST/Events")
Rel(masterdeal, crm, "Обратные статусы/идентификаторы", "REST/Events")

Rel(dms, masterdeal, "Подписанные документы", "API/Webhooks")
Rel(masterdeal, dms, "Версии КП/договоров", "API")

Rel(masterdeal, ad, "IO/настройки кампаний", "API")
Rel(ad, masterdeal, "Статусы/перформанс (raw)", "Events")

Rel(masterdeal, erp, "Счета/акты к оплате", "API")
Rel(erp, masterdeal, "Платежи/сверки", "API")

Rel(masterdeal, dwh, "Каноничные данные сделки, SLA, аудит", "Batch/CDC")
Rel(idm, masterdeal, "SSO/OAuth, роли", "OIDC/SAML")
Rel(masterdeal, notif, "Алерты/напоминания", "Webhook/SMTP")
Rel(extdata, masterdeal, "Обогащение", "API")

Rel(llm, masterdeal, "Ответы моделей", "API")
Rel(masterdeal, llm, "Промпты/данные сделки", "API")

Rel(bus, masterdeal, "Шинные события (raw/canonical)", "Kafka/RabbitMQ")
Rel(masterdeal, bus, "Публикация/подписка событий", "Kafka/RabbitMQ")

@enduml

```



---

### C4: Container (L2)

```plantuml
@startuml MasterDeal_Container
' Подтянуть C4-PlantUML (уровень контейнеров)
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
LAYOUT_WITH_LEGEND()

' ==== Граница системы ====
System_Boundary(md, "Master-Deal") {
  Container(api, "API Gateway", "HTTP/REST, GraphQL", "Внешний вход: валидация, rate-limit, authN/authZ")
  Container(orch, "Deal Orchestrator", "Java/Kotlin или Node.js", "Правила стадий, SLA, эскалации")
  Container(canon, "Canonical Model Store", "PostgreSQL или Document DB", "Мастер-сущности сделки, версии, ссылки на документы")
  Container(ev, "Event Router", "Kafka Streams", "Raw → Canonical → Outbound")
  Container(wf, "Workflow Engine", "BPMN/DMN", "Бизнес-процессы, правила")
  Container(audit, "Audit & Observability", "ELK / OpenTelemetry", "Логи, трассировки, аудит")
}

' ==== Персоны ====
Person(sales, "Sales/Pre-sales", "CRUD сделки, чтение статусов")
Person(ops, "AdOps/Traffic", "Операции по кампаниям")
Person(fin, "Finance", "Финансовые статусы")

' ==== Внешние контейнеры/системы ====
Container_Ext(crm, "CRM/SFA", "SaaS/On-prem", "Лиды/сделки/стадии")
Container_Ext(dms, "DMS / e-Sign", "SaaS", "Документооборот и подписание")
Container_Ext(ad, "Ad Platforms", "SaaS", "DSP/SSP/AdServer")
Container_Ext(erp, "ERP/Billing", "Система учёта", "Счета/платежи/закрывающие")
Container_Ext(dwh, "DWH/BI", "Хранилище/BI", "Отчёты, витрины, SLA")
Container_Ext(idm, "IDM/SSO", "IdP", "SSO/OIDC/SAML")
Container_Ext(bus, "Event Bus / ESB", "Kafka/RabbitMQ", "Шинные интеграции")
Container_Ext(llm, "LLM/AI", "API", "Генерация КП/медиапланов, QA")
Container_Ext(notif, "Notifications", "SMTP/Webhook", "Почта/чат/алерты")
Container_Ext(extdata, "External Data", "API", "Обогащение аккаунтов/прайсов")

' ==== Связи: пользователи ====
Rel(sales, api, "HTTP (OIDC)")
Rel(ops, api, "HTTP (OIDC)")
Rel(fin, api, "HTTP (OIDC)")

' ==== Связи внутри системы ====
Rel(idm, api, "SSO/OAuth2/OIDC")
Rel(crm, api, "Лиды/сделки/стадии", "REST")
Rel(api, orch, "Команды/запросы", "gRPC/REST")
Rel(orch, wf, "Запуск/контроль процессов", "gRPC")
Rel(orch, ev, "Публикация доменных событий", "Kafka")
Rel(ev, canon, "Канонизация/апсерт сущностей", "CDC/DAO")
Rel(api, canon, "Чтение мастера/поиск", "SQL/Read Models")
Rel(api, dms, "Версии документов", "REST")
Rel(dms, canon, "Webhook: ссылка на подписанный документ", "Webhooks")

Rel(orch, ad, "IO/настройки кампаний", "REST")
Rel(ad, ev, "Перформанс (raw events)", "Webhooks/Kafka")

Rel(orch, erp, "Счета/акты к оплате", "REST")
Rel(erp, canon, "Платежи/сверки", "REST")

Rel(canon, dwh, "Выгрузки/CDC слои", "Batch/Streams")
Rel(orch, notif, "Алерты SLA/эскалации", "Webhook/SMTP")
Rel(api, llm, "Промпты/данные сделки", "REST")
Rel(llm, api, "Ответы моделей", "REST")
Rel(audit, dwh, "Экспорт логов/трассировок/аудита", "Batch")

' ==== Интеграция через шину ====
Rel(bus, ev, "Потоки raw/canonical", "Kafka/RabbitMQ")
Rel(ev, bus, "Публикация/подписка", "Kafka/RabbitMQ")

@enduml

```