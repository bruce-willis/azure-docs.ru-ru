---
title: Экспериментирование в Azure Cognitive Services | Документация Майкрософт
description: Эта статья содержит руководство по экспериментированию в Пользовательской службе принятия решений Azure.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382393"
---
# <a name="experimentation"></a>Экспериментирование

Следуя теории [контекстных бандитов](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), Пользовательская служба принятия решений многократно изучает контекст, выполняет действие и изучает вознаграждение для выбранного действия. Примером может служить персонализация содержимого: контекст описывает пользователя, действиями являются предлагаемые истории, а вознаграждением измеряется одобрение пользователем рекомендуемой истории.

Пользовательская служба принятия решений создает политику, сопоставляя контекст с действиями. Для конкретной целевой политики необходимо знать ожидаемое вознаграждение. Один из способов оценить вознаграждение — создать политику в сети и позволить ей выбирать действия (например, рекомендуемые истории для пользователей). Однако такая оценка в сети может обойтись дорого по двум причинам:

* она предоставляет пользователям непроверенную, экспериментальную политику;
* она не масштабируется для оценки нескольких целевых политик.

Альтернативной парадигмой является оценка вне политики. Если у вас есть журналы из существующей веб-системы, которые соответствуют политике ведения журнала, оценка вне политики сможет прогнозировать ожидаемые вознаграждения новых целевых политик.

С помощью файла журнала служба "Экспериментирование" пытается найти политику с наивысшим предполагаемым вознаграждением. Целевые политики параметризуются с помощью аргументов [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki). В режиме по умолчанию сценарий проверяет различные аргументы Vowpal Wabbit, добавляя их к `--base_command`. Этот сценарий выполняет следующие действия:

* Автоматически обнаруживает пространства имен компонентов из первых строк `--auto_lines` входного файла.
* Выполняет первый проход по гиперпараметрам (`learning rate`, `L1 regularization` и `power_t`).
* Проверяет вычисление политики `--cb_type` (оценку обратной предрасположенности (`ips`) или двойственную надежность (`dr`). Дополнительные сведения можно получить, ознакомившись с [примером Contextual Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Выполняет граничную проверку.
* Проверяет функции квадратичного взаимодействия:
   * **этап подбора**: проверяются все сочетания с парами `--q_bruteforce_terms` (или меньше);
   * **этап "жадности"**: добавляются наиболее соответствующие пары, пока циклы `--q_greedy_stop` не перестают обеспечивать улучшение.
* Выполняет второй проход по гиперпараметрам (`learning rate`, `L1 regularization` и `power_t`).

Этими действиями управляют параметры с аргументами Vowpal Wabbit.
- Параметры манипуляции примерами:
  - общие пространства имен;
  - пространства имен действий;
  - граничные пространства имен;
  - квадратичные функции.
- Параметры обновления правил:
  - скорость обучения;
  - регуляризация L1;
  - значение степени t.

Более подробно приведенные выше аргументы рассмотрены в описании [аргументов командной строки Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>предварительным требованиям
- Vowpal Wabbit: установлен и указан в переменной path:
  - Windows: [используйте установщик (`.msi`)](https://github.com/eisber/vowpal_wabbit/releases);
  - другие платформы: [получите исходный код](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: установлен и указан в переменной path.
- NumPy: используйте диспетчер пакетов на свой выбор.
- Репозиторий *Microsoft/mwt-ds*: [клонируйте репозиторий](https://github.com/Microsoft/mwt-ds).
- Файл журнала службы принятия решений в формате JSON: по умолчанию базовая команда включает в себя параметр `--dsjson`, обеспечивающий анализ файла входных данных JSON в службе принятия решений. [Получите пример этого формата](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Использование
Перейдите к `mwt-ds/DataScience` и запустите `Experimentation.py` с соответствующими аргументами, как показано в следующем коде.

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Журнал результатов добавляется в файл *mwt-ds/DataScience/experiments.csv*.

### <a name="parameters"></a>Параметры
| Входные данные | ОПИСАНИЕ | значение по умолчанию |
| --- | --- | --- |
| `-h`, `--help` | Отображение справочного сообщения и выход. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Путь к файлу данных (в формате `.json` или `.json.gz`, каждая строка — `dsjson`). | Обязательно |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Базовая команда Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Количество используемых параллельных процессов. | Логические процессоры. |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Общие пространства имен компонентов (например, `abc` означает пространства имен `a`, `b` и `c`).  | Автоматическое определение из файла данных. |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Пространства имен компонентов действий. | Автоматическое определение из файла данных. |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Пространства имен граничных компонентов. | Автоматическое определение из файла данных. |  
| `--auto_lines AUTO_LINES` | Количество проверяемых строк в файле данных для автоматического обнаружение пространств имен компонентов. | `100` |  
| `--only_hp` | Проход только по гиперпараметрам (`learning rate`, `L1 regularization` и `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Диапазон положительных значений скорости обучения `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Диапазон положительных значений регуляризации L1 `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Диапазон положительных значений power_t `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Число квадратичных пар для тестирования на этапе подбора. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Число циклов без улучшения, после которых этап квадратичного "жадного" поиска прерывается. | `3` |  

### <a name="examples"></a>Примеры
Используйте текущие значения по умолчанию.
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Vowpal Wabbit может также принимать файлы с расширением `.json.gz`.
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Теперь выполните проход только по гиперпараметрам (`learning rate`, `L1 regularization` и `power_t`, остановка после шага 2).
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
