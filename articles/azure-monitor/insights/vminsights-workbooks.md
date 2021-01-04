---
title: Erstellen interaktiver Berichte mit Arbeitsmappen mithilfe von Azure Monitor für VMs
description: Vereinfachen Sie komplexe Berichte mit vorgefertigten und benutzerdefiniert parametrisierten Arbeitsmappen für Azure Monitor für VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 43cdb9de111bdea5486e49a56d58d38279b685c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95985542"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Erstellen interaktiver Berichte mit Arbeitsmappen mithilfe von Azure Monitor für VMs

Arbeitsmappen kombinieren Text,  [Protokollabfragen](/azure/data-explorer/kusto/query/), Metriken und Parameter zu umfassenden interaktiven Berichten. Arbeitsmappen können von anderen Teammitgliedern bearbeitet werden, die Zugriff auf dieselben Azure-Ressourcen haben.

Arbeitsmappen sind beispielsweise für folgende Szenarien hilfreich:

* Untersuchen der Nutzung Ihres virtuellen Computers, wenn Sie noch nicht wissen, welche Metriken für Sie von Interesse sind: CPU-Auslastung, Speicherplatz auf dem Datenträger, Arbeitsspeicher, Netzwerkabhängigkeiten, usw. Im Gegensatz zu anderen Tools für die Analyse der Nutzung können Sie mit Arbeitsmappen mehrere Arten von Visualisierungen und Analysen kombinieren, sodass Sie bei der Untersuchung deutlich weniger stark eingeschränkt sind.
* Präsentieren von Leistungsindikatoren und anderen Protokollereignissen, um Ihrem Team die Leistung eines kürzlich bereitgestellten virtuellen Computers zu veranschaulichen
* Weitergeben der Ergebnisse eines Größenänderungsexperiments für Ihren virtuellen Computer an andere Mitglieder Ihres Teams. Sie können die Ziele des Experiments in Textform erläutern und anschließend die einzelnen Nutzungsmetriken und Analyseabfragen für die Auswertung des Experiments sowie eindeutige Anmerkungen dazu präsentieren, ob eine Metrik oberhalb oder unterhalb des Zielwerts lag.
* Melden der Auswirkungen eines Ausfalls auf die Nutzung Ihres virtuellen Computers durch eine Kombination aus Daten, einer Erläuterung in Textform und einer Auflistung der nächsten Schritte, mit denen sich Ausfälle in Zukunft vermeiden lassen.

Um Ihnen den Einstieg zu erleichtern, sind in der folgenden Tabelle die Arbeitsmappen zusammengefasst, die Azure Monitor für VMs enthält:

| Arbeitsmappe | BESCHREIBUNG | `Scope` |
|----------|-------------|-------|
| Leistung | Eine anpassbare Version unserer Top-N-Liste und der Diagrammansicht in einer einzelnen Arbeitsmappe, die alle von Ihnen aktivierten Log Analytics-Leistungsindikatoren nutzt.| Skalierbar |
| Leistungsindikatoren | Eine Top-N-Diagrammansicht für ein breites Spektrum von Leistungsindikatoren. | Skalierbar |
| Verbindungen | Eine detaillierte Darstellung der ein- und ausgehenden Verbindungen Ihrer überwachten virtuellen Computer. | Skalierbar |
| Aktive Ports | Eine Liste der Prozesse, die an die Ports der überwachten virtuellen Computer gebunden sind, und ihrer Aktivität innerhalb des ausgewählten Zeitrahmens. | Skalierbar |
| Offene Ports | Die Anzahl geöffneter Ports Ihrer überwachten virtuellen Computer sowie Details zu diesen geöffneten Ports. | Skalierbar |
| Verbindungsfehler | Die Anzahl von Verbindungsfehlern für Ihre überwachten virtuellen Computer, der Fehlertrend sowie die Angabe, ob sich der Prozentsatz an Fehlern im Laufe der Zeit erhöht. | Skalierbar |
| Security and Audit | Eine Analyse Ihres TCP/IP-Datenverkehrs mit Informationen zu Verbindungen im Allgemeinen, zu schädlichen Verbindungen sowie dazu, wo sich die IP-Endpunkte global befinden.  Wenn Sie alle Features aktivieren möchten, müssen Sie die Sicherheitserkennung aktivieren. | Skalierbar |
| TCP-Datenverkehr | Ein Bericht mit einer Rangfolge Ihrer überwachten virtuellen Computer und ihrem gesendeten, empfangenen und gesamten Netzwerkdatenverkehr in einem Raster und als Trendlinie. | Skalierbar |
| Vergleich von Datenverkehr | Diese Arbeitsmappe ermöglicht den Vergleich von Trends beim Netzwerkdatenverkehr eines einzelnen Computers oder einer Gruppe von Computern. | Skalierbar |
| Leistung | Eine anpassbare Version unserer Leistungsansicht, die alle von Ihnen aktivierten Log Analytics-Leistungsindikatoren nutzt. | Einzelne VM | 
| Verbindungen | Eine detaillierte Darstellung der ein- und ausgehenden Verbindungen Ihrer virtuellen Computer. | Einzelne VM |
 
## <a name="creating-a-new-workbook"></a>Erstellen einer neuen Arbeitsmappe

Eine Arbeitsmappe besteht aus Abschnitten, die wiederum aus unabhängig voneinander bearbeitbaren Diagrammen, Tabellen, Text und Eingabesteuerelementen bestehen. Zum besseren Verständnis von Arbeitsmappen öffnen wir eine Vorlage und sehen uns die einzelnen Schritte für die Erstellung einer benutzerdefinierten Arbeitsmappe an. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Virtuelle Computer**.

3. Wählen Sie einen virtuellen Computer in der Liste aus.

4. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights** aus.

5. Wählen Sie auf der Seite „VM Insights“ die Registerkarte **Leistung** oder **Zuordnen** aus, und wählen Sie anschließend über den Link auf der Seite die Option **Arbeitsmappen anzeigen** aus. Wählen Sie in der Dropdownliste den Eintrag **Zum Katalog wechseln** aus.

    ![Screenshot: Dropdownliste mit Arbeitsmappen](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Daraufhin wird der Arbeitsmappenkatalog mit einer Reihe vordefinierter Arbeitsmappen gestartet, die Ihnen den Einstieg erleichtern.

7. Erstellen Sie eine neue Arbeitsmappe, indem Sie **Neu** auswählen.

    ![Screenshot des Arbeitsmappenkatalogs](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Bearbeiten von Arbeitsmappenabschnitten

Arbeitsmappen verfügen über zwei Modi: **Bearbeitungsmodus** und **Lesemodus**. Wenn eine neue Arbeitsmappe zum ersten Mal gestartet wird, wird sie im **Bearbeitungsmodus** geöffnet. In diesem Modus wird der gesamte Inhalt der Arbeitsmappe angezeigt, einschließlich aller Schritte und Parameter, die andernfalls ausgeblendet sind. Im **Lesemodus** wird eine vereinfachte, berichtartige Ansicht präsentiert. Der Lesemodus abstrahiert zwar die Komplexität der Berichterstellung, bei Bedarf können Sie jedoch jederzeit mit wenigen Klicks auf die zugrunde liegenden Mechanismen zugreifen, um Änderungen vorzunehmen.

![Screenshot: Abschnitt für Virtual Machines-Arbeitsmappe in Azure Monitor mit einer neuen Arbeitsmappe im Bearbeitungsmodus und hervorgehobenen Bearbeitungssteuerelementen](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Klicken Sie in der linken unteren Ecke des Abschnitts auf **Bearbeitung abgeschlossen**, wenn Sie mit der Bearbeitung fertig sind.

2. Klicken Sie zum Erstellen eines Duplikats eines Abschnitts auf das Symbol **Abschnitt klonen**. Die Erstellung von doppelten Abschnitten ist eine hervorragende Möglichkeit, eine Abfrage zu durchlaufen, ohne vorherige Iterationen zu verlieren.

3. Klicken Sie auf das Symbol für **Nach oben** bzw. **Nach unten**, um in einer Arbeitsmappe zum nächsten Abschnitt zu springen.

4. Klicken Sie zum dauerhaften Entfernen eines Abschnitts auf das Symbol **Entfernen**.

## <a name="adding-text-and-markdown-sections"></a>Hinzufügen von Text und Markdown-Abschnitten

Das Hinzufügen von Überschriften, Erklärungen und Kommentaren zu Ihren Arbeitsmappen ermöglicht es Ihnen, aus einer Gruppe von Tabellen und Diagrammen eine Geschichte zu formen. Für Textabschnitte in Arbeitsmappen wird die [Markdownsyntax](https://daringfireball.net/projects/markdown/) zum Formatieren von Text unterstützt, z.B. Überschriften, Fettdruck, Kursivdruck und Listen mit Aufzählungszeichen.

Verwenden Sie die Schaltfläche **Text hinzufügen** unten in der Arbeitsmappe bzw. unten in einem Abschnitt, um Ihrer Arbeitsmappe einen Textabschnitt hinzuzufügen.

## <a name="adding-query-sections"></a>Hinzufügen von Abfrageabschnitten

![Abfrageabschnitt in Arbeitsmappen](media/vminsights-workbooks/005-workbook-query-section.png)

Verwenden Sie die Schaltfläche **Abfrage hinzufügen** unten in der Arbeitsmappe bzw. unten in einem Abschnitt, um Ihrer Arbeitsmappe einen Abfrageabschnitt hinzuzufügen.

Abfrageabschnitte sind sehr flexibel und können verwendet werden, um Fragen zu beantworten wie:

* Wie sah meine CPU-Auslastung während eines Zeitraums mit gestiegenem Netzwerkdatenverkehr aus?
* Wie hat sich der verfügbare Speicherplatz im letzten Monat entwickelt?
* Wie viele Netzwerkverbindungsfehler sind für meinen virtuellen Computer in den letzten beiden Wochen aufgetreten? 

Darüber hinaus sind Sie nicht auf Abfragen aus dem Kontext des virtuellen Computers beschränkt, über den Sie die Arbeitsmappe gestartet haben. Sie können Abfragen für mehrere virtuelle Computer sowie für Log Analytics-Arbeitsbereiche ausführen, sofern Sie Zugriffsberechtigungen für diese Ressourcen besitzen.

Mithilfe des Bezeichners **workspace** können Sie Daten aus anderen Log Analytics-Arbeitsbereichen oder aus einer bestimmten Application Insights-App einschließen. Weitere Informationen zu ressourcenübergreifenden Abfragen finden Sie in der [offiziellen Anleitung](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Erweiterte Analyseabfrageeinstellungen

Jeder Abschnitt verfügt über eigene erweiterte Einstellungen, auf die über das Symbol „Einstellungen“ ![Bearbeitungssteuerelemente Arbeitsmappenabschnitte](media/vminsights-workbooks/006-settings.png) rechts neben der Schaltfläche **Parameter hinzufügen** zugegriffen werden kann.

![Screenshot: Dialogfeld „Erweiterte Einstellungen“ im Abschnitt für die Virtual Machines-Arbeitsmappe von Azure Monitor. Das Symbol, mit dem das Dialogfeld geöffnet wird, ist hervorgehoben.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Benutzerdefinierte Breite**    | Legt ein Element auf eine beliebige Größe fest. Dadurch können Sie zahlreiche Elemente in einer einzelnen Zeile platzieren und Ihre Diagramme und Tabellen besser als umfassende interaktive Berichte strukturieren.  |
| **Bedingt sichtbar** | Verwenden Sie diese Option, um Schritte im Lesemodus auf der Grundlage eines Parameters auszublenden. |
| **Parameter exportieren**| Mit dieser Option können durch Auswählen einer Zeile im Raster oder Diagramm die Werte späterer Schritte geändert oder spätere Schritte sichtbar gemacht werden.  |
| **Abfrage anzeigen, wenn keine Bearbeitung erfolgt** | Zeigt die Abfrage oberhalb des Diagramms oder der Tabelle an, auch wenn der Lesemodus aktiv ist.
| **Schaltfläche „In Analyse öffnen“ anzeigen, wenn gerade keine Bearbeitung erfolgt** | Fügt in der rechten Ecke des Diagramms das blaue Analytics-Symbol hinzu, um mit einem Klick auf Analytics zugreifen zu können.|

Die meisten dieser Einstellungen sind recht intuitiv, aber um **Parameter exportieren** zu verstehen, ist es besser, eine Arbeitsmappe zu untersuchen, diese Funktionalität verwendet.

Die vordefinierte Arbeitsmappe **TCP-Datenverkehr** enthält Informationen zu Verbindungsmetriken eines virtuellen Computers.

Der erste Abschnitt der Arbeitsmappe basiert auf Protokollabfragedaten. Der zweite Abschnitt basiert ebenfalls auf Protokollabfragedaten, durch Auswählen einer Zeile in der ersten Tabelle wird jedoch der Inhalt der Diagramme interaktiv aktualisiert:

![Screenshot: Abschnitt „Virtual Machines“ in Azure Monitor mit Anzeige des TCP-Datenverkehrs für die vordefinierte Arbeitsmappe](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Dieses Verhalten wird durch die erweiterte Einstellung **Bei Auswahl eines Elements Parameter exportieren** möglich, die in der Protokollabfrage der Tabelle aktiviert wird.

![Screenshot: Dialogfeld „Erweiterte Einstellungen“ für eine Virtual Machines-Arbeitsmappe mit Aktivierung der Option „Bei Auswahl eines Elements Parameter exportieren“](media/vminsights-workbooks/009-settings-export.png)

Wenn eine Zeile ausgewählt wird, werden die exportierten Werte von der zweiten Protokollabfrage genutzt, um eine Gruppe von Werten zu erstellen, die dann von der Abschnittsüberschrift und den Diagrammen verwendet werden. Wird keine Zeile ausgewählt, werden Abschnittsüberschrift und Diagramme ausgeblendet. 

Der ausgeblendete Parameter im zweiten Abschnitt verwendet beispielsweise den folgenden Verweis aus der im Raster ausgewählten Zeile:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Hinzufügen von Metrikabschnitten

Metrikabschnitte bieten Ihnen vollständigen Zugriff, um Metrikdaten von Azure Monitor in Ihre interaktiven Berichte zu integrieren. Die vorgefertigten Arbeitsmappen in Azure Monitor für VMs enthalten in der Regel keine Metrikdaten, sondern Analyseabfragedaten.  Sie können allerdings Arbeitsmappen mit Metrikdaten erstellen, um die Vorteile beider Features miteinander zu kombinieren. Sie können außerdem Metrikdaten aus Ressourcen in jedem der Abonnements einlesen, auf die Sie Zugriff haben.

Hier sehen Sie ein Beispiel für Daten eines virtuellen Computers, die in eine Arbeitsmappe gepullt werden, um eine Rastervisualisierung der CPU-Leistung bereitzustellen:

![Screenshot: Abschnitt mit Metriken einer VM-Arbeitsmappe in Azure Monitor. Die CPU-Leistung aller virtuellen Computer ist grafisch dargestellt.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Hinzufügen von Parameterabschnitten

Arbeitsmappenparameter erlauben Ihnen das Ändern von Werten in der Arbeitsmappe, ohne die Abfrage- oder Textabschnitte manuell bearbeiten zu müssen. Hierdurch entfällt die Anforderung, die zugrunde liegende Analyseabfragesprache verstehen zu müssen, was die potenzielle Zielgruppe für arbeitsmappenbasierte Berichterstellung erheblich erweitert.

Die Werte der Parameter werden in Abfrage-, Text- oder anderen Parameterabschnitten eingesetzt, indem der Name des Parameters in geschweifte Klammern gesetzt wird, z. B. ``{parameterName}``. Für Parameternamen gelten ähnliche Regeln wie für JavaScript-Bezeichner: Buchstaben oder Unterstriche, gefolgt von alphanumerischen Zeichen oder Unterstrichen. Beispielsweise ist **a1** zulässig, während **1a** nicht zulässig ist.

Parameter sind linear, beginnen am Anfang einer Arbeitsmappe und fließen nach unten zu den jeweils folgenden Schritten.  Später in der Arbeitsmappe deklarierte Parameter können weiter oben deklarierte Parameter überschreiben. Dies ermöglicht auch die Verwendung von Parametern, die Abfragen verwenden, um auf die Werte weiter oben definierter Parameter zuzugreifen. Innerhalb des Schritts eines Parameters sind Parameter ebenfalls linear, von links nach rechts, wobei rechts liegende Parameter von einem früher im selben Schritt deklarierten Parameter abhängig sein können.
 
Aktuell werden vier verschiedene Arten von Parametern unterstützt:

|                  |      |
| ---------------- |:-----|
| **Text**    | Ermöglicht es dem Benutzer, ein Textfeld zu bearbeiten. Sie können optional eine Abfrage bereitstellen, um den Standardwert einzutragen. |
| **Dropdown** | Ermöglicht es dem Benutzer, aus einer Gruppe von Werten zu wählen. |
| **Zeitbereichsauswahl**| Ermöglicht es dem Benutzer, aus einem vordefinierten Satz von Zeitbereichswerten oder aus einem benutzerdefinierten Zeitbereich zu wählen.|
| **Ressourcenauswahl** | Ermöglicht es dem Benutzer, aus den für die Arbeitsmappe ausgewählten Ressourcen zu wählen.|

### <a name="using-a-text-parameter"></a>Verwenden eines Textparameters

Der Wert, den ein Benutzer in das Textfeld eingibt, wird direkt in der Abfrage ersetzt (ohne Escape- oder Anführungszeichen). Wenn der benötigte Wert eine Zeichenfolge ist, sollte die Abfrage den Parameter in Anführungszeichen setzen (z. B. **'{parameter}'** ).

Mit dem Textparameter kann der Wert eines Textfelds überall verwendet werden. Er kann ein Tabellenname, Spaltenname, Funktionsname, Operator usw. sein.  Der Parametertyp „Text“ verfügt über die Einstellung **Standardwert aus Analyseabfrage abrufen**, die es dem Ersteller der Arbeitsmappe ermöglicht, eine Abfrage zu verwenden, um den Standardwert für das Textfeld aufzufüllen.

Wenn Sie den Standardwert aus einer Protokollabfrage verwenden, wird nur der erste Wert der ersten Zeile (Zeile 0, Spalte 0) als Standardwert verwendet. Aus diesem Grund empfiehlt es sich, um Ihre Abfrage einzuschränken, nur eine Zeile und eine Spalte zurückzugeben. Alle anderen von der Abfrage zurückgegebenen Daten werden ignoriert. 

Jeder von der Abfrage zurückgegebene Wert wird direkt ersetzt, ohne Escape- oder Anführungszeichen. Wenn die Abfrage keine Zeilen zurückgibt, ist das Ergebnis des Parameters entweder eine leere Zeichenfolge (wenn der Parameter nicht erforderlich ist) oder nicht definiert (wenn der Parameter erforderlich ist).

### <a name="using-a-drop-down"></a>Verwenden eines Dropdown-Steuerelements

Der Parametertyp „Dropdown“ ermöglicht das Erstellen eines Dropdown-Steuerelements, über das einzelne oder mehrere Werte ausgewählt werden können.

Das Dropdown-Steuerelement wird per Protokollabfrage oder JSON aufgefüllt. Wenn die Abfrage eine einzelne Spalte zurückgibt, sind die Werte in dieser Spalte sowohl der Wert als auch die Bezeichnung im Dropdown-Steuerelement. Wenn die Abfrage zwei Spalten zurückgibt, ist die erste Spalte der Wert und die zweite Spalte die Bezeichnung im Dropdown-Steuerelement. Wenn die Abfrage drei Spalten zurückgibt, gibt die dritte Spalte die Standardauswahl in diesem Dropdown-Steuerelement an. Diese Spalte kann von einem beliebigen Typ sein, aber am einfachsten ist es, boolesche oder numerische Typen zu verwenden, bei denen 0 „falsch“ und 1 „wahr“ ist.

Wenn die Spalte vom Typ „Zeichenfolge“ ist, wird eine Null-/leere Zeichenfolge als „falsch“ betrachtet und jeder andere Wert als „wahr“. Bei Dropdown-Steuerelementen mit Einfachauswahl wird der erste Wert mit einem wahren Wert als Standardauswahl verwendet.  Bei Dropdown-Steuerelementen mit Mehrfachauswahl werden alle Werte mit einem wahren Wert als standardmäßig ausgewählte Gruppe verwendet. Die Elemente im Dropdown-Steuerelement werden in der Reihenfolge angezeigt, in der die Abfrage Zeilen zurückgegeben hat. 

Sehen wir uns die Parameter im Verbindungsübersichtsbericht an. Klicken Sie neben **Richtung** auf das Bearbeitungssymbol.

![Screenshot: Abschnitt zum Hinzufügen und Bearbeiten von Berichtsparametern in Azure Monitor. Das Symbol „Bearbeiten“ für den Parameter „Direction“ ist ausgewählt.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Daraufhin wird das Menüelement **Parameter bearbeiten** gestartet.

![Screenshot: Dialogfeld „Parameter bearbeiten“. Der Name des Parameters lautet „Direction“, der Parametertyp ist „Dropdown“, und für „Daten abrufen aus“ ist „JSON“ ausgewählt.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Mithilfe des JSON-Codes können Sie eine beliebige Tabelle mit Inhalt generieren. Der folgende JSON-Code generiert beispielsweise zwei Werte im Dropdown-Steuerelement:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Ein praxisnäheres Beispiel wäre etwa die Verwendung eines Dropdown-Steuerelements zur Auswahl aus einer Gruppe von Leistungsindikatoren anhand des Namens:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Die Abfrage zeigt die Ergebnisse wie folgt an:

![Dropdown-Steuerelement für Leistungsindikatoren](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Dropdown-Steuerelemente sind äußerst leistungsfähige Tools für die Anpassung und Erstellung interaktiver Berichte.

### <a name="time-range-parameters"></a>Zeitbereichsparameter

Zwar können Sie Ihren eigenen benutzerdefinierten Zeitbereichsparameter über den Parametertyp „Dropdown“ erstellen, doch können Sie auch den vorkonfigurierten Parametertyp „Zeitbereich“ verwenden, wenn Sie nicht dasselbe Maß an Flexibilität benötigen. 

Parametertypen „Zeitbereich“ besitzen 15 Standardbereiche, die zwischen fünf Minuten und den letzten 90 Tagen liegen. Es gibt auch eine Option, um eine benutzerdefinierte Zeitbereichsauswahl zu ermöglichen, die es dem Bediener des Berichts gestattet, explizite Anfangs- und Endwerte für den Zeitbereich auszuwählen.

### <a name="resource-picker"></a>Ressourcenauswahl

Der Parametertyp „Ressourcenauswahl“ bietet Ihnen die Möglichkeit, den Gültigkeitsbereich Ihres Bericht auf bestimmte Arten von Ressourcen zu beschränken. Ein Beispiel für eine vorgefertigte Arbeitsmappe mit Ressourcenauswahl ist die Arbeitsmappe **Leistung**.

![Dropdown-Steuerelement für Arbeitsbereiche](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Speichern und Freigeben von Arbeitsmappen für Ihr Team

Arbeitsmappen werden in einem Log Analytics-Arbeitsbereich oder in einer VM-Ressource gespeichert (je nachdem, wie Sie auf den Arbeitsmappenkatalog zugreifen). Die Arbeitsmappe kann im für Sie privaten Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte** gespeichert werden. Letzterer ist für alle Benutzer mit Zugriff auf die Ressource zugänglich. Klicken Sie zum Anzeigen aller Arbeitsmappen in der Ressource in der Aktionsleiste auf die Schaltfläche **Öffnen**.

Gehen Sie wie folgt vor, um eine Arbeitsmappe freizugeben, die derzeit unter **Meine Berichte** vorhanden ist:

1. Klicken Sie in der Aktionsleiste auf **Öffnen**.
2. Klicken Sie neben der Arbeitsmappe, die Sie freigeben möchten, auf die Schaltfläche „...“.
3. Klicken Sie auf **Move to Shared Reports** (In „Freigegebene Berichte“ verschieben).

Klicken Sie in der Aktionsleiste auf **Freigeben**, um eine Arbeitsmappe per Link bzw. E-Mail freizugeben. Beachten Sie, dass Empfänger des Links im Azure-Portal Zugriff auf diese Ressource benötigen, um die Arbeitsmappe anzuzeigen. Zum Durchführen von Bearbeitungen benötigen Empfänger mindestens Berechtigungen vom Typ „Mitwirkender“ für die Ressource.

Gehen Sie wie folgt vor, um in einem Azure-Dashboard einen Link zu einer Arbeitsmappe anzuheften:

1. Klicken Sie in der Aktionsleiste auf **Öffnen**.
2. Klicken Sie neben der Arbeitsmappe, die Sie anheften möchten, auf die Schaltfläche „...“.
3. Klicken Sie auf **An Dashboard anheften**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Ermitteln von Beschränkungen und der Gesamtleistung einer VM finden Sie unter [Direktes Anzeigen der Leistung in einer Azure-VM](vminsights-performance.md).

- Weitere Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Anzeigen der Zuordnung in Azure Monitor](vminsights-maps.md).