---
title: 'Tutorial: Erstellen und Bereitstellen von Smart Contracts – Azure Blockchain Service'
description: Tutorial zur Verwendung des Azure Blockchain Development Kit für die Ethereum-Erweiterung in Visual Studio Code zum Erstellen und Bereitstellen von Smart Contracts in Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: f7605a0c118a40e52210582d2411569795fb25ee
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763688"
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

Mit der Funktion **SendRequest** des Contracts **HelloBlockchain** wird die Zustandsvariable **RequestMessage** geändert. Die Änderung des Zustands eines Blockchainnetzwerks wird nicht per Transaktion durchgeführt. Auf der Seite für die Smart Contract-Interaktion des Azure Blockchain Development Kit können Sie die Funktion **SendRequest** über eine Transaktion aufrufen.

1. Klicken Sie für die Interaktion mit Ihrem Smart Contract mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü **Show Smart Contract Interaction Page** (Smart Contract-Interaktionsseite anzeigen) aus.

    ![Auswählen von „Show Smart Contract Interaction Page“ (Smart Contract-Interaktionsseite anzeigen) im Menü](./media/send-transaction/contract-interaction.png)

1. Auf der Interaktionsseite können Sie eine bereitgestellte Vertragsversion auswählen, Funktionen aufrufen sowie den aktuellen Zustand und Metadaten anzeigen.

    ![Beispiel für eine Smart Contract-Interaktionsseite](./media/send-transaction/interaction-page.png)

1. Wählen Sie zum Aufrufen der Smart Contract-Funktion die Vertragsaktion auf, und übergeben Sie Ihre Argumente. Wählen Sie die Vertragsaktion **SendRequest** aus, und geben Sie **Hello, Blockchain!** für den Parameter **requestMessage** ein. Wählen Sie **Ausführen** aus, um die Funktion **SendRequest** per Transaktion aufzurufen.

    ![Ausführen der Aktion „SendRequest“](./media/send-transaction/sendrequest-action.png)

Nach der Verarbeitung der Transaktion sehen Sie die Zustandsänderungen im Interaktionsabschnitt.

![Änderungen des Vertragszustands](./media/send-transaction/contract-state.png)

Mit der Funktion „SendRequest“ werden die Felder **RequestMessage** und **State** festgelegt. Der aktuelle Zustand für **RequestMessage** ist das übergebene Argument **Hello, Blockchain**. Das Feld **State** lautet weiterhin **Request**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn sie nicht mehr benötigt werden, können Sie die Ressourcen löschen. Löschen Sie hierzu die Ressourcengruppe `myResourceGroup`, die Sie im Rahmen der Schnellstartanleitung unter *Erstellen eines Blockchainmitglieds* zur Vorbereitung erstellt haben.

So löschen Sie die Ressourcengruppe:

1. Navigieren Sie im Azure-Portal im linken Navigationsbereich zu **Ressourcengruppe**, und wählen Sie die Ressourcengruppe aus, die gelöscht werden soll.
1. Wählen Sie die Option **Ressourcengruppe löschen**. Überprüfen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit dem Azure Blockchain Development Kit ein Solidity-Beispielprojekt erstellt. Sie haben einen Smart Contract erstellt und bereitgestellt und dann eine Funktion per Transaktion in einem Blockchain-Konsortiumsnetzwerk aufgerufen, das unter dem Azure Blockchain Service gehostet wird.

> [!div class="nextstepaction"]
> [Übersicht über die Entwicklung mit dem Azure Blockchain-Dienst](develop.md)
