---
title: Überwachen von Azure Storage-Diensten mit Azure Monitor für Storage | Microsoft-Dokumentation
description: In diesem Artikel wird das Azure Monitor für Storage-Feature beschrieben, das Speicheradministratoren ein schnelles Verständnis der Leistungs- und Nutzungsprobleme bei ihren Azure Storage-Konten ermöglicht.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 2538bc91be59ff12b39ee3f5f629e4c016480a97
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296334"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Überwachen Ihres Speicherdiensts mit Azure Monitor für Storage

Azure Monitor für Storage ermöglicht eine umfassende Überwachung Ihrer Azure Storage-Konten, indem eine einheitliche Ansicht der Leistung, Kapazität und Verfügbarkeit Ihrer Azure Storage-Dienste bereitgestellt wird. Sie können die Speicherkapazität und Leistung auf zwei Arten beobachten: direktes Anzeigen in einem Speicherkonto oder Anzeigen verschiedener Gruppen von Speicherkonten in Azure Monitor. 

Dieser Artikel soll Ihnen verwertbares Wissen zur Oberfläche von Azure Monitor für Storage liefern und zeigen, was sie im Hinblick auf Integrität und Leistung von Storage-Konten im gewünschten Umfang bietet, wobei Sie sich auf Hotspots sowie die Diagnose von Wartezeit, Drosselung und Verfügbarkeitsproblemen konzentrieren können.

## <a name="introduction-to-azure-monitor-for-storage"></a>Einführung in Azure Monitor für Storage

Bevor Sie sich mit der Oberfläche genauer befassen, sollten Sie nachvollziehen können, wie sie Informationen darstellt und visualisiert. Unabhängig davon, ob Sie das Storage-Feature direkt aus einem Speicherkonto oder in Azure Monitor auswählen, stellt Azure Monitor für Storage eine einheitliche Oberfläche bereit. 

In Kombination bietet sie Folgendes:

* **Perspektive im gewünschten Umfang** mit einer Momentaufnahme von deren Verfügbarkeit, die auf Folgendem basiert: der Integrität des Speicherdiensts oder des API-Vorgangs, der Nutzung mit der Gesamtzahl der vom Speicherdienst empfangenen Anforderungen und der Latenz mit der durchschnittlichen Zeit, die der Speicherdienst oder der API-Vorgangstyp für die Verarbeitung von Anforderungen benötigt. Sie können auch die Kapazität nach Blob, Datei, Tabelle und Warteschlange anzeigen.

* **Drilldownanalyse** für ein bestimmtes Speicherkonto als Hilfe bei der Diagnose von Problemen oder der Ausführung einer detaillierten Analyse nach Kategorie – Verfügbarkeit, Leistung, Fehler und Kapazität. Wenn Sie eine dieser Optionen auswählen, erhalten Sie eine detaillierte Übersicht über die Metriken.  

* **Anpassbar**. Hier können Sie ändern, welche Metriken Sie anzeigen oder abändern oder aber Schwellenwerte dafür festlegen möchten, die Ihren Grenzwerten entsprechen, und sie als Ihre eigene Arbeitsmappe speichern. Diagramme in der Arbeitsmappe können an das Azure-Dashboard angeheftet werden.  

Bei diesem Feature müssen Sie nichts aktivieren oder konfigurieren, weil die Speichermetriken aus Ihren Speicherkonten standardmäßig erfasst werden. Wenn Sie mit den in Azure Storage verfügbaren Metriken nicht vertraut sind, können Sie unter [Azure Storage-Metriken](../../storage/blobs/monitor-blob-storage.md) die jeweilige Beschreibung und Definition anzeigen.

>[!NOTE]
>Für den Zugriff auf dieses Feature fallen keine Gebühren an. Ihnen werden nur die grundlegenden Features von Azure Monitor in Rechnung gestellt, die Sie entsprechend der Beschreibung auf der Seite [Azure Monitor-Preisdetails](https://azure.microsoft.com/pricing/details/monitor/) konfigurieren oder aktivieren.


## <a name="view-from-azure-monitor"></a>Anzeigen in Azure Monitor

In Azure Monitor können Sie Details zu Transaktion, Latenz und Kapazität aus mehreren Speicherkonten in Ihrem Abonnement anzeigen sowie Leistung, Kapazitätsprobleme und Fehler leichter identifizieren.

Führen Sie die folgenden Schritte aus, um die Nutzung und Verfügbarkeit Ihrer Speicherkonten in allen ihren Abonnements anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im linken Bereich des Azure-Portals **Überwachen** und im Abschnitt **Insights** die Option **Speicherkonten** aus.

    ![Ansicht mehrerer Speicherkonten](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Arbeitsmappe „Übersicht“

In der Arbeitsmappe **Übersicht** für das ausgewählte Abonnement werden in der Tabelle interaktive Speichermetriken und der Dienstverfügbarkeitsstatus für bis zu 5 Speicherkonten angezeigt, die innerhalb des Abonnements gruppiert sind. Sie können die Ergebnisse anhand der Optionen filtern, die Sie in den folgenden Dropdownlisten auswählen:

* **Abonnements** – Es werden nur Abonnements mit Speicherkonten aufgelistet.  

* **Speicherkonten** – standardmäßig sind 5 Speicherkonten vorab ausgewählt. Wenn Sie in der Bereichsauswahl alle oder mehrere Speicherkonten auswählen, werden bis zu 200 Speicherkonten zurückgegeben. Wenn Sie beispielsweise insgesamt 573 Speicherkonten in drei ausgewählten Abonnements haben, werden nur 200 Konten angezeigt. 

* **Zeitbereich** – Standardmäßig werden die Informationen aus den letzten 4 Stunden basierend auf der entsprechenden Auswahl angezeigt.

Die Zählerkachel unter den Dropdownlisten führt ein Rollup der Gesamtanzahl der Speicherkonten im Abonnement durch und gibt an, wie viele davon ausgewählt sind. Für Spalten in der Arbeitsmappe, die Transaktionsmetriken oder-Fehler melden, gibt es eine bedingte Farbcodierung oder Wärmebilder. Die dunkelste Farbe hat den höchsten Wert, und eine hellere Farbe basiert auf den niedrigsten Werten. Der Wert für die fehlerbasierten Spalten ist „rot“, und für die metrikbasierten Spalten ist er „blau“.

Wählen Sie einen Wert in den Spalten **Verfügbarkeit**, **E2E-Latenz**, **Serverlatenz** und **Transaktionsfehlertyp/Fehler** aus. Dann werden Sie zu einem Bericht weitergeleitet, der auf den spezifischen Typ von Speichermetriken zugeschnitten ist, die mit der für dieses Speicherkonto ausgewählten Spalte übereinstimmen. Weitere Informationen zu den Arbeitsmappen für die einzelnen Kategorien finden Sie weiter unten im Abschnitt [Detaillierte Speicherarbeitsmappen](#detailed-storage-workbooks). 

>[!NOTE]
>Ausführliche Informationen dazu, welche Fehler im Bericht angezeigt werden können, finden Sie unter [Response Type schema](../../storage/blobs/monitor-blob-storage-reference.md#metrics-dimensions) (Schema der Antworttypen). Suchen Sie dort nach Antworttypen wie **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Abhängig von den ausgewählten Speicherkonten werden alle anderen Fehler unter der Kategorie **Andere** dargestellt, wenn mehr als drei Arten von Fehlern gemeldet werden.

Der Standardschwellenwert für **Verfügbarkeit** lautet:

* Warnung – 99%
* Kritisch – 90%

Wenn Sie einen Verfügbarkeitsschwellenwert basierend auf den Ergebnissen Ihrer Beobachtung oder Anforderungen festlegen möchten, lesen Sie [Ändern des Verfügbarkeitsschwellenwerts](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Arbeitsmappe „Kapazität“

Wählen Sie oben auf der Seite die Option **Kapazität** aus. Dann wird die Arbeitsmappe **Kapazität** geöffnet. Sie zeigt Ihnen die Menge des im Konto genutzten gesamten Speichers und die Kapazität, die von den einzelnen Datendiensten im Konto genutzt wird. So können Sie zu stark und zu wenig genutzten Speicherplatz einfacher ermitteln.

![Arbeitsmappe „Kapazität“ für mehrere Speicherkonten](./media/storage-insights-overview/storage-account-capacity-02.png) 

Für Spalten in der Arbeitsmappe, die Kapazitätsmetriken mit einem blauen Wert melden, gibt es eine bedingte Farbcodierung oder Wärmebilder. Die dunkelste Farbe hat den höchsten Wert, und eine hellere Farbe basiert auf den niedrigsten Werten.

Wenn Sie in einer der Spalten der Arbeitsmappe einen Wert auswählen, führen Sie einen Drilldown zur Arbeitsmappe **Kapazität** für das Speicherkonto aus. Weitere Details zum Drilldownbericht werden weiter unten im Abschnitt [Detaillierte Speicherarbeitsmappen](#detailed-storage-workbooks) beschrieben. 

## <a name="view-from-a-storage-account"></a>Anzeigen über ein Speicherkonto

Gehen Sie wie folgt vor, um direkt über ein Speicherkonto auf Azure Monitor für VMs zuzugreifen:

1. Wählen Sie im Azure-Portal „Speicherkonten“ aus.

2. Wählen Sie in der Liste ein Speicherkonto aus. Wählen Sie im Abschnitt „Überwachung“ die Option „Insights“ aus.

    ![Screenshot der Arbeitsmappenseite „Übersicht“ für das Speicherkonto](./media/storage-insights-overview/storage-account-direct-overview-01.png)

In der Arbeitsmappe **Übersicht** für das Speicherkonto werden mehrere Leistungsmetriken für die Speicherung angezeigt, anhand derer Sie schnell Folgendes bewerten können:

* Integrität des Speicherdiensts, um sofort festzustellen, ob ein Problem außerhalb ihrer Kontrolle den Speicherdienst in der Region beeinträchtigt, in der er bereitgestellt wird. Dies wird in der Spalte **Zusammenfassung** angegeben.

* Interaktive Leistungsdiagramme, die die wichtigsten Details im Zusammenhang mit Speicherkapazität, Verfügbarkeit, Transaktionen und Latenz zeigen.  

* Metriken und Statuskacheln mit hervorgehobener Dienstverfügbarkeit, Gesamtzahl der Transaktionen für den Speicherdienst, E2E-Latenz und Serverlatenz.

Wenn Sie eine der Schaltflächen für **Fehler**, **Leistung**, **Verfügbarkeit** und **Kapazität** auswählen, wird die jeweilige Arbeitsmappe geöffnet. 

![Seite „Übersicht“ für das ausgewählte Speicherkonto](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Detaillierte Speicherarbeitsmappen

Abhängig davon, ob Sie einen Wert in den Spalten **Verfügbarkeit**, **E2E-Latenz**, **Serverlatenz** und **Transaktionsfehlertyp/Fehler** in der Arbeitsmappe **Übersicht** für mehrere Speicherkonten ausgewählt haben oder eine der Schaltflächen für **Fehler**, **Leistung**, **Verfügbarkeit** und **Kapazität** in der Arbeitsmappe **Übersicht** eines bestimmten Speicherkontos auswählen, liefert jede dieser Möglichkeiten eine Reihe von interaktiven speicherbezogenen Informationen, die auf die jeweilige Kategorie zugeschnitten sind.  

* **Verfügbarkeit** öffnet die Arbeitsmappe **Verfügbarkeit**. Sie zeigt den aktuellen Integritätsstatus des Azure Storage-Diensts, eine Tabelle mit dem verfügbaren Integritätsstatus jedes Objekts – kategorisiert nach dem im Speicherkonto definierten Datendienst – mit einer Trendlinie, die den ausgewählten Zeitbereich darstellt, und einem Verfügbarkeitstrenddiagramm für die einzelnen Datendienste im Konto.  

    ![Beispiel für Verfügbarkeitsbericht](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E-Latenz** und **Serverlatenz** öffnet die Arbeitsmappe **Leistung**. Sie enthält eine Rollupstatuskachel mit E2E-Latenz und Serverlatenz, ein Leistungsdiagramm von E2E-Latenz im Vergleich zur Serverlatenz und eine Tabelle, in der die Latenz von erfolgreichen Aufrufen durch die API aufgeschlüsselt wird, kategorisiert nach dem im Speicherkonto definierten Datendienst.

    ![Beispiel für einen Leistungsbericht](./media/storage-insights-overview/storage-account-performance-01.png)

* Wenn Sie eine der im Raster aufgelisteten Fehlerkategorien auswählen, wird die Arbeitsmappe **Fehler** geöffnet. Der Bericht zeigt Folgendes an: Metrikkacheln für alle anderen clientseitigen Fehler (mit Ausnahme von beschriebenen und erfolgreichen Anforderungen), Clientdrosselungsfehler, ein Leistungsdiagramm für den **Antworttyp** der Transaktion, Dimensionsmetrik, die für das Attribut „ClientOtherError“ spezifisch ist, sowie zwei Tabellen – **Transaktionen nach API-Name** und **Transaktionen nach Antworttyp**.

   ![Beispiel für einen Fehlerbericht](./media/storage-insights-overview/storage-account-failures-01.png)

* **Kapazität** öffnet die Arbeitsmappe **Kapazität**. Sie zeigt die Gesamtmenge des Speichers, der für die einzelnen Speicherdatenobjekte im Konto in den Kacheln und im Diagramm verwendet wird, sowie die Anzahl der Datenobjekte, die im Konto gespeichert sind.  

    ![Ausgewählte Seite „Kapazität“ für das Speicherkonto](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Anheften und exportieren

Sie können jeden beliebigen der Metrikabschnitte an ein Azure-Dashboard anheften, indem Sie oben rechts im Abschnitt das Stecknadelsymbol auswählen.

![Beispiel für das Anheften eines Metrikabschnitts an das Dashboard](./media/storage-insights-overview/workbook-pin-example.png)

Die Arbeitsmappen **Übersicht** oder **Kapazität** für Mehrfachabonnement und Speicherkonto unterstützen den Export der Ergebnisse im Excel-Format. Dazu müssen Sie den Abwärtspfeil rechts neben dem Stecknadelsymbol auswählen.

![Beispiel für das Exportieren von Ergebnissen aus dem Raster einer Arbeitsmappe](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Anpassen von Azure Monitor für Storage

In diesem Abschnitt werden gängige Szenarien für die Bearbeitung der Arbeitsmappe erläutert, um sie zur Unterstützung Ihrer Datenanalyseanforderungen anzupassen:

* Arbeitsmappe so festlegen, dass immer ein bestimmtes Abonnement oder ein bestimmtes Speicherkonto/bestimmte Speicherkonten ausgewählt wird/werden
* Ändern von Metriken im Raster
* Ändern des Verfügbarkeitsschwellenwerts
* Ändern des Farbrendering

Die Anpassungen werden in einer benutzerdefinierten Arbeitsmappe gespeichert, um zu verhindern, dass die Standardkonfiguration in unserer veröffentlichten Arbeitsmappe überschrieben wird. Arbeitsmappen werden in einer Ressourcengruppe gespeichert, und zwar entweder im für Sie privaten Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte**, der für alle Benutzer mit Zugriff auf die Ressourcengruppe zugänglich ist. Nachdem Sie die benutzerdefinierte Arbeitsmappe gespeichert haben, müssen Sie zum Arbeitsmappenkatalog wechseln, um die Mappe zu starten.

![Starten des Arbeitsmappenkatalogs über die Befehlsleiste](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Angeben eines Abonnements oder Speicherkontos

Sie können die Arbeitsmappen **Übersicht** oder **Kapazität** für Mehrfachabonnement und Speicherkonto bei jeder Ausführung auf den Bereich eines bestimmten Abonnements/von bestimmten Abonnements oder eines bestimmten Speicherkontos/von bestimmten Speicherkonten konfigurieren. Führen Sie dazu die folgenden Schritte aus:

1. Wählen Sie im Portal **Überwachen** und dann im linken Bereich **Speicherkonten** aus.

2. Wählen Sie in der Arbeitsmappe **Übersicht** in der Befehlsleiste den Befehl **Bearbeiten** aus.

3. Wählen Sie in der Dropdownliste **Abonnements** mindestens ein Abonnement aus, das standardmäßig ausgewählt werden soll. Beachten Sie, dass die Arbeitsmappe die Auswahl von bis zu 10 Abonnements unterstützt.  

4. Wählen Sie in der Dropdownliste **Speicherkonten** mindestens ein Konto aus, das standardmäßig ausgewählt werden soll. Beachten Sie, dass die Arbeitsmappe die Auswahl von bis zu insgesamt 200 Speicherkonten unterstützt. 

5. Wählen Sie in der Befehlsleiste den Befehl **Speichern unter** aus, um eine Kopie der Arbeitsmappe mit Ihren Anpassungen zu speichern. Klicken Sie dann auf **Bearbeitung abgeschlossen**, um zum Lesemodus zurückzukehren.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Ändern von Metriken und Farben in der Arbeitsmappe

Die vorkonfigurierten Arbeitsmappen enthalten Metrikdaten, und Sie haben die Möglichkeit, jede der Visualisierungen zu ändern oder zu entfernen und sie an die spezifischen Anforderungen Ihres Teams anzupassen.

In unserem Beispiel arbeiten wir mit der Arbeitsmappe „Kapazität“ für Mehrfachabonnement und Speicherkonto, um Folgendes zu veranschaulichen:

* Entfernen einer Metrik
* Ändern des Farbrendering

Sie können bei jeder der vordefinierten Arbeitsmappen **Fehler**, **Leistung**, **Verfügbarkeit** und **Kapazität** dieselben Änderungen ausführen.

1. Wählen Sie im Portal **Überwachen** und dann im linken Bereich **Speicherkonten** aus.

2. Wählen Sie **Kapazität** aus, um zur Arbeitsmappe „Kapazität“ zu wechseln, und wählen Sie in der Befehlsleiste den Befehl **Bearbeiten** aus.

    ![„Bearbeiten“ auswählen, um eine Arbeitsmappe zu ändern](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Wählen Sie neben dem Abschnitt „Metriken“ **Bearbeiten** aus.

    ![„Bearbeiten“ auswählen, um die Metriken für die Arbeitsmappe „Kapazität“ zu ändern](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Weil die Spalte **Zeitskala für verwendete Kontokapazität** entfernt werden soll, wählen Sie im Metrikraster **Spalteneinstellungen** aus.

    ![Bearbeiten von Spalteneinstellungen](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Wählen Sie im Bereich **Spalteneinstellungen bearbeiten** im Abschnitt **Spalten** die Option **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Verwendete Kontokapazität Timeline$** und dann in der Dropdownliste **Spaltenrenderer** den Eintrag **Ausgeblendet** aus.

6. Wählen Sie **Speichern und schließen** aus, um Ihre Änderung zu committen.

Nun ändern wir das Farbdesign für die Kapazitätsmetriken im Bericht so, dass Grün statt Blau verwendet wird.

1. Wählen Sie im Metrikraster **Spalteneinstellungen** aus.

2. Wählen Sie im Bereich **Spalteneinstellungen bearbeiten** im Abschnitt **Spalten** die Option **microsoft.storage/storageaccounts-Capacity-UsedCapacity$`|`microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$`|`microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$`|`microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$`|`microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$** aus. Wählen Sie in der Dropdownliste **Farbpalette** den Eintrag **Grün** aus.

3. Wählen Sie **Speichern und schließen** aus, um Ihre Änderung zu committen.

4. Wählen Sie in der Befehlsleiste den Befehl **Speichern unter** aus, um eine Kopie der Arbeitsmappe mit Ihren Anpassungen zu speichern. Klicken Sie dann auf **Bearbeitung abgeschlossen**, um zum Lesemodus zurückzukehren.  

### <a name="modify-the-availability-threshold"></a>Ändern des Verfügbarkeitsschwellenwerts

In diesem Beispiel arbeiten wir mit der Arbeitsmappe „Kapazität“ des Speicherkontos und zeigen, wie der Verfügbarkeitsschwellenwert geändert wird. Standardmäßig werden die Kachel und das Raster, die bzw. das die prozentuale Verfügbarkeit meldet, mit einem minimalen Schwellenwert von „90“ und einem maximalen Schwellenwert von „99“ konfiguriert. Wir werden den minimalen Schwellenwert für **Verfügbarkeit %** im Raster **Verfügbarkeit nach API-Name** auf „85%“ ändern. Dies bedeutet, dass sich der Integritätszustand in „kritisch“ ändert, wenn der Schwellenwert kleiner als 85 Prozent ist. 

1. Wählen Sie im Portal **Speicherkonten** und dann ein Speicherkonto aus der Liste aus.

2. Wählen Sie im linken Bereich **Insights** aus.

3. Wählen Sie in der Arbeitsmappe **Verfügbarkeit** aus, um zur Arbeitsmappe „Verfügbarkeit“ zu wechseln. Wählen Sie dann in der Befehlsleiste den Befehl **Bearbeiten** aus. 

4. Scrollen Sie nach unten bis zum unteren Seitenrand, und wählen Sie links neben dem Raster **Verfügbarkeit nach API** die Option **Bearbeiten** aus.

    ![Rastereinstellungen „Verfügbarkeit nach API-Name“ bearbeiten](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Wählen Sie **Spalteneinstellungen** und dann im Bereich **Spalteneinstellungen bearbeiten** im Abschnitt **Spalten** die Option **Verfügbarkeit (%) (Schwellenwerte + Formatiert)** aus.

6. Ändern Sie den Wert für den Integritätsstatus **Kritisch** von **90** auf **85**. Klicken Sie dann auf **Speichern und schließen**.

    ![Den Verfügbarkeitsschwellenwert für den Status „Kritisch“ ändern](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Wählen Sie in der Befehlsleiste den Befehl **Speichern unter** aus, um eine Kopie der Arbeitsmappe mit Ihren Anpassungen zu speichern. Klicken Sie dann auf **Bearbeitung abgeschlossen**, um zum Lesemodus zurückzukehren.

## <a name="troubleshooting"></a>Problembehandlung

Allgemeine Anleitungen zur Problembehandlung finden Sie im Artikel zur [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](troubleshoot-workbooks.md).

Dieser Abschnitt wird Sie bei der Diagnose und Behandlung einiger bekannter Probleme unterstützen, die bei der Verwendung von Azure Monitor für Storage auftreten können. In der Liste unten finden Sie die für Ihre spezifische Fragestellung relevanten Informationen.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Beheben von Leistungs-, Kapazitäts-oder Verfügbarkeitsproblemen

Informationen zur Problembehandlung bei speicherbezogenen Problemen, die Sie im Zusammenhang mit Azure Monitor für Storage ermitteln, finden Sie in den [Anleitungen zur Problembehandlung](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) für Azure Storage.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Warum kann ich nur 200 Speicherkonten sehen?

Die Anzahl der ausgewählten Speicherkonten ist auf 200 begrenzt, unabhängig von der Anzahl der ausgewählten Abonnements.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Wie ändert man die Farbgebung und den Schwellenwert für die Verfügbarkeit?

Im Abschnitt [Ändern des Verfügbarkeitsschwellenwerts](#modify-the-availability-threshold) finden Sie detaillierte Schritte zum Ändern der Farbgebung und der Verfügbarkeitsschwellenwerte.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Wie können Sie die Daten analysieren und Probleme behandeln, die in Azure Monitor für Storage angezeigt werden?

 Weitere Informationen zur Analyse und Problembehandlung der im Azure Monitor für Storage angezeigten Azure Storage-Daten finden Sie im Artikel [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Warum werden nicht alle Fehlertypen in Metriken angezeigt?

Derzeit werden bis zu drei verschiedene Fehlertypen angezeigt und der Rest der Fehler in einem einzigen Bucket zusammengefasst. Dieses wird über splitByLimit gesteuert und kann geändert werden. Zum Ändern der Eigenschaft:

1. Klicken Sie auf „Edit workbook“ (Arbeitsmappe bearbeiten).
2. Navigieren Sie zu „metrics“ (Metriken), klicken Sie auf „edit“ (Bearbeiten), und wählen Sie **Transactions, Sum** (Transaktionen, Summe) oder andere beliebige Metriken aus, die Sie bearbeiten möchten.

    ![Navigieren zu „metrics“ (Metriken), Klicken auf „edit“ (Bearbeiten) und „Transactions, Sums“ (Transaktionen, Summen)](./media/storage-insights-overview/fqa7.png)

3. Ändern Sie dann die Anzahl der Aufteilungen.

    ![Auswählen von „Metrikparameter“](./media/storage-insights-overview/fqa7-2.png)

Wenn Sie n verschiedene Fehlertypen anzeigen möchten, geben Sie splitByLimit als n+1 an. 1 steht hier für die zusätzlichen, restlichen Fehler.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Ich habe meine Arbeitsmappe in einem Speicherkonto gespeichert. Warum kann ich die Arbeitsmappe nicht mehr finden?

Jede Arbeitsmappe wird in dem Speicherkonto gespeichert, in der Sie diese gespeichert haben. Versuchen Sie das bestimmte Speicherkonto zu finden, in dem der Benutzer die Arbeitsmappe gespeichert hat. Andernfalls gibt es keine Möglichkeit, eine bestimmte Arbeitsmappe zu finden, ohne die Ressource (Speicherkonto) zu kennen.

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren Sie [Metrikwarnungen](../platform/alerts-metric.md) und [Dienstintegritätsbenachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md), um automatisierte Warnungen einzurichten, die beim Erkennen von Problemen hilfreich sein können.

* Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../platform/workbooks-overview.md) über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können, und vieles mehr.

* Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
