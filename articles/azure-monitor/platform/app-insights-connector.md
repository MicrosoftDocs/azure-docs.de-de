---
title: Anzeigen von Azure Application Insights-App-Daten | Microsoft-Dokumentation
description: Sie können die Application Insights-Connector-Lösung verwenden, um Leistungsprobleme zu diagnostizieren und zu verstehen, wofür Benutzer Ihre App verwenden, wenn die Überwachung mit Application Insights durchgeführt wird.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: e426962310417fdca56ea2f7d45a6ea820d41981
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335875"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-Connector-Verwaltungslösung (Veraltet)

![Application Insights-Symbol](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Durch die Unterstützung von [ressourcenübergreifenden Abfragen](../log-query/cross-workspace-query.md) ist die Application Insights-Connector-Verwaltungslösung nicht mehr erforderlich. Sie wurde eingestellt und aus dem Azure Marketplace entfernt. Gleichzeitig wurde das OMS-Portal entfernt, das für die kommerzielle Azure-Cloud am 15. Januar 2019 offiziell als veraltet eingestuft wurde. Für die Azure US Government-Cloud wird die Lösung am 30. März 2019 außer Betrieb gesetzt.
>
>Vorhandene Verbindungen sind weiterhin bis zum 30 Juni 2019 einsetzbar.  Aufgrund der Veraltung des OMS-Portals können vorhandene Verbindungen nicht mehr über das Portal konfiguriert oder entfernt werden. Ein Skript zur Verwendung von PowerShell für das Entfernen vorhandener Verbindungen finden Sie weiter unten im Abschnitt [Entfernen des Connectors mit PowerShell](#removing-the-connector-with-powershell).
>
>Eine Anleitung zum Abfragen von Application Insights-Protokolldaten für mehrere Anwendungen finden Sie unter [Vereinigen mehrerer Azure Monitor-Application Insights-Ressourcen](../log-query/unify-app-resource-data.md). Weitere Informationen zur Veraltung des OMS-Portals finden Sie unter [Wechsel des OMS-Portals zu Azure](./oms-portal-transition.md).
>
> 

Mit der Application Insights-Connector-Lösung können Sie Leistungsprobleme diagnostizieren und verstehen, wofür Benutzer Ihre App verwenden, wenn sie mit [Application Insights](../app/app-insights-overview.md) überwacht wird. Ansichten der gleichen Anwendungstelemetrie, die Entwickler in Application Insights sehen, sind in Log Analytics verfügbar. Durch Integration Ihrer Application Insights-Apps in Log Analytics erzielen Sie jedoch eine höhere Transparenz für Ihre Anwendungen, da sich die Vorgangs- und Anwendungsdaten an ein- und demselben Ort befinden. Sie können besser mit Ihren App-Entwicklern zusammenarbeiten, wenn jeweils die gleichen Ansichten genutzt werden. Die gemeinsamen Ansichten verringern den Zeitaufwand, der für das Erkennen und Lösen von Anwendungs- und Plattformproblemen anfällt.

Bei Verwendung der Lösung haben Sie folgende Möglichkeiten:

- Anzeigen aller Application Insights-Apps an einem Ort auch dann, wenn sie sich in unterschiedlichen Azure-Abonnements befinden
- Korrelieren von Infrastrukturdaten mit Anwendungsdaten
- Visualisieren von Anwendungsdaten mit Perspektiven bei der Protokollsuche
- Pivotieren von Log Analytics-Daten zu Ihrer Application Insights-App im Azure-Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Verbundene Quellen

Im Gegensatz zu den meisten anderen Log Analytics-Lösungen werden die Daten für den Application Insights-Connector nicht von Agents gesammelt. Alle von der Lösung verwendeten Daten stammen direkt von Azure.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| [Windows-Agents](./agent-windows.md) | Nein | Die Lösung erfasst keine Informationen von Windows-Agents. |
| [Linux-Agents](../learn/quick-collect-linux-computer.md) | Nein | Die Lösung erfasst keine Informationen von Linux-Agents. |
| [SCOM-Verwaltungsgruppe](./om-agents.md) | Nein | Die Lösung erfasst keine Informationen von Agents in einer verbundenen SCOM-Verwaltungsgruppe. |
| [Azure-Speicherkonto](./resource-logs.md#send-to-log-analytics-workspace) | Nein | Die Lösung sammelt keine Informationen von Azure Storage. |

## <a name="prerequisites"></a>Voraussetzungen

- Für den Zugriff auf die Informationen des Application Insights-Connectors müssen Sie über ein Azure-Abonnement verfügen.
- Sie müssen mindestens eine konfigurierte Application Insights-Ressource verwenden.
- Sie müssen der Besitzer oder Mitwirkende der Application Insights-Ressource sein.

## <a name="configuration"></a>Konfiguration

1. Aktivieren Sie die Lösung Azure-Web-Apps-Analyse in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) oder mit den unter [Hinzufügen von Log Analytics-Lösungen aus dem Lösungskatalog](../insights/solutions.md) beschriebenen Schritten.
2. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Wählen Sie **Alle Dienste** , um Application Insights zu öffnen. Suchen Sie dann nach „Application Insights“. 
3. Wählen Sie unter **Abonnements** ein Abonnement aus, das über Application Insights-Ressourcen verfügt, und wählen Sie dann unter **Name** mindestens eine Anwendung aus.
4. Klicken Sie auf **Speichern**.

Innerhalb von ca. 30 Minuten sind die Daten verfügbar, und die Application Insights-Kachel wird mit Daten aktualisiert. Dies ist in der folgenden Abbildung dargestellt:

![Application Insights-Kachel](./media/app-insights-connector/app-insights-tile.png)

Beachten Sie auch die folgenden Punkte:

- Sie können Application Insights-Apps nur mit einem einzigen Log Analytics-Arbeitsbereich verknüpfen.
- Sie können nur [Application Insights-Ressourcen vom Typ „Basic“ oder „Enterprise“](https://azure.microsoft.com/pricing/details/application-insights) mit Log Analytics verknüpfen. Sie können aber den Free-Tarif von Log Analytics nutzen.

## <a name="management-packs"></a>Management Packs

Bei dieser Lösung werden keine Management Packs in verbundenen Verwaltungsgruppen installiert.

## <a name="use-the-solution"></a>Verwenden der Lösung

In den folgenden Abschnitten wird beschrieben, wie Sie die Blätter im Application Insights-Dashboard nutzen können, um Daten Ihrer Apps anzuzeigen und damit zu interagieren.

### <a name="view-application-insights-connector-information"></a>Anzeigen von Application Insights-Connector-Informationen

Klicken Sie auf die Kachel **Application Insights** , um das **Application Insights** -Dashboard zu öffnen und die folgenden Blätter anzuzeigen.

![Screenshot des Application Insights-Dashboards mit den Blättern „Anwendungen“, „Datenvolumen“ und „Verfügbarkeit“](./media/app-insights-connector/app-insights-dash01.png)

![Screenshot des Application Insights-Dashboards mit den Blättern „Serveranforderungen“, „Fehler“ und „Ausnahmen“](./media/app-insights-connector/app-insights-dash02.png)

Das Dashboard enthält die in der Tabelle aufgeführten Blätter. Auf jedem Blatt sind bis zu 10 Einträge aufgeführt, die die Kriterien des Blatts für den angegebenen Bereich und Zeitraum erfüllen. Sie können eine Protokollsuche durchführen, mit der alle Einträge zurückgegeben werden, wenn Sie unten auf dem Blatt auf **Alle anzeigen** oder auf die Blattüberschrift klicken.


| **Spalte** | **Beschreibung** |
| --- | --- |
| Anwendungen – Anzahl von Anwendungen | Zeigt die Anzahl von Anwendungen in Anwendungsressourcen an. Außerdem werden die Anwendungsnamen und jeweils die Anzahl von Anwendungsdatensätzen aufgeführt. Klicken Sie auf die Zahl, um eine Protokollsuche nach <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> durchzuführen. <br><br>  Klicken Sie auf einen Anwendungsnamen, um eine Protokollsuche für die Anwendung auszuführen, bei der die Anwendungsdatensätze pro Host, Datensätze nach Telemetrietyp und alle Daten nach Typ (basierend auf dem letzten Tag) angezeigt werden. |
| Datenvolume – Vom Host gesendete Daten | Zeigt die Anzahl von Computerhosts an, die Daten senden. Außerdem werden die Computerhosts und für jeden Host die Datensatzanzahl aufgeführt. Klicken Sie auf die Zahl, um eine Protokollsuche nach <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> durchzuführen. <br><br> Klicken Sie auf einen Computernamen, um eine Protokollsuche für den Host durchzuführen, bei der die Anwendungsdatensätze pro Host, Datensätze nach Telemetrietyp und alle Daten nach Typ (basierend auf dem letzten Tag) angezeigt werden. |
| Verfügbarkeit – Webtest-Ergebnisse | Zeigt ein Ringdiagramm für Webtest-Ergebnisse an, um „Bestanden“ oder „Nicht bestanden“ anzugeben. Klicken Sie auf das Diagramm, um eine Protokollsuche nach <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> durchzuführen. <br><br> In den Ergebnissen ist für alle Tests die Anzahl von bestandenen und nicht bestandenen Prüfungen angegeben. Dabei werden alle Web-Apps mit Datenverkehr während der letzten Minute angezeigt. Klicken Sie auf einen Anwendungsnamen, um eine Protokollsuche mit Details zu nicht erfolgreichen Webtests anzuzeigen. |
| Serveranforderungen – Anforderungen pro Stunde | Zeigt ein Liniendiagramm mit den Serveranforderungen pro Stunde für verschiedene Anwendungen an. Zeigen Sie im Diagramm auf eine Linie, um die Top 3 der Anwendungen anzuzeigen, die für einen bestimmten Zeitpunkt Anforderungen erhalten. Außerdem wird eine Liste mit den Anwendungen angezeigt, die Anforderungen erhalten, sowie die Anzahl von Anforderungen für die ausgewählte Periode. <br><br>Klicken Sie auf den Graphen, um eine Protokollsuche nach <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> durchzuführen. Hiermit wird ein ausführlicheres Liniendiagramm zu den Serveranforderungen pro Stunde für verschiedene Anwendungen angezeigt. <br><br> Klicken Sie in der Liste auf eine Anwendung, um eine Protokollsuche nach <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> durchzuführen. Hiermit werden eine Liste mit Anforderungen, Diagramme für Anforderungen in Abhängigkeit der Zeit und Dauer und eine Liste mit Antwortcodes von Anforderungen angezeigt.   |
| Fehler – Fehlerhafte Anforderungen pro Stunde | Zeigt ein Liniendiagramm mit Informationen zu den fehlerhaften Anforderungen pro Stunde an. Zeigen Sie auf das Diagramm, um die Top 3 der Anwendungen mit fehlerhaften Anforderungen für einen bestimmten Zeitpunkt anzuzeigen. Außerdem wird eine Liste mit den Anwendungen und der jeweiligen Anzahl von fehlerhaften Anforderungen angezeigt. Klicken Sie auf das Diagramm, um eine Protokollsuche nach <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> durchzuführen. Hiermit wird ein ausführlicheres Liniendiagramm mit Informationen zu fehlerhaften Anwendungsanforderungen angezeigt. <br><br>Klicken Sie in der Liste auf einen Eintrag, um eine Protokollsuche nach <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> durchzuführen. Hiermit werden fehlerhafte Anforderungen, Diagramme für fehlerhafte Anforderungen in Abhängigkeit der Zeit und Dauer und eine Liste mit Antwortcodes von fehlerhaften Anforderungen angezeigt. |
| Ausnahmen – Ausnahmen pro Stunde | Zeigt ein Liniendiagramm zu den Ausnahmen pro Stunde an. Zeigen Sie auf das Diagramm, um die Top 3 der Anwendungen mit Ausnahmen für einen bestimmten Zeitpunkt anzuzeigen. Außerdem wird eine Liste mit den Anwendungen und der jeweiligen Anzahl von Ausnahmen angezeigt. Klicken Sie auf das Diagramm, um eine Protokollsuche nach <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> durchzuführen. Hiermit wird ein ausführlicheres Liniendiagramm mit Ausnahmen angezeigt. <br><br>Klicken Sie in der Liste auf einen Eintrag, um eine Protokollsuche nach <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> durchzuführen. Hiermit werden eine Liste mit Ausnahmen, Diagramme für Ausnahmen in Abhängigkeit der Zeit und für fehlerhafte Anforderungen und eine Liste mit Ausnahmetypen angezeigt.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Anzeigen der Application Insights-Perspektive per Protokollsuche

Wenn Sie im Dashboard auf einen Eintrag klicken, sehen Sie, dass in der Suche eine Application Insights-Perspektive angezeigt wird. Die Perspektive ermöglicht eine erweiterte Visualisierung basierend auf dem ausgewählten Telemetrietyp. Der Visualisierungsinhalt ändert sich also für verschiedene Telemetrietypen.

Wenn Sie im Blatt „Anwendungen“ auf eine beliebige Stelle klicken, wird die Standardperspektive **Anwendungen** angezeigt.

![Application Insights – Anwendungsperspektive](./media/app-insights-connector/applications-blade-drill-search.png)

Die Perspektive zeigt eine Übersicht für die Anwendung, die Sie ausgewählt haben.

Auf dem Blatt **Verfügbarkeit** wird eine andere Perspektive mit Webtestergebnissen und dazugehörigen fehlerhaften Anforderungen angezeigt.

![Application Insights – Verfügbarkeitsperspektive](./media/app-insights-connector/availability-blade-drill-search.png)

Wenn Sie auf dem Blatt **Serveranforderungen** oder **Fehler** auf eine beliebige Stelle klicken, ändern sich die Komponenten der Perspektive, um eine Visualisierung zu erhalten, die sich auf die Anforderungen bezieht.

![Application Insights – Blatt „Fehler“](./media/app-insights-connector/server-requests-failures-drill-search.png)

Wenn Sie auf dem Blatt **Ausnahmen** auf eine beliebige Stelle klicken, wird eine Visualisierung angezeigt, die für Ausnahmen maßgeschneidert ist.

![Application Insights – Blatt „Ausnahmen“](./media/app-insights-connector/exceptions-blade-drill-search.png)

Unabhängig davon, ob Sie im Dashboard **Application Insights-Connector** auf ein Element klicken, wird auf der Seite **Suche** selbst für alle Abfragen, bei denen Application Insights-Daten zurückgegeben werden, die Application Insights-Perspektive angezeigt. Wenn Sie beispielsweise Application Insights-Daten anzeigen, wird für eine **&#42;** -Abfrage auch die Registerkarte „Perspektive“ angezeigt. Dies ist in der folgenden Abbildung dargestellt:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Komponenten der Perspektive werden je nach Suchabfrage aktualisiert. Dies bedeutet, dass Sie die Ergebnisse mithilfe jedes Suchfelds filtern können, über das Sie die Daten anzeigen können:

- Ihre gesamten Anwendungen
- Eine einzelne ausgewählte Anwendung
- Eine Gruppe von Anwendungen

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivotieren zu einer App im Azure-Portal

Die Blätter des Application Insights-Connectors sind so konzipiert, dass Sie zur ausgewählten Application Insights-App pivotieren können, *wenn Sie das Azure-Portal verwenden*. Sie können die Lösung als allgemeine Überwachungsplattform zur Unterstützung der Problembehandlung einer App verwenden. Wenn Sie in einer Ihrer verbundenen Anwendungen ein potenzielles Problem erkennen, können Sie entweder in der Log Analytics-Suche einen Drilldown dafür durchführen oder direkt zur Application Insights-App pivotieren.

Klicken Sie zum Pivotieren auf die Auslassungspunkte ( **…** ) am Ende einer Zeile, und wählen Sie die Option **In Application Insights öffnen**.

>[!NOTE]
>Die Option **In Application Insights öffnen** ist im Azure-Portal nicht verfügbar.

![In Application Insights öffnen](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Datenkorrektur durch Sampling

Application Insights ermöglicht eine *[Korrektur durch Stichprobenentnahme](../app/sampling.md)* , um den Telemetriedatenverkehr zu reduzieren. Wenn Sie das Sampling in Ihrer Application Insights-App aktivieren, erhalten Sie eine verringerte Anzahl von Einträgen, die sowohl in Application Insights als auch in Log Analytics gespeichert werden. Die Konsistenz der Daten wird für die Seite **Application Insights-Connector** und die Perspektiven beibehalten, aber Sie sollten Datenstichproben für Ihre benutzerdefinierten Abfragen manuell korrigieren.

Hier ist ein Beispiel für die Samplingkorrektur in einer Abfrage der Protokollsuche angegeben:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Das Feld **Sampled Count** (Stichprobenanzahl) ist in allen Einträgen vorhanden und zeigt die Anzahl von Datenpunkten an, die zu dem Eintrag gehören. Wenn Sie das Sampling für Ihre Application Insights-App aktivieren, ist der Wert für **Sampled Count** (Stichprobenanzahl) größer als 1. Bilden Sie die Summe für die Felder **Sampled Count** (Stichprobenanzahl), um die tatsächliche Anzahl von Einträgen zu zählen, die von Ihrer Anwendung generiert werden.

Das Sampling wirkt sich nur auf die Gesamtzahl von Einträgen aus, die von Ihrer Anwendung generiert werden. Es ist nicht erforderlich, das Sampling für metrische Felder wie **RequestDuration** oder **AvailabilityDuration** zu korrigieren, da in diesen Feldern der Mittelwert für die jeweiligen Einträge angezeigt wird.

## <a name="input-data"></a>Eingabedaten

Die Lösung empfängt die folgenden Telemetrietypen der Daten von Ihren verbundenen Application Insights-Apps:

- Verfügbarkeit
- Ausnahmen
- Requests
- Seitenansichten: Damit Ihr Arbeitsbereich Seitenansichten erhält, müssen Sie für Ihre Apps das Sammeln dieser Informationen konfigurieren. Weitere Informationen finden Sie unter [PageViews](../app/api-custom-events-metrics.md#page-views).
- Benutzerdefinierte Ereignisse: Damit Ihr Arbeitsbereich benutzerdefinierte Ereignisse erhält, müssen Sie für Ihre Apps das Sammeln dieser Informationen konfigurieren. Weitere Informationen finden Sie unter [TrackEvent](../app/api-custom-events-metrics.md#trackevent).

Die Daten werden von Log Analytics über Application Insights empfangen, wenn sie verfügbar sind.

## <a name="output-data"></a>Ausgabedaten

Ein Datensatz mit dem *Typ* *ApplicationInsights* wird für jeden Eingabedatentyp erstellt. ApplicationInsights-Datensätze verfügen über Eigenschaften, die in den folgenden Abschnitten angezeigt werden:

### <a name="generic-fields"></a>Generische Felder

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Uhrzeit des Datensatzes |
| ApplicationId | Instrumentationsschlüssel der Application Insights-App |
| ApplicationName | Name der Application Insights-App |
| RoleInstance | ID des Serverhosts |
| DeviceType | Clientgerät |
| ScreenResolution |   |
| Continent | Kontinent, von dem die Anforderung stammt |
| Country | Land oder Region, aus dem bzw. der die Anforderung stammt |
| Province | Bundesland/Kanton/Bundesstaat bzw. Gebietsschema der Anforderung |
| City | Stadt oder Ort der Anforderung |
| isSynthetic | Gibt an, ob die Anforderung von einem Benutzer oder mit einer automatisierten Methode erstellt wurde. True = automatisierte Methode oder false = vom Benutzer generiert |
| SamplingRate | Prozentsatz der vom SDK generierten Telemetriedaten, die an das Portal gesendet werden. Bereich liegt zwischen 0.0 und 100.0. |
| SampledCount | 100/(SamplingRate). Beispiel: 4 =&gt; 25%. |
| IsAuthenticated | „true“ oder „false“ |
| OperationID | Elemente mit derselben Vorgangs-ID werden im Portal als verwandte Elemente angezeigt. Normalerweise die Anforderungs-ID. |
| ParentOperationID | ID des übergeordneten Vorgangs |
| Vorgangsname |   |
| SessionID | GUID zum eindeutigen Identifizieren der Sitzung, in der die Anforderung erstellt wurde |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Verfügbarkeitsfelder

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TelemetryType | Verfügbarkeit |
| AvailabilityTestName | Name des Webtests |
| AvailabilityRunLocation | Geografische Quelle der HTTP-Anforderung |
| AvailabilityResult | Gibt das Erfolgsergebnis des Webtests an |
| AvailabilityMessage | Nachricht, die an den Webtest angefügt ist |
| AvailabilityCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| DataSizeMetricValue | 1.0 oder 0.0 |
| DataSizeMetricCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| AvailabilityDuration | Zeit für die Webtestdauer in Millisekunden |
| AvailabilityDurationCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Eindeutige GUID für den Webtest |
| AvailabilityTimestamp | Genauer Zeitstempel des Verfügbarkeitstests |
| AvailabilityDurationMin | Für Samplingdatensätze zeigt dieses Feld die minimale Webtestdauer (Millisekunden) für die dargestellten Datenpunkte an |
| AvailabilityDurationMax | Für Samplingdatensätze zeigt dieses Feld die maximale Webtestdauer (Millisekunden) für die dargestellten Datenpunkte an |
| AvailabilityDurationStdDev | Für Samplingdatensätze zeigt dieses Feld die Standardabweichung zwischen allen Webtestdauern (Millisekunden) für die dargestellten Datenpunkte an |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Ausnahmenfelder

| type | ApplicationInsights |
| --- | --- |
| TelemetryType | Ausnahme |
| ExceptionType | Typ der Ausnahme |
| ExceptionMethod | Methode, mit der die Ausnahme erstellt wird |
| ExceptionAssembly | Die Assembly enthält das Framework und die Version sowie das Token mit dem öffentlichen Schlüssel |
| ExceptionGroup | Typ der Ausnahme |
| ExceptionHandledAt | Gibt die Ebene an, auf der die Ausnahme behandelt wurde |
| ExceptionCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| ExceptionMessage | Nachricht der Ausnahme |
| ExceptionStack | Vollständiger Stapel der Ausnahme |
| ExceptionHasStack | „true“, wenn die Ausnahme über einen Stapel verfügt |



### <a name="request-specific-fields"></a>Anforderungsfelder

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| type | ApplicationInsights |
| TelemetryType | Anforderung |
| ResponseCode | HTTP-Antwort, die an den Client gesendet wird. |
| RequestSuccess | Gibt den Erfolg oder Fehler an. „true“ oder „false“. |
| RequestID | ID zur eindeutigen Identifizierung der Anforderung |
| RequestName | GET/POST + URL-Basis |
| RequestDuration | Anforderungsdauer in Sekunden |
| URL | URL der Anforderung ohne Host |
| Host | Webserverhost |
| URLBase | Vollständige URL der Anforderung |
| ApplicationProtocol | Typ des von der Anwendung verwendeten Protokolls |
| RequestCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| RequestDurationCount | 100/(Samplingrate). Beispiel: 4 =&gt; 25%. |
| RequestDurationMin | Für Samplingdatensätze zeigt dieses Feld die minimale Anforderungsdauer (Millisekunden) für die dargestellten Datenpunkte an. |
| RequestDurationMax | Für Samplingdatensätze zeigt dieses Feld die maximale Anforderungsdauer (Millisekunden) für die dargestellten Datenpunkte an. |
| RequestDurationStdDev | Für Samplingdatensätze zeigt dieses Feld die Standardabweichung zwischen allen Anforderungsdauern (Millisekunden) für die dargestellten Datenpunkte an. |

## <a name="sample-log-searches"></a>Beispiele für Protokollsuchen

Diese Lösung verfügt nicht über einen Satz mit Beispielen für Protokollsuchen, die im Dashboard angezeigt werden. Im Abschnitt [Anzeigen von Application Insights-Connector-Informationen](#view-application-insights-connector-information) werden aber Beispielabfragen für die Protokollsuche angezeigt.

## <a name="removing-the-connector-with-powershell"></a>Entfernen des Connectors mit PowerShell
Aufgrund der Veraltung des OMS-Portals können vorhandene Verbindungen nicht mehr über das Portal konfiguriert oder entfernt werden. Mit dem folgenden PowerShell-Skript können Sie vorhandene Verbindungen entfernen. Sie müssen der Besitzer oder Mitwirkender des Arbeitsbereichs und Leser der Application Insights-Ressource sein, um diesen Vorgang ausführen zu können.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Mit dem folgenden PowerShell-Skript, das einen REST-API-Aufruf startet, können Sie eine Liste von Anwendungen abrufen. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Dieses Skript erfordert ein Bearertoken zur Authentifizierung für Azure Active Directory. Als eine Möglichkeit zum Abrufen dieses Tokens können Sie einen Artikel auf der [Dokumentationswebsite für die REST-API](/rest/api/loganalytics/datasources/createorupdate) verwenden. Klicken Sie auf **Jetzt testen** , und melden Sie sich bei Ihrem Azure-Abonnement an. Sie können das Bearertoken aus der **Anforderungsvorschau** kopieren, wie es in der folgenden Abbildung dargestellt ist.


![Bearertoken](media/app-insights-connector/bearer-token.png)


Sie können eine Liste von Anwendungen auch mit einer Protokollabfrage abrufen:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie die [Protokollsuche](../log-query/log-query-overview.md), um ausführliche Informationen zu Ihren Application Insights-Apps anzuzeigen.

