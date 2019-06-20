---
title: Erstellen eines Event Hubs mithilfe der Azure CLI – Azure Event Hubs | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe der Azure CLI einen Event Hub erstellen und anschließend Ereignisse über Java senden und empfangen.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3bc7b409d590ac096b70431ae009fed36c968307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66148246"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Schnellstart: Erstellen eines Event Hubs mithilfe der Azure CLI

Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In dieser Schnellstartanleitung erstellen Sie einen Event Hub mit der Azure CLI.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen][] erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Azure CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um Ihre Version zu überprüfen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Die folgenden Schritte sind nicht erforderlich, wenn Sie Befehle in Cloud Shell ausführen. Bei lokaler Ausführung der CLI führen Sie die folgenden Schritte aus, um sich an Azure anzumelden und Ihr aktuelles Abonnement festzulegen:

Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

```azurecli-interactive
az login
```

Legen Sie den aktuellen Abonnementkontext fest. Ersetzen Sie `MyAzureSub` durch den Namen des Azure-Abonnements, das Sie verwenden möchten:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist eine logische Sammlung mit Azure-Ressourcen. Alle Ressourcen werden in einer Ressourcengruppe bereitgestellt und verwaltet. Führen Sie den folgenden Befehl aus, um eine Ressourcengruppe zu erstellen:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace
Ein Event Hubs-Namespace stellt einen eindeutigen Bereichscontainer bereit, auf den über den vollqualifizierten Domänennamen verwiesen wird und in dem Sie mindestens einen Event Hub erstellen. Führen Sie den folgenden Befehl aus, um einen Namespace in Ihrer Ressourcengruppe zu erstellen:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs
Führen Sie den folgenden Befehl aus, um einen Event Hub zu erstellen:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

Glückwunsch! Sie haben die Azure CLI verwendet, um einen Event Hubs-Namespace und einen darin enthaltenen Event Hub zu erstellen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Ressourcengruppe, einen Event Hubs-Namespace und einen Event Hub erstellt. Schritt-für-Schritt-Anleitungen zum Senden von Ereignissen an einen Event Hub oder Empfangen von Ereignissen von einem Event Hub finden Sie in den Tutorials zum **Senden und Empfangen von Ereignissen**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (nur senden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (nur empfangen)](event-hubs-storm-getstarted-receive.md)

[kostenloses Konto erstellen]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
