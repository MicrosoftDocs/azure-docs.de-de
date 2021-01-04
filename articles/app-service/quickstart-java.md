---
title: 'Schnellstart: Erstellen einer Java-App in Azure App Service'
description: Stellen Sie in wenigen Minuten Ihre erste Java-App vom Typ „Hallo Welt“ in Azure App Service bereit. Mit dem Azure-Web-App-Plug-In für Maven können Sie Java-Apps bequem bereitstellen.
keywords: Azure, App Service, Web-App, Windows, Linux, Java, Maven, Schnellstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 3c5a04bd14bdcbf250908db78c622b963f191d91
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333085"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Schnellstart: Erstellen einer Java-App in Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.  In dieser Schnellstartanleitung wird gezeigt, wie Sie mit der [Azure CLI](/cli/azure/get-started-with-azure-cli) und dem [Azure-Web-App-Plug-In für Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) eine JAR- oder WAR-Datei bereitstellen. Verwenden Sie die Registerkarten, um zwischen Java SE- und Tomcat-Anweisungen zu wechseln.


> [!NOTE]
> Dazu können auch gängige IDEs wie IntelliJ und Eclipse verwendet werden. Sehen Sie sich unsere ähnlichen Dokumente in den Schnellstarts für [Azure-Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) oder [Azure-Toolkit für Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app) an.


![In Azure App Service ausgeführte Beispiel-App](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Erstellen einer Java-App

# <a name="java-se"></a>[Java SE](#tab/javase)

Klonen Sie das Beispielprojekt [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Führen Sie den folgenden Maven-Befehl über die Eingabeaufforderung der Cloud Shell aus, um eine neue App mit dem Namen `helloworld` zu erstellen:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ändern Sie anschließend Ihr Arbeitsverzeichnis in den Projektordner:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Konfigurieren des Maven-Plug-Ins

Der Prozess zur Bereitstellung in Azure App Service verwendet automatisch Ihre Azure-Anmeldeinformation aus der Azure CLI. Ist die Azure CLI nicht lokal installiert, nutzt das Maven-Plug-In OAuth oder die Geräteanmeldung für die Authentifizierung. Weitere Informationen finden Sie unter [Authentifizierung mit Maven-Plug-Ins](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Führen Sie den folgenden Maven-Befehl aus, um die Bereitstellung zu konfigurieren. Dieser Befehl unterstützt Sie beim Einrichten des App Service-Betriebssystems, der Java-Version und der Tomcat-Version.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Wenn die Eingabeaufforderung für die **Abonnementoption** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die **Web-App-Option** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken, oder wählen Sie eine vorhandene App aus.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Windows** aus, indem Sie `3` eingeben.
1. Wenn die Eingabeaufforderung für die **Tarifoption** angezeigt wird, wählen Sie **B2** aus, in dem Sie `2` eingeben.
1. Verwenden Sie die Java-Standardversion ( **Java 8** ), indem Sie die EINGABETASTE drücken.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    Die Zusammenfassungsausgabe sieht in etwa wie der unten gezeigte Codeausschnitt aus.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wenn die Eingabeaufforderung für die **Abonnementoption** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die **Web-App-Option** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken, oder wählen Sie eine vorhandene App aus.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Windows** aus, indem Sie `3` eingeben.
1. Wenn die Eingabeaufforderung für die **Tarifoption** angezeigt wird, wählen Sie **B2** aus, in dem Sie `2` eingeben.
1. Verwenden Sie die Java-Standardversion ( **Java 8** ), indem Sie die EINGABETASTE drücken.
1. Verwenden Sie den Standardwebcontainer ( **Tomcat 8.5** ), indem Sie die EINGABETASTE drücken.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    Die Zusammenfassungsausgabe sieht in etwa wie der unten gezeigte Codeausschnitt aus.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Wenn die Eingabeaufforderung für die **Abonnementoption** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die **Web-App-Option** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken, oder wählen Sie eine vorhandene App aus.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Linux** aus, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die **Tarifoption** angezeigt wird, wählen Sie **B2** aus, in dem Sie `2` eingeben.
1. Verwenden Sie die Java-Standardversion ( **Java 8** ), indem Sie die EINGABETASTE drücken.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wenn die Eingabeaufforderung für die **Abonnementoption** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die **Web-App-Option** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken, oder wählen Sie eine vorhandene App aus.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Linux** aus, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die **Tarifoption** angezeigt wird, wählen Sie **B2** aus, in dem Sie `2` eingeben.
1. Verwenden Sie die Java-Standardversion ( **Java 8** ), indem Sie die EINGABETASTE drücken.
1. Verwenden Sie den Standardwebcontainer ( **Tomcat 8.5** ), indem Sie die EINGABETASTE drücken.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Sie können bei Bedarf die Konfigurationen für App Service direkt in der Datei `pom.xml` ändern. Im Folgenden sind einige gängige Konfigurationen aufgeführt:

Eigenschaft | Erforderlich | Beschreibung | Version
---|---|---|---
`<schemaVersion>` | false | Gibt die Version des Konfigurationsschemas an. Folgende Werte werden unterstützt: `v1` und `v2`. | 1.5.2
`<subscriptionId>` | false | Geben Sie die Abonnement-ID an. | 0.1.0+
`<resourceGroup>` | true | Azure-Ressourcengruppe für Ihre Web-App. | 0.1.0+
`<appName>` | true | Der Name Ihrer Web-App. | 0.1.0+
`<region>` | true | Gibt die Region an, in der Ihre Web-App gehostet wird. Der Standardwert ist **westeurope**. Alle gültigen Regionen finden Sie im Abschnitt [Unterstützten Regionen](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | Der Tarif für Ihre Web-App. Für Produktionsworkloads lautet der Standardwert **P1V2** , für Entwicklungs- und Testworkloads in Java ist der empfohlene Mindestwert hingegen **B2**. [Weitere Informationen](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | Details zur Konfiguration der Laufzeitumgebung finden Sie [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | Details zur Konfiguration der Bereitstellung finden Sie [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Notieren Sie sich die Werte `<appName>` und `<resourceGroup>` (`helloworld-1590394316693` in `helloworld-1590394316693-rg` in der Demo). Sie werden später verwendet.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Bereitstellen der App

Das Maven-Plug-In verwendet bei der Bereitstellung in App Services Kontoanmeldeinformation aus der Azure CLI. [Melden Sie sich über die Azure CLI an](/cli/azure/authenticate-azure-cli), ehe Sie fortfahren.

```azurecli
az login
```

Dann können Sie Ihre Java-App mit dem folgenden Befehl in Azure bereitstellen.

```bash
mvn package azure-webapp:deploy
```

Nach Abschluss der Bereitstellung steht Ihre Anwendung unter `http://<appName>.azurewebsites.net/` (`http://helloworld-1590394316693.azurewebsites.net` in der Demo) bereit. Öffnen Sie die URL in Ihrem lokalen Webbrowser. Folgendes sollte angezeigt werden:

![In Azure App Service ausgeführte Beispiel-App](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Glückwunsch!** Sie haben Ihre erste Java-App in App Service bereitgestellt.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe im Portal, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Verwenden von Java zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver und zum Abfragen von Daten](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Richten Sie CI/CD ein.](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Preisinformationen](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Aggregieren von Protokollen und Metriken](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Hochskalieren](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Ressourcen: Azure für Java-Entwickler](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurieren der Java-App](configure-language-java.md)
