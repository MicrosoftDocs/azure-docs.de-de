---
title: Migrieren von der Unternehmensberichterstellung zu Azure Resource Manager-APIs
description: Dieser Artikel enthält eine Beschreibung der Unterschiede zwischen den Berichterstellungs-APIs und den Azure Resource Manager-APIs, der Migration zu den Azure Resource Manager-APIs und der neuen Funktionen, die für die neuen Azure Resource Manager-APIs verfügbar sind.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355815"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migrieren von der Unternehmensberichterstellung zu Azure Resource Manager-APIs

Dieser Artikel dient Entwicklern, die benutzerdefinierte Lösungen mit den [Azure-Berichterstellungs-APIs für Unternehmenskunden](../manage/enterprise-api.md) erstellt haben, als Hilfe beim Migrieren zu den Azure Resource Manager-APIs für die Kostenverwaltung (Cost Management). Die Dienstprinzipalunterstützung für die neueren Azure Resource Manager-APIs ist jetzt allgemein verfügbar. Azure Resource Manager-APIs befinden sich in der aktiven Entwicklung. Erwägen Sie die Migration zu diesen APIs, anstatt die älteren Azure-Berichterstellungs-APIs für Unternehmenskunden zu verwenden. Die älteren APIs werden als veraltet gekennzeichnet. Dieser Artikel enthält eine Beschreibung der Unterschiede zwischen den Berichterstellungs-APIs und den Azure Resource Manager-APIs, der Migration zu den Azure Resource Manager-APIs und der neuen Funktionen, die für die neuen Azure Resource Manager-APIs verfügbar sind.

## <a name="api-differences"></a>API-Unterschiede

Im Folgenden sind die Unterschiede zwischen den älteren Berichterstellungs-APIs für Unternehmenskunden und den neueren Azure Resource Manager-APIs beschrieben.

| **Verwenden Sie** | **Enterprise Agreement-APIs** | **Azure Resource Manager-APIs** |
| --- | --- | --- |
| Authentifizierung | API-Schlüssel, der im EA Portal (Enterprise Agreement) bereitgestellt wird | Azure AD-Authentifizierung (Azure Active Directory) mit Benutzertoken oder Dienstprinzipalen. Dienstprinzipale werden anstelle von API-Schlüsseln verwendet. |
| Bereiche und Berechtigungen | Alle Anforderungen sind im Registrierungsbereich enthalten. Anhand der Zuweisungen der API-Schlüsselberechtigungen wird ermittelt, ob Daten für die gesamte Registrierung, eine Abteilung oder ein bestimmtes Konto zurückgegeben werden. Keine Benutzerauthentifizierung. | Für Benutzer oder Dienstprinzipale werden Berechtigungen für den Zugriff auf den Registrierungs-, Abteilungs- oder Kontobereich zugewiesen. |
| URI-Endpunkt | https://consumption.azure.com | https://management.azure.com |
| Entwicklungsstatus | Im Wartungsmodus. Auf dem Weg zur Veraltung. | Aktive Entwicklung |
| Verfügbare APIs | Auf den derzeit verfügbaren Stand beschränkt | Es sind gleichwertige APIs als Ersatz für die einzelnen EA-APIs verfügbar. <p> Es sind auch zusätzliche [Cost Management-APIs](/rest/api/cost-management/) für Sie verfügbar, z. B.: <p> <ul><li>Budgets</li><li>Alerts<li>Exports</li></ul> |

## <a name="migration-checklist"></a>Migrationscheckliste

- Machen Sie sich mit den [Azure Resource Manager-REST-APIs](/rest/api/azure) vertraut.
- Ermitteln Sie unter [Zuordnung von EA-APIs zu neuen Azure Resource Manager-APIs](#ea-api-mapping-to-new-azure-resource-manager-apis), welche EA-APIs Sie verwenden und auf welche Azure Resource Manager-APIs umgestellt werden sollte.
- Konfigurieren Sie die Dienstautorisierung und -authentifizierung für die Azure Resource Manager-APIs.
  - Wenn Sie Azure Resource Manager-APIs noch nicht verwenden, [registrieren Sie Ihre Client-App in Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Bei der Registrierung wird ein Dienstprinzipal erstellt, den Sie zum Abrufen der APIs verwenden können.
  - Weisen Sie für den Dienstprinzipal wie unten beschrieben die Berechtigungen für den Zugriff auf die erforderlichen Bereiche zu.
  - Aktualisieren Sie den Programmcode so, dass die [Azure AD-Authentifizierung](/rest/api/azure/#create-the-request) für Ihren Dienstprinzipal genutzt wird.
- Testen Sie die APIs, und aktualisieren Sie dann den gesamten Programmcode, um Aufrufe der EA-APIs durch Aufrufe der Azure Resource Manager-APIs zu ersetzen.
- Aktualisieren Sie die Fehlerbehandlung für die Verwendung neuer Fehlercodes. Einige zu berücksichtigende Aspekte:
  - Azure Resource Manager-APIs verfügen über ein Zeitlimit von 60 Sekunden.
  - Für Azure Resource Manager-APIs wird eine Ratenbegrenzung verwendet. Dies führt zu einem Drosselungsfehler vom Typ 429, wenn die Raten überschritten werden. Erstellen Sie Ihre Lösungen so, dass nicht zu viele API-Aufrufe in einem kurzen Zeitraum durchgeführt werden.
- Sehen Sie sich die anderen Cost Management-APIs an, die unter Azure Resource Manager verfügbar sind, und bewerten Sie sie im Hinblick auf eine spätere Nutzung. Weitere Informationen finden Sie unter [Verwenden zusätzlicher Cost Management-APIs](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Zuweisen des Dienstprinzipalzugriffs auf Azure Resource Manager-APIs

Nachdem Sie einen Dienstprinzipal zum programmgesteuerten Aufrufen der Azure Resource Manager-APIs erstellt haben, müssen Sie dafür die richtigen Berechtigungen für die Autorisierung und die Ausführung von Anforderungen in Azure Resource Manager zuweisen. Es sind zwei Berechtigungsframeworks für unterschiedliche Szenarien vorhanden.

### <a name="azure-billing-hierarchy-access"></a>Zugriff auf Azure-Abrechnungshierarchie

Verwenden Sie zum Zuweisen von Dienstprinzipalberechtigungen zu den Bereichen „Abrechnungskonto des Unternehmens“, „Abteilungen“ oder „Registrierungskonto“ die APIs vom Typ [Berechtigungen für die Abrechnung](/rest/api/billing/2019-10-01-preview/billingpermissions), [Definitionen von Abrechnungsrollen](/rest/api/billing/2019-10-01-preview/billingroledefinitions) und [Zuweisungen von Abrechnungsrollen](/rest/api/billing/2019-10-01-preview/billingroleassignments).

- Verwenden Sie die APIs vom Typ „Berechtigungen für die Abrechnung“, zum Identifizieren der Berechtigungen, über die ein Dienstprinzipal für einen bestimmten Bereich, z. B. ein Abrechnungskonto oder eine Abteilung, bereits verfügt.
- Verwenden Sie die APIs vom Typ „Definitionen von Abrechnungsrollen“ zum Enumerieren der verfügbaren Rollen, die Ihrem Dienstprinzipal zugewiesen werden können.
  - Derzeit können Dienstprinzipalen nur die Rollen vom Typ „Unternehmensadministrator ohne Schreibberechtigungen“ und „Abteilungsadministrator ohne Schreibberechtigungen“ zugewiesen werden.
- Verwenden Sie die APIs vom Typ „Zuweisungen von Abrechnungsrollen“, um Ihrem Dienstprinzipal eine Rolle zuzuweisen.

Im folgenden Beispiel wird veranschaulicht, wie Sie die API für Rollenzuweisungen aufrufen, um für einen Dienstprinzipal den Zugriff auf Ihr Abrechnungskonto zu gewähren. Wir empfehlen die Verwendung von [Postman](https://postman.com), um diese einmaligen Berechtigungskonfigurationen durchzuführen.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Anforderungstext

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Die neue Dienstprinzipalunterstützung gilt auch für Azure-spezifische Bereiche, z. B. Verwaltungsgruppen, Abonnements und Ressourcengruppen. Sie können diesen Bereichen Dienstprinzipalberechtigungen zuweisen, indem Sie direkt das [Azure-Portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) oder [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role) verwenden.

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Zuordnung von EA-APIs zu neuen Azure Resource Manager-APIs

Ermitteln Sie anhand der Tabelle unten Ihre derzeit verwendeten EA-APIs und die entsprechenden Azure Resource Manager-APIs, die als Ersatz dienen.

| **Szenario** | **EA-APIs** | **Azure Resource Manager-APIs** |
| --- | --- | --- |
| Saldozusammenfassung | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Preisblatt | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet): Verwendung für vereinbarte Preise <p> [API für Einzelhandelspreise](/rest/api/cost-management/retail-prices/azure-retail-prices): Verwendung für Einzelhandelspreise |
| Details zu reservierten Instanzen | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Zusammenfassung reservierter Instanzen | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Empfehlungen für reservierte Instanzen | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Gebühren für reservierte Instanzen | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Migrationsdetails nach API

In den folgenden Abschnitten sind Beispiele für alte API-Anforderungen und die neuen Ersatz-APIs aufgeführt.

### <a name="balance-summary-api"></a>Saldozusammenfassungs-API

Verwenden Sie die folgenden Anforderungs-URIs beim Aufrufen der neuen Saldozusammenfassungs-API. Ihre Registrierungsnummer sollte als billingAccountId verwendet werden.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

[Abrufen für Registrierung](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Änderungen des Antworttexts

_Alter Antworttext_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Neuer Antworttext_:

Die gleichen Daten sind jetzt im Feld `properties` der neuen API-Antwort verfügbar. Unter Umständen weisen einige Feldnamen eine etwas andere Schreibweise auf.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Preisblatt

Verwenden Sie die folgenden Anforderungs-URIs beim Aufrufen der neuen Preisblatt-API.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

 Sie können die API mit den folgenden Bereichen aufrufen:

- Registrierung: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Abonnement: `subscriptions/{subscriptionId}`

[_Abrufen für aktuellen Abrechnungszeitraum_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Abrufen für angegebenen Abrechnungszeitraum_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Änderungen des Antworttexts

_Alter Antworttext_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
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
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Neuer Antworttext_:

Die alten Daten befinden sich jetzt im Feld `pricesheets` der neuen API-Antwort. Informationen zu den Details der Verbrauchseinheit sind ebenfalls vorhanden.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Nutzungsdetails zu reservierten Instanzen

Bei Microsoft wird nicht aktiv an APIs für Reservierungsdetails mit synchroner Vorgehensweise gearbeitet. Wir empfehlen Ihnen, im Rahmen der Migration auf das neuere asynchrone API-Aufrufmuster mit SPN-Unterstützung umzustellen. Bei asynchronen Anforderungen werden große Datenmengen besser verarbeitet und Timeoutfehler reduziert.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

Verwenden Sie die folgenden Anforderungs-URIs beim Aufrufen der neuen asynchronen API für Reservierungsdetails. Ihre Registrierungsnummer sollte als `billingAccountId` verwendet werden. Sie können die API mit den folgenden Bereichen aufrufen:

- Registrierung: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Beispielanforderung zum Generieren eines Berichts mit Reservierungsdetails

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Beispielanforderung zum Abfragen des Status der Berichterstellung

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Beispiel für Antwort auf Abfrage

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Änderungen des Antworttexts

Die Antwort der älteren synchronen API für Reservierungsdetails ist unten angegeben.

_Alter Antworttext_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Neuer Antworttext_:

Mit der neuen API wird eine CSV-Datei für Sie erstellt. Hier sind die Dateifelder angegeben.

| **Alte Eigenschaft** | **Neue Eigenschaft** | **Hinweise** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Neue Eigenschaft für Instanzflexibilität |
|  | InstanceFlexibilityRatio | Neue Eigenschaft für Instanzflexibilität |
| instanceId | InstanceName |  |
|  | Art | Dies ist eine neue Eigenschaft. Der Wert lautet `None`, `Reservation` oder `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Nutzungszusammenfassung zu reservierten Instanzen

Verwenden Sie die folgenden Anforderungs-URIs, um die neue API für Reservierungszusammenfassungen aufzurufen.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

 Rufen Sie die API mit den folgenden Bereichen auf:

- Registrierung: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Abrufen der Reservierungszusammenfassung: Täglich_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Abrufen der Reservierungszusammenfassung: Monatlich_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Änderungen des Antworttexts

_Alter Antworttext_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Neuer Antworttext_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Empfehlungen für reservierte Instanzen

Verwenden Sie die folgenden Anforderungs-URIs, um die neue API für Reservierungsempfehlungen aufzurufen.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

 Rufen Sie die API mit den folgenden Bereichen auf:

- Registrierung: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Abonnement: `subscriptions/{subscriptionId}`
- Ressourcengruppen: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Abrufen von Empfehlungen_](/rest/api/consumption/reservationrecommendations/list)

Sowohl die freigegebenen als auch die einzelnen Bereichsempfehlungen sind über diese API verfügbar. Sie können auch über den optionalen API-Parameter nach dem Bereich filtern.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Änderungen des Antworttexts

Empfehlungen für freigegebene und einzelne Bereiche werden zu einer API zusammengefasst.

_Alter Antworttext_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Neuer Antworttext_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Gebühren für reservierte Instanzen

Verwenden Sie die folgenden Anforderungs-URIs, um die neue API für Gebühren für reservierte Instanzen aufzurufen.

#### <a name="supported-requests"></a>Unterstützte Anforderungen

[_Abrufen von Reservierungsgebühren nach Datumsbereich_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Änderungen des Antworttexts

_Alter Antworttext_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Neuer Antworttext_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Verwenden zusätzlicher Cost Management-APIs

Nachdem Sie die Migration zu den Azure Resource Manager-APIs für Ihre vorhandenen Berichterstellungsszenarien abgeschlossen haben, können Sie noch viele weitere APIs nutzen. Die APIs sind auch über Azure Resource Manager verfügbar und können per Authentifizierung auf Dienstprinzipalbasis automatisiert werden. Hier ist eine kurze Zusammenfassung der neuen Funktionen angegeben, die Sie nutzen können.

- [Budgets](/rest/api/consumption/budgets/createorupdate): Verwenden Sie diese Funktion zum Festlegen von Schwellenwerten, um Ihre Kosten proaktiv zu überwachen, die relevanten Projektbeteiligten zu benachrichtigen und automatisierte Aktionen als Reaktion auf Überschreitungen der Schwellenwerte festzulegen.

- [Warnungen](/rest/api/cost-management/alerts): Verwenden Sie diese Funktion zum Anzeigen von Warnungsinformationen, z. B. für die Bereiche Budget, Rechnungen, Guthaben, Kontingente und mehr.

- [Exporte](/rest/api/cost-management/exports): Verwenden Sie diese Funktion zum Planen des wiederkehrenden Exports Ihrer Gebührendaten in ein Azure Storage-Konto Ihrer Wahl. Dies ist die empfohlene Lösung für Kunden mit einer großen Azure-Präsenz, die ihre Daten analysieren und in eigenen internen Systemen verwenden möchten.

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit den [Azure Resource Manager-REST-APIs](/rest/api/azure) vertraut.
- Ermitteln Sie bei Bedarf unter [Zuordnung von EA-APIs zu neuen Azure Resource Manager-APIs](#ea-api-mapping-to-new-azure-resource-manager-apis), welche EA-APIs Sie verwenden und auf welche Azure Resource Manager-APIs umgestellt werden sollte.
- Wenn Sie Azure Resource Manager-APIs noch nicht verwenden, [registrieren Sie Ihre Client-App in Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualisieren Sie bei Bedarf Ihren Programmcode so, dass die [Azure AD-Authentifizierung](/rest/api/azure/#create-the-request) für Ihren Dienstprinzipal genutzt wird.