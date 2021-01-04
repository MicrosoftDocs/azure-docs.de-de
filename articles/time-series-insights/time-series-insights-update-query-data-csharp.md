---
title: Abfragen von Daten aus einer Gen2-Umgebung mit C# – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Daten mithilfe einer in C# geschriebenen App aus einer Azure Time Series Insights Gen2-Umgebung abfragen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 81725a28102caf0d69a9fb303eaccdcf2151587d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020026"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Abfragen von Daten aus der Azure Time Series Insights Gen2-Umgebung mit C#

Dieses C#-Beispiel veranschaulicht, wie Daten von den [Gen2-APIs für den Datenzugriff](/rest/api/time-series-insights/reference-data-access-overview) in der Azure Time Series Insights Gen2-Umgebung abgefragt werden können.

> [!TIP]
> Gen2-Codebeispiele in C# finden Sie unter [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample).

## <a name="summary"></a>Zusammenfassung

Im unten stehenden Beispielcode werden die folgenden Funktionen veranschaulicht:

* Unterstützung der automatischen SDK-Generierung von [Azure AutoRest](https://github.com/Azure/AutoRest).
* Abrufen eines Zugriffstokens durch Azure Active Directory mithilfe von [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Übergeben dieses erhaltenen Zugriffstokens an den `Authorization`-Header nachfolgender Datenzugriffs-API-Anforderungen.
* Im Beispiel wird eine Konsolenschnittstelle bereitgestellt, die veranschaulicht, wie HTTP-Anforderungen an folgende Komponenten ausgegeben werden:
  * [Gen2-Umgebungen-API](/rest/api/time-series-insights/reference-environments-apis)
    * [„Umgebungsverfügbarkeit abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) und [„Ereignisschema abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2-Abfrage-API](/rest/api/time-series-insights/reference-query-apis)
    * [„Ereignisse abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [„Reihe abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) und [„Aggregierte Reihe abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Time Series-Modell-APIs](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [„Hierarchien abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) und [Hierarchienbatch-API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [„Typen abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) und [Typenbatch-API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [„Instanzen abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) und [Instanzenbatch-API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Erweiterte [Such](/rest/api/time-series-insights/reference-model-apis#search-features)- und [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax)-Funktionen.

## <a name="prerequisites-and-setup"></a>Voraussetzungen und Setup

Führen Sie vor dem Kompilieren und Ausführen des Beispielcodes die folgenden Schritte aus:

1. [Stellen Sie eine Gen2 Azure Time Series Insights-Umgebung bereit](./how-to-provision-manage.md#create-the-environment).
1. Konfigurieren Sie Ihre Azure Time Series Insights-Umgebung für Azure Active Directory wie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md) beschrieben.
1. Führen Sie die [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) wie in [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) angegeben aus, um die Azure Time Series Insights Gen2-Clientabhängigkeiten zu generieren.
1. Öffnen Sie die Projektmappe `TSIPreviewDataPlaneclient.sln`, und legen Sie `DataPlaneClientSampleApp` als Standardprojekt in Visual Studio fest.
1. Installieren Sie erforderlichen Projektabhängigkeiten mithilfe der [unten](#project-dependencies) beschriebenen Schritte, und kompilieren Sie das Beispiel in eine ausführbare `.exe`-Datei.
1. Führen Sie die `.exe`-Datei aus, indem Sie darauf doppelklicken.

## <a name="project-dependencies"></a>Projektabhängigkeiten

Es wird empfohlen, die neueste Version von Visual Studio zu verwenden:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ab Version 16.4.2

Der Beispielcode besitzt mehrere erforderliche Abhängigkeiten, die in der Datei [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) zu sehen sind.

Laden Sie die Pakete in Visual Studio 2019 herunter, indem Sie die Optionen **Build** > **Projektmappe erstellen** auswählen.

Alternativ fügen Sie jedes Paket mithilfe von [NuGet 2.12+](https://www.nuget.org/) hinzu. Beispiel:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#-Beispielcode

Informationen zum Zugreifen auf den C#-Beispielcode finden Sie im Repository [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample).

> [!NOTE]
>
> * Das Codebeispiel kann ausgeführt werden, ohne die Standardumgebungsvariablen zu ändern.
> * Das Codebeispiel wird in eine ausführbare .NET-Konsolen-App kompiliert.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Abfragen finden Sie in der [Abfrage-API-Referenz](/rest/api/time-series-insights/reference-query-apis).

* Lesen Sie die Informationen, wie Sie [eine JavaScript-App mithilfe des Client-SDKs ](https://github.com/microsoft/tsiclient) mit Azure Time Series Insights verbinden.