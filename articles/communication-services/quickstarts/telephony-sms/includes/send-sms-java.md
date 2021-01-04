---
title: include file
description: include file
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: cb8e6934125630590a337ed7bf7f4c81b2b73bb3
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915318"
---
Steigen Sie in Azure Communication Services ein, indem Sie die Java-Clientbibliothek für SMS von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster `mvn -v` aus, um sich zu vergewissern, dass Maven installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, und navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt auf der Grundlage der Vorlage „maven-archetype-quickstart“ zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Durch das Ziel „generate“ wird ein Verzeichnis erstellt, das den gleichen Namen besitzt wie die Artefakt-ID (artifactId). In diesem Verzeichnis enthält das Verzeichnis **src/main/java** den Quellcode des Projekts. Das Verzeichnis **src/test/java** enthält die Testquelle, und die Datei **pom.xml** ist das Projektobjektmodell (POM) des Projekts.

### <a name="install-the-package"></a>Installieren des Pakets

Öffnen Sie die Datei **pom.xml** in Ihrem Text-Editor. Fügen Sie der Gruppe „dependencies“ das folgende Abhängigkeitselement hinzu.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Fügen Sie der Datei **pom.xml** die Abhängigkeit `azure-core-http-netty` hinzu.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Öffnen Sie **/src/main/java/com/communication/quickstart/App.java** in einem Text-Editor, fügen Sie Importanweisungen hinzu, und entfernen Sie die Anweisung `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features der Java-Clientbibliothek für SMS von Azure Communication Services verwendet:

| Name                                                             | Beschreibung                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Diese Klasse dient zum Erstellen des SMS-Clients (SmsClient). Für sie müssen ein Endpunkt, Anmeldeinformationen und ein HTTP-Client angegeben werden. |
| SmsClient                    | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie wird zum Senden von SMS-Nachrichten verwendet.                |
| SendSmsResponse               | Diese Klasse enthält die Antwort des SMS-Diensts.                                          |
| PhoneNumber                   | Diese Klasse enthält Telefonnummerninformationen.

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen SMS-Client (`SmsClient`) mit Ihrer Verbindungszeichenfolge. Der folgende Code ruft die Endpunkt- und Anmeldeinformationszeichenfolge für die Ressource aus den Umgebungsvariablen `COMMUNICATION_SERVICES_ENDPOINT_STRING` und `COMMUNICATION_SERVICES_CREDENTIAL_STRING` ab. Bei der Anmeldeinformation handelt es sich um den Schlüssel (`Key`) aus dem Azure-Portal. Informationen zur Verwaltung der Verbindungszeichenfolge Ihrer Ressource finden Sie [hier](../../create-communication-resource.md#store-your-connection-string).

Fügen Sie der `main`-Methode folgenden Code hinzu:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Sie können den Client mit einem beliebigen benutzerdefinierten HTTP-Client initialisieren, der die Schnittstelle `com.azure.core.http.HttpClient` implementiert. Im obigen Code wird die Verwendung des von `azure-core` bereitgestellten [Azure Core-Netty-HTTP-Clients](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable) gezeigt.

Anstelle des Endpunkts und des Zugriffsschlüssels können Sie mithilfe der ConnectionString()-Funktion auch die gesamte Verbindungszeichenfolge bereitstellen. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>Senden einer SMS

Senden Sie eine SMS-Nachricht durch Aufrufen der Methode „sendMessage“. Fügen Sie am Ende der Methode `main` den folgenden Code hinzu:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Ersetzen Sie `<leased-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number>` durch die Telefonnummer, an die Sie eine Nachricht senden möchten.

Der Parameter `enableDeliveryReport` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie zum Verzeichnis mit der Datei **pom.xml**, und kompilieren Sie das Projekt mithilfe des Befehls `mvn`.

```console

mvn compile

```

Erstellen Sie dann das Paket.

```console

mvn package

```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```