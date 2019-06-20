---
title: Senden von Transaktionen mit dem Azure Blockchain-Dienst
description: Tutorial zur Verwendung des Azure Blockchain-Diensts zum Bereitstellen eines Smart Contracts und zum Senden einer privaten Transaktion.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417489"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Tutorial: Senden von Transaktionen mit dem Azure Blockchain-Dienst

In diesem Tutorial erstellen Sie Transaktionsknoten, um den Datenschutz von Verträgen und Transaktionen zu testen.  Sie verwenden Truffle zum Erstellen einer lokalen Entwicklungsumgebung, stellen einen Smart Contract bereit und senden eine private Transaktion.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Hinzufügen von Transaktionsknoten
> * Verwenden von Truffle zum Bereitstellen eines Smart Contracts
> * Senden einer Transaktion
> * Validieren des Datenschutzes einer Transaktion

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Quickstart: Create an Azure Blockchain Service using the Azure portal (Schnellstart: Erstellen eines Blockchainmitglieds über das Azure-Portal)](create-member.md) abgeschlossen
* [Quickstart: Use Truffle to connect to a an Azure Blockchain Service network (Schnellstart: Verwenden von Truffle zum Herstellen einer Verbindung mit einem Azure Blockchain-Netzwerk)](connect-truffle.md) abgeschlossen
* Installieren Sie [Truffle](https://github.com/trufflesuite/truffle). Für Truffle ist die Installation mehrerer Tools erforderlich, dazu gehören [Node.js](https://nodejs.org) und [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installieren Sie [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python ist für Web3 erforderlich.
* [Visual Studio Code](https://code.visualstudio.com/Download) installiert
* [Solidity-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) installiert

## <a name="create-transaction-nodes"></a>Erstellen von Transaktionsknoten

Sie verfügen standardmäßig über einen Transaktionsknoten. Im Folgenden fügen Sie zwei weitere hinzu. Einer dieser Knoten ist Teil der privaten Transaktion. Der andere ist nicht in der privaten Transaktion enthalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem Azure Blockchain-Mitglied, und klicken Sie auf **Transaktionsknoten > Hinzufügen**.
1. Vervollständigen Sie die Einstellungen für einen neuen Transaktionsknoten namens `alpha`.

    ![Erstellen eines Transaktionsknotens](./media/send-transaction/create-node.png)

    | Einstellung | Wert | BESCHREIBUNG |
    |---------|-------|-------------|
    | NAME | `alpha` | Der Name des Transaktionsknotens. Der Name wird zum Erstellen der DNS-Adresse für den Transaktionsknotenendpunkt verwendet. Beispiel: `alpha-mymanagedledger.blockchain.azure.com`. |
    | Kennwort | Ein sicheres Kennwort | Das Kennwort wird für den Zugriff auf den Transaktionsknotenendpunkt mit Standardauthentifizierung verwendet.

1. Klicken Sie auf **Erstellen**.

    Die Bereitstellung eines neuen Transaktionsknotens beansprucht etwa 10 Minuten.

1. Wiederholen Sie die Schritte 2 bis 4, um einen Transaktionsknoten namens `beta` hinzuzufügen.

Sie können mit dem Tutorial fortfahren, während die Knoten bereitgestellt werden. Wenn die Bereitstellung abgeschlossen ist, verfügen Sie über drei Transaktionsknoten.

## <a name="open-truffle-console"></a>Öffnen der Truffle-Konsole

1. Öffnen Sie eine Node.js-Eingabeaufforderung oder -Shell.
1. Ändern Sie den Pfad aus der Voraussetzung des [Schnellstarts zur Verwendung von Truffle zum Herstellen einer Verbindung mit einem Azure Blockchain-Netzwerk](connect-truffle.md) in das Truffle-Projektverzeichnis. Beispiel:

    ```bash
    cd truffledemo
    ```

1. Starten Sie die interaktive Entwicklungskonsole von Truffle.

    ``` bash
    truffle develop
    ```

    Truffle erstellt eine lokale Entwicklungsblockchain und stellt eine interaktive Konsole bereit.

## <a name="create-ethereum-account"></a>Erstellen eines Ethereum-Kontos

Verwenden Sie Web3, um eine Verbindung mit dem Standardtransaktionsknoten herzustellen und ein Ethereum-Konto zu erstellen. Sie können die Web3-Verbindungszeichenfolge aus dem Azure-Portal abrufen.

1. Navigieren Sie im Azure-Portal zum Standardtransaktionsknoten, und klicken Sie auf **Transaktionsknoten > Beispielcode > Web3**.
1. Kopieren Sie den JavaScript-Code aus **HTTPS (Zugriffsschlüssel 1)** ![Web3-Beispielcode](./media/send-transaction/web3-code.png)

1. Fügen Sie den Web3-JavaScript-Code für den Standardtransaktionsknoten in die interaktive Entwicklungskonsole von Truffle ein. Mit dem Code wird ein Web3-Objekt erstellt, das mit Ihrem Azure Blockchain-Transaktionsknoten verbunden ist.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Sie können Methoden für das Web3-Objekt aufrufen, um mit Ihrem Transaktionsknoten zu interagieren.

1. Erstellen Sie ein neues Konto auf dem Standardtransaktionsknoten. Ersetzen Sie den Kennwortparameter durch Ihr eigenes sicheres Kennwort.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Notieren Sie sich die zurückgegebene Kontoadresse und das Kennwort. Sie benötigen die Adresse und das Kennwort des Ethereum-Kontos im nächsten Abschnitt.

1. Beenden Sie die Truffle-Entwicklungsumgebung.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurieren eines Truffle-Projekts

Zum Konfigurieren des Truffle-Projekts benötigen Sie einige Informationen zum Transaktionsknoten aus dem Azure-Portal.

### <a name="transaction-node-public-key"></a>Öffentlicher Schlüssel für den Transaktionsknoten

Jeder Transaktionsknoten verfügt über einen öffentlichen Schlüssel. Mit dem öffentlichen Schlüssel können Sie private Transaktionen an den Knoten senden. Zum Senden einer Transaktion vom Standardtransaktionsknoten an den Transaktionsknoten *alpha* benötigen Sie den öffentlichen Schlüssel des Transaktionsknotens *alpha*.

Diesen öffentlichen Schlüssel finden Sie in der Liste der Transaktionsknoten. Kopieren Sie den öffentlichen Schlüssel für den Knoten „alpha“, und speichern Sie den Wert für später.

![Liste der Transaktionsknoten](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Endpunktadressen für den Transaktionsknoten

1. Navigieren Sie im Azure-Portal zu den einzelnen Transaktionsknoten, und klicken Sie auf **Transaktionsknoten > Verbindungszeichenfolgen**.
1. Kopieren Sie die Endpunkt-URL für jeden Transaktionsknoten aus **HTTPS (Zugriffsschlüssel 1)** , und speichern Sie sie. Sie benötigen die Endpunktadressen für die Smart Contract-Konfigurationsdatei im späteren Verlauf dieses Tutorials.

    ![Transaktionsendpunktadresse](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Bearbeiten der Konfigurationsdatei

1. Starten Sie Visual Studio Code, und öffnen Sie den Truffle-Projektverzeichnisordner über das Menü **Datei > Ordner öffnen**.
1. Öffnen Sie die Truffle-Konfigurationsdatei `truffle-config.js`.
1. Ersetzen Sie den Inhalt der Datei durch die folgenden Konfigurationsinformationen. Fügen Sie Variablen hinzu, die die Endpunktadressen und Kontoinformationen enthalten. Ersetzen Sie die Abschnitte in den spitzen Klammen durch die Werde, die Sie in den vorherigen Abschnitten abgerufen haben.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. Speichern Sie die Änderungen in `truffle-config.js`.

## <a name="create-smart-contract"></a>Erstellen des Smart Contracts

1. Erstellen Sie im Ordner **contracts** eine neue Datei namens `SimpleStorage.sol`. Fügen Sie den folgenden Code hinzu:

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. Erstellen Sie im Ordner **migrations** eine neue Datei namens `2_deploy_simplestorage.js`. Fügen Sie den folgenden Code hinzu:

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Ersetzen Sie die Werte in den spitzen Klammern.

    | Wert | BESCHREIBUNG
    |-------|-------------
    | \<öffentlicher Schlüssel des alpha-Knotens\> | Öffentlicher Schlüssel des alpha-Knotens
    | \<Adresse des Ethereum-Kontos\> | Ethereum-Kontoadresse, die im Standardtransaktionsknoten erstellt wurde

    In diesem Beispiel wird der Anfangswert des Werts **storeData** auf „42“ festgelegt.

    **privateFor** definiert die Knoten, auf denen der Vertrag verfügbar ist. In diesem Beispiel kann das Konto des Standardtransaktionsknotens private Transaktionen an den Knoten **alpha** übermitteln. Sie fügen öffentliche Schlüssel für alle Teilnehmer an der privaten Transaktion hinzu. Wenn Sie **privateFor:** und **from:** nicht einschließen, sind die Smart Contract-Transaktionen öffentlich und für alle Konsortiumsmitglieder sichtbar.

1. Speichern Sie alle Dateien, indem Sie auf **Datei > Alle speichern** klicken.

## <a name="deploy-smart-contract"></a>Bereitstellen des Smart Contracts

Verwenden Sie Truffle, um `SimpleStorage.sol` im Netzwerk des Standardtransaktionsknotens bereitzustellen.

```bash
truffle migrate --network defaultnode
```

Truffle kompiliert zunächst den Smart Contract **SimpleStorage** und stellt ihn dann bereit.

Beispielausgabe:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Überprüfen des Datenschutzes des Vertrags

Aufgrund des Datenschutzes des Vertrags, können Vertragswerte nur von Knoten abgefragt werden, die in **privateFor** deklariert wurden. In diesem Beispiel können Sie den Standardtransaktionsknoten abfragen, weil das Konto in diesem Knoten enthalten ist. 

1. Verwenden Sie die Truffle-Konsole, um eine Verbindung mit dem Standardtransaktionsknoten herzustellen.

    ```bash
    truffle console --network defaultnode
    ```

1. Führen Sie in der Truffle-Konsole Code aus, der den Wert der Vertragsinstanz zurückgibt.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Wenn das Abfragen des Standardtransaktionsknotens erfolgreich ist, wird der Wert „42“ zurückgegeben. Beispiel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Beenden Sie die Truffle-Konsole

    ```bash
    .exit
    ```

Da Sie den öffentlichen Schlüssel des Knotens **alpha** in **privateFor** deklariert haben, können Sie den Knoten **alpha** abfragen.

1. Stellen Sie mithilfe der Truffle-Konsole eine Verbindung mit dem Knoten **alpha** her.

    ```bash
    truffle console --network alpha
    ```

1. Führen Sie in der Truffle-Konsole Code aus, der den Wert der Vertragsinstanz zurückgibt.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Wenn das Abfragen des Knotens **alpha** erfolgreich ist, wird der Wert „42“ zurückgegeben. Beispiel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Beenden Sie die Truffle-Konsole.

    ```bash
    .exit
    ```

Da Sie den öffentlichen Schlüssel des Knotens **beta** nicht in **privateFor** deklariert haben, können Sie den Knoten **beta** wegen des Vertragsdatenschutzes nicht abfragen.

1. Stellen Sie mithilfe der Truffle-Konsole eine Verbindung mit dem Knoten **beta** her.

    ```bash
    truffle console --network beta
    ```

1. Führen Sie Code aus, der den Wert der Vertragsinstanz zurückgibt.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Das Abfragen des Knotens **beta** schlägt fehl, weil der Vertrag privat ist. Beispiel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Beenden Sie die Truffle-Konsole.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Senden einer Transaktion

1. Erstellen Sie eine Datei namens `sampletx.js`. Speichern Sie sie im Stammverzeichnis Ihres Projekts.
1. Mit dem folgenden Skript wird der Wert der Variablen **storedData** auf „65“ festgelegt. Fügen Sie den folgenden Code zur neuen Datei hinzu.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Ersetzen Sie die Werte in den spitzen Klammern, und speichern Sie dann die Datei.

    | Wert | BESCHREIBUNG
    |-------|-------------
    | \<öffentlicher Schlüssel des alpha-Knotens\> | Öffentlicher Schlüssel des alpha-Knotens
    | \<Adresse des Ethereum-Kontos\> | Ethereum-Kontoadresse, die im Standardtransaktionsknoten erstellt wurde

    **privateFor** definiert die Knoten, auf denen die Transaktion verfügbar ist. In diesem Beispiel kann das Konto des Standardtransaktionsknotens private Transaktionen an den Knoten **alpha** übermitteln. Sie müssen öffentliche Schlüssel für alle Teilnehmer an der privaten Transaktion hinzufügen.

1. Verwenden Sie Truffle, um das Skript für den Standardtransaktionsknoten auszuführen.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. Führen Sie in der Truffle-Konsole Code aus, der den Wert der Vertragsinstanz zurückgibt.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Wenn die Transaktion erfolgreich durchgeführt wurde, wird der Wert „65“ zurückgegeben. Beispiel:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Beenden Sie die Truffle-Konsole.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Validieren des Datenschutzes einer Transaktion

Aufgrund des Transaktionsdatenschutzes, können Transaktionen nur auf Knoten durchgeführt werden, die in **privateFor** deklariert wurden. In diesem Beispiel können Sie Transaktionen durchführen, weil Sie den öffentlichen Schlüssel des Knotens **alpha** in **privateFor** deklariert haben. 

1. Verwenden Sie Truffle, um die Transaktion auf dem Knoten **alpha** auszuführen.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Führen Sie Code aus, der den Wert der Vertragsinstanz zurückgibt.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Wenn die Transaktion erfolgreich durchgeführt wurde, wird der Wert „65“ zurückgegeben. Beispiel:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Beenden Sie die Truffle-Konsole.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcen nicht mehr benötigt werden, können Sie diese löschen, indem Sie die `myResourceGroup`-Ressourcengruppe löschen, die Sie mit Azure Blockchain erstellt haben.

So löschen Sie die Ressourcengruppe:

1. Navigieren Sie im Azure-Portal im linken Navigationsbereich zu **Ressourcengruppe**, und wählen Sie die Ressourcengruppe aus, die gelöscht werden soll.
1. Wählen Sie die Option **Ressourcengruppe löschen**. Überprüfen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie zwei Transaktionsknoten hinzugefügt, um den Datenschutz von Verträgen und Transaktionen zu veranschaulichen. Sie haben den Standardknoten zum Bereitstellen eines privaten Smart Contracts verwendet. Außerdem haben Sie den Datenschutz getestet, indem Sie Vertragswerte abgefragt und Transaktionen in der Blockchain durchgeführt haben.

> [!div class="nextstepaction"]
> [Übersicht über die Entwicklung mit dem Azure Blockchain-Dienst](develop.md)
