---
title: Erstellen einer Flask-Web-App mit der API für MongoDB von Azure Cosmos DB und dem Python SDK
description: Stellt ein Python Flask-Codebeispiel vor, mit dem Sie eine Verbindung herstellen und Abfragen über die API für MongoDB von Azure Cosmos DB durchführen können.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 07f213de3509ed16af627af5e0583cd985c278f6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954660"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Erstellen einer Python-App mit der API für MongoDB von Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Cosmos DB zugrunde liegen.

In diesem Schnellstart wird anhand des folgenden [Flask-Beispiels](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) gezeigt, wie Sie eine einfache Flask-Aufgaben-App unter Verwendung des [Azure Cosmos DB-Emulators](local-emulator.md) und der API für MongoDB von Azure Cosmos DB erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Laden Sie den [Azure Cosmos DB-Emulator](local-emulator.md) herunter. Der Emulator wird derzeit nur unter Windows unterstützt. Das Beispiel zeigt, wie Sie das Beispiel mit einem Produktionsschlüssel von Azure verwenden, was mit jeder Plattform möglich ist.

- Sollte [Visual Studio Code](https://code.visualstudio.com/Download) bei Ihnen noch nicht installiert sein, können Sie es schnell für Ihre Plattform (Windows, Mac, Linux) installieren.

- Fügen Sie die Python-Sprachunterstützung hinzu, indem Sie eine der beliebten Python-Erweiterungen installieren.
  1. Wählen Sie eine Erweiterung aus.
  2. Geben Sie `ext install` in die Befehlspalette (`Ctrl+Shift+P`) ein, um die Erweiterung zu installieren.

     In den Beispielen dieses Dokuments wird die beliebte und umfassende [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) von Don Jayamanne verwendet.

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie als Nächstes eine Flask-MongoDB-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Führen Sie den folgenden Befehl aus, um die Python-Module zu installieren.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Öffnen Sie den Ordner in Visual Studio Code.

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der Web-App](#run-the-web-app) fortfahren. 

Die folgenden Codeausschnitte stammen alle aus der Datei „app.py“ und verwenden die Verbindungszeichenfolge für den lokalen Azure Cosmos DB-Emulator. Das Kennwort muss wie weiter unten dargestellt aufgeteilt werden, da die Schrägstriche ansonsten nicht analysiert werden können.

* Initialisieren Sie den MongoDB-Client, rufen Sie die Datenbank ab, und führen Sie die Authentifizierung durch.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Rufen Sie die Sammlung ab, oder erstellen Sie sie, falls sie noch nicht vorhanden ist.

    ```python
    todos = db.todo #Select the collection
    ```

* Erstellen der App

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Ausführen der Web-App

1. Vergewissern Sie sich, dass der Azure Cosmos DB-Emulator ausgeführt wird.

2. Öffnen Sie ein Terminalfenster, und wechseln Sie mithilfe von `cd` zu dem Verzeichnis, in dem die App gespeichert ist.

3. Legen Sie die Umgebungsvariable für die Flask-App mit `set FLASK_APP=app.py` bzw. mit `$env:FLASK_APP = app.py` für PowerShell-Editoren oder mit `export FLASK_APP=app.py` fest, wenn Sie einen Mac verwenden. 

4. Führen Sie die App mit `flask run` aus, und navigieren Sie zu [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Fügen Sie Aufgaben hinzu, entfernen Sie Aufgaben, und verfolgen Sie, wie sie der Sammlung hinzugefügt bzw. darin geändert werden.

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wenn Sie den Code mit einem aktiven Cosmos-Konto testen möchten, erstellen Sie über das Azure-Portal ein Konto, und rufen Sie die Informationen zur Verbindungszeichenfolge ab. Kopieren Sie sie anschließend in die App.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Cosmos-Konto im linken Navigationsbereich auf **Verbindungszeichenfolge**, und klicken Sie anschließend auf **Lese-/Schreibschlüssel**. Kopieren Sie im nächsten Schritt mithilfe der Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms den Benutzernamen, das Kennwort sowie den Host in die Datei „dal.cs“.

2. Öffnen Sie die Datei **app.py** im Stammverzeichnis.

3. Kopieren Sie den Wert für **Benutzername** aus dem Portal (mithilfe der Schaltfläche zum Kopieren), und legen Sie ihn in der Datei **app.py** als Wert für **Name** fest.

4. Kopieren Sie dann den Wert Ihrer **Verbindungszeichenfolge** aus dem Portal, und legen Sie ihn in der Datei **app.py** als MongoClient-Wert fest.

5. Kopieren Sie schließlich das **Kennwort** aus dem Portal, und legen Sie es in der Datei **app.py** als **Kennwort** fest.

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Cosmos DB aktualisiert. Sie können sie nun auf die gleiche Weise ausführen wie zuvor.

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

Zum Bereitstellen dieser App können Sie in Azure eine neue Web-App erstellen und Continuous Deployment mit einer Verzweigung dieses GitHub-Repositorys aktivieren. Eine Anleitung zum Einrichten von Continuous Deployment mit GitHub in Azure finden Sie in [diesem Tutorial](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).

Entfernen Sie beim Bereitstellen in Azure Ihre Anwendungsschlüssel, und stellen Sie sicher, dass der folgende Abschnitt auskommentiert ist:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Fügen Sie anschließend „MONGOURL“, „MONGO_PASSWORD“ und „MONGO_USERNAME“ den Anwendungseinstellungen hinzu. Weitere Informationen zu Anwendungseinstellungen in Azure-Web-Apps finden Sie in [diesem Tutorial](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings).

Wenn Sie keine Verzweigung dieses Repositorys erstellen möchten, können Sie weiter unten auch auf die Schaltfläche für die Bereitstellung in Azure klicken. Richten Sie anschließend in Azure die Anwendungseinstellungen mit Ihren Cosmos DB-Kontoinformationen ein.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Wenn Sie Ihren Code in GitHub speichern oder andere Quellcodeverwaltungsoptionen verwenden möchten, entfernen Sie vorher unbedingt Ihre Verbindungszeichenfolgen aus dem Code. Sie können stattdessen mit Anwendungseinstellungen für die Web-App festgelegt werden.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie ein Cosmos-Konto erstellen und eine Flask-App ausführen. Jetzt können Sie zusätzliche Daten in Ihre Cosmos-Datenbank importieren. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](mongodb-migrate.md)
