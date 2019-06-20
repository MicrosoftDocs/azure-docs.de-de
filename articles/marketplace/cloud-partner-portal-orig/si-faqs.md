---
title: Seller Insights – häufig gestellte Fragen
description: Häufig gestellte Fragen zum Feature Seller Insights des Cloud-Partnerportals.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943096"
---
<a name="seller-insights-faq"></a>Seller Insights – häufig gestellte Fragen
===================

Dieser Artikel enthält Anleitungen zu gängigen Benutzerverfahren und Antworten auf Fragen zu Seller Insights.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Finden von Definitionen für die Werte in der heruntergeladenen Transaktionsdatei
------------------------------------------------------------------

Die Definitionen der Metrikwerte in der Transaktionsdatei finden Sie im Artikel [Seller Insights – Definitionen](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Anzeigen von Kundendaten von Transaktionen, für die ich bezahlt wurde
-------------------------------------------------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung **Auszahlungsstatus** an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „Ausbezahlt“ an. Folgende Spalten werden angezeigt, die Kundendaten enthalten: **Firmenname**, **E-Mail-Adresse des Kunden**, **Land des Kunden**, **Bundesland/Kanton des Kunden** und **Postleitzahl des Kunden**.


<a name="calculate-my-open-accounts-receivable"></a>Berechnen meiner Außenstände
-------------------------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung **Auszahlungsstatus** an, und wenden Sie den Filter zur ausschließlichen Anzeige der Werte „Bevorstehende Auszahlung“ und „Nicht auszahlungsbereit“ an. Bilden Sie dann die Summe der Spalte mit der Bezeichnung **Auszahlungsbetrag (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Berechnen des Umsatzes nach Kundennutzungsdauer
------------------------------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung  **Transaktionsstatus** an, und wenden Sie den Filter auf den Wert „Bezahlt“ an.   Für jede aufgelistete Transaktion gibt die Spalte mit der Bezeichnung  **Auszahlungsbetrag (PC)**   den Betrag an, der Ihnen bezahlt wurde.  Um die mit der Transaktion verbundene Nutzungsdauer zu schätzen, verwenden Sie die Spalte  **Abrechnungsdatum**, eine enge Annäherung an den letzten Nutzungstag des Zeitraums, auf den sich die Transaktion bezieht.


<a name="calculate-your-bad-debt"></a>Berechnen Ihres Forderungsausfalls
---------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung **Endgültiger Inkassostatus** an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „Abschreibung“ an. Bilden Sie dann die Summe der Spalte mit der Bezeichnung **Auszahlungsbetrag (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Anzeigen von Auszahlungs- oder Kundenkontaktinformationen
-------------------------------------------

Melden Sie sich als Benutzer mit der Rolle „Besitzer“ und nicht „Mitwirkender“ an. Nur für die Rolle „Besitzer“ werden Auszahlungs- und Kundendaten angezeigt. Weitere Informationen zu Benutzerrollen finden Sie im Artikel [Verwalten von Benutzern im Cloud-Partnerportal](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Berechnen meiner Vorauszahlungen
----------------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung **Transaktionstyp** an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „Gebühr“ an. Zeigen Sie als nächstes die Spalte mit der Bezeichnung **Endgültiger Inkassostatus** an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „In Bearbeitung“ an. Bilden Sie schließlich die Summe der Spalte **Auszahlungsbetrag (PC)** , um alle Vorauszahlungen zu berechnen, die an Sie geleistet wurden, bevor Sie den Betrag beim Kunden eingezogen haben.


<a name="calculate-customer-refunds"></a>Berechnen von Erstattungen an Kunden
--------------------------

Zeigen Sie nach dem Herunterladen Ihrer Transaktionen aus dem Modul „Auszahlung“ die Spalte mit der Bezeichnung **Endgültiger Inkassostatus** an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „Erstattung“ an. Bilden Sie die Summe der Spalte **Abrechnungsbetrag (PC)** zum Berechnen aller an Ihre Kunden geleisteten Erstattungen.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifizieren der Transaktionen, an denen ein Channelpartner von Microsoft beteiligt ist
----------------------------------------------------------------

An allen Transaktionen in der Spalte **Azure-Lizenztyp**, die zum Anzeigen der Werte „Enterprise über Handelspartner“ und „Cloud Solution Provider“ gefiltert werden, ist ein Channelpartner von Microsoft beteiligt. Weitere Informationen zum Partner finden Sie im Download der Module „Auszahlung“ und „Kunde“ unter **Name des Handelspartners** und **E-Mail-Adresse des Handelspartners**.


<a name="identify-trial-usage-and-trial-conversions"></a>Identifizieren von Testversionsnutzung und Testversionskonvertierungen
------------------------------------------

Die Downloads der Module „Bestellung“, „Nutzung“ und „Auszahlung“ enthalten nun das **Enddatum der Testversion**, damit Sie ggf. erfahren, wann der Testzeitraum für diese konkrete Bestellung endete. Um Testversionsnutzung und Bestellungen anzuzeigen, zeigen Sie die Spalte **SKU-Abrechnungstyp** in den Downloads an, und wenden Sie den Filter zur ausschließlichen Anzeige des Werts „Testversion“ an. Um Testversionskonvertierungen anzuzeigen, zeigen Sie die Spalte **Enddatum der Testversion** in den Downloads an, und wenden Sie den Filter zur ausschließlichen Anzeige solcher Bestellungen an, bei denen das **Enddatum der Testversion** vor dem heutigen Datum liegt und die Spalte **Abbruchdatum** leer ist oder ihr Wert nach **Enddatum der Testversion** liegt.


<a name="when-is-my-monthly-payout-calculated"></a>Wann wird meine monatliche Auszahlung berechnet?
------------------------------------

Alle Beträge, die am letzten Kalendertag des Vormonats zur Auszahlung bereit waren, werden Ihnen zum 15. jedes Monats in Rechnung gestellt. Microsoft berechnet am dritten Tag des Monats den Auszahlungsbetrag des vorherigen Monats und aktualisiert alle fälligen Gebührentransaktionen in Ihrem Download in der Spalte **Auszahlungsstatus** mit „Bevorstehende Auszahlung“. Diese Transaktionen bleiben in diesem Zustand, bis die Zahlungsanforderung an Ihr Bankkonto gerichtet wird. Zu diesem Zeitpunkt wird Ihr **Auszahlungsstatus** mit „Ausbezahlt“ aktualisiert, und das „Auszahlungsdatum“ wird mit dem Datum aktualisiert, zu dem wir die Zahlungsanforderung an Ihre Bank gesendet haben.


<a name="calculate-customer-acquisition-and-loss"></a>Berechnen von Kundengewinnung und -verlust
---------------------------------------

Um zu erfahren, wann der Kunde zum ersten Mal eines Ihrer Angebote gekauft hat, nutzen Sie die Spalte **Erwerbsdatum** im Kundendownload. Auf ähnliche Weise können Sie über die Spalte **Verlustdatum** im Kundendownload erfahren, seit wann er kein von Ihnen veröffentlichtes Angebot mehr genutzt hat.


<a name="finding-more-help"></a>Weitere Hilfe
-----------------

- [Seller Insights – Definitionen](./si-insights-definitions-v4.md): Hier finden Sie Definitionen für Metriken und Daten.

- [Erste Schritte mit Seller Insights](./si-getting-started.md): Einführung in das Feature „Seller Insights“.

