---
title: Ressourcenübergreifende Abfragen mit Azure Monitor | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Abfragen für Ressourcen aus mehreren Arbeitsbereichen und für Daten aus einer Application Insights-App in Ihrem Abonnement ausführen können.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: magoedte
ms.openlocfilehash: 51645f4f0c6dcc70d76ed1a20bc40f95db9d9717
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693361"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Ausführen ressourcenübergreifender Protokollabfragen in Azure Monitor  

Mit Azure Monitor konnten Sie bislang nur Daten innerhalb des aktuellen Arbeitsbereichs analysieren. Abfragen für mehrere in Ihrem Abonnement definierte Arbeitsbereiche waren nicht möglich.  Darüber hinaus konnten Sie Telemetrieelemente, die von Ihrer webbasierten Anwendung mit Application Insights gesammelt wurden, nur direkt in Application Insights oder über Visual Studio suchen. Dadurch wurde auch die gemeinsame native Analyse von Betriebs- und Anwendungsdaten zu einer Herausforderung.   

Sie können jetzt nicht nur Abfragen über mehrere Log Analytics-Arbeitsbereiche ausführen, sondern auch Daten aus einer bestimmten Application Insights-App in der gleichen Ressourcengruppe, einer anderen Ressourcengruppe oder einem anderen Abonnement in Abfragen einbeziehen. Dies bietet Ihnen eine systemweite Ansicht Ihrer Daten. Sie können diese Arten von Abfragen nur in [Log Analytics](portals.md) ausführen.

## <a name="cross-resource-query-limits"></a>Ressourcenübergreifende Abfragelimits 

* Die Anzahl von Application Insights-Ressourcen und Log Analytics-Arbeitsbereichen, die Sie in eine einzelne Abfrage einschließen können, ist auf 100 beschränkt.
* Ressourcenübergreifende Abfrage wird im View Designer nicht unterstützt. Sie können eine Abfrage in Log Analytics erstellen, dem Azure-Dashboard anheften und [eine Protokollsuche visualisieren](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Eine ressourcenübergreifende Abfrage in Protokollwarnungen wird in der neuen [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) unterstützt. Standardmäßig verwendet Azure Monitor die [Legacywarnungs-API von Log Analytics](../platform/api-alerts.md) zum Erstellen neuer Protokollwarnungsregeln über das Azure-Portal, es sei denn, Sie nehmen einen Umstieg von der [Legacyprotokollwarnungen-API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) vor. Nach dem Umstieg wird die neue API standardmäßig für neue Warnungsregeln im Azure-Portal verwendet, und Sie können Protokollwarnungsregeln für ressourcenübergreifende Abfragen erstellen. Sie können Protokollwarnungsregeln für ressourcenübergreifende Abfragen erstellen, ohne den Umstieg vorzunehmen, indem Sie die [ARM-Vorlage für die scheduledQueryRules-API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) verwenden. Diese Warnungsregel wird jedoch über die [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) und nicht über das Azure-Portal verwaltet.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Abfragen über mehrere Log Analytics-Arbeitsbereiche und mit Application Insights
Um einen anderen Arbeitsbereich in der Abfrage anzugeben, verwenden Sie die [*Arbeitsbereichs*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression)-ID, und verwenden Sie für eine App in Application Insights die [*App*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)-ID.  

### <a name="identifying-workspace-resources"></a>Identifizieren von Arbeitsbereichsressourcen
In den folgenden Beispielen werden Abfragen für Log Analytics-Arbeitsbereiche gezeigt, mit denen die zusammengefasste Anzahl von Protokollen aus der Tabelle „Update“ für einen Arbeitsbereich namens *contosoretail-it* zurückgegeben wird. 

Ein Arbeitsbereich kann auf mehrere Arten identifiziert werden:

* Ressourcenname: Ein für Benutzer lesbarer Name des Arbeitsbereichs (manchmal auch *Komponentenname* genannt). 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >Bei der Identifizierung eines Arbeitsbereichs über seinen Namen wird vorausgesetzt, dass er in allen zugänglichen Abonnements eindeutig ist. Sollten mehrere Anwendungen mit dem angegebenen Namen vorhanden sein, ist die Abfrage aufgrund der Mehrdeutigkeit nicht erfolgreich. In diesem Fall muss einer der anderen Bezeichner verwendet werden.

* Qualifizierter Name: Der vollständige Name des Arbeitsbereichs, bestehend aus Abonnementname, Ressourcengruppe und Komponentenname im folgenden Format: *Abonnementname/Ressourcengruppe/Komponentenname*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Da die Namen von Azure-Abonnements nicht eindeutig sind, ist dieser Bezeichner unter Umständen mehrdeutig. 
    >

* Arbeitsbereichs-ID: Bei einer Arbeitsbereichs-ID handelt es sich um den eindeutigen, unveränderlichen Bezeichner, der jedem Arbeitsbereich zugewiesen ist und als GUID (Globally Unique Identifier) dargestellt wird.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-Ressourcen-ID: Die von Azure definierte, eindeutige Identität des Arbeitsbereichs. Verwenden Sie die Ressourcen-ID, wenn der Ressourcenname nicht eindeutig ist.  Das Format für Arbeitsbereiche lautet wie folgt: */subscriptions/<Abonnement-ID>/resourcegroups/<Ressourcengruppe>/providers/Microsoft.OperationalInsights/workspaces/<Komponentenname>* .  

    Beispiel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifizieren einer Anwendung
In den folgenden Beispielen wird die zusammengefasste Anzahl der Anforderungen für eine App namens *fabrikamapp* in Application Insights zurückgegeben. 

Eine Anwendung in Application Insights kann mit dem Ausdruck *app(Identifier)* identifiziert werden.  Das *Identifier*-Argument gibt die App mit einem der folgenden Namen an:

* Ressourcenname: Ein für Benutzer lesbarer Name der App (manchmal auch *Komponentenname* genannt).  

    `app("fabrikamapp")`

* Qualifizierter Name: Der vollständige Name der App, bestehend aus Abonnementname, Ressourcengruppe und Komponentenname im folgenden Format: *Abonnementname/Ressourcengruppe/Komponentenname*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Da die Namen von Azure-Abonnements nicht eindeutig sind, ist dieser Bezeichner unter Umständen mehrdeutig. 
    >

* ID: Die App-GUID der Anwendung.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-Ressourcen-ID: Die von Azure definierte, eindeutige Identität der App. Verwenden Sie die Ressourcen-ID, wenn der Ressourcenname nicht eindeutig ist. Das Format ist: */subscriptions/<Abonnement-ID>/resourcegroups/<Ressourcengruppe>/providers/microsoft.OperationalInsights/components/<Komponentenname>* .  

    Beispiel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Durchführen einer mehrere Ressourcen übergreifenden Abfrage
Sie können mehrere Ressourcen aus beliebigen Ihrer Ressourceninstanzen abfragen. Dabei können Arbeitsbereiche und Apps kombiniert werden.
    
Beispiel für eine zwei Arbeitsbereiche übergreifende Abfrage:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Verwenden einer ressourcenübergreifenden Abfrage für mehrere Ressourcen
Wenn Sie mit ressourcenübergreifenden Abfragen Daten aus mehreren Log Analytics-Arbeitsbereichen und Application Insights-Ressourcen korrelieren, kann die Abfrage komplex und schwierig zu verwalten werden. Sie können die Abfragestruktur vereinfachen, indem Sie mithilfe der [Funktionen in Azure Monitor-Protokollabfragen](functions.md) die Abfragelogik von der Bereichsdefinition der Abfrageressourcen trennen. Das folgende Beispiel zeigt, wie Sie mehrere Application Insights-Ressourcen überwachen und die Anzahl fehlgeschlagener Anforderungen anhand des Anwendungsnamens visualisieren können. 

Erstellen Sie wie unten dargestellt eine Abfrage, die auf den Bereich der Application Insights-Ressourcen verweist. Der Befehl `withsource= SourceApp` fügt eine Spalte hinzu, die den Namen der Anwendung angibt, von der das Protokoll gesendet wurde. [Speichern Sie die Abfrage als Funktion](functions.md#create-a-function) mit dem Alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



[Diese Funktion](../../azure-monitor/log-query/functions.md#use-a-function) können Sie nun wie folgt in einer ressourcenübergreifenden Abfrage verwenden. Der Funktionsalias _applicationsScoping_ gibt die Vereinigungsmenge der Anforderungstabelle aller definierten Anwendungen zurück. Die Abfrage filtert dann fehlgeschlagene Anforderungen heraus und visualisiert die Trends nach Anwendung sortiert. Der _parse_-Operator ist in diesem Beispiel optional. Er extrahiert den Namen der Anwendung aus der _SourceApp_-Eigenschaft.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Zeitdiagramm](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Protokollabfragen und die Strukturierung von Azure Monitor-Protokolldaten erhalten Sie unter [Analysieren von Protokolldaten in Azure Monitor](log-query-overview.md).
- Informationen zum Anzeigen aller Ressourcen für Azure Monitor-Protokollabfragen finden Sie unter [Azure Monitor-Protokollabfragen](query-language.md).
