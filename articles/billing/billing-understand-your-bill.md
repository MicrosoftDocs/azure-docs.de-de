---
title: Grundlegendes zu Ihrer Azure-Rechnung | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Nutzung und Abrechnung Ihres Azure-Abonnements anzeigen und besser verstehen können.
services: ''
documentationcenter: ''
author: tonguyen10
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 4303b79a7ee69d029504bf6ca2359f6e6070e5b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370156"
---
# <a name="understand-your-microsoft-azure-bill"></a>Grundlegendes zu Ihrer Microsoft Azure-Rechnung
Um Ihre Azure-Rechnung besser zu verstehen, können Sie sie mit der Datei, die die ausführlichen Angaben zur täglichen Nutzung enthält, und den Kostenverwaltungsberichten im Azure-Portal vergleichen.

Dieser Artikel gilt nicht für Azure-Kunden mit einem Enterprise Agreement (EA-Kunden). Wenn Sie ein EA-Kunde sind, finden Sie weitere Informationen unter [Erläuterungen zur Rechnung für Azure-Kunden mit Enterprise Agreement](billing-understand-your-bill-ea.md).

Dieser Artikel gilt nicht für Azure-Kunden mit einer Microsoft-Kundenvereinbarung. Wenn Sie über ein Rechnungskonto für eine Microsoft-Kundenvereinbarung verfügen, finden Sie Informationen dazu unter [Erläuterungen zu Azure-Gebühren auf der Rechnung zur Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md).

Eine Erläuterung der Abrechnung im Azure Cloud Solution Provider-Programm (Azure CSP) einschließlich Abrechnungszyklus, Preisgestaltung und Nutzung finden Sie unter [Übersicht zur Azure CSP-Abrechnung](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Überprüfen Ihrer Gebühren

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Wenn Sie zu einer Gebühr in Ihrer Rechnung weitere Informationen erhalten möchten, können Sie Nutzung und Kosten mit der Nutzungsdatei oder mit den Angaben im Azure-Portal vergleichen.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Option 1: Vergleichen von Nutzung und Kosten mit der Nutzungsdatei

Die CSV-Datei mit ausführlichen Nutzungsdaten enthält Ihre Gebühren nach Abrechnungszeitraum und Daten zur täglichen Nutzung. Informationen zum Abrufen der Datei finden Sie unter [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](billing-download-azure-invoice-daily-usage-date.md).

Ihre Nutzungsgebühren werden auf der Ebene der Verbrauchseinheiten angezeigt. Die Bedeutung der folgenden Benennungen ist in der Rechnung und in der Datei mit ausführlichen Nutzungsdaten identisch. Beispielsweise entspricht der Abrechnungszyklus in der Rechnung dem Abrechnungszeitraum in der Datei mit ausführlichen Nutzungsdaten.

 | Rechnung (PDF) | Detaillierte Nutzungsdaten (CSV)|
 | --- | --- |
|Billing Cycle | Billing Period |
 |NAME |Meter Category |
 |Type |Meter Subcategory |
 |Resource |Meter Name |
 |Region |Meter Region |
 |Consumed |Consumed Quantity |
 |Enthalten |Included Quantity |
 |Billable |Overage Quantity |

Der Abschnitt zu den **Nutzungsgebühren** Ihrer Rechnung enthält den Gesamtwert aller Verbrauchseinheiten, die während des Abrechnungszeitraums genutzt wurden. Im folgenden Screenshot wird beispielsweise eine Nutzungsgebühr für den Azure Scheduler-Dienst gezeigt.

![Nutzungsgebühren (Rechnung)](./media/billing-understand-your-bill/1.png)

Dieselbe Gebühr wird im Abschnitt **Statement** (Auszug) der CSV-Datei mit ausführlichen Nutzungsdaten angezeigt. Sowohl der verbrauchte Betrag (*Verbraucht*) als auch der *Wert* stimmt mit der Rechnung überein.

![Nutzungsgebühren (CSV-Datei)](./media/billing-understand-your-bill/2.png)

Eine Aufstellung dieser Gebühren finden Sie in der CSV-Datei im Abschnitt **Tägliche Nutzung**. Filtern Sie unter *Kategorie der Abrechnungseinheit* nach *Planer*. Sie können sehen, an welchen Tagen die Verbrauchseinheit verwendet wurde und wie hoch der Verbrauch war. Zu Vergleichszwecken sind auch Informationen zu *Ressource* und *Ressourcengruppe* aufgeführt. Die Werte unter *Verbraucht* sollten zusammen die in der Rechnung enthaltene Summe ergeben.

![Abschnitt zur täglichen Nutzung in der CSV-Datei](./media/billing-understand-your-bill/3.png)

Sie erhalten die Kosten pro Tag, indem Sie die Beträge von *Verbraucht* mit dem Wert *Rate* aus dem Abschnitt **Statement** (Auszug) multiplizieren.

Weitere Informationen finden Sie unter:

- [Grundlegendes zu Ihrer Azure-Rechnung](billing-understand-your-invoice.md)
- [Grundlegendes über die detaillierte Nutzung in Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Option 2: Vergleichen der Nutzung und Kosten im Azure-Portal

Mithilfe des Azure-Portals können Sie auch Ihre Gebühren überprüfen. Um einen schnellen Überblick über Ihre berechnete Nutzung und Gebühren zu erhalten, zeigen Sie die Kostenverwaltungsdiagramme an.

1. Navigieren Sie im Azure-Portal zu [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wählen Sie Ihr Abonnement und dann **Kostenanalyse** aus.
1. Filtern Sie nach **Zeitraum**.
1. Im Rahmen des vorherigen Beispiels sehen Sie eine Nutzungsgebühr für den Azure Scheduler-Dienst.

   ![Kostenanalyseansicht im Azure-Portal](./media/billing-understand-your-bill/4.png)

1. Wählen Sie diese Zeile aus, um die Aufschlüsselung der täglichen Kosten anzuzeigen.

   ![Ansicht „Kostenverlauf“ im Azure-Portal](./media/billing-understand-your-bill/5.png)

Weitere Informationen finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md#costs).

## <a name="external"></a>Separat in Rechnung gestellte externe Dienste

Externe Dienste oder Marketplace-Gebühren gelten für Ressourcen, die von Drittanbietern erstellt wurden. Diese Ressourcen können über den Azure Marketplace genutzt werden. Barracuda Firewall ist beispielsweise eine von einem Drittanbieter angebotene Azure Marketplace-Ressource. Alle Gebühren für die Firewall und die entsprechenden Verbrauchseinheiten werden als Gebühren für externe Dienste angezeigt.

Gebühren für externe Dienste werden separat in Rechnung gestellt. Die Gebühren werden nicht in Ihrer Azure-Rechnung aufgeführt. Weitere Informationen finden Sie unter [Grundlegendes zu Azure-Gebühren für externe Dienste](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Nach Nutzungsverbrauchseinheiten in Rechnung gestellte Ressourcen

Azure wird nicht direkt nach Ressourcenkosten abgerechnet. Die Gebühren für eine Ressource werden mithilfe von Verbrauchseinheiten berechnet. Verbrauchseinheiten werden zum Nachverfolgen der Nutzung einer Ressource über die Dauer ihres Lebenszyklus verwendet. Diese Verbrauchseinheiten werden dann zum Berechnen der Rechnung verwendet.

Wenn Sie beispielsweise eine einzelne Azure-Ressource erstellen, z.B. einen virtuellen Computer, werden dafür eine oder mehrere Verbrauchseinheiten erstellt. Verbrauchseinheiten werden verwendet, um die Nutzung der Ressource im zeitlichen Verlauf nachzuverfolgen. Jede Verbrauchseinheit gibt Verwendungsdatensätze aus, die von Azure zum Berechnen der Rechnung verwendet werden.

Für einen in Azure erstellten einzelnen virtuellen Computer können beispielsweise die folgenden Verbrauchseinheiten erstellt werden, um seine Verwendung nachzuverfolgen:

- Computestunden
- IP-Adressstunden
- Eingehende Datenübertragung
- Ausgehende Datenübertragung
- Verwalteter Datenträger Standard
- Managed Disks Standard-Vorgänge
- Standard-E/A – Datenträger
- Standard-E/A – Blockblob-Lesevorgangseinheiten
- Standard-E/A – Blockblob-Schreibvorgangseinheiten
- Standard-E/A – Blockblob-Löschvorgangseinheiten

Nachdem der virtuelle Computer erstellt wurde, beginnen diese Verbrauchseinheiten mit der Ausgabe von Nutzungsdatensätzen. Diese Nutzung und der Preis der Verbrauchseinheit werden im Azure-System zur Messung von Verbrauchseinheiten nachverfolgt.

## <a name="payment"></a>Bezahlen Ihrer Rechnung

Wenn Sie eine Kreditkarte oder Debitkarte als Zahlungsmethode eingerichtet haben, wird die Zahlung zehn Tage nach Abschluss des Abrechnungszeitraums automatisch berechnet. Auf Ihrem Kreditkartenbeleg lautet die Postenzeile dann **MSFT Azure**.

Informationen zum Ändern der abgerechneten Kredit- oder Debitkarte finden Sie unter [Hinzufügen, Aktualisieren oder Entfernen einer Kredit- oder Debitkarte für Azure](billing-how-to-change-credit-card.md).

Wenn Sie [auf Rechnung zahlen](billing-how-to-pay-by-invoice.md), senden Sie Ihre Zahlung an den Empfänger, der unten auf der Rechnung angegeben ist.

Um den Status Ihrer Zahlung zu erfragen, [erstellen Sie ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tipps für das Kostenmanagement

- Schätzen Sie die Kosten mithilfe folgender Methoden:
  - [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)
  - [Gesamtkostenrechner](https://aka.ms/azure-tco-calculator)
  - [Detaillierte Preisinformationen für jeden Dienst](https://azure.microsoft.com/pricing/)
- [Überprüfen Sie Nutzung und Kosten regelmäßig im Azure-Portal](billing-getting-started.md#costs).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Weitere Informationen

- [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](billing-download-azure-invoice-daily-usage-date.md)
- [Grundlegendes über Benennungen in Ihrer Microsoft Azure-Rechnung](billing-understand-your-invoice.md)
- [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](billing-understand-your-usage.md)
- [Kostenverwaltung im Azure-Portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md#costs)
