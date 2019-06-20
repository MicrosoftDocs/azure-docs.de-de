---
title: Grundlegendes zu detaillierter Nutzung und Gebühren | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre detaillierte Nutzung und Gebühren lesen und verstehen.
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683985"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Grundlegendes zu den Bedingungen in der Datei für die Azure-Nutzung und -Gebühren

Die Datei zu detaillierter Nutzung und Gebühren enthält die tägliche bewertete Nutzung basierend auf vereinbarten Tarifen, Einkäufen (z.B. Reservierungen, Marketplace-Gebühren) und Rückerstattungen für den angegebenen Zeitraum.
Die Gebühren beinhalten keine Gutschriften, Steuern oder andere Gebühren und Rabatte.
Die folgende Tabelle zeigt, welche Gebühren für jeden Kontotyp enthalten sind.

Kontotyp | Azure-Nutzung | Marketplace-Nutzung | Einkäufe | Rückerstattungen
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Ja | Ja | Ja | Nein
Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) | Ja | Ja | Ja | Ja
Nutzungsbasierte Bezahlung (Pay-as-you-go, PAYG) | Ja | Nein | Nein | Nein

Weitere Informationen zu Marketplace-Bestellungen (auch als externe Dienstleistungen bezeichnet) finden Sie unter [Grundlegendes zu Azure-Gebühren für externe Dienste](billing-understand-your-azure-marketplace-charges.md).

Anweisungen für den Download finden Sie unter [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](billing-download-azure-invoice-daily-usage-date.md).
Die Datei für die Nutzung und Gebühren ist im Dateiformat mit kommagetrennten Werten (CSV) verfügbar, das Sie in einem Tabellenkalkulationsprogramm öffnen können.

## <a name="list-of-terms-and-descriptions"></a>Liste der Begriffe und Beschreibungen

In der folgenden Tabelle werden die wichtigen Begriffe in der neuesten Version der Datei für die Azure-Nutzung und -Gebühren erläutert.
Die Liste umfasst Konten für nutzungsbasierte Bezahlung (Pay-as-you-go, PAYG), Enterprise Agreement (EA) und Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA).

Begriff | Kontotyp | BESCHREIBUNG
--- | --- | ---
AccountName | EA | Anzeigename des Registrierungskontos.
AccountOwnerId | EA | Eindeutiger Bezeichner für das Registrierungskonto.
AdditionalInfo | Alle | Dienstspezifische Metadaten. Dies kann beispielsweise ein Imagetyp für einen virtuellen Computer sein.
BillingAccountId | EA, MCA | Eindeutiger Bezeichner für das Stammabrechnungskonto.
BillingAccountName | EA, MCA | Name des Abrechnungskontos.
BillingCurrency | EA, MCA | Dem Abrechnungskonto zugeordnete Währung.
BillingPeriod | EA | Abrechnungszeitraum der Gebühren.
BillingPeriodEndDate | EA, MCA | Enddatum des Abrechnungszeitraums.
BillingPeriodStartDate | EA, MCA | Startdatum des Abrechnungszeitraums.
BillingProfileId | EA, MCA | Eindeutiger Bezeichner des EA-Registrierungs- oder MCA-Abrechnungsprofils.
BillingProfileName | EA, MCA | Name des EA-Registrierungs- oder MCA-Abrechnungsprofils.
ChargeType | EA, MCA | Gibt an, ob die Gebühren die Nutzung (**Nutzung**), einen Kauf (**Einkauf**) oder eine Erstattung (**Rückerstattung**) darstellt.
ConsumedQuantity | Nutzungsbasierte Zahlung | Siehe „Quantity“.
ConsumedService | Alle | Name des Diensts, dem die Gebühren zugeordnet sind.
Kosten | EA | Siehe „CostInBillingCurrency“.
CostCenter | EA, MCA | Die Kostenstelle, die im Abonnement für die Nachverfolgung der Kosten festgelegt wurde (nur in offenen Abrechnungszeiträumen für MCA-Konten verfügbar).
CostInBillingCurrency | MCA | Kosten der Gebühren in der Abrechnungswährung vor Gutschriften oder Steuern.
CostInPricingCurrency | MCA | Kosten der Gebühren in der Preiswährung vor Gutschriften oder Steuern.
Currency | Nutzungsbasierte Zahlung | Siehe „BillingCurrency“.
Date | EA, MCA | Verbrauchs- oder Kaufdatum der Gebühren.
ExchangeRateDate | MCA | Datum, an dem der Wechselkurs festgelegt wurde.
ExchangeRatePricingToBilling | MCA | Der für die Umrechnung der Kosten aus der Preiswährung in die Abrechnungswährung verwendete Wechselkurs.
Frequency | EA, MCA | Gibt an, ob eine Gebühr sich voraussichtlich wiederholt. Gebühren fallen entweder einmal (**OneTime**), wiederholt monatlich oder jährlich (**Recurring**) oder basierend auf der Nutzung (**UsageBased**) an.
IncludedQuantity | Nutzungsbasierte Zahlung | Die Menge der Verbrauchseinheit, die kostenlos in Ihrem Abrechnungszeitraum inbegriffen ist.
InstanceId | PAGY | Siehe „ResourceId“.
InvoiceId | EA, MCA | Die eindeutige Dokument-ID, die in der PDF-Datei für die Rechnung aufgeführt ist.
InvoiceSection | MCA | Siehe „InvoiceSectionName“.
InvoiceSectionId | EA, MCA | Eindeutiger Bezeichner für EA-Abteilung oder MCA-Rechnungsabschnitt.
InvoiceSectionName | EA, MCA | Name für EA-Abteilung oder MCA-Rechnungsabschnitt.
IsAzureCreditEligible | EA, MCA | Gibt an, ob die Gebühr mit einer Azure-Gutschrift bezahlt werden kann (Werte: True, False).
Location | EA, MCA | Standort des Rechenzentrums, in dem die Ressource ausgeführt wird.
MeterCategory | Alle | Name der Klassifizierungskategorie der Verbrauchseinheit. Beispiele: *Clouddienste* und *Netzwerk*.
MeterId | Alle | Der eindeutige Bezeichner für die Verbrauchseinheit.
MeterName | Alle | Der Name der Verbrauchseinheit.
MeterRegion | Alle | Der Name des Standorts des Rechenzentrums für auf Basis des Standorts abgerechnete Dienste. Siehe „Location“.
MeterSubCategory | Alle | Name der Unterklassifizierungskategorie der Verbrauchseinheit.
OfferId | EA, MCA | Der Name des erworbenen Angebots.
PartNumber | EA | Der Bezeichner, der verwendet wird, um bestimmte Verbrauchseinheitspreise zu erhalten.
PlanName | EA | Name des Marketplace-Plans.
PreviousInvoiceId | MCA | Verweis auf die ursprüngliche Rechnung, wenn es sich bei diesem Posten um eine Rückerstattung handelt.
PricingCurrency | MCA | Währung, die verwendet wird, wenn die Bewertung basierend auf vereinbarten Preisen erfolgt.
Produkt | MCA | Siehe „ProductName“.
ProductId | EA, MCA | Der eindeutige Bezeichner für das Produkt.
ProductName | EA | Der Name des Produkts.
ProductOrderId | EA, MCA | Der eindeutige Bezeichner für die Produktbestellung.
ProductOrderName | EA, MCA | Der eindeutige Name für die Produktbestellung.
PublisherName | EA, MCA | Herausgeber für Marketplace-Dienste.
PublisherType | EA, MCA | Art des Herausgebers (Werte: firstParty, thirdPartyReseller, thirdPartyAgency).
Menge | EA, MCA | Die Anzahl der erworbenen oder genutzten Einheiten.
Rate | Nutzungsbasierte Zahlung | Siehe „UnitPrice“.
ReservationId | EA, MCA | Der eindeutige Bezeichner für die erworbene Reservierungsinstanz.
ReservationName | EA, MCA | Der Name der erworbenen Reservierungsinstanz.
ResourceGroupId | EA, MCA | Der eindeutiger Bezeichner für die [Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), in der sich die Ressource befindet.
ResourceGroupName | EA, MCA | Name der [Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), in der sich die Ressource befindet.
resourceId | EA, MCA | Der eindeutige Bezeichner der [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources)-Ressource.
ResourceLocation | EA, MCA | Standort des Rechenzentrums, in dem die Ressource ausgeführt wird. Siehe „Location“.
ResourceName | EA | Der Name der Ressource.
ResourceType | MCA | Der Ressourceninstanztyp.
ServiceFamily | EA, MCA | Die Dienstfamilie, zu der der Dienst gehört.
ServiceInfo1 | Alle | Dienstspezifische Metadaten.
ServiceInfo2 | Alle | Ein Legacyfeld mit optionalen dienstspezifischen Metadaten.
ServicePeriodEndDate | MCA | Das Enddatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden.
ServicePeriodStartDate | MCA | Das Startdatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden.
SubscriptionId | Alle | Der eindeutige Bezeichner für das Abonnement.
SubscriptionName | Alle | Name des Abonnements.
`Tags` | Alle | Tags, die der Ressource zugewiesen werden. Umfasst keine Ressourcengruppen-Tags. Kann verwendet werden, um die Kosten für interne verbrauchsbasierte Kostenzuteilung zu gruppieren oder zu verteilen. Weitere Informationen finden Sie unter [Organisieren von Azure-Ressourcen mit Tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unit | Nutzungsbasierte Zahlung | Siehe „UnitOfMeasure“.
UnitOfMeasure | Alle | Die Abrechnungsmaßeinheiten für den Dienst. Computedienste werden beispielsweise pro Stunde abgerechnet.
UnitPrice | EA | Der Preis pro Einheit für die Gebühren.
UsageDate | Nutzungsbasierte Zahlung | Siehe „Date“.

Beachten Sie, dass einige Felder bei den verschiedenen Kontotypen in Groß-/Kleinschreibung und Abstand abweichen können.
Ältere Versionen von Pay-as-you-go-Nutzungsdateien verfügen über separate Abschnitte für Abrechnung und tägliche Nutzung.

## <a name="ensure-that-your-charges-are-correct"></a>Sicherstellen, dass Ihre Gebühren richtig sind

Weitere Informationen über detaillierte Nutzung und Gebühren finden Sie in den Grundlegenden Informationen zur Rechnung nach [nutzungsbasierter Bezahlung (Pay-as-you-go)](./billing-understand-your-bill.md) oder [Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](billing-download-azure-invoice.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](billing-download-azure-daily-usage.md)
