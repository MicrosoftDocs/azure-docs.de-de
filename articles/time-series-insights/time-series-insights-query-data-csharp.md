---
title: Abfragen von Daten aus einer Gen1-Umgebung mit C#-Code – Azure Time Series Insights Gen1 | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Daten mithilfe einer in C# geschriebenen benutzerdefinierten App aus einer Azure Time Series Insights Gen1-Umgebung abfragen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020043"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Abfragen von Daten aus der Azure Time Series Insights Gen1-Umgebung mithilfe von C#

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem C#-Beispiel wird gezeigt, wie mithilfe der [Gen1-Abfrage-APIs](/rest/api/time-series-insights/gen1-query) Daten aus Azure Time Series Insights Gen1-Umgebungen abgefragt werden.

> [!TIP]
> Gen1-Codebeispiele in C# finden Sie unter [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample).

## <a name="summary"></a>Zusammenfassung

Im unten stehenden Beispielcode werden die folgenden Funktionen veranschaulicht:

* Abrufen eines Zugriffstokens durch Azure Active Directory mithilfe von [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Übergeben dieses erhaltenen Zugriffstokens an den `Authorization`-Header nachfolgender Abfrage-API-Anforderungen.

* Im Beispiel wird jede der Gen1-Abfrage-APIs aufgerufen, um zu veranschaulichen, wie HTTP-Anforderungen gestellt werden an:
  * die [„Umgebungen abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environments-api), um die Umgebungen zurückzugeben, auf die der Benutzer Zugriff besitzt,
  * die [„Umgebungsverfügbarkeit abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api),
  * die [„Umgebungsmetadaten abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api), um Umgebungsmetadaten abzurufen
  * die [„Umgebungsereignisse abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api),
  * die [„Umgebungsaggregate abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api).

* Interagieren mit den Gen1-Abfrage-APIs mithilfe von WSS zum Senden von Nachrichten an:

  * die [„Gestreamte Umgebungsereignisse abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api),
  * die [„Gestreamte Umgebungsaggregate abrufen“-API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api),

## <a name="prerequisites-and-setup"></a>Voraussetzungen und Setup

Führen Sie vor dem Kompilieren und Ausführen des Beispielcodes die folgenden Schritte aus:

1. [Stellen Sie eine Azure Time Series Insights Gen1-Umgebung bereit](./time-series-insights-get-started.md).
1. Konfigurieren Sie Ihre Azure Time Series Insights-Umgebung für Azure Active Directory wie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md) beschrieben.
1. Installieren Sie die erforderlichen Projektabhängigkeiten.
1. Bearbeiten Sie den unten stehenden Beispielcode, indem Sie alle Vorkommen von **#DUMMY#** durch den entsprechenden Umgebungsbezeichner ersetzen.
1. Führen Sie den Code innerhalb von Visual Studio aus.

## <a name="project-dependencies"></a>Projektabhängigkeiten

Es wird empfohlen, die neueste Version von Visual Studio zu verwenden:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ab Version 16.4.2

Der Beispielcode weist zwei erforderliche Abhängigkeiten auf:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/): Paket 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json): Paket 9.0.1.

Laden Sie die Pakete in Visual Studio 2019 herunter, indem Sie die Optionen **Build** > **Projektmappe erstellen** auswählen.

Alternativ fügen Sie die Pakete mithilfe von [NuGet 2.12+](https://www.nuget.org/) hinzu:

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#-Beispielcode

Öffnen Sie das Repository [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)], um auf den C#-Beispielcode zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Abfragen finden Sie in der [Abfrage-API-Referenz](/rest/api/time-series-insights/gen1-query-api).

* Lesen Sie die Informationen, wie Sie [eine JavaScript-App mithilfe des Client-SDKs ](https://github.com/microsoft/tsiclient) mit Time Series Insights verbinden.
Azure-Samples/Azure-Time-Series-Insights/gen1-sample/csharp-tsi-gen1-sample/Program.cs