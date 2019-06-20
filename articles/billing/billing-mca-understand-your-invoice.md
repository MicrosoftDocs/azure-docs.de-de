---
title: Grundlegendes zur Rechnung für eine Microsoft-Kundenvereinbarung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Rechnung für die Microsoft-Kundenvereinbarung lesen und verstehen können.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: aee51793c66ae57f740300797b8fdc1799e685cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604043"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Grundlegendes zu den Begriffen auf der Rechnung für eine Microsoft-Kundenvereinbarung

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben](#check-access-to-a-microsoft-customer-agreement).

Die Rechnung enthält eine Zusammenfassung der Gebühren sowie Zahlungsanweisungen. Die Rechnung steht im [Azure-Portal](https://portal.azure.com/) im PDF-Format (Portable Document Format) zum Download bereit oder kann per E-Mail gesendet werden. Weitere Informationen finden Sie unter [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Begriffe auf der Rechnung und entsprechende Beschreibungen

In den folgenden Abschnitten werden die wichtigsten Begriffe auf der Rechnung aufgeführt und beschrieben.

### <a name="invoice-summary"></a>Invoice summary (Rechnungsübersicht)

Den Abschnitt **Invoice summary** (Rechnungsübersicht) finden Sie oben auf der ersten Seite. Dort werden Informationen zu Ihrem Abrechnungsprofil und Ihren Zahlungsbedingungen aufgeführt.

![Abschnitt „Rechnungszusammenfassung“](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Begriff | BESCHREIBUNG |
| --- | --- |
| Sold To |Adresse des Rechtsträgers gemäß den Angaben im Abrechnungskonto|
| Bill to |Rechnungsadresse des Abrechnungsprofils, das die Rechnung erhält, gemäß den Angaben im Abrechnungsprofil|
| Billing Profile (Abrechnungsprofil) |Der Name des Abrechnungsprofils, für das die Rechnung ausgestellt wird |
| P.O. number |Eine optionale Bestellnummer, die Ihnen zur Nachverfolgung zugewiesen wird |
| Invoice number (Rechnungsnummer) |Eine eindeutige, von Microsoft generierte Rechnungsnummer, die für Nachverfolgungszwecke verwendet wird |
| Invoice Date |Datum, an dem die Rechnung generiert wurde, in der Regel fünf bis zwölf Tage nach dem Ende des Abrechnungszyklus. Sie können das Rechnungsdatum auch in den Angaben des Abrechnungsprofils überprüfen.|
| Zahlungsbestimmungen |Die Zahlungsbedingungen Ihrer Microsoft-Rechnung. *Net 30 days* (Zahlbar in 30 Tagen netto) bedeutet, das die Zahlung innerhalb von 30 Tage nach dem Rechnungsdatum getätigt werden muss. |

### <a name="billing-summary"></a>Billing summary (Abrechnungsübersicht)

Im Abschnitt **Billing summary** (Abrechnungsübersicht) werden alle in Rechnung gestellten Gebühren des Abrechnungsprofils seit dem letzten Abrechnungszeitraum, etwaige Gutschriften, Steuern und der fällige Gesamtbetrag aufgelistet.

![Abschnitt „Billing Summary“ (Abrechnungsübersicht)](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Begriff | BESCHREIBUNG |
| --- | --- |
| Charges|Microsoft-Gesamtgebühren für dieses Abrechnungsprofil seit dem letzten Abrechnungszeitraum |
| Guthaben |Gutschriften durch Rückgaben |
| Azure credits applied (Angewendete Azure-Gutschriften) | Azure-Gutschriften, die in jedem Abrechnungszeitraum automatisch mit den Azure-Gebühren verrechnet werden |
| Subtotal (Zwischensumme) |Der fällige Betrag ohne Steuern |
| Tax (Steuern) |Art und Menge der berechneten Steuer, abhängig vom Land bzw. der Region des Abrechnungsprofils. Wenn Sie keine Steuern zahlen müssen, werden auf Ihrer Rechnung keine Steuern aufgeführt. |
| Estimated total savings (Geschätzte Gesamteinsparungen) |Der geschätzte Gesamtbetrag an Einsparungen durch geltende Rabatte. Wenn vorhanden, werden geltende Rabatte unter der jeweiligen Kaufposition in den Details nach Rechnungsabschnitt aufgeführt. |

### <a name="invoice-sections"></a>Rechnungsabschnitte

Sie sehen für jeden Rechnungsabschnitt in Ihrem Abrechnungsprofil die entsprechenden Gebühren, die angewendeten Azure-Gutschriften, die Steuern und den fälligen Gesamtbetrag.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Details nach Rechnungsabschnitt

In den Details werden die Kosten für jeden Rechnungsabschnitt pro Produkt angezeigt. Innerhalb jedes Produkts werden die Kosten nach Dienstart angezeigt. Täglich anfallende Gebühren für Ihre Produkte und Dienste finden Sie im Azure-Portal sowie in der CSV-Datei zu Azure-Nutzung und -Gebühren. Weitere Informationen finden Sie unter [Grundlegendes zu den Gebühren auf der Rechnung für eine Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md).

Der fällige Gesamtbetrag für jede Dienstfamilie errechnet sich aus den *Guthaben/Gebühren* minus *Azure-Gutschriften* plus *Steuern*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Details nach Rechnungsabschnitt](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Begriff |BESCHREIBUNG |
| --- | --- |
| Unit price (Einzelpreis) | Der geltenden Einzelpreis für den Dienst (in der Abrechnungswährung), anhand dessen die Gebühren für die Nutzung berechnet werden. Dieser Preis ist für jedes Produkt, jede Dienstfamilie, jede Verbrauchseinheit und jedes Angebot anders. |
| Qty (Menge) | Während des Abrechnungszeitraums gekaufte oder genutzt Menge |
| Charges/Credits (Gebühren/Guthaben) | Gebühren nach Abzug von Gutschriften/Erstattungen |
| Azure Credit (Azure-Gutschrift) | Die Azure-Gutschriften, die mit den Gebühren/Guthaben verrechnet werden|
| Tax rate (Steuersatz) | Steuersätze nach Land/Region |
| Tax amount (Steuerbetrag) | Steuerbetrag, der auf Grundlage des Steuersatz auf einen Kauf angewendet wird |
| Gesamt | Der insgesamt fällige Betrag für einen Kauf |

### <a name="how-to-pay"></a>Zahlung

Anweisungen zur Bezahlung Ihrer Rechnung finden Sie unten auf der Rechnung. Sie können per Scheck, Überweisung oder online bezahlen. Wenn Sie online bezahlen, können Sie dazu eine Kredit-/EC-Karte oder Azure-Gutschriften verwenden (falls vorhanden).

### <a name="publisher-information"></a>Informationen zum Herausgeber

Wenn in Ihrer Rechnung Dienste von Drittanbietern aufgeführt werden, werden am unteren Rand der Rechnung der Name und die Adresse jedes Herausgebers aufgelistet.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zu den Gebühren auf der Rechnung für eine Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md)
- [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](billing-download-azure-invoice-daily-usage-date.md)
- [Anzeigen der Azure Enterprise Agreement-Preise für Ihre Organisation](billing-ea-pricing.md)
- [Anzeigen der Steuerdokumente für Ihre Microsoft-Kundenvereinbarung](billing-mca-download-tax-document.md)
