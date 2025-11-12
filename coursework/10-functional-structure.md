# Функциональная структура предметной области «Master-Deal»

## 1. Иерархия функций

1. **Управление лидами и квалификация**

* Приём лидов (формы, импорт, API), дедупликация
* ICP/fit-скоринг, обогащение данных, валидация контактов
* Передача в проспект/инициацию мастер-сделки

2. **Аккаунты и иерархия клиентов**

* Компания, холдинг, бренды, продукты/линейки
* Контакты и роли (decision maker, buyer, finance)
* Правила консолидации и MDM-идентификаторы

3. **Инициация и жизненный цикл Master-Deal**

* Создание карточки мастер-сделки и версионирование
* Статусы/этапы: Инициация → Пресейл → КП → Переговоры → Подписание → Запуск → Исполнение → Закрытие
* Связи с под-сделками/кампаниями/Work Orders

4. **Бриф и требования клиента**

* Захват брифа (анкеты, файлы), нормализация
* Цели, KPI, гео/аудитории, ограничения (brand safety, частоты)

5. **Медиапланирование**

* Подбор инвентаря/каналов, частоты, даты, бюджеты
* Пакеты размещений, прайс-карты, сценарии оптимизации
* Валидатор медиаплана и конфликты

6. **Коммерческое предложение (КП)**

* Генерация версий КП из медиаплана
* Дисконт-политики, наценки, спецусловия
* История согласований и сравнение версий

7. **Ценообразование и маржинальность**

* Правила прайсинга, нетто/брутто, комиссии
* Плановая/фактическая маржа, пороги эскалаций
* Валюты и курсы

8. **Договоры и юридические артефакты**

* Договор/доп. соглашения, SLA-приложения
* Юр. проверка, E-подпись, хранение шаблонов
* Сопоставление условий договора с КП/медиапланом

9. **Заказы на выполнение (IO/WO) и бронирование инвентаря**

* Формирование IO/Insertion Order, бронь инвентаря
* Разбивка по площадкам/форматам/таймслотам
* Управление изменениями (change orders)

10. **Интеграция с Ad-системами и трейдингом**

* Экспорт кампаний в DSP/AdServer/SSP
* Теги/пиксели, UTM/UTID, согласование трекинга
* Обратный импорт статусов и ограничений площадок

11. **Запуск и исполнение кампаний**

* Чек-листы pre-launch, запуск по календарю
* Оперативные правки (caps, bids, creatives)
* Заморозка/перезапуск по правилам

12. **Управление креативами**

* Приём, модерация, соответствие спецификациям
* Версии, адаптации, хранилище ассетов
* Мэппинг креативов к плейсментам

13. **SLA и контроль исполнения (сквозной)**

* Нормативы по этапам pipeline
* Трекер отклонений, эскалации
* Gantt/борд стадий, time-to-launch

14. **События и телеметрия (сырые/канонические)**

* Приём сырых событий (impression/click/conversion, статусы, ошибки)
* Канонизация/нормализация, словари событий
* Шина событий для триггеров, webhooks

15. **Отчётность и аналитика**

* План-факт по бюджетам, delivery, KPI
* Дэшборды кампаний и мастер-сделок
* Экспорт в BI/DWH, витрины данных

16. **Финансы и биллинг**

* PO/сметы, акты/инвойсы, графики оплат
* Реконсиляция поставщика/клиента, недопоставка/оверделивери
* Налоги, удержания, агентские схемы

17. **Управление рисками и соответствием**

* Лимиты кредитования, KYC/санкционные проверки
* Brand safety, юр. ограничения отраслей
* Журнал инцидентов и пост-морем

18. **Workflow и согласования**

* Маршруты согласования (коммерция, юристы, финансы)
* Правила эскалаций и SLA по задачам
* Комментарии, упоминания, чек-листы

19. **Уведомления и коммуникации**

* Шаблоны писем/уведомлений, мультиканальность
* Триггеры по событиям и дедлайнам
* Ленты активности в карточке сделки

20. **Управление доступом и безопасность**

* Роли и зоны ответственности (RBAC/ABAC)
* Аудит действий и журнал изменений
* Псевдонимизация/шифрование персональных данных

21. **Нормативно-справочная информация (NSI/MDM)**

* Справочники: каналы, форматы, площадки, валюты, единицы измерения
* Контрагенты/реквизиты, прайс-листы поставщиков
* Версионирование и жизненный цикл справочников

22. **Интеграции и шины данных**

* API Gateway (CRUD по сущностям, search, webhooks)
* Event Bus/Kafka темы домена
* Коннекторы к CRM, ERP, DWH, биллингу

23. **Качество данных и соответствие схемам**

* Валидации, дедупликация, правила слияния
* Контроль целостности ссылок (deal → план → IO → отчёт)
* Мониторинг дрейфа схем/контрактов API

24. **Администрирование и конфигурирование домена**

* Настройка стадий, правил прайсинга, SLA, шаблонов
* Импорт/экспорт настроек (environments)
* Управление версиями конфигураций

25. **Помощники на базе LLM/автоматизация**

* Генерация КП/медиапланов/писем на основе брифа
* Объяснения отклонений KPI, подсказки по оптимизации
* Резюмирование переписок и протоколов встреч

26. **Мониторинг и наблюдаемость**

* Метрики приложений/интеграций, алёрты
* Трассировка сквозных сценариев (deal id correlation)
* Здоровье очередей, ретраи, dead-letter

27. **Импорт/миграции и архив**

* Пакетный импорт исторических сделок/документов
* Архивация закрытых сделок, политики хранения
* Удаление/анонимизация по требованиям регуляторов

28. **UX-слой и рабочие места ролей**

* Карточка Master-Deal (360°)
* Борды по стадиям, календари запусков
* Мастер-формы для быстрого онбординга сделок

---

## 2. Ядро доменных сущностей (ER)

Ключевые сущности: `MasterDeal`, `Account`/`Brand`, `Contact`, `Brief`, `MediaPlan`, `Proposal`, `Contract`, `Campaign`, `Placement`, `Creative`, `IO`, `Invoice`, `Event`, `SLARecord`, `PriceCard`, `Dictionary*`.

```mermaid
erDiagram
  Account ||--o{ Brand : has
  Account ||--o{ Contact : includes
  Account ||--o{ MasterDeal : owns

  MasterDeal ||--o{ Brief : has
  MasterDeal ||--o{ MediaPlan : has
  MasterDeal ||--o{ Proposal : has
  MasterDeal ||--o{ Contract : results_in
  MasterDeal ||--o{ Campaign : decomposes_to
  MasterDeal ||--o{ SLARecord : tracks
  MasterDeal ||--o{ IO : issues
  MasterDeal ||--o{ Invoice : bills
  MasterDeal ||--o{ Event : emits

  MediaPlan ||--o{ Placement : defines
  Placement }o--|| PriceCard : priced_by
  Campaign ||--o{ Placement : executes
  Placement }o--o{ Creative : uses

  Contract ||--o{ Addendum : includes
  IO ||--o{ LineItem : contains
  Invoice ||--o{ LineItem : contains

  Event }o--|| DictionaryEventType : typed_by
  Creative }o--|| DictionarySpec : conforms_to
  Placement }o--|| DictionaryInventory : maps_to
```

**Мини-описание сущностей**

* **MasterDeal** — агрегирующая сущность сквозного процесса (ID, статус, владелец, валюты, маржа, ссылки).
* **Brief** — цели/KPI, аудитории, ограничения, вложения.
* **MediaPlan** — каналы, плейсменты, ставки, периоды, бюджеты.
* **Proposal** — версии КП, условия, скидки/наценки, согласования.
* **Contract/Addendum** — условия, SLA-приложения, стороны, подписи.
* **Campaign** — реализации в ad-системах, статусы, трекинг.
* **Placement** — площадка/формат/инвентарь, частоты/капсы.
* **Creative** — ассеты, версии, спецификации/модерация.
* **IO/LineItem** — заказы на выполнение, детализация.
* **Invoice/LineItem** — счет/акт, суммы/налоги/статусы оплат.
* **Event** — сырые/канонические события, корреляция по deal-id.
* **SLARecord** — нормативы/факты по стадиям pipeline.
* **PriceCard** — прайс-карты поставщиков/наценки.
* **Dictionary*** — справочники домена (площадки, форматы, валюты и т.п.).

---

## 3. Точки интеграции и потоки данных

* **CRM ↔ Master-Deal**
  Импорт лидов/аккаунтов/контактов; обратная синхронизация стадий сделки, сумм, вероятностей; webhooks на изменение статусов/согласований.

* **ERP/Биллинг ↔ Финансы**
  PO/инвойсы/акты, платежные статусы, налоги; реконсиляция план-факт.

* **Ad-системы (DSP/AdServer/SSP) ↔ Кампании**
  Экспорт кампаний/плейсментов/креативов; обратные статусы, delivery и ошибки; трекинг-пиксели/UTM.

* **Event Bus (Kafka) ↔ Телеметрия/SLA**
  Темы сырых событий (`ad.raw.*`), канонизированные темы (`ad.canon.*`); триггеры SLA/уведомлений.

* **DWH/BI ↔ Отчётность**
  Инкрементальная выгрузка витрин (deal, plan, io, spend, delivery, kpi); дашборды план-факт, маржа, TtL.

* **MDM/НСИ**
  Справочники контрагентов/площадок/форматов/валют; версии и согласование изменений.

---

# Логическая модель данных для обеспечения функциональной структуры

```mermaid
classDiagram
  direction LR

  class Account {
    <<entity>>
    +account_id: UUID «PK»
    +name: string
    +inn_or_vat: string?
    +country_code: string
    +mdm_key: string?
    +status: string
    +created_at: datetime
    +updated_at: datetime
  }

  class Brand {
    <<entity>>
    +brand_id: UUID «PK»
    +account_id: UUID «FK»
    +name: string
    +category: string?
    +active: bool
  }

  class Contact {
    <<entity>>
    +contact_id: UUID «PK»
    +email: string?
    +phone: string?
    +full_name: string
    +title: string?
    +gdpr_consent: bool
  }

  class AccountContact {
    <<bridge>>
    +account_id: UUID «FK»
    +contact_id: UUID «FK»
    +role_code: string  // DM/Buyer/Finance/Legal
    +is_primary: bool
    «PK(account_id, contact_id, role_code)»
  }

  class MasterDeal {
    <<aggregate>>
    +deal_id: UUID «PK»
    +account_id: UUID «FK»
    +brand_id: UUID «FK?»
    +owner_user_id: UUID
    +name: string
    +stage_code: string  // pipeline stage
    +currency: string    // ISO 4217
    +probability: int?   // 0..100
    +planned_margin_pct: decimal(5,2)?
    +go_live_date: date?
    +created_at: datetime
    +updated_at: datetime
  }

  class DealStageHistory {
    <<log>>
    +id: UUID «PK»
    +deal_id: UUID «FK»
    +from_stage: string
    +to_stage: string
    +changed_by: UUID
    +changed_at: datetime
    +sla_due_at: datetime?
  }

  class Brief {
    <<entity>>
    +brief_id: UUID «PK»
    +deal_id: UUID «FK»
    +goals: text
    +kpi_json: jsonb
    +audiences_json: jsonb
    +constraints_json: jsonb
    +attachments_uri: string?
    +version: int
    +created_at: datetime
  }

  class MediaPlan {
    <<entity>>
    +plan_id: UUID «PK»
    +deal_id: UUID «FK»
    +name: string
    +version: int
    +total_budget: money
    +start_date: date?
    +end_date: date?
    +status: string  // draft/approved
    +created_at: datetime
  }

  class MediaPlanItem {
    <<detail>>
    +item_id: UUID «PK»
    +plan_id: UUID «FK»
    +inventory_code: string «FK DictInventory»
    +channel: string
    +format: string
    +qty: int?
    +rate: money?
    +budget: money
    +freq_cap: int?
    +targeting_json: jsonb?
  }

  class PriceCard {
    <<ref/pricing>>
    +pricecard_id: UUID «PK»
    +supplier_id: UUID?
    +inventory_code: string «FK DictInventory»
    +base_rate: money
    +currency: string
    +valid_from: date
    +valid_to: date?
  }

  class Proposal {
    <<entity>>
    +proposal_id: UUID «PK»
    +deal_id: UUID «FK»
    +plan_id: UUID «FK»
    +version: int
    +discount_pct: decimal(5,2)?
    +uplift_pct: decimal(5,2)?
    +net_amount: money
    +gross_amount: money
    +status: string // draft/sent/approved/rejected
    +approved_at: datetime?
  }

  class Contract {
    <<entity>>
    +contract_id: UUID «PK»
    +deal_id: UUID «FK»
    +number: string
    +signed_at: date?
    +e_sign_doc_id: string?
    +sla_json: jsonb?
    +terms_hash: string
    +status: string // draft/sent/signed/terminated
  }

  class Addendum {
    <<entity>>
    +addendum_id: UUID «PK»
    +contract_id: UUID «FK»
    +number: string
    +signed_at: date?
    +diff_json: jsonb
  }

  class IO {
    <<order>>
    +io_id: UUID «PK»
    +deal_id: UUID «FK»
    +plan_id: UUID «FK»
    +po_number: string?
    +status: string // draft/confirmed/cancelled
    +created_at: datetime
  }

  class IOLineItem {
    <<detail>>
    +io_line_id: UUID «PK»
    +io_id: UUID «FK»
    +plan_item_id: UUID «FK»
    +quantity: int?
    +unit_rate: money?
    +amount: money
  }

  class Campaign {
    <<entity>>
    +campaign_id: UUID «PK»
    +deal_id: UUID «FK»
    +ext_platform: string // DSP/AdServer
    +ext_campaign_id: string?
    +status: string // planned/active/paused/ended
    +start_date: date?
    +end_date: date?
  }

  class Placement {
    <<entity>>
    +placement_id: UUID «PK»
    +campaign_id: UUID «FK»
    +plan_item_id: UUID «FK»
    +ext_lineitem_id: string?
    +status: string
  }

  class Creative {
    <<entity>>
    +creative_id: UUID «PK»
    +name: string
    +spec_code: string «FK DictSpec»
    +status: string // draft/approved/rejected
  }

  class PlacementCreative {
    <<bridge>>
    +placement_id: UUID «FK»
    +creative_id: UUID «FK»
    +weight_pct: int?
    «PK(placement_id, creative_id)»
  }

  class Invoice {
    <<billing>>
    +invoice_id: UUID «PK»
    +deal_id: UUID «FK»
    +number: string
    +issue_date: date
    +due_date: date
    +currency: string
    +status: string // issued/paid/overdue
    +total: money
  }

  class InvoiceLine {
    <<detail>>
    +invoice_line_id: UUID «PK»
    +invoice_id: UUID «FK»
    +io_line_id: UUID «FK»
    +description: string
    +amount: money
    +tax_pct: decimal(5,2)?
  }

  class EventRaw {
    <<telemetry>>
    +event_raw_id: UUID «PK»
    +deal_id: UUID «FK»
    +source: string
    +type: string
    +payload: jsonb
    +occurred_at: datetime
    +ingested_at: datetime
    +correlation_id: string?
  }

  class EventCanon {
    <<telemetry>>
    +event_id: UUID «PK»
    +deal_id: UUID «FK»
    +event_type_code: string «FK DictEventType»
    +campaign_id: UUID «FK?»
    +placement_id: UUID «FK?»
    +ts: datetime
    +metrics_json: jsonb
  }

  class SLARecord {
    <<sla>>
    +sla_id: UUID «PK»
    +deal_id: UUID «FK»
    +stage_code: string
    +due_at: datetime
    +completed_at: datetime?
    +status: string // ontime/late/breached
  }

  class DictInventory {
    <<dict>>
    +inventory_code: string «PK»
    +vendor: string
    +channel: string
    +format: string
    +spec_code: string «FK DictSpec»
    +active: bool
  }

  class DictSpec {
    <<dict>>
    +spec_code: string «PK»
    +media_type: string
    +width: int?
    +height: int?
    +duration_sec: int?
    +max_weight_kb: int?
  }

  class DictEventType {
    <<dict>>
    +event_type_code: string «PK»
    +name: string
    +schema_ref: string
    +granularity: string // deal/campaign/placement
  }

  %% Relationships
  Account "1" o-- "0..*" Brand
  Account "1" o-- "0..*" MasterDeal
  Account "1" o-- "0..*" AccountContact
  Contact "1" o-- "0..*" AccountContact

  MasterDeal "1" o-- "0..*" DealStageHistory
  MasterDeal "1" o-- "0..*" Brief
  MasterDeal "1" o-- "0..*" MediaPlan
  MasterDeal "1" o-- "0..*" Proposal
  MasterDeal "1" o-- "0..*" Contract
  MasterDeal "1" o-- "0..*" IO
  MasterDeal "1" o-- "0..*" Campaign
  MasterDeal "1" o-- "0..*" Invoice
  MasterDeal "1" o-- "0..*" SLARecord
  MasterDeal "1" o-- "0..*" EventRaw
  MasterDeal "1" o-- "0..*" EventCanon

  Brand "1" o-- "0..*" MasterDeal

  MediaPlan "1" o-- "1..*" MediaPlanItem
  MediaPlanItem "0..*" o-- "0..*" PriceCard
  Proposal "1" o-- "1" MediaPlan

  Contract "1" o-- "0..*" Addendum

  IO "1" o-- "1..*" IOLineItem
  IOLineItem "0..*" o-- "1" MediaPlanItem

  Campaign "1" o-- "0..*" Placement
  Placement "0..*" o-- "0..*" Creative : PlacementCreative

  Invoice "1" o-- "1..*" InvoiceLine
  InvoiceLine "0..*" o-- "1" IOLineItem

  MediaPlanItem "0..*" o-- "1" DictInventory
  DictInventory "0..*" o-- "1" DictSpec
  Creative "0..*" o-- "1" DictSpec

  EventCanon "0..*" o-- "1" DictEventType
```

## Пояснения и принципы логической модели

* **Нормализация:** сущности разделены на агрегаты (MasterDeal), справочники (Dict*), документы (Contract/IO/Invoice), операционные детали (MediaPlanItem/IOLineItem) и телеметрию (EventRaw/EventCanon).
* **Версионирование:** `Brief.version`, `MediaPlan.version`, `Proposal.version`; истории стадий в `DealStageHistory`.
* **M:N связи:** через мосты `AccountContact`, `PlacementCreative`.
* **Справочники и валидация:** `DictInventory`, `DictSpec`, `DictEventType` задают допустимые значения и схемы событий.
* **Согласованность цепочки план-факт:** `MediaPlanItem → IOLineItem → InvoiceLine` обеспечивает трассируемость сумм и delivery.
* **SLA:** `SLARecord` и отметки в `DealStageHistory` позволяют считать TtL и нарушения.
* **Сырые/канонические события:** `EventRaw` хранит входящие payload’ы; `EventCanon` — нормализованные метрики, связанные с сущностями домена.