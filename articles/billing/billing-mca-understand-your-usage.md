---
title: Begriffe in der CSV-Datei für die Azure-Nutzung und -Gebühren bei abgeschlossener Microsoft-Kundenvereinbarung | Microsoft-Dokumentation
description: Informationen zu den Abschnitten der CSV-Datei für die Azure-Nutzung und -Gebühren für Ihr Abrechnungsprofil
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371307"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Begriffe in der CSV-Datei für die Azure-Nutzung und -Gebühren bei abgeschlossener Microsoft-Kundenvereinbarung

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben](#check-access-to-a-microsoft-customer-agreement).

Die CSV-Datei zur Azure-Nutzung und den Azure-Gebühren enthält die tägliche Nutzung und die Nutzung auf Ebene von Verbrauchseinheiten für den aktuellen Abrechnungszeitraum.

Informationen zum Abrufen der Datei für die Azure-Nutzung und Gebühren finden Sie unter [Herunterladen oder Anzeigen von Rechnungen](billing-download-azure-daily-usage.md).
Sie ist als Datei mit kommagetrennten Werten (CSV) verfügbar, die Sie in einem Tabellenkalkulationsprogramm öffnen können.

Nutzungsgebühren sind die **monatlichen** Gesamtgebühren für ein Abonnement. Die Nutzungsgebühren berücksichtigen keine Guthaben oder Rabatte.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Änderungen an der CSV-Datei für die Azure-Nutzung und -Gebühren (Enterprise Agreement)

Wenn Sie Enterprise Agreement-Kunde sind, werden Sie feststellen, dass sich die Begriffe in der CSV-Datei für die Azure-Nutzung mit Abrechnungsprofil von den Begriffen in der CSV-Datei für die Azure-Nutzung mit Enterprise Agreement unterscheiden. Hier finden Sie eine Zuordnung der Begriffe für die Nutzung mit Enterprise Agreement zu den Begriffen für die Nutzung mit Abrechnungsprofil:

| CSV-Datei für die Azure-Nutzung mit Enterprise Agreement | CSV-Datei für die Azure-Nutzung und -Gebühren mit Microsoft-Kundenvereinbarung |
| --- | --- |
| Date | date |
| Month (Monat)| date |
| Day (Tag) | date |
| Jahr | date |
| Produkt | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice | <!-- this was highlighted -->
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| `Tags` | tags |
| StoreServiceIdentifier | – |
| Abteilungsname | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | Ressourcengruppe |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Begriffe und ausführliche Beschreibungen für die Datei für die Azure-Nutzung und -Gebühren

Im folgenden Abschnitt werden die wichtigen Begriffe in der Datei für die Azure-Nutzung und -Gebühren erläutert.

Begriff | BESCHREIBUNG
--- | ---
invoiceId | Die eindeutige Dokument-ID, die in der PDF-Datei für die Rechnung aufgeführt ist
previousInvoiceId | Verweis auf die ursprüngliche Rechnung, wenn es sich bei diesem Posten um eine Rückerstattung handelt
billingAccountName | Name des Abrechnungskontos
billingAccountId | Eindeutiger Bezeichner für das Stammabrechnungskonto
billingProfileId | Name des Abrechnungsprofils, für das die in Rechnung gestellten Gebühren anfallen
billingProfileName | Eindeutiger Bezeichner für das Abrechnungsprofil, für das die in Rechnung gestellten Gebühren anfallen
invoiceSectionId | Eindeutiger Bezeichner für den Rechnungsabschnitt
invoiceSectionName | Name des Rechnungsabschnitts
costCenter | Die Kostenstelle, die im Abonnement für die Nachverfolgung der Kosten festgelegt wurde (nur in offenen Abrechnungszeiträumen)
billingPeriodStartDate | Das Startdatum des Abrechnungszeitraums, für das die Rechnung generiert wird
billingPeriodEndDate | Das Enddatum des Abrechnungszeitraums, für das die Rechnung generiert wird
servicePeriodStartDate | Das Startdatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden
servicePeriodEndDate | Das Enddatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden
date | Das Bewertungsdatum für nutzungsbasierte Gebühren für Azure und Marketplace. Bei einmaligen Käufen (Reservierungen, Marketplace) oder festen laufenden Gebühren (Supportangebote) entspricht dies dem Kaufdatum.
serviceFamily | Die Dienstfamilie, zu der der Dienst gehört
productOrderId | Der eindeutige Bezeichner für den Produktauftrag
productOrderName | Der eindeutige Name für den Produktauftrag
consumedService | Der Name des genutzten Diensts
meterId | Der eindeutige Bezeichner für die Verbrauchseinheit
meterName | Der Name der Verbrauchseinheit
meterCategory | Name der Klassifizierungskategorie der Verbrauchseinheit. Beispiele: *Clouddienste*, *Netzwerk* usw.
meterSubCategory | Der Name der Unterklassifizierungskategorie der Verbrauchseinheit
meterRegion | Der Name der Region, in der die Verbrauchseinheit für den Dienst verfügbar ist. Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden.
offer | Der Name des erworbenen Angebots
productId | Der eindeutige Bezeichner für das Produkt, für das die Gebühren anfallen
product | Der Name des Produkts, für das die Gebühren anfallen
Abonnement-ID | Der eindeutige Bezeichner für das Abonnement, für das die Gebühren anfallen
subscriptionName | Der Name des Abonnements, für das die Gebühren anfallen
reservationId | Der eindeutige Bezeichner für die erworbene Reservierungsinstanz
reservationName | Der Name der erworbenen Reservierungsinstanz
publisherType | Die Art des Herausgebers (Werte: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Der Herausgeber für Marketplace-Dienste
resourceGroupId | Der eindeutiger Bezeichner für die der Ressourcengruppe zugeordnete Ressource
resourceGroupName | Der Name der Ressourcengruppe, die der Ressource zugeordnet ist
resourceId | Der eindeutige Bezeichner für die Ressourceninstanz
resourceType | Der Ressourceninstanztyp
resourceLocation | Gibt den Standort des Rechenzentrums an, in dem die Ressource ausgeführt wird
location | Normalisierter Standort der Ressource, wenn mehrere Ressourcenstandorte für dieselben Regionen konfiguriert wurden
quantity | Die Anzahl der erworbenen oder genutzten Einheiten
unitOfMeasure | Die Abrechnungsmaßeinheiten für den Dienst. Computedienste werden beispielsweise pro Stunde abgerechnet.
chargeType | Die Art der Gebühr Werte: <ul><li>AsCharged-Usage: Gebühren fallen entsprechend der Nutzung eines Azure-Diensts an. Dazu zählt die Nutzung mit VMs, die aufgrund von reservierten Instanzen nicht in Rechnung gestellt werden.</li><li>AsCharged-PurchaseMarketplace: Einmalige oder feste laufende Gebühren für Marketplace-Käufe</li><li>AsCharged-UsageMarketplace: Gebühren für Marketplace-Dienste, deren Gebühren anhand der Verbrauchseinheiten berechnet werden</li></ul>
isAzureCreditEligible | Ein Flag, das angibt, ob die Gebühr für den Dienst mit einer Azure-Gutschrift bezahlt werden kann (Werte: True, False)
serviceInfo1 | Dienstspezifische Metadaten
serviceInfo2 | Ein Legacyfeld, in dem optionale dienstspezifische Metadaten erfasst werden
additionalInfo | Weitere dienstspezifische Metadaten
tags | Die Tags, die Sie der Ressource zuweisen

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Wie stelle ich sicher, dass die aufgeführten Gebühren in der Datei für die Azure-Nutzung und -Gebühren richtig sind?

Wenn Sie mehr über eine Gebühr in Ihrer Datei mit detaillierten Nutzungsdaten erfahren möchten, lesen Sie den Abschnitt [Grundlegendes zu den Gebühren auf der Rechnung für eine Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](billing-download-azure-invoice.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](billing-download-azure-daily-usage.md)
