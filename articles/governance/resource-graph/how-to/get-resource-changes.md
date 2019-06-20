---
title: Abrufen von Ressourcenänderungen
description: Erfahren Sie, wie Sie feststellen können, wann eine Ressource geändert wurde, und wie Sie eine Liste der geänderten Eigenschaften abrufen.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: b6ef57a3f39c82be30d92aef72c1bbe03b653768
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236508"
---
# <a name="get-resource-changes"></a>Abrufen von Ressourcenänderungen

Ressourcen werden während der alltäglichen Verwendung, Neukonfiguration und sogar der erneuten Bereitstellung geändert.
Änderungen können durch eine Person oder einen automatisierten Prozess herbeigeführt werden. Die meisten, jedoch nicht alle Änderungen sind beabsichtigt. Azure Resource Graph ermöglicht innerhalb der letzten 14 Tage des Änderungsverlaufs Folgendes:

- Ermitteln, wann Änderungen an einer Azure Resource Manager-Eigenschaft festgestellt wurden
- Anzeigen der bei dem Änderungsereignis geänderten Eigenschaften

Die Erkennung und die Details von Änderungen sind in folgenden Beispielszenarien nützlich:

- Bei der Verwaltung von Incidents zum Suchen _potenziell_ ähnlicher Änderungen. Sie können Änderungsereignisse in einem bestimmten Zeitfenster abfragen und die Änderungsdetails auswerten.
- Wenn eine Datenbank für die Konfigurationsverwaltung (Configuration Management Database, CMDB) auf dem neuesten Stand gehalten werden soll. Anstatt alle Ressourcen und die zugehörigen vollständigen Eigenschaftensätze in einem festgelegten Intervall zu aktualisieren, können Sie nur die Änderungen abrufen.
- Ermitteln, welche anderen Eigenschaften sich unter Umständen geändert haben, wenn der Konformitätszustand einer Ressource geändert wurde. Die Auswertung dieser zusätzlichen Eigenschaften kann Einblicke in andere Eigenschaften bieten, die möglicherweise über eine Azure Policy-Definition verwaltet werden müssen.

In diesem Artikel wird erläutert, wie diese Informationen über das Resource Graph-SDK erfasst werden. Beachten Sie für das Anzeigen dieser Informationen im Azure-Portal die Beschreibung unter [Änderungsverlauf](../../policy/how-to/determine-non-compliance.md#change-history-preview) von Azure Policy bzw. [Änderungsverlauf](../../../azure-monitor/platform/activity-log-view.md#azure-portal) des Azure-Aktivitätsprotokolls.

> [!NOTE]
> Die Änderungsdetails in Resource Graph beziehen sich auf Resource Manager-Eigenschaften. Informationen zur Nachverfolgung von Änderungen in einem virtuellen Computer finden Sie unter [Änderungsnachverfolgung](../../../automation/automation-change-tracking.md) für Azure Automation oder unter [Guest Configuration für virtuelle Computer](../../policy/concepts/guest-configuration.md) für Azure Policy.

> [!IMPORTANT]
> Der Änderungsverlauf in Azure Resource Graph befindet sich in der öffentlichen Vorschau.

## <a name="find-when-changes-were-detected"></a>Ermitteln, wann Änderungen festgestellt wurden

Der erste Schritt beim Ermitteln, was sich in einer Ressource geändert hat, besteht darin, die Änderungsereignisse in dieser Ressource innerhalb eines Zeitfensters zu suchen. Dieser Schritt erfolgt über den REST-Endpunkt **resourceChanges**.

Der Endpunkt **resourceChanges** erfordert zwei Parameter im Anforderungstext:

- **resourceId:** Die Azure-Ressource, in der nach Änderungen gesucht werden soll.
- **interval:** Eine Eigenschaft mit dem _Start-_ und _Enddatum_, innerhalb derer in der **Zulu-Zeitzone (Z)** nach einem Änderungsereignis gesucht werden soll.

Beispiel für Anforderungstext:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Mit dem Anforderungstext oben lautet der REST-API-URI für **resourceChanges** wie folgt:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Die Antwort ähnelt der im folgenden Beispiel:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Jedes erkannte Änderungsereignis für **resourceId** enthält eine **changeId**, die für die Ressource eindeutig ist. Die Zeichenfolge **changeId** kann auch andere Eigenschaften enthalten, jedoch ist nur ihre Eindeutigkeit garantiert. Der Änderungsdatensatz enthält die Zeiten vor und nach dem Erstellen von Momentaufnahmen.
Das Änderungsereignis ist zu einem bestimmten Zeitpunkt in diesem Zeitfenster eingetreten.

## <a name="see-what-properties-changed"></a>Anzeigen der geänderten Eigenschaften

Mit der **changeId** des Endpunkts **resourceChanges** werden dann anhand des REST-Endpunkts **resourceChangeDetails** Einzelheiten des Änderungsereignisses abgerufen.

Der Endpunkt **resourceChangeDetails** erfordert zwei Parameter im Anforderungstext:

- **resourceId:** Die Azure-Ressource, in der nach Änderungen gesucht werden soll.
- **changeId:** Das eindeutige Änderungsereignis für **resourceId**, das aus **resourceChanges** erfasst wird.

Beispiel für Anforderungstext:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Mit dem Anforderungstext oben lautet der REST-API-URI für **resourceChangeDetails** wie folgt:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Die Antwort ähnelt der im folgenden Beispiel:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** und **afterSnapshot** geben jeweils den Zeitpunkt, an dem die Momentaufnahme erstellt wurde, und die Eigenschaften zu diesem Zeitpunkt an. Die Änderung wurde zu einem Zeitpunkt zwischen diesen Momentaufnahmen vorgenommen. Im Beispiel oben können Sie sehen, dass es sich bei der geänderten Eigenschaft um **supportsHttpsTrafficOnly** handelt.

Wenn Sie die Ergebnisse programmgesteuert vergleichen möchten, vergleichen Sie den **content**-Teil jeder Momentaufnahme, um Unterschiede zu ermitteln. Wenn Sie die gesamte Momentaufnahme vergleichen, wird **timestamp** entgegen der Erwartung immer als Unterschied angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur verwendeten Sprache finden Sie unter [Einfache Abfragen](../samples/starter.md).
- Informationen zur anspruchsvolleren Nutzung finden Sie unter [Erweiterte Abfragen](../samples/advanced.md).
- Erfahren Sie, wie Sie [Ressourcen untersuchen](../concepts/explore-resources.md).