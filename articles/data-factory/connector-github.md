---
title: Herstellen einer Verbindung mit GitHub
description: Verwenden von GitHub zum Angeben von Common Data Model-Entitätsverweisen
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372279"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Verwenden von GitHub zum Lesen von Common Data Model-Entitätsverweisen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Der GitHub-Connector in Azure Data Factory wird nur zum Empfangen des Entitätsverweisschemas für das [Common Data Model](format-common-data-model.md)-Format im Zuordnungsdatenfluss verwendet.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit GitHub verknüpften Dienst unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **GitHub** festgelegt werden. | ja
| userName | GitHub-Benutzername | ja |
| password | GitHub-Kennwort | ja |

## <a name="next-steps"></a>Nächste Schritte

Erstellen eines [Quelldatasets](data-flow-source.md) im Zuordnungsdatenfluss