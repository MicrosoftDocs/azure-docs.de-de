---
title: Erstellen einer Blockchainanwendung in Blockchain Workbench
description: Tutorial zum Erstellen einer Blockchainanwendung in Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/30/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: b444ad799eaa356d654952c32ac58188de8d7131
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417372"
---
# <a name="tutorial-create-a-blockchain-application-in-azure-blockchain-workbench"></a>Tutorial: Erstellen einer Blockchainanwendung in Blockchain Workbench

Mithilfe von Azure Blockchain Workbench können Sie Blockchainanwendungen erstellen, die mehrere Workflows darstellen, die durch Konfigurations- und intelligenten Vertragscode definiert werden.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Konfigurieren einer Blockchainanwendung
> * Erstellen einer Datei mit intelligentem Vertragscode
> * Hinzufügen einer Blockchainanwendung zu Blockchain Workbench
> * Hinzufügen von Mitgliedern zur Blockchainanwendung

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Eine Blockchain Workbench-Bereitstellung. Weitere Informationen zu Details dieser Bereitstellung finden Sie unter [Azure Blockchain Workbench-Bereitstellung](deploy.md).
* Azure Active Directory-Benutzer im Mandanten, die mit Blockchain Workbench verknüpft sind. Weitere Informationen finden Sie unter [Hinzufügen von Azure AD-Benutzer in Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Ein Blockchain Workbench-Administratorkonto. Weitere Informationen finden Sie unter [Hinzufügen von Blockchain Workbench-Administratoren in Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Blockchain

Erstellen Sie eine einfache Anwendung, in der ein Anforderer eine Anforderung sendet und ein Antwortdienst eine Antwort auf die Anforderung sendet.
Zum Beispiel kann eine Anforderung lauten: „Hallo, wie geht es dir?“, und die Antwort kann lauten: „Mir geht es gut“. Sowohl die Anforderung als auch die Antwort werden auf der zugrundeliegenden Blockchain aufgezeichnet.

Führen Sie die Schritte zum Erstellen der Anwendungsdateien aus, oder [laden Sie das Beispiel aus GitHub herunter](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Konfigurationsdatei

Konfigurationsmetadaten definieren die allgemeinen Workflows und das Interaktionsmodell der Blockchainanwendung. Zudem stellen die Konfigurationsmetadaten die Workflowstufen und das Interaktionsmodell der Blockchainanwendung dar.

1. Erstellen Sie in Ihrem bevorzugten Editor eine Datei namens `HelloBlockchain.json`.
2. Fügen Sie die folgende JSON-Funktion hinzu, um die Konfiguration der Blockchainanwendung zu definieren.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Speichern Sie die Datei `HelloBlockchain.json`.

In der Konfigurationsdatei gibt es mehrere Abschnitte. Hier sind die Details zu jedem Abschnitt:

### <a name="application-metadata"></a>Anwendungsmetadaten

Der Anfang der Konfigurationsdatei enthält Informationen über die Anwendung einschließlich Anwendungsname und Beschreibung.

### <a name="application-roles"></a>Anwendungsrollen

Der Abschnitt zu den Anwendungsrollen definiert die Benutzerrollen, die innerhalb der Blockchainanwendung agieren oder teilnehmen können. Sie definieren eine Reihe von unterschiedlichen Rollen basierend auf der Funktionalität. Im Anforderung/Antwort-Szenario wird zwischen der Funktionalität eines Anforderers als Entität, die Anforderungen erzeugt, und eines Antwortdiensts als Entität, die Antworten erzeugt, unterschieden.

### <a name="workflows"></a>Workflows

Workflows definieren eine oder mehrere Stufen und Aktionen des Vertrages. Im Anforderung/Antwort-Szenario ist die erste Stufe (Zustand) des Workflows ein Anforderer (Rolle), der eine Aktion (Übergang) ausführt, um eine Anforderung (Funktion) zu senden. Die nächste Stufe (Zustand) ist ein Antwortdienst (Rolle), der eine Aktion (Übergang) ausführt, um eine Antwort (Funktion) zu senden. Der Workflow einer Anwendung kann Eigenschaften, Funktionen und Zustände beinhalten, die den Ablauf eines Vertrages beschreiben.

Weitere Informationen zum Inhalt von Konfigurationsdateien finden Sie unter [Referenz zur Azure Blockchain-Workflowkonfiguration](configuration.md).

## <a name="smart-contract-code-file"></a>Datei mit intelligentem Vertragscode

Intelligente Verträge stellen die Geschäftslogik der Blockchainanwendung dar. Blockchain Workbench unterstützt derzeit Ethereum für den Blockchainledger. Ethereum verwendet [Solidity](https://solidity.readthedocs.io) als Programmiersprache, um selbstverstärkende Geschäftslogik für intelligente Verträge zu schreiben.

Intelligente Verträge in Solidity ähneln Klassen in objektorientierten Sprachen. Jeder Vertrag enthält Zustand und Funktionen, um die Stufen und Aktionen des intelligenten Vertrags zu implementieren.

Erstellen Sie in Ihrem bevorzugten Editor eine Datei namens `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Versionspragma

Eine bewährte Methode ist, die Version von Solidity anzugeben, auf die Sie abzielen. Die Angabe der Version hilft, Inkompatibilitäten mit zukünftigen Solidity-Versionen zu vermeiden.

Fügen Sie das folgende Versionspragma am Anfang der intelligenten Vertragscodedatei `HelloBlockchain.sol` hinzu.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Beziehung zwischen Konfiguration und intelligentem Vertragscode

Blockchain Workbench verwendet die Konfigurationsdatei und die intelligente Vertragscodedatei, um eine Blockchainanwendung zu erstellen. Es besteht ein Zusammenhang zwischen dem, was in der Konfiguration definiert ist, und dem Code im intelligenten Vertrag. Vertragsdetails, Funktionen, Parameter und Typen müssen übereinstimmen, um die Anwendung zu erstellen. Blockchain Workbench überprüft die Dateien vor der Erstellung der Anwendung.

### <a name="contract"></a>Vertrag

Fügen Sie den Header des **Vertrags** zu Ihrer `HelloBlockchain.sol` intelligenten Vertragscodedatei hinzu.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Zustandsvariablen

Zustandsvariablen speichern für jede Vertragsinstanz Werte des Zustands. Die Zustandsvariablen in Ihrem Vertrag müssen mit den in der Konfigurationsdatei definierten Workfloweigenschaften übereinstimmen.

Fügen Sie die Zustandsvariablen zu Ihrem Vertrag in Ihrer intelligenten Vertragscodedatei `HelloBlockchain.sol` hinzu.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

Der Konstruktor definiert Eingabeparameter für eine neue intelligente Vertragsinstanz eines Workflows. Erforderliche Parameter für den Konstruktor werden als Konstruktorparameter in der Konfigurationsdatei definiert. Anzahl, Reihenfolge und Art der Parameter müssen in beiden Dateien übereinstimmen.

Schreiben Sie in die Konstruktorfunktion eine beliebige Geschäftslogik, die Sie vor dem Erstellen des Vertrags ausführen möchten. Initialisieren Sie z.B. die Zustandsvariablen mit Anfangswerten.

Fügen Sie die Konstruktorfunktion zu Ihrem Vertrag in Ihrer intelligenten Vertragscodedatei `HelloBlockchain.sol` hinzu.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

Funktionen sind die ausführbaren Einheiten der Geschäftslogik innerhalb eines Vertrags. Die für die Funktion erforderlichen Parameter sind als Funktionsparameter in der Konfigurationsdatei definiert. Anzahl, Reihenfolge und Art der Parameter müssen in beiden Dateien übereinstimmen. Funktionen sind mit Übergängen in einem Blockchain Workbench-Workflow in der Konfigurationsdatei verknüpft. Ein Übergang ist eine Aktion, die ausgeführt wird, um zur nächsten Stufe des Workflows einer Anwendung zu gelangen, die durch den Vertrag festgelegt ist.

Schreiben Sie eine beliebige Geschäftslogik, die Sie in der Funktion ausführen möchten. Zum Beispiel, dass der Wert einer Zustandsvariablen geändert wird.

1. Fügen Sie die folgenden Funktionen zu Ihrem Vertrag in Ihrer intelligenten Vertragscodedatei `HelloBlockchain.sol` hinzu.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Speichern Sie Ihre intelligente Vertragscodedatei `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Hinzufügen einer Blockchainanwendung zu Blockchain Workbench

Um eine Blockchainanwendung zu Blockchain Workbench hinzuzufügen, laden Sie die Konfigurations- und intelligenten Vertragsdateien hoch, um die Anwendung zu definieren.

1. Navigieren Sie in einem Webbrowser zur Blockchain Workbench-Webadresse. (Beispiel: `https://{workbench URL}.azurewebsites.net/`) Die Webanwendung wird erstellt, wenn Sie Blockchain Workbench bereitstellen. Informationen darüber, wie Sie Ihre Blockchain Workbench-Webadresse finden, erhalten Sie unter [Blockchain Workbench-Web-URL](deploy.md#blockchain-workbench-web-url).
2. Melden Sie sich als [Blockchain Workbench-Administrator](manage-users.md#manage-blockchain-workbench-administrators) an.
3. Wählen Sie **Anwendungen** > **Neu**. Der Bereich **Neue Anwendung** wird angezeigt.
4. Wählen Sie **Vertragskonfiguration hochladen** > **Durchsuchen**, um die von Ihnen erstellte Konfigurationsdatei **HelloBlockchain.json** zu finden. Die Konfigurationsdatei wird automatisch überprüft. Wählen Sie den Link **Anzeigen**, um Überprüfungsfehler anzuzeigen. Beheben Sie Überprüfungsfehler, bevor Sie die Anwendung bereitstellen.
5. Wählen Sie **Vertragscode hochladen** > **Durchsuchen**, um die intelligente Vertragscodedatei **HelloBlockchain.sol** zu finden. Die Codedatei wird automatisch überprüft. Wählen Sie den Link **Anzeigen**, um Überprüfungsfehler anzuzeigen. Beheben Sie Überprüfungsfehler, bevor Sie die Anwendung bereitstellen.
6. Wählen Sie **Bereitstellen**, um die Blockchainanwendung basierend auf den Dateien der Konfiguration und des intelligenten Vertrags zu erstellen.

Die Bereitstellung der Blockchainanwendung kann einige Minuten in Anspruch nehmen. Wenn die Bereitstellung abgeschlossen ist, wird die neue Anwendung unter **Anwendungen** angezeigt. 

> [!NOTE]
> Sie können Blockchainanwendungen auch über die [Azure Blockchain Workbench-REST-API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench) erstellen.

## <a name="add-blockchain-application-members"></a>Hinzufügen von Mitgliedern zu einer Blockchainanwendung

Fügen Sie Anwendungsmitglieder zu Ihrer Anwendung hinzu, um Aktionen und Verträge zu initiieren und auszuführen. Damit Sie Anwendungsmitglieder hinzufügen können, müssen Sie ein [Blockchain Workbench-Administrator](manage-users.md#manage-blockchain-workbench-administrators) sein.

1. Wählen Sie **Anwendungen** > **Hello, Blockchain!** aus.
2. Die Anzahl der Mitglieder, die der Anwendung zugeordnet sind, wird in der oberen rechten Ecke der Seite angezeigt. Bei einer neuen Anwendung ist die Anzahl der Mitglieder gleich Null.
3. Wählen Sie in der oberen rechten Ecke der Seite den Link **Mitglieder**. Daraufhin wird eine aktuelle Liste der Mitglieder für die Anwendung angezeigt.
4. Wählen Sie in der Mitgliederliste die Option **Mitglieder hinzufügen** aus.
5. Wählen Sie den Namen des hinzuzufügenden Mitglieds aus, oder geben Sie diesen ein. Es werden nur Azure AD-Benutzer aufgelistet, die im Blockchain Workbench-Mandanten vorhanden sind. Wenn der Benutzer nicht gefunden wird, müssen Sie [Azure AD-Benutzer hinzufügen](manage-users.md#add-azure-ad-users).
6. Wählen Sie die **Rolle** für das Mitglied aus. Für das erste Mitglied wählen Sie als Rolle **Anforderer** aus.
7. Wählen Sie **Hinzufügen**, um das Mitglied mit der zugehörigen Rolle zur Anwendung hinzuzufügen.
8. Fügen Sie der Anwendung ein weiteres Mitglied mit der Rolle **Antwortdienst** hinzu.

Weitere Informationen zum Verwalten von Benutzern in Blockchain Workbench finden Sie unter [Verwalten von Benutzern in Azure Blockchain Workbench](manage-users.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel Sie haben eine allgemeine Anforderung/Antwort-Anwendung erstellt. Informationen zur Verwendung der Anwendung finden Sie im nächsten Artikel.

> [!div class="nextstepaction"]
> [Verwenden einer Blockchainanwendung](use.md)
