---
title: Übersicht über die Azure Relay-API | Microsoft-Dokumentation
description: Übersicht über verfügbare Azure Relay-APIs
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593340"
---
# <a name="available-relay-apis"></a>Verfügbare Relay-APIs

## <a name="runtime-apis"></a>Laufzeit-APIs

In der folgenden Tabelle sind alle derzeit verfügbaren Relay-Laufzeitclients aufgeführt.

Der Abschnitt [Zusätzliche Informationen](#additional-information) enthält weitere Informationen zum Status der einzelnen Laufzeitbibliotheken.

| Sprache/Plattform | Verfügbares Feature | Clientpaket | Repository |
| --- | --- | --- | --- |
| .NET Standard | Hybridverbindungen | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-Relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | – |
| Knoten | Hybridverbindungen | [WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-Anforderungen: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Zusätzliche Informationen

#### <a name="net"></a>.NET

Das .NET-Ökosystem enthält mehrere Runtimes, daher stehen mehrere .NET-Bibliotheken für Relay zur Verfügung. Die .NET Standard-Bibliothek kann entweder mit .NET Core oder .NET Framework ausgeführt werden. Die .NET Framework-Bibliothek kann hingegen nur in einer .NET Framework-Umgebung ausgeführt werden. Weitere Informationen zu .NET Frameworks finden Sie unter [Framework-Versionen](/dotnet/articles/standard/frameworks).

Die .NET Framework-Bibliothek unterstützt nur das WCF-Programmiermodell und verwendet ein proprietäres binäres Protokoll, das auf dem WCF-Transport `net.tcp` basiert. Dieses Protokoll und die Bibliothek werden zur Abwärtskompatibilität mit vorhandenen Anwendungen weiterhin verwaltet.

Die .NET-Standardbibliothek basiert auf der offenen Protokolldefinition für das Hybrid Connections Relay, das wiederum auf HTTP und WebSockets aufbaut. Die Bibliothek unterstützt die Streamabstraktion über WebSockets und eine einfache Anforderungs-/Antwort-API-Geste für die Beantwortung von HTTP-Anforderungen. Das [Web-API](https://github.com/Azure/azure-relay-dotnet)-Beispiel zeigt, wie Hybrid Connections in ASP.NET Core für Webdienste integriert wird.

#### <a name="nodejs"></a>Node.js

Die in der obigen Tabelle aufgeführten Hybrid Connections-Module ersetzen oder ändern die bestehenden Node.js-Module durch alternative Implementierungen, die am Azure Relay-Dienst und nicht am lokalen Netzwerkstapel lauschen.

Das Modul `hyco-https` ändert und überschreibt teilweise die Node.js-Kernmodule `http` und `https`. Es stellt eine HTTPS-Listenerimplementierung bereit, die mit vielen vorhandenen Node.js-Modulen und -Anwendungen kompatibel ist, die diese Kernmodule verwenden.

Die Module `hyco-ws` und `hyco-websocket` ändern die verbreiteten Node.js-Module `ws` und `websocket`. Sie stellen alternative Listenerimplementierungen bereit, die Module und Anwendungen möglich machen, für die eines der Module hinter dem Hybrid Connections Relay arbeiten muss.

Ausführliche Informationen zu diesen Modulen finden Sie im GitHub-Repository [azure-relay-node](https://github.com/Azure/azure-relay-node).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Relay finden Sie unter diesen Links:
* [Was ist Azure Relay?](relay-what-is-it.md)
* [Relay – Häufig gestellte Fragen](relay-faq.md)
