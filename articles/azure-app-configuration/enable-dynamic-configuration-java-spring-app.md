---
title: Verwenden der dynamischen Konfiguration in einer Spring Boot-App
titleSuffix: Azure App Configuration
description: Hier erfahren Sie, wie Sie Konfigurationsdaten für Spring Boot-Apps dynamisch aktualisieren.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929156"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer Java Spring-App

Die App Configuration-Spring Boot-Clientbibliothek unterstützt die Aktualisierung einer Reihe von Konfigurationseinstellungen bei Bedarf, ohne dass eine Anwendung neu gestartet werden muss. Die Clientbibliothek speichert die einzelnen Einstellungen zwischen, um zu viele Aufrufe des Konfigurationsspeichers zu vermeiden. Der Wert wird vom Aktualisierungsvorgang erst aktualisiert, wenn der zwischengespeicherte Wert abgelaufen ist. Dies gilt auch, wenn sich der Wert im Konfigurationsspeicher geändert hat. Die Standardablaufzeit für jede Anforderung beträgt 30 Sekunden. Sie kann bei Bedarf außer Kraft gesetzt werden.

Sie können bei Bedarf nach aktualisierten Einstellungen suchen, indem Sie die Methode `refreshConfigurations()` von `AppConfigurationRefresh` aufrufen.

Alternativ können Sie das Paket `spring-cloud-azure-appconfiguration-config-web` verwenden, das von `spring-web` abhängig ist, um die automatisierte Aktualisierung zu verarbeiten.

## <a name="use-automated-refresh"></a>Verwenden der automatisierten Aktualisierung

Wenn Sie die automatisierte Aktualisierung verwenden möchten, beginnen Sie mit einer Spring Boot-App, die App Configuration nutzt. Dies kann beispielsweise die App sein, die Sie durch Ausführen der Anleitung unter [Schnellstart: Erstellen einer Java Spring-App mit Azure App Configuration](quickstart-java-spring-app.md) erstellen.

Öffnen Sie dann die Datei *pom.xml* in einem Text-Editor, und fügen Sie `<dependency>` für `spring-cloud-azure-appconfiguration-config-web` hinzu.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Lokales Ausführen und Testen der App

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Öffnen Sie ein neues Browserfenster, und navigieren Sie zur URL `http://localhost:8080`.  Die Nachricht für Ihren Schlüssel wird angezeigt. 

    Sie können auch *curl* zum Testen Ihrer Anwendung verwenden. Beispiel: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Öffnen Sie zum Testen der dynamischen Konfiguration das Azure App Configuration-Portal, das Ihrer Anwendung zugeordnet ist. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie den Wert des angezeigten Schlüssels. Beispiel:
    | Schlüssel | Wert |
    |---|---|
    | application/config.message | Hello - Updated |

1. Aktualisieren Sie die Browserseite, um die neue Nachricht anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre Spring Boot-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
