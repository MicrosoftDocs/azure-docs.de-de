---
title: API zum Abrufen eines bestimmten Angebots | Azure Marketplace
description: Die API ruft das angegebene Angebot im Herausgebernamespace ab.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bb6bbd457ff372ad46091f49cf4ae7e4b34b3d83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935441"
---
<a name="retrieve-a-specific-offer"></a>Abrufen eines bestimmten Angebots
=========================

Ruft das angegebene Angebot im Herausgebernamespace ab.  

Sie können auch eine bestimmte Version des Angebots abrufen oder das Angebot aus einem Entwurfs-, Vorschau- oder Produktionsslot abrufen. Ist kein Slot angegeben, wird standardmäßig `draft` (Entwurf) verwendet. Ein Versuch, ein Angebot abzurufen, das nicht in der Vorschau angezeigt oder nicht veröffentlicht wurde, führt zu einem `404 Not Found`-Fehler.

> [!WARNING]
> Die geheimen Werte für Felder mit geheimem Typ werden von dieser API nicht abgerufen.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-Parameter
--------------


| **Name**    | **Beschreibung**                                                                          | **Datentyp** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | Herausgeber-ID. Beispiel: Contoso                                                        | string        |
| offerId     | GUID, die das Angebot eindeutig identifiziert.                                                 | string        |
| version     | Die Version des Angebots, das abgerufen wird. Standardmäßig wird die neueste Version des Angebots abgerufen. | Integer       |
| slotId      | Der Slot, aus dem das Angebot abgerufen werden soll. Einer der folgenden Werte ist möglich:      <br/>  - `Draft` (Standard) Ruft die Angebotsversion ab, die sich aktuell im Entwurfsstadium befindet.  <br/>  -  `Preview` Ruft die Angebotsversion ab, die sich aktuell im Vorschaustadium befindet.     <br/>  -  `Production` Ruft die Angebotsversion ab, die sich aktuell im Produktionsstadium befindet.          |      enum |
| api-version | Neueste Version der API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Header
------

|  **Name**          |   **Wert**            |
|  ---------------   |  --------------        |
|  Content-Typ      | `application/json`     |
|  Autorisierung     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Beispiel für Hauptteil
------------

### <a name="response"></a>response

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Eigenschaften für Antworthauptteil

|  **Name**       |   **Beschreibung**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Gibt den Typ des Angebots an                                                                                                    |
|  publisherId    | Eindeutiger Bezeichner des Herausgebers                                                                                              |
|  status         | Status des Angebots. Die Liste der möglichen Werte finden Sie weiter unten unter [Angebotsstatus](#offer-status).                                  |
|  id             | GUID, die das Angebot eindeutig identifiziert                                                                                         |
|  version        | Aktuelle Version des Angebots. Die version-Eigenschaft kann vom Client nicht geändert werden. Sie wird nach jeder Veröffentlichung inkrementiert.    |
|  Definition     | Tatsächliche Definition der des Workloads                                                                                               |
|  changedTime    | UTC-Zeitpunkt (Datum und Uhrzeit), zu dem das Angebot letztmalig geändert wurde                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Antwortstatuscodes

| **Code**  | **Beschreibung**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`: Die Anforderung wurde erfolgreich verarbeitet, und alle Angebote unter dem Herausgeber wurden an den Client zurückgegeben.               |
|  400      | `Bad/Malformed request` – der Fehlerantworttext enthält möglicherweise weitere Informationen.                                                 |
|  403      | `Forbidden` – der Client hat keinen Zugriff auf den angegebenen Namespace.                                                        |
|  404      | `Not found`: Die angegebene Entität ist nicht vorhanden. Für den Client sollten die Parameter „publisherId“, „offerId“ und „version“ geprüft werden (sofern angegeben).      |
|  |  |


### <a name="offer-status"></a>Angebotsstatus

|  **Name**                   |   **Beschreibung**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Das Angebot wurde nie veröffentlicht.               |
|  NotStarted                 | Das Angebot ist neu, aber nicht gestartet.              |
|  WaitingForPublisherReview  | Das Angebot wartet auf die Herausgebergenehmigung.      |
|  Wird ausgeführt                    | Die Angebotsübermittlung wird verarbeitet.          |
|  Succeeded                  | Die Verarbeitung der Angebotsübermittlung ist abgeschlossen.    |
|  Canceled                   | Die Angebotsübermittlung wurde abgebrochen.                |
|  Fehler                     | Fehler bei der Angebotsübermittlung.                      |
|  |  |
