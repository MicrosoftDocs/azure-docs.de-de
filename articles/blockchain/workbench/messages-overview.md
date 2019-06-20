---
title: Übersicht über die Integration von Nachrichten in Azure Blockchain Workbench
description: Übersicht über die Verwendung von Nachrichten in Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 49b2bdd1780caa4ae04efbc979e2ea33e2c13c4c
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147231"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integration von Nachrichten in Azure Blockchain Workbench

Neben der Bereitstellung einer REST-API bietet Azure Blockchain Workbench auch eine nachrichtenbasierte Integration. Workbench veröffentlicht ledgerorientierte Ereignisse über Azure Event Grid, sodass Downstreamconsumer Daten erfassen oder basierend auf diesen Ereignissen Aktionen ausführen können. Für Clients, die eine zuverlässige Nachrichtenübermittlung erfordern, liefert Azure Blockchain Workbench zudem Nachrichten an einen Azure Service Bus-Endpunkt.

## <a name="input-apis"></a>Eingabe-APIs

Wenn Sie Transaktionen von externen Systemen initiieren möchten, um Benutzer oder Verträge zu erstellen und Verträge zu aktualisieren, können Sie Transaktionen mithilfe von Messagingeingabe-APIs in einem Ledger durchführen. Ein Beispiel zu Eingabe-APIs finden Sie unter [Beispiele zur Integration von Nachrichten](https://aka.ms/blockchain-workbench-integration-sample).

Im Folgenden werden die derzeit verfügbaren Eingabe-APIs aufgeführt.

### <a name="create-user"></a>Benutzer erstellen

Diese Anforderung erstellt einen neuen Benutzer.

Für die Anforderung sind folgende Felder erforderlich:

| **Name**             | **Beschreibung**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Vom Client bereitgestellte GUID                                |
| firstName            | Vorname des Benutzers                              |
| lastName             | Nachname des Benutzers                               |
| emailAddress         | E-Mail-Adresse des Benutzers                           |
| externalId           | Azure AD-Objekt-ID des Benutzers                      |
| connectionId         | Eindeutiger Bezeichner für die Blockchainverbindung |
| messageSchemaVersion | Version des Messagingschemas                            |
| messageName          | **CreateUserRequest**                               |

Beispiel:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench gibt eine Antwort mit den folgenden Feldern zurück:

| **Name**              | **Beschreibung**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Vom Client bereitgestellte GUID |
| userId                | ID des erstellten Benutzers |
| userChainIdentifier   | Adresse des Benutzers, der im Blockchainnetzwerk erstellt wurde. In Ethereum ist die Adresse des Benutzers  **on-chain** . |
| connectionId          | Eindeutiger Bezeichner für die Blockchainverbindung|
| messageSchemaVersion  | Version des Messagingschemas |
| messageName           | **CreateUserUpdate** |
| status                | Status der Anforderung zum Erstellen des Benutzers.  Bei einer erfolgreichen Ausführung lautet der Wert **Success**. Bei einem Fehler lautet der Wert **Failure**.     |
| additionalInformation | Weitere Informationen basierend auf dem Status |

Beispiel für eine erfolgreiche Antwort auf **create user** von Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Wenn die Anforderung nicht erfolgreich war, werden Details zum Fehler in den zusätzlichen Informationen bereitgestellt.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Erstellen eines Vertrags

Diese Anforderung erstellt einen neuen Vertrag.

Für die Anforderung sind folgende Felder erforderlich:

| **Name**             | **Beschreibung**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Vom Client bereitgestellte GUID |
| userChainIdentifier  | Adresse des Benutzers, der im Blockchainnetzwerk erstellt wurde. In Ethereum ist diese Adresse die **on chain**-Adresse des Benutzers. |
| applicationName      | Name der Anwendung |
| version              | Die Version der Anwendung. Erforderlich, falls Sie mehrere Versionen der Anwendung aktiviert haben. Andernfalls ist die Version optional. Weitere Informationen zur Versionsverwaltung von Anwendungen finden Sie unter [Versionsverwaltung für die Azure Blockchain Workbench-Anwendung](version-app.md). |
| workflowName         | Name des Workflows |
| parameters           | Parametereingabe für die Vertragserstellung |
| connectionId         | Eindeutiger Bezeichner für die Blockchainverbindung |
| messageSchemaVersion | Version des Messagingschemas |
| messageName          | **CreateContractRequest** |

Beispiel:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench gibt eine Antwort mit den folgenden Feldern zurück:

| **Name**                 | **Beschreibung**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Vom Client bereitgestellte GUID                                                             |
| contractId               | Eindeutiger Bezeichner für den Vertrag innerhalb von Azure Blockchain Workbench. |
| contractLedgerIdentifier | Adresse des Vertrags im Ledger                                            |
| connectionId             | Eindeutiger Bezeichner für die Blockchainverbindung                               |
| messageSchemaVersion     | Version des Messagingschemas                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status der Anforderung zum Erstellen des Vertrags.  Mögliche Werte: **Submitted**, **Committed**, **Failure**.  |
| additionalInformation    | Weitere Informationen basierend auf dem Status                              |

Beispiel für eine übermittelte Antwort auf **create contract** von Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Beispiel für eine committete Antwort auf **create contract** von Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Wenn die Anforderung nicht erfolgreich war, werden Details zum Fehler in den zusätzlichen Informationen bereitgestellt.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Aktion zum Erstellen eines Vertrags

Diese Anforderung erstellt eine neue Vertragsaktion.

Für die Anforderung sind folgende Felder erforderlich:

| **Name**                 | **Beschreibung**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Vom Client bereitgestellte GUID |
| userChainIdentifier      | Adresse des Benutzers, der im Blockchainnetzwerk erstellt wurde. In Ethereum ist diese Adresse die **on chain**-Adresse des Benutzers. |
| contractLedgerIdentifier | Adresse des Vertrags im Ledger |
| version                  | Die Version der Anwendung. Erforderlich, falls Sie mehrere Versionen der Anwendung aktiviert haben. Andernfalls ist die Version optional. Weitere Informationen zur Versionsverwaltung von Anwendungen finden Sie unter [Versionsverwaltung für die Azure Blockchain Workbench-Anwendung](version-app.md). |
| workflowFunctionName     | Name der Workflowfunktion |
| parameters               | Parametereingabe für die Vertragserstellung |
| connectionId             | Eindeutiger Bezeichner für die Blockchainverbindung |
| messageSchemaVersion     | Version des Messagingschemas |
| messageName              | **CreateContractActionRequest** |

Beispiel:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench gibt eine Antwort mit den folgenden Feldern zurück:

| **Name**              | **Beschreibung**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Vom Client bereitgestellte GUID|
| contractId            | Eindeutiger Bezeichner für den Vertrag innerhalb von Azure Blockchain Workbench. |
| connectionId          | Eindeutiger Bezeichner für die Blockchainverbindung |
| messageSchemaVersion  | Version des Messagingschemas |
| messageName           | **CreateContractActionUpdate** |
| status                | Status der Anforderung zum Erstellen der Vertragsaktion. Mögliche Werte: **Submitted**, **Committed**, **Failure**.                         |
| additionalInformation | Weitere Informationen basierend auf dem Status |

Beispiel für eine übermittelte Antwort auf **create contract action** von Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Beispiel für eine committete Antwort auf **create contract action** von Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Wenn die Anforderung nicht erfolgreich war, werden Details zum Fehler in den zusätzlichen Informationen bereitgestellt.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Eingabe-API-Fehlercodes und -meldungen

**Fehlercode 4000: Fehler – Ungültige Anforderung**
- Ungültige Verbindungs-ID
- Fehler bei der Deserialisierung von CreateUserRequest
- Fehler bei der Deserialisierung von CreateContractRequest
- Fehler bei der Deserialisierung von CreateContractActionRequest
- Die Anwendung {identifiziert durch Anwendungsnamen} ist nicht vorhanden.
- Die Anwendung {identifiziert durch Anwendungsnamen} enthält keinen Workflow.
- UserChainIdentifier ist nicht vorhanden.
- Der Vertrag {identifiziert durch Ledger-ID} ist nicht vorhanden.
- Der Vertrag {identifiziert durch Ledger-ID} enthält keine Funktion {Name der Workflowfunktion}
- UserChainIdentifier ist nicht vorhanden.

**Fehlercode 4090: Konfliktfehler**
- Der Benutzer ist bereits vorhanden.
- Der Vertrag ist bereits vorhanden.
- Die Vertragsaktion ist bereits vorhanden.

**Fehlercode 5000: Interner Serverfehler**
- Ausnahmemeldungen

## <a name="event-notifications"></a>Ereignisbenachrichtigungen

Durch Ereignisbenachrichtigungen können Benutzer und Downstreamsysteme über Ereignisse informiert werden, die in Blockchain Workbench und dem damit verbundenen Blockchainnetzwerk auftreten. Ereignisbenachrichtigungen können direkt im Code genutzt oder mit Tools wie Logic Apps und Flow verwendet werden, um den Datenfluss zu Downstreamsystemen auszulösen.

Weitere Informationen zu den verschiedenen Nachrichten, die empfangen werden können, finden Sie unter [Referenz zur Benachrichtigungsnachricht](#notification-message-reference).

### <a name="consuming-event-grid-events-with-azure-functions"></a>Nutzen von Event Grid-Ereignissen mit Azure Functions

Wenn ein Benutzer mit Event Grid über Ereignisse in Blockchain Workbench benachrichtigt werden möchte, können Sie Ereignisse aus Event Grid mithilfe von Azure Functions nutzen.

1. Erstellen Sie im Azure-Portal eine **Azure-Funktionen-App**.
2. Erstellen Sie eine neue Funktion.
3. Suchen Sie die Vorlage für Event Grid. Der standardmäßige Vorlagencode zum Lesen der Nachricht wird angezeigt. Ändern Sie den Code nach Bedarf.
4. Speichern Sie die Funktion. 
5. Wählen Sie das Event Grid aus der Blockchain Workbench-Ressourcengruppe aus.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Nutzen von Event Grid-Ereignissen mit Logic Apps

1. Erstellen Sie im Azure-Portal eine neue **Azure Logic-App**.
2. Beim Öffnen der Azure Logic-App im Portal werden Sie aufgefordert, einen Trigger auszuwählen. Wählen Sie **Azure Event Grid – Wenn ein Ressourcenereignis eintritt** aus.
3. Wenn der Workflow-Designer angezeigt wird, werden Sie aufgefordert, sich anzumelden.
4. Wählen Sie das Abonnement aus. Ressource als **Microsoft.EventGrid.Topics**. Wählen Sie den **Ressourcennamen** aus dem Namen der Ressource aus der Azure Blockchain Workbench-Ressourcengruppe aus.
5. Wählen Sie das Event Grid aus der Blockchain Workbench-Ressourcengruppe aus.

## <a name="using-service-bus-topics-for-notifications"></a>Verwenden von Service Bus-Themen für Benachrichtigungen

Mithilfe von Service Bus-Themen können Benutzer über Ereignisse in Blockchain Workbench benachrichtigt werden. 

1. Navigieren Sie zum Service Bus innerhalb der Workbench-Ressourcengruppe.
2. Wählen Sie **Themen** aus.
3. Wählen Sie **egress-topic** aus.
4. Erstellen Sie ein neues Abonnement zu diesem Thema. Erhalten Sie dafür einen Schlüssel.
5. Erstellen Sie ein Programm, das Ereignisse aus diesem Abonnement abonniert.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Nutzen von Service Bus-Nachrichten mit Logic Apps

1. Erstellen Sie im Azure-Portal eine neue **Azure Logic-App**.
2. Beim Öffnen der Azure Logic-App im Portal werden Sie aufgefordert, einen Trigger auszuwählen. Geben Sie in das Suchfeld **Service Bus** ein, und wählen Sie den passenden Trigger für den Typ der Interaktion mit dem Service Bus. Zum Beispiel, **Service Bus – Wenn eine Nachricht in einem Themenabonnement empfangen wird (automatisch abschließen)** .
3. Wenn der Workflow-Designer angezeigt wird, geben Sie die Verbindungsinformationen für den Service Bus an.
4. Wählen Sie Ihr Abonnement aus, und legen Sie das Thema **workbench-external** fest.
5. Entwickeln Sie die Logik für Ihre Anwendung, die die Nachricht von diesem Trigger verwendet.

## <a name="notification-message-reference"></a>Referenz zur Benachrichtigungsnachricht

Abhängig von **messageName** weisen die Benachrichtigungen einen der folgenden Nachrichtentypen auf.

### <a name="block-message"></a>Blocknachricht

Enthält Informationen zu einzelnen Blöcken. Die Blocknachricht (*BlockMessage*) enthält einen Abschnitt mit Block-Level-Informationen und einen Abschnitt mit Transaktionsinformationen.

| NAME | BESCHREIBUNG |
|------|-------------|
| block | Enthält [Blockinformationen](#block-information) |
| transactions | Enthält eine Sammlung mit [Transaktionsinformationen](#transaction-information) für den Block |
| connectionId | Eindeutiger Bezeichner für die Verbindung |
| messageSchemaVersion | Version des Messagingschemas |
| messageName | **BlockMessage** |
| additionalInformation | Bereitgestellte Zusatzinformationen |

#### <a name="block-information"></a>Blockinformationen

| NAME              | BESCHREIBUNG |
|-------------------|-------------|
| blockId           | Eindeutiger Bezeichner für den Block innerhalb von Azure Blockchain Workbench |
| blockNumber       | Eindeutiger Bezeichner für einen Block im Ledger |
| blockHash         | Der Hash des Blocks |
| previousBlockHash | Der Hash des vorherigen Blocks |
| blockTimestamp    | Der Zeitstempel des Blocks |

#### <a name="transaction-information"></a>Transaktionsinformationen

| NAME               | BESCHREIBUNG |
|--------------------|-------------|
| transactionId      | Eindeutiger Bezeichner für die Transaktion innerhalb von Azure Blockchain Workbench |
| transactionHash    | Der Hash der Transaktion im Ledger |
| from               | Eindeutiger Bezeichner im Ledger für den Transaktionsursprung |
| zu                 | Eindeutiger Bezeichner im Ledger für das Transaktionsziel |
| provisioningStatus | Identifiziert den aktuellen Status des Bereitstellungsprozesses für die Transaktion. Mögliche Werte: </br>0 – Die Transaktion wurde von der API in der Datenbank erstellt</br>1 – Die Transaktion wurde an den Ledger gesendet</br>2 – Die Transaktion wurde erfolgreich im Ledger committet</br>3 oder 4 – Fehler beim Committen der Transaktion im Ledger</br>5 – Transaktion erfolgreich im Ledger committet |

Beispiel für eine Blocknachricht (*BlockMessage*) aus Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Vertragsnachricht

Enthält Informationen zu einem Vertrag. Die Nachricht enthält einen Abschnitt mit Vertragseigenschaften und einen Abschnitt mit Transaktionsinformationen. Alle Transaktionen, durch die der Vertrag für den spezifischen Block geändert wurde, sind im Transaktionsabschnitt enthalten.

| NAME | BESCHREIBUNG |
|------|-------------|
| blockId | Eindeutiger Bezeichner für den Block innerhalb von Azure Blockchain Workbench |
| blockHash | Hash des Blocks |
| modifyingTransactions | [Transaktionen, die den Vertrag geändert haben](#modifying-transaction-information) |
| contractId | Eindeutiger Bezeichner für den Vertrag innerhalb von Azure Blockchain Workbench. |
| contractLedgerIdentifier | Eindeutiger Bezeichner für den Vertrag im Ledger |
| contractProperties | [Eigenschaften des Vertrags](#contract-properties) |
| isNewContract | Gibt an, ob dieser Vertrag neu erstellt wurde. Mögliche Werte: „true“: Der Vertrag wurde neu erstellt. „false“: Der Vertrag wurde aktualisiert. |
| connectionId | Eindeutiger Bezeichner für die Verbindung |
| messageSchemaVersion | Version des Messagingschemas |
| messageName | **ContractMessage** |
| additionalInformation | Bereitgestellte Zusatzinformationen |

#### <a name="modifying-transaction-information"></a>Ändern von Transaktionsinformationen

| NAME               | BESCHREIBUNG |
|--------------------|-------------|
| transactionId | Eindeutiger Bezeichner für die Transaktion innerhalb von Azure Blockchain Workbench |
| transactionHash | Der Hash der Transaktion im Ledger |
| from | Eindeutiger Bezeichner im Ledger für den Transaktionsursprung |
| zu | Eindeutiger Bezeichner im Ledger für das Transaktionsziel |

#### <a name="contract-properties"></a>Vertragseigenschaften

| NAME               | BESCHREIBUNG |
|--------------------|-------------|
| workflowPropertyId | Eindeutiger Bezeichner für die Workfloweigenschaft innerhalb von Azure Blockchain Workbench |
| name | Name der Workfloweigenschaft |
| value | Wert der Workfloweigenschaft |

Beispiel für eine Vertragsnachricht (*ContractMessage*) aus Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Ereignisnachricht: Aufruf der Vertragsfunktion

Enthält Informationen, wenn eine Vertragsfunktion aufgerufen wird. Hierzu zählen etwa der Funktionsname, die Eingabeparameter und der Aufrufer der Funktion.

| NAME | BESCHREIBUNG |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| caller                      | [Aufruferinformationen](#caller-information) |
| contractId                  | Eindeutiger Bezeichner für den Vertrag innerhalb von Azure Blockchain Workbench. |
| contractLedgerIdentifier    | Eindeutiger Bezeichner für den Vertrag im Ledger |
| functionName                | Name der Funktion |
| parameters                  | [Parameterinformationen](#parameter-information) |
| transaction                 | Transaktionsinformationen |
| inTransactionSequenceNumber | Die Sequenznummer der Transaktion im Block |
| connectionId                | Eindeutiger Bezeichner für die Verbindung |
| messageSchemaVersion        | Version des Messagingschemas |
| messageName                 | **EventMessage** |
| additionalInformation       | Bereitgestellte Zusatzinformationen |

#### <a name="caller-information"></a>Aufruferinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| type | Art des Aufrufers (beispielsweise Benutzer oder Vertrag) |
| id | Eindeutiger Bezeichner für den Aufrufer innerhalb von Azure Blockchain Workbench |
| ledgerIdentifier | Eindeutiger Bezeichner für den Aufrufer im Ledger |

#### <a name="parameter-information"></a>Parameterinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| name | Parametername |
| value | Parameterwert |

#### <a name="event-message-transaction-information"></a>Ereignisnachricht: Transaktionsinformationen

| NAME               | BESCHREIBUNG |
|--------------------|-------------|
| transactionId      | Eindeutiger Bezeichner für die Transaktion innerhalb von Azure Blockchain Workbench |
| transactionHash    | Der Hash der Transaktion im Ledger |
| from               | Eindeutiger Bezeichner im Ledger für den Transaktionsursprung |
| zu                 | Eindeutiger Bezeichner im Ledger für das Transaktionsziel |

Beispiel für *EventMessage ContractFunctionInvocation* aus Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Ereignisnachricht: Anwendungserfassung

Enthält Informationen, wenn eine Anwendung in Workbench hochgeladen wird – etwa Name und Version der hochgeladenen Anwendung.

| NAME | BESCHREIBUNG |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Eindeutiger Bezeichner für die Anwendung innerhalb von Azure Blockchain Workbench |
| applicationName | Anwendungsname |
| applicationDisplayName | Anwendungsanzeigename |
| applicationVersion | Anwendungsversion |
| applicationDefinitionLocation | URL, unter der sich die Anwendungskonfigurationsdatei befindet |
| contractCodes | Sammlung von [Vertragscodes](#contract-code-information) für die Anwendung |
| applicationRoles | Sammlung von [Anwendungsrollen](#application-role-information) für die Anwendung |
| applicationWorkflows | Sammlung von [Anwendungsworkflows](#application-workflow-information) für die Anwendung |
| connectionId | Eindeutiger Bezeichner für die Verbindung |
| messageSchemaVersion | Version des Messagingschemas |
| messageName | **EventMessage** |
| additionalInformation | Zu den hier bereitgestellten Zusatzinformationen zählen unter anderem die Anwendungsworkflowzustände sowie Übergangsinformationen. |

#### <a name="contract-code-information"></a>Vertragscodeinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für die Vertragscodedatei innerhalb von Azure Blockchain Workbench |
| ledgerId | Eindeutiger Bezeichner für den Ledger innerhalb von Azure Blockchain Workbench |
| location | URL, unter der sich die Vertragscodedatei befindet |

#### <a name="application-role-information"></a>Anwendungsrolleninformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für die Anwendungsrolle innerhalb von Azure Blockchain Workbench |
| name | Name der Anwendungsrolle |

#### <a name="application-workflow-information"></a>Anwendungsworkflowinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für den Anwendungsworkflow innerhalb von Azure Blockchain Workbench |
| name | Name des Anwendungsworkflows |
| displayName | Anzeigename des Anwendungsworkflows |
| functions | Sammlung von [Funktionen für den Anwendungsworkflow](#workflow-function-information)|
| states | Sammlung von [Zuständen für den Anwendungsworkflow](#workflow-state-information) |
| properties | [Informationen zu Workfloweigenschaften](#workflow-property-information) für die Anwendung |

##### <a name="workflow-function-information"></a>Workflowfunktionsinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für die Anwendungsworkflowfunktion innerhalb von Azure Blockchain Workbench |
| name | Funktionsname |
| parameters | Parameter für die Funktion |

##### <a name="workflow-state-information"></a>Informationen zum Workflowstatus

| NAME | BESCHREIBUNG |
|------|-------------|
| name | Name des Zustands |
| displayName | Anzeigename des Zustands |
| style | Zustandsstil (erfolgreich oder Fehler) |

##### <a name="workflow-property-information"></a>Workfloweigenschaftsinformationen

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für die Anwendungsworkfloweigenschaft innerhalb von Azure Blockchain Workbench |
| name | Eigenschaftenname |
| type | Eigenschaftstyp |

Beispiel für *EventMessage ApplicationIngestion* aus Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Ereignisnachricht: Rollenzuweisung

Enthält Informationen, wenn einem Benutzer eine Rolle in Workbench zugewiesen wird. Hierzu zählt unter anderem, wer die Rollenzuweisung vorgenommen hat, sowie der Name der Rolle und die entsprechende Anwendung.

| NAME | BESCHREIBUNG |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Eindeutiger Bezeichner für die Anwendung innerhalb von Azure Blockchain Workbench |
| applicationName | Anwendungsname |
| applicationDisplayName | Anwendungsanzeigename |
| applicationVersion | Anwendungsversion |
| applicationRole        | Informationen zur [Anwendungsrolle](#roleassignment-application-role) |
| assigner               | Informationen zum [Zuweiser](#roleassignment-assigner) |
| assignee               | Informationen zur [zugewiesenen Person](#roleassignment-assignee) |
| connectionId           | Eindeutiger Bezeichner für die Verbindung |
| messageSchemaVersion   | Version des Messagingschemas |
| messageName            | **EventMessage** |
| additionalInformation  | Bereitgestellte Zusatzinformationen |

#### <a name="roleassignment-application-role"></a>RoleAssignment: Anwendungsrolle

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner für die Anwendungsrolle innerhalb von Azure Blockchain Workbench |
| name | Name der Anwendungsrolle |

#### <a name="roleassignment-assigner"></a>RoleAssignment: Zuweiser

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner des Benutzers innerhalb von Azure Blockchain Workbench |
| type | Art des Zuweisers |
| chainIdentifier | Eindeutiger Bezeichner des Benutzers im Ledger |

#### <a name="roleassignment-assignee"></a>RoleAssignment: zugewiesene Person

| NAME | BESCHREIBUNG |
|------|-------------|
| id | Eindeutiger Bezeichner des Benutzers innerhalb von Azure Blockchain Workbench |
| type | Art der zugewiesenen Person |
| chainIdentifier | Eindeutiger Bezeichner des Benutzers im Ledger |

Beispiel für *EventMessage RoleAssignment* aus Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Smart Contract-Integrationsmuster](integration-patterns.md)
