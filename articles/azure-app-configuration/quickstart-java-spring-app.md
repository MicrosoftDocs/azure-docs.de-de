---
title: 'Schnellstart: Verwenden von Azure App Configuration | Microsoft-Dokumentation'
description: Enthält eine Schnellstartanleitung für die Verwendung von Azure App Configuration mit Java Spring-Apps.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: a91c61edd773b5742b092f5d72a5a22f1d90e63b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393548"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Schnellstart: Erstellen einer Java Spring-App mit App Configuration

Azure App Configuration ist ein verwalteter Konfigurationsdienst in Azure. Mit diesem Dienst können Sie Ihre gesamten Anwendungseinstellungen komfortabel an einem zentralen Ort speichern und verwalten, der von Ihrem Code getrennt ist. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie den Dienst in eine Java Spring-App einbinden.

Für die Ausführung der Schritte in diesem Schnellstart können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie für diesen Schnellstart ein unterstütztes [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) (Version 8) und [Apache Maven](https://maven.apache.org/) (Version 3.0 oder höher).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Erstellen eines App-Konfigurationsspeichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wählen Sie **Konfigurations-Explorer** >  **+ Erstellen** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | /application/config.message | Hallo |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

## <a name="create-a-spring-boot-app"></a>Erstellen einer Spring Boot-App

Sie verwenden [Spring Initializr](https://start.spring.io/), um ein neues Spring Boot-Projekt zu erstellen.

1. Navigieren Sie zu <https://start.spring.io/>.

2. Verwenden Sie die folgenden Optionen:

   * Generieren Sie ein **Maven**-Projekt mit **Java**.
   * Geben Sie eine **Spring Boot**-Version ab 2.0 an.
   * Geben Sie Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung an.
   * Fügen Sie die **Web**-Abhängigkeit hinzu.

3. Wählen Sie nach Angabe der vorherigen Optionen die Option **Projekt generieren** aus. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

## <a name="connect-to-an-app-configuration-store"></a>Herstellen einer Verbindung mit einem App-Konfigurationsspeicher

1. Nachdem Sie die Dateien auf Ihrem lokalen System extrahiert haben, kann Ihre einfache Spring Boot-Anwendung bearbeitet werden. Suchen Sie im Stammverzeichnis Ihrer App nach der Datei *pom.xml*.

2. Öffnen Sie die Datei *pom.xml* in einem Text-Editor, und fügen Sie Spring Cloud Azure Config Starter der Liste mit den Abhängigkeiten (`<dependencies>`) hinzu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
    </dependency>
    ```

3. Erstellen Sie eine neue Java-Datei mit dem Namen *MessageProperties.java* im Paketverzeichnis Ihrer App. Fügen Sie die folgenden Zeilen hinzu:

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Erstellen Sie im Paketverzeichnis Ihrer App eine neue Java-Datei mit dem Namen *HelloController.java*. Fügen Sie die folgenden Zeilen hinzu:

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Öffnen Sie die Java-Hauptanwendungsdatei, und fügen Sie `@EnableConfigurationProperties` hinzu, um diese Funktion zu aktivieren.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Erstellen Sie eine neue Datei mit der Bezeichnung `bootstrap.properties` unter dem Verzeichnis „resources“ der App, und fügen Sie die folgenden Zeilen in der Datei ein. Ersetzen Sie die Beispielwerte durch die entsprechenden Eigenschaften für Ihren App-Konfigurationsspeicher.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus. Beispiel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Nachdem Ihre Anwendung ausgeführt wird, testen Sie sie mit *cURL*. Beispiel:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Es wird die Nachricht angezeigt, die Sie im App-Konfigurationsspeicher eingegeben haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen neuen App-Konfigurationsspeicher erstellt und mit einer Java Spring-App verwendet. Weitere Informationen finden Sie unter [Spring in Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Weitere Informationen zur Verwendung von App Configuration finden Sie im nächsten Tutorial, in dem es um die Authentifizierung geht.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
