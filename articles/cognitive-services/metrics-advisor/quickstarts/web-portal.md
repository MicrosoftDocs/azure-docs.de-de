---
title: 'Schnellstart: Metrics Advisor-Webportal'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den ersten Schritten mit dem Metrics Advisor-Webportal.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 5dbf98d363429e6d22a0b7719cdc669deebd21a0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348635"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Schnellstart: Überwachen Ihrer ersten Metrik mithilfe des Webportals

Wenn Sie eine Metrics Advisor-Instanz bereitstellen, können Sie den Dienst über die APIs sowie über den webbasierten Arbeitsbereich verwenden. Der webbasierte Arbeitsbereich ermöglicht einen einfachen und schnellen Einstieg in die Verwendung des Diensts. Darüber hinaus können Sie hier visuell Einstellungen konfigurieren, Ihr Modell anpassen und Ursachenanalysen durchführen. 

* Durchführen des Onboardings für Ihre Metrikdaten
* Anzeigen Ihrer Metriken und Visualisierungen
* Optimieren von Erkennungskonfigurationen
* Untersuchen von Diagnoseerkenntnissen
* Erstellen und Abonnieren von Anomaliewarnungen

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  

    
> [!TIP]
> * Die Bereitstellung Ihrer Metrics Advisor-Ressource kann zwischen zehn und 30 Minuten dauern. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**.
> * Wenn Sie über die REST-API mit dem Dienst interagieren möchten, benötigen Sie den Schlüssel und den Endpunkt der erstellten Ressource. Diese finden Sie in der erstellten Ressource auf der Registerkarte **Schlüssel und Endpunkte**.

In diesem Dokument wird eine SQL-Datenbank-Instanz als Beispiel für die Erstellung Ihres ersten Monitors verwendet.

## <a name="sign-in-to-your-workspace"></a>Anmelden in Ihrem Arbeitsbereich

Melden Sie sich nach der Erstellung der Ressource beim [Metrics Advisor-Portal](https://go.microsoft.com/fwlink/?linkid=2143774) an. Wählen Sie Ihren Arbeitsbereich aus, um mit der Überwachung Ihrer Metriken zu beginnen. 
 
Derzeit können Sie in jeder verfügbaren Region eine Metrics Advisor-Ressource erstellen. Sie können die Arbeitsbereiche im Metrics Advisor-Portal jederzeit wechseln.


## <a name="onboard-time-series-data"></a>Durchführen des Onboardings für Zeitreihendaten

Metrics Advisor bietet Connectors für verschiedene Datenquellen wie SQL-Datenbank, Azure Data Explorer und Azure Table Storage. Die Schritte für die Verbindung von Daten sind für unterschiedliche Connectors zwar ähnlich, einige Konfigurationsparameter können jedoch variieren. Informationen zu den erforderlichen Parametern für bestimmte Datenquellen finden Sie unter [Verbinden von Daten aus verschiedenen Quellen](../data-feeds-from-different-sources.md).

In dieser Schnellstartanleitung wird als Beispiel eine SQL-Datenbank-Instanz verwendet. Mit den gleichen Schritten können Sie auch Ihre eigenen Daten erfassen.

Melden Sie sich zunächst mit Ihrem Active Directory-Konto bei Ihrem Metrics Advisor-Arbeitsbereich an. Wählen Sie auf der Zielseite Ihr **Verzeichnis**, Ihr **Abonnement** und den soeben erstellten **Arbeitsbereich** aus, und klicken Sie anschließend auf **Erste Schritte**. Wählen Sie im linken Menü die Option **Add data feed** (Datenfeed hinzufügen) aus, nachdem die Hauptseite der Workload geladen wurde.

### <a name="data-schema-requirements-and-configuration"></a>Datenschemaanforderungen und -konfiguration

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfigurieren der Verbindungseinstellungen

> [!TIP]
> Ausführliche Informationen zu den verfügbaren Parametern finden Sie unter [Hinzufügen von Datenfeeds](../how-tos/onboard-your-data.md).

Fügen Sie den Datenfeed hinzu, indem Sie eine Verbindung mit Ihrer Zeitreihendatenquelle herstellen. Wählen Sie die folgenden Parameter aus:

* **Quelltyp**: Die Art der Datenquelle, in der Ihre Zeitreihendaten gespeichert sind.
* **Granularität**: Das Intervall zwischen aufeinanderfolgenden Datenpunkten in Ihren Zeitreihendaten (beispielsweise jährlich, monatlich oder täglich). Das kürzestmögliche Intervall für die Anpassung beträgt 60 Sekunden.
* **Ingest data since (UTC)** (Daten erfassen seit (UTC)): Die Startzeit für den ersten zu erfassenden Zeitstempel. 


Geben Sie als Nächstes die **Verbindungszeichenfolge** mit den Anmeldeinformationen für Ihre Datenquelle sowie eine benutzerdefinierte **Abfrage** an. Die Abfrage dient zum Angeben der Daten, die erfasst und in das erforderliche Schema konvertiert werden sollen.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Verbindungseinstellungen" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Überprüfen der Verbindung und Laden des Datenschemas

Wählen Sie nach Erstellung der Verbindungs- und Abfragezeichenfolge die Option **Verify and get schema** (Schema überprüfen und abrufen) aus, um die Verbindung zu überprüfen und die Abfrage auszuführen, damit Ihr Datenschema aus der Datenquelle abgerufen wird. Je nach Datenquellenverbindung dauert dies normalerweise einige Sekunden. Stellen Sie Folgendes sicher, falls bei diesem Schritt ein Fehler auftritt:

1. Ihre Verbindungszeichenfolge und die Abfrage sind korrekt.
2. Mit Ihrer Metrics Advisor-Instanz kann eine Verbindung mit der Datenquelle hergestellt werden, falls Firewalleinstellungen vorhanden sind.

### <a name="schema-configuration"></a>Schemakonfiguration

Wählen Sie die entsprechenden Felder aus, nachdem das Datenschema geladen wurde und so aussieht wie unten dargestellt.


|Auswahl  |Beschreibung  |Hinweise  |
|---------|---------|---------|
|**Timestamp**     | Der Zeitstempel eines Datenpunkts. Wenn kein Wert angegeben ist, wird von Metrics Advisor stattdessen der Zeitstempel der Erfassung des Datenpunkts verwendet. Pro Datenfeed kann maximal eine Spalte als Zeitstempel angegeben werden.        | Dies ist optional. Geben Sie maximal eine Spalte an.       |
|**Measure**     |  Die numerischen Werte im Datenfeed. Pro Datenfeed können mehrere Measures angegeben werden. Es muss jedoch mindestens eine Spalte als Measure ausgewählt werden.        | Geben Sie mindestens eine Spalte an.        |
|**Dimension**     | Enthält Kategoriewerte. Durch eine Kombination aus unterschiedlichen Werten wird eine bestimmte Zeitreihe mit einer einzelnen Dimension identifiziert (beispielsweise Land, Sprache, Mandant). Sie können beliebig viele Spalten (oder auch keine Spalten) als Dimensionen auswählen. Hinweis: Wenn Sie als Dimension eine Spalte auswählen, die keine Zeichenfolge enthält, müssen Sie darauf achten, dass es zu keiner Dimensionsexplosion kommt. | Dies ist optional.        |
|**Ignorieren**     | Die ausgewählte Spalte wird ignoriert.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Schemakonfiguration" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Einstellungen für automatisches Rollup

> [!IMPORTANT]
> Wenn Sie die **Ursachenanalyse** und andere Diagnosefunktionen aktivieren möchten, muss die Einstellung für automatisches Rollup konfiguriert werden. Nach der Aktivierung können die Einstellungen für automatisches Rollup nicht mehr geändert werden.

Metrics Advisor kann während der Erfassung für jede Dimension automatisch eine Aggregation durchführen (SUM, MAX, MIN...). Anschließend wird eine Hierarchie erstellt, die für die Ursachenanalyse und andere Diagnosefeatures verwendet wird. Ausführlichere Informationen finden Sie unter [Einstellungen für automatisches Rollup](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Geben Sie einen benutzerdefinierten Namen für den Datenfeed an. Dieser wird in Ihrem Arbeitsbereich angezeigt. Klicken Sie auf **Submit** (Senden). 

## <a name="tune-detection-configuration"></a>Optimieren der Erkennungskonfiguration

Nach dem Hinzufügen des Datenfeeds wird von Metrics Advisor versucht, Metrikdaten des angegebenen Startdatums zu erfassen. Es dauert etwas, bis die Daten vollständig erfasst wurden. Klicken Sie zum Anzeigen des Erfassungsstatus im oberen Bereich der Datenfeedseite auf **Ingestion progress** (Erfassungsstatus). Wenn Daten erfasst wurden, wendet Metrics Advisor die Erkennung an und überwacht die Quelle weiter auf neue Daten.

Klicken Sie bei angewendeter Erkennung auf eine der im Datenfeed aufgelisteten Metriken, um zur Seite **Metrikdetails** zu gelangen. Dort haben Sie folgende Möglichkeiten: 
- Anzeigen von Visualisierungen aller Zeitreihensegmente unter der betreffenden Metrik
- Aktualisieren der Erkennungskonfiguration, um die erwarteten Ergebnisse zu erhalten
- Einrichten von Benachrichtigungen für erkannte Anomalien

:::image type="content" source="../media/metric-details.png" alt-text="Metrikdetails" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Anzeigen der Diagnoseerkenntnisse

Nach dem Optimieren der Erkennungskonfiguration sollte es sich bei ggf. gefundenen Anomalien um tatsächliche Anomalien in Ihren Daten handeln. Metrics Advisor führt Analysen für mehrdimensionale Metriken durch. Hierzu zählen beispielsweise Anomalieclustering, Incidentkorrelation und Ursachenanalyse. Verwenden Sie diese Features, um Incidents in Ihren Daten zu analysieren und zu diagnostizieren.

Klicken Sie zum Anzeigen der Diagnoseerkenntnisse auf die roten Punkte in Zeitreihenvisualisierungen. Diese stellen erkannte Anomalien dar. Daraufhin wird ein Fenster mit einem Link zur Seite für die Incidentanalyse angezeigt. 

:::image type="content" source="../media/incident-link.png" alt-text="Incidentlink" lightbox="../media/incident-link.png":::

Durch Klicken auf diesen Link gelangen Sie zur Seite für die Incidentanalyse, auf der die entsprechende Anomalie analysiert und eine Reihe von Diagnoseerkenntnissen angezeigt wird. Im oberen Bereich finden Sie eine Statistik für den Incident. Diese liefert unter anderem Informationen zu **Schweregrad** und **beteiligten Anomalien** sowie zu **Startzeit** und **Endzeit**. 

Als Nächstes sehen Sie die Vorgängeranomalie des Incidents sowie einen automatisierten Ursachenratschlag. Zur Generierung dieses automatisierten Ursachenratschlags wird die Incidentstruktur aller verwandten Anomalien analysiert – einschließlich Abweichung, Verteilung und Anteil an den übergeordneten Anomalien. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Incidentdiagnose" lightbox="../media/incident-diagnostic.png":::

Anhand dieser Aspekte können Sie sich bereits einen Überblick über die Vorgänge sowie über die Auswirkungen des Incidents und die wahrscheinlichste Grundursache verschaffen. Dadurch können Sie umgehend Maßnahmen ergreifen, um den Incident schnellstmöglich aufzulösen. 

Sie können sich aber auch noch weitere Diagnoseerkenntnisse ansehen und zusätzliche Features nutzen, um Detailinformationen zu Anomalien nach Dimensionen zu erhalten, ähnliche Anomalien anzuzeigen und metrikübergreifende Vergleiche anzustellen. Weitere Informationen finden Sie unter [Diagnostizieren eines Incidents](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Erhalten einer Benachrichtigung, wenn neue Anomalien gefunden werden

Wenn Sie im Falle einer erkannten Anomalie in Ihren Daten eine Benachrichtigung erhalten möchten, können Sie ein Abonnement für einzelne oder mehrere Ihrer Metriken erstellen. Metrics Advisor verwendet Hooks, um Warnungen zu senden. Drei Arten von Hooks werden unterstützt: E-Mail-Hook, Webhook und Azure DevOps. Hier wird als Beispiel ein Webhook verwendet. 

### <a name="create-a-web-hook"></a>Erstellen eines Webhooks

Ein Webhook ist der Einstiegspunkt für die Anomalieerkennung durch eine programmgesteuerte Methode des Metrics Advisor-Diensts. Dadurch wird eine vom Benutzer bereitgestellte API aufgerufen, wenn eine Warnung ausgelöst wird. Ausführliche Informationen zur Erstellung von Hooks finden Sie im Abschnitt **Erstellen eines Hooks** der Anleitung [Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurieren der Warnungseinstellungen

Nach Erstellung eines Hooks wird durch eine Warnungseinstellung bestimmt, wie und welche Warnbenachrichtigungen gesendet werden. Pro Metrik können jeweils mehrere Warnungseinstellungen festgelegt werden. Zwei wichtige Einstellungen sind **Warnung für** zur Angabe der einzuschließenden Anomalien und **Filter anomaly options** (Anomalieoptionen filtern), um zu definieren, welche Anomalien in die Warnung eingeschlossen werden sollen. Ausführlichere Informationen finden Sie im Abschnitt **Hinzufügen oder Bearbeiten von Warnungseinstellungen** der Anleitung [Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook](../how-tos/alerts.md#add-or-edit-alert-settings).


## <a name="next-steps"></a>Nächste Schritte

- [Durchführen des Onboardings für Ihre Datenfeeds](../how-tos/onboard-your-data.md)
    - [Verwalten von Datenfeeds](../how-tos/manage-data-feeds.md)
    - [Konfigurationen für unterschiedliche Datenquellen](../data-feeds-from-different-sources.md)
- [Verwenden von REST-API oder Clientbibliotheken](./rest-api-and-client-library.md)
- [Konfigurieren von Metriken und Optimieren der Konfigurationserkennung](../how-tos/configure-metrics.md)