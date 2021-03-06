---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: b960b19f67dee802b9058896d5924f883d7463a9
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846063"
---
Die Einträge im Dokument `deploymentconfig.json` werden den Parametern für [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration) zugeordnet. In der folgenden Tabelle wird die Zuordnung zwischen den Entitäten im JSON-Dokument und den Parametern für die Methode beschrieben:

| JSON-Entität | Methodenparameter | Beschreibung |
| ----- | ----- | ----- |
| `computeType` | Nicht verfügbar | Das Computeziel. Für lokale Ziele ist der Wert `local` erforderlich. |
| `port` | `port` | Der lokale Port, auf dem der HTTP-Endpunkt des Diensts verfügbar gemacht werden soll. |

Dieser JSON-Code ist ein Beispiel für eine Bereitstellungskonfiguration für die Verwendung mit der CLI:


:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/deploymentconfig.json":::

Speichern Sie diese JSON-Datei unter dem Namen `deploymentconfig.json`.