---
title: Abfragen von Daten aus einer Azure Time Series Insights GA-Umgebung mit C#-Code | Microsoft-Dokumentation
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
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 250dd691c3ef3146d6768123de52bf0628b10e42
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728963"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Abfragen von Daten aus der Azure Time Series Insights GA-Umgebung mit C#

In diesem C#-Beispiel wird gezeigt, wie Sie Daten aus der Azure Time Series Insights GA-Umgebung abfragen können.

Das Beispiel zeigt einige einfache Beispiele für die Verwendung der Abfrage-API:

1. Rufen Sie zur Vorbereitung das Zugriffstoken mithilfe der Azure Active Directory-API ab. Übergeben Sie dieses Token im `Authorization`-Header jeder Abfrage-API-Anforderung. Informationen zum Einrichten nicht interaktiver Anwendungen finden Sie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md). Stellen Sie außerdem sicher, dass alle Konstanten, die zu Anfang des Beispiels definiert wurden, ordnungsgemäß festgelegt sind.
1. Die Liste der Umgebungen, auf die der Benutzer Zugriff hat, wird abgerufen. Eine der Umgebungen wird als relevant übernommen, und für diese Umgebung werden weitere Daten abgefragt.
1. Als Beispiel für eine HTTPS-Anforderung werden Verfügbarkeitsdaten für die relevante Umgebung abgefragt.
1. Als Beispiel für eine Websocketanforderung werden aggregierte Ereignisdaten für die relevante Umgebung abgefragt. Daten werden für den gesamten Verfügbarkeitszeitbereich abgefragt.

> [!NOTE]
> Der Beispielcode ist verfügbar unter [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Projektabhängigkeiten

Fügen Sie die NuGet-Pakete `Microsoft.IdentityModel.Clients.ActiveDirectory` und `Newtonsoft.Json` hinzu.

## <a name="c-example"></a>C#-Beispiel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Abfragen finden Sie in der [Abfrage-API-Referenz](/rest/api/time-series-insights/ga-query-api).

- Lesen Sie die Informationen zum [Verbinden einer JavaScript-Einzelseiten-App](tutorial-create-tsi-sample-spa.md) für Time Series Insights.