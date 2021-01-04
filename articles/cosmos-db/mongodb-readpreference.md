---
title: Verwenden von Read Preference mit der Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie MongoDB Read Preference mit der Azure Cosmos DB-API für MongoDB verwenden können.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349043"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Globale Verteilung von Lesevorgängen mit der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieser Artikel zeigt, wie Lesevorgänge mit [MongoDB Read Preference](https://docs.mongodb.com/manual/core/read-preference/)-Einstellungen mit der Azure Cosmos DB-API für MongoDB global verteilt werden können.

## <a name="prerequisites"></a>Voraussetzungen 
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

In diesem [Schnellstart](tutorial-global-distribution-mongodb.md)-Artikel finden Sie Anweisungen zur Verwendung des Azure-Portals zum Einrichten eines Cosmos-Kontos mit globaler Verteilung und zum anschließendem Herstellen einer Verbindung.

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Öffnen Sie ein Git-Terminalfenster, z.B. ein Git Bash, und `cd` in einem Arbeitsverzeichnis.  

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen. Basierend auf der Plattform, die Sie interessiert, verwenden Sie eines der folgenden Beispielrepositorys:

1. [.NET-Beispielanwendung](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS-Beispielanwendung]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose-Beispielanwendung](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-Beispielanwendung](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot-Beispielanwendung](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Installieren Sie je nach verwendeter Plattform die erforderlichen Pakete, und starten Sie die Anwendung. Um Abhängigkeiten zu installieren, befolgen Sie die Anleitungen in der Infodatei, die im Beispielanwendungsrepository enthalten ist. Verwenden Sie beispielsweise in der NodeJS-Beispielanwendung die folgenden Befehle, um die erforderlichen Pakete zu installieren und die Anwendung zu starten.

```bash
cd mean
npm install
node index.js
```
Die Anwendung versucht, eine Verbindung mit einer MongoDB-Quelle herzustellen und schlägt fehl, weil die Verbindungszeichenfolge ungültig ist. Führen Sie die Schritte in der Infodatei aus, um die `url` der Verbindungszeichenfolge zu aktualisieren. Aktualisieren Sie außerdem die Angabe `readFromRegion` in eine Leseregion in Ihrem Cosmos-Konto. Die folgenden Anweisungen stammen aus dem NodeJS-Beispiel:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Nachdem Sie diese Schritte ausgeführt haben, wird die Beispielanwendung ausgeführt und generiert die folgende Ausgabe:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Lesen mit dem Read Preference-Modus

Das MongoDB-Protokoll stellt die folgenden Read Preference-Modi für Clients zur Verfügung:

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

Weitere Informationen zum Verhalten der einzelnen Lesepräferenzeinstellungen finden Sie in der ausführlichen Dokumentation [Verhalten von MongoDB Read Preference](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior). In Cosmos DB wird „primary“ der WRITE-Region und „secondary“ der READ-Region zugeordnet.

Basierend auf allgemeine Szenarien empfiehlt sich die Verwendung der folgenden Einstellungen:

1. Wenn **niedrigere Latenz für Lesevorgänge** erforderlich ist, verwenden Sie den Lesepräferenzmodus **NEAREST**. Mit dieser Einstellung werden die Lesevorgänge an die nächste verfügbare Region weitergeleitet. Beachten Sie Folgendes: Wenn die nächstgelegene Region die WRITE-Region ist, werden diese Vorgänge an diese Region weitergeleitet.
2. Wenn **Hochverfügbarkeit und Geoverteilung von Lesevorgängen** erforderlich sind (Latenz ist keine Einschränkung), verwenden Sie den Lesepräferenzmodus **PRIMARY PREFERRED** oder **SECONDARY PREFERRED**. Mit dieser Einstellung werden die Lesevorgänge jeweils an eine verfügbare WRITE- oder READ-Region weitergeleitet. Wenn die Region nicht verfügbar ist, werden Anforderungen gemäß dem Lesevoreinstellungsverhalten an die nächste verfügbare Region weitergeleitet.

Der folgende Codeausschnitt aus der Beispielanwendung veranschaulicht, wie die Lesepräferenz NEAREST in NodeJS konfiguriert wird:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

In ähnlicher Weise zeigt der folgende Codeausschnitt, wie die Lesepräferenz SECONDARY_PREFERRED in NodeJS konfiguriert wird:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Die Lesepräferenz kann auch durch Übergabe von `readPreference` als Parameter in den URI-Optionen der Verbindungszeichenfolge festgelegt werden:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Verwenden Sie die entsprechenden Beispielanwendungsrepositorys für andere Plattformen, z.B. für [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) und [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Lesevorgänge mithilfe von Tags

Zusätzlich zum Read Preference-Modus ermöglicht das MongoDB-Protokoll die Verwendung von Tags zum Weiterleiten von Lesevorgängen. In der Cosmos DB-API für MongoDB ist das `region`-Tag standardmäßig als Teil der `isMaster`-Antwort enthalten:

```json
"tags": {
         "region": "West US"
      }
```

Daher kann MongoClient das `region`-Tag zusammen mit dem Regionsnamen verwenden, um Lesevorgänge an bestimmte Regionen weiterzuleiten. Für Cosmos-Konten finden Sie die Regionsnamen im Azure-Portal auf der linken Seite unter **Einstellungen > Daten global replizieren**. Diese Einstellung ist nützlich, um **Leseisolierung** zu erreichen: Fälle, in denen die Clientanwendung Lesevorgänge nur an eine bestimmte Region weiterleiten möchte. Diese Einstellung ist ideal für Szenarien vom Typ Nicht-Produktion/Analyse geeignet, die im Hintergrund ausgeführt werden und keine produktionsrelevanten Dienste sind.

Der folgende Codeausschnitt aus der Beispielanwendung veranschaulicht, wie die Lesepräferenz mit Tags in NodeJS konfiguriert wird:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Verwenden Sie die entsprechenden Beispielanwendungsrepositorys für andere Plattformen, z.B. für [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) und [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

In diesem Artikel haben Sie erfahren, wie Lesevorgänge mit MongoDB Read Preference-Einstellungen mit der Azure Cosmos DB-API für MongoDB global verteilt werden können.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese App nicht weiterhin verwenden, löschen Sie im Azure-Portal sämtliche Ressourcen, die mit diesem Artikel erstellt wurden. Führen Sie dazu folgende Schritte aus:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den Namen der erstellten Ressource. 
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

* [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Einrichten einer global verteilten Datenbank mit der Azure Cosmos DB-API für MongoDB](tutorial-global-distribution-mongodb.md)
* [Lokale Entwicklung mit dem Azure Cosmos DB-Emulator](local-emulator.md)