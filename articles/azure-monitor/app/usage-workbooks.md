---
title: Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen | Microsoft-Dokumentation
description: Vereinfachen der komplexen Berichterstellung mit vordefinierten und benutzerdefiniert parametrisierten Arbeitsmappen
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569603"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen

Arbeitsmappen kombinieren Text,  [Analyseabfragen](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure-Metriken und Parameter in umfassende interaktive Berichte. Arbeitsmappen können von anderen Teammitgliedern bearbeitet werden, die Zugriff auf dieselben Azure-Ressourcen haben.

Arbeitsmappen sind für folgende Szenarien hilfreich:

* Untersuchen der Nutzung Ihrer App, wenn Ihnen die jeweilige Metrik nicht bereits bekannt ist: Anzahl von Benutzern, Bindungsraten, Konvertierungsraten usw. Im Gegensatz zu anderen Tools für die Analyse der Nutzung können Sie mit Arbeitsmappen mehrere Arten von Visualisierungen und Analysen kombinieren, sodass Sie bei der Untersuchung deutlich weniger stark eingeschränkt sind.
* Erläutern, welche Leistung für ein neu veröffentlichtes Feature erzielt wird, für Ihr Team, indem Sie die Benutzeranzahl für wichtige Interaktionen und andere Metriken anzeigen.
* Freigeben der Ergebnisse eines A/B-Experiments in Ihrer App für andere Mitglieder Ihres Teams. Sie können die Ziele für das Experiment anhand von Text erklären und anschließend die einzelnen Nutzungsmetriken und Analytics-Abfragen anzeigen, die zum Auswerten des Experiments verwendet werden – und zusätzlich eindeutige Anmerkungen dazu, ob eine Metrik jeweils oberhalb oder unterhalb des Zielwerts gelegen hat.
* Melden der Auswirkungen eines Ausfalls auf die Nutzung Ihrer App, indem Daten, eine Erläuterung in Textform und eine Auflistung der nächsten Schritte verwendet wird, um Ausfälle in der Zukunft zu verhindern.

## <a name="starting-with-a-template-or-saved-workbook"></a>Ausgehend von einer Vorlage oder einer gespeicherten Arbeitsmappe

Eine Arbeitsmappe besteht aus Abschnitten, die wiederum aus unabhängig voneinander bearbeitbaren Diagrammen, Tabellen, Text und Eingabesteuerelementen bestehen. Zum besseren Verständnis von Arbeitsmappen empfiehlt es sich, eine zu öffnen. 

Wählen Sie in der Application Insights-Erfahrung für Ihre App im linken Menü **Arbeitsmappen** aus.

![Screenshot der Navigation zu Arbeitsmappen](./media/usage-workbooks/001-workbooks.png)

Dadurch wird ein Arbeitsmappenkatalog mit einer Reihe vordefinierter Arbeitsmappen gestartet, um Ihnen beim Einstieg zu helfen.

![Screenshot des Arbeitsmappenkatalogs](./media/usage-workbooks/002-workbook-gallery.png)

Wir beginnen mit der **Standardvorlage**, die sich unter der Überschrift **Schnellstart** befindet.

![Screenshot des Arbeitsmappenkatalogs](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Bearbeiten, Neuanordnen, Klonen und Löschen der Abschnitte von Arbeitsmappen

Arbeitsmappen verfügen über zwei Modi: **Bearbeitungsmodus** und **Lesemodus**. Wenn die Standardarbeitsmappe zum ersten Mal gestartet wird, wird sie im **Bearbeitungsmodus** geöffnet. Hierbei wird der gesamte Inhalte der Arbeitsmappe angezeigt, einschließlich aller Schritte und Parameter, die andernfalls ausgeblendet sind. Im **Lesemodus** wird eine vereinfachte, berichtartige Ansicht präsentiert. Dies ermöglicht es Ihnen, von der Komplexität zu abstrahieren, die mit der Erstellung des Berichts einhergegangen ist, und gleichzeitig die zugrunde liegenden Mechanismen mit nur wenigen Klicks zur Hand zu haben, wenn sie zur Vornahme von Änderungen benötigt werden.

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/editing-controls-new.png)

1. Klicken Sie in der unten linken Ecke des Abschnitts auf **Bearbeitung abgeschlossen**, wenn Sie mit der Bearbeitung fertig sind.

2. Klicken Sie zum Erstellen eines Duplikats eines Abschnitts auf das Symbol **Abschnitt klonen**. Die Erstellung von doppelten Abschnitten ist eine hervorragende Möglichkeit, eine Abfrage zu durchlaufen, ohne vorherige Iterationen zu verlieren.

3. Klicken Sie auf das Symbol für **Nach oben** bzw. **Nach unten**, um in einer Arbeitsmappe zum nächsten Abschnitt zu springen.

4. Klicken Sie zum dauerhaften Entfernen eines Abschnitts auf das Symbol **Entfernen**.

## <a name="adding-text-and-markdown-sections"></a>Hinzufügen von Text und Markdown-Abschnitten

Das Hinzufügen von Überschriften, Erklärungen und Kommentaren zu Ihren Arbeitsmappen ermöglicht es Ihnen, aus einer Gruppe von Tabellen und Diagrammen eine Geschichte zu formen. Für Textabschnitte in Arbeitsmappen wird die [Markdownsyntax](https://daringfireball.net/projects/markdown/) zum Formatieren von Text unterstützt, z.B. Überschriften, Fettdruck, Kursivdruck und Listen mit Aufzählungszeichen.

Verwenden Sie die Schaltfläche **Text hinzufügen** unten in der Arbeitsmappe bzw. unten in einem Abschnitt, um Ihrer Arbeitsmappe einen Textabschnitt hinzuzufügen.

## <a name="adding-query-sections"></a>Hinzufügen von Abfrageabschnitten

![Abfrageabschnitt in Arbeitsmappen](./media/usage-workbooks/analytics-section-new.png)

Verwenden Sie die Schaltfläche **Abfrage hinzufügen** unten in der Arbeitsmappe bzw. unten in einem Abschnitt, um Ihrer Arbeitsmappe einen Abfrageabschnitt hinzuzufügen.

Abfrageabschnitte sind sehr flexibel und können verwendet werden, um Fragen zu beantworten wie:

* Wie viele Ausnahmen wurden von Ihrer Website während des Zeitraums ausgelöst, in dem sich die Nutzung verringert hat?
* Wie waren die Seiteladezeiten für Benutzer verteilt, die eine Seite angezeigt haben?
* Wie viele Benutzer haben eine Gruppe von Seiten auf Ihrer Website angezeigt, eine andere Gruppe von Seiten aber nicht? Dies kann das Verständnis fördern, ob Sie über Cluster mit Benutzern verfügen, die unterschiedliche Teilmengen der Funktionalität Ihrer Website nutzen. (Verwenden Sie den Operator `join` mit dem Modifizierer `kind=leftanti` in der [Abfragesprache Kusto](/azure/kusto/query/).)

Außerdem sind Sie nicht nur auf Abfragen aus dem Kontext der Anwendung, aus der Sie die Arbeitsmappe gestartet haben, beschränkt. Sie können über mehrere von Application Insights überwachte Apps hinweg sowie auch Log Analytics-Arbeitsbereiche abfragen, solange Sie Zugriffsberechtigungen für diese Ressourcen besitzen.

Um Abfragen an zusätzliche externe Application Insights-Ressourcen zu richten, verwenden Sie den Bezeichner **app**.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Diese Abfrage kombiniert Anforderungen von drei verschiedenen Anwendungen. Eine App namens „app01“, eine App namens „app02“ und die Anforderungen von der lokalen Application Insights-Ressource.

Um Daten aus einem externen Log Analytics-Arbeitsbereich einzulesen, verwenden Sie den Bezeichner **workspace**.

Weitere Informationen über ressourcenübergreifende Abfragen finden Sie in der [offiziellen Anleitung](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Erweiterte Analyseabfrageeinstellungen

Jeder Abschnitt verfügt über eigene, erweiterte Einstellungen, auf die über das Symbol „Einstellungen“ ![Bearbeitungssteuerelemente für Application Insights-Arbeitsmappenabschnitte](./media/usage-workbooks/005-settings.png), das sich rechts neben der Schaltfläche **Parameter hinzufügen** befindet, zugegriffen werden kann.

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Benutzerdefinierte Breite**    | Legen Sie diese Option fest, um einem Element eine beliebige Größe zu geben, damit Sie viele Elemente in einer einzelnen Zeile unterbringen können, was Ihnen eine bessere Organisation Ihrer Diagramme und Tabellen zu umfassenden interaktiven Berichten gestattet.  |
   | **Bedingt sichtbar** | Hiermit können Sie im Lesemodus Schritte ausblenden, basierend auf einem Parameter. |
   | **Parameter exportieren**| Hiermit kann eine ausgewählte Zeile im Raster oder Diagramm in einem späteren Schritt Werte ändern oder die Zeile anzeigen.  |
   | **Abfrage anzeigen, wenn keine Bearbeitung erfolgt** | Dies zeigt die Abfrage oberhalb des Diagramms oder der Tabelle an, auch wenn der Lesemodus aktiv ist.
   | **Schaltfläche „In Analyse öffnen“ anzeigen, wenn gerade keine Bearbeitung erfolgt** | Hierdurch wird in der rechten Ecke des Diagramms das blaue „Analytics“-Symbol hinzugefügt, um mit einem Klick Zugriff zu ermöglichen.|

Die meisten dieser Einstellungen sind recht intuitiv, aber um **Parameter exportieren** zu verstehen, ist es besser, eine Arbeitsmappe zu untersuchen, diese Funktionalität verwendet.

Eine der vordefinierten Arbeitsmappen bietet Informationen zu aktiven Benutzern.

Der erste Abschnitt der Arbeitsmappe basiert auf Analyseabfragedaten:

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/003-active-users.png)

Der zweite Abschnitt basiert ebenfalls auf Analyseabfragedaten, aber durch das Auswählen einer Zeile in der ersten Tabelle wird der Inhalt des Diagramms interaktiv aktualisiert:

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/004-active-users-trend.png)

 Dies ist möglich, weil erweiterte Einstellungen von **Bei Auswahl eines Elements Parameter exportieren** verwendet werden, die in der Analyseabfrage der Tabelle aktiviert sind.

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/007-settings-export.png)

Die zweite Analyseabfrage verwendet dann die exportierten Werte, wenn eine Zeile ausgewählt wird. Wenn keine Zeile ausgewählt ist, wird standardmäßig die Zeile verwendet, die die Gesamtwerte darstellt. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Hinzufügen von Metrikabschnitten

Metrikabschnitte bieten Ihnen vollständigen Zugriff, um Metrikdaten von Azure Monitor in Ihre interaktiven Berichte zu integrieren. Viele der vordefinierten Arbeitsmappen enthalten sowohl Analyseabfragedaten als auch Metrikdaten, die es Ihnen gestatten, das beste aus beiden Funktionen an einem Ort zu nutzen. Sie können außerdem Metrikdaten aus Ressourcen in jedem der Abonnements einlesen, auf die Sie Zugriff haben.

Hier ist ein Beispiel für Daten eines virtuellen Computers, die in eine Arbeitsmappe eingelesen werden, um eine Rastervisualisierung der CPU-Leistung bereitzustellen:

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Hinzufügen von Parameterabschnitten

Arbeitsmappenparameter erlauben Ihnen das Ändern von Werten in der Arbeitsmappe, ohne die Abfrage- oder Textabschnitte manuell bearbeiten zu müssen.  Hierdurch entfällt die Anforderung, die zugrunde liegende Analyseabfragesprache verstehen zu müssen, was die potenzielle Zielgruppe für arbeitsmappenbasierte Berichterstellung erheblich erweitert.

Die Werte der Parameter werden in Abfrage-, Text- oder anderen Parameterabschnitten eingesetzt, indem der Name des Parameters in geschweifte Klammern gesetzt wird, z. B. ``{parameterName}``.  Parameternamen unterliegen ähnlichen Regeln wie JavaScript-Bezeichner, also im Grunde Buchstaben oder Unterstriche, gefolgt von alphanumerischen Zeichen oder Unterstrichen. Beispielsweise ist **a1** zulässig, während **1a** nicht zulässig ist.

Parameter sind linear, beginnen am Anfang einer Arbeitsmappe und fließen nach unten zu den jeweils folgenden Schritten.  Später in der Arbeitsmappe deklarierte Parameter können solche, die weiter oben deklariert wurden, außer Kraft setzen.  Dadurch können auch Parameter, die Abfragen verwenden, um auf die Werte von weiter oben definierten Parametern zuzugreifen.  Innerhalb des Schritts eines Parameters sind Parameter ebenfalls linear, von links nach rechts, wobei rechts liegende Parameter von einem früher im selben Schritt deklarierten Parameter abhängig sein können.
 
Es gibt vier verschiedene Typen von Parametern, die zurzeit unterstützt werden:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | Der Benutzer bearbeitet ein Textfeld, und Sie können optional eine Abfrage bereitstellen, um den Standardwert einzutragen. |
   | **Dropdown** | Der Benutzer wählt aus einer Gruppe von Werten aus. |
   | **Zeitbereichsauswahl**| Der Benutzer wählt aus einem vordefinierten Satz von Zeitbereichswerten aus, oder er wählt aus einem benutzerdefinierten Zeitbereich aus.|
   | **Ressourcenauswahl** | Der Benutzer wählt aus den für die Arbeitsmappe ausgewählten Ressourcen aus.|

### <a name="using-a-text-parameter"></a>Verwenden eines Textparameters

Der Wert, den ein Benutzer in das Textfeld eingibt, wird direkt in der Abfrage ersetzt, ohne Escape- oder Anführungszeichen. Wenn der benötigte Wert eine Zeichenfolge ist, sollte die Abfrage den Parameter in Anführungszeichen setzen (z. B. **'{parameter}'** ).

Dadurch kann der Wert in einem Textfeld überall verwendet werden. Er kann ein Tabellenname, Spaltenname, Funktionsname, Operator usw. sein.

Der Parametertyp „Text“ verfügt über eine Einstellung **Standardwert aus Analyseabfrage abrufen**, die es dem Autor der Arbeitsmappe gestattet, eine Abfrage zu verwenden, um den Standardwert für das Textfeld aufzufüllen.

Wenn Sie den Standardwert aus einer Analyseabfrage verwenden, wird nur der erste Wert der ersten Zeile (Zeile 0, Spalte 0) als Standardwert verwendet. Aus diesem Grund empfiehlt es sich, um Ihre Abfrage einzuschränken, nur eine Zeile und eine Spalte zurückzugeben. Alle anderen von der Abfrage zurückgegebenen Daten werden ignoriert. 

Jeder von der Abfrage zurückgegebene Wert wird direkt ersetzt, ohne Escape- oder Anführungszeichen. Wenn die Abfrage keine Zeilen zurückgibt, ist das Ergebnis des Parameters entweder eine leere Zeichenfolge (wenn der Parameter nicht erforderlich ist) oder nicht definiert (wenn der Parameter erforderlich ist).

### <a name="using-a-dropdown"></a>Verwenden eines Dropdowns

Der Parametertyp „Dropdown“ ermöglicht Ihnen das Erstellen eines Dropdown-Steuerelements, was die Auswahl eines oder mehrerer Werte ermöglicht.

Das Dropdown wird von einer Analyseabfrage aufgefüllt. Wenn die Abfrage eine Spalte zurückgibt, sind die Werte in dieser Spalte sowohl der **Wert** als auch die **Bezeichnung** im Dropdown-Steuerelement. Wenn die Abfrage zwei Spalten zurückgibt, ist die erste Spalte der **Wert**, und die zweite Spalte ist die **Bezeichnung**, die im Dropdown angezeigt wird.  Wenn die Abfrage drei Spalten zurückgibt, wird die dritte Spalte verwendet, um die Standardauswahl in diesem Dropdown anzugeben.  Diese Spalte kann von einem beliebigen Typ sein, aber am einfachsten ist es, boolesche oder numerische Typen zu verwenden, bei denen 0 „falsch“ und 1 „wahr“ ist.

 Wenn die Spalte vom Typ „Zeichenfolge“ ist, wird eine Null-/leere Zeichenfolge als „falsch“ betrachtet und jeder andere Wert als „wahr“. Bei Dropdowns mit Einfachauswahl wird der erste Wert mit einem wahren Wert als Standardauswahl verwendet.  Bei Dropdowns mit Mehrfachauswahl werden alle Werte mit einem wahren Wert als standardmäßig ausgewählte Gruppe verwendet. Die Elemente im Dropdown werden in der Reihenfolge angezeigt, in der die Abfrage Zeilen zurückgegeben hat. 

Sehen wir uns die im Bericht „Aktive Benutzer“ vorhandenen Parameter an. Klicken Sie auf das Bearbeitungssymbol neben **TimeRange**.

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/009-time-range.png)

Hierdurch wird das Menüelement „Parameter bearbeiten“ gestartet:

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/010-time-range-edit.png)

Die Abfrage verwendet eine Funktion aus der Analyseabfragesprache namens **datatable**, mit der Sie eine beliebige Tabelle mit vollständigem Inhalt quasi aus dem Nichts generieren können! Die folgende Analyseabfrage beispielsweise:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Generiert folgendes Ergebnis:

![Application Insights – Bearbeitungssteuerelemente für Arbeitsmappenabschnitte](./media/usage-workbooks/011-data-table.png)

Ein praxisnäheres Beispiel besteht in der Verwendung eines Dropdowns für die Auswahl aus einer Gruppe von Ländern/Regionen anhand des Namens:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Die Abfrage zeigt die Ergebnisse wie folgt an:

![Länderdropdown](./media/usage-workbooks/012-country-dropdown.png)

Dropdowns sind unglaublich leistungsfähige Tools zum Anpassen und Erstellen interaktiver Berichte.

### <a name="time-range-parameters"></a>Zeitbereichsparameter

Zwar können Sie Ihren eigenen benutzerdefinierten Zeitbereichsparameter über den Parametertyp „Dropdown“ erstellen, doch können Sie auch den vorkonfigurierten Parametertyp „Zeitbereich“ verwenden, wenn Sie nicht dasselbe Maß an Flexibilität benötigen. 

Parametertypen „Zeitbereich“ besitzen 15 Standardbereiche, die zwischen fünf Minuten und den letzten 90 Tagen liegen. Es gibt auch eine Option, um eine benutzerdefinierte Zeitbereichsauswahl zu ermöglichen, die es dem Bediener des Berichts gestattet, explizite Anfangs- und Endwerte für den Zeitbereich auszuwählen.

### <a name="resource-picker"></a>Ressourcenauswahl

Der Parametertyp „Ressourcenauswahl“ bietet Ihnen die Möglichkeit, den Gültigkeitsbereich Ihres Bericht auf bestimmte Arten von Ressourcen zu beschränken. Ein Beispiel für eine vordefinierte Arbeitsmappe, die den Typ „Ressourcenauswahl“ nutzt, ist die Arbeitsmappe **Failure Insights**.

![Länderdropdown](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Speichern und Freigeben von Arbeitsmappen für Ihr Team

Arbeitsmappen werden in einer Application Insights-Ressource gespeichert, und zwar entweder im für Sie privaten Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte**, der für alle Benutzer mit Zugriff auf die Application Insights-Ressource zugänglich ist. Klicken Sie zum Anzeigen aller Arbeitsmappen in der Ressource in der Aktionsleiste auf die Schaltfläche **Öffnen**.

Gehen Sie wie folgt vor, um eine Arbeitsmappe freizugeben, die derzeit unter **Meine Berichte** vorhanden ist:

1. Klicken Sie in der Aktionsleiste auf **Öffnen**.
2. Klicken Sie neben der Arbeitsmappe, die Sie freigeben möchten, auf die Schaltfläche „...“.
3. Klicken Sie auf **Move to Shared Reports** (In „Freigegebene Berichte“ verschieben).

Klicken Sie in der Aktionsleiste auf **Freigeben**, um eine Arbeitsmappe per Link bzw. E-Mail freizugeben. Beachten Sie, dass Empfänger des Links im Azure-Portal Zugriff auf diese Ressource benötigen, um die Arbeitsmappe anzuzeigen. Zum Durchführen von Bearbeitungen benötigen Empfänger mindestens Berechtigungen vom Typ „Mitwirkender“ für die Ressource.

Gehen Sie wie folgt vor, um in einem Azure-Dashboard einen Link zu einer Arbeitsmappe anzuheften:

1. Klicken Sie in der Aktionsleiste auf **Öffnen**.
2. Klicken Sie neben der Arbeitsmappe, die Sie anheften möchten, auf die Schaltfläche „...“.
3. Klicken Sie auf **An Dashboard anheften**.

## <a name="contributing-workbook-templates"></a>Mitwirken bei Arbeitsmappenvorlagen

Haben Sie eine tolle Arbeitsmappenvorlage erstellt und möchten sie mit der Community teilen? Weitere Informationen finden Sie in unserem [GitHub repo](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Nächste Schritte
- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) oder [Seitenansichten](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Benutzer, Sitzungen, Ereignisse](../../azure-monitor/app/usage-segmentation.md)
    - [Trichter](../../azure-monitor/app/usage-funnels.md)
    - [Aufbewahrung](../../azure-monitor/app/usage-retention.md)
    - [Benutzerabläufe](../../azure-monitor/app/usage-flows.md)
    - [Hinzufügen von Benutzerkontext](../../azure-monitor/app/usage-send-user-context.md)