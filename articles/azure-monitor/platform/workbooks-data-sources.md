---
title: Datenquellen für Azure Monitor-Arbeitsmappen | Microsoft-Dokumentation
description: Vereinfachen Sie die komplexe Berichterstellung mit vordefinierten und benutzerdefiniert parametrisierten Azure Monitor-Arbeitsmappen, die aus mehreren Datenquellen erstellt werden.
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: d41629dd9a56272af89a06cb55e9bd88b604baee
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927905"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Datenquellen für Azure Monitor-Arbeitsmappen

Arbeitsmappen sind mit einer großen Anzahl von Datenquellen kompatibel. In diesem Artikel werden die Datenquellen erläutert, die derzeit für Azure Monitor-Arbeitsmappen verfügbar sind.

## <a name="logs"></a>Protokolle

Arbeitsmappen ermöglichen das Abfragen von Protokollen aus folgenden Quellen:

* Azure Monitor-Protokolle (Application Insights-Ressourcen und Log Analytics-Arbeitsbereiche)
* Ressourcenbezogene Daten (Aktivitätsprotokolle)

Arbeitsmappenautoren können KQL-Abfragen verwenden, mit denen die zugrunde liegenden Ressourcendaten transformiert werden, um ein Resultset auszuwählen, das als Text, Diagramme oder Raster visuell dargestellt werden kann.

![Screenshot der Berichtsschnittstelle für Protokolle in der Arbeitsmappe](./media/workbooks-overview/logs.png)

Arbeitsmappenautoren können problemlos Abfragen für mehrere Ressourcen durchführen und so eine wirklich einheitliche und umfassende Berichtsoberfläche erstellen.

## <a name="metrics"></a>Metriken

Azure-Ressourcen geben [Metriken](data-platform-metrics.md) aus, auf die über Arbeitsmappen zugegriffen werden kann. Der Zugriff auf Metriken kann in Arbeitsmappen über ein spezielles Steuerelement erfolgen, das Ihnen die Angabe der Zielressourcen, der gewünschten Metriken und deren Aggregation ermöglicht. Diese Daten können dann in Diagrammen oder Rastern dargestellt werden.

![Screenshot des Metrikdiagramms für die CPU-Auslastung in der Arbeitsmappe](./media/workbooks-overview/metrics-graph.png)

![Screenshot der Schnittstelle für Metriken in der Arbeitsmappe](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Arbeitsmappen unterstützen das Abfragen von Ressourcen und deren Metadaten mithilfe von Azure Resource Graph (ARG). Diese Funktion wird hauptsächlich zum Erstellen benutzerdefinierter Abfragebereiche für Berichte verwendet. Der Ressourcenbereich wird über eine KQL-Teilmenge ausgedrückt, die von ARG unterstützt wird. Dies ist für gängige Anwendungsfälle oft ausreichend.

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste „Abfragetyp“ die Option „Azure Resource Graph“ aus, und wählen Sie dann die Zielabonnements aus. Verwenden Sie das Abfragesteuerelement, um die KQL-Teilmenge von ARG hinzuzufügen, mit der eine interessante Ressourcenteilmenge ausgewählt wird.

![Screenshot der KQL-Abfrage von Azure Resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

Die Arbeitsmappe unterstützt Azure Resource Manager-REST-Vorgänge. Dadurch kann der management.azure.com-Endpunkt abgefragt werden, ohne dass Sie das eigene Autorisierungsheader-Token bereitstellen müssen.

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste „Datenquelle“ die Option „Azure Resource Manager“ aus. Geben Sie die entsprechenden Parameter an, z. B. „Http method“, „url path“, „headers“, „url parameters“ und/oder „body“.

> [!NOTE]
> Nur die Vorgangstypen `GET`, `POST` und `HEAD` werden derzeit unterstützt.

## <a name="azure-data-explorer"></a>Azure-Daten-Explorer

Arbeitsmappen unterstützen jetzt das Abfragen von [Azure Data Explorer](/azure/data-explorer/)-Clustern mit der leistungsstarken [Kusto](/azure/kusto/query/index)-Abfragesprache.   

![Screenshot des Kusto-Abfragefensters](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>Workloadintegrität

Azure Monitor bietet Funktionen, mit denen die Verfügbarkeit und Leistung von Windows- oder Linux-Gastbetriebssystemen proaktiv überwacht werden. Azure Monitor modelliert wichtige Komponenten und deren Beziehungen, Kriterien für die Messung der Integrität dieser Komponenten und welche Komponenten eine Warnung ausgeben, sobald ein fehlerhafter Zustand erkannt wird. Arbeitsmappen ermöglichen es Benutzern, diese Informationen zum Erstellen umfassender interaktiver Berichte zu verwenden.

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste **Abfragetyp** die Option „Workloadintegrität“ aus, und wählen Sie dann das als Ziel zu verwendende Abonnement, die Ressourcengruppe oder die VM-Ressourcen aus. Verwenden Sie die Dropdownlisten für Integritätsfilter, um eine interessante Teilmenge von Integritätsvorfällen für Ihre Analyseanforderungen auszuwählen.

![Screenshot der Warnungsabfrage](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health

Arbeitsmappen unterstützen das Abrufen von Azure Resource Health und das Kombinieren mit anderen Datenquellen, um umfassende, interaktive Integritätsberichte zu erstellen.

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste **Abfragetyp** die Option „Azure Health“ aus, und wählen Sie dann die Zielressourcen aus. Verwenden Sie die Dropdownlisten für Integritätsfilter, um eine interessante Teilmenge von Ressourcenproblemen für Ihre Analyseanforderungen auszuwählen.

![Screenshot der Warnungsabfrage mit den Listen für Integritätsfilter](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

Der JSON-Anbieter ermöglicht Ihnen das Erstellen eines Abfrageergebnisses aus statischem JSON-Inhalt. Er wird am häufigsten in Parametern verwendet, um Dropdownparameter statischer Werte zu erstellen. Einfache JSON-Arrays oder -Objekte werden automatisch in Rasterzeilen und -spalten konvertiert.  Für spezifischere Verhalten können Sie die Registerkarte „Ergebnisse“ und JSONPath-Einstellungen verwenden, um Spalten zu konfigurieren.

## <a name="alerts-preview"></a>Warnungen (Vorschau)

> [!NOTE]
> Die empfohlene Vorgehensweise zum Abfragen von Azure-Warnungsinformationen ist die Verwendung der [Azure Resource Graph](#azure-resource-graph)-Datenquelle und Abfragen der Tabelle `AlertsManagementResources`.
>
> Beispiele finden Sie in der [Azure Resource Graph-Tabellenreferenz](../../governance/resource-graph/reference/supported-tables-resources.md) oder der [Warnungsvorlage](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook).
>
> Die Warnungsdatenquelle bleibt über einen bestimmten Zeitraum verfügbar, während Autoren zur Verwendung von ARG wechseln. Die Verwendung dieser Datenquelle in Vorlagen wird nicht empfohlen. 

Arbeitsmappen ermöglichen es Benutzern, die aktiven Warnungen in Bezug auf Ressourcen visuell darzustellen. Einschränkungen: Die Warnungsdatenquelle erfordert Lesezugriff auf das Abonnement, um Ressourcen abzufragen, und zeigt möglicherweise keine neueren Arten von Warnungen an. 

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste _Datenquelle_ die Option _Warnungen (Vorschau)_ aus, und wählen Sie dann die als Ziel zu verwendenden Abonnements, Ressourcengruppen und Ressourcen aus. Verwenden Sie die Dropdownlisten für Warnungsfilter, um eine interessante Teilmenge von Warnungen für Ihre Analyseanforderungen auszuwählen.

## <a name="custom-endpoint"></a>Benutzerdefinierter Endpunkt

Arbeitsmappen unterstützen das Abrufen von Daten aus einer beliebigen externen Quelle. Wenn sich Ihre Daten außerhalb von Azure befinden, können Sie sie mithilfe dieses Datenquellentyps in Arbeitsmappen einbinden.

Damit ein Abfragesteuerelement diese Datenquelle verwendet, wählen Sie über die Dropdownliste _Datenquelle_ die Option _Benutzerdefinierter Endpunkt_ aus. Geben Sie die entsprechenden Parameter an, z. B. `Http method`, `url`, `headers`, `url parameters` und/oder `body`. Stellen Sie sicher, dass Ihre Datenquelle [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) unterstützt, da andernfalls bei der Anforderung ein Fehler auftritt.

Um zu vermeiden, dass bei Verwendung von Vorlagen nicht vertrauenswürdige Hosts automatisch aufgerufen werden, muss der Benutzer die verwendeten Hosts als vertrauenswürdig kennzeichnen. Klicken Sie dazu auf die Schaltfläche _Als vertrauenswürdig hinzufügen_ , oder fügen Sie den Host als vertrauenswürdigen Host in den Arbeitsmappeneinstellungen hinzu. Diese Einstellungen werden in Browsern gespeichert, die IndexDb mit Web-Workern unterstützen. Weitere Informationen finden Sie [hier](https://caniuse.com/#feat=indexeddb).

> [!NOTE]
> Schreiben Sie keine geheimen Schlüssel in eines der Felder (`headers`, `parameters`, `body`, `url`), da diese für alle Arbeitsmappenbenutzer sichtbar sind.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr](./workbooks-overview.md#visualizations) über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
* [Steuern](workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.
* [Tipps zur Optimierung von Log Analytics-Abfragen](../log-query/query-optimization.md)