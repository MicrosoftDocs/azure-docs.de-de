---
title: 'Schnellstart: Verbinden einer Node.js-MongoDB-App mit Azure Cosmos DB'
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie eine Verbindung zwischen einer vorhandenen, in Node.js geschriebenen MongoDB-App und Azure Cosmos DB herstellen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: cfeb19fa36bf65021fadc7ac3bc60a9f3a620602
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349077"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Schnellstart: Migrieren einer vorhandenen MongoDB-Node.js-Web-App zu Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

In dieser Schnellstartanleitung erstellen und verwalten Sie mithilfe von Azure Cloud Shell ein Azure Cosmos DB-Konto für die Mongo DB-API mit einer über GitHub geklonten MEAN-App (MongoDB, Express, Angular und Node.js). Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit der Verbindungszeichenfolge `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` verwenden.

- [Node.js](https://nodejs.org/) und ausreichende Kenntnisse zu Node.js

- [Git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.


## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen. Dieses Beispielrepository enthält eine [MEAN.js](https://meanjs.org/)-Standardanwendung.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Diese in Node.js geschriebene MongoDB-App stellt eine Verbindung mit Ihrer Azure Cosmos DB-Datenbank her, die den MongoDB-Client unterstützt. Anders ausgedrückt: Für die Anwendung ist es ersichtlich, dass die Daten in einer Azure Cosmos DB-Datenbank gespeichert sind.

Installieren Sie die erforderlichen Pakete, und starten Sie die Anwendung.

```bash
cd mean
npm install
npm start
```
Die Anwendung versucht, eine Verbindung mit einer MongoDB-Quelle herzustellen. Dabei tritt ein Fehler auf. Beenden Sie die Anwendung, wenn die Ausgabe „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]“ zurückgibt.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Wenn Sie eine installierte Azure CLI verwenden, melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Hinzufügen des Azure Cosmos DB-Moduls

Wenn Sie eine installierte Azure CLI verwenden, überprüfen Sie, ob die `cosmosdb`-Komponente bereits installiert ist, indem Sie den `az`-Befehl ausführen. Wenn `cosmosdb` in der Liste der Basisbefehle enthalten ist, fahren Sie mit dem nächsten Befehl fort. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

Wenn `cosmosdb` nicht in der Liste der Basisbefehle enthalten ist, installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) erneut.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine [Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie Web-Apps, Datenbanken und Speicherkonten bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird eine Ressourcengruppe in der Region „Europa, Westen“ erstellt. Wählen Sie einen eindeutigen Namen für die Ressourcengruppe aus.

Wenn Sie Azure Cloud Shell verwenden, wählen Sie **Ausprobieren** aus, befolgen Sie die Anweisungen auf dem Bildschirm für die Anmeldung, und kopieren Sie dann den Befehl in die Eingabeaufforderung.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie mit dem Befehl [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) ein Cosmos-Konto.

Ersetzen Sie im folgenden Befehl den Platzhalter `<cosmosdb-name>` durch Ihren eigenen eindeutigen Cosmos-Kontonamen. Da dieser eindeutige Name als Teil des Cosmos DB-Endpunkts (`https://<cosmosdb-name>.documents.azure.com/`) verwendet wird, muss er für alle Cosmos-Konten in Azure eindeutig sein. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Der `--kind MongoDB`-Parameter ermöglicht MongoDB-Clientverbindungen.

Nach dem Erstellen des Azure Cosmos DB-Kontos zeigt die Azure-CLI ähnliche Informationen wie im folgenden Beispiel an. 

> [!NOTE]
> In diesem Beispiel wird JSON als Azure CLI-Ausgabeformat (Standardeinstellung) verwendet. Informationen zur Verwendung anderer Ausgabeformate finden Sie unter [Ausgabeformate für Azure CLI-Befehle](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Verbinden der Node.js-Anwendung mit der Datenbank

In diesem Schritt verbinden Sie die MEAN.js-Beispielanwendung mit dem soeben erstellten Azure Cosmos DB-Datenbankkonto. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurieren der Verbindungszeichenfolge in der Node.js-Anwendung

Öffnen Sie in Ihrem MEAN.js.Repository `config/env/local-development.js`.

Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code. Achten Sie auch darauf, dass Sie die beiden `<cosmosdb-name>`-Platzhalter durch den Namen Ihres Cosmos-Kontos ersetzen.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Abrufen des Schlüssels

Um eine Verbindung mit der Cosmos-Datenbank herstellen zu können, benötigen Sie den Datenbankschlüssel. Rufen Sie den Primärschlüssel mit dem Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) ab.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Die Azure-CLI gibt Informationen ähnlich wie im folgenden Beispiel aus. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopieren Sie den Wert von `primaryMasterKey`. Fügen Sie ihn anstelle von `<primary_master_key>` in `local-development.js` ein.

Speichern Sie die Änderungen.

### <a name="run-the-application-again"></a>Erneutes Ausführen der Anwendung

Führen Sie `npm start` erneut aus. 

```bash
npm start
```

Eine Konsolenmeldung sollte Sie darüber informieren, dass die Entwicklungsumgebung ausgeführt wird. 

Navigieren Sie in einem Browser zu `http://localhost:3000`. Wählen Sie oben im Menü **Sign Up** (Registrieren) aus, und versuchen Sie, zwei Dummy-Benutzer zu erstellen. 

Die MEAN.js-Beispielanwendung speichert Benutzerdaten in der Datenbank. Wenn der Vorgang erfolgreich ist und Sie von MEAN.js automatisch mit dem erstellten Benutzer angemeldet werden, funktioniert Ihre Azure Cosmos DB-Verbindung. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="Erfolgreiche Verbindung zwischen MEAN.js und MongoDB":::

## <a name="view-data-in-data-explorer"></a>Anzeigen von Daten im Daten-Explorer

In einer Cosmos-Datenbank gespeicherte Daten können im Azure-Portal angezeigt und abgefragt werden.

Melden Sie sich im [Azure-Portal](https://portal.azure.com) in Ihrem Webbrowser an, um die im vorherigen Schritt erstellten Benutzerdaten anzuzeigen, abzufragen und mit ihnen zu arbeiten.

Geben Sie im Suchfeld oben **Azure Cosmos DB** ein. Wenn sich das Blatt Ihres Cosmos-Kontos öffnet, wählen Sie Ihr Cosmos-Konto aus. Wählen Sie im linken Navigationsbereich **Daten-Explorer** aus. Erweitern Sie Ihre Sammlung im Bereich „Sammlungen“. Anschließend können Sie die Dokumente in der Sammlung anzeigen, die Daten abfragen und sogar gespeicherte Prozeduren, Trigger und UDFs erstellen und ausführen. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Daten-Explorer im Azure-Portal":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Bereitstellen der Node.js-Anwendung in Azure

In diesem Schritt stellen Sie die Node.js-Anwendung in Cosmos DB bereit.

Sie haben vielleicht festgestellt, dass die Konfigurationsdatei, die Sie zuvor geändert haben, für die Entwicklungsumgebung ist (`/config/env/local-development.js`). Wenn Sie die Anwendung in App Service bereitstellen, wird sie standardmäßig in der Produktionsumgebung ausgeführt. Nun müssen Sie die gleiche Änderung an der jeweiligen Konfigurationsdatei vornehmen.

Öffnen Sie in Ihrem MEAN.js.Repository `config/env/production.js`.

Ersetzen Sie im `db`-Objekt den Wert von `uri` wie im folgenden Beispiel gezeigt. Achten Sie darauf, die Platzhalter wie zuvor zu ersetzen.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Die Option `ssl=true` ist wichtig, um die Voraussetzungen für Cosmos DB zu erfüllen. Weitere Informationen finden Sie unter [Anforderungen an die Verbindungszeichenfolge](connect-mongodb-account.md#connection-string-requirements).
>
>

Committen Sie im Terminal alle Änderungen in Git. Sie können beide Befehle kopieren, um sie gemeinsam auszuführen.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit Azure Cloud Shell ein Azure Cosmos DB-Konto für die MongoDB-API erstellen sowie eine MEAN.js-App erstellen und ausführen, um dem Konto Benutzer hinzuzufügen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren.

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
