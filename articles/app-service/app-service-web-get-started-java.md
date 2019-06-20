---
title: Erstellen von Java-Web-Apps unter Windows – Azure App Service
description: In dieser Schnellstartanleitung stellen Sie in wenigen Minuten Ihre erste Java-App „Hallo Welt“ in Azure App Service unter Windows bereit.
keywords: Azure, App Service, Web-App, Windows, Java, Maven, Schnellstart
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jasonfreeberg
ms.custom: mvc
ms.openlocfilehash: c77f7afe3941395a156896135043710252637ef3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393233"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Schnellstart: Erstellen einer Java-App in App Service

> [!NOTE]
> In diesem Artikel wird eine App in App Service unter Windows bereitgestellt. Informationen zur Bereitstellung in App Service unter _Linux_ finden Sie unter [Erstellen einer Java-Web-App unter Linux](./containers/quickstart-java.md).
>

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.  In dieser Schnellstartanleitung wird gezeigt, wie Sie mit der [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) und dem [Maven-Plug-In für Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) eine Java-Webarchivdatei (WAR) bereitstellen.

> [!NOTE]
> Dazu können auch gängige IDEs wie IntelliJ und Eclipse verwendet werden. Sehen Sie sich unsere ähnlichen Dokumente in den Schnellstarts für [Azure-Toolkit für IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) oder [Azure-Toolkit für Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) an.
>
![In Azure ausgeführte Beispiel-App](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Erstellen einer Java-App

Führen Sie den folgenden Maven-Befehl über die Eingabeaufforderung der Cloud Shell aus, um eine neue App mit dem Namen `helloworld` zu erstellen:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurieren des Maven-Plug-Ins

Verwenden Sie zum Bereitstellen über Maven den Code-Editor in der Cloud Shell, um die Projektdatei `pom.xml` im Verzeichnis `helloworld` zu öffnen. 

```bash
code pom.xml
```

Fügen Sie dann die folgende Plug-In-Definition im `<build>`-Element der Datei `pom.xml` hinzu.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.4</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> In diesem Artikel arbeiten wir nur mit Java-Apps, die in WAR-Dateien enthalten sind. Das Plug-In unterstützt auch JAR-Webanwendungen. Dies können Sie unter [Bereitstellen einer Spring Boot-App mit JAR-Datei in Azure App Service unter Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ausprobieren.


Aktualisieren Sie die folgenden Platzhalter in der Plug-In-Konfiguration:

| Platzhalter | BESCHREIBUNG |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Die eindeutige ID des Abonnements, in dem Sie Ihre App bereitstellen möchten. Die ID des Standardabonnement finden Sie in der Cloud Shell oder der CLI mithilfe des Befehls `az account show`. Verwenden Sie für alle verfügbaren Abonnements den Befehl `az account list`.|
| `RESOURCEGROUP_NAME` | Der Name der neuen Ressourcengruppe, in der Ihre App erstellt wird. Indem Sie alle Ressourcen für eine App in einer Gruppe zusammenfassen, können Sie sie zusammen verwalten. Wenn Sie beispielsweise die Ressourcengruppe löschen, werden alle Ressourcen gelöscht, die der App zugeordnet sind. Aktualisieren Sie diesen Wert mit einem neuen eindeutigen Ressourcengruppennamen, z.B. *TestResources*. In einem Abschnitt weiter unten verwenden Sie diesen Ressourcengruppennamen zum Bereinigen aller Azure-Ressourcen. |
| `WEBAPP_NAME` | Der App-Name wird bei der Bereitstellung in Azure als Teil des Hostnamens für die App (WEBAPP_NAME.azurewebsites.net) verwendet. Aktualisieren Sie diesen Wert mit einem eindeutigen Namen für die neue App Service-App, die Ihre Java-App hostet, z.B. *contoso*. |
| `REGION` | Eine Azure-Region, in der die App gehostet wird, z.B. `westus2`. Sie können eine Liste von Regionen über die Cloud Shell oder CLI mit dem Befehl `az account list-locations` abrufen. |

## <a name="deploy-the-app"></a>Bereitstellen der App

Stellen Sie Ihre Java-App mit dem folgenden Befehl in Azure bereit:

```bash
mvn package azure-webapp:deploy
```

Navigieren Sie nach Abschluss der Bereitstellung zur bereitgestellten Anwendung, indem Sie in Ihrem Webbrowser die folgende URL verwenden, z. B. `http://<webapp>.azurewebsites.net/`.

![In Azure ausgeführte Beispiel-App](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Glückwunsch!** Sie haben Ihre erste Java-App in App Service unter Windows bereitgestellt.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ressourcen: Azure für Java-Entwickler](/java/azure/)

> [!div class="nextstepaction"]
> [Zuordnen einer benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain.md)