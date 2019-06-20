---
title: Überprüfen von Abrechnungsdaten der Azure Enterprise-Registrierung mit REST-API | Microsoft Docs
description: Erfahren Sie, wie Sie Azure-REST-APIs zum Überprüfen von Abrechnungsinformationen der Enterprise-Registrierung verwenden.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: 9a0b536426ab024d5af7b257e44a2d5e20f14def
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371035"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Überprüfen von Abrechnungsdaten der Enterprise-Registrierung mit REST-APIs

Mit Azure-Berichterstellungs-APIs können Sie Ihre Azure-Kosten überprüfen und verwalten.

In diesem Artikel erfahren Sie, wie Sie die Abrechnungsinformationen zu Abrechnungskonten, Abteilungs- oder Enterprise Agreement-Registrierungskonten (EA) mithilfe der Azure-REST-APIs abrufen. 

## <a name="individual-account-billing"></a>Einzelkontoabrechnung

So rufen Sie Nutzungsdetails für Konten in einer Abteilung ab:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Der Parameter `{billingAccountId}` ist erforderlich und sollte die ID für das Konto enthalten.

Die folgenden Header sind erforderlich: 

|Anforderungsheader|BESCHREIBUNG|  
|--------------------|-----------------|  
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|  
|*Authorization:*|Erforderlich. Legen Sie diese Option auf einen gültigen `Bearer`-[API-Schlüssel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) fest. |  

In diesem Beispiel wird ein synchroner Aufruf gezeigt, der Details für den aktuellen Abrechnungszyklus zurückgibt. Aus Leistungsgründen geben synchrone Aufrufe Informationen des letzten Monats zurück.  Sie können auch die [API asynchron](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) aufrufen, um Daten von 36 Monaten zurückzugeben.


## <a name="response"></a>response  

Bei einer erfolgreichen Antwort wird der Statuscode 200 (OK) zurückgegeben, der eine Liste der detaillierten Kosten für das Konto enthält.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Dieses Beispiel ist abgekürzt. Eine vollständige Beschreibung der einzelnen Antwortfelder sowie der Fehlerbehandlung finden Sie unter [Abrufen von Nutzungsdetails für ein Abrechnungskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist).

## <a name="department-billing"></a>Abteilungsabrechnung 

Rufen Sie Nutzungsdetails in aggregierter Form für alle Konten in einer Abteilung ab. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Der Parameter `{departmentId}` ist erforderlich und sollte die ID für die Abteilung im Registrierungskonto enthalten.

Die folgenden Header sind erforderlich: 

|Anforderungsheader|BESCHREIBUNG|  
|--------------------|-----------------|  
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|  
|*Authorization:*|Erforderlich. Legen Sie diese Option auf einen gültigen `Bearer`-[API-Schlüssel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) fest. |  

In diesem Beispiel wird ein synchroner Aufruf gezeigt, der Details für den aktuellen Abrechnungszyklus zurückgibt. Aus Leistungsgründen geben synchrone Aufrufe Informationen des letzten Monats zurück.  Sie können auch die [API asynchron](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) aufrufen, um Daten von 36 Monaten zurückzugeben.

### <a name="response"></a>response  

Bei einer erfolgreichen Antwort wird der Statuscode 200 (OK) zurückgegeben, der eine Liste mit detaillierten Nutzungsinformationen und Kosten für einen angegebenen Abrechnungszeitraum und die Rechnungs-ID für die Abteilung enthält.


Das folgende Beispiel zeigt die Ausgabe der REST-API für die Abteilung `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Dieses Beispiel ist abgekürzt. Eine vollständige Beschreibung der einzelnen Antwortfelder sowie der Fehlerbehandlung finden Sie unter [Abrufen von Nutzungsdetails für eine Abteilung](/rest/api/consumption/usagedetails/list#departmentusagedetailslist).

## <a name="enrollment-account-billing"></a>Registrierungskontoabrechnung

Rufen Sie Nutzungsdetails in aggregierter Form für das Registrierungskonto ab.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Der Parameter `{enrollmentAccountId}` ist erforderlich und sollte die ID für das Registrierungskonto enthalten.

Die folgenden Header sind erforderlich: 

|Anforderungsheader|BESCHREIBUNG|  
|--------------------|-----------------|  
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|  
|*Authorization:*|Erforderlich. Legen Sie diese Option auf einen gültigen `Bearer`-[API-Schlüssel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) fest. |  

In diesem Beispiel wird ein synchroner Aufruf gezeigt, der Details für den aktuellen Abrechnungszyklus zurückgibt. Aus Leistungsgründen geben synchrone Aufrufe Informationen des letzten Monats zurück.  Sie können auch die [API asynchron](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) aufrufen, um Daten von 36 Monaten zurückzugeben.

### <a name="response"></a>response  

Bei einer erfolgreichen Antwort wird der Statuscode 200 (OK) zurückgegeben, der eine Liste mit detaillierten Nutzungsinformationen und Kosten für einen angegebenen Abrechnungszeitraum und die Rechnungs-ID für die Abteilung enthält.

Das folgende Beispiel zeigt die Ausgabe der REST-API für die Unternehmensregistrierung `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Dieses Beispiel ist abgekürzt. Eine vollständige Beschreibung der einzelnen Antwortfelder sowie der Fehlerbehandlung finden Sie unter [Abrufen von Nutzungsdetails für ein Registrierungskonto](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist).

## <a name="next-steps"></a>Nächste Schritte 
- Sehen Sie sich die [Übersicht über Unternehmensberichte](https://docs.microsoft.com/azure/billing/billing-enterprise-api) an.
- Erkunden Sie die [Enterprise-Abrechnungs-REST-API](https://docs.microsoft.com/rest/api/billing/).   
- [Erste Schritte mit der Azure-REST-API](https://docs.microsoft.com/rest/api/azure/)   
