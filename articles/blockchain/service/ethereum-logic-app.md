---
title: Verwenden des Ethereum-Blockchain-Connectors mit Azure Logic Apps – Azure Blockchain Service
description: Es wird beschrieben, wie Sie den Ethereum-Blockchain-Connector mit Azure Logic Apps zum Auslösen von Smart Contract-Funktionen und zum Reagieren auf Smart Contract-Ereignisse verwenden.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763790"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Verwenden des Ethereum-Blockchain-Connectors mit Azure Logic Apps

Verwenden Sie den [Ethereum-Blockchain-Connector](/connectors/blockchainethereum/) mit [Azure Logic Apps](../../logic-apps/index.yml) zum Ausführen von Smart Contract-Funktionen sowie zum Reagieren auf Smart Contract-Ereignisse. In diesem Artikel wird erläutert, wie Sie mit dem Ethereum Blockchain-Connector Blockchaininformationen an einen anderen Dienst senden oder eine Blockchainfunktion aufrufen. Angenommen, Sie möchten einen REST-basierten Microservice erstellen, der Informationen von einem Blockchainledger zurückgibt. Mithilfe einer Logik-App können Sie HTTP-Anforderungen akzeptieren, die in einem Blockchainledger gespeicherte Informationen abfragen.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie den optionalen [Schnellstart: Herstellen einer Verbindung mit einem Azure Blockchain Service-Konsortiumsnetzwerk mithilfe von Visual Studio Code](connect-vscode.md) durch. Im Schnellstart werden Schritt für Schritt die Installation des [Azure Blockchain Development Kit für Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) und die Einrichtung Ihrer Blockchain-Entwicklungsumgebung beschrieben.
- Wenn Sie noch nicht mit Azure Logic Apps vertraut sind, sollten Sie die Microsoft Learn-Module [Einführung in Azure Logic Apps](/learn/modules/intro-to-logic-apps/) und [Aufrufen einer API von einem Logic Apps-Workflow mithilfe eines benutzerdefinierten Connectors](/learn/modules/logic-apps-and-custom-connectors/) in Betracht ziehen.

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

Azure Logic Apps hilft Ihnen beim Planen und Automatisieren von Geschäftsprozessen und Workflows, wenn Sie Systeme und Dienste integrieren müssen. Erstellen Sie zunächst eine Logik, die den Ethereum-Blockchain-Connector verwendet.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Integration** > **Logik-App** aus.
1. Geben Sie unter **Logik-App erstellen** an, wo die Logik-App erstellt werden soll. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

    Weitere Informationen zum Erstellen von Logik-Apps finden Sie unter [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nachdem Azure Ihre App bereitgestellt hat, wählen Sie Ihre Logik-App-Ressource aus.
1. Wählen Sie im Designer für Logik-Apps unter **Vorlagen** die Option **Leere Logik-App** aus.

Jede Logik-App muss mit einem Trigger beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt die Logic Apps-Engine eine Logik-App-Instanz, mit der Ihr Workflow gestartet und ausgeführt wird.

Der Ethereum-Blockchain-Connector verfügt über einen Trigger und mehrere Aktionen. Welche Trigger oder Aktionen Sie verwenden, hängt von Ihrem Szenario ab. Befolgen Sie den Abschnitt in diesem Artikel, der am besten zu Ihrem Szenario passt.

Gehen Sie für Ihren Workflow wie folgt vor:

* Wenn Ihr Workflow beim Auftreten eines Ereignisses in der Blockchain ausgelöst wird, [verwenden Sie den Ereignisauslöser](#use-the-event-trigger).
* Wenn Ihr Workflow einen Smart Contract bereitstellt, [verwenden Sie Aktionen](#use-actions).
* Wenn Ihr Workflow einem häufigen Szenario folgt, [erstellen Sie einen Workflow mithilfe des Entwickler-Kits](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Verwenden des Ereignisauslösers

Verwenden Sie Ethereum-Blockchain-Ereignisauslöser, wenn Sie möchten, dass eine Logik-App ausgeführt wird, nachdem ein Smart Contract-Ereignis aufgetreten ist. Beispiel: Nach dem Aufrufen einer Smart Contract-Funktion soll eine E-Mail versendet werden.

1. Wählen Sie im Designer für Logik-Apps den Ethereum-Blockchain-Connector aus.
1. Wählen Sie auf der Registerkarte **Trigger** die Option **When a smart contract event occurs** (Wenn ein Smart Contract-Ereignis auftritt) aus.
1. Ändern oder [erstellen Sie eine API-Verbindung](#create-an-api-connection) in Azure Blockchain Service.
1. Geben Sie die Details zum Smart Contract ein, den Sie auf Ereignisse überprüfen möchten.

    ![Designer für Logik-Apps mit Eigenschaften von Ereignisauslösern](./media/ethereum-logic-app/event-properties.png)

    | Eigenschaft | BESCHREIBUNG |
    |----------|-------------|
    | **Contract ABI** (Vertrags-ABI) | Die binäre Anwendungsschnittstelle (Application Binary Interface, ABI) des Vertrags definiert die Smart Contract-Schnittstellen. Weitere Informationen finden Sie unter [Abrufen der Vertrags-ABI](#get-the-contract-abi). |
    | **Smart contract address** (Smart Contract-Adresse) | Die Vertragsadresse ist die Smart Contract-Zieladresse in der Ethereum-Blockchain. Weitere Informationen finden Sie unter [Abrufen der Vertragsadresse](#get-the-contract-address). |
    | **Event name** (Ereignisname) | Wählen Sie ein Smart Contract-Ereignis für die Überprüfung aus. Das Ereignis löst die Logik-App aus. |
    | **Interval** (Intervall) und **Frequency** (Häufigkeit) | Wählen Sie aus, wie häufig eine Überprüfung auf das Ereignis durchgeführt werden soll. |

1. Wählen Sie **Speichern** aus.

Zum Fertigstellen Ihrer Logik-App können Sie einen neuen Schritt hinzufügen, in dem eine Aktion ausgeführt wird, die auf dem Ethereum-Blockchain-Ereignisauslöser basiert. Beispiel: Senden einer E-Mail.

## <a name="use-actions"></a>Verwenden von Aktionen

Verwenden Sie Ethereum-Blockchain-Aktionen, wenn eine Logik-App eine Aktion im Blockchainledger ausführen soll. Beispiel: Sie möchten einen REST-basierten Microservice erstellen, der eine Smart Contract-Funktion aufruft, wenn eine HTTP-Anforderung an eine Logik-App gesendet wird.

Connector-Aktionen erfordern einen Trigger. Sie können eine Ethereum-Blockchain-Connectoraktion als nächsten Schritt nach einem Trigger (Auslöser) verwenden, z. B. als HTTP-Anforderungstrigger für einen Microservice.

1. Wählen Sie im Designer für Logik-Apps nach einem Trigger die Option **Neuer Schritt** aus.
1. Wählen Sie den Ethereum-Blockchain-Connector aus.
1. Wählen Sie auf der Registerkarte **Aktionen** eine der verfügbaren Aktionen aus.

    ![Designer für Logik-Apps mit Eigenschaften von „Aktionen“](./media/ethereum-logic-app/action-properties.png)

1. Ändern oder [erstellen Sie eine API-Verbindung](#create-an-api-connection) in Azure Blockchain Service.
1. Geben Sie je nach der gewählten Aktion die folgenden Details zu Ihrer Smart Contract-Funktion an:

    | Eigenschaft | BESCHREIBUNG |
    |----------|-------------|
    | **Contract ABI** (Vertrags-ABI) | Mit der Vertrags-ABI werden die Smart Contract-Schnittstellen definiert. Weitere Informationen finden Sie unter [Abrufen der Vertrags-ABI](#get-the-contract-abi). |
    | **Contract bytecode** (Vertragsbytecode) | Der kompilierte Smart Contract-Bytecode. Weitere Informationen finden Sie unter [Abrufen des Vertragsbytecodes](#get-the-contract-bytecode). |
    | **Smart contract address** (Smart Contract-Adresse) | Die Vertragsadresse ist die Smart Contract-Zieladresse in der Ethereum-Blockchain. Weitere Informationen finden Sie unter [Abrufen der Vertragsadresse](#get-the-contract-address). |
    | **Smart contract function name** (Smart Contract-Funktionsname) | Wählen Sie den Smart Contract-Funktionsnamen für die Aktion aus. Die Liste wird mit den Details in der Vertrags-ABI ausgefüllt. |

    Nachdem Sie einen Smart Contract-Funktionsnamen ausgewählt haben, werden unter Umständen die erforderlichen Felder für Funktionsparameter angezeigt. Geben Sie die Werte oder dynamischen Inhalte ein, die für Ihr Szenario erforderlich sind.

Sie können Ihre Logik-App jetzt verwenden. Wenn das Logik-App-Ereignis ausgelöst wird, wird die Ethereum-Blockchain-Aktion ausgeführt. Beispiel: Ein HTTP-Anforderungstrigger führt eine Ethereum-Blockchain-Aktion aus, um einen Smart Contract-Zustandswert abzufragen. Diese Abfrage führt zu einer HTTP-Antwort, mit der der Wert zurückgegeben wird.

## <a name="generate-a-workflow"></a>Generieren eines Workflows

Das Azure Blockchain Development Kit für die Ethereum-Erweiterung in Visual Studio Code kann Logik-App-Workflows für gängige Szenarien generieren. Vier Szenarien sind verfügbar:

* Veröffentlichen von Daten für eine Azure SQL-Datenbank-Instanz
* Veröffentlichen von Ereignissen für eine Instanz von Azure Event Grid oder Azure Service Bus
* Veröffentlichung von Berichten
* REST-basierter Microservice

 Das Azure Blockchain Development Kit verwendet Truffle, um die Blockchainentwicklung zu vereinfachen. Wenn Sie ausgehend von einem Smart Contract eine Logik-App generieren möchten, benötigen Sie eine Truffle-Lösung für den Smart Contract. Außerdem benötigen Sie eine Verbindung mit Ihrem Azure Blockchain Service-Konsortiumsnetzwerk. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem Azure Blockchain Service-Konsortiumsnetzwerk mithilfe von Visual Studio Code](connect-vscode.md).

Die folgenden Schritte generieren beispielsweise eine REST-basierte Microservice-Logik-App basierend auf dem Schnellstart-Smart Contract **HelloBlockchain**:

1. Erweitern Sie auf der Seitenleiste des Visual Studio Code-Explorers den Ordner **contracts** in Ihrer Lösung.
1. Klicken Sie mit der rechten Maustaste auf **HelloBlockchain.sol**, und wählen Sie im Menü die Option **Generate Microservices for Smart Contracts** (Microservices für Smart Contracts generieren) aus.

    ![Visual Studio Code-Bereich mit Auswahl von „Generate Microservices for Smart Contracts“ (Microservices für Smart Contracts generieren)](./media/ethereum-logic-app/generate-logic-app.png)

1. Wählen Sie in der Befehlspalette **Logik-App** aus.
1. Geben Sie die **Vertragsadresse** ein. Weitere Informationen finden Sie unter [Abrufen der Vertragsadresse](#get-the-contract-address).
1. Wählen Sie das Azure-Abonnement und die Ressourcengruppe für die Logik-App aus.

    Die Logik-App-Konfiguration und die Codedateien werden im Verzeichnis **generatedLogicApp** generiert.

1. Zeigen Sie das Verzeichnis **generatedLogicApp/HelloBlockchain** an. Für jede Smart Contract-Funktion, jedes Ereignis und jede Eigenschaft gibt es eine Logik-App-JSON-Datei.
1. Öffnen Sie die Datei **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json**, und kopieren Sie ihren Inhalt.

    ![JSON-Datei mit zu kopierendem Code](./media/ethereum-logic-app/requestmessage.png)

1. Wählen Sie in Ihrer Logik-App die Option **Logik-App-Codeansicht** aus. Ersetzen Sie den vorhandenen JSON-Code durch den generierten JSON-Code der Logik-App.

    ![Logik-App-Codeansicht mit ersetztem App-Code](./media/ethereum-logic-app/code-view.png)

1. Wählen Sie **Designer** aus, um zur Designeransicht zu wechseln.
1. Die Logik-App enthält die grundlegenden Schritte für das Szenario. Allerdings müssen Sie die Konfigurationsdetails für den Ethereum-Blockchain-Connector aktualisieren.
1. Wählen Sie den Schritt **Verbindungen** aus, und ändern oder [erstellen Sie eine API-Verbindung](#create-an-api-connection) mit Azure Blockchain Service.

    ![Designeransicht mit Auswahl von „Verbindungen“](./media/ethereum-logic-app/microservice-logic-app.png)

1. Sie können Ihre Logik-App jetzt verwenden. Um den REST-basierten Microservice zu testen, geben Sie eine HTTP-POST-Anforderung an die Logik-App-Anforderungs-URL aus. Kopieren Sie den Inhalt der **HTTP POST-URL** aus dem Schritt **Beim Empfang einer HTTP-Anforderung**.

    ![Bereich des Designers für Logik-Apps mit der HTTP POST-URL](./media/ethereum-logic-app/post-url.png)

1. Verwenden Sie cURL zum Erstellen einer HTTP-POST-Anforderung. Ersetzen Sie den Platzhaltertext *\<HTTP POST URL\>* durch die URL aus dem vorherigen Schritt.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Der cURL-Befehl gibt eine Antwort von der Logik-App zurück. In diesem Fall ist die Antwort die Ausgabe der Smart Contract-Funktion **RequestMessage**.

    ![Codeausgabe der Smart Contract-Funktion „RequestMessage“](./media/ethereum-logic-app/curl.png)

Weitere Informationen zur Verwendung des Development Kit finden Sie im [Wiki für das Azure Blockchain Development Kit für Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Erstellen einer API-Verbindung

Für den Ethereum-Blockchain-Connector ist eine API-Verbindung mit einer Blockchain erforderlich. Sie können den API-Connector für mehrere Logik-Apps verwenden. Einige Eigenschaften sind erforderlich, während andere von Ihrem Szenario abhängig sind.

> [!IMPORTANT]
> Zum Erstellen von Transaktionen in einer Blockchain ist ein privater Schlüssel oder eine Kontoadresse mit einem Kennwort erforderlich. Es ist nur eine Art der Authentifizierung erforderlich. Sie müssen nicht sowohl den privaten Schlüssel als auch die Kontodetails angeben. Zum Abfragen von Verträgen ist keine Transaktion erforderlich. Wenn Sie Aktionen verwenden, die den Vertragsstatus abfragen, sind der private Schlüssel oder die Kontoadresse und das Kennwort nicht erforderlich.

Als Hilfe beim Einrichten einer Verbindung mit einem Azure Blockchain Service-Mitglied enthält die Liste unten mögliche Eigenschaften, die je nach Szenario ggf. erforderlich sind.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
|**Verbindungsname** | Der Name der API-Verbindung. Erforderlich. |
|**Ethereum-RPC-Endpunkt** | HTTP-Adresse des Azure Blockchain Service-Transaktionsknoten. Erforderlich. Weitere Informationen finden Sie unter [Abrufen des RPC-Endpunkts](#get-the-rpc-endpoint). |
|**Privater Schlüssel** | Privater Schlüssel des Ethereum-Kontos. Der private Schlüssel oder die Kontoadresse und das Kennwort sind für Transaktionen erforderlich. Weitere Informationen finden Sie unter [Abrufen des privaten Schlüssels](#get-the-private-key). |
|**Kontoadresse** | Die Kontoadresse des Azure Blockchain-Mitglieds. Der private Schlüssel oder die Kontoadresse und das Kennwort sind für Transaktionen erforderlich. Weitere Informationen finden Sie unter [Abrufen der Kontoadresse](#get-the-account-address). |
|**Kontokennwort** | Das Kontokennwort wird festgelegt, wenn Sie das Mitglied erstellen. Weitere Informationen zum Zurücksetzen des Kennworts finden Sie unter [Ethereum-Konto](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Abrufen des RPC-Endpunkts

Die Azure Blockchain Service-RPC-Endpunktadresse ist erforderlich, um eine Verbindung mit dem Blockchainnetzwerk herzustellen. Sie können die Endpunktadresse mit dem Azure Blockchain Development Kit für Ethereum oder dem Azure-Portal abrufen.

**Gehen Sie wie folgt vor, um das Development Kit zu verwenden:**

1. Klicken Sie in Visual Studio Code unter **Azure Blockchain Service** mit der rechten Maustaste auf das Konsortium.
1. Wählen Sie **Copy RPC Endpoint Address** (Adresse des RPC-Endpunkts kopieren) aus.

    ![Visual Studio Code-Bereich: Konsortium mit Auswahl von „Copy RPC Endpoint Address“ (Adresse des RPC-Endpunkts kopieren)](./media/ethereum-logic-app/devkit-rpc.png)

    Der RPC-Endpunkt wird in die Zwischenablage kopiert.

**Gehen Sie wie folgt vor, um das Azure-Portal zu verwenden:**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem Azure Blockchain Service-Mitglied. Klicken Sie auf **Transaktionsknoten** und dann auf den Link „default transaction node“ (Standardtransaktionsknoten).

    ![Seite „Transaktionsknoten“ mit Auswahl des Standardknotens](./media/ethereum-logic-app/transaction-nodes.png)

1. Wählen Sie **Verbindungszeichenfolgen** > **Zugriffsschlüssel** aus.
1. Kopieren Sie die Endpunktadresse von **HTTPS (Zugriffsschlüssel 1)** oder **HTTPS (Zugriffsschlüssel 2)** .

    ![Azure-Portal mit Zugriffsschlüsseln für Verbindungszeichenfolgen](./media/ethereum-logic-app/connection-string.png)

    Der RPC-Endpunkt ist die HTTPS-URL, die die Adresse und den Zugriffsschlüssel für den Transaktionsknoten Ihres Azure Blockchain Service-Mitglieds enthält.

## <a name="get-the-private-key"></a>Abrufen des privaten Schlüssels

Sie können den privaten Schlüssel des Ethereum-Kontos zum Authentifizieren verwenden, wenn eine Transaktion an die Blockchain gesendet wird. Der öffentliche und der private Schlüssel Ihres Ethereum-Kontos werden aus einem mnemonischen Zeichen generiert, das aus zwölf Wörtern besteht. Das Azure Blockchain Development Kit für Ethereum generiert ein mnemonisches Zeichen, wenn Sie eine Verbindung mit einem Azure Blockchain Service-Konsortiumsmitglied herstellen. Sie können die Endpunktadresse mit der Development Kit-Erweiterung abrufen.

1. Öffnen Sie in Visual Studio Code die Befehlspalette (F1).
1. Wählen Sie **Blockchain: Retrieve private key** (Azure Blockchain: Privaten Schlüssel abrufen) aus.
1. Wählen Sie das mnemonische Zeichen aus, das Sie beim Herstellen einer Verbindung mit dem Konsortiumsmitglied gespeichert haben.

    ![Befehlspalette mit einer Option zum Auswählen des mnemonischen Zeichens](./media/ethereum-logic-app/private-key.png)

    Der private Schlüssel wird in die Zwischenablage kopiert.

## <a name="get-the-account-address"></a>Abrufen der Kontoadresse

Sie können das Mitgliedskonto und das Kennwort zum Authentifizieren verwenden, wenn Sie eine Transaktion an die Blockchain senden. Das Kennwort wird festgelegt, wenn Sie das Mitglied erstellen.

1. Navigieren Sie im Azure-Portal zur Azure Blockchain Service-Übersichtsseite.
1. Kopieren Sie die Adresse des **Mitgliedskontos**.

    ![Übersichtsseite mit der Adresse des Mitgliedskontos](./media/ethereum-logic-app/member-account.png)

Weitere Informationen zur Kontoadresse und zum Kennwort finden Sie unter [Ethereum-Konto](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Abrufen der Vertrags-ABI

Mit der Vertrags-ABI werden die Smart Contract-Schnittstellen definiert. Sie beschreibt, wie mit dem Smart Contract interagiert wird. Sie können die Vertrags-ABI abrufen, indem Sie das Azure Blockchain Development Kit für Ethereum verwenden. Darüber hinaus ist sie in der Datei mit den Vertragsmetadaten enthalten, die vom Solidity-Compiler erstellt wurde.

**Gehen Sie wie folgt vor, um das Development Kit zu verwenden:**

Wenn Sie Ihren Smart Contract mit dem Development Kit oder mit Truffle erstellt haben, können Sie die Erweiterung verwenden, um die Vertrags-ABI in die Zwischenablage zu kopieren.

1. Erweitern Sie im Explorer-Bereich von Visual Studio Code den Ordner **build/contracts** Ihres Solidity-Projekts.
1. Klicken Sie mit der rechten Maustaste auf die Vertragsmetadaten-JSON-Datei. Der Dateiname ist der Smart Contract-Name gefolgt von der Erweiterung **.json**.
1. Wählen Sie **Copy Contract ABI** (Vertrags-ABI kopieren) aus.

    ![Visual Studio Code-Bereich mit Auswahl von „Copy Contract ABI“ (Vertrags-ABI kopieren)](./media/ethereum-logic-app/abi-devkit.png)

    Die Vertrags-ABI wird in die Zwischenablage kopiert.

**Gehen Sie wie folgt vor, um die Datei mit den Vertragsmetadaten zu verwenden:**

1. Öffnen Sie die Vertragsmetadatendatei, die im Ordner **build/contracts** Ihres Solidity-Projekts enthalten ist. Der Dateiname ist der Smart Contract-Name gefolgt von der Erweiterung **.json**.
1. Suchen Sie den Abschnitt **abi** in der JSON-Datei.
1. Kopieren Sie das JSON-Array **abi**.

    ![ABI-Code in der Vertragsmetadatendatei](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Abrufen des Vertragsbytecodes

Der Vertragsbytecode ist der kompilierte Smart Contract, der vom virtuellen Ethereum-Computer ausgeführt wird. Sie können Vertragsbytecode abrufen, indem Sie das Azure Blockchain Development Kit für Ethereum verwenden. Darüber hinaus können Sie ihn auch über den Solidity-Compiler erhalten.

**Gehen Sie wie folgt vor, um das Development Kit zu verwenden:**

Wenn Sie Ihren Smart Contract mit dem Development Kit oder mit Truffle erstellt haben, können Sie die Erweiterung verwenden, um den Vertragsbytecode in die Zwischenablage zu kopieren.

1. Erweitern Sie im Explorer-Bereich von Visual Studio Code den Ordner **build/contracts** Ihres Solidity-Projekts.
1. Klicken Sie mit der rechten Maustaste auf die Vertragsmetadaten-JSON-Datei. Der Dateiname ist der Smart Contract-Name gefolgt von der Erweiterung **.json**.
1. Wählen Sie **Copy contract bytecode** (Vertragsbytecode kopieren) aus.

    ![Visual Studio Code-Bereich mit Auswahl von „Copy Contract bytecode“ (Vertragsbytecode kopieren)](./media/ethereum-logic-app/bytecode-devkit.png)

    Der Vertragsbytecode wird in die Zwischenablage kopiert.

**Gehen Sie wie folgt vor, um die Datei mit den Vertragsmetadaten zu verwenden:**

1. Öffnen Sie die Vertragsmetadatendatei, die im Ordner **build/contracts** Ihres Solidity-Projekts enthalten ist. Der Dateiname ist der Smart Contract-Name gefolgt von der Erweiterung **.json**.
1. Suchen Sie das Element **bytecode** in der JSON-Datei.
1. Kopieren Sie den Wert **bytecode**.

    ![Visual Studio Code-Bereich mit Bytecode in den Metadaten](./media/ethereum-logic-app/bytecode-metadata.png)

**Gehen Sie wie folgt vor, um den Solidity-Compiler zu verwenden:**

Verwenden Sie den Befehl `solc --bin <smart contract>.sol`, um den Vertragsbytecode zu generieren.

## <a name="get-the-contract-address"></a>Abrufen der Vertragsadresse

Die Vertragsadresse ist die Smart Contract-Zieladresse in der Ethereum-Blockchain. Diese Adresse wird zum Senden einer Transaktion oder eines Smart Contract-Abfragezustands verwendet. Sie können die Vertragsadresse aus der Truffle-Migrationsausgabe oder der Vertragsmetadatendatei abrufen.

**Gehen Sie wie folgt vor, um die Ausgabe der Truffle-Migration zu verwenden:**

Truffle zeigt die Vertragsadresse an, nachdem der Smart Contract bereitgestellt wurde. Kopieren Sie die **Vertragsadresse** aus der Ausgabe.

![Ausgabe der Truffle-Migration mit der Vertragsadresse in Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Gehen Sie wie folgt vor, um die Datei mit den Vertragsmetadaten zu verwenden:**

1. Öffnen Sie die Vertragsmetadatendatei, die im Ordner **build/contracts** Ihres Solidity-Projekts enthalten ist. Der Dateiname ist der Smart Contract-Name gefolgt von der Erweiterung **.json**.
1. Suchen Sie den Abschnitt **networks** in der JSON-Datei.
1. Private Netzwerke werden durch eine ganzzahlige Netzwerk-ID identifiziert. Suchen Sie den Adresswert im Netzwerkabschnitt.
1. Kopieren Sie den Wert **address**.

![Metadaten mit dem Adresswert in Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die häufig vorkommenden Szenarien im Video zum Thema [Mehr Möglichkeiten mit Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true) an.
