---
title: Überprüfen von Abrechnungsdaten von Azure-Abonnements mit REST-API | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure-REST-APIs Abonnementabrechnungsdetails überprüfen.
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
ms.openlocfilehash: 0a73462b7fdbaf6386a3051a72da755f31ff8dd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192128"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Überprüfen der Abonnementabrechnung mit REST-APIs

Mit Azure-Berichterstellungs-APIs können Sie Ihre Azure-Kosten überprüfen und verwalten.

Mit Filtern können Sie die Ergebnisse gemäß Ihren Anforderungen anpassen.

Hier erfahren Sie, wie Sie eine REST-API verwenden, um Abonnementabrechnungsdetails für einen bestimmten Datumsbereich zurückzugeben.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Erstellen der Anforderung

Der Parameter `{subscriptionID}` ist erforderlich und identifiziert das Zielabonnement.

Der Parameter `{billingPeriod}` ist erforderlich und gibt einen aktuellen [Abrechnungszeitraum](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) an.

Die Parameter `${startDate}` und `${endDate}` sind für dieses Beispiel erforderlich, aber für den Endpunkt optional. Sie geben den Datumsbereich als Zeichenfolgen im Format JJJJ-MM-TT an (Beispiele: `'20180501'` und `'20180615'`).

Die folgenden Header sind erforderlich:

|Anforderungsheader|BESCHREIBUNG|
|--------------------|-----------------|
|*Content-Type:*|Erforderlich. Legen Sie diese Option auf `application/json` fest.|
|*Authorization:*|Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer` [Zugriffstoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |

## <a name="response"></a>response

Bei einer erfolgreichen Antwort wird der Statuscode 200 (OK) zurückgegeben, der eine Liste der detaillierten Kosten für Ihr Konto enthält.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Jedes Element in **value** stellt Details in Bezug auf die Verwendung eines Diensts dar:

|Response-Eigenschaft|BESCHREIBUNG|
|----------------|----------|
|**subscriptionGuid** | Die global eindeutige ID des Abonnements. |
|**startDate** | Das Datum, an dem die Verwendung begann. |
|**endDate** | Das Datum, an dem die Verwendung endete. |
|**usageQuantity** | Die verwendete Menge. |
|**billableQuantity** | Die tatsächlich in Rechnung gestellte Menge. |
|**pretaxCost** | Die in Rechnung gestellten Kosten, vor Steuern. |
|**meterDetails** | Detaillierte Informationen zur Verwendung. |
|**nextLink**| Gibt bei entsprechender Festlegung eine URL für die nächste „Seite“ mit Details an. Leer, wenn es sich um die letzte Seite handelt. |

Dieses Beispiel ist abgekürzt. Eine vollständige Beschreibung der einzelnen Antwortfelder finden Sie unter [Auflisten von Nutzungsdetails](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod).

Andere Statuscodes geben Fehlerbedingungen an. In diesen Fällen erläutert das Antwortobjekt, warum ein Fehler bei der Anforderung aufgetreten ist.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte
- Sehen Sie sich die [Übersicht über Unternehmensberichte](https://docs.microsoft.com/azure/billing/billing-enterprise-api) an.
- Erkunden Sie die [Enterprise-Abrechnungs-REST-API](https://docs.microsoft.com/rest/api/billing/).
- [Erste Schritte mit der Azure-REST-API](https://docs.microsoft.com/rest/api/azure/)
