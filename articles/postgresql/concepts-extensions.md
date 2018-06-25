---
title: Использование расширений PostgreSQL в службе "База данных Azure для PostgreSQL"
description: Описывается, как расширить функциональные возможности базы данных с помощью расширений в базе данных Azure для PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/30/2018
ms.openlocfilehash: c1e541450103c0c30854c027c5dc39f3b94cdafd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640580"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Расширения PostgreSQL в базе данных Azure для PostgreSQL
PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения позволяют объединить несколько связанных объектов SQL в одном пакете, который затем можно загрузить или удалить из базы данных с помощью одной команды. После загрузки в базу данных расширения могут работать как встроенные функции. Дополнительные сведения о расширениях PostgreSQL см. на странице [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html) (Упаковка связанных объектов в расширение).

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL
Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, выполните команду [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html), используя инструмент psql. При этом упакованные объекты будут загружены в базу данных.

В настоящее время База данных Azure для PostgreSQL поддерживает подмножество основных расширений, которые перечислены ниже. Поддерживаются только указанные выше расширения. С помощью службы базы данных Azure для PostgreSQL невозможно создать собственное расширение.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Расширения, поддерживаемые базой данных Azure для PostgreSQL
В таблицах ниже приведен список стандартных расширений PostgreSQL, которые в настоящее время поддерживаются базой данных Azure для PostgreSQL. Эти сведения также можно получить, выполнив `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Расширения типов данных

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Предоставляет тип данных для автоматического шифрования паролей. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Предоставляет тип строки символов без учета регистра. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Предоставляет тип данных для многомерных кубов. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Предоставляет тип данных для хранения наборов пар "ключ — значение". |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Предоставляет типы данных для международных стандартов нумерации продуктов. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Предоставляет тип данных для иерархических древовидных структур. |

### <a name="functions-extensions"></a>Расширения функций

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Предоставляет средства для вычисления ортодромических расстояний на поверхности Земли. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Предоставляет несколько функций для определения сходства и расстояния между строками. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Предоставляет функции и операторы для управления массивами целых чисел, не содержащими значений null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Предоставляет функции шифрования. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Управляет секционированными таблицами по времени или идентификатору. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Предоставляет функции и операторы для определения сходства буквенно-цифрового текста на основе сопоставления триграмм. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Предоставляет функции для полного управления таблицами, включая перекрестные таблицы. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Создает глобально уникальные идентификаторы (UUID). |

### <a name="full-text-search-extensions"></a>Расширения для полнотекстового поиска

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Предоставляет шаблон словаря текстового поиска для целых чисел. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Словарь для текстового поиска, который удаляет из лексем знаки ударения (диакритические знаки). |

### <a name="index-types-extensions"></a>Расширения типов индекса

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Предоставляет примеры классов оператора GIN, которые реализуют поведение сбалансированного дерева для определенных типов данных. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Предоставляет классы оператора индекса GiST, которые реализуют сбалансированное дерево. |

### <a name="language-extensions"></a>Расширения языка

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Загружаемый процедурный язык PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Прочие расширения

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Предоставляет средства для анализа того, что происходит в общем буферном кэше в режиме реального времени. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Предоставляет способ загрузки реляционных данных в буферный кэш. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. (Примечание об этом расширении см. ниже.) |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Предоставляет средства для отображения сведений о блокировке на уровне строк. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Предоставляет средства для отображения статистических данных на уровне кортежей. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Оболочка для внешних данных, используемая для доступа к данным на внешних серверах PostgreSQL. |
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Предоставляет способ создания гипотетических индексов, которые используют ЦП или диск. |

### <a name="postgis-extensions"></a>Расширения PostGIS

> [!div class="mx-tableFixed"]
| **Расширение** | **Описание** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Пространственные и географические объекты для PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Используются для анализа адреса в составных элементы. Используется для поддержки шага нормализации геокодирования адресов. |
| [pgrouting](http://pgrouting.org/) | Расширяет геопространственную базу данных PostGIS / PostgreSQL для предоставления функции геопространственной маршрутизации. |


### <a name="using-pgstatstatements"></a>Использование pg_stat_statements
\_Расширение инструкций](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) [pg\_stat предварительно загружено на каждом сервере базы данных Azure для PostgreSQL, что предоставляет вам средства отслеживания статистики выполнения инструкций SQL.
Параметр `pg_stat_statements.track`, который управляет тем, какие инструкции учитываются расширением, по умолчанию имеет значение `top`. Это означает, что все инструкции, выпущенные непосредственно клиентами, отслеживаются. Два других уровня отслеживания: `none` и `all`. Этот параметр можно настроить в качестве параметра сервера с помощью [портала Azure](https://docs.microsoft.com/en-us/azure/postgresql/howto-configure-server-parameters-using-portal) или [Azure CLI](https://docs.microsoft.com/en-us/azure/postgresql/howto-configure-server-parameters-using-cli).

Существует компромисс между информацией о выполнении запроса, предоставляемой pg_stat_statements, и воздействием на производительность сервера при регистрации каждой инструкции SQL. Если расширение pg_stat_statements не используется активно, рекомендуется задать для параметра `pg_stat_statements.track` значение `none`. Обратите внимание, что некоторые сторонние службы мониторинга могут зависеть от pg_stat_statements при предоставлении информации о производительности запроса, поэтому подтвердите ваш ли это случай.


## <a name="next-steps"></a>Дополнительная информация
Если вы не видите расширение, которое вы хотите использовать, сообщите нам. Вы можете проголосовать за существующие запросы или оставить свои предложения на нашем [форуме отзывов клиентов](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
