---
title: Migrieren von Enterprise Agreement-APIs zu APIs der Microsoft-Kundenvereinbarung – Azure | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen, die Folgen einer Migration von Microsoft Enterprise Agreement (EA) zu einer Microsoft-Kundenvereinbarung zu verstehen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: c3fb1f430076b26f7b5dd83e167371ac6d957ac4
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967241"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrieren von Enterprise Agreement-APIs zu APIs der Microsoft-Kundenvereinbarung

Dieser Artikel hilft Ihnen, die Unterschiede bezüglich Datenstruktur, API und anderer Aspekte der Systemintegration zwischen EA-Konten (Enterprise Agreement) und MCA-Konten (Microsoft-Kundenvereinbarung) zu verstehen. Azure Cost Management unterstützt APIs für beide Kontotypen. Lesen Sie den Artikel zum [Einrichten des Abrechnungskontos für eine Microsoft-Kundenvereinbarung](../billing/billing-mca-setup-account.md), bevor Sie fortfahren.

Organisationen mit einem bestehenden EA-Konto sollten sich diesen Artikel im Zusammenhang mit der Einrichtung eines MCA-Kontos ansehen. Zuvor waren zur Erneuerung eines EA-Kontos einige minimale Arbeitsschritte für den Wechseln von einer alten Registrierung zu einer neuen Registrierung erforderlich. Das Migrieren zu einem MCA-Konto erfordert jedoch zusätzlichen Aufwand. Der zusätzliche Aufwand ergibt sich durch Änderungen im zugrunde liegenden Abrechnungssubsystem, die sich auf alle kostenbezogenen APIs und Dienstangebote auswirken.

## <a name="mca-apis-and-integration"></a>MCA-APIs und Integration

MCA-APIs und die neue Integration bieten Ihnen folgende Möglichkeiten:

- Vollständige API-Verfügbarkeit durch native Azure-APIs
- Konfigurieren mehrerer Rechnungen in einem einzigen Abrechnungskonto
- Zugreifen auf eine kombinierte API mit Nutzung von Azure-Diensten, Nutzung des Marketplace von Drittanbietern und Marketplace-Einkäufen
- Anzeigen von Kosten für alle Abrechnungsprofile (entsprechen den Registrierungen) mit Azure Cost Management
- Zugreifen auf neue APIs zum Anzeigen von Kosten, Benachrichtigung, wenn Kosten vorab definierte Schwellenwerte überschreiten, und automatisches Exportieren von Rohdaten

## <a name="migration-checklist"></a>Migrationscheckliste

Die folgenden Punkte helfen Ihnen beim Übergang zu MCA-APIs.

- Machen Sie sich mit dem neuen [Abrechnungskonto für eine Microsoft-Kundenvereinbarung](../billing/billing-mca-overview.md) vertraut.
- Ermitteln Sie, welche APIs Sie verwenden, und sehen Sie im folgenden Abschnitt nach, welche ersetzt werden.
- Machen Sie sich mit [Azure Resource Manager-REST-APIs](/rest/api/azure) vertraut.
- Wenn Sie Azure Resource Manager-APIs noch nicht verwenden, [registrieren Sie Ihre Client-App in Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualisieren Sie Programmcode für die [Verwendung der Azure AD-Authentifizierung](/rest/api/azure/#create-the-request).
- Aktualisieren Sie Programmcode, sodass Aufrufe der EA-APIs durch Aufrufe der MCA-APIs ersetzt werden.
- Aktualisieren Sie die Fehlerbehandlung für die Verwendung neuer Fehlercodes.
- Überprüfen Sie zusätzliche Integrationsangebote, z.B. Cloudyn und Power BI, auf weitere erforderliche Aktionen.

## <a name="ea-apis-replaced-with-mca-apis"></a>Durch MCA-APIs ersetzte EA-APIs

EA-APIs verwenden einen API-Schlüssel zur Authentifizierung und Autorisierung. MCA-APIs verwenden die Azure AD-Authentifizierung.

| Zweck | EA-API | MCA-API |
| --- | --- | --- |
| Saldo und Guthaben | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Nutzung (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Nutzung (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-Nutzung (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Abrechnungszeiträume | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Preisblatt | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Reservierungseinkäufe | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Reservierungsempfehlungen | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reservierungsnutzung | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Nutzung von Azure-Diensten und Marketplace eines Drittanbieters sind über der [Nutzungsdetails-API](/rest/api/consumption/usagedetails) verfügbar.

Die folgenden APIs stehen für MCA-Abrechnungskonten zur Verfügung:

| Zweck | MCA-API (Microsoft-Kundenvereinbarung) |
| --- | --- |
| Abrechnungskonten<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Abrechnungsprofile<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Rechnungsabschnitte<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Invoices | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Abrechnungsabonnements | {scope}/billingSubscriptions |

<sup>2</sup> APIs geben Objektlisten zurück. Hierbei handelt es sich um Bereiche, in denen Cost Management-Funktionen im Azure-Portal und in APIs ausgeführt werden. Weitere Informationen zu Cost Management-Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

Wenn Sie vorhandene EA-APIs verwenden, müssen Sie diese für die Unterstützung von MCA-Abrechnungskonten aktualisieren. In der folgenden Tabelle sind weitere Integrationsänderungen angegeben:

| Zweck | Altes Angebot | Neues Angebot |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)-Inhaltspaket und -Connector | [Microsoft Azure Consumption Insights Power BI-App](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) und [Azure Consumption Insights-Connector](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs zum Abrufen von Saldo und Guthaben

Die [API zum Abrufen der Saldozusammenfassung](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) bietet Ihnen eine monatliche Übersicht über:

- Bilanzen
- Neue Einkäufe
- Gebühren für den Azure Marketplace-Dienst
- Adjustments
- Gebühren für Dienstüberschreitung

Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request).

Die API zum Abrufen der Saldozusammenfassung wird durch die Microsoft.Billing/billingAccounts/billingProfiles/availableBalance-API ersetzt.

Zum Abrufen verfügbarer Salden mit der API für verfügbare Salden gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs zum Abrufen von Kosten und Nutzung

Mit den folgenden APIs können Sie eine tägliche Aufschlüsselung der Kosten für die Nutzung der Azure-Dienste, des Marketplace von Drittanbietern und andere Marketplace-Einkäufe abrufen. Die folgenden getrennten APIs wurden für die Nutzung der Azure-Dienste und des Marketplace von Drittanbietern zusammengeführt. Die alten APIs werden durch die [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)-API ersetzt. Diese umfasst auch Marketplace-Einkäufe, die zuvor nur in der aktuellen Saldozusammenfassung angezeigt wurden.

- [Abrufen von Nutzungsdetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Abrufen von Nutzungsdetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Abrufen von Nutzungsdetails/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Abrufen von Nutzungsdetails/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Abrufen von Marketplace Store-Gebühren/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Abrufen von Marketplace Store-Gebühren/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request).

Alle vorherigen APIs werden durch die Nutzungs-/Nutzungsdetails-API ersetzt.

Zum Abrufen von Nutzungsdetails mit der Nutzungsdetails-API gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Die Nutzungsdetails-API ist wie alle Cost Management-APIs in mehreren Bereichen verfügbar. Verwenden Sie für berechnete Kosten, die Sie normalerweise auf Registrierungsebene erhalten würden, den Bereich des Abrechnungsprofils.  Weitere Informationen zu Cost Management-Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

| Type | ID-Format |
| --- | --- |
| Abrechnungskonto | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Abrechnungsprofil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonnement | `/subscriptions/{subscriptionId}` |
| Ressourcengruppe | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Verwenden Sie die folgenden QueryString-Parameter, um Programmcode zu aktualisieren.

| Alte Parameter | Neue Parameter |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nicht unterstützt |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Der Antworttext hat sich ebenfalls geändert.

Alter Antworttext:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Neuer Antworttext:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Der Eigenschaftenname, der das Array von Nutzungsdatensätzen enthält, hat sich von „data“ in _values_ geändert. Jeder Datensatz wies bisher eine flache Liste mit detaillierten Eigenschaften auf. Nun sind jedoch alle Details in einer geschachtelten Eigenschaft mit dem Namen _properties_ enthalten, mit Ausnahme von Tags. Die neue Struktur ist mit anderen Azure-APIs konsistent. Einige Eigenschaftennamen haben sich geändert. Die folgende Tabelle zeigt die sich jeweils entsprechenden Eigenschaften.

| Alte Eigenschaft | Neue Eigenschaft | Notizen |
| --- | --- | --- |
| AccountId | – | Der Abonnementersteller wird nicht nachverfolgt. Verwenden Sie invoiceSectionId (entspricht departmentID). |
| AccountNameAccountOwnerId und AccountOwnerEmail | – | Der Abonnementersteller wird nicht nachverfolgt. Verwenden Sie invoiceSectionName (entspricht departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Beachten Sie, dass es sich bei diesen Eigenschaften um Gegensätze handelt. Wenn IsAzureCreditEnabled auf „true“ festgelegt ist, weist ChargesBilledSeparately den Wert „false“ auf. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Genaue Zeichenfolgenwerte können abweichen. |
| ConsumedServiceId | Keine | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date und usageStartDate | date | &nbsp;  |
| Day (Tag) | Keine | Analysiert den Tag ab Datum. |
| DepartmentId | invoiceSectionId | Genaue Werte abweichend. |
| Abteilungsname | invoiceSectionName | Genaue Zeichenfolgenwerte können abweichen. Konfigurieren Sie Rechnungsabschnitte ggf. entsprechend den Abteilungen. |
| ExtendedCost und Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | Keine | &nbsp;  |
| Location | location | &nbsp;  |
| MeterCategory | meterCategory | Genaue Zeichenfolgenwerte können abweichen. |
| MeterId | meterId | Genaue Zeichenfolgenwerte abweichend. |
| MeterName | meterName | Genaue Zeichenfolgenwerte können abweichen. |
| MeterRegion | meterRegion | Genaue Zeichenfolgenwerte können abweichen. |
| MeterSubCategory | meterSubCategory | Genaue Zeichenfolgenwerte können abweichen. |
| Month (Monat) | Keine | Analysiert den Monat ab Datum. |
| Angebotsname | Keine | Verwenden Sie publisherName und productOrderName. |
| OfferId | Keine | &nbsp;  |
| Order Number | Keine | &nbsp;  |
| PartNumber | Keine | Verwenden Sie meterId und productOrderName zur eindeutigen Identifizierung von Preisen. |
| Plan Name | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | Genaue Zeichenfolgenwerte abweichend. |
| Name des Herausgebers | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Genaue Zeichenfolgenwerte abweichend. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Keine | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | – | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Genaue Zeichenfolgenwerte können abweichen. |
| ServiceTier | meterSubCategory | Genaue Zeichenfolgenwerte können abweichen. |
| StoreServiceIdentifier | – | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| `Tags` | tags | Die tags-Eigenschaft gilt für das Stammobjekt und nicht für die geschachtelte properties-Eigenschaft. |
| UnitOfMeasure | unitOfMeasure | Genaue Zeichenfolgenwerte abweichend. |
| usageEndDate | date | &nbsp;  |
| Jahr | Keine | Analysiert das Jahr ab Datum. |
| (neu) | billingCurrency | Die für die Gebühr verwendete Währung. |
| (neu) | billingProfileId | Eindeutige ID für das Abrechnungsprofil (entspricht der Registrierung). |
| (neu) | billingProfileName | Name des Abrechnungsprofils (entspricht der Registrierung). |
| (neu) | chargeType | Wird zur Unterscheidung zwischen Nutzung der Azure-Dienste, Marketplace-Nutzung und Einkäufen verwendet. |
| (neu) | invoiceId | Eindeutige ID für die Rechnung. Für den aktuellen, offenen Monat leer. |
| (neu) | publisherType | Der Typ des Herausgebers für Einkäufe. Zur Verwendung leer. |
| (neu) | serviceFamily | Typ des Einkaufs. Zur Verwendung leer. |
| (neu) | servicePeriodEndDate | Enddatum für den erworbenen Dienst. |
| (neu) | servicePeriodStartDate | Startdatum für den erworbenen Dienst. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API für Abrechnungszeiträume durch Rechnungen-API ersetzt

MCA-Abrechnungskonten verwenden keine Abrechnungszeiträume. Stattdessen werden Rechnungen verwenden, um Kosten auf bestimmte Abrechnungszeiträume zu begrenzen. Die [API für Abrechnungszeiträume](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) wird durch die Rechnungen-API ersetzt. Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request).

Zum Abrufen von Rechnungen mit der Rechnungen-API gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Preisblatt-APIs

In diesem Abschnitt werden vorhandene Preisblatt-APIs erläutert und Empfehlungen für den Übergang zur Preisblatt-API für Microsoft-Kundenvereinbarungen gegeben. Außerdem werden die Preisblatt-API für Microsoft-Kundenvereinbarungen sowie die Felder auf den Preisblättern erläutert. Die APIs zum [Abrufen des Preisblatts für Unternehmen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) und zum [Abrufen der Abrechnungszeiträume für Unternehmen](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) werden durch die Preisblatt-API für Microsoft-Kundenvereinbarungen (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet) ersetzt. Die neue API unterstützt sowohl JSON- als auch CSV-Formate in asynchronen REST-Formaten. Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Abrechnungs-APIs für Unternehmen

Bei Enterprise-Registrierungen wurden Abrechnungs-APIs für Unternehmen verwendet, um Informationen zu Preisen und Abrechnungszeiträumen zu erhalten. Für die Authentifizierung und Autorisierung wurden Azure Active Directory-Webtoken verwendet.

Zum Abrufen der geltenden Preise für die angegebene Enterprise-Registrierung mit den APIs für das Preisblatt und Abrechnungszeiträume gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Preisblatt-API für Microsoft-Kundenvereinbarungen

Verwenden Sie die Preisblatt-API für Microsoft-Kundenvereinbarungen, um Preise für alle Azure- und Marketplace-Nutzungsdienste anzuzeigen. Die für das Abrechnungsprofil angezeigten Preise gelten für alle Abonnements, die dem Abrechnungsprofil angehören.

Verwenden Sie die Preisblatt-API, um alle Preisblattdaten für Azure-Nutzungsdienste im CSV-Format anzuzeigen:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Verwenden Sie die Preisblatt-API, um alle Preisblattdaten für Azure-Nutzungsdienste im JSON-Format anzuzeigen:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Bei Verwendung der API wird das Preisblatt für das gesamte Konto zurückgegeben. Sie können aber auch eine verkürzte Version des Preisblatts im PDF-Format abrufen. Die Zusammenfassung enthält Azure- und Marketplace-Nutzungsdienste, die für eine bestimmte Rechnung berechnet werden. Die Rechnung wird durch die {InvoiceId} identifiziert, die der **Rechnungsnummer** entspricht, die in den PDF-Dateien der Rechnungszusammenfassung angegeben ist. Hier sehen Sie ein Beispiel.

![Beispielabbildung der Rechnungsnummer, die InvoiceId entspricht](./media/migrate-cost-management-api/invoicesummary.png)

Zum Anzeigen von Rechnungsinformationen mit der Preisblatt-API im CSV-Format gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Zum Anzeigen von Rechnungsinformationen mit der Preisblatt-API im JSON-Format gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Sie können auch geschätzte Preise für jeden Azure- oder Marketplace-Nutzungsdienst im aktuellen offenen Abrechnungszyklus oder im Dienstzeitraum anzeigen.

Zum Anzeigen geschätzter Preise für Nutzungsdienste mit der Preisblatt-API im CSV-Format gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Zum Anzeigen geschätzter Preise für Nutzungsdienste mit der Preisblatt-API im JSON-Format gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Die Preisblatt-APIs für Microsoft-Kundenvereinbarungen sind *asynchrone REST-APIs*. Die Antworten für die APIs haben sich gegenüber den älteren synchronen APIs geändert. Der Textkörper der API-Antwort hat sich ebenfalls geändert.

#### <a name="old-response-body"></a>Alter Antworttext

Hier sehen Sie ein Beispiel für die Antwort einer synchronen REST-API:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Neuer Antworttext

Die APIs unterstützen das [asynchrone Azure REST-Format](../azure-resource-manager/resource-manager-async-operations.md). Rufen Sie die API mit GET auf, und Sie erhalten die folgende Antwort:

```
No Response Body

HTTP Status 202 Accepted
```

Die folgenden Header werden mit dem Speicherort der Ausgabe gesendet:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Führen Sie einen weiteren GET-Aufruf für den Speicherort aus. Die Antwort auf den GET-Aufruf ist immer gleich, bis der Vorgang einen Abschluss- oder Fehlerstatus erreicht. Nach dem Abschluss gibt die Antwort auf den GET-Aufruf für den Speicherort die Download-URL zurück. Ganz so, als wäre der Vorgang gleichzeitig ausgeführt worden. Hier sehen Sie ein Beispiel:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Der Client kann auch einen GET-Aufruf für `Azure-AsyncOperation` starten. Der Endpunkt gibt den Status für den Vorgang zurück.

In der folgenden Tabelle sind Felder in der älteren API zum Abrufen des Preisblatts für Unternehmen aufgelistet. Die Tabelle enthält auch die entsprechenden Felder im neuen Preisblatt für Microsoft-Kundenvereinbarungen:

| Alte Eigenschaft | Neue Eigenschaft | Notizen |
| --- | --- | --- |
| billingPeriodId  | _Nicht zutreffend_ | Nicht zutreffend Bei Microsoft-Kundenvereinbarungen wurde das Konzept der billingPeriodId durch die Rechnung und das zugehörige Preisblatt ersetzt. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Genaue Zeichenfolgenwerte können abweichen. |
| includedQuantity  | includedQuantity | Gilt nicht für Dienste in Microsoft-Kundenvereinbarungen. |
| partNumber  | _Nicht zutreffend_ | Verwenden Sie stattdessen eine Kombination aus productOrderName (entspricht offerId) und meterid. |
| unitPrice  | unitPrice | Der Einzelpreis gilt für Dienste, die in Microsoft-Kundenvereinbarungen genutzt werden. |
| currencyCode  | pricingCurrency | Bei Microsoft-Kundenvereinbarungen werden Preise in Preis- und Abrechnungswährung dargestellt. CurrencyCode entspricht pricingCurrency in Microsoft-Kundenvereinbarungen. |
| offerId | productOrderName | Anstelle von OfferId können Sie productOrderName verwenden, doch ist dies nicht mit OfferId identisch. ProductOrderName und meter bestimmen jedoch die Preise in Microsoft-Kundenvereinbarungen in Bezug auf meterId und Offerid in Legacyregistrierungen. |

## <a name="consumption-price-sheet-api-operations"></a>Vorgänge der Preisblatt-API für die Nutzung

Bei Enterprise Agreements haben Sie die Vorgänge [Abrufen](/rest/api/consumption/pricesheet/get) und [Abrufen nach Abrechnungszeitraum](/rest/api/consumption/pricesheet/getbybillingperiod) der Preisblatt-API für die Nutzung für einen Bereich nach subscriptionId oder einen Abrechnungszeitraum verwendet. Die API verwendet die Azure Resource Manager-Authentifizierung.

Zum Abrufen der Preisblattinformationen für einen Bereich mit der Preisblatt-API gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Zum Abrufen von Preisblattinformationen nach Abrechnungszeitraum mit der Preisblatt-API gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Verwenden Sie anstelle der oben genannten API-Endpunkte die nachfolgend aufgeführten für Microsoft-Kundenvereinbarungen:

**Preisblatt-API für Microsoft-Kundenvereinbarungen (asynchrone REST-API)**

Diese API wird für Microsoft-Kundenvereinbarungen verwendet und bietet zusätzliche Attribute.

**Preisblatt für einen Bereich des Abrechnungsprofils in einem Abrechnungskonto**

Diese API ist die vorhandene API. Sie wurde aktualisiert, um das Preisblatt für ein Abrechnungsprofil in einem Abrechnungskonto bereitzustellen.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Preisblatt für einen Bereich nach Abrechnungskonto

Die Azure Resource Manager-Authentifizierung wird verwendet, wenn Sie das Preisblatt für den Bereich der Registrierung in einem Abrechnungskonto abrufen.

Zum Abrufen des Preisblatts für den Bereich der Registrierung in einem Abrechnungskonto gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Verwenden Sie für eine Microsoft-Kundenvereinbarung die Informationen im folgenden Abschnitt. Dort sind die Feldeigenschaften angegeben, die für Microsoft-Kundenvereinbarungen verwendet werden.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Preisblatt für einen Bereich des Abrechnungsprofils in einem Abrechnungskonto

Beim aktualisierten Preisblatt nach Abrechnungskonto-API wird das Preisblatt im CSV-Format abgerufen. Zum Abrufen des Preisblatts für den Bereich des Abrechnungsprofils für eine MCA gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Im Bereich der EA-Registrierung sind die API-Antwort und Eigenschaften identisch. Die Eigenschaften entsprechen den jeweiligen MCA-Eigenschaften.

Die älteren Eigenschaften für [Azure Resource Manager-Preisblatt-APIs](/rest/api/consumption/pricesheet) und die entsprechenden neuen Eigenschaften sind in der folgenden Tabelle enthalten.

| Alte Eigenschaft der Azure Resource Manager-Preisblatt-API  | Neue Eigenschaft der Preisblatt-API für Microsoft-Kundenvereinbarungen   | BESCHREIBUNG |
| --- | --- | --- |
| Meter ID | _meterId_ | Eindeutiger Bezeichner für die Verbrauchseinheit. Entspricht meterId. |
| Meter name | meterName | Name der Verbrauchseinheit. Die Verbrauchseinheit stellt die bereitstellbare Ressource eines Azure-Diensts dar. |
| Meter category  | service | Name der Klassifizierungskategorie der Verbrauchseinheit. Entspricht dem Dienst auf dem Preisblatt für die Microsoft-Kundenvereinbarung. Genaue Zeichenfolgenwerte abweichend. |
| Meter subcategory | meterSubCategory | Name der Unterklassifizierungskategorie der Verbrauchseinheit. Basierend auf der Klassifizierung der Unterschiede beim allgemeinen Funktionsumfang im Dienst, z.B. SQL-Datenbank (Basic) oder SQL-Datenbank (Standard). |
| Meter Region | meterRegion | &nbsp;  |
| Unit | _Nicht zutreffend_ | Kann aus unitOfMeasure analysiert werden. |
| Unit of measure | unitOfMeasure | &nbsp;  |
| Part number | _Nicht zutreffend_ | Verwenden Sie anstelle von partNumber die Eigenschaften productOrderName und MeterId zur eindeutigen Identifizierung des Preises für ein Abrechnungsprofil. Felder werden in der MCA-Rechnung anstelle von partNumber in MCA-Rechnungen aufgeführt. |
| Unit price | unitPrice | Einzelpreis der Microsoft-Kundenvereinbarung. |
| Currency code | pricingCurrency | Bei Microsoft-Kundenvereinbarungen werden Preise in Preis- und Abrechnungswährung dargestellt. Currency code entspricht pricingCurrency in Microsoft-Kundenvereinbarungen. |
| Included quantity | includedQuantity | Gilt nicht für Dienste in Microsoft-Kundenvereinbarungen. Wird mit Nullwerten angezeigt. |
|  Offer Id  | productOrderName | Verwenden Sie anstelle von OfferId die Eigenschaft productOrderName. Ist nicht mit OfferId identisch, doch bestimmen productOrderName und meter die Preise in Microsoft-Kundenvereinbarungen. Bezieht sich auf meterId und Offerid in Legacyregistrierungen. |

Der Preis für Microsoft-Kundenvereinbarungen ist anders definiert als bei Enterprise Agreements. Der Preis für Dienste bei der Enterprise-Registrierung ist für Produkt, Teilenummer, Verbrauchseinheit und Angebot eindeutig. Die Teilenummer wird bei Microsoft-Kundenvereinbarungen nicht verwendet.

Der Preis für den Azure-Nutzungsdienst, der Teil einer Microsoft-Kundenvereinbarung ist, ist für productOrderName und meterId eindeutig. Diese stellen die Verbrauchseinheit des Diensts und den Produktplan dar.

Zur Abstimmung zwischen dem Preisblatt und der Nutzung in der Nutzungsdetails-API können Sie productOrderName und meterId verwenden.

Benutzer, die über Rechte als Besitzer oder Mitwirkender des Abrechnungsprofils, Leseberechtigung für das Abrechnungsprofil und Rechte als Rechnungs-Manager verfügen, können das Preisblatt herunterladen.

Das Preisblatt enthält Preise für Dienste, deren Preis auf der Nutzung basiert. Die Dienste umfassen Azure-Nutzung und Marketplace-Nutzung. Der aktuelle Preis am Ende jedes Dienstzeitraums ist gesperrt und wird für die Nutzung in einem einzelnen Dienstzeitraum angewendet. Bei Azure-Nutzungsdiensten beträgt der Dienstzeitraum in der Regel einen Kalendermonat.

### <a name="retired-price-sheet-api-fields"></a>Nicht mehr verwendete Felder der Preisblatt-API

Die folgenden Felder sind entweder nicht in Preisblatt-APIs für Microsoft-Kundenvereinbarungen verfügbar oder diese weisen die gleichen Felder auf.

|Nicht mehr verwendetes Feld| BESCHREIBUNG|
|---|---|
| billingPeriodId | Nicht zutreffend. Entspricht InvoiceId für MCA. |
| offerId | Nicht zutreffend Entspricht productOrderName in MCA. |
| meterCategory  | Nicht zutreffend Entspricht Service in MCA. |
| unit | Nicht zutreffend Kann aus unitOfMeasure analysiert werden. |
| currencyCode | Entspricht pricingCurrency in MCA. |
| meterLocation | Entspricht meterRegion in MCA. |
| partNumber partnumber | Nicht zutreffend, da die Teilenummer in MCA-Rechnungen nicht aufgeführt wird. Verwenden Sie anstelle von partnumber die Kombination aus meterId und productOrderName zur eindeutigen Identifizierung von Preisen. |
| totalIncludedQuantity | Nicht zutreffend |
| pretaxStandardRate  | Nicht zutreffend |

## <a name="reservation-instance-charge-api-replaced"></a>API für reservierte Instanzgebühren ersetzt

Mit der [API für reservierte Instanzgebühren](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) können Sie Abrechnungstransaktionen für Reservierungseinkäufe abrufen. Die neue API umfasst alle Einkäufe, einschließlich Marketplace-Angeboten von Drittanbietern. Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request). Die API für reservierte Instanzgebühren wird durch die Transaktionen-API ersetzt.

Zum Abrufen von Reservierungserwerbtransaktionen mit der Transaktionen-API gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>APIs für Empfehlungen ersetzt

APIs für Empfehlungen reservierter Instanzeinkäufe stellen die Nutzung virtueller Computer innerhalb der letzten 7, 30 oder 60 Tage bereit. APIs geben auch Empfehlungen für den Reservierungserwerb. Hierzu gehören folgende Berechtigungen:

- [API für Empfehlungen zu freigegebenen reservierten Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API für Empfehlungen zu einzelnen reservierten Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request). Die zuvor aufgelisteten APIs für Reservierungsempfehlungen werden durch die [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list)-API ersetzt.

Zum Abrufen von Reservierungsempfehlungen mit der API für Reservierungsempfehlungen gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>APIs für Reservierungsnutzung ersetzt

Sie können die Reservierungsnutzung in einer Registrierung mit der API für die Nutzung reservierter Instanzen abrufen. Wenn mehr als eine reservierte Instanz in einer Registrierung vorhanden ist, können Sie auch die Nutzung aller Käufe reservierter Instanzen mit dieser API abrufen.

Hierzu gehören folgende Berechtigungen:

- [Nutzungsdetails zu reservierten Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Nutzungszusammenfassung zu reservierten Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alle Nutzungs-APIs werden durch native Azure-APIs ersetzt, die Azure AD zur Authentifizierung und Autorisierung verwenden. Weitere Informationen zum Aufrufen von Azure-REST-APIs finden Sie unter [Erste Schritte mit der REST-API](/rest/api/azure/#create-the-request). Die zuvor aufgelisteten APIs für Reservierungsempfehlungen werden durch die APIs [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) und [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) ersetzt.

Zum Abrufen von Reservierungsdetails mit der API für Reservierungsdetails gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Zum Abrufen von Reservierungszusammenfassungen mit der API für Reservierungszusammenfassungen gehen Sie folgendermaßen vor:

| Methode | Anforderungs-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Wechsel von Cloudyn zu Cost Management

Organisationen, die [Cloudyn](https://cloudyn.com) verwenden, sollten mit der Verwendung von [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) für alle Kostenmanagementanforderungen beginnen. Cost Management steht im Azure-Portal ohne Onboarding und mit einer Wartezeit von acht Stunden zur Verfügung. Weitere Informationen finden Sie in der [Dokumentation zur Kostenverwaltung](index.yml).

Azure Cost Management bietet Ihnen folgende Möglichkeiten:

- Zeigen Sie Kosten im zeitlichen Verlauf gegenüber einem vordefinierten Budget an. Analysieren Sie die täglichen Kostenmuster, um ungewöhnliche Ausgaben zu erkennen und zu vermeiden. Unterteilen Sie Kosten nach Tags, Ressourcengruppe, Dienst und Standort.
- Erstellen Sie Budgets, um Grenzwerte für Nutzung und Kosten festzulegen, und erhalten Sie Benachrichtigungen, wenn wichtige Schwellenwerte erreicht sind. Richten Sie Automation mit Aktionsgruppen ein, um benutzerdefinierte Ereignisse auszulösen und feste Grenzwerte zu Ihren Bedingungen zu erzwingen.
- Optimieren Sie Kosten und Nutzung anhand von Azure Advisor-Empfehlungen. Ermitteln Sie Einkaufsoptimierungen mit Reservierungen, verkleinern Sie nicht ausgelastete virtuelle Computer, und löschen Sie nicht verwendete Ressourcen, um innerhalb der Budgets zu bleiben.
- Planen Sie den Export von Kosten- und Nutzungsdaten, um täglich eine CSV-Datei in Ihrem Speicherkonto zu veröffentlichen. Automatisieren Sie die Integration in externe Systeme, um Abrechnungsdaten synchron und auf dem neuesten Stand zu halten.

## <a name="power-bi-integration"></a>Power BI-Integration

Wenn Sie Power BI für die Kostenberichterstattung verwenden, müssen Sie zu Folgendem wechseln:

- Microsoft Azure Consumption Insights Power BI-App
- Azure Consumption Insights-Desktop-Connector


Der Connector wird für Organisationen empfohlen, die größte Flexibilität wünschen. Es steht aber auch die Power BI-App für eine schnelle Einrichtung zur Verfügung.

- Installieren der [Microsoft Azure Consumption Insights Power BI-App](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Herstellen einer Verbindung mit dem Azure Consumption Insights-Connector](/power-bi/desktop-connect-azure-consumption-insights)

Das ältere Consumption Insights-Inhaltspaket und der Connector funktionierten auf Registrierungsebene und erforderten mindestens Lesezugriff. Die neue Consumption Insights Power BI-App und der neue Azure Consumption Insights-Connector sind für Benutzer mit Abrechnungsprofil verfügbar. Teams, die zusätzliche Optionen für die Prüfung von Kosten oder die Anzeige von Kosten für alle Abrechnungsprofile benötigen, sollten die [Kostenanalyse](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) im Azure-Portal verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Dokumentation zur Kostenverwaltung](index.yml), und erfahren Sie, wie Sie Azure-Ausgaben überwachen und steuern. Dort finden Sie auch Informationen zum Optimieren der Ressourcennutzung mithilfe der Kostenverwaltung.
