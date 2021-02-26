---
title: Azure Event Grid-SDKs
description: Beschreibt die SDKs für Azure Event Grid. Diese SDKs bieten Verwaltung, Veröffentlichung und Verwendung.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: c5b79f7cb7ae0112e2dacd077289786fc7b5c956
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516754"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid-SDKs für Verwaltung und Veröffentlichung

Event Grid bietet SDKs, mit denen Sie programmgesteuert Ihre Ressourcen verwalten und Ereignisse bereitstellen können.

## <a name="management-sdks"></a>Verwaltungs-SDKs

Die Verwaltungs-SDKs ermöglichen Ihnen das Erstellen, Aktualisieren und Löschen von Event Grid-Themen und -Abonnements. Derzeit sind folgende SDKs verfügbar:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs für Datenebenen

Mit den Datenebenen-SDKs können Sie Ereignisse durch Authentifizieren, Bilden des Ereignisses und asynchrones Bereitstellen am angegebenen Endpunkt Themen zur Verfügung stellen. Sie ermöglichen außerdem die Nutzung von Erstanbieterereignissen. Derzeit sind folgende SDKs verfügbar:

| Programmiersprache | SDK | 
| -------------------- | ---------- | ---------- | 
| .NET | Stabiles SDK: [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Vorschau-SDK: [Azure.Messaging.EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Stabiles SDK: [azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Vorschau-SDK: [azure-messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [azure-eventgrid-](https://pypi.org/project/azure-eventgrid/#history) (die neuesten stabilen und Vorabversionen finden Sie auf der Seite **Releaseverlauf**) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (wechseln Sie zur Registerkarte **Versionen**, um die neuesten stabilen und Betaversionenpakete anzuzeigen). | 
| Go | [Azure SDK für Go](https://github.com/Azure/azure-sdk-for-go) | | 
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) | | 


## <a name="next-steps"></a>Nächste Schritte

* Beispielanwendungen finden Sie unter [Codebeispiele für Event Grid](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)
* Eine Einführung zu Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Informationen zu Event Grid-Befehlen in der Azure-CLI finden Sie unter [Azure-CLI](/cli/azure/eventgrid).
* Informationen zu Event Grid-Befehlen in PowerShell finden Sie unter [PowerShell](/powershell/module/az.eventgrid).
