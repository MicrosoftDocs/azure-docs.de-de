---
title: Ressourcenübergreifende Abfragen mit Azure Monitor | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Abfragen für Ressourcen aus mehreren Arbeitsbereichen und für Daten aus einer Application Insights-App in Ihrem Abonnement ausführen können.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 8eb163c95fb1426ebae8956d50f6d8f6aec6fd7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013648"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Ausführen arbeitsbereichs- und anwendungsübergreifender Protokollabfragen in Azure Monitor

Azure Monitor-Protokolle unterstützen Abfragen über mehrere Log Analytics-Arbeitsbereiche und Application Insights-Apps in der gleichen Ressourcengruppe, einer anderen Ressourcengruppe oder einem anderen Abonnement. Dies bietet Ihnen eine systemweite Ansicht Ihrer Daten.

Es gibt zwei Methoden zum Abfragen von Daten, die in mehreren Arbeitsbereichen und Apps gespeichert sind:
1. Explizit durch Angabe der Arbeitsbereichs- und App-Details. Dieses Verfahren wird in diesem Artikel ausführlich erläutert.
2. Implizit durch Verwendung von [Abfragen im Ressourcenkontext](../platform/design-logs-deployment.md#access-mode). Wenn Sie Abfragen im Kontext einer bestimmten Ressource, Ressourcengruppe oder eines Abonnements ausführen, werden die relevanten Daten aus allen Arbeitsbereichen abgerufen, die Daten für diese Ressourcen enthalten. Application Insights-Daten, die in Apps gespeichert werden, werden nicht abgerufen.

> [!IMPORTANT]
> Wenn Sie eine [arbeitsbereichsbasierte Application Insights-Ressource](../app/create-workspace-resource.md) verwenden, werden Telemetriedaten in einem Log Analytics-Arbeitsbereich mit allen anderen Protokolldaten gespeichert. Verwenden Sie den log()-Ausdruck, um eine Abfrage zu schreiben, die Anwendungen in mehreren Arbeitsbereichen umfasst. Für mehrere Anwendungen im gleichen Arbeitsbereich benötigen Sie keine arbeitsbereichübergreifende Abfrage.


## <a name="cross-resource-query-limits"></a>Ressourcenübergreifende Abfragelimits 

* Die Anzahl von Application Insights-Ressourcen und Log Analytics-Arbeitsbereichen, die Sie in eine einzelne Abfrage einschließen können, ist auf 100 beschränkt.
* Ressourcenübergreifende Abfrage wird im View Designer nicht unterstützt. Sie können eine Abfrage in Log Analytics erstellen und dem Azure-Dashboard anheften, um [eine Protokollabfrage zu visualisieren](../learn/tutorial-logs-dashboards.md). 
* Ressourcenübergreifende Abfragen in Protokollwarnungen werden nur in der aktuellen [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)-API unterstützt. Wenn Sie die Legacy-API für Log Analytics-Warnungen verwenden, müssen Sie [zur aktuellen API wechseln](../platform/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Abfragen über mehrere Log Analytics-Arbeitsbereiche und mit Application Insights
Um einen anderen Arbeitsbereich in der Abfrage anzugeben, verwenden Sie die [*Arbeitsbereichs*](./workspace-expression.md)-ID, und verwenden Sie für eine App in Application Insights die [*App*](./app-expression.md)-ID.  

### <a name="identifying-workspace-resources"></a>Identifizieren von Arbeitsbereichsressourcen
In den folgenden Beispielen werden Abfragen für Log Analytics-Arbeitsbereiche gezeigt, mit denen die zusammengefasste Anzahl von Protokollen aus der Tabelle „Update“ für einen Arbeitsbereich namens *contosoretail-it* zurückgegeben wird. 

Ein Arbeitsbereich kann auf mehrere Arten identifiziert werden:

* Ressourcenname: Ein für Benutzer lesbarer Name des Arbeitsbereichs (manchmal auch *Komponentenname* genannt). 

    `workspace("contosoretail-it").Update | count`

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

    >[!NOTE]
    >Bei der Identifizierung einer Anwendung über ihren Namen wird vorausgesetzt, dass er in allen zugänglichen Abonnements eindeutig ist. Sollten mehrere Anwendungen mit dem angegebenen Namen vorhanden sein, ist die Abfrage aufgrund der Mehrdeutigkeit nicht erfolgreich. In diesem Fall muss einer der anderen Bezeichner verwendet werden.

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



[Diese Funktion](./functions.md#use-a-function) können Sie nun wie folgt in einer ressourcenübergreifenden Abfrage verwenden. Der Funktionsalias _applicationsScoping_ gibt die Vereinigungsmenge der Anforderungstabelle aller definierten Anwendungen zurück. Die Abfrage filtert dann fehlgeschlagene Anforderungen heraus und visualisiert die Trends nach Anwendung sortiert. Der _parse_-Operator ist in diesem Beispiel optional. Er extrahiert den Namen der Anwendung aus der _SourceApp_-Eigenschaft.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Diese Methode kann bei Protokollwarnungen nicht verwendet werden, weil die Zugriffsüberprüfung der Warnungsregelressourcen, einschließlich Arbeitsbereichen und Anwendungen, zum Zeitpunkt der Warnungserstellung ausgeführt wird. Das Hinzufügen neuer Ressourcen zur Funktion nach dem Erstellen der Warnung wird nicht unterstützt. Wenn Sie eine Funktion zur Ressourcenbereichsdefinition in Protokollwarnungen verwenden möchten, müssen Sie die Warnungsregel im Portal bearbeiten oder eine Resource Manager-Vorlage verwenden, um die bereichsbezogenen Ressourcen zu aktualisieren. Alternativ dazu können Sie die Liste der Ressourcen in die Abfrage der Protokollwarnung einbeziehen.


![Zeitdiagramm](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Protokollabfragen und die Strukturierung von Azure Monitor-Protokolldaten erhalten Sie unter [Analysieren von Protokolldaten in Azure Monitor](log-query-overview.md).

