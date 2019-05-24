---
title: Grundlegendes zu Cloudyn-Kostenverwaltungsberichten in Azure | Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie Informationen zur grundlegenden Struktur und den Funktionen von Cloudyn-Kostenverwaltungsberichten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 91dc386641c758c42f64afff387d0463d1445f1d
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969082"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Grundlegendes zu Cloudyn-Kostenverwaltungsberichten

In diesem Artikel erhalten Sie Informationen zur grundlegenden Struktur und den Funktionen von Cloudyn-Kostenverwaltungsberichten. Die meisten Cloudyn-Berichte sind intuitiv und weisen ein einheitliches Erscheinungsbild auf. Nachdem Sie diesen Artikel gelesen haben, sind Sie zum Verwenden aller Kostenverwaltungsberichte bereit. In den verschiedenen Berichten sind zahlreiche Standardfunktionen zur problemlosen Navigation in den Berichten verfügbar. Berichte können angepasst werden, und Sie haben die Wahl zwischen mehreren Optionen zum Berechnen und Anzeigen von Ergebnissen.

## <a name="report-fields-and-options"></a>Felder und Optionen von Berichten

Hier sehen Sie ein Beispiel für den Bericht für die Kosten im Lauf der Zeit, Die meisten Cloudyn-Berichte sind ähnlich aufgebaut.

![Beispiel für den Bericht „Kosten im Zeitverlauf“ mit nummerierten Bereichen entsprechend den Beschreibungen](./media/understanding-cost-reports/sample-report.png)

Jeder nummerierte Bereich in der vorherigen Abbildung wird in den folgenden Informationen im Detail beschrieben:

1. **Datumsbereich**

    Verwenden Sie die Liste „Datumsbereich“, um ein voreingestelltes oder benutzerdefiniertes Berichtzeitintervall zu definieren.
2. **Gespeicherter Filter**

    Verwenden Sie die Liste „Gespeicherter Filter“, um die Gruppen und Filter zu speichern, die aktuell auf den Bericht angewendet werden. Gespeicherte Filter sind in Kosten- und Leistungsberichten verfügbar, einschließlich:

      - Kostenanalyse
      - Zuteilung
      - Ressourcenverwaltung
      - Optimierung

   Geben Sie einen Filternamen ein, und klicken Sie dann auf **Speichern**.

3. **Tags**

    Verwenden Sie den Bereich „Tags“, um nach Tag-Kategorien zu gruppieren. Bei den Tags im Menü handelt es sich um Tags für Azure-Abteilungen oder -Kostenstellen oder um die Entitäts- und Abonnement-Tags von Cloudyn. Wählen Sie Tags zum Filtern von Ergebnissen aus. Sie können auch einen Tag-Namen (Schlüsselwort) eingeben, um Ergebnisse zu filtern.

    ![Beispiel für eine Liste von Tags zum Filtern von Ergebnissen](./media/understanding-cost-reports/select-options.png)

    Klicken Sie auf **Hinzufügen**, um einen neuen Filter hinzuzufügen.

    ![Hinzufügen des Filterfelds mit Optionen und Bedingungen, nach denen gefiltert wird](./media/understanding-cost-reports/add-filter.png)

    Das Gruppieren oder Filtern bezieht sich nicht auf Azure-Ressourcen oder Ressourcengruppen-Tags.

    Tag-Gruppierung und -Filterung für Kostenzuteilung sind in der Menüoption **Gruppen** verfügbar.

4. **Gruppen in Berichten**

    Verwenden Sie Gruppen in Kostenanalyseberichten, um Standardkategorien aus Abrechnungsdaten in Ihrem Bericht einzeln aufzuführen.  Gruppen in Kostenzuteilungsberichten zeigen jedoch Tag-basierte Kategorien an. Tag-basierte Kategorien werden im Kostenzuteilungsmodell und in aufgeschlüsselten Standardkategorien aus Abrechnungsdaten definiert.

    ![Erste Beispielliste von Tags, nach denen Sie gruppieren können](./media/understanding-cost-reports/groups-tags01.png)

    ![Zweite Beispielliste von Tags, nach denen Sie gruppieren können](./media/understanding-cost-reports/groups-tags02.png)

    In Kostenzuteilungsberichten können Gruppen in Tag-basierten Gruppenkategorien Folgendes umfassen:
      - `Tags`
      - Ressourcengruppen-Tags
      - Kostenentitäts-Tags von Cloudyn
      - Abonnement-Tag-Kategorien für die Kostenzuteilung

   Beispiele:
   - Kostenstelle
   - Department
   - Anwendung
   - Environment
   - Kostencode

     Hier ist eine Liste mit den integrierten Gruppen des Berichts angegeben:

     - **Kostenart**
     - Wählen Sie eine, mehrere oder alle Kostenarten aus. Es gibt folgende Kostenarten:
       - Einmalige Gebühr
       - Support
       - Nutzungskosten
     - **Kunde**
       - Wählen Sie einen bestimmten, mehrere oder alle Kunden aus.
     - **Kontoname**
       - Der Name des Kontos oder des Abonnements. In Azure ist dies der Name des Azure-Abonnements.
     - **Kontonummer**
       - Wählen Sie ein, mehrere oder alle Konten aus. In Azure ist dies die GUID des Azure-Abonnements.
     - **Übergeordnetes Konto**
       - Wählen Sie ein, mehrere oder alle übergeordneten Konten aus.
     - **Service**
       - Wählen Sie einen, mehrere oder alle Dienste aus.
     - **Anbieter**
       - Der Cloudanbieter, dem Objekte und Ausgaben zugeordnet sind.
     - **Region**
       - Die Region, in der die Ressource gehostet wird.
     - **Verfügbarkeitszone**
       - Die Standorte mit AWS-Isolation in einer Region.
     - **Ressourcentyp**
       - Der Typ der verwendeten Ressource.
     - **Untertyp**
       - Wählen Sie den Untertyp aus.
     - **Vorgang**
       - Wählen Sie den Vorgang oder die Option **Alle anzeigen**.
     - **Preismodell**
       - Alles im Voraus
       - Keine Vorauszahlung
       - Teilweise im Voraus
       - On Demand
       - Reservierung
       - Sofortige Zahlung
     - **Gebührentyp**
       - Wählen Sie negative, positive oder beide Gebührentypen aus.
     - **Mandant**
       - Gibt an, ob ein Computer als dedizierter Computer ausgeführt wird.
     - **Verwendungstyp**
       - Der Verwendungstyp kann auf einmalige Gebühr oder wiederkehrende Gebühren festgelegt werden.

5. **Filter**

    Verwenden Sie Einfach- oder Mehrfachauswahlfilter, um Bereiche für ausgewählte Werte festzulegen. Zum Festlegen eines Filters klicken Sie auf **Hinzufügen** und wählen dann Filterkategorien und -werte aus.

6. **Kostenmodell**

    Verwenden Sie „Kostenmodell“ zum Auswählen eines Kostenmodells, das Sie zuvor mit „Kostenzuteilung 360“ erstellt haben. Je nach Ihren Kostenzuteilungsanforderungen können Sie über mehrere Cloudyn-Kostenmodelle verfügen. Die Kostenzuteilungsanforderungen Ihrer einzelnen Organisationsteams können unterschiedlich sein. Jedes Team kann ein eigenes dediziertes Kostenmodell nutzen.

    Informationen zum Erstellen einer Modelldefinition für Kostenzuteilung finden Sie unter [Verwenden von benutzerdefinierten Tags zum Zuteilen von Kosten](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortisierung**

    Verwenden Sie „Amortisierung“ in Kostenzuteilungsberichten zum Anzeigen von nicht nutzungsbasierten Dienstgebühren oder einmalig zu zahlenden Kosten und zur gleichmäßigen Verteilung dieser Kosten während ihrer Lebensdauer. Beispiele für die einmalige Gebühren:
    - Jährliche Supportgebühren
    - Jährliche Gebühren für Sicherheitskomponenten
    - Gebühren für den Kauf von reservierten Instanzen
    - Einige Azure Marketplace-Elemente

   Wählen Sie unter Amortisierung die Option **Amortized cost** (Amortisierte Kosten) oder **Actual Cost** (Istkosten) aus.

8. **Lösung**

    Verwenden Sie „Lösung“, um die Zeitauflösung innerhalb des ausgewählten Datumsbereichs auszuwählen. Ihre Zeitauflösung legt fest, wie Einheiten im Bericht angezeigt werden. Folgende Optionen sind verfügbar:
    - Täglich
    - Wöchentlich
    - Monatlich
    - Vierteljährlich
    - Jährlich

9. **Zuteilungsregeln**

    Verwenden Sie Zuteilungsregeln, um die Kostenneuberechnung für die Kostenzuteilung anzuwenden oder zu deaktivieren. Sie können die Neuberechnung der Kostenzuteilung für Abrechnungsdaten aktivieren oder deaktivieren. Die Neuberechnung wird für die ausgewählten Kategorien im Bericht durchgeführt. Sie ermöglicht Ihnen das Bewerten der Auswirkung einer Neuberechnung der Kostenzuteilung für Abrechnungsrohdaten.

10. **Nicht kategorisiert**

    Verwenden Sie „Nicht kategorisiert“, um nicht kategorisierte Kosten in den Bericht einzubeziehen bzw. davon auszuschließen.

11. **Felder anzeigen/ausblenden**

    Die Option zum Anzeigen/Ausblenden Option hat keine Auswirkung in Berichten.

12. **Anzeigeformate**

    Verwenden Sie „Anzeigeformate“, um verschiedene Diagramm- oder Tabellenansichten auszuwählen.

    ![Symbole der Anzeigeformate, die Sie auswählen können](./media/understanding-cost-reports/display-formats.png)

13. **Mehrfarbig**

    Verwenden Sie „Mehrfarbig“, um in Ihrem Bericht die Farbe von Diagrammen festzulegen.

14. **Aktionen**

    Verwenden Sie „Aktionen“ zum Speichern, Exportieren oder Planen des Berichts.

15. **Richtlinie**

    Einige Berichte enthalten eine Richtlinie zur Berechnung der voraussichtlichen geplanten Kosten, die jedoch nicht abgebildet sind. In diesen Berichten zeigt die Richtlinie **Konsolidiert** Empfehlungen für alle Konten und Abonnements unter der aktuellen Entität wie z. B. Microsoft-Registrierung oder AWS-Zahlender an. Die Richtlinie **Eigenständig** zeigt Empfehlungen für ein Konto oder ein Abonnement an, als ob keine anderen Abonnements vorhanden wären. Die Richtlinie, die Sie auswählen, hängt von der Optimierungsstrategie Ihrer Organisation ab. Kostenprojektionen basieren auf den letzten 30 Tagen der Nutzung.

## <a name="save-and-schedule-reports"></a>Speichern und Planen von Berichten

Nachdem Sie einen Bericht erstellt haben, können Sie ihn zur späteren Verwendung speichern. Gespeicherte Berichte stehen unter **Meine Tools** > **Meine Berichte** zur Verfügung. Wenn Sie Änderungen an einen vorhandenen Bericht vornehmen und speichern, wird der Bericht als neue Version gespeichert. Sie können ihn aber auch als neuen Bericht speichern.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Speichern eines Berichts im Cloudyn-Portal

Klicken Sie in einem geöffneten Bericht auf **Aktionen**, und wählen Sie dann **Save to my reports** (Unter „Meine Berichte“ speichern) aus. Geben Sie dem Bericht einen Namen, und fügen Sie dann entweder Ihre eigene URL hinzu, oder verwenden Sie die automatisch erstellte URL. Sie haben die Option, den Bericht öffentlich für andere Personen Ihrer Organisation oder Ihre Entität **freizugeben**. Wenn Sie den Bericht nicht freigeben, bleibt er ein persönlicher Bericht, den nur Sie anzeigen können. Speichern Sie den Bericht.


### <a name="save-a-report-to-cloud-provider-storage"></a>Speichern eines Berichts im Speicher eines Cloudanbieters

Zum Speichern eines Berichts bei Ihrem Cloud-Dienstanbieter müssen Sie bereits ein Speicherkonto konfiguriert haben. Klicken Sie in einem geöffneten Bericht auf **Aktionen**, und wählen Sie dann **Schedule report** (Bericht planen) aus. Geben Sie dem Bericht einen Namen, und fügen Sie dann entweder Ihre eigene URL hinzu, oder verwenden Sie die automatisch erstellte URL. Wählen Sie **Save to storage** (In Speicher speichern), und wählen Sie dann das Speicherkonto aus, oder fügen Sie ein neues hinzu. Geben Sie ein Präfix ein, das an den Namen der Berichtdatei angefügt wird. Wählen Sie CSV oder JSON als Dateiformat aus, und speichern Sie dann den Bericht.

### <a name="schedule-a-report"></a>Planen eines Berichts

Sie können Sie Berichte in bestimmten Zeitabständen ausführen und diese an eine Empfängerliste oder das Speicherkonto eines Cloud-Dienstanbieters senden. Klicken Sie in einem geöffneten Bericht auf **Aktionen**, und wählen Sie dann **Schedule report** (Bericht planen) aus. Sie können den Bericht per E-Mail senden und in einem Speicherkonto speichern. Wählen Sie unter **Zeitplan** das Intervall (täglich, wöchentlich oder monatlich) aus. Wählen Sie für wöchentliche und monatliche Intervalle den Tag oder die Daten für die Übermittlung sowie die Uhrzeit aus. Speichern Sie den geplanten Bericht. Wenn Sie das Excel-Berichtformat auswählen, wird der Bericht als Anlage gesendet. Wenn Sie E-Mail-Inhalt als Format auswählen, werden im Diagrammformat angezeigte Ergebnisse als Graph übermittelt.

### <a name="export-a-report-as-a-csv-file"></a>Exportieren eines Berichts als CSV-Datei

Klicken Sie in einem geöffneten Bericht auf **Aktionen**, und wählen Sie dann **Export all report data** (Alle Berichtsdaten exportieren) aus. Ein Popupfenster wird angezeigt, und eine CSV-Datei wird heruntergeladen.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Verwenden der Cloudyn-Berichte](use-reports.md), um mehr über die in Cloudyn enthaltenen Berichte zu erfahren.
- Erfahren Sie, wie Sie Berichte zum Erstellen von [Dashboards](dashboards.md) verwenden.
