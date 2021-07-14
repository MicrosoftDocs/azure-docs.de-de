---
title: 'Tutorial: Erstellen und Bereitstellen von Smart Contracts – Azure Blockchain Service'
description: Tutorial zur Verwendung des Azure Blockchain Development Kit für die Ethereum-Erweiterung in Visual Studio Code zum Erstellen und Bereitstellen von Smart Contracts in Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628644"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Tutorial: Erstellen und Bereitstellen von Smart Contracts in Azure Blockchain Service

In diesem Tutorial verwenden Sie das Azure Blockchain Development Kit für die Ethereum-Erweiterung in Visual Studio Code zum Erstellen und Bereitstellen von Smart Contracts in Azure Blockchain Service. Außerdem verwenden Sie das Development Kit zum Ausführen einer Smart Contract-Funktion per Transaktion.

Sie verwenden das Azure Blockchain Development Kit für Ethereum für folgende Zwecke:

> [!div class="checklist"]
> * Erstellen eines Smart Contracts
> * Bereitstellen eines Smart Contracts
> * Ausführen einer Smart Contract-Funktion per Transaktion

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Quickstart: Herstellen einer Verbindung mit einem Azure Blockchain Service-Konsortiumsnetzwerk mithilfe von Visual Studio Code](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit für Ethereum-Erweiterung](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x oder höher](https://nodejs.org/download)
* [Git 2.10.x oder höher](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Unter Windows ist ein installierter C++-Compiler für das Modul „node-gyp“ erforderlich. Sie können die MSBuild-Tools verwenden:

* Wenn Visual Studio 2017 installiert ist, konfigurieren Sie npm mit dem Befehl `npm config set msvs_version 2017 -g` für die Verwendung der MSBuild-Tools.
* Ist Visual Studio 2019 installiert, legen Sie den Pfad der MSBuild-Tools für npm fest. Zum Beispiel, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Installieren Sie andernfalls die eigenständigen VS-Buildtools. Verwenden Sie hierzu `npm install --global windows-build-tools` in einer Befehlsshell mit erhöhten Rechten (*Als Administrator ausführen*).

Weitere Informationen zu node-gyp finden Sie im [node-gyp-Repository auf GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Erstellen eines Smart Contracts

Im Azure Blockchain Development Kit für Ethereum werden Projektvorlagen und Truffle-Tools verwendet, die als Hilfe beim Vorbereiten, Erstellen und Bereitstellen von Contracts dienen. Bevor Sie beginnen, führen Sie die erforderlichen Schritte in [Schnellstart: Herstellen einer Verbindung mit einem Azure Blockchain Service-Konsortiumsnetzwerk mithilfe von Visual Studio Code](connect-vscode.md) aus. Der Schnellstart führt Sie durch die Installation und Konfiguration des Azure Blockchain Development Kit für Ethereum.

1. Wählen Sie in der VS Code-Befehlspalette die Option **Blockchain: New Solidity Project** (Azure Blockchain: Neues Solidity-Projekt).
1. Wählen Sie **Create basic project** (Einfaches Projekt erstellen).
1. Erstellen Sie einen neuen Ordner mit dem Namen `HelloBlockchain`, und wählen Sie einen **neuen Projektpfad** aus.

Das Azure Blockchain Development Kit erstellt und initialisiert ein neues Solidity-Projekt für Sie. Das Basisprojekt enthält den Smart Contract **HelloBlockchain** als Beispiel sowie alle Dateien, die Sie für die Erstellung und die Bereitstellung für Ihr Konsortiumsmitglied in Azure Blockchain Service benötigen. Es kann einige Minuten dauern, bis das Projekt erstellt wurde. Sie können den Status im Terminalbereich von VS Code verfolgen, indem Sie die Ausgabe für Azure Blockchain auswählen.

Die Projektstruktur sieht wie im folgenden Beispiel aus:

   ![Solidity-Projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Erstellen eines Smart Contracts

Smart Contracts befinden sich im Verzeichnis **contracts** des Projekts. Sie kompilieren Smart Contracts, bevor Sie diese für eine Blockchain bereitstellen. Verwenden Sie den Befehl **Build Contracts** (Contracts erstellen), um alle Smart Contracts in Ihrem Projekt zu kompilieren.

1. Erweitern Sie in der Seitenleiste des VS Code-Explorers den Ordner **contracts** Ihres Projekts.
1. Klicken Sie mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü die Option **Build Contracts** (Contracts erstellen).

    ![Auswählen von „Build contracts“ (Contracts erstellen) im Menü ](./media/send-transaction/build-contracts.png)

Vom Azure Blockchain Development Kit wird Truffle verwendet, um Smart Contracts zu kompilieren.

![Truffle-Compilerausgabe](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Bereitstellen eines Smart Contracts

Für Truffle werden Migrationsskripts verwendet, um Ihre Contracts in einem Ethereum-Netzwerk bereitzustellen. Migrationen sind JavaScript-Dateien, die im Verzeichnis **migrations** des Projekts enthalten sind.

1. Klicken Sie zum Bereitstellen Ihres Smart Contracts mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü die Option **Deploy Contracts** (Contracts bereitstellen).
1. Wählen Sie Ihr Azure Blockchain-Konsortiumsnetzwerk in der Befehlspalette. Das Blockchainnetzwerk für Konsortien wurde der Truffle-Konfigurationsdatei des Projekts hinzugefügt, als Sie das Projekt erstellt haben.
1. Wählen Sie **Generate mnemonic** (Mnemonisches Zeichen generieren). Wählen Sie einen Dateinamen aus, und speichern Sie die Datei im Projektordner. Beispiel: `myblockchainmember.env`. Die Datei mit dem mnemonischen Zeichen wird verwendet, um einen privaten Ethereum-Schlüssel für Ihr Blockchainmitglied zu generieren.

Für das Azure Blockchain Development Kit wird Truffle zum Ausführen des Migrationsskripts verwendet, um die Contracts für die Blockchain bereitzustellen.

![Erfolgreich bereitgestellter Contract](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Aufrufen einer Funktion des Contracts
Mit der Funktion **SendRequest** des Contracts **HelloBlockchain** wird die Zustandsvariable **RequestMessage** geändert. Die Änderung des Zustands eines Blockchainnetzwerks wird nicht per Transaktion durchgeführt. Sie können ein Skript erstellen, um die Funktion **SendRequest** per Transaktion auszuführen.

1. Erstellen Sie im Stammverzeichnis Ihres Truffle-Projekts eine neue Datei, und geben Sie ihr den Namen `sendrequest.js`. Fügen Sie der Datei den folgenden Web3-JavaScript-Code hinzu.

    ```javascript
    var HelloBlockchain = artifacts.require(&quot;HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Wenn mit dem Azure Blockchain Development Kit ein Projekt erstellt wird, wird die Truffle-Konfigurationsdatei mit den Endpunktdetails Ihres Blockchainnetzwerks für Konsortien generiert. Öffnen Sie in Ihrem Projekt die Datei **truffle-config.js**. In der Konfigurationsdatei sind zwei Netzwerke aufgeführt: ein benanntes Entwicklungsnetzwerk und ein Netzwerk mit demselben Namen wie das Konsortium.
1. Verwenden Sie Truffle im Terminalbereich von VS Code, um das Skript in Ihrem Blockchainnetzwerk für Konsortien auszuführen. Wählen Sie in der Menüleiste des Terminalbereichs die Registerkarte **Terminal** und in der Dropdownliste die Option **PowerShell**.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Ersetzen Sie \<blockchain network\> durch den Namen des Blockchainnetzwerks, das in der Datei **truffle-config.js** definiert ist.

Truffle führt das Skript in Ihrem Blockchainnetzwerk aus.

![Ausgabe, die zeigt, dass die Transaktion gesendet wurde](./media/send-transaction/execute-transaction.png)

Wenn Sie die Funktion eines Contracts per Transaktion ausführen, wird die Transaktion erst verarbeitet, nachdem ein Block erstellt wurde. Bei Funktionen mit Ausführung per Transaktion wird anstelle eines Rückgabewerts eine Transaktions-ID zurückgegeben.

## <a name="query-contract-state"></a>Abfragen des Contract-Status

Smart Contract-Funktionen können den aktuellen Wert von Zustandsvariablen zurückgeben. Wir fügen eine Funktion hinzu, um den Wert einer Zustandsvariablen zurückzugeben.

1. Fügen Sie in der Datei **HelloBlockchain.sol** dem Smart Contract **HelloBlockchain** die Funktion **getMessage** hinzu.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Die Funktion gibt die in einer Zustandsvariablen gespeicherte Nachricht basierend auf dem aktuellen Zustand des Contracts zurück.

1. Klicken Sie mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü die Option **Build Contracts** (Contracts erstellen), um die Änderungen am Smart Contract zu kompilieren.
1. Klicken Sie zum Bereitstellen mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü die Option **Deploy Contracts** (Contracts bereitstellen). Wählen Sie Ihr Azure Blockchain-Konsortiumsnetzwerk in der Befehlspalette, wenn Sie dazu aufgefordert werden.
1. Erstellen Sie als Nächstes ein Skript, um die Funktion **getMessage** aufzurufen. Erstellen Sie im Stammverzeichnis Ihres Truffle-Projekts eine neue Datei, und geben Sie ihr den Namen `getmessage.js`. Fügen Sie der Datei den folgenden Web3-JavaScript-Code hinzu.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Verwenden Sie Truffle im Terminalbereich von VS Code, um das Skript in Ihrem Blockchainnetzwerk auszuführen. Wählen Sie in der Menüleiste des Terminalbereichs die Registerkarte **Terminal** und in der Dropdownliste die Option **PowerShell**.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Ersetzen Sie \<blockchain network\> durch den Namen des Blockchainnetzwerks, das in der Datei **truffle-config.js** definiert ist.

Das Skript fragt den Smart Contract ab, indem die Funktion „getMessage“ aufgerufen wird. Der aktuelle Wert der Zustandsvariablen **RequestMessage** wird zurückgegeben.

![Ausgabe der Abfrage „getmessage“ mit dem aktuellen Wert der Zustandsvariablen für „RequestMessage“](./media/send-transaction/execute-get.png)

Beachten Sie, dass der Wert nicht **Hello, blockchain!** lautet. Stattdessen ist der zurückgegebene Wert ein Platzhalter. Wenn Sie den Contract ändern und bereitstellen, wird die geänderte Version unter einer neuen Adresse bereitgestellt, und die Zustandsvariablen werden Werten im Smart Contract-Konstruktor zugewiesen. Im Truffle-Beispiel **2_deploy_contracts.js** für ein Migrationsskript wird der Smart Contract bereitgestellt und ein Platzhalterwert als Argument übergeben. Der Konstruktor legt die Zustandsvariable **RequestMessage** auf den Platzhalterwert fest, und der Wert wird zurückgegeben.

1. Führen Sie die Skripts **sendrequest.js** und **getmessage.js** erneut aus, um die Zustandsvariable **RequestMessage** festzulegen und den Wert abzufragen.

    ![Ausgabe der Skripts „sendrequest“ und „getmessage“, die zeigt, dass „RequestMessage“ gesendet wurde](./media/send-transaction/execute-set-get.png)

    Mit **sendrequest.js** wird die Zustandsvariable **RequestMessage** auf **Hello, blockchain!** festgelegt. Mit **getmessage.js** wird der Contract nach dem Wert der Zustandsvariablen **RequestMessage** abgefragt, und **Hello, blockchain!** wird zurückgegeben.
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn sie nicht mehr benötigt werden, können Sie die Ressourcen löschen. Löschen Sie hierzu die Ressourcengruppe `myResourceGroup`, die Sie im Rahmen der Schnellstartanleitung unter *Erstellen eines Blockchainmitglieds* zur Vorbereitung erstellt haben.

So löschen Sie die Ressourcengruppe:

1. Navigieren Sie im Azure-Portal im linken Navigationsbereich zu **Ressourcengruppe**, und wählen Sie die Ressourcengruppe aus, die gelöscht werden soll.
1. Wählen Sie die Option **Ressourcengruppe löschen**. Überprüfen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit dem Azure Blockchain Development Kit ein Solidity-Beispielprojekt erstellt. Sie haben einen Smart Contract erstellt und bereitgestellt und dann eine Funktion per Transaktion in einem Blockchain-Konsortiumsnetzwerk aufgerufen, das unter dem Azure Blockchain Service gehostet wird.

> [!div class="nextstepaction"]
> [Übersicht über die Entwicklung mit dem Azure Blockchain-Dienst](develop.md)
