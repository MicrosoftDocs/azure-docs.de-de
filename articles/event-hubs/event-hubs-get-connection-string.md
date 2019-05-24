---
title: Abrufen einer Verbindungszeichenfolge – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Anweisungen zum Abrufen einer Verbindungszeichenfolge, mit der Clients eine Verbindung mit Azure Event Hubs herstellen können.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158922"
---
# <a name="get-an-event-hubs-connection-string"></a>Abrufen einer Event Hubs-Verbindungszeichenfolge

Zur Verwendung von Event Hubs müssen Sie einen Event Hubs-Namespace erstellen. Ein Namespace ist ein Bereichscontainer für mehrere Event Hubs- oder Kafka-Themen. Mit dem Namespace erhalten Sie einen eindeutigen [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Nach dem Erstellen eines Namespace können Sie die Verbindungszeichenfolge abrufen, die für die Kommunikation mit Event Hubs erforderlich ist.

In der Verbindungszeichenfolge für Azure Event Hubs sind die folgenden Komponenten eingebettet:

* FQDN: der FQDN des erstellten EventHubs-Namespace (umfasst den EventHubs-Namespacenamen, gefolgt von „servicebus.windows.net“)
* SharedAccessKeyName: der Name, den Sie für die SAS-Schlüssel der Anwendung ausgewählt haben
* SharedAccessKey: der generierte Wert des Schlüssels

Die Vorlage der Verbindungszeichenfolge sieht folgendermaßen aus:
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Eine Verbindungszeichenfolge kann beispielsweise wie folgt aussehen: `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

In diesem Artikel werden verschiedene Möglichkeiten zum Abrufen der Verbindungszeichenfolge beschrieben.

## <a name="get-connection-string-from-the-portal"></a>Abrufen der Verbindungszeichenfolge im Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an. 
2. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus. 
3. Wählen Sie im Abschnitt **Analytics** die Option **Event Hubs** aus. 
4. Wählen Sie in der Liste der Event Hubs Ihren Event Hub aus.
6. Wählen Sie auf der Seite **Event Hubs-Namespace** im linken Menü die Option **Freigegebene Zugriffsrichtlinien** aus.

    ![Menüelement „Freigegebene Zugriffsrichtlinien“](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Wählen Sie in der Liste der Richtlinien eine **freigegebene Zugriffsrichtlinie** aus. Die Standardrichtlinie heißt: **RootManageSharedAccessPolicy**. Sie können eine Richtlinie mit entsprechenden Berechtigungen (Lesen, Schreiben) hinzufügen und diese Richtlinie verwenden. 

    ![Freigegebene Zugriffsrichtlinien für Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Wählen Sie neben dem Feld **Verbindungszeichenfolge – Primärschlüssel** die Schaltfläche **Kopieren** aus. 

    ![Event Hubs – Abrufen der Verbindungszeichenfolge](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Abrufen der Verbindungszeichenfolge mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mit dem Cmdlet [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) können Sie die Verbindungszeichenfolge für den angegebenen Richtlinien- oder Regelnamen wie unten gezeigt abrufen:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Abrufen der Verbindungszeichenfolge über die Azure-Befehlszeilenschnittstelle
Die Verbindungszeichenfolge für den Namespace können Sie wie folgt abrufen:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Weitere Informationen zu Azure-CLI-Befehlen für Event Hubs finden Sie unter [Azure-Befehlszeilenschnittstelle für Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
