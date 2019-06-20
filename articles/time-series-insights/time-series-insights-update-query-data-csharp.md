---
title: Abfragen von Daten aus einer Azure Time Series Insights Preview-Umgebung mit C#-Code | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie durch Codieren einer in der .NET-Sprache C# (C-Sharp) geschriebenen benutzerdefinierten App Daten aus einer Azure Time Series Insights-Umgebung abgefragt werden.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: fc5f35aedd52e206433afb0f556bc1cde8296232
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237472"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Abfragen von Daten aus der Azure Time Series Insights Preview-Umgebung mit C#

Dieses C#-Beispiel veranschaulicht, wie Daten aus der Azure Time Series Insights Preview-Umgebung abgefragt werden können.

Das Beispiel zeigt einige einfache Beispiele für die Verwendung der Abfrage-API:

1. Rufen Sie zur Vorbereitung das Zugriffstoken mithilfe der Azure Active Directory-API ab. Übergeben Sie dieses Token im `Authorization`-Header jeder Abfrage-API-Anforderung. Informationen zum Einrichten nicht interaktiver Anwendungen finden Sie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md). Stellen Sie außerdem sicher, dass alle Konstanten, die zu Anfang des Beispiels definiert wurden, ordnungsgemäß festgelegt sind.
1. Die Liste der Umgebungen, auf die der Benutzer Zugriff hat, wird abgerufen. Eine der Umgebungen wird als relevant übernommen, und für diese Umgebung werden weitere Daten abgefragt.
1. Als Beispiel für eine HTTPS-Anforderung werden Verfügbarkeitsdaten für die relevante Umgebung abgefragt.
1. Als Beispiel für eine Websocketanforderung werden aggregierte Ereignisdaten für die relevante Umgebung abgefragt. Daten werden für den gesamten Verfügbarkeitszeitbereich abgefragt.

> [!NOTE]
> Dieser Beispielcode ist auch unter [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) verfügbar.

## <a name="c-example"></a>C#-Beispiel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Das obige Codebeispiel kann ausgeführt werden, ohne die Standardumgebungswerte zu ändern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Abfragen finden Sie in der [Abfrage-API-Referenz](/rest/api/time-series-insights/preview-query).

- Lesen Sie die Informationen zum [Verbinden einer JavaScript-Einzelseiten-App](tutorial-create-tsi-sample-spa.md) für Time Series Insights.