---
title: 'Schnellstart: Bereitstellen Ihrer ersten Azure Spring Cloud-Anwendung'
description: In dieser Schnellstartanleitung stellen Sie eine Spring Cloud-Anwendung in Azure Spring Cloud bereit.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 3c07d64fd046766521e030b6ee1b88a590246e24
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226168"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Schnellstart: Bereitstellen Ihrer ersten Azure Spring Cloud-Anwendung

::: zone pivot="programming-language-csharp"
In dieser Schnellstartanleitung erfahren Sie, wie Sie eine einfache Azure Spring Cloud-Microserviceanwendung für die Ausführung in Azure bereitstellen.

>[!NOTE]
> Steeltoe-Unterstützung für Azure Spring Cloud wird zurzeit als Public Preview angeboten. Angebote der Public Preview ermöglichen Kunden das Experimentieren mit neuen Funktionen vor der offiziellen Veröffentlichung.  Funktionen und Dienste in der Public Preview sind nicht zur Verwendung in der Produktion bestimmt.  Weitere Informationen zur Unterstützung während der Vorschauphase finden Sie in den [häufig gestellten Fragen](https://azure.microsoft.com/support/faq/). Sie können auch eine [Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md) einreichen.

In dieser Schnellstartanleitung wird Folgendes vermittelt:

> [!div class="checklist"]
> * Generieren eines einfachen Steeltoe-.NET Core-Projekts
> * Bereitstellen einer Azure Spring Cloud-Dienstinstanz
> * Erstellen und Bereitstellen der App mit einem öffentlichen Endpunkt
> * Streamen von Protokollen in Echtzeit

Der in diesem Schnellstart verwendete Anwendungscode ist eine einfache App, die mit einer .NET Core-Web-API-Projektvorlage erstellt wurde. Wenn Sie dieses Beispiel abgeschlossen haben, kann auf die Anwendung online zugegriffen werden, und sie kann über das Azure-Portal und die Azure CLI verwaltet werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Der Azure Spring Cloud-Dienst unterstützt .NET Core 3.1 und höhere Versionen.
* [Azure CLI-Version 2.0.67 oder höher](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Installieren der Azure CLI-Erweiterung

Vergewissern Sie sich, dass Sie mindestens Version 2.0.67 der Azure CLI verwenden:

```azurecli
az --version
```

Führen Sie den folgenden Befehl aus, um die Azure Spring Cloud-Erweiterung für die Azure CLI zu installieren:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Anmelden an Azure

1. Anmelden bei der Azure CLI

    ```azurecli
    az login
    ```

1. Falls Sie über mehrere Abonnements verfügen, wählen Sie das gewünschte Abonnement für diese Schnellstartanleitung aus.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Generieren eines Steeltoe-.NET Core-Projekts

Erstellen Sie in Visual Studio mit der API-Projektvorlage eine ASP.NET Core-Webanwendung mit dem Namen „hello-world“. Beachten Sie, dass ein automatisch generiertes WeatherForecastController-Element vorhanden ist, das später als Testendpunkt verwendet wird.

1. Erstellen Sie einen Ordner für den Projektquellcode, und generieren Sie das Projekt.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Navigieren Sie zum Projektverzeichnis.

   ```console
   cd hello-world
   ```

1. Bearbeiten Sie die Datei *appSettings.json*, um die folgenden Einstellungen hinzuzufügen:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Ändern Sie außerdem in *appsettings.json* die Protokollebene für die Kategorie `Microsoft` von `Warning` in `Information`. Durch diese Änderung wird sichergestellt, dass Protokolle erstellt werden, wenn Sie Streamingprotokolle in einem späteren Schritt anzeigen.

   Die Datei *appsettings.json* sieht nun in etwa wie im folgenden Beispiel aus:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Fügen Sie der Datei *.csproj* Abhängigkeiten und eine Aufgabe vom Typ `Zip` hinzu:

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Die Pakete sind für die Steeltoe-Dienstermittlung und die Azure Spring Cloud-Clientbibliothek bestimmt. Die Aufgabe `Zip` ist für die Bereitstellung in Azure vorgesehen. Wenn Sie den Befehl `dotnet publish` ausführen, werden die Binärdateien im Ordner *publish* generiert, und mit dieser Aufgabe wird der Ordner *publish* in einer *ZIP*-Datei komprimiert, die Sie in Azure hochladen.

3. Fügen Sie in der Datei *Program.cs* eine Anweisung vom Typ `using` sowie Code hinzu, der die Azure Spring Cloud-Clientbibliothek verwendet:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. Fügen Sie in der Datei *Startup.cs* eine Anweisung vom Typ `using` sowie Code hinzu, der die Steeltoe-Dienstermittlung am Ende der Methoden `ConfigureServices` und `Configure` verwendet:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Kompilierungsfehler auftreten.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Bereitstellen einer Dienstinstanz

Mit dem folgenden Verfahren wird eine Azure Spring Cloud-Instanz über das Azure-Portal erstellt:

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/). 

1. Suchen Sie im oberen Suchfeld nach *Azure Spring Cloud*.

1. Wählen Sie in den Ergebnissen *Azure Spring Cloud* aus.

   ![ASC-Symbol: Starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Wählen Sie auf der Seite Azure Spring Cloud die Option **+ Hinzufügen** aus.

   ![ASC-Symbol: Hinzufügen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Füllen Sie das Formular auf der Azure Spring Cloud-Seite **Erstellen** aus.  Berücksichtigen Sie die folgenden Richtlinien:

   * **Abonnement**: Wählen Sie das Abonnement aus, unter dem diese Ressource abgerechnet werden soll.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe. Der hier eingegebene Name wird später als **\<resource group name\>** verwendet.
   * **Dienstdetails/Name**: Geben Sie **\<service instance name\>** an.  Der Name muss zwischen 4 und 32 Zeichen lang sein und darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.  Das erste Zeichen des Dienstnamens muss ein Buchstabe und das letzte Zeichen entweder ein Buchstabe oder eine Ziffer sein.
   * **Region**: Wählen Sie die Region für Ihre Dienstinstanz aus.

   ![Startseite des ASC-Portals](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicken Sie auf **Überprüfen und erstellen**.

## <a name="build-and-deploy-the-app"></a>Erstellen und Bereitstellen der App

Im folgenden Verfahren wird das zuvor von Ihnen generierte Projekt erstellt und bereitgestellt.

1. Stellen Sie sicher, dass sich die Eingabeaufforderung noch im Projektordner befindet.

1. Führen Sie den folgenden Befehl aus, um das Projekt zu erstellen, die Binärdateien zu veröffentlichen und die Binärdateien in einer *ZIP*-Datei im Projektordner zu speichern.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Erstellen Sie eine App in Ihrer Azure Spring Cloud-Instanz mit einem zugewiesenen öffentlichen Endpunkt. Verwenden Sie den gleichen Anwendungsnamen (hello-world), den Sie in *appsettings.json* angegeben haben.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Stellen Sie die *ZIP*-Datei in der App bereit.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   Die Option `--main-entry` identifiziert die *DLL*-Datei, die den Einstiegspunkt der Anwendung enthält. Nachdem der Dienst die *ZIP*-Datei hochgeladen hat, werden alle Dateien und Ordner extrahiert, und es wird versucht, den Einstiegspunkt in der von `--main-entry`angegebenen *DLL*-Datei auszuführen.

   Es dauert einige Minuten, bis die Bereitstellung der Anwendung abgeschlossen ist. Navigieren Sie im Azure-Portal zum Blatt **Apps**, um zu überprüfen, ob die Anwendung bereitgestellt wurde.

## <a name="test-the-app"></a>Testen der App

Nach Abschluss der Bereitstellung können Sie unter folgender URL auf die App zugreifen:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Die App gibt JSON-Daten wie im folgenden Beispiel zurück:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Streamen von Protokollen in Echtzeit

Verwenden Sie den folgenden Befehl, um Echtzeitprotokolle von der App zu erhalten.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Protokolle werden in der Ausgabe angezeigt:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Verwenden Sie `az spring-cloud app logs -h`, um weitere Parameter und Funktionen für Protokolldatenströme zu untersuchen.

Informationen zu erweiterten Protokollanalysefeatures finden Sie im Menü im [Azure-Portal](https://portal.azure.com/) auf der Registerkarte **Protokolle**. Bei den hier aufgeführten Protokollen kommt es zu einer Wartezeit von einigen Minuten.
[ ![Protokollanalyse](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
In dieser Schnellstartanleitung erfahren Sie, wie Sie eine einfache Azure Spring Cloud-Microserviceanwendung für die Ausführung in Azure bereitstellen. 

Der in diesem Tutorial verwendete Anwendungscode ist eine einfache App, die mit Spring initializr erstellt wurde. Wenn Sie das Tutorial abgeschlossen haben, kann auf die Anwendung online zugegriffen werden, und sie kann über das Azure-Portal verwaltet werden.

In dieser Schnellstartanleitung wird Folgendes erläutert:

> [!div class="checklist"]
> * Generieren eines grundlegenden Spring Cloud-Projekts
> * Bereitstellen einer Dienstinstanz
> * Erstellen und Bereitstellen der App mit einem öffentlichen Endpunkt
> * Streamen von Protokollen in Echtzeit

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

* [Installation von JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Registrierung für ein Azure-Abonnement](https://azure.microsoft.com/free/)
* (Optional) [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) und der Azure Spring Cloud-Erweiterung mit dem Befehl `az extension add --name spring-cloud`
* (Optional) [Installation des Azure-Toolkits für IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) und [Anmeldung](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Generieren eines Spring Cloud-Projekts

Beginnen Sie mit [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client), um ein Beispielprojekt mit empfohlenen Abhängigkeiten für Azure Spring Cloud zu generieren. Die folgende Abbildung zeigt die für dieses Beispielprojekt eingerichtete Initializr-Instanz:
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Initializr-Seite](media/spring-cloud-quickstart-java/initializr-page.png)

1. Klicken Sie auf **Generieren**, wenn alle Abhängigkeiten festgelegt sind. Laden Sie das Paket herunter, entpacken Sie es, und erstellen Sie dann einen Webcontroller für eine einfache Webanwendung, indem Sie `src/main/java/com/example/hellospring/HelloController.java` wie folgt hinzufügen:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Bereitstellen einer Instanz von Azure Spring Cloud

Mit dem folgenden Verfahren wird eine Azure Spring Cloud-Instanz über das Azure-Portal erstellt:

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/) in einem neuen Tab. 

2. Suchen Sie im oberen Suchfeld nach *Azure Spring Cloud*.

3. Wählen Sie in den Ergebnissen *Azure Spring Cloud* aus.

    ![ASC-Symbol: Starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klicken Sie auf der Azure Spring Cloud-Seite auf **+ Hinzufügen**.

    ![ASC-Symbol: Hinzufügen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Füllen Sie das Formular auf der Azure Spring Cloud-Seite **Erstellen** aus.  Berücksichtigen Sie die folgenden Richtlinien:
    - **Abonnement**: Wählen Sie das Abonnement aus, unter dem diese Ressource abgerechnet werden soll.
    - **Ressourcengruppe**: Die Erstellung neuer Ressourcengruppen für neue Ressourcen ist eine bewährte Methode. Sie wird später als **\<resource group name\>** verwendet.
    - **Dienstdetails/Name**: Geben Sie **\<service instance name\>** an.  Der Name muss zwischen 4 und 32 Zeichen lang sein und darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.  Das erste Zeichen des Dienstnamens muss ein Buchstabe und das letzte Zeichen entweder ein Buchstabe oder eine Ziffer sein.
    - **Standort**: Wählen Sie die Region für Ihre Dienstinstanz aus.

    ![Startseite des ASC-Portals](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicken Sie auf **Überprüfen und erstellen**.

## <a name="build-and-deploy-the-app"></a>Erstellen und Bereitstellen der App
    
#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/Azure-CLI)
Im folgenden Verfahren wird die Anwendung mithilfe der Azure CLI erstellt und bereitgestellt. Führen Sie im Stammverzeichnis des Projekts folgenden Befehl aus:

1. Erstellen Sie das Projekt mithilfe von Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Installieren Sie die Azure Spring Cloud-Erweiterung für die Azure CLI (sofern sie noch nicht installiert ist):

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Erstellen Sie die App mit einem zugewiesenen öffentlichen Endpunkt:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public true
    ```

1. Stellen Sie die JAR-Datei für die App bereit (`target\hellospring-0.0.1-SNAPSHOT.jar` unter Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Es dauert einige Minuten, bis die Bereitstellung der Anwendung abgeschlossen ist. Navigieren Sie im Azure-Portal zum Blatt **Apps**, um zu überprüfen, ob die Anwendung bereitgestellt wurde. Der Status der Anwendung sollte angezeigt werden.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Im folgenden Verfahren wird das IntelliJ-Plug-In für Azure Spring Cloud verwendet, um die Beispiel-App in IntelliJ IDEA bereitzustellen.  

### <a name="import-project"></a>Importieren des Projekts

1. Öffnen Sie das IntelliJ-Dialogfeld **Willkommen**, und wählen Sie **Projekt importieren** aus, um den Import-Assistenten zu öffnen.
1. Wählen Sie den Ordner `hellospring` aus.

    ![Importieren des Projekts](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Bereitstellen der App
Für die Bereitstellung in Azure müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihr Abonnement auswählen.  Anmeldeinformationen finden Sie unter [Installation und Anmeldung](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Klicken Sie im Projektexplorer von IntelliJ mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Azure** -> **In Azure Spring Cloud bereitstellen** aus.

    [ ![Bereitstellen in Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Übernehmen Sie den Namen für die App im Feld **Name**. **Name** bezieht sich auf die Konfiguration, nicht auf den App-Namen. Benutzer müssen ihn in der Regel nicht ändern.
1. Wählen Sie im Textfeld **Artefakt** die Datei *hellospring-0.0.1-SNAPSHOT.jar* aus.
1. Überprüfen Sie im Textfeld **Abonnement** Ihr Abonnement.
1. Wählen Sie im Textfeld **Spring Cloud** die Instanz von Azure Spring Cloud aus, die Sie unter [Schnellstart: Bereitstellen des Azure Spring Cloud-Diensts](./spring-cloud-quickstart-provision-service-instance.md) erstellt haben.
1. Legen Sie **Öffentlicher Endpunkt** auf *Aktivieren* fest.
1. Wählen Sie im Textfeld **App:** die Option **App erstellen...** aus.
1. Geben Sie *hellospring* ein, und klicken Sie dann auf **OK**.

    [ ![Bereitstellung in Azure: OK](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Starten Sie die Bereitstellung, indem Sie unten im Dialogfeld **Deploy Azure Spring Cloud app** (Azure Spring Cloud-App bereitstellen) auf die Schaltfläche **Ausführen** klicken. Das Plug-In führt den Befehl `mvn package` für die App `hellospring` aus, und stellt die vom `package`-Befehl generierte JAR-Datei bereit.
---

Nach Abschluss der Bereitstellung können Sie unter `https://<service instance name>-hellospring.azuremicroservices.io/` auf die App zugreifen.

  [ ![Zugreifen auf die App über den Browser](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Streamingprotokolle in Echtzeit

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/Azure-CLI)

Verwenden Sie den folgenden Befehl, um Echtzeitprotokolle von der App zu erhalten.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Protokolle werden in den Ergebnissen angezeigt:

[ ![Streamingprotokolle](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Verwenden Sie `az spring-cloud app logs -h`, um weitere Parameter und Funktionen für Protokolldatenströme zu untersuchen.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Wählen Sie **Azure-Explorer** und dann **Spring Cloud** aus.
1. Klicken Sie mit der rechten Maustaste auf die ausgeführte App.
1. Wählen Sie in der Dropdownliste **Streamingprotokolle** aus.
1. Wählen Sie die Instanz aus.

    [ ![Auswählen von Streamingprotokollen](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Das Streamingprotokoll wird im Ausgabefenster angezeigt.

    [ ![Ausgabe des Streamingprotokolls](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Informationen zu erweiterten Protokollanalysefeatures finden Sie im Menü im [Azure-Portal](https://portal.azure.com/) auf der Registerkarte **Protokolle**. Bei den hier aufgeführten Protokollen kommt es zu einer Wartezeit von einigen Minuten.

[ ![Protokollanalyse](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen erstellt, für die weiterhin Gebühren anfallen, während sie in Ihrem Abonnement verbleiben. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe über das Portal oder durch Ausführen des folgenden Befehls in der Azure CLI:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie:

> [!div class="checklist"]
> * Generieren eines grundlegenden Azure Spring Cloud-Projekts
> * Bereitstellen einer Dienstinstanz
> * Erstellen und Bereitstellen der App mit einem öffentlichen Endpunkt
> * Streamen von Protokollen in Echtzeit

Informationen zu weiteren Azure Spring-Funktionen finden Sie in der Schnellstartserie, in der eine Beispielanwendung in Azure Spring Cloud bereitgestellt wird:

> [!div class="nextstepaction"]
> [Erstellen und Ausführen von Microservices](spring-cloud-quickstart-sample-app-introduction.md)

Weitere Beispiele finden Sie auf GitHub: [Azure Spring Cloud-Beispiele](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).