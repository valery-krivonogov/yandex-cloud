# Облачные сервисы баз данных от Yandex.Cloud 

Тестирование сервиса "Кластер ClickHouse"

## Создание кластера

Сервер создан с использованием мастера создания кластера ClickHouse - Managed Service for ClickHouse <br/>
* Кластер БД собран на единственном хосте (в целях оценки скорости работы)<br/>
* Выбран режим создания БД и пользователей с использованием SQL<br/>
* Включен режим использования WebSQL<br/>


![Clustrer](/img/cluster.jpg)

Создано подключение к базе данных для использования WebSQL<br/>

![WebSQL property](/img/connect_prop.jpg)


Состояние кластера отражается на закладке "Мониторинг"<br/>
![Monitor](/img/cluster_monitor.jpg)


Проверка работоспособности кластера на примере использования подключения из приложения на языке Python<br/>
![Проверка соединения](/img/test_connect_1.jpg)
![Проверка соединения](/img/test_connect_2.jpg)

```python
import requests

response = requests.get(
    'https://{0}:8443'.format('rc1d-0gbek7dopba47vo4.mdb.yandexcloud.net'),
    params={
        'query': 'SELECT version()',
    },
    headers={
        'X-ClickHouse-User': 'valery',
        'X-ClickHouse-Key': 'password',
    },
    verify='C:/Users/valer/.yandex/RootCA.crt'
)

print(response.text)
response.raise_for_status()

```
![Проверка соединения](/img/test_connect_4.jpg)


Настройка подключения из внешней сети с использованием инструмента DBeaver<br/>
![connect](/img/connect_set_1.jpg)

Обязательно указать использование SSL, корневой сертификат Yandex.Cloud<br/>
![connect](/img/connect_set_2.jpg)

Проверка соединения из DBeaver<br/>
![connect](/img/connect.jpg)

## Тестирование скорости выполнения запросов

Выполнена загрузка тестовых даннх в таблицу addr_data (около 5 млн записей)

Структура таблицы
```sql
CREATE TABLE default.addr_data
(
    subject_id Int32,
    adr_type Int32,
    country String,
    area String,
    date_since String,
    state String,
    zip String,
    regnum Int32,
    district String,
    city String,
    position String,
    street String,
    house String,
    block String,
    appartment String,
    fiasnumber String,
    locationokato String,
    estate String,
    regtype String,
    regauthority String,
    regauthoritycode String,
    issameasreg String,
    garnumber String
)
ENGINE = MergeTree
ORDER BY (subject_id, adr_type)
SETTINGS index_granularity = 8192;
```


Запрос количества записей в таблице и выборка первых 100 записей из таблицы<br/>
```sql
select COUNT (1) from addr_data
```

### Результаты тестов <br/>

Внешнее подключение<br/>
![Результаты тестов](/img/select_data.jpg)
![Результаты тестов](/img/select_data_result.jpg)

WebSQL<br/>
![Результаты тестов](/img/select_data_wsql.jpg)
![Результаты тестов](/img/select_data_wsql_result.jpg)
