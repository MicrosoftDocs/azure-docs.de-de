---
title: Azure Application Insights | Microsoft-Dokumentation
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: cbb144cc8aac6dc8e90d196147b0c154471b7239
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60901479"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korrelieren von Application Insights-Daten mit benutzerdefinierten Datenquellen

Application Insights erfasst mehrere unterschiedliche Datentypen: Ausnahmen, Ablaufverfolgungen, Seitenzugriffe und andere. Häufig reicht es aus, die Leistung, Zuverlässigkeit und Nutzung Ihrer Anwendung zu untersuchen. Es gibt aber auch Fälle, in denen es hilfreich ist, die in Application Insights gespeicherten Daten mit anderen vollständig benutzerdefinierten Datasets zu korrelieren.

Einige Situationen, in denen Sie ggf. benutzerdefinierte Daten benötigen, sind:

- Datenanreicherung oder Nachschlagetabellen: Beispielsweise die Ergänzung eines Servernamens durch den Besitzer des Servers und den zugehörigen Labstandort 
- Korrelation mit anderen Datenquellen als Application Insights: Beispielsweise das Korrelieren von Daten zu einem Kauf in einem Web-Store mit Informationen Ihres Diensts zur Erfüllung des Kaufs, um zu bestimmen, wie genau Ihre Schätzungen in Bezug auf die Versandzeit waren 
- Vollständig benutzerdefinierte Daten: Viele Ihrer Kunden schätzen die Abfragesprache und Leistung der Azure Monitor-Protokollplattform, auf der Application Insights basiert, und möchten diese zum Abfragen von Daten verwenden, die sich nicht auf Application Insights beziehen. Ein Beispiel hierfür ist die Nachverfolgung der Solaranlage einer Smart Home-Installation, wie [hier]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/) beschrieben.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Korrelieren von benutzerdefinierten Daten mit Application Insights-Daten 

Da Application Insights auf der leistungsstarken Azure Monitor-Protokollplattform basiert, können wir für die Erfassung der Daten die gesamte Leistung von Azure Monitor nutzen. Anschließend schreiben wir Abfragen mit dem Operator „join“, mit dem diese benutzerdefinierten Daten mit den Daten korreliert werden, die in Azure Monitor-Protokollen verfügbar sind. 

## <a name="ingesting-data"></a>Erfassen von Daten

In diesem Abschnitt wird beschrieben, wie Ihre Daten in Azure Monitor-Protokolle gelangen.

Falls noch nicht geschehen, sollten Sie einen neuen Log Analytics-Arbeitsbereich bereitstellen, indem Sie [diese Anleitung](../learn/quick-collect-azurevm.md) befolgen und den Schritt zum „Erstellen eines Arbeitsbereichs“ ausführen.

Gehen Sie wie unten angegeben vor, um mit dem Senden von Daten an Azure Monitor zu beginnen. Sie haben mehrere Optionen:

- Zur Verwendung eines synchronen Mechanismus können Sie entweder die [Datensammler-API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) direkt aufrufen oder unseren Logic App-Connector verwenden. Suchen Sie einfach nach „Azure Log Analytics“, und wählen Sie die Option zum Senden von Daten:

  ![Screenshot: Auswahl und Aktion](./media/custom-data-correlation/01-logic-app-connector.png)  

- Wenn Sie eine asynchrone Option verwenden möchten, können Sie die Datensammler-API nutzen, um eine Verarbeitungspipeline zu erstellen. Nähere Einzelheiten finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api).

## <a name="correlating-data"></a>Korrelieren von Daten

Application Insights basiert auf der Azure Monitor-Protokollplattform. Daher können wir [ressourcenübergreifende Verknüpfungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) verwenden, um alle Daten, die wir über Azure Monitor erfasst haben, mit den Application Insights-Daten zu korrelieren.

Beispielsweise können wir unseren Labbestand und die Standorte in einer Tabelle mit dem Namen „LabLocations_CL“ in einem Log Analytics-Arbeitsbereich mit dem Namen „myLA“ erfassen. Wenn wir dann unsere Anforderungen prüfen möchten, die mit der Application Insights-App „myAI“ nachverfolgt wurden, und die Namen der Computer, über die die Anforderungen bereitgestellt wurden, mit den Standorten dieser Computer aus der oben erwähnten benutzerdefinierten Tabelle korrelieren möchten, können wir über den Application Insights- oder den Azure Monitor-Kontext die folgende Abfrage ausführen:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Referenz zur [Datensammler-API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Sehen Sie sich die weiteren Informationen zu [ressourcenübergreifenden Verknüpfungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) an.
