# Подключение к PIM через API QRC.ai версия v1

# Общая информация

Существует 2 окружения API QRC:

- `tndev` окружение [https://qrcai.tndev.ru](https://qrcai.tndev.ru/) - c ним настраивается интеграция с вашим тестовым стендом
- `prod` окружение [https://qrc.ai](https://qrc.ai/) - рабочая версия, с которой получаются данные на ваше prod окружение

⚠️ Доступ к окружениям закрыт токеном.

⚠️ Ограничение по пропускной способности: 10 запросов раз в 5 сек.

# Получение токена

Для получения токена необходимо ответственному за ваш продукт менеджеру запросить токен по почте [pim@tn.ru](mailto:pim@tn.ru)

В запросе нужно указать:

- Название ресурса, который будет подключаться к API QRC
- Описание ресурса

<aside>
💡 Токен действует как на `tndev` так и на `prod` окружении

</aside>

# Получение информации в формате json из API QRC

## Swagger

```json
{
    "swagger": "2.0",
    "info": {
        "title": "Product API",
        "contact": {},
        "version": "0.0.1"
    },
    "host": "https://qrc.ai",
    "basePath": "/api",
    "paths": {
        "/v1/product/updated": {
            "post": {
                "security": [
                    {
                        "ApiKeyAuth": []
                    }
                ],
                "description": "ApiKeyAuth required (Authorization: Bearer \u003ctoken\u003e)",
                "produces": [
                    "application/json"
                ],
                "tags": [
                    "product info"
                ],
                "summary": "Updated product retrieve (time in RFC3339 or unix timestamp)",
                "parameters": [
                    {
                        "type": "string",
                        "description": "from",
                        "name": "from",
                        "in": "query",
                        "required": true
                    },
                    {
                        "type": "string",
                        "description": "to",
                        "name": "to",
                        "in": "query"
                    },
                    {
                        "description": "EKN List",
                        "name": "req",
                        "in": "body",
                        "required": true,
                        "schema": {
                            "type": "array",
                            "items": {
                                "type": "string"
                            }
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "OK",
                        "schema": {
                            "type": "array",
                            "items": {
                                "type": "string"
                            }
                        }
                    },
                    "404": {
                        "description": "Not Found",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    },
                    "500": {
                        "description": "Internal Server Error",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    }
                }
            }
        },
        "/v1/product/{ekn}": {
            "get": {
                "security": [
                    {
                        "ApiKeyAuth": []
                    }
                ],
                "description": "ApiKeyAuth required (Authorization: Bearer \u003ctoken\u003e)",
                "produces": [
                    "application/json"
                ],
                "tags": [
                    "product info"
                ],
                "summary": "Product retrieve",
                "parameters": [
                    {
                        "type": "string",
                        "description": "ekn",
                        "name": "ekn",
                        "in": "path",
                        "required": true
                    },
                    {
                        "type": "string",
                        "description": "локаль по умолчанию ru_RU",
                        "name": "locale",
                        "in": "query"
                    },
                    {
                        "type": "string",
                        "description": "список атрибутов которые необходимо вернуть разделенных запятой (по умолчанию все атрибуты)",
                        "name": "attributes",
                        "in": "query"
                    }
                ],
                "responses": {
                    "200": {
                        "description": "OK",
                        "schema": {
                            "$ref": "#/definitions/product.GetResponseV1"
                        }
                    },
                    "404": {
                        "description": "Not Found",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    },
                    "500": {
                        "description": "Internal Server Error",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "helpers.ErrorMsg": {
            "type": "object",
            "properties": {
                "message": {
                    "type": "string"
                }
            }
        },
        
        "product.Attribute": {
            "type": "object",
            "properties": {
                "attribute_type": {
                    "type": "string"
                },
                "data": {},
                "label": {
                    "type": "string"
                },
                "value": {}
            }
        },
        "product.Attributes": {
            "type": "object",
            "additionalProperties": {
                "$ref": "#/definitions/product.Attribute"
            }
        },
        
    },
    "securityDefinitions": {
        "ApiKeyAuth": {
            "type": "apiKey",
            "name": "Authorization.",
            "in": "header"
        }
    }
}
```

## Запрос для доступа к данным продукта по ЕКН

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`ЕКН`
```

### Пример запроса

Пример запроса для ЕКН 19:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19
```

### Структура данных в JSON

```json
{
  "id": "string",
  "groups": {
    "groupName": {
      "attribute": {
        "attribute_type": "string",
        "data": "string",
        "label": "string",
        "value": {
					"string"
        },
  
  },
  "locale": "string",
  "locales": [
    "string"
  ],
  "categories": [
    "string"
  ],
  "associations": [
    "string"
  ]
}
```

где:

`id`- номер sku (ЕКН)

`groups`- ветка групп данных sku

`groupName`- код группы атрибутов sku

`attribute`- код атрибута sku с данными. Легенду см. [Атрибуты и группы атрибутов карточек ЕКН и систем](https://www.notion.so/ed362e3da9d44c8799039984d8d72563?pvs=21) 

`attribute_type`- тип атрибута (текст, число, ссылка на справочник и прочие)

`data`- указываются опции атрибута:
 - если атрибут ссылается на справочники или медиа, то здесь указывается перечень кодов записей справочников и карточек медиа
- если атрибут метрический, то указывается единица измерения и значение а данной единице измерения

`label`- название атрибута на языке локали (например, для локали **ru**_RU язык русский - обозначается прописными символами ru)

`value`- значение атрибута

`locale`- название локали, в которой предоставлены данные в формате `код языка_код страны Альфа2` , например ru_RU

`locales`- список локалей, в рамках которых доступны данные по sku

`categories`- перечень кодов категорий из ПИМ, в которых находятся sku

`associations`- указывает связь sku между собой. Таким образом можно связывать sku систем и sku продуктов, а также привязывать sku комплектующих к основными sku. В атрибуте label указывается название ассоциации в формате: [Тип материала главный/альтернативный] - [Название слоя системы] [Номер слоя на изображении системы], например: "Главный - Пароизоляционный слой 6".

<aside>
💡 В случае, если атрибут ссылается на справочники или медиа, то в `value`указываются атрибуты справочников и карточке медиа, а также их значения.

</aside>

## Запрос для доступа к данным продукта по ЕКН с фильтром атрибутам

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`?attributes=`Атрибут_1`, `Атрибут_2`, …, `Атрибут_N`
```

### Пример запроса

Пример запроса для ЕКН 19 для атрибутов tds, atr_qual_manag_cert:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19?attributes=tds, atr_qual_manag_cert
```

## Запрос для доступа к данным продукта по ЕКН с фильтром по локали и атрибутам

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`?locale=`[Локаль](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`&attributes=`Атрибут_1`, `Атрибут_2`, …, `Атрибут_N`
```

### Пример запроса

Пример запроса для ЕКН 19 в локали ru_RU для атрибутов tds, atr_qual_manag_cert:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19?locale=ru_RU&attributes=tds, atr_qual_manag_cert
```

## Запрос для доступа к сертификатам о соответствии и документации продукта по ЕКН

<aside>
💡 Данный запрос возвращает такие документы как:

- Технические листы на системы tds_system
- Технические листы на системы (старые) astf_tds_sys_old
- Технические листы на марку (старые) tds
- Технические свидетельства (или отказные письма) req_tech_cert
- Технические одобрения tech_appr
- Технологические карты technological_map
- Страховые сертификаты insur_cert
- Стандарты организации (СТО) на проектирование и применение sto_design_usage
- Сертификаты о соответствии (или отказные письма) certificates  Сертификаты менеджмента качества astf_qual_manag_cert
- Свидетельства о государственной регистрации (СГР) (или отказные письма) stat_reg_cert
- Санитарно-гигиенические заключения (СЭЗ) (или отказные письма) san_hyg_concl
- Руководства по проектированию, монтажу и эксплуатации manual_design_installation_exploitation
Рекламные брошюры и каталоги promotional_material
- Протоколы испытаний сертификационные vol_cert_test_mond
- Пожарные сертификаты и декларации (или отказные письма) firecertificate
- Паспорта безопасности для химической продукции safety_ds_chem_prod
- Лист безопасности safety_data_sheet
- Информационные письма inform_letters
- Инструкции по монтажу installation_instructions
- Заключения на системы conclusions_on_systems
- Заключения на продукцию conclusions_on_products
- Другие документы other_docs
- Декларации о соответствии (или отказные письма) decl_conf_req
- Акустические сертификаты (или отказные письма) acoustic_cert
- Гарантийные сертификаты warranty
- Декларации характеристик dop
- Презентации маркетинговые presentations
- Технические листы на Марку tds_series
</aside>

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`/certificates
```

### Пример запроса

Пример запроса для ЕКН 19:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19/certificates
```

## Определение списка обновленных ЕКН

### Формат запроса

```jsx
curl https://qrcai.tndev.ru/api/v1/product/updated?from=`YYYY`-`MM`-`DD`T`HH`:`mm`:`ss`.`ffK`&to=`YYYY`-`MM`-`DD`T`HH`:`mm`:`ss`.`ffK`\
-H "Authorization: Bearer `Ваш_токен`"
-H 'Content-Type: application/json' \
--data-raw ' [
"`ЕКН`"
]
```

### Пример запроса

Пример запроса для ЕКН 19, 602201, 606878 с 10:00 01.03.2023 по 20:00 12.04.2023:

```jsx
curl https://qrcai.tndev.ru/api/v1/product/updated?from=2023-03-01T10:00:00Z&to=2023-04-12T20:00:00Z \
-H "Authorization: Bearer Токен"
-H 'Content-Type: application/json' \
--data-raw ' [
        "19",
        "602201",
        "606878"
]
```

# Архитектура данных PIM

Схема архитектуры PIM:
https://i.imgur.com/zOppFSB.png

## Атрибуты и группы атрибутов карточек ЕКН и систем

Таблица с перечнем атрибутов продуктов и систем в разрезе семейств:

[https://docs.google.com/spreadsheets/d/1KGepTSnfsn63dR7YDWIkwEm2Wg_zrquTOSo1jYOZKqA/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1KGepTSnfsn63dR7YDWIkwEm2Wg_zrquTOSo1jYOZKqA/edit?usp=sharing)

В таблице приведены данные:

- Коды атрибутов PIM (столбец A)
- Наименования атрибутов (столбец B)
- Типы атрибутов (столбец C)
- Группа атрибутов (столбец D)

<aside>
💡 Коды и описания групп атрибутов можно найти на вкладке `Группы атрибутов`

</aside>

- Описание атрибута (столбец G) - опционально
- Ссылка на справочники или карточки медиа (столбец K) - здесь можно найти атрибуты карточек медиа и атрибуты справочников
- Обязательность атрибута для семейств (типов продукции и систем):
    - 0 = атрибут не используется в семействе продукции;
    - 1 = атрибут есть в семействе, но не обязательный;
    - 2 = атрибут используется в семействе и является обязательным для заполнения.

## Атрибуты справочников

Атрибуты, которые ссылаются на справочники имеют в столбце К Опции атрибутов ссылку на таблицы с атрибутами конкретного справочника приведены в таблицах по ссылке:

[https://drive.google.com/drive/folders/1Os_LpvxUNXb5_KDzjfLtvPdiy-yZD-EL?usp=sharing](https://drive.google.com/drive/folders/1Os_LpvxUNXb5_KDzjfLtvPdiy-yZD-EL?usp=sharing)

## Атрибуты карточек медиа (ассетов)

Атрибуты, которые ссылаются на ассеты (хранилища медиаданных) имеют в столбце К Опции атрибутов ссылку на таблицы с атрибутами конкретного типа ассета приведены в таблицах по ссылке:

[https://drive.google.com/drive/folders/1nuI6CB-JaWttEZf4BTzw2rzytLTQfj64?usp=sharing](https://drive.google.com/drive/folders/1nuI6CB-JaWttEZf4BTzw2rzytLTQfj64?usp=sharing)
