---
title: Сведения о последовательности развертывания в Azure Blueprint
description: Сведения о жизненном цикле схемы и подробные сведения о каждом этапе цикла.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955458"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Сведения о последовательности развертывания в Azure Blueprint

Azure Blueprint использует **порядок последовательности**, чтобы определить порядок создания ресурсов при обработке схемы. В этой статье приведены сведения о порядке последовательности, который используется по умолчанию, о том, как изменить порядок и обработать измененный порядок.

В примерах кода JSON есть переменные, значения которых необходимо заменить на ваши собственные значения:

- `{YourMG}` — замените это значение именем своей группы управления

## <a name="default-sequencing-order"></a>Порядок последовательности по умолчанию

Если схема не содержит директивы для порядка развертывания артефактов или директива имеет значение null, используется следующий порядок:

- Артефакты **назначения ролей** уровня подписки сортируются по имени артефакта
- Артефакты **назначения политик** уровня подписки сортируются по имени артефакта
- Артефакты **шаблонов Azure Resource Manager** уровня подписки сортируются по имени артефакта
- Артефакты **группы ресурсов** (включая дочерние артефакты) сортируются по имени заполнителя

Для каждого обрабатываемого артефакта **группы ресурсов** при создании артефактов в этой группе ресурсов используется следующий порядок последовательности:

- Дочерние артефакты **назначения ролей** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **назначения политик** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **шаблонов Azure Resource Manager** группы ресурсов сортируются по имени артефакта

## <a name="customizing-the-sequencing-order"></a>Изменение порядка последовательности

При создании крупных схем может потребоваться создавать ресурсы в определенном порядке по отношению к другим ресурсам. Наиболее распространенной из таких ситуаций является та, в которой схема включает несколько шаблонов Azure Resource Manager. Для решения этой задачи в Azure Blueprint можно определить порядок последовательности.

Для этого в JSON определяется свойство `dependsOn`. Это свойство поддерживается только схемой (для групп ресурсов) и объектами артефактов. `dependsOn` представляет собой массив строк имен артефактов, которые необходимо создать перед созданием указанного артефакта.

### <a name="example---blueprint-with-ordered-resource-group"></a>Пример — схема с упорядоченной группой ресурсов

Это пример схемы с группой ресурсов, в которой определен пользовательский порядок последовательности путем объявления значения `dependsOn` со стандартной группой ресурсов. В этом случае артефакт **assignPolicyTags** будет обрабатываться до группы ресурсов **ordered-rg**.
**standard-rg** будет обрабатываться в соответствии с порядком последовательности по умолчанию.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Пример: артефакт с пользовательским порядком последовательности

Это пример артефакта политики, который зависит от шаблона Azure Resource Manager. При использовании порядка сортировки по умолчанию артефакт политики был бы создан перед шаблоном Azure Resource Manager. Это позволяет артефакту политики подождать, пока будет создан шаблон Azure Resource Manager.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>Обработка пользовательской последовательности

В процессе создания используется топологическая сортировка для создания графа зависимостей для схемы и ее артефактов. Это гарантирует поддержку нескольких уровней зависимостей между группами ресурсов и артефактами.

Если зависимость объявлена для схемы или артефакта, которые не изменяют порядок последовательности по умолчанию, порядок последовательности не изменяется. К примерам таких случаев относятся группа ресурсов, которая зависит от политики уровня подписки, или дочернее назначение политики "standard-rg", которое зависит от дочернего назначения роли "standard-rg" группы ресурсов. В обоих случаях `dependsOn` не изменяет порядок последовательности по умолчанию, и изменения не производятся.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [жизненным циклом схемы](lifecycle.md)
- Узнайте, как использовать [статические и динамические параметры](parameters.md)
- Узнайте, как применять [блокировку ресурсов схемы](resource-locking.md)
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md)
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md)