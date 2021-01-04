---
title: Rechnungen für die Azure Enterprise-Registrierung
description: In diesem Artikel wird erläutert, wie Sie Ihre Azure-Unternehmensrechnung verwalten und Aktionen dafür ausführen.
author: bandersmsft
ms.author: banders
ms.date: 09/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: 57ada2ca760ffff2124582c31fdd095d04d4375f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410952"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Rechnungen für die Azure Enterprise-Registrierung

In diesem Artikel wird erläutert, wie Sie Ihre Azure Enterprise Agreement-Rechnung (Azure EA) verwalten und Aktionen dafür ausführen. Die Rechnung stellt Ihre Abrechnung dar. Überprüfen Sie die Richtigkeit. Machen Sie sich außerdem auch mit anderen Aufgaben vertraut, die ggf. zum Verwalten Ihrer Rechnung erforderlich sind.

## <a name="view-usage-summary-and-download-reports"></a>Anzeigen der Verwendungszusammenfassung und Herunterladen von Berichten

Unternehmensadministratoren können eine Zusammenfassung ihrer Nutzungsdaten, die in Anspruch genommene Azure-Vorauszahlung und die Gebühren für die zusätzliche Nutzung im Azure Enterprise Portal anzeigen. Die Gebühren werden auf der Übersichtsebene für alle Konten und Abonnements angezeigt.

Laden Sie den Bericht mit den Nutzungsdetails herunter, um die ausführliche Nutzung für bestimmte Konten anzuzeigen:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Wählen Sie **Berichte** aus.
1. Wählen Sie die Registerkarte **Nutzung herunterladen** aus.
1. Klicken Sie in der Liste der Berichte für den monatlichen Bericht, den Sie abrufen möchten, auf **Herunterladen**.

   > [!NOTE]
   > Der Bericht mit den Nutzungsdetails enthält keine anwendbaren Steuern.
   >
   > Zwischen der Nutzung und der entsprechenden Angabe im Bericht gibt es unter Umständen eine Verzögerung von bis zu acht Stunden.

So zeigen Sie die zusammengefassten Nutzungsberichte und -diagramme an:

1. Melden Sie sich beim Azure Enterprise Portal an.

1. Wählen Sie eine Laufzeit für die Vorauszahlung aus.

   Um den Datumsbereich für die **Nutzungszusammenfassung** zu ändern, können Sie in der rechten oberen Ecke der Seite von **M** (monatlich) zu **C** (benutzerdefiniert) wechseln und dann das benutzerdefinierte Start- und Enddatum eingeben.

   ![Erstellen und Anzeigen der Nutzungszusammenfassung und Herunterladen von Berichten in der benutzerdefinierten Ansicht](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Wählen Sie einen Zeitraum oder einen Monat im Diagramm aus, um weitere Details anzuzeigen.

   - Das Diagramm zeigt die Nutzung im Vergleich zum Vormonat mit einer Aufschlüsselung der tatsächlichen Nutzung, der übermäßig berechneten Dienste, der getrennt berechneten Gebühren und der Azure Marketplace-Gebühren an.
   - Für den ausgewählten Monat können Sie die Felder unterhalb des Diagramms verwenden, um nach Abteilungen, Konten und Abonnements zu filtern.
   - Sie können zwischen **Gebühr nach Diensten** und **Gebühr nach Hierarchie** wechseln.
   - Zeigen Sie Details zu den Kategorien **Azure-Dienst** , **Getrennt abgerechnete Gebühren** und **Azure Marketplace** an, indem Sie die entsprechenden Abschnitte erweitern.

In diesem Video können Sie sich die Nutzungsanzeige ansehen:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Herunterladen von CSV-Berichten

Unternehmensadministratoren verwenden die Seite „Download des monatlichen Berichts“, um die folgenden Berichte als CSV-Dateien herunterzuladen:

- Saldo und Gebühren
- Nutzungsdetails
- Azure Marketplace-Gebühren
- Preisblatt

So laden Sie Berichte herunter:

1. Wählen Sie im Azure Enterprise Portal **Berichte** aus.
2. Klicken Sie oben auf der Seite auf **Nutzung herunterladen**.
3. Wählen Sie neben dem Bericht des Monats **Herunterladen** aus.

   > [!NOTE]
   > Zwischen dem Nutzungsdatum und dem Zeitpunkt, zu dem die Nutzung in den Berichten angezeigt wird, gibt es unter Umständen eine Wartezeit von bis zu fünf Tagen.
   >
   > Wenn Sie die CSV-Dateien mit Safari in Excel herunterladen, treten möglicherweise Formatierungsfehler auf. Öffnen Sie die Datei in einem Text-Editor, um solche Fehler zu vermeiden.

![Beispiel für die Seite „Nutzung herunterladen“](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Das folgende Video zeigt, wie Sie Nutzungsinformationen herunterladen:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Herunterladen des erweiterten Berichts

Über den Download erweiterter Berichte können Sie Berichte abrufen, die bestimmte Datumsbereiche oder Konten abdecken. Die Ausgabedatei liegt im CSV-Format vor, um große Datensatzgruppen zu unterstützen.

1. Wählen Sie im Azure Enterprise Portal **Download erweiterter Berichte** aus.
1. Wählen Sie einen geeigneten Datumsbereich und die entsprechenden Konten aus.
1. Wählen Sie **Nutzungsdaten anfordern** aus.
1. Wählen Sie die Schaltfläche **Aktualisieren** aus, bis der Status des Berichts auf **Herunterladen** aktualisiert wird.
1. Laden Sie den Bericht herunter.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Herunterladen von Nutzungsberichten und Abrechnungsinformationen für eine vorherige Registrierung

Sie können Nutzungsberichte und Abrechnungsinformationen für eine vorherige Registrierung herunterladen, nachdem eine Registrierungsübertragung durchgeführt wurde. Historische Berichte sind sowohl im Azure Enterprise Portal als auch in der Kostenverwaltung verfügbar.

Das Azure Enterprise Portal filtert inaktive Registrierungen aus der Ansicht heraus. Sie müssen das Kontrollkästchen **Aktiv** deaktivieren, um inaktive übertragene Registrierungen anzuzeigen.  

![Durch Deaktivieren des Kontrollkästchens kann der Benutzer inaktive Registrierungen anzeigen.](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Ändern einer Auftragsnummer für eine bevorstehende Überschreitungsrechnung

Das Azure Enterprise Portal generiert automatisch eine Standardauftragsnummer (Purchase Order, PO), sofern der Enterprise-Administrator vor dem Rechnungsdatum keine Nummer festlegt. Ein Enterprise-Administrator kann die Auftragsnummer bis zu sieben Tage nach dem Empfang einer automatisierten E-Mail-Rechnungsbenachrichtigung aktualisieren.

### <a name="to-update-the-azure-services-purchase-order-number"></a>So aktualisieren Sie die Auftragsnummer der Azure-Dienste:

1. Wählen Sie im Azure Enterprise Portal **Bericht** > **Nutzungszusammenfassung** aus.
1. Wählen Sie in der oberen rechten Ecke **Auftragsnummern bearbeiten** aus.
1. Aktivieren Sie das Optionsfeld **Azure-Dienste**.
1. Wählen Sie im Dropdownmenü der Datumsbereiche einen **Rechnungszeitraum** aus.

   Sie können eine Auftragsnummer innerhalb eines Zeitraums von sieben Tagen bearbeiten, nachdem Sie eine Rechnungsbenachrichtigung erhalten haben, aber bevor Sie die Rechnung bezahlt haben.
1. Geben Sie eine neue Auftragsnummer in das Feld **Auftragsnummer** ein.
1. Wählen Sie **Speichern** aus, um die Änderung zu übermitteln.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>So aktualisieren Sie die Azure Marketplace-Auftragsnummer:

1. Wählen Sie im Azure Enterprise Portal **Bericht** > **Nutzungszusammenfassung** aus.
1. Wählen Sie in der oberen rechten Ecke **Auftragsnummern bearbeiten** aus.
1. Aktivieren Sie das Optionsfeld **Marketplace**.
1. Wählen Sie im Dropdownmenü der Datumsbereiche einen **Rechnungszeitraum** aus.

   Sie können eine Auftragsnummer innerhalb eines Zeitraums von sieben Tagen bearbeiten, nachdem Sie eine Rechnungsbenachrichtigung erhalten haben, aber bevor Sie die Rechnung bezahlt haben.
1. Geben Sie eine neue Auftragsnummer in das Feld **Auftragsnummer** ein.
1. Wählen Sie **Speichern** aus, um die Änderung zu übermitteln.

## <a name="azure-enterprise-billing-frequency"></a>Azure Enterprise-Fakturierungsintervall

Microsoft erstellt jährlich am jeweiligen Registrierungsdatum eine Rechnung für alle Vorauszahlungskäufe von Microsoft Azure-Diensten. Für die Nutzung, die über die Vorauszahlungsbeträge hinausgeht, rechnet Microsoft nachträglich ab.

- Die Vorauszahlungsgebühren werden basierend auf einem monatlichen Tarif festgelegt und im Voraus jährlich in Rechnung gestellt.
- Überschreitungsgebühren werden monatlich berechnet und am Ende des Abrechnungszeitraums nachträglich in Rechnung gestellt.

### <a name="billing-intervals"></a>Abrechnungsintervalle

Ihr Abrechnungsintervall hängt davon ab, wie Sie Ihre Vorauszahlungskäufe durchführen. Stichtag Ihrer jährlichen Vorauszahlung ist entweder:

- Der Jahrestag Ihrer Registrierung
- Das Gültigkeitsdatum Ihres einjährigen Zusatzabonnements.

Wann Sie Ihre Überschreitungsrechnung erhalten, hängt vom Startdatum von Registrierung und Einrichtung ab:

- **Direkte Registrierungen mit Startdatum vor dem 1. Mai 2018** :
  - Für direkte Enterprise Agreement-Kunden (EA) wird ein jährlicher Abrechnungszeitraum für Azure-Dienste eingerichtet, ausgenommen Azure Marketplace-Dienste. Ihr Abrechnungszeitraum basiert auf dem Jahrestag: dem Datum, an dem Ihre Vereinbarung wirksam wurde.
  - Wenn Sie 150 % Ihrer Azure EA-Vorauszahlung überschreiten, wird für Sie automatisch ein vierteljährlicher Abrechnungszeitraum eingerichtet, der auf Ihrem Jahrestag basiert. Außerdem erhalten Sie eine Rechnung für die Azure-Dienstüberschreitung.
  - Wenn Sie nicht 150 % Ihres Azure-Vorauszahlungsschwellenwerts überschreiten, verbleibt Ihre Registrierung in einem jährlichen Abrechnungszeitraum. Die Rechnung für die Überschreitung erhalten Sie am Ende des Vorauszahlungsjahres.

- **Direkte Registrierungen mit Startdatum nach dem 1. Mai 2018** :
  - Ihr Azure-Verbrauch und separat abgerechnete Gebühren werden in einem monatlichen Abrechnungszeitraum abgerechnet.
  - Gebühren, die nicht durch ihre Azure-Vorauszahlung abgedeckt werden, sind als Überschreitungszahlung fällig.  

- **Indirekte Registrierungen mit Startdatum vor dem 1. Mai 2018** :

  Wenn Sie ein indirekter Enterprise Agreement-Kunde (EA) mit einem Startdatum vor dem 1. Mai 2018 sind, wird für Sie ein vierteljährlicher Abrechnungszeitraum eingerichtet. Vom Channelpartner(CP) erhalten Sie direkt eine Rechnung.  

- **Indirekte Registrierungen mit Startdatum nach dem 1. Mai 2018** :

  Für Sie gilt ein monatlicher Abrechnungszeitraum.  

### <a name="increase-your-azure-prepayment"></a>Möchten Sie Ihre Azure-Vorauszahlung erhöhen?

Sie können ihre Vorauszahlung jederzeit erhöhen. Ihnen wird die Anzahl der Monate in Rechnung gestellt, die im Vorauszahlungszeitraum des betreffenden Jahrs verbleiben. Wenn Sie sich z. B. für ein einjähriges Zusatzabonnement registrieren und ihre Vorauszahlung während des 6. Monats erhöhen, werden Ihnen die verbleibenden sechs Monate dieser Laufzeit in Rechnung gestellt. Die Höhe Ihrer Vorauszahlung wird dann für die letzten sechs Monaten ihrer Vorauszahlungslaufzeit aktualisiert. Diese neuen Mengen werden verwendet, um Überschreitungsgebühren zu ermitteln.

### <a name="overage"></a>Überschreitung

Bei Überschreitung werden Ihnen die Nutzung oder Reservierungen in Rechnung gestellt, die ihre Vorauszahlung während des Abrechnungszeitraums überschreiten. Eine Aufschlüsselung der Berechnung der Überschreitungsmenge für einzelne Elemente finden Sie im Bericht der Nutzungszusammenfassung, oder wenden Sie sich an Ihren Channelpartner.

Für jedes Element in der Rechnung wird Folgendes angezeigt:

- **Erweiterter Betrag** : die Gesamtkosten
- **Nutzung der Vorauszahlung** : der zum Abdecken der Gebühren verwendete Betrag Ihrer Vorauszahlung
- **Nettobetrag** : die Gebühren, die ihre Vorauszahlung überschreiten

Die fälligen Steuern werden nur für den Nettobetrag berechnet, der Ihre Vorauszahlung überschreitet.

Rechnungsstellung bei Überschreitung ist automatisiert. Der Zeitpunkt von Benachrichtigungen und Rechnungen hängt vom Enddatum des Abrechnungszeitraums ab.

- Überschreitungsbenachrichtigungen werden normalerweise sieben Tage nach Ihrem Abrechnungsenddatum gesendet.
- Überschreitungsrechnungen werden sieben bis neun Tage nach der Benachrichtigung gesendet.
- Sie können während der sieben Tage zwischen der Überschreitungsbenachrichtigung und der Rechnungsstellung die Gebühren überprüfen und die vom System generierten Auftragsnummern aktualisieren.

### <a name="azure-marketplace"></a>Azure Marketplace

Ab dem Abrechnungszyklus von April 2019 erhalten Kunden eine einzelne Azure-Rechnung, da wir alle Azure- und Azure Marketplace-Gebühren in einer einzigen Rechnung zusammengefasst haben, anstatt zwei separate Rechnungen zu erstellen. Diese Änderung gilt nicht für Kunden in Australien, Japan oder Singapur.

Während der Umstellung auf eine kombinierte Rechnung erhalten Sie eine partielle Azure Marketplace-Rechnung. Diese abschließende gesonderte Rechnung enthält Azure Marketplace-Gebühren, die vor dem Datum Ihres Abrechnungsupdates angefallen sind. Nach diesem Datum angefallene Azure Marketplace-Gebühren sind in ihrer Azure-Rechnung enthalten. Nach dem Übergangszeitraum sind alle Azure- und Azure Marketplace-Gebühren in der kombinierten Rechnung enthalten.  

### <a name="adjust-billing-frequency"></a>Ändern des Fakturierungsintervalls

Das Fakturierungsintervall eines Kunden kann jährlich, vierteljährlich oder monatlich sein. Der Abrechnungszeitraum wird festgelegt, wenn ein Kunde seine Vereinbarung unterzeichnet. Die monatliche Abrechnung ist das kleinste Abrechnungsintervall.

- Die **Genehmigung** eines Unternehmensadministrators ist erforderlich, um den Abrechnungszeitraum für direkte Registrierungen von jährlich in vierteljährlich zu ändern. Für indirekte Registrierungen ist die Genehmigung eines Partneradministrators erforderlich. Die Änderung wird am Ende des laufenden Abrechnungsquartals wirksam.
- Um einen jährlichen oder vierteljährlichen Abrechnungszeitraum in monatlich zu ändern, ist ein **Zusatz** zur Vereinbarung erforderlich.  Jede Änderung am bestehenden Abrechnungszeitraum der Registrierung erfordert die Genehmigung eines Unternehmensadministrators oder Ihres „Bill to Contact“ (Rechnungsempfängers).
- **Senden Sie** Ihre Genehmigung an den [Azure Enterprise Portal-Support](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Wählen Sie die Problemkategorie aus: **Abrechnung und Rechnungsstellung**.

Die Änderung wird am Ende des laufenden Abrechnungsquartals wirksam.

Wenn eine M503-Zusatzvereinbarung geschlossen wurde, können Sie jeden Vertrag von einem beliebigen Intervall in eine monatliche Abrechnung ändern. 

### <a name="request-an-invoice-copy"></a>Anfordern einer Rechnungskopie

Wenden Sie sich an Ihren Partner, um eine Kopie Ihrer Rechnung anzufordern.

## <a name="credits-and-adjustments"></a>Gutschriften und Berichtigungen

Im Abschnitt **Berichte** des [Azure Enterprise Portals](https://ea.azure.com) können Sie alle Gutschriften oder Anpassungen anzeigen, die auf Ihre Registrierung angewendet werden.

So zeigen Sie Gutschriften an:

1. Wählen Sie im [Azure Enterprise Portal](https://ea.azure.com) den Abschnitt **Berichte** aus.
1. Wählen Sie **Nutzungszusammenfassung** aus.
1. Ändern Sie oben rechts die **M** -Ansicht in die **C** -Ansicht.
1. Erweitern Sie das Anpassungsfeld in der Tabelle „Vorauszahlung für Azure-Dienst“.
1. Es werden Gutschriften für Ihre Registrierung und eine kurze Erläuterung angezeigt. Beispiel: SLA-Gutschrift.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Bezahlen Ihrer Überschreitung mit ihrer Azure-Vorauszahlung

Sie müssen die folgenden Kriterien erfüllen, um Ihre Azure-Vorauszahlung auf Überschreitungen anzuwenden:

- Bei Ihnen sind Überschreitungsgebühren angefallen, die noch nicht gezahlt wurden und innerhalb eines Jahres nach dem Enddatum des abgerechneten Diensts fällig sind.
- Ihr verfügbarer Azure-Vorauszahlungsbetrag deckt die angefallenen Gebühren vollständig ab, einschließlich aller vergangenen unbezahlten Azure-Rechnungen.
- Der Abrechnungszeitraum, den Sie abschließen möchten, muss vollständig geschlossen werden. Die Abrechnung wird am fünften Tag jedes Monats vollständig geschlossen.
- Der Abrechnungszeitraum, den Sie ausgleichen möchten, muss vollständig geschlossen werden.
- Ihr Azure-Vorauszahlungsrabatt (Azure Prepayment Discount) basiert auf der neuen Vorauszahlung abzüglich der Beträge, die für den vorherigen Verbrauch geplant wurden. Diese Anforderung gilt nur für angefallene Überschreitungsgebühren. Sie gilt nur für Dienste, für die eine Azure-Vorauszahlung gültig ist, und ist nicht auf Azure Marketplace-Gebühren anwendbar. Azure Marketplace-Gebühren werden separat in Rechnung gestellt.

Um einen Überschreitungsausgleich durchzuführen, können Sie oder das Kontoteam eine Supportanfrage eröffnen. Eine per E-Mail gesendete Genehmigung Ihres Unternehmensadministrators oder „Bill to Contact“ ist erforderlich.

## <a name="move-charges-to-another-enrollment"></a>Verschieben von Kosten in eine andere Registrierung

Nutzungsdaten werden nur verschoben, wenn eine Übertragung rückdatiert wird. Es gibt zwei Möglichkeiten, um Nutzungsdaten von einer Registrierung in eine andere zu verschieben:

- Kontoübertragungen von einer Registrierung zu einer anderen
- Registrierungsübertragungen von einer Registrierung zu einer anderen

Für beide Optionen müssen Sie eine [Supportanfrage](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) an das EA-Support-Team senden. 

## <a name="enterprise-agreement-usage-calculations"></a>Berechnungen der Enterprise Agreement-Nutzung

Grundlegende öffentliche Preisinformationen, Maßeinheiten, FAQs und Richtlinien zur Erstellung von Nutzungsberichten für jeden einzelnen Dienst finden Sie unter [Azure-Produkte](https://azure.microsoft.com/services/) und [Azure-Preise](https://azure.microsoft.com/pricing/). Weitere Informationen zu EA-Berechnungen finden Sie in den folgenden Abschnitten.

### <a name="enterprise-agreement-units-of-measure"></a>Maßeinheiten des Enterprise Agreement

Die Maßeinheiten für Enterprise Agreements unterscheiden sich häufig von denen in anderen Programmen wie z. B. dem Microsoft Online Services-Abonnementvertrag-Programm (MOSA). Diese Ungleichheit bedeutet, dass für eine Reihe von Diensten die Maßeinheit aggregiert wird, um die normalisierten Preise bereitzustellen. Die Maßeinheit, die in der Ansicht „Nutzungszusammenfassung“ des Azure Enterprise Portals angezeigt wird, ist immer das Enterprise-Maß. Eine vollständige Liste der aktuellen Maßeinheiten und Konvertierungen für jeden Dienst wird Ihnen in der Excel-Datei [Friendly_Service_Names.xlsx](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) bereitgestellt.

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konvertierung zwischen Nutzungsdetailbericht und Nutzungszusammenfassungs-Seite

Im heruntergeladenen Nutzungsdatenbericht können Sie die reine Ressourcennutzung auf bis zu sechs Dezimalstellen genau sehen. Allerdings werden die im Azure Enterprise Portal angezeigten Nutzungsdaten für Vorauszahlungseinheiten auf vier Dezimalstellen gerundet, und bei Überschreitungseinheiten werden alle Dezimalstellen abgeschnitten. Die reinen Nutzungsdaten werden zuerst auf vier Ziffern gerundet, bevor sie in Einheiten konvertiert werden, die im Azure Enterprise Portal verwendet werden. Anschließend werden die konvertierten Enterprise-Einheiten erneut auf vier Ziffern gerundet. Die tatsächlich verbrauchten Stunden vor der Konvertierung werden im heruntergeladenen Nutzungsbericht und nicht im Azure Enterprise Portal angezeigt.

Beispiel: 694,533404 tatsächliche SQL Server-Stunden werden im Nutzungsdetailsbericht angezeigt. Diese Einheiten werden dann in 6,94533404 Einheiten von 100 Computestunden konvertiert, die dann zur Anzeige im Azure Enterprise Portal auf 6,9453 gerundet werden.

- Um den erweiterten Abrechnungsbetrag zu bestimmen, werden die angezeigten Einheiten mit dem Preis je Vorauszahlungseinheit multipliziert, und das Ergebnis wird auf zwei Dezimalzahlen gekürzt. Für japanische Yen (JPY) und koreanische Won (KRW) wird der erweiterte Betrag auf null (0) Dezimalstellen gerundet.
- Bei einer Überschreitung werden die Abrechnungseinheiten auf sechs Stellen gekürzt und dann mit dem Preis je Einheit der Überschreitung multipliziert, um den erweiterten Abrechnungsbetrag zu bestimmen.
- Bei der Abrechnung für Anbieter verwalteter Dienste (Managed Service Provider, MSP) wird die gesamte als MSP gekennzeichnete Verwendung einer Abteilung nach der Konvertierung in die EA-Maßeinheit auf null (0) Dezimalstellen gekürzt. Folglich kann die Summe dieser Nutzung niedriger sein als die Summe der gesamten im Azure Enterprise Portal berichteten Nutzung. Dies hängt davon ab, ob sich der MSP innerhalb der Azure-Vorauszahlung oder in Überschreitung befindet.

### <a name="graduated-pricing"></a>Gestaffelte Preise

Für Enterprise Agreement-Programme liegen derzeit keine gestaffelten Preise vor, wobei die Preise pro Einheit sinken, wenn die Nutzung zunimmt. Wenn Sie von einem MOSA-Programm mit gestaffelten Preisen zu einem Enterprise Agreement wechseln, werden die Preise gemäß des Basistarifs des Diensts abzüglich ggf. anwendbarer Anpassungen für Enterprise Agreement-Rabatte festgelegt.

### <a name="partial-hour-billing"></a>Teilweise Stundenabrechnung

Die gesamte abgerechnete Nutzung basiert auf Minuten, die in Teil- und nicht Gesamtstundeninkremente umgerechnet werden. Die aufgelisteten stündlichen Enterprise-Tarife werden auf die Gesamt- und die Teilstunden angewendet.

### <a name="average-daily-consumption"></a>Durchschnittlicher täglicher Verbrauch

Einige Dienste werden monatlich berechnet, aber die Nutzung wird täglich gemeldet. In diesen Fällen wird die Nutzung einmal pro Tag ausgewertet, durch 31 dividiert und über die Anzahl der Tage in diesem Abrechnungsmonat summiert. Dies führt zu Tarifen, die für einen Monat nie höher als erwartet und für die Monate mit weniger als 31 Tagen etwas niedriger sind.

### <a name="compute-hours-conversion"></a>Konvertierung von Computestunden

Vor dem 28. Januar 2016 wurde die Nutzung von virtuellen Computern der Tarife „Standard“ und „Basic“ sowie Clouddiensten mit den Größen A0, A2, A3 und A4 als Abrechnungsminuten für A1-VMs abgerechnet. A0-VMs zählten als Bruchteile von A1-VM-Minuten, während A2s, A3s und A4s in Vielfache konvertiert wurden. Diese Richtlinie führte bei unseren Kunden zu Verwirrung, sodass wir eine Änderung einführten, um die A0-, A2-, A3- und A4-Nutzung minutenbasiert abzurechnen.

Die neue Messung wurde zwischen dem 27. und 28. Januar 2016 wirksam. Im CSV-Download, der die Nutzung während dieses Übergangszeitraums anzeigt, sehen Sie beides:

- In der A1-Verbrauchseinheit ausgegebene Nutzung
- Nutzung, die in der neuen dedizierten Verbrauchseinheit gemäß der Größe Ihrer Bereitstellung ausgegeben wird.

| **Bereitstellungsgröße** | **Berechnung der Nutzung als Mehrfaches von A1 (bis 26. Januar 2016)** | **Berechnung der Nutzung mit dedizierten Abrechnungseinheiten (ab 27. Januar 2016)** |
| --- | --- | --- |
| A0 | 0,25 einer A1-Stunde | 1 einer A0-Stunde |
| A2 | 2 einer A1-Stunde | 1 einer A2-Stunde |
| A3 | 4 einer A1-Stunde | 1 einer A3-Stunde |
| A4 | 8 einer A1-Stunde | 1 einer A4-Stunde |

### <a name="regions"></a>Regions

Für Dienste, bei denen Zone und Region die Preise beeinflussen, zeigt die folgende Tabelle die Zuordnung für geografische Bereiche und Regionen:

| geografischer Raum | Datenübertragungsregionen | CDN-Regionen |
| --- | --- | --- |
| Zone 1 | Europa, Norden <br> Europa, Westen <br> USA, Westen <br> USA, Osten <br> USA, Norden-Mitte <br> USA, Süden-Mitte <br> USA (Ost 2) <br> USA, Mitte | Nordamerika <br> Europa |
| Zone 2 | Asien-Pazifik, Osten <br> Asien-Pazifik, Südosten <br> Japan, Osten <br> Japan, Westen <br> Australien (Osten) <br> Australien, Südosten | Asien-Pazifik <br> Japan <br> Lateinamerika <br> Naher Osten/Afrika <br> Australien (Osten) <br> Australien, Südosten |
| Zone 3 | Brasilien Süd |   |

Es fallen keine Gebühren für den Datenausgang zwischen Diensten an, die im selben Rechenzentrum untergebracht sind. Beispiel: Microsoft 365 und Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Azure-Vorauszahlung und nicht berechnete Nutzung

Die Azure-Vorauszahlung ist ein Betrag, der vorab für Azure-Dienste gezahlt wird. Die Azure-Vorauszahlung wird genutzt, wenn Dienste verwendet werden. Azure-Erstanbieterdienste werden gegen die Azure-Vorauszahlung abgerechnet. Einige Gebühren werden jedoch separat abgerechnet, und Azure Marketplace-Dienste werden nicht auf die Azure-Vorauszahlung angerechnet.

### <a name="charges-billed-separately"></a>Separat abgerechnete Gebühren

Einige Produkte und Dienste, die aus Drittanbieterquellen bereitgestellt werden, werden nicht auf die Azure-Vorauszahlung angerechnet. Stattdessen werden diese Elemente separat als Teil der Überschreitungsrechnung für den Standardabrechnungszyklus abgerechnet.

Wir haben alle Azure- und Azure Marketplace-Gebühren in einer einzigen Rechnung zusammengefasst, die sich am Abrechnungszyklus der Registrierung orientiert. Die kombinierte Rechnung gilt nicht für Azure-Kunden in Australien, Japan oder Singapur.

Die kombinierte Rechnung zeigt zunächst die Azure-Nutzung und dann alle Azure Marketplace-Gebühren an. Kunden in Australien, Japan oder Singapur sehen die Azure Marketplace-Gebühren in einer separaten Rechnung.

Wenn am Ende des Standardabrechnungszyklus keine Überschreitungsrechnung erstellt wird, werden die Gebühren separat abgerechnet. Dieser Prozess gilt für Kunden in Australien, Japan und Singapur.

Die folgenden Dienste werden separat in Rechnung gestellt:

- Canonical
- Citrix XenApp Essentials
- Registrierter Benutzer von Citrix XenDesktop
- OpenLogic
- Registrierter Benutzer von Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (monatlich)
- Visual Studio Enterprise (jährlich)
- Visual Studio Professional (monatlich)
- Visual Studio Professional (jährlich)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Auswirkungen der Änderung des Kanalpartners

Wenn die Änderung des Kanalpartners (Change Of Channel Partner, COCP) in der Mitte des Monats erfolgt, erhält der Kunde eine Rechnung für die Nutzung unter dem zuvor zugeordneten Partner und eine weitere Rechnung für die Nutzung unter dem neuen Partner.

Die Rechnungen werden im Monat nach dem Ende des Abrechnungszeitraums veröffentlicht. Bei einem monatlichen Abrechnungsintervall wird die Septemberrechnung für beide Partner im Oktober veröffentlicht. Im Falle eines vierteljährlichen oder jährlichen Abrechnungszeitraums erhält der Kunde eine Rechnung für den zuvor zugeordneten Partner, die die Nutzung des zugehörigen Zeitraums enthält. Der Rest wird dann für den neuen Partner auf der Grundlage des Abrechnungsintervalls abgerechnet.

## <a name="next-steps"></a>Nächste Schritte

- Die folgenden Excel-Dateien enthalten Details zu den Azure-Diensten und werden am 6. und 20. jedes Monats aktualisiert:

   | Titel | BESCHREIBUNG | Dateiname |
   | --- | --- | --- |
   | [Anzeigedienstnamen](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Listet alle aktiven Dienste auf und umfasst Folgendes: <br>  <ul><li>Dienstkategorie</li>   <li>Anzeigedienstnamen</li>   <li>Vorauszahlungsname und Artikelnummer</li> <li>Verbrauchsname und Artikelnummer</li>   <li>Maßeinheiten</li>   <li>Faktoren zur Konvertierung zwischen berichteter Verwendung und angezeigter Enterprise Portal-Verwendung</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Dienstdownloadfelder](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Diese Tabelle enthält eine Liste aller möglichen Kombinationen dienstbezogener Felder im Verwendungsdownloadbericht. | Service\_Download\_Fields.xlsx |

- Informationen zu Ihren Rechnungen und Gebühren finden Sie unter [Grundlegendes zu Ihrer Azure Enterprise-Rechnung](../understand/review-enterprise-agreement-bill.md).
- Informationen zur Verwendung des Azure Enterprise Portals finden Sie unter [Erste Schritte mit dem Azure EA-Portal](ea-portal-get-started.md).
