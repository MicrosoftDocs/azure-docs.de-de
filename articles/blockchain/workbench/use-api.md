---
title: Verwenden der REST-APIs von Azure Blockchain Workbench
description: Szenarien für die Verwendung der REST-API der Vorschauversion von Azure Blockchain Workbench
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004839"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Verwenden der REST-API der Vorschauversion von Azure Blockchain Workbench

Die REST-API der Vorschauversion von Azure Blockchain Workbench bietet Entwicklern und Information Workern eine Möglichkeit zum Erstellen umfangreicher Integrationen in Blockchain-Anwendungen. In diesem Artikel werden verschiedene Szenarien für die Verwendung der Workbench-REST-API beschrieben. Nehmen Sie beispielsweise an, Sie möchten einen benutzerdefinierten Blockchainclient erstellen, über den angemeldete Benutzer ihre zugewiesenen Blockchainanwendungen anzeigen und mit ihnen interagieren können. Der Client kann mithilfe der Blockchain Workbench-API Vertragsinstanzen anzeigen und Maßnahmen für Smart Contracts ergreifen.

## <a name="blockchain-workbench-api-endpoint"></a>Endpunkt der Blockchain Workbench-API

Der Zugriff auf Blockchain Workbench-APIs erfolgt über einen Endpunkt für Ihre Bereitstellung. So rufen Sie die URL des API-Endpunkts für Ihre Bereitstellung ab:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich **Ressourcengruppen** aus.
1. Wählen Sie den Namen der Ressourcengruppe für die bereitgestellte Blockchain Workbench aus.
1. Klicken Sie auf die Spaltenüberschrift **TYP**, um die Liste alphabetisch nach Typ zu sortieren.
1. Es gibt zwei Ressourcen vom Typ **App-Dienst**. Wählen Sie die Ressource vom Typ **App Service** *mit* dem Suffix „-api“ aus.
1. Kopieren Sie in der **Übersicht** des App-Diensts den Wert für **URL**. Dieser stellt die URL des API-Endpunkts für die bereitgestellte Blockchain Workbench dar.

    ![URL des API-Endpunkts für den App-Dienst](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

Anforderungen an die Blockchain Workbench-REST-API werden durch Azure Active Directory (Azure AD) geschützt.

Um eine authentifizierte Anforderung an die REST-APIs zu senden, erfordert der Clientcode eine Authentifizierung mit gültigen Anmeldeinformationen, bevor Sie die API aufrufen können. Die Authentifizierung zwischen den verschiedenen Akteuren wird von Azure AD koordiniert. Ihr Client erhält ein [Zugriffstoken](../../active-directory/develop/developer-glossary.md#access-token) als Nachweis der Authentifizierung. Das Token wird dann im HTTP-Autorisierungsheader der REST-API-Anforderungen gesendet. Weitere Informationen zur Azure AD-Authentifizierung finden Sie unter [Azure Active Directory für Entwickler](../../active-directory/develop/index.yml).

Beispiele zum Authentifizieren finden Sie unter [REST-API-Beispiele](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples).

## <a name="using-postman"></a>Verwenden von Postman

Wenn Sie Workbench-APIs testen oder mit ihnen experimentieren möchten, können Sie [Postman](https://www.postman.com) verwenden, um API-Aufrufe an Ihre Bereitstellung durchzuführen. [Laden Sie eine Postman-Beispielsammlung von Workbench-API-Anforderungen von GitHub herunter](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman). Ausführliche Informationen zum Authentifizieren und Verwenden der API-Beispielanforderungen finden Sie in der Infodatei.

## <a name="create-an-application"></a>Erstellen einer Anwendung

Sie erstellen Blockchain Workbench-Anwendungen mithilfe von zwei API-Aufrufen. Diese Methode kann nur von Benutzern ausgeführt werden, die Workbench-Administratoren sind.

Verwenden Sie die [Anwendungs-POST-API](/rest/api/azure-blockchain-workbench/applications/applicationspost), um die JSON-Datei der Anwendung hochzuladen und eine Anwendungs-ID zu erhalten.

### <a name="applications-post-request"></a>Anwendungs-POST-Anforderung

Verwenden Sie den Parameter **appFile**, um die Konfigurationsdatei als Teil des Anforderungstexts zu senden.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Anwendungs-POST-Antwort

Die erstellte Anwendungs-ID wird in der Antwort zurückgegeben. Sie benötigen die Anwendungs-ID, um die Konfigurationsdatei der Codedatei zuzuordnen, wenn Sie die nächste API aufrufen.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Vertragscode-POST-Anforderung

Laden Sie die Solidity-Codedatei der Anwendung über die [Anwendungen-Vertragscode-POST-API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) hoch, indem Sie die Anwendungs-ID übergeben. Bei der Nutzlast kann es sich um eine einzelne Solidity-Datei oder eine ZIP-Datei mit Solidity-Dateien handeln.

Ersetzen Sie die folgenden Werte:

| Parameter | value |
|-----------|-------|
| {applicationId} | Rückgabewert der Anwendungs-POST-API. |
| {ledgerId} | Der Index des Ledgers. Der Wert ist in der Regel 1. Sie können den Wert auch in der [Tabelle „Ledger“](data-sql-management-studio.md) überprüfen. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Vertragscode-POST-Antwort

Bei Erfolg enthält die Antwort die erstellte Vertragscode-ID aus der [Tabelle „ContractCode“](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Zuweisen von Rollen zu Benutzern

Erstellen Sie mithilfe der [Anwendungs-Rollenzuweisungs-POST-API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) eine Zuordnung zwischen Benutzer und Rolle in der angegebenen Blockchainanwendung, indem Sie die Anwendungs-ID, die Benutzer-ID und die Anwendungsrollen-ID übergeben. Diese Methode kann nur von Benutzern ausgeführt werden, die Workbench-Administratoren sind.

### <a name="role-assignments-post-request"></a>Rollenzuweisungs-POST-Anforderung

Ersetzen Sie die folgenden Werte:

| Parameter | value |
|-----------|-------|
| {applicationId} | Rückgabewert der Anwendungs-POST-API. |
| {userId} | Der Benutzer-ID-Wert aus der [Tabelle „User“](data-sql-management-studio.md). |
| {applicationRoleId} | Der Anwendungsrollen-ID-Wert aus der [Tabelle „ApplicationRole“](data-sql-management-studio.md), der der Anwendungs-ID zugeordnet ist. |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Rollenzuweisungs-POST-Antwort

Bei Erfolg enthält die Antwort die erstellte Rollenzuweisungs-ID aus der [Tabelle „RoleAssignment“](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Auflisten von Anwendungen

Verwenden Sie die [Anwendungs-GET-API](/rest/api/azure-blockchain-workbench/applications/applicationsget), um alle Blockchain Workbench-Anwendungen für den Benutzer abzurufen. In diesem Beispiel hat der angemeldete Benutzer Zugriff auf zwei Anwendungen:

- [AssetTransfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md) (Vermögensübertragung)
- [RefrigeratedTransportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md) (Transport mit Kühlkette)

### <a name="applications-get-request"></a>Anwendungs-GET-Anforderung

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Anwendungs-GET-Antwort

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

Listen Sie mithilfe der [Anwendungsworkflow-GET-API](/rest/api/azure-blockchain-workbench/applications/workflowsget) alle Workflows der angegebenen Blockchainanwendung auf, auf die ein Benutzer in Blockchain Workbench Zugriff hat. Jede Blockchainanwendung besitzt mindestens einen Workflow, und jeder Workflow verfügt über null oder mehr Smart Contract-Instanzen. Bei Blockchainclientanwendungen mit nur einem Workflow wird empfohlen, den Schritt auf der Benutzeroberfläche zu überspringen, der den Benutzern die Auswahl des entsprechenden Workflows ermöglicht.

### <a name="application-workflows-request"></a>Anwendungsworkflow-Anforderung

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Anwendungsworkflow-Antwort

Blockchain Workbench-Administratoren können alle Blockchain-Workflows abrufen. Benutzer, die keine Workbench-Administratoren sind, können alle Workflows abrufen, für die sie über mindestens eine entsprechende Anwendungsrolle verfügen oder einer Smart Contract-Instanzrolle zugeordnet sind.

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

## <a name="create-a-contract-instance"></a>Erstellen einer Vertragsinstanz

Verwenden Sie [Vertrags-POST-API V2](/rest/api/azure-blockchain-workbench/contractsv2/contractpost), um eine neue Smart Contract-Instanz für einen Workflow zu erstellen. Benutzer können nur dann eine neue Smart Contract-Instanz erstellen, wenn sie einer Anwendungsrolle zugeordnet sind, die diese Instanz für den Workflow initiieren kann.

> [!NOTE]
> In diesem Beispiel wird Version 2 der API verwendet. Vertrags-APIs der Version 2 bieten eine feinere Granularität für die zugehörigen ProvisioningStatus-Felder.

### <a name="contracts-post-request"></a>Vertrags-POST-Anforderung

Ersetzen Sie die folgenden Werte:

| Parameter | value |
|-----------|-------|
| {workflowId} | Der Workflow-ID-Wert ist die ConstructorID des Vertrags aus der [Tabelle „Workflow“](data-sql-management-studio.md). |
| {contractCodeId} | Der Vertragscode-ID-Wert aus der [Tabelle „ContractCode“](data-sql-management-studio.md). Korrelieren Sie die Anwendungs-ID und die Ledger-ID für die Vertragsinstanz, die Sie erstellen möchten. |
| {connectionId} | Der Verbindungs-ID-Wert aus der [Tabelle „Connection“](data-sql-management-studio.md). |

Legen Sie für den Anforderungstext mithilfe der folgenden Informationen Werte fest:

| Parameter | value |
|-----------|-------|
| workflowFunctionID | Die ID aus der [Tabelle „WorkflowFunction“](data-sql-management-studio.md). |
| workflowActionParameters | Name-Wert-Paare von Parametern, die an den Konstruktor übergeben werden. Verwenden Sie für jeden Parameter den Wert workflowFunctionParameterID aus der Tabelle [WorkflowFunctionParameter](data-sql-management-studio.md). |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Vertrags-POST-Antwort

Bei Erfolg gibt die Rollenzuweisungs-API die ContractActionID aus der [Tabelle „ContractActionParameter“](data-sql-management-studio.md) zurück.

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Auflistung von Smart Contract-Instanzen für einen Workflow

Verwenden Sie die [Vertrags-GET-API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget), um alle Smart Contract-Instanzen für einen Workflow anzuzeigen. Oder Sie können den Benutzern einen tieferen Einblick in jede angezeigte Smart Contract-Instanz ermöglichen.

### <a name="contracts-request"></a>Vertragsanforderung

Nehmen Sie in diesem Beispiel an, dass ein Benutzer mit einer der Smart Contract-Instanzen interagieren möchte, um Maßnahmen zu ergreifen.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Vertragsantwort

In der Antwort werden alle Smart Contract-Instanzen des angegebenen Workflows aufgelistet. Workbench-Administratoren können alle Smart Contract-Instanzen abrufen. Benutzer, die keine Workbench-Administratoren sind, können alle Smart Contract-Instanzen abrufen, für die sie über mindestens eine entsprechende Anwendungsrolle verfügen oder einer Smart Contract-Instanzrolle zugeordnet sind.

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

Verwenden Sie [Vertragsaktions-GET-API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget), um die verfügbaren Benutzeraktionen für den Status des Vertrags anzuzeigen. 

### <a name="contract-action-request"></a>Vertragsaktionsanforderung

In diesem Beispiel zeigt der Benutzer alle verfügbaren Aktionen für einen neu erstellten Smart Contract an.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Vertragsaktionsantwort

Die Antwort listet alle Aktionen auf, die ein Benutzer ausführen kann, wenn der aktuelle Zustand der angegebenen Smart Contract-Instanz angegeben wird.

* Modify (Ändern): Ermöglicht dem Benutzer das Ändern der Beschreibung und des Preises für eine Ressource.
* Terminate (Beenden): Ermöglicht dem Benutzer das Beenden des Vertrags der Ressource.

Benutzern werden alle anwendbaren Aktionen angezeigt, wenn der Benutzer eine zugehörige Anwendungsrolle besitzt oder einer Smart Contract-Instanzrolle für den aktuellen Zustand der angegebenen Smart Contract-Instanz zugeordnet ist.

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

Verwenden Sie die [Vertragsaktions-POST-API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost), um eine Aktion für die angegebene Smart Contract-Instanz auszuführen.

### <a name="contract-action-post-request"></a>Vertragsaktions-POST-Anforderung

Betrachten Sie in diesem Fall ein Szenario, in dem ein Benutzer die Beschreibung und den Preis einer Ressource ändern möchte.

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

Benutzer können die Aktion nur ausführen, wenn der aktuelle Zustand der angegebenen Smart Contract-Instanz und der zugehörigen Anwendungsrolle bzw. Smart Contract-Instanzrolle des Benutzers angegeben wird.

### <a name="contract-action-post-response"></a>Vertragsaktions-POST-Antwort

Wenn POST erfolgreich ist, wird eine HTTP 200 OK-Antwort ohne Antworttext zurückgegeben.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nächste Schritte

Referenzinformationen zu Blockchain Workbench-APIs finden Sie in der [Referenz zur REST-API von Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench).
