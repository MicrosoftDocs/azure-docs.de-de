---
title: Abrufen der Operations-API | Azure Marketplace
description: Ruft alle Vorgänge im Angebot oder einen bestimmten Vorgang für die angegebene Vorgangs-ID ab.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 1fbcc1d50dbc4488c4123be64e85de612233ccc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935777"
---
<a name="retrieve-operations"></a>Abrufen von Vorgängen
===================

Ruft alle Vorgänge im Angebot oder einen bestimmten Vorgang für die angegebene Vorgangs-ID ab. Der Client kann Abfrageparameter zum Filtern nach ausgeführten Vorgängen verwenden.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-Parameter
--------------

|  **Name**          |      **Beschreibung**                                                                                           | **Datentyp** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Herausgeber-ID, z.B. `Contoso`                                                                   |  string       |
|  offerId           |  Angebots-ID                                                                                              |  string       |
|  operationId       |  GUID, die den Vorgang im Angebot eindeutig identifiziert. Die Vorgangs-ID kann mithilfe dieser API abgerufen werden. Sie wird auch im HTTP-Header der Antwort für lang ausgeführte Vorgänge zurückgegeben, wie z.B. bei der [API zum Veröffentlichen von Angeboten](./cloud-partner-portal-api-publish-offer.md).  |   Guid   |
|  filteredStatus    | Optionaler Abfrageparameter zum Filtern nach dem Status (z.B. `running`) in der von dieser API zurückgegebenen Sammlung.  |   string |
|  api-version       | Neueste Version der API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Header
------

|  **Name**          |  **Wert**           |
|  ---------------   | -------------------- |
|  Content-Typ      | `application/json`   |
|  Autorisierung     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Beispiel für Hauptteil
------------

### <a name="response"></a>response

#### <a name="get-operations"></a>GET-Vorgänge

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET-Vorgang

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Eigenschaften für Antworthauptteil

|  **Name**                    |  **Beschreibung**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID, die den Vorgang eindeutig identifiziert                                                       |
|  submissionType              | Gibt den Typ des Vorgangs, der für das Angebot gemeldet wird, an, z.B. `Publish/GGoLive`      |
|  createdDateTime             | UTC-Datum und -Uhrzeit der Erstellung des Vorgangs                                                       |
|  lastActionDateTime          | UTC-Datum und -Uhrzeit der letzten Änderung des Vorgangs                                       |
|  status                      | Status des Vorgangs: `not started` \| `running` \| `failed` \| `completed`. Nur ein Vorgang kann jeweils den Status `running` aufweisen. |
|  error                       | Fehlermeldung für Vorgangsfehler                                                               |
|  |  |


### <a name="response-status-codes"></a>Antwortstatuscodes

| **Code**  |   **Beschreibung**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` – die Anforderung wurde erfolgreich verarbeitet, und die angeforderten Vorgänge wurden zurückgegeben.        |
|  400      | `Bad/Malformed request` – der Fehlerantworttext enthält möglicherweise weitere Informationen.                    |
|  403      | `Forbidden` – der Client hat keinen Zugriff auf den angegebenen Namespace.                          |
|  404      | `Not found` – die angegebene Entität ist nicht vorhanden.                                                 |
|  |  |
