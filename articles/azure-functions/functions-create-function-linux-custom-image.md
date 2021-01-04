---
title: Erstellen von Azure-Funktionen unter Linux mit einem benutzerdefinierten Image
description: Hier erfahren Sie, wie Sie Azure Functions erstellen, die auf einem benutzerdefinierten Linux-Image ausgeführt werden.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: f270f74f97a9b9306d7b23dacec12c38f418dbd1
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921826"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Erstellen einer Funktion unter Linux mit einem benutzerdefinierten Container

In diesem Tutorial erstellen Sie Code und stellen ihn anschließend in Azure Functions als benutzerdefinierten Docker-Container mit einem Linux-Basisimage bereit. Normalerweise verwenden Sie ein benutzerdefiniertes Image, wenn Sie für Ihre Funktionen eine bestimmte Sprachversion benötigen oder über eine bestimmte Abhängigkeit oder Konfiguration verfügen, die über das integrierte Image nicht bereitgestellt wird.

::: zone pivot="programming-language-other"
Von Azure Functions wird jede Sprache oder Runtime mit [benutzerdefinierten Handlern](functions-custom-handlers.md) unterstützt. Für einige Sprachen (beispielsweise die in diesem Tutorial verwendete Programmiersprache R) müssen Sie die Runtime oder zusätzliche Bibliotheken als Abhängigkeiten installieren, die die Verwendung eines benutzerdefinierten Containers erfordern.
::: zone-end

Die Bereitstellung Ihres Funktionscodes in einem benutzerdefinierten Linux-Container erfordert Hosting im [Premium-Tarif](functions-premium-plan.md#features) oder in einem [Dedizierten Tarif (App Service-Tarif)](functions-scale.md#app-service-plan). Das Abschließen dieses Tutorials verursacht Kosten von einigen USD auf Ihrem Azure-Konto, die Sie durch [Bereinigen der Ressourcen](#clean-up-resources) minimieren können, wenn Sie fertig sind.

Sie können auch einen Azure App Service-Standardcontainer verwenden. Dies ist unter [Schnellstart: Erstellen Ihrer ersten unter Linux gehosteten Funktion unter Verwendung von Befehlszeilentools](./create-first-function-cli-csharp.md?pivots=programming-language-python) beschrieben. Unterstützte Basisimages für Azure Functions finden Sie im [Azure Functions-Repository für Basisimages](https://hub.docker.com/_/microsoft-azure-functions-base).

In diesem Tutorial lernen Sie Folgendes:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Erstellen einer Funktions-App und Dockerfile mit den Azure Functions Core Tools.
> * Erstellen eines benutzerdefinierten Images mit Docker.
> * Veröffentlichen eines benutzerdefinierten Images in einer Containerregistrierung.
> * Erstellen von unterstützenden Ressourcen in Azure für die Funktions-App.
> * Bereitstellen einer Funktions-App über Docker Hub.
> * Hinzufügen von Anwendungseinstellungen für die Funktions-App.
> * Aktivieren Sie Continuous Deployment.
> * Aktivieren Sie SSH-Verbindungen mit dem Container.
> * Fügen Sie eine Queue Storage-Ausgabebindung hinzu. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Erstellen einer Funktions-App und Dockerfile mit den Azure Functions Core Tools.
> * Erstellen eines benutzerdefinierten Images mit Docker.
> * Veröffentlichen eines benutzerdefinierten Images in einer Containerregistrierung.
> * Erstellen von unterstützenden Ressourcen in Azure für die Funktions-App.
> * Bereitstellen einer Funktions-App über Docker Hub.
> * Hinzufügen von Anwendungseinstellungen für die Funktions-App.
> * Aktivieren Sie Continuous Deployment.
> * Aktivieren Sie SSH-Verbindungen mit dem Container.
::: zone-end

Sie können dieses Tutorial auf allen Computern durcharbeiten, auf denen Windows, macOS oder Linux ausgeführt wird. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Führen Sie `docker login` aus, um sich bei Docker anzumelden. Für diesen Befehl tritt ein Fehler auf, wenn Docker nicht ausgeführt wird. Starten Sie in diesem Fall Docker, und führen Sie den Befehl erneut aus.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Erstellen und Testen des lokalen Funktionsprojekts

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Führen Sie in einem Terminal oder an einer Eingabeaufforderung den folgenden Befehl für die gewählte Sprache aus, um ein Funktions-App-Projekt in einem Ordner mit dem Namen `LocalFunctionsProject` zu erstellen.  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Führen Sie in einem leeren Ordner den folgenden Befehl aus, um das Functions-Projekt über einen [Maven-Archetyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) zu generieren.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

Der `-DjavaVersion`-Parameter teilt der Functions-Runtime mit, welche Version von Java verwendet werden soll. Verwenden Sie `-DjavaVersion=11`, wenn Sie Ihre Funktionen in Java 11 ausführen möchten. Wenn Sie `-DjavaVersion` nicht angeben, wird für Maven standardmäßig Java 8 verwendet. Weitere Informationen finden Sie unter [Java-Versionen](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> Damit Sie diesen Artikel durcharbeiten können, muss die Umgebungsvariable `JAVA_HOME` auf den Installationsspeicherort der richtigen Version des JDK festgelegt sein.

Maven fordert Sie zur Eingabe von Werten auf, die erforderlich sind, um die Generierung des Projekts bei der Bereitstellung abzuschließen.   
Geben Sie die folgenden Werte ein, wenn Sie dazu aufgefordert werden:

| Prompt | Wert | BESCHREIBUNG |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ein Wert, der Ihr Projekt projektübergreifend eindeutig identifiziert. Für den Wert müssen die [Paketbenennungsregeln](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) für Java eingehalten werden. |
| **artifactId** | `fabrikam-functions` | Der Name des Behälters (ohne Versionsnummer). |
| **version** | `1.0-SNAPSHOT` | Wählen Sie den Standardwert aus. |
| **package** | `com.fabrikam.functions` | Das Java-Paket für den generierten Funktionscode. Verwenden Sie den Standardwert. |

Geben Sie zur Bestätigung `Y` ein, oder drücken Sie die EINGABETASTE.

Maven erstellt die Projektdateien in einem neuen Ordner und benennt ihn mit dem Wert von _artifactId_ (in diesem Beispiel: `fabrikam-functions`). 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

Mit der Option `--docker` wird eine `Dockerfile` für das Projekt generiert. Hiermit wird ein geeigneter benutzerdefinierter Container zur Verwendung mit Azure Functions und der ausgewählten Runtime definiert.

Navigieren Sie zum Projektordner:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Fügen Sie dem Projekt eine Funktion hinzu, indem Sie den unten angegebenen Befehl verwenden. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion, und mit dem `--template`-Argument wird der Trigger der Funktion angegeben. Mit `func new` wird ein Unterordner passend zum Funktionsnamen erstellt. Er enthält eine geeignete Codedatei für die gewählte Sprache des Projekts und eine Konfigurationsdatei mit dem Namen *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Fügen Sie dem Projekt eine Funktion hinzu, indem Sie den unten angegebenen Befehl verwenden. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion, und mit dem `--template`-Argument wird der Trigger der Funktion angegeben. Mit `func new` wird ein Unterordner mit dem Funktionsnamen erstellt, der eine Konfigurationsdatei mit dem Namen *function.json* enthält.

```console
func new --name HttpExample --template "HTTP trigger"
```

Erstellen Sie in einem Text-Editor eine Datei im Projektordner mit dem Namen *handler.R*. Fügen Sie den folgenden Inhalt hinzu:

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Ändern Sie in *host.json* den Abschnitt `customHandler`, um den Startbefehl des benutzerdefinierten Handlers zu konfigurieren.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Starten Sie zum lokalen Testen der Funktion den lokalen Azure Functions-Runtimehost im Stammverzeichnis des Projektordners: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Navigieren Sie zu `http://localhost:7071/api/HttpExample?name=Functions`, nachdem in der Ausgabe der `HttpExample`-Endpunkt angezeigt wird. Im Browser sollte eine Begrüßungsnachricht mit `Functions` (dem für den Abfrageparameter `name` angegebenen Wert) angezeigt werden.

Verwenden Sie **STRG**-**C**, um den Host zu beenden.

## <a name="build-the-container-image-and-test-locally"></a>Erstellen und lokales Testen des Containerimages

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Optional) Untersuchen Sie die *Dockerfile* im Stammverzeichnis des Projektordners. In der Dockerfile wird die Umgebung beschrieben, die zum Ausführen der Funktions-App unter Linux benötigt wird.  Die vollständige Liste mit den unterstützten Basisimages für Azure Functions finden Sie auf der [Seite mit Azure Functions-Basisimages](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Untersuchen Sie das *Dockerfile* im Stammverzeichnis des Projektordners. In der Dockerfile wird die Umgebung beschrieben, die zum Ausführen der Funktions-App unter Linux benötigt wird. Von Anwendungen mit benutzerdefiniertem Handler wird das Image `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` als Grundlage verwendet.

Ändern Sie das *Dockerfile*, um R zu installieren. Ersetzen Sie den Inhalt des *Dockerfile* durch Folgendes:

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Führen Sie im Stammprojektordner den Befehl [docker build](https://docs.docker.com/engine/reference/commandline/build/) aus, und geben Sie einen Namen (`azurefunctionsimage`) und ein Tag (`v1.0.0`) an. Ersetzen Sie `<DOCKER_ID>` durch Ihre Docker Hub-Konto-ID. Dieser Befehl erstellt das Docker-Image für den Container.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Nachdem der Befehl abgeschlossen wurde, können Sie den neuen Container lokal ausführen.
    
Führen Sie das Image zum Testen des Builds in einem lokalen Container aus, indem Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) verwenden und erneut `<DOCKER_ID` durch Ihre Docker-ID ersetzen und das Portargument `-p 8080:80` hinzufügen:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Wenn das Image in einem lokalen Container ausgeführt wird, können Sie in einem Browser auf `http://localhost:8080` zugreifen, damit das unten angegebene Platzhalterbild angezeigt wird. Das Bild wird hier angezeigt, weil Ihre Funktion im lokalen Container so ausgeführt wird, wie dies auch in Azure der Fall wäre. Dies bedeutet, dass sie durch einen Zugriffsschlüssel geschützt ist, der in *function.json* mit der `"authLevel": "function"`-Eigenschaft definiert wird. Da der Container aber noch nicht in einer Funktions-App in Azure veröffentlicht wurde, ist der Schlüssel noch nicht verfügbar. Gehen Sie wie folgt vor, wenn Sie den lokalen Container testen möchten: Beenden Sie Docker, ändern Sie die Autorisierungseigenschaft in `"authLevel": "anonymous"`, erstellen Sie das Image neu, und starten Sie Docker anschließend wieder. Setzen Sie dann `"authLevel": "function"` in *function.json* zurück. Weitere Informationen finden Sie unter [Autorisierungsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Platzhalterbild mit dem Hinweis, dass der Container lokal ausgeführt wird](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Wenn das Image in einem lokalen Container ausgeführt wird, navigieren Sie in einem Browser zu `http://localhost:8080/api/HttpExample?name=Functions`. Daraufhin sollte die gleiche Begrüßungsnachricht wie zuvor angezeigt werden. Da der Maven-Archetyp eine durch HTTP ausgelöste Funktion generiert, die die anonyme Autorisierung verwendet, können Sie die Funktion weiterhin aufrufen, auch wenn sie im Container ausgeführt wird. 
::: zone-end  

Nachdem Sie die Funktions-App im Container überprüft haben, beenden Sie Docker mit **STRG**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Übertragen des Images per Pushvorgang auf Docker Hub

Docker Hub ist eine Containerregistrierung, mit der Images gehostet und Image- und Containerdienste bereitgestellt werden. Um Ihr Image freizugeben (umfasst auch die Bereitstellung in Azure), müssen Sie es per Pushvorgang in eine Registrierung übertragen.

1. Wenn Sie sich noch nicht bei Docker angemeldet haben, können Sie dies durchführen, indem Sie den Befehl [docker login](https://docs.docker.com/engine/reference/commandline/login/) verwenden und `<docker_id>` durch Ihre Docker-ID ersetzen. Mit diesem Befehl werden Sie zum Eingeben Ihres Benutzernamens und Kennworts aufgefordert. Mit einer Meldung der Art „Anmeldung erfolgreich“ wird bestätigt, dass die Anmeldung erfolgreich war.

    ```console
    docker login
    ```
    
1. Übertragen Sie das Image nach dem Anmelden per Pushvorgang an Docker Hub, indem Sie den Befehl [docker push](https://docs.docker.com/engine/reference/commandline/push/) verwenden und `<docker_id>` wieder durch Ihre Docker-ID ersetzen.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Je nach Netzwerkgeschwindigkeit kann der erste Pushvorgang für das Image einige Minuten dauern (das Pushen der nachfolgenden Änderungen geht deutlich schneller). Während Sie warten, können Sie mit dem nächsten Abschnitt fortfahren und Azure-Ressourcen in einem anderen Terminal erstellen.

## <a name="create-supporting-azure-resources-for-your-function"></a>Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen:

- Eine Ressourcengruppe als logischen Container für verwandte Ressourcen.
- Ein Azure Storage-Konto, unter dem Status- und andere Informationen zu Ihren Projekten verwaltet werden.
- Eine Azure-Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Eine Funktions-App ist Ihrem lokalen Funktionsprojekt zugeordnet und ermöglicht Ihnen das Gruppieren von Funktionen als logische Einheit, um die Verwaltung, Bereitstellung und Freigabe von Ressourcen zu vereinfachen.

Sie verwenden Azure CLI-Befehle, um diese Elemente zu erstellen. Für jeden Befehl wird nach Abschluss der Ausführung eine JSON-Ausgabe bereitgestellt.

1. Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Azure an:

    ```azurecli
    az login
    ```
    
1. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `AzureFunctionsContainers-rg` in der Region `westeurope` erstellt. (Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie über den Befehl `az account list-locations` eine verfügbare Region nutzen.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > In derselben Ressourcengruppe können nicht gleichzeitig Linux- und Windows-Apps gehostet werden. Wenn Sie über eine bestehende Ressourcengruppe mit dem Namen `AzureFunctionsContainers-rg` und einer Windows-Funktions-App oder -Web-App verfügen, müssen Sie eine andere Ressourcengruppe verwenden.
    
1. Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) in Ihrer Ressourcengruppe und Region ein universelles Speicherkonto. Ersetzen Sie im folgenden Beispiel `<storage_name>` durch einen global eindeutigen Namen, der für Sie geeignet ist. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein typisches universelles Konto angegeben.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Mit diesem Speicherkonto fallen für dieses Tutorial nur Kosten in Höhe von wenigen US-Cent an.
    
1. Verwenden Sie den Befehl, um den Premium-Plan `myPremiumPlan` für Azure Functions im Tarif **Elastic Premium 1** (`--sku EP1`), in der Region „Europa, Westen“ (`-location westeurope` oder einer geeigneten Region in Ihrer Nähe) und in einem Linux-Container (`--is-linux`) zu erstellen.

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Wir verwenden hier den Premium-Plan, der je nach Bedarf skaliert werden kann. Weitere Informationen zum Hosting finden Sie unter [Vergleich von Hostingplänen für Azure Functions](functions-scale.md). Informationen zum Berechnen der Kosten finden Sie auf der Seite [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

    Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Erstellen und Konfigurieren einer Funktions-App in Azure mit dem Image

Mit einer Funktions-App in Azure wird die Ausführung der Funktionen Ihres Hostingplans verwaltet. In diesem Abschnitt verwenden Sie die Azure-Ressourcen aus dem vorherigen Abschnitt, um eine Funktions-App aus einem Image unter Docker Hub zu erstellen und mit einer Verbindungszeichenfolge für Azure Storage zu konfigurieren.

1. Erstellen Sie die Funktions-App mit dem Befehl [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Ersetzen Sie im folgenden Beispiel `<storage_name>` durch den Namen, den Sie im vorherigen Abschnitt für das Speicherkonto verwendet haben. Ersetzen Sie außerdem `<app_name>` durch einen für Sie geeigneten global eindeutigen Namen und `<docker_id>` durch Ihre Docker-ID.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    Mit dem Parameter *deployment-container-image-name* wird das Image angegeben, das für die Funktions-App verwendet wird. Sie können den Befehl [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) verwenden, um Informationen zum für die Bereitstellung verwendeten Image anzuzeigen. Darüber hinaus können Sie den Befehl [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) nutzen, um ein anderes Image für die Bereitstellung zu verwenden.

1. Verwenden Sie den Befehl [az storage account show-connection-string](/cli/azure/storage/account), um die Verbindungszeichenfolge für das von Ihnen erstellte Speicherkonto anzuzeigen. Ersetzen Sie `<storage-name>` durch den Namen des weiter oben erstellten Speicherkontos:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Verwenden Sie den Befehl [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set), um diese Einstellung der Funktions-App hinzuzufügen. Ersetzen Sie im folgenden Befehl `<app_name>` durch den Namen Ihrer Funktions-App und `<connection_string>` durch die Verbindungszeichenfolge aus dem vorherigen Schritt (eine lange codierte Zeichenfolge, die mit „DefaultEndpointProtocol=“ beginnt):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > In Bash können Sie anstelle der Zwischenablage eine Shellvariable verwenden, um die Verbindungszeichenfolge zu erfassen. Verwenden Sie zunächst den folgenden Befehl, um eine Variable mit der Verbindungszeichenfolge zu erstellen:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Verweisen Sie dann im zweiten Befehl auf die Variable:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Die Funktion kann diese Verbindungszeichenfolge jetzt verwenden, um auf das Speicherkonto zuzugreifen.

> [!NOTE]    
> Wenn Sie Ihr benutzerdefiniertes Image unter einem privaten Containerkonto veröffentlichen, sollten Sie stattdessen Umgebungsvariablen in der Dockerfile für die Verbindungszeichenfolge verwenden. Weitere Informationen finden Sie unter der [ENV-Anweisung](https://docs.docker.com/engine/reference/builder/#env). Sie sollten auch die Variablen `DOCKER_REGISTRY_SERVER_USERNAME` und `DOCKER_REGISTRY_SERVER_PASSWORD` festlegen. Zum Verwenden der Werte müssen Sie das Image dann neu erstellen und per Pushvorgang in die Registrierung übertragen und anschließend die Funktions-App in Azure neu starten.

## <a name="verify-your-functions-on-azure"></a>Überprüfen Ihrer Funktionen in Azure

Da das Image nun für die Funktions-App in Azure bereitgestellt wurde, können Sie die Funktion über HTTP-Anforderungen aufrufen. Da die *function.json*-Definition die `"authLevel": "function"`-Eigenschaft enthält, müssen Sie zuerst den Zugriffsschlüssel (auch als „Funktionsschlüssel“ bezeichnet) beschaffen und als URL-Parameter in alle Anforderungen für den Endpunkt einfügen.

1. Rufen Sie die Funktions-URL mit dem Zugriffsschlüssel (Funktionsschlüssel) ab, indem Sie das Azure-Portal oder die Azure CLI mit dem Befehl `az rest` verwenden.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Melden Sie sich beim Azure-Portal an, suchen Sie nach **Funktions-App**, und wählen Sie diese Option aus.

    1. Wählen Sie die Funktion aus, die Sie überprüfen möchten.

    1. Wählen Sie im linken Navigationsbereich die Option **Funktionen** und dann die zu überprüfende Funktion aus.

        ![Auswählen Ihrer Funktion im Azure-Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Wählen Sie **Funktions-URL abrufen** aus.

        ![Abrufen der Funktions-URL aus dem Azure-Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Wählen Sie im Popupfenster die Option **Standard (Funktionstaste)** aus, und kopieren Sie die URL in die Zwischenablage. Der Schlüssel ist die Zeichenfolge nach `?code=`.

        ![Auswählen des Standardfunktions-Zugriffsschlüssels](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Da Ihre Funktions-App als Container bereitgestellt ist, können Sie im Portal keine Änderungen an Ihrem Funktionscode vornehmen. Stattdessen müssen Sie das Projekt im lokalen Image aktualisieren und wieder per Pushvorgang an die Registrierung übertragen und anschließend erneut in Azure bereitstellen. Sie können Continuous Deployment in einem späteren Abschnitt einrichten.
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

    1. Erstellen Sie eine URL-Zeichenfolge im folgenden Format, und ersetzen Sie `<subscription_id>`, `<resource_group>` und `<app_name>` durch die ID Ihres Azure-Abonnements, die Ressourcengruppe Ihrer Funktions-App bzw. den Namen Ihrer Funktions-App:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Die URL kann beispielsweise wie die folgende Adresse aussehen:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Der Einfachheit halber können Sie die URL stattdessen einer Umgebungsvariablen zuweisen und im Befehl `az rest` verwenden.
    
    1. Führen Sie den folgenden `az rest`-Befehl aus (verfügbar unter Azure CLI-Version 2.0.77 und höher), indem Sie `<uri>` durch die URI-Zeichenfolge aus dem letzten Schritt ersetzen (einschließlich der Anführungszeichen):

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Die Ausgabe des Befehls ist der Funktionsschlüssel. Die vollständige Funktions-URL lautet dann `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`. Ersetzen Sie `<app_name>`, `<function_name>` und `<key>` durch Ihre jeweiligen Werte.
    
        > [!NOTE]
        > Der hier abgerufene Schlüssel ist der *Hostschlüssel*, der für alle Funktionen in der Funktions-App genutzt werden kann. Mit der für das Portal angezeigten Methode wird nur der Schlüssel für die eine Funktion abgerufen.

    ---

1. Fügen Sie die Funktions-URL in die Adressleiste Ihres Browsers ein, und fügen Sie den Parameter `&name=Azure` am Ende dieser URL hinzu. Im Browser sollte Text wie „Hello, Azure“ angezeigt werden.

    ![Funktionsantwort im Browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Entfernen Sie zum Testen der Autorisierung den Parameter `code=` aus der URL, und vergewissern Sie sich, dass Sie keine Antwort von der Funktion erhalten.


## <a name="enable-continuous-deployment-to-azure"></a>Aktivieren von Continuous Deployment in Azure

Sie können Azure Functions aktivieren, um Ihre Bereitstellung eines Images jeweils automatisch zu aktualisieren, wenn Sie das Bild in der Registrierung aktualisieren.

1. Aktivieren Sie Continuous Deployment, indem Sie den Befehl [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) verwenden und `<app_name>` durch den Namen Ihrer Funktions-App ersetzen:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Mit diesem Befehl wird Continuous Deployment aktiviert und die Webhook-URL der Bereitstellung zurückgegeben. (Sie können diese URL später jederzeit abrufen, indem Sie den Befehl [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) verwenden.)

1. Kopieren Sie die Webhook-URL der Bereitstellung in die Zwischenablage.

1. Öffnen Sie [Docker Hub](https://hub.docker.com/), melden Sie sich an, und wählen Sie in der Navigationsleiste die Option **Repositories** (Repositorys) aus. Suchen Sie nach dem Image, und wählen Sie es aus. Wählen Sie die Registerkarte **Webhooks** aus, geben Sie einen **Webhooknamen** ein, fügen Sie unter **Webhook-URL** Ihre URL ein, und wählen Sie **Erstellen** aus:

    ![Hinzufügen des Webhooks in Ihrem DockerHub-Repository](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Nachdem der Webhook festgelegt wurde, wird Ihr Image von Azure Functions jeweils erneut bereitgestellt, wenn Sie es in Docker Hub aktualisieren.

## <a name="enable-ssh-connections"></a>Aktivieren von SSH-Verbindungen

SSH ermöglicht die sichere Kommunikation zwischen einem Container und einem Client. Wenn SSH aktiviert ist, können Sie mithilfe der erweiterten App Service-Tools (Kudu) eine Verbindung mit Ihrem Container herstellen. Azure Functions stellt ein Basisimage bereit, für das SSH bereits aktiviert ist, um das Herstellen einer Verbindung mit Ihrem Container per SSH zu vereinfachen. Sie müssen nur Ihre Dockerfile bearbeiten und dann das Image neu erstellen und bereitstellen. Anschließend können Sie mit den Advanced Tools (Kudu) eine Verbindung mit dem Container herstellen.

1. Fügen Sie in Ihrer Dockerfile die Zeichenfolge `-appservice` an das Basisimage in Ihrer `FROM`-Anweisung an:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Erstellen Sie das Image neu, indem Sie den Befehl `docker build` verwenden und `<docker_id>` durch Ihre Docker-ID ersetzen:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Übertragen Sie das aktualisierte Image per Pushvorgang an Docker Hub. Dies sollte deutlich weniger Zeit als der erste Pushvorgang in Anspruch nehmen, da nur die aktualisierten Segmente des Images hochgeladen werden müssen.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions stellt das Image automatisch für Ihre Funktions-App bereit. Der Prozess dauert weniger als eine Minute.

1. Öffnen Sie in einem Browser `https://<app_name>.scm.azurewebsites.net/`, indem Sie `<app_name>` durch Ihren eindeutigen Namen ersetzen. Diese URL ist der Advanced Tools-Endpunkt (Kudu) für Ihren Funktions-App-Container.

1. Melden Sie sich an Ihrem Azure-Konto an, und wählen Sie **SSH** aus, um eine Verbindung mit dem Container herzustellen. Die Verbindungsherstellung kann etwas dauern, falls das Containerimage von Azure noch aktualisiert wird.

1. Nachdem eine Verbindung mit Ihrem Container hergestellt wurde, können Sie den Befehl `top` ausführen, um die derzeit ausgeführten Prozesse anzuzeigen. 

    ![Linux-Befehl „top“, der in einer SSH-Sitzung ausgeführt wird](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Schreiben in eine Azure Storage-Warteschlange

Mit Azure Functions können Sie Ihre Funktionen mit anderen Azure-Diensten und -Ressourcen verbinden, ohne dass Sie eigenen Integrationscode schreiben müssen. Diese *Bindungen*, die sowohl Eingabe als auch Ausgabe darstellen, werden innerhalb der Funktionsdefinition deklariert. Daten von Bindungen werden der Funktion als Parameter bereitgestellt. Ein *Trigger* ist ein spezieller Typ von Eingabebindung. Eine Funktion hat zwar nur einen Trigger, kann aber mehrere Ein- und Ausgabebindungen haben. Weitere Informationen finden Sie unter [Konzepte der Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md).

In diesem Abschnitt wird gezeigt, wie Sie Ihre Funktion in eine Azure Storage-Warteschlange integrieren. Die Ausgabebindung, die Sie dieser Funktion hinzufügen, schreibt Daten aus einer HTTP-Anforderung in eine Nachricht in der Warteschlange.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Hinzufügen von Code für die Verwendung der Ausgabebindung

Nachdem die Warteschlangenbindung definiert wurde, können Sie Ihre Funktion nun so aktualisieren, dass sie den Ausgabeparameter `msg` empfängt und Nachrichten in die Warteschlange geschrieben werden.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Aktualisieren des Images in der Registrierung

1. Führen Sie im Stammordner erneut `docker build` aus, und aktualisieren Sie die Version im Tag dieses Mal auf `v1.0.1`. Ersetzen Sie `<docker_id>` wie zuvor durch Ihre Docker Hub-Konto-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Übertragen Sie das aktualisierte Image per `docker push` zurück in das Repository:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Da Sie Continuous Delivery konfiguriert haben, wird Ihre Funktions-App beim erneuten Aktualisieren des Images in der Registrierung automatisch in Azure aktualisiert.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Anzeigen der Nachricht in der Azure Storage-Warteschlange

Verwenden Sie in einem Browser dieselbe URL wie zuvor, um Ihre Funktion aufzurufen. Im Browser sollte die gleiche Antwort wie zuvor angezeigt werden, da Sie diesen Teil des Funktionscodes nicht geändert haben. Aufgrund des hinzugefügten Codes wurde aber eine Nachricht in die Speicherwarteschlange `outqueue` geschrieben, indem der URL-Parameter `name` verwendet wurde.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit Azure Functions weiterarbeiten und die in diesem Tutorial erstellten Ressourcen weiterhin nutzen möchten, können Sie sie alle beibehalten. Da Sie einen Premium-Plan für Azure Functions erstellt haben, fallen für Sie pro Tag ein oder zwei US-Dollar an laufenden Kosten an.

Löschen Sie zur Vermeidung laufender Kosten die Ressourcengruppe `AzureFunctionsContainer-rg`, um alle Ressourcen dieser Gruppe zu bereinigen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nächste Schritte

+ [Überwachen von Funktionen](functions-monitoring.md)
+ [Skalierungs- und Hostingoptionen](functions-scale.md)
+ [Kubernetes-basiertes serverloses Hosting](functions-kubernetes-keda.md)
