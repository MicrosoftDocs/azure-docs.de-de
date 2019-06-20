---
title: Integrieren einer vorhandenen MongoDB-Anwendung mit Azure Cosmos DB-API für MongoDB und Open Service Broker for Azure (OSBA)
description: In diesem Artikel wird beschrieben, wie Sie eine vorhandene Java und MongoDB-Anwendung mit der Azure Cosmos DB-API für MongoDB integrieren, indem Sie Open Service Broker for Azure (OSBA) verwenden.
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61028409"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrieren einer vorhandenen MongoDB-Anwendung mit Azure Cosmos DB-API für MongoDB und Open Service Broker for Azure (OSBA)

Azure Cosmos DB ist ein global verteilter Datenbankdienst mit Unterstützung mehrerer Modelle. Darüber hinaus wird Wire Protocol-Kompatibilität mit mehreren NoSQL-APIs, z.B. MongoDB, bereitgestellt. Mit der Cosmos DB-API für MongoDB können Sie Cosmos DB mit Ihrer vorhandenen MongoDB-Anwendung verwenden, ohne dass Sie die Datenbanktreiber oder die Implementierung Ihrer Anwendung ändern müssen. Sie können auch einen Cosmos DB-Dienst bereitstellen, indem Sie Open Service Broker für Azure verwenden.

In diesem Artikel nutzen Sie eine vorhandene Java-Anwendung, für die eine MongoDB-Datenbank verwendet wird, und aktualisieren sie mit Open Service Broker for Azure für die Nutzung einer Cosmos DB-Datenbank.

## <a name="prerequisites"></a>Voraussetzungen

Für die weiteren Schritte wird Folgendes vorausgesetzt:
    
* Erstellung eines [Azure Kubernetes Service-Clusters](kubernetes-walkthrough.md)
* [Installation und Konfiguration des Open Service Broker for Azure in Ihrem AKS-Cluster](integrate-azure.md) 
* Installation und Konfiguration der [Dienstkatalog-CLI](https://svc-cat.io/docs/install/) zum Ausführen von `svcat`-Befehlen
* Vorhandensein einer [MongoDB](https://www.mongodb.com/)-Datenbank. MongoDB kann beispielsweise auf Ihrem [Entwicklungscomputer](https://docs.mongodb.com/manual/administration/install-community/) oder auf einer [Azure-VM](../virtual-machines/linux/install-mongodb.md) ausgeführt werden.
* Möglichkeit zum Verbinden und Abfragen der MongoDB-Datenbank, z.B. per [Mongo-Shell](https://docs.mongodb.com/manual/mongo/)

## <a name="get-application-code"></a>Abrufen von Anwendungscode
    
In diesem Artikel verwenden Sie die [Spring-Musik-Beispielanwendung von Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) zur Veranschaulichung einer Anwendung mit einer MongoDB-Datenbank.
    
Klonen Sie die Anwendung von GitHub, und navigieren Sie zum Verzeichnis der Anwendung:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Vorbereiten der Anwendung auf die Nutzung Ihrer MongoDB-Datenbank

Die Spring-Musik-Beispielanwendung enthält viele Optionen für Datenquellen. In diesem Artikel konfigurieren Sie sie so, dass eine vorhandene MongoDB-Datenbank verwendet wird. 

Fügen Sie den folgenden YAML-Code am Ende der Datei *src/main/resources/application.yml* hinzu. Mit dieser Hinzufügung wird ein Profil mit dem Namen *mongodb* erstellt, und es werden ein URI und ein Datenbankname konfiguriert. Ersetzen Sie den URI durch die Verbindungsinformationen für Ihre vorhandene MongoDB-Datenbank. Das direkte Hinzufügen des URI, der einen Benutzernamen und ein Kennwort umfasst, zu dieser Datei ist **nur für Entwicklungszwecke bestimmt**, und **der URI sollte niemals der Versionskontrolle hinzugefügt werden**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Wenn Sie Ihre Anwendung starten und vorgeben, dass das *mongodb*-Profil verwendet werden soll, wird eine Verbindung mit Ihrer MongoDB-Datenbank hergestellt und zum Speichern der Anwendungsdaten verwendet.

Erstellen Sie Ihre Anwendung wie folgt:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Starten Sie Ihre Anwendung, und weisen Sie sie an, das *mongodb*-Profil zu verwenden:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigieren Sie in Ihrem Browser zu `http://localhost:8080`.

![Spring-Musik-App mit Standarddaten](media/music-app.png)

Sie sehen, dass die Anwendung bereits einige [Standarddaten](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json) enthält. Führen Sie Interaktionen durch, indem Sie einige vorhandene Alben löschen und einige neue erstellen.

Sie können überprüfen, ob Ihre Anwendung die MongoDB-Datenbank nutzt, indem Sie eine Verbindung damit herstellen und die Datenbank *musicdb* abfragen:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Im vorherigen Beispiel wird die [Mongo-Shell](https://docs.mongodb.com/manual/mongo/) verwendet, um eine Verbindung mit der MongoDB-Datenbank herzustellen und eine Abfrage durchzuführen. Sie können auch überprüfen, ob Ihre Änderungen gespeichert werden, indem Sie die Anwendung anhalten, neu starten und anschließend im Browser wieder zur Anwendung navigieren. Sie sehen dann, ob die vorgenommenen Änderungen noch vorhanden sind.


## <a name="create-a-cosmos-db-database"></a>Erstellen einer Cosmos DB-Datenbank

Verwenden Sie den Befehl `svcat provision`, um mit Open Service Broker eine Cosmos DB-Datenbank in Azure zu erstellen:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Mit dem obigen Befehl wird eine Cosmos DB-Datenbank in Azure in der Ressourcengruppe *MyResourceGroup* in der Region *eastus* bereitgestellt. Weitere Informationen zu *resourceGroup*, *location* und anderen Azure-spezifischen JSON-Parametern finden Sie in der [Referenzdokumentation für das Cosmos DB-Modul](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Vergewissern Sie sich mithilfe des Befehls `svcat get instance`, dass die Bereitstellung Ihrer Datenbank abgeschlossen ist:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Ihre Datenbank ist bereit, wenn unter *STATUS* der Status *Bereit* angezeigt wird.

Nachdem die Bereitstellung Ihrer Datenbank abgeschlossen wurde, müssen Sie die zugehörigen Metadaten an ein [Kubernetes-Geheimnis](https://kubernetes.io/docs/concepts/configuration/secret/) binden. Andere Anwendungen können dann auf diese Daten zugreifen, nachdem sie an ein Geheimnis gebunden wurden. Verwenden Sie den Befehl `svcat bind`, um die Metadaten Ihrer Datenbank an ein Geheimnis zu binden:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Verwenden der Cosmos DB-Datenbank mit Ihrer Anwendung

Für die Verwendung der Cosmos DB-Datenbank mit Ihrer Anwendung müssen Sie den URI für die Verbindungsherstellung kennen. Verwenden Sie den Befehl `kubectl get secret`, um diese Informationen abzurufen:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Mit dem vorherigen Befehl wird das *musicdb*-Geheimnis abgerufen und nur der URI angezeigt. Geheimnisse werden im Base64-Format gespeichert, sodass diese mit dem obigen Befehl auch decodiert werden.

Verwenden Sie den URI der Cosmos DB-Datenbank, um die Datei *src/main/resources/application.yml* zu aktualisieren und die Nutzung zu ermöglichen:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Das direkte Aktualisieren des URI, der einen Benutzernamen und ein Kennwort umfasst, in dieser Datei ist **nur für Entwicklungszwecke bestimmt**, und **der URI sollte niemals der Versionskontrolle hinzugefügt werden**.

Erstellen Sie Ihre Anwendung neu, und starten Sie sie, um mit der Nutzung der Cosmos DB-Datenbank zu beginnen:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Beachten Sie, dass Ihre Anwendung weiterhin das *mongodb*-Profil und einen URI nutzt, der mit *mongodb://* beginnt, um eine Verbindung mit der Cosmos DB-Datenbank herzustellen. Diese Kompatibilität wird über die [Azure Cosmos DB-API für MongoDB](../cosmos-db/mongodb-introduction.md) bereitgestellt. Auf diese Weise kann Ihre Anwendung weiterhin so betrieben werden, als ob eine MongoDB-Datenbank verwendet wird, während eigentlich Cosmos DB genutzt wird.

Navigieren Sie in Ihrem Browser zu `http://localhost:8080`. Beachten Sie, dass die Standarddaten wiederhergestellt wurden. Führen Sie Interaktionen durch, indem Sie einige vorhandene Alben löschen und einige neue erstellen. Sie können überprüfen, ob Ihre Änderungen gespeichert werden, indem Sie die Anwendung anhalten und neu starten und anschließend im Browser wieder zur Anwendung navigieren. Sie sehen dann, ob die vorgenommenen Änderungen noch vorhanden sind. Die Änderungen werden in der Cosmos DB-Instanz beibehalten, die Sie mit Open Service Broker for Azure erstellt haben.


## <a name="run-your-application-on-your-aks-cluster"></a>Ausführen der Anwendung in Ihrem AKS-Cluster

Sie können [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) nutzen, um die Anwendung in Ihrem AKS-Cluster bereitzustellen. Mit Azure Dev Spaces können Sie Artefakte generieren, z. B. Dockerfiles und Helm-Diagramme, und eine Anwendung in AKS bereitstellen und ausführen.

Aktivieren Sie Azure Dev Spaces wie folgt in Ihrem AKS-Cluster:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Verwenden Sie die Azure Dev Spaces-Tools, um Ihre Anwendung auf die Ausführung in AKS vorzubereiten:

```cmd
azds prep --public
```

Mit diesem Befehl werden mehrere Artefakte generiert, z.B. der Ordner *charts/* (Helm-Diagramm) im Stammverzeichnis des Projekts. Da mit diesem Befehl für dieses spezifische Projekt keine *Dockerfile* generiert werden kann, müssen Sie sie erstellen.

Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei mit dem Namen *Dockerfile* und dem folgenden Inhalt:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Außerdem müssen Sie die *configurations.develop.build*-Eigenschaft in *azds.yaml* auf *false* festlegen:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Darüber hinaus müssen Sie das Attribut *containerPort* in *charts/spring-music/templates/deployment.yaml* auf *8080* festlegen:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Stellen Sie Ihre Anwendung wie folgt in AKS bereit:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navigieren Sie zu der URL, die in den Protokollen angezeigt wird. Im vorherigen Beispiel verwenden Sie hierfür *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Vergewissern Sie sich, dass die Anwendung mit Ihren Änderungen angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie eine vorhandene Anwendung von der MongoDB-Nutzung auf die Cosmos DB-API für MongoDB umstellen. Darüber hinaus wurde beschrieben, wie Sie mit Open Service Broker for Azure einen Cosmos DB-Dienst und diese Anwendung dann mit Azure Dev Spaces unter AKS bereitstellen.

Weitere Informationen zu Cosmos DB, Open Service Broker for Azure und Azure Dev Spaces finden Sie unter:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Entwickeln mit Dev Spaces](../dev-spaces/azure-dev-spaces.md)
