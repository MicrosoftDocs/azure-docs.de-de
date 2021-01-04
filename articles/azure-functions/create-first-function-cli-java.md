---
title: 'Erstellen einer Java-Funktion über die Befehlszeile: Azure Functions'
description: Erfahren Sie, wie Sie eine Java-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für das serverlose Hosten in Azure Functions veröffentlichen.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ms.openlocfilehash: db24fa4721e2e502c5098b415c4cf06da80f697d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175677"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer Java-Funktion über die Befehlszeile in Azure

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In diesem Artikel verwenden Sie Befehlszeilentools zum Erstellen einer Java-Funktion, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

> [!NOTE]
> Sollte Maven nicht Ihr bevorzugtes Entwicklungstool sein, stehen ähnliche Tutorials mit [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) und [Visual Studio Code](create-first-function-vs-code-java.md) für Java-Entwickler zur Verfügung.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

+ [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) ab Version 2.4

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), Version 8 oder 11. Die Umgebungsvariable `JAVA_HOME` muss auf den Installationsspeicherort der richtigen Version des JDK festgelegt sein.     

+ [Apache Maven](https://maven.apache.org), Version 3.0 oder höher

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

## <a name="create-a-local-function-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie in einem leeren Ordner den folgenden Befehl aus, um das Functions-Projekt über einen [Maven-Archetyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) zu generieren. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Verwenden Sie `-DjavaVersion=11`, wenn Sie Ihre Funktionen in Java 11 ausführen möchten. Weitere Informationen finden Sie unter [Java-Versionen](functions-reference-java.md#java-versions). 
    > + Damit Sie diesen Artikel durcharbeiten können, muss die Umgebungsvariable `JAVA_HOME` auf den Installationsspeicherort der richtigen Version des JDK festgelegt sein.

1. Maven fordert Sie zur Eingabe von Werten auf, die erforderlich sind, um die Generierung des Projekts bei der Bereitstellung abzuschließen.   
    Geben Sie die folgenden Werte ein, wenn Sie dazu aufgefordert werden:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Ein Wert, der Ihr Projekt projektübergreifend eindeutig identifiziert. Für den Wert müssen die [Paketbenennungsregeln](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) für Java eingehalten werden. |
    | **artifactId** | `fabrikam-functions` | Der Name des Behälters (ohne Versionsnummer). |
    | **version** | `1.0-SNAPSHOT` | Wählen Sie den Standardwert aus. |
    | **package** | `com.fabrikam` | Das Java-Paket für den generierten Funktionscode. Verwenden Sie den Standardwert. |

1. Geben Sie zur Bestätigung `Y` ein, oder drücken Sie die EINGABETASTE.

    Maven erstellt die Projektdateien in einem neuen Ordner und benennt ihn mit dem Wert von _artifactId_ (in diesem Beispiel: `fabrikam-functions`). 

1. Navigieren Sie zum Projektordner:

    ```console
    cd fabrikam-functions
    ```

    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-run-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE*-Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.

### <a name="optional-examine-the-file-contents"></a>(Optional) Untersuchen des Dateiinhalts

Bei Bedarf können Sie [Lokales Ausführen der Funktion](#run-the-function-locally) überspringen und den Dateiinhalt später untersuchen.

#### <a name="functionjava"></a>Function.java
*Function.java* enthält eine Methode vom Typ `run`, die Anforderungsdaten in der Variablen `request` empfängt. Hierbei handelt es sich um eine HTTP-Anforderungsnachricht ([HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)), die mit der Anmerkung [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) versehen ist, um das Triggerverhalten zu definieren. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Die Antwortnachricht wird von der API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) generiert.

#### <a name="pomxml"></a>pom.xml

Einstellungen für die Azure-Ressourcen, die zum Hosten Ihrer App erstellt werden, werden in der generierten Datei „pom.xml“ im Konfigurationselement (**configuration**) des Plug-Ins mit der Gruppen-ID (**groupId**) `com.microsoft.azure` definiert. So wird beispielsweise durch das folgende Konfigurationselement eine Maven-basierte Bereitstellung angewiesen, eine Funktions-App in der Ressourcengruppe `java-functions-group` in der Region `westus` zu erstellen. Die Funktions-App selbst wird unter Windows mit Hosting im Tarif `java-functions-app-service-plan` ausgeführt. Bei dem Tarif handelt es sich standardmäßig um einen serverlosen Verbrauchstarif.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Diese Einstellungen ermöglichen die Steuerung der Ressourcenerstellung in Azure. Beispielsweise können Sie `runtime.os` vor der ersten Bereitstellung von `windows` in `linux` ändern. Eine umfassende Liste der Einstellungen, die vom Maven-Plug-In unterstützt werden, finden Sie in den [Konfigurationsdetails](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

Durch den Archetyp wird auch ein Komponententest für Ihre Funktion generiert. Wenn Sie Ihrer Funktion Bindungen hinzufügen oder dem Projekt neue Funktionen hinzufügen, müssen auch die Tests in der Datei *FunctionTest.java* geändert werden.

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Führen Sie Ihre Funktion aus, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *LocalFunctionProj* starten:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    Gegen Ende der Ausgabe sollten die folgenden Zeilen angezeigt werden:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Sollte „HttpExample“ nicht wie unten dargestellt angezeigt werden, haben Sie den Host wahrscheinlich außerhalb des Stammordners des Projekts gestartet. Drücken Sie in diesem Fall **STRG**+**C**, um den Host zu beenden. Navigieren Sie anschließend zum Stammordner des Projekts, und führen Sie den vorherigen Befehl erneut aus.

1. Kopieren Sie die URL Ihrer `HttpExample`-Funktion aus dieser Ausgabe in einen Browser, und fügen Sie die Abfragezeichenfolge `?name=<YOUR_NAME>` an. Die vollständige URL lautet dann wie folgt: `http://localhost:7071/api/HttpExample?name=Functions`. Im Browser sollte eine Meldung wie `Hello Functions` angezeigt werden:

    ![Ergebnis der lokal im Browser ausgeführten Funktion](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Im Terminal, in dem Sie Ihr Projekt gestartet haben, wird beim Senden von Anforderungen auch die Protokollausgabe angezeigt.

1. Wenn Sie fertig sind, drücken Sie **STRG**+**C**, und wählen Sie `y` aus, um den Funktionshost zu beenden.

## <a name="deploy-the-function-project-to-azure"></a>Bereitstellen des Funktionsprojekts in Azure

Wenn Sie Ihr Funktionsprojekt erstmals bereitstellen, werden in Azure eine Funktions-App und zugehörige Ressourcen erstellt. Einstellungen für die Azure-Ressourcen, die zum Hosten Ihrer App erstellt werden, werden in der Datei [pom.xml](#pomxml) definiert. In diesem Artikel werden die Standardeinstellungen übernommen.

> [!TIP]
> Wenn Sie eine Funktions-App für Linux anstatt für Windows erstellen möchten, ändern Sie das Element `runtime.os` in der Datei „pom.xml“ von `windows` in `linux`. Das Ausführen von Linux in einem Verbrauchstarif wird in [diesen Regionen](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) unterstützt. Unter Linux ausgeführte Apps und unter Windows ausgeführte Apps können nicht in der gleichen Ressourcengruppe enthalten sein.

1. Damit Sie die Bereitstellung durchführen können, müssen Sie sich entweder über die Azure CLI oder über Azure PowerShell bei Ihrem Azure-Abonnement anmelden. 

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Mit dem Befehl [az login](/cli/azure/reference-index#az-login) werden Sie bei Ihrem Azure-Konto angemeldet.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldet Sie bei Ihrem Konto an.

    ---

1. Verwenden Sie den folgenden Befehl, um Ihr Projekt für eine neue Funktions-App bereitzustellen.

    ```console
    mvn azure-functions:deploy
    ```
    
    Dadurch werden in Azure die folgenden Ressourcen erstellt:
    
    + Ressourcengruppe namens _java-functions-group_.
    + Speicherkonto – von Functions benötigt. Der Name wird nach dem Zufallsprinzip basierend auf den Anforderungen für den Speicherkontonamen generiert.
    + Hostingplan: Serverloses Hosting für Ihre Funktions-App in der Region _westus_. Der Name lautet _java-functions-app-service-plan_.
    + Funktions-App – die Bereitstellungs-und Ausführungseinheit für Ihre Funktionen. Der Name wird auf Grundlage der _artifactId_ zufällig generiert und mit einer zufällig generierten Zahl angefügt.
    
    Bei der Bereitstellung werden die Projektdateien mithilfe von [zip deployment](functions-deployment-technologies.md#zip-deploy) gepackt und für die neue Funktions-App bereitgestellt. Der Code wird über das Bereitstellungspaket in Azure ausgeführt.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Gehen Sie wie folgt vor, falls Sie mit dem [nächsten Schritt](#next-steps) fortfahren möchten: Fügen Sie eine Ausgabebindung für die Azure Storage-Warteschlange hinzu, und behalten Sie alle Ressourcen bei, weil der nächste Schritt auf der bisherigen Arbeit aufbaut.

Verwenden Sie andernfalls den unten angegebenen Befehl, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen, damit keine weiteren Kosten anfallen.

 # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)