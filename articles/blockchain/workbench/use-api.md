---
title: Verwenden der REST-API von Azure Blockchain Workbench
description: Szenarien für die Verwendung der REST-API von Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 7fa72ad62d7d11c795422a203d13a4dd45484c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60896039"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Verwenden der REST-API von Azure Blockchain Workbench

Die REST-API von Azure Blockchain Workbench bietet Entwicklern und Information Workern eine Möglichkeit zum Erstellen umfangreicher Integrationen in Blockchain-Anwendungen. Dieses Dokument führt Sie schrittweise durch verschiedene wichtige Methoden der Workbench REST-API. Nehmen Sie beispielsweise an, dass ein Entwickler einen benutzerdefinierten Blockchainclient erstellen möchte. Mithilfe dieses Blockchainclients können angemeldete Benutzer ihre zugewiesenen Blockchainanwendungen anzeigen und mit diesen in Interaktion treten. Der Client ermöglicht es den Benutzern, Vertragsinstanzen anzuzeigen und Maßnahmen für Smart Contracts zu ergreifen. Der Client verwendet die Workbench REST-API im Kontext des angemeldeten Benutzers für die folgenden Aktionen:

* Auflisten von Anwendungen
* Auflisten von Workflows für eine Anwendung
* Auflistung von Smart Contract-Instanzen für einen Workflow
* Auflisten der verfügbaren Aktionen für einen Vertrag
* Ausführen einer Aktion für einen Vertrag

Die in diesen Szenarien verwendeten Blockchain-Beispielanwendungen können [von GitHub heruntergeladen werden](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Auflisten von Anwendungen

Nachdem sich ein Benutzer am Blockchainclient angemeldet hat, besteht die erste Aufgabe darin, alle Anwendungen von Blockchain Workbench für den Benutzer abzurufen. In diesem Szenario besitzt der Benutzer Zugriff auf zwei Anwendungen:

1. [AssetTransfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md) (Vermögensübertragung)
2. [RefrigeratedTransportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md) (Transport mit Kühlkette)

Verwenden Sie die [GET-API für Anwendungen](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

In der Antwort sind alle Blockchainanwendungen aufgelistet, auf die ein Benutzer in Blockchain Workbench Zugriff hat. Blockchain Workbench-Administratoren können alle Blockchainanwendungen abrufen. Benutzer, die keine Workbench-Administratoren sind, können alle Blockchainanwendungen abrufen, für die sie über mindestens eine zugeordnete Anwendungsrolle oder Smart Contract-Instanzrolle verfügen.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Auflisten von Workflows für eine Anwendung

Sobald ein Benutzer die entsprechende Blockchainanwendung (z. B. **AssetTransfer**) ausgewählt hat, ruft der Blockchainclient alle Workflows der jeweiligen Blockchainanwendung ab. Benutzer können dann den entsprechenden Workflow auswählen, bevor alle Smart Contract Instanzen für den Workflow für sie angezeigt werden. Jede Blockchainanwendung besitzt mindestens einen Workflow, und jeder Workflow verfügt über null oder mehr Smart Contract-Instanzen. Bei Blockchainclientanwendungen mit nur einem Workflow wird empfohlen, den Schritt auf der Benutzeroberfläche zu überspringen, der den Benutzern die Auswahl des entsprechenden Workflows ermöglicht. In diesem Fall weist **AssetTransfer** nur einen Workflow auf, der ebenfalls den Namen **AssetTransfer** hat.

Verwenden Sie die [GET-API für Anwendungsworkflows](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Die Antwort listet alle Workflows der angegebenen Blockchainanwendungen auf, auf die ein Benutzer in Blockchain Workbench Zugriff besitzt. Blockchain Workbench-Administratoren können alle Blockchain-Workflows abrufen. Benutzer, die keine Workbench-Administratoren sind, können alle Workflows abrufen, für die sie über mindestens eine entsprechende Anwendungsrolle verfügen oder einer Smart Contract-Instanzrolle zugeordnet sind.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Auflistung von Smart Contract-Instanzen für einen Workflow

Sobald ein Benutzer den entsprechenden Workflow (in diesem Fall **AssetTransfer**) auswählt, ruft der Blockchainclient alle Smart Contract-Instanzen für den angegebenen Workflow ab. Sie können diese Informationen verwenden, um alle Smart Contract-Instanzen für den Workflow anzuzeigen. Oder Sie können den Benutzern einen tieferen Einblick in jede angezeigte Smart Contract-Instanz ermöglichen. Nehmen Sie in diesem Beispiel an, dass ein Benutzer mit einer der Smart Contract-Instanzen interagieren möchte, um Maßnahmen zu ergreifen.

Verwenden Sie die [GET-API für Verträge](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Die Antwort listet alle Smart Contract-Instanzen des angegebenen Workflows auf. Workbench-Administratoren können alle Smart Contract-Instanzen abrufen. Benutzer, die keine Workbench-Administratoren sind, können alle Smart Contract-Instanzen abrufen, für die sie über mindestens eine entsprechende Anwendungsrolle verfügen oder einer Smart Contract-Instanzrolle zugeordnet sind.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Auflisten der verfügbaren Aktionen für einen Vertrag

Sobald sich ein Benutzer entscheidet, einen Vertrag detailliert zu untersuchen, kann der Blockchainclient alle in Hinblick auf den Vertragsstatus verfügbaren Benutzeraktionen anzeigen. In diesem Beispiel zeigt der Benutzer alle verfügbaren Aktionen für einen neu erstellten Smart Contract an:

* Modify (Ändern): Ermöglicht dem Benutzer das Ändern der Beschreibung und des Preises für eine Ressource.
* Terminate (Beenden): Ermöglicht dem Benutzer das Beenden des Vertrags der Ressource.

Verwenden Sie die [GET-API für Vertragsaktionen](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Die Antwort listet alle Aktionen auf, die ein Benutzer ausführen kann, wenn der aktuelle Zustand der angegebenen Smart Contract-Instanz angegeben wird. Benutzern werden alle anwendbaren Aktionen angezeigt, wenn der Benutzer eine zugehörige Anwendungsrolle besitzt oder einer Smart Contract-Instanzrolle für den aktuellen Zustand der angegebenen Smart Contract-Instanz zugeordnet ist.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Ausführen einer Aktion für einen Vertrag

Ein Benutzer kann sich dann entscheiden, für die angegebene Smart Contract-Instanz Maßnahmen zu ergreifen. Betrachten Sie in diesem Fall das Szenario, in dem ein Benutzer die Beschreibung und den Preis einer Ressource wie folgt ändern möchte:

* Beschreibung: „Mein aktualisiertes Auto“
* Preis: 54321

Verwenden Sie die [POST-API für Vertragsaktionen](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Benutzer können die Aktion nur ausführen, wenn der aktuelle Zustand der angegebenen Smart Contract-Instanz und der zugehörigen Anwendungsrolle bzw. Smart Contract-Instanzrolle des Benutzers angegeben wird. Wenn POST erfolgreich ist, wird eine HTTP 200 OK-Antwort ohne Antworttext zurückgegeben.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenz zur REST-API von Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)