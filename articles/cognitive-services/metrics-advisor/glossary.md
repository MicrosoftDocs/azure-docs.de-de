---
title: Metrics Advisor-Glossar
titleSuffix: Azure Cognitive Services
description: Wichtige Konzepte und Begriffe für den Metrics Advisor-Dienst
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893419"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Metrics Advisor-Glossar mit häufigen Vokabeln und Begriffen

In diesem Dokument werden die technischen Begriffe beschrieben, die in Metrics Advisor verwendet werden. Verwenden Sie diesen Artikel, um sich über häufig verwendete Begriffe und Objekte zu informieren, die Ihnen bei der Nutzung des Diensts begegnen können.

## <a name="data-feed"></a>Datenfeed

> [!NOTE]
> Für mehrere Metriken kann dieselbe Datenquelle und sogar derselbe Datenfeed gemeinsam verwendet werden.

Ein Datenfeed umfasst die Daten, die von Metrics Advisor über Ihre Datenquelle erfasst werden, z. B. Cosmos DB oder eine SQL Server-Instanz. Ein Datenfeed enthält Zeilen mit den folgenden Informationen:
* Timestamps
* Null oder mehr Dimensionen
* Ein oder mehrere Measures 

## <a name="metric"></a>Metrik

Eine Metrik ist ein quantifizierbares Measure, das zum Überwachen und Bewerten des Status eines bestimmten Geschäftsprozesses verwendet wird. Hierbei kann es sich um eine Kombination aus mehreren Zeitreihen handeln, die in Dimensionen unterteilt sind. Eine Metrik vom Typ *Webintegrität* kann beispielsweise Dimensionen für die *Benutzeranzahl* und den *US-amerikanischen Markt* enthalten.

## <a name="dimension"></a>Dimension

Eine Dimension umfasst einen oder mehrere Kategoriewerte. Mit der Kombination dieser Werte wird eine bestimmte univariate Zeitreihe identifiziert, z. B. Land, Sprache, Mandant usw.

## <a name="multi-dimensional-metric"></a>Mehrdimensionale Metrik

Was ist eine mehrdimensionale Metrik? Wir verwenden zwei Beispiele. 

**Umsatz für ein Unternehmen**

Angenommen, Sie verfügen über Daten zum Umsatz Ihres Unternehmens. Ihre Zeitreihendaten können beispielsweise Folgendes umfassen:

| Timestamp | Category | Market | Umsatz |
| ----------|----------|--------|----- |
| 01.06.2020 | Lebensmittel | US | 1000 |
| 01.06.2020 | Kleidung | US | 2000 |
| 02.06.2020 | Lebensmittel | UK | 800 | 
| ...      | ...  |... | ... |

In diesem Beispiel werden die Dimensionen *Category* (Kategorie) und *Market* (Markt) verwendet. *Revenue* (Umsatz) ist der Key Performance Indicator (KPI), der in unterschiedliche Kategorien bzw. Märkte unterteilt und außerdem aggregiert werden kann. Ein Beispiel hierfür ist der Umsatz für *Lebensmittel* für alle Märkte.
 
**Fehleranzahl für eine komplexe Anwendung**

Angenommen, Sie verfügen über Daten zur Anzahl von Fehlern, die in einer Anwendung protokolliert werden. Ihre Zeitreihendaten können beispielsweise Folgendes umfassen:

| Timestamp | Anwendungskomponente | Region | Fehler (Anzahl) |
| ----------|----------|--------|----- |
| 01.06.2020 | Mitarbeiterdatenbank | EUROPA, WESTEN | 9000 |
| 01.06.2020 | Nachrichtenwarteschlange | USA, OSTEN | 1000 |
| 02.06.2020 | Nachrichtenwarteschlange | USA, OSTEN | 8.000| 
| ...      | ...           | ...     |  ...|

Dieses Beispiel enthält die Dimensionen *Anwendungskomponente* und *Region* . *Fehleranzahl* kann in unterschiedliche Kategorien bzw. Märkte unterteilt und außerdem aggregiert werden. Ein Beispiel hierfür ist die Fehleranzahl für *Nachrichtenwarteschlange* in allen Regionen.

## <a name="measure"></a>"Measure"

Ein Measure ist ein grundlegender bzw. einheitenspezifischer Ausdruck und ein quantifizierbarer Wert der Metrik.

## <a name="time-series"></a>Zeitreihe

Eine Zeitreihe ist eine Reihe von Datenpunkten, die in chronologischer Reihenfolge indiziert (bzw. aufgelistet oder dargestellt) werden. Meist handelt es sich bei einer Zeitreihe um eine Sequenz, die auf aufeinanderfolgenden Zeitpunkten mit dem gleichen Abstand zueinander basiert. Dies ist also eine Sequenz mit einzelnen Zeitdaten.

In Metrics Advisor werden Werte einer Metrik für eine bestimmte Dimensionskombination als Reihe bezeichnet.

## <a name="granularity"></a>Granularität

Mit der Granularität wird angegeben, wie häufig auf der Datenquelle Datenpunkte generiert werden. Beispiele hierfür sind „Täglich“ oder „Wöchentlich“.

## <a name="start-time"></a>Startzeit

Die Startzeit ist der Zeitpunkt, ab dem Metrics Advisor mit dem Erfassen von Daten von Ihrer Datenquelle beginnen soll. Ihre Datenquelle muss zur angegebenen Startzeit über Daten verfügen.

## <a name="confidence-boundaries"></a>Vertrauensgrenzen

> [!NOTE]
> Vertrauensgrenzen sind nicht der einzige Messwert, der zum Ermitteln von Anomalien verwendet wird. Es ist möglich, dass Datenpunkte außerhalb dieser Grenzen vom Erkennungsmodell als „normal“ gekennzeichnet werden. 

In Metrics Advisor stehen Vertrauensgrenzen für die Empfindlichkeit des verwendeten Algorithmus und werden verwendet, um übermäßig empfindliche Anomalien herauszufiltern. Im Webportal werden Vertrauensgrenzen als transparentes blaues Band angezeigt. Alle Punkte innerhalb des Bands werden als normale Punkte behandelt.

Von Metrics Advisor werden Tools bereitgestellt, um die Empfindlichkeit der verwendeten Algorithmen anzupassen. Unter [Vorgehensweise: Konfigurieren von Metriken und Optimieren der Konfigurationserkennung](how-tos/configure-metrics.md) finden Sie hierzu weitere Informationen.

![Vertrauensgrenzen](media/confidence-bounds.png)


## <a name="hook"></a>Hook

Mit Metrics Advisor können Sie Echtzeitwarnungen erstellen und abonnieren. Diese Warnungen werden [per hook](how-tos/alerts.md) über das Internet gesendet.

## <a name="anomaly-incident"></a>Incident aufgrund einer Anomalie

Nachdem eine Erkennungskonfiguration auf Metriken angewendet wurde, wird jeweils ein Incident generiert, wenn eine enthaltene Reihe eine Anomalie aufweist. Da dies bei größeren Datasets sehr aufwendig sein kann, gruppiert Metrics Advisor Anomaliereihen in einer Metrik unter einem Incident. Der Dienst wertet zudem den Schweregrad aus und stellt Tools für die [Diagnose des Incidents](how-tos/diagnose-incident.md) bereit.

### <a name="incident-tree"></a>Incidentstruktur

In Metrics Advisor können Sie die Anomalieerkennung auf Metriken anwenden. Von Metrics Advisor werden dann automatisch alle Zeitreihen aller Dimensionskombinationen überwacht. Bei jeder Erkennung einer Anomalie werden von Metrics Advisor Anomalien zu Incidents aggregiert.
Nach dem Auftreten des Incidents wird von Metrics Advisor eine Incidentstruktur mit einer Hierarchie relevanter Anomalien bereitgestellt, und es wird ermittelt, welche davon die größte Auswirkung haben. Jeder Incident verfügt über eine Grundursachenanomalie, die jeweils als oberster Knoten der Struktur angezeigt wird.

### <a name="anomaly-grouping"></a>Anomaliegruppierung

Metrics Advisor verfügt über eine Funktion zum Suchen nach verwandten Zeitreihen mit ähnlichen Mustern. Sie kann auch genauere Erkenntnisse zur Auswirkung auf andere Dimensionen liefern und die Anomalien korrelieren.

### <a name="time-series-comparison"></a>Zeitreihenvergleich

Sie können mehrere Zeitreihen auswählen, um Trends in einer einzelnen Visualisierung zu vergleichen. Dies ist eine eindeutige und aufschlussreiche Möglichkeit, um zusammengehörige Reihen anzuzeigen und zu vergleichen.

## <a name="detection-configuration"></a>Erkennungskonfiguration

>[!Note]
>Erkennungskonfigurationen werden nur innerhalb einer bestimmten Metrik angewendet.

Im Metrics Advisor-Webportal ist die Erkennungskonfiguration (z. B. Empfindlichkeit, automatische erneute Erinnerung und Richtung) beim Anzeigen einer Metrik im Bereich auf der linken Seite aufgeführt. Parameter können für alle Reihen dieser Metrik optimiert und angewendet werden.

Eine Erkennungskonfiguration ist für jede Zeitreihe erforderlich und bestimmt, ob ein Punkt in der Zeitreihe eine Anomalie ist. Metrics Advisor richtet eine Standardkonfiguration für die gesamte Metrik ein, wenn Sie zum ersten Mal das Onboarding für Daten durchführen. 

Sie können die Konfiguration zusätzlich optimieren, indem Sie Optimierungsparameter auf eine Gruppe mit mehreren Reihen oder auf eine bestimmte Reihe anwenden. Auf eine Zeitreihe wird nur eine Konfiguration angewendet:
* Mit auf eine bestimmte Reihe angewendeten Konfigurationen werden Konfigurationen für eine Gruppe außer Kraft gesetzt
* Mit Konfigurationen für eine Gruppe werden Konfigurationen außer Kraft gesetzt, die auf die gesamte Metrik angewendet werden.

Metrics Advisor verfügt über mehrere [Erkennungsmethoden](how-tos/configure-metrics.md#anomaly-detection-methods), und Sie können sie mit logischen Operatoren kombinieren.

### <a name="smart-detection"></a>Intelligente Erkennung

Anomalieerkennung mit mehreren Machine Learning-Algorithmen.

**Empfindlichkeit** : Ein numerischer Wert, mit dem die Toleranz der Anomalieerkennung angepasst werden kann. Je höher der Wert ist, desto näher liegt die obere und untere Grenze an der Zeitreihe.

### <a name="hard-threshold"></a>Harter Schwellenwert

Werte außerhalb der oberen und unteren Grenze sind Anomalien.

**Min** : Untere Grenze

**Max** : Obere Grenze

### <a name="change-threshold"></a>Änderungsschwellenwert

Verwenden Sie den vorherigen Punktwert, um zu ermitteln, ob es sich bei dem Punkt um eine Anomalie handelt.

**Change percentage** (Änderungsprozentsatz): Im Vergleich zum vorherigen Punkt ist der aktuelle Punkt eine Anomalie, wenn der Änderungsprozentsatz höher als dieser Parameter ist.

**Change over points** (Punkte für Änderung): Gibt an, wie viele Punkte die Änderung zurückreichen soll.

### <a name="common-parameters"></a>Allgemeine Parameter

**Richtung** : Bei einem Punkt handelt es sich nur um eine Anomalie, wenn die Abweichung die Richtung *Nach oben* , *Nach unten* oder *Beide* aufweist.

**Not valid anomaly until** (Erst gültige Anomalie ab): Ein Datenpunkt ist nur eine Anomalie, wenn es sich bei einem angegebenen Prozentsatz vorheriger Punkte ebenfalls um Anomalien handelt.

## <a name="alert-settings"></a>Warnungseinstellungen

Mit den Warnungseinstellungen wird ermittelt, bei welchen Anomalien eine Warnung ausgelöst werden soll. Sie können mehrere Warnungen mit unterschiedlichen Einstellungen festlegen. Beispielsweise können Sie eine Warnung für Anomalien mit einer geringeren geschäftlichen Auswirkung und eine andere für wichtigere Warnungen erstellen.

Darüber hinaus können Sie auch eine metrikübergreifende Warnung erstellen. Dies kann beispielsweise eine Warnung sein, die nur ausgelöst wird, wenn zwei angegebene Metriken über Anomalien verfügen.

### <a name="alert-scope"></a>Warnungsbereich

Der Warnungsbereich bezieht sich auf den Bereich, für den die Warnung gilt. Es gibt vier Optionen:

**Anomalies of all series** (Anomalien aller Reihen): Warnungen werden für Anomalien in allen Reihen innerhalb der Metrik ausgelöst.

**Anomalies in series group** (Anomalien in der Reihengruppe): Warnungen werden nur für Anomalien in bestimmten Dimensionen der Reihengruppe ausgelöst. Die Anzahl von angegebenen Dimensionen sollte kleiner als die Gesamtzahl der Dimensionen sein.

**Anomalies in favorite series** (Anomalien in bevorzugten Reihen): Warnungen werden nur für Anomalien ausgelöst, die als Favoriten hinzugefügt werden. Sie können eine Gruppe mit Reihen als Favorit für jede erkannte Konfiguration auswählen.

**Anomalies in top N of all series** (Oberste „n“ Anomalien aller Reihen): Warnungen werden nur für Anomalien der oberen „n“ Reihen ausgelöst. Sie können Parameter festlegen, um die Anzahl von zu berücksichtigenden Zeitstempeln und die Anzahl von Anomalien anzugeben, die darin enthalten sein müssen, damit die Warnung gesendet wird.

### <a name="severity"></a>severity

Anhand des Schweregradwerts wird von Metrics Advisor der Schweregrad des Incidents angegeben, z. B. *Hoch* , *Mittel* und *Niedrig* .

Von Metrics Advisor werden derzeit die folgenden Faktoren verwendet, um den Warnungsschweregrad zu messen:
1. Der Wertanteil und der Mengenanteil für die Anomalien in der Metrik.
1. Vertrauenswert von Anomalien.
1. Ihre bevorzugten Einstellungen tragen ebenfalls zum Schweregrad bei.

### <a name="auto-snooze"></a>Automatisches erneutes Erinnern

Bei einigen Anomalien handelt es sich um vorübergehende Probleme. Dies gilt vor allem für Metriken mit geringerer Granularität. Sie können für eine Warnung für eine bestimmte Anzahl von Zeitpunkten die *erneute Erinnerung* nutzen. Falls innerhalb der angegebenen Anzahl von Punkten Anomalien gefunden werden, wird keine Warnung ausgelöst. Das Verhalten der automatischen erneuten Erinnerung kann auf Metrik- oder Reihenebene festgelegt werden.

Das Verhalten der erneuten Erinnerung kann auf Metrik- oder Reihenebene festgelegt werden.

## <a name="data-feed-settings"></a>Datenfeedeinstellungen

### <a name="ingestion-time-offset"></a>Ingestion time offset (Erfassungszeit-Offset)

Standardmäßig werden Daten gemäß der Granularität erfasst (z. B. *Täglich* ). Mit einer positiven ganzen Zahl können Sie die Erfassung der Daten um den angegebenen Wert verzögern. Bei Verwendung einer negativen Zahl können Sie die Erfassung um den angegebenen Wert erweitern.

### <a name="max-ingestion-per-minute"></a>Max Ingestion per Minute (Maximale Erfassung pro Minute)

Legen Sie diesen Parameter fest, wenn Ihre Datenquelle eingeschränkte Parallelität unterstützt. Behalten Sie andernfalls die Standardeinstellungen bei.

### <a name="stop-retry-after"></a>Stop retry after (Wiederholungsversuche beenden nach)

Falls bei der Datenerfassung ein Fehler aufgetreten ist, führt Metrics Advisor nach einer bestimmten Zeit automatisch einen Wiederholungsversuch durch. Der Anfang dieses Zeitraums ist der Zeitpunkt, zu dem die erste Datenerfassung stattgefunden hat. Die Länge des Wiederholungszeitraums wird gemäß der Granularität definiert. Bei Verwendung des Standardwerts (`-1`) wird der Wiederholungszeitraum gemäß der Granularität bestimmt:

| Granularität       | Stop retry after (Wiederholungsversuche beenden nach)           |
| :------------ | :--------------- |
| Täglich, Benutzerdefiniert (>= 1 Tag), Wöchentlich, Monatlich, Jährlich     | 7 Tage          |
| Stündlich, Benutzerdefiniert (< 1 Tag)       | 72 Stunden |

### <a name="min-retry-interval"></a>Min retry interval (Min. Wiederholungsintervall)

Sie können das Mindestintervall angeben, nach dem das Pullen von Daten von der Quelle wiederholt werden soll. Bei Verwendung des Standardwerts (`-1`) wird das Wiederholungsintervall gemäß der Granularität bestimmt:

| Granularität       | Mindestwiederholungsintervall           |
| :------------ | :--------------- |
| Täglich, Benutzerdefiniert (>= 1 Tag), Wöchentlich, Monatlich     | 30 Minuten          |
| Stündlich, Benutzerdefiniert (< 1 Tag)      | 10 Minuten |
| Jährlich | 1 Tag          |

### <a name="grace-period"></a>Karenzzeit

> [!Note]
> Die Toleranzperiode beginnt zu dem Zeitpunkt, der sich aus der Summe der regulären Erfassungszeit und des Erfassungszeit-Offsetwerts ergibt.
    
Eine Toleranzperiode ist ein Zeitraum, in dem Metrics Advisor mit dem Abrufen von Daten von der Datenquelle fortfährt, aber keine Warnungen auslöst. Falls nach der Toleranzperiode keine Daten erfasst wurden, wird eine Warnung vom Typ *Datenfeed nicht verfügbar* ausgelöst.

### <a name="snooze-alerts-in"></a>Snooze alerts in (Erneute Erinnerung in)

Wenn diese Option auf Null festgelegt ist, löst jeder Zeitstempel mit *Nicht verfügbar* eine Warnung aus. Bei einem anderen Wert als Null wird für die angegebene Anzahl von Warnungen vom Typ *Nicht verfügbar* ein erneuter Erinnerungsvorgang durchgeführt, wenn keine Daten abgerufen wurden.

## <a name="data-feed-permissions"></a>Datenfeedberechtigungen

Es sind zwei Rollen zum Verwalten von Datenfeedberechtigungen verfügbar: *Administrator* und *Anzeigender Benutzer* . 

* Ein *Administrator* hat die volle Kontrolle über den Datenfeed und die darin enthaltenen Metriken. Sie können den Datenfeed aktivieren, anhalten und löschen und Updates an Feeds und Konfigurationen vornehmen. Ein *Administrator* ist normalerweise der Besitzer der Metriken.

* *Anzeigende Benutzer* können den Datenfeed bzw. die Metriken anzeigen, aber keine Änderungen vornehmen. 

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über Metrics Advisor](overview.md)
- [Verwenden des Webportals](quickstarts/web-portal.md)