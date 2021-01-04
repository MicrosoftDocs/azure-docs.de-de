---
title: Arbeiten mit Azure Functions Core Tools
description: Erfahren Sie, wie Sie Azure Functions über die Eingabeaufforderung oder das Terminal auf dem lokalen Computer codieren und testen, bevor Sie sie in Azure Functions ausführen.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 3cf495ca0687e6fa37f619615fb1529659a462dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020350"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeiten mit Azure Functions Core Tools

Mit Azure Functions Core Tools können Sie Ihre Funktionen über die Eingabeaufforderung oder das Terminal auf dem lokalen Computer entwickeln und testen. Die lokalen Funktionen können mit Azure-Livediensten verbunden werden, und Sie können die Funktionen unter Verwendung der vollständigen Functions-Runtime auf dem lokalen Computer debuggen. Sie können sogar eine Funktions-App in Ihrem Azure-Abonnement bereitstellen.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Beim Entwickeln von Funktionen auf Ihrem lokalen Computer und der anschließenden Veröffentlichung auf Azure mithilfe von Core Tools führen Sie diese grundlegenden Schritte aus:

> [!div class="checklist"]
> * [Installieren Sie die Core-Tools und Abhängigkeiten.](#v2)
> * [Erstellen Sie ein Projekt für eine Funktions-App aus einer sprachspezifischen Vorlage.](#create-a-local-functions-project)
> * [Registrieren Sie die Trigger- und Bindungserweiterungen.](#register-extensions)
> * [Definieren Sie Speicher- und andere Verbindungen.](#local-settings-file)
> * [Erstellen Sie eine Funktion aus einem Trigger und einer sprachspezifischen Vorlage.](#create-func)
> * [Lokales Ausführen der Funktion](#start)
> * [Veröffentlichen des Projekts in Azure](#publish)

## <a name="core-tools-versions"></a>Core Tools-Versionen

Es sind drei Versionen der Azure Functions Core Tools verfügbar. Welche Version Sie verwenden, hängt von der lokalen Entwicklungsumgebung, der [Sprachauswahl](supported-languages.md) und der erforderlichen Unterstützung ab:

+ [**Version 3.x/2.x**](#v2): Unterstützt entweder [Version 3.x oder 2.x der Azure Functions-Runtime](functions-versions.md). Diese Versionen unterstützen [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2)und [Linux](?tabs=linux#v2) und verwenden plattformspezifische Paket-Manager oder npm für die Installation.

+ **Version 1.x**: Unterstützt Version 1.x der Azure Functions-Runtime. Diese Version der Tools wird nur auf Windows-Computern unterstützt und wird über ein [npm-Paket](https://www.npmjs.com/package/azure-functions-core-tools) installiert.

Sie können auf einem Computer immer nur jeweils eine Version der Core Tools installieren. Sofern nicht anders angegeben, gelten die Beispiele in diesem Artikel für Version 3.x.

## <a name="prerequisites"></a>Voraussetzungen

Azure Functions Core Tools verwendet zurzeit die Azure CLI, um sich mit Ihrem Azure-Konto zu authentifizieren. Dies bedeutet, dass Sie [die Azure-CLI lokal installieren](/cli/azure/install-azure-cli) müssen, um von Azure Functions Core Tools aus [in Azure veröffentlichen](#publish) zu können. 

## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

[Azure Functions Core Tools] umfasst eine Version der gleichen Runtime, auf der die Azure Functions-Runtime basiert, die Sie auf dem lokalen Entwicklungscomputer ausführen können. Zudem sind Befehle zum Erstellen von Funktionen, Herstellen einer Verbindung mit Azure und Bereitstellen von Functions-Projekten enthalten.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Version 3.x und 2.x

Die Tools der Version 3.x/2.x verwenden die Azure Functions-Runtime, die auf .NET Core basiert. Diese Version wird auf allen Plattformen unterstützt, die von .NET Core unterstützt werden, einschließlich [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2) und [Linux](?tabs=linux#v2). 

> [!IMPORTANT]
> Sie können auf das Installieren des .NET Core SDK verzichten, wenn Sie [Erweiterungsbundles] verwenden.

# <a name="windows"></a>[Windows](#tab/windows)

Die folgenden Schritte verwenden einen Windows Installer (MSI) zum Installieren der Core Tools v3.x. Weitere Informationen zu anderen paketbasierten Installationsprogrammen, die für die Installation von Core Tools v2.x erforderlich sind, finden Sie in der [Infodatei zu Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Laden Sie den Core Tools-Installer basierend auf Ihrer Version von Windows herunter, und führen Sie ihn aus:

    - [v3.x: Windows 64-Bit](https://go.microsoft.com/fwlink/?linkid=2135274) (empfohlen. [Visual Studio Code-Debuggen](functions-develop-vs-code.md#debugging-functions-locally) erfordert 64-Bit.)
    - [v3.x: Windows 32-Bit](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles](functions-bindings-register.md#extension-bundles) zu verwenden, installieren Sie das [.NET Core 3.x SDK für Windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

Die folgenden Schritte verwenden Homebrew zum Installieren der Core Tools unter macOS.

1. Installieren Sie [Homebrew](https://brew.sh/), sofern dies noch nicht geschehen ist.

1. Installieren Sie das Core Tools-Paket:

    ##### <a name="v3x-recommended"></a>v3.x (empfohlen)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles](functions-bindings-register.md#extension-bundles) zu verwenden, installieren Sie das [.NET Core 3.x SDK für macOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Die folgenden Schritte verwenden [APT](https://wiki.debian.org/Apt) zum Installieren der Core Tools unter Ihrer Ubuntu/Debian Linux-Distribution. Informationen zu anderen Linux-Distributionen finden Sie in der [Infodatei zu den Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installieren Sie den GPG-Schlüssel des Microsoft-Paketrepositorys, um die Paketintegrität zu überprüfen:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Richten Sie die Liste der APT-Quellen ein, bevor Sie ein APT-Update ausführen.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Überprüfen Sie die Datei `/etc/apt/sources.list.d/dotnetdev.list` auf eine der entsprechenden Linux-Versionszeichenfolgen, die unten aufgeführt sind:

    | Linux-Verteilung | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Starten Sie das Update der APT-Quelle:

    ```bash
    sudo apt-get update
    ```

1. Installieren Sie das Core Tools-Paket:

    ##### <a name="v3x-recommended"></a>v3.x (empfohlen)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2.x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles](functions-bindings-register.md#extension-bundles) zu verwenden, installieren Sie das [.NET Core 3.x SDK für Linux](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Functions-Projekts

Ein Functions-Projektverzeichnis enthält die Dateien [host.json](functions-host-json.md) und [local.settings.json](#local-settings-file) sowie Unterordner, die den Code für einzelne Funktionen enthalten. Dieses Verzeichnis ist das Äquivalent zu einer Funktions-App in Azure. Weitere Informationen zur Azure Functions-Ordnerstruktur finden Sie unter [Azure Functions: Entwicklerhandbuch](functions-reference.md#folder-structure).

In Version 3.x/2.x müssen Sie eine Standardsprache für das Projekt auswählen, wenn es initialisiert wird. In Version 3.x/2.x werden für alle hinzugefügten Funktionen Vorlagen mit der Standardsprache verwendet. In Version 1.x geben Sie die Sprache bei jedem Erstellen einer Funktion an.

Führen Sie im Terminalfenster oder über eine Eingabeaufforderung den folgenden Befehl aus, um das Projekt und ein lokales Git-Repository zu erstellen:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java verwendet einen Maven-Archetyp, um das lokale Functions-Projekt zusammen mit der ersten durch HTTP ausgelösten Funktion zu erstellen. Verwenden Sie den folgenden Befehl, um das Java-Projekt zu erstellen: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype`. Ein Beispiel für die Verwendung des Maven-Archetyps finden Sie im [Befehlszeilen-Schnellstart](./create-first-function-cli-java.md).  

Wenn Sie einen Projektnamen angeben, wird ein neuer Ordner mit diesem Namen erstellt und initialisiert. Andernfalls wird der aktuelle Ordner initialisiert.  
In Version 3.x/2.x müssen Sie beim Ausführen des Befehls eine Runtime für das Projekt auswählen. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Verwenden Sie die NACH-OBEN- oder NACH-UNTEN-TASTE, um eine Sprache auszuwählen, und drücken Sie dann die EINGABETASTE. Wenn Sie JavaScript- oder TypeScript-Funktionen entwickeln möchten, wählen Sie **node** und dann die Sprache aus. Für TypeScript gelten [einige zusätzliche Anforderungen](functions-reference-node.md#typescript). 

Die Ausgabe ähnelt dem folgenden Beispiel für ein JavaScript-Projekt:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` unterstützt die folgenden Optionen, die nur unter Version 3.x/2.x verfügbar sind, sofern nicht anders angegeben:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Erstellt .NET-Funktionen als C#-Skript, dies entspricht dem Verhalten in Version 1.x. Nur mit `--worker-runtime dotnet` gültig. |
| **`--docker`** | Erstellt ein Dockerfile für einen Container mithilfe eines Basisimages, das auf dem ausgewählten Wert für `--worker-runtime` basiert. Verwenden Sie diese Option, wenn Sie die Veröffentlichung in einem benutzerdefinierten Linux-Container planen. |
| **`--docker-only`** |  Fügt einem vorhandenen Projekt eine Dockerfile hinzu. Fordert die Worker-Runtime an, wenn sie nicht angegeben oder in „local.settings.json“ festgelegt wurde. Verwenden Sie diese Option, wenn Sie die Veröffentlichung eines vorhandenen Projekts in einem benutzerdefinierten Linux-Container planen. |
| **`--force`** | Initialisiert das Projekt, auch wenn es bereits Dateien enthält. Diese Einstellung überschreibt vorhandene Dateien mit dem gleichen Namen. Andere Dateien im Projektordner sind nicht betroffen. |
| **`--language`** | Initialisiert ein sprachspezifisches Projekt. Wird derzeit unterstützt, wenn `--worker-runtime` auf `node` festgelegt ist. Die Optionen sind `typescript` und `javascript`. Sie können auch `--worker-runtime javascript` oder `--worker-runtime typescript` verwenden.  |
| **`--managed-dependencies`**  | Installiert verwaltete Abhängigkeiten. Derzeit unterstützt nur die PowerShell-Workerruntime diese Funktion. |
| **`--source-control`** | Steuert, ob ein Git-Repository erstellt wird. Standardmäßig wird kein Repository erstellt. Im Fall von `true` wird ein Repository erstellt. |
| **`--worker-runtime`** | Legt die Runtime der Sprache für das Projekt fest. Unterstützte Werte sind: `csharp`, `dotnet`, `javascript`,`node` (JavaScript), `powershell`, `python` und `typescript`. Für Java verwenden Sie [Maven](functions-reference-java.md#create-java-functions). Wird kein Wert festgelegt, werden Sie während der Initialisierung zur Auswahl der Runtime aufgefordert. |
|
> [!IMPORTANT]
> Ab Version 2.x der Core-Tools werden Funktions-App-Projekte für die .NET-Runtime als [C#-Klassenprojekte](functions-dotnet-class-library.md) (.csproj) erstellt. Diese C#-Projekte, die mit Visual Studio oder Visual Studio Code verwendet werden können, werden während der Tests und beim Veröffentlichen in Azure kompiliert. Wenn Sie stattdessen dieselben C#-Skriptdateien (.csx) erstellen und verwenden möchten, die in Version 1.x und im Portal erstellt wurden, müssen Sie die `--csx`-Parameter beim Erstellen und Bereitstellen von Funktionen einschließen.

## <a name="register-extensions"></a>Registrieren von Erweiterungen

Mit Ausnahme von HTTP- und Zeitgebertriggern werden Functions-Bindungen in der Runtimeversion 2.x und höher als Erweiterungspakete implementiert. Für HTTP-Bindungen und Zeitgebertrigger sind keine Erweiterungen erforderlich. 

Um Inkompatibilitäten zwischen den verschiedenen Erweiterungspaketen zu reduzieren, können Sie mit Functions auf ein Erweiterungsbündel in der host.json-Projektdatei verweisen. Wenn Sie keine Erweiterungsbündel verwenden möchten, müssen Sie auch das .NET Core 2.x SDK lokal installieren und die Erweiterung CSPROJ mit Ihrem Functions-Projekt verwalten.  

In Version 2.x und höheren Versionen der Azure Functions-Runtime müssen Sie die in Ihren Funktionen für die Bindungstypen verwendeten Erweiterungen explizit registrieren. Sie können auch Bindungserweiterungen einzeln installieren, oder Sie können einen Erweiterungsbündelverweis in der Datei „host.json“ hinzufügen. Erweiterungsbündel wirken eventuellen Kompatibilitätsproblemen bei Paketen entgegen, wenn mehrere Bindungstypen verwendet werden. Es handelt sich hierbei um den empfohlenen Ansatz zum Registrieren von Bindungserweiterungen. Erweiterungsbündel beseitigen außerdem die Notwendigkeit der Installation des .NET Core 2.x SDK. 

### <a name="use-extension-bundles"></a>Verwenden von Erweiterungsbündeln

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Weitere Informationen finden Sie unter [Registrieren von Bindungserweiterungen von Azure Functions](functions-bindings-register.md#extension-bundles). Sie sollten Erweiterungsbündel in de Datei „host.json“ hinzufügen, bevor Sie der Datei „function.json“ Bindungen hinzufügen.

### <a name="explicitly-install-extensions"></a>Explizites Installieren von Erweiterungen

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standardmäßig werden diese Einstellungen nicht automatisch migriert, wenn das Projekt in Azure veröffentlicht wird. Verwenden Sie den Switch `--publish-local-settings`[bei der Veröffentlichung](#publish), um sicherzustellen, dass diese Einstellungen zur Funktionen-App in Azure hinzugefügt werden. Beachten Sie, dass Werte in **ConnectionStrings** niemals veröffentlicht werden.

Die Werte für Funktions-App-Einstellungen können auch im Code als Umgebungsvariablen gelesen werden. Weitere Informationen finden Sie im Abschnitt „Umgebungsvariablen“ dieser sprachspezifischen Referenzthemen:

* [Vorkompilierter C#-Code](functions-dotnet-class-library.md#environment-variables)
* [C#-Skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Wenn keine gültige Speicherverbindungszeichenfolge für [`AzureWebJobsStorage`] festgelegt ist und der Emulator nicht verwendet wird, wird die folgende Fehlermeldung angezeigt:

> Missing value for AzureWebJobsStorage in local.settings.json (Fehlender Wert für AzureWebJobsStorage in local.settings.json). Dies ist für alle Nicht-HTTP-Trigger erforderlich. Sie können „func azure functionapp fetch-app-settings \<functionAppName\>“ ausführen oder eine Verbindungszeichenfolge in „local.settings.json“ eingeben.

### <a name="get-your-storage-connection-strings"></a>Abrufen der Speicherverbindungszeichenfolgen

Auch wenn der Microsoft Azure-Speicheremulator für die Entwicklung verwendet wird, empfiehlt es sich, einen Test mit einer tatsächlichen Speicherverbindung durchzuführen. Wenn Sie bereits [ein Speicherkonto erstellt haben](../storage/common/storage-account-create.md), können Sie eine gültige Speicherverbindungszeichenfolge anhand einer der folgenden Methoden abrufen:

- Suchen Sie im [Azure portal] nach **Speicherkonten**, und wählen Sie diese Option aus. 
  ![Auswählen von Speicherkonten im Azure-Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Wählen Sie Ihr Speicherkonto aus, wählen Sie **Zugriffsschlüssel** unter **Einstellungen** aus, und kopieren Sie dann einen der Werte für **Verbindungszeichenfolge**.
  ![Kopieren der Verbindungszeichenfolge im Azure-Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Verwenden Sie [Azure Storage-Explorer](https://storageexplorer.com/), um eine Verbindung mit Ihrem Azure-Konto herzustellen. Erweitern Sie im **Explorer** Ihr Abonnement und anschließend **Speicherkonten**, wählen Sie Ihr Speicherkonto aus, und kopieren Sie die primäre oder sekundäre Verbindungszeichenfolge.

  ![Kopieren der Verbindungszeichenfolge im Storage-Explorer](./media/functions-run-local/storage-explorer.png)

+ Verwenden Sie Core Tools im Projektstamm, um mit einem der folgenden Befehle die Verbindungszeichenfolge von Azure herunterzuladen:

  + Herunterladen aller Einstellungen aus einer vorhandenen Funktions-App:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Abrufen der Verbindungszeichenfolge für ein bestimmtes Speicherkonto:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Wenn Sie noch nicht in Azure angemeldet sind, werden Sie aufgefordert, sich anzumelden. Diese Befehle überschreiben alle vorhandenen Einstellungen in der Datei „local.settings.json“. 

## <a name="create-a-function"></a><a name="create-func"></a>Erstellen einer Funktion

Um eine Funktion zu erstellen, führen Sie den folgenden Befehl aus:

```
func new
```

In Version 3.x/2.x werden Sie beim Ausführen von `func new` aufgefordert, eine Vorlage in der Standardsprache der Funktions-App auszuwählen. Sie werden außerdem aufgefordert, einen Namen für die Funktion auszuwählen. Auch in Version 1.x werden Sie aufgefordert, die Sprache auszuwählen.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

Der Funktionscode wird mit dem angegebenen Funktionsnamen in einem Unterordner generiert, wie dies in der folgenden Warteschlangentriggerausgabe zu sehen ist:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Sie können diese Optionen im Befehl auch mit folgenden Argumenten angeben:

| Argument     | BESCHREIBUNG                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2.x und höher) Generiert dieselben C#-Skriptvorlagen (.csx), die in Version 1.x und im Portal verwendet wurden. |
| **`--language`**, **`-l`**| Die Vorlagenprogrammiersprache, z.B. C#, F# oder JavaScript. Diese Option ist in Version 1.x erforderlich. Verwenden Sie diese Option nicht ab Version 2.x, oder wählen Sie die der Workerruntime entsprechende Sprache. |
| **`--name`**, **`-n`** | Der Funktionsname. |
| **`--template`**, **`-t`** | Mit dem Befehl `func templates list` können Sie sich die vollständige Liste der verfügbaren Vorlagen für jede unterstützte Sprache anzeigen lassen.   |


Führen Sie z.B. zum Erstellen eines JavaScript-HTTP-Triggers in einem einzelnen Befehl Folgendes aus:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Führen Sie zum Erstellen einer durch die Warteschlange ausgelösten Funktion in einem einzelnen Befehl Folgendes aus:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Lokales Ausführen von Funktionen

Um ein Functions-Projekt auszuführen, führen Sie den Functions-Host aus. Der Host aktiviert Trigger für alle Funktionen im Projekt. Der Startbefehl variiert abhängig von der Projektsprache.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Dieser Befehl [muss in einer virtuellen Umgebung ausgeführt werden](./create-first-function-cli-python.md).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Version 1.x der Functions-Runtime erfordert den `host`-Befehl, wie im folgenden Beispiel gezeigt:
>
> ```
> func host start
> ```

`func start` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Das aktuelle Projekt wird vor der Ausführung nicht erstellt. Nur für dotnet-Projekte. Standardmäßig ist „false“ festgelegt. Wird für Version 1.x nicht unterstützt. |
| **`--cors-credentials`** | Lässt ursprungsübergreifende authentifizierte Anforderungen (d. h. Cookies und Authentifizierungsheader) zu. Wird für Version 1.x nicht unterstützt. |
| **`--cors`** | Eine durch Trennzeichen getrennte Liste der CORS-Ursprünge ohne Leerzeichen. |
| **`--language-worker`** | Argumente zum Konfigurieren des Spracharbeitsthreads. Beispielsweise können Sie das Debuggen für den Spracharbeiter aktivieren, indem Sie den [Debugport und andere erforderliche Argumente](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers) bereitstellen. Wird für Version 1.x nicht unterstützt. |
| **`--cert`** | Der Pfad zu einer PFX-Datei, die einen privaten Schlüssel enthält. Nur mit `--useHttps` verwendet. Wird für Version 1.x nicht unterstützt. |
| **`--password`** | Entweder das Kennwort oder eine Datei, die das Kennwort für eine PFX-Datei enthält. Nur mit `--cert` verwendet. Wird für Version 1.x nicht unterstützt. |
| **`--port`**, **`-p`** | Der lokale Port, auf dem gelauscht werden soll. Standardwert: 7071. |
| **`--pause-on-error`** | Vor Beenden des Prozesses für zusätzliche Eingabe anhalten. Wird nur beim Starten von Core Tools in einer integrierten Entwicklungsumgebung (IDE) verwendet.|
| **`--script-root`**, **`--prefix`** | Wird zum Angeben des Pfads zum Stamm der Funktions-App verwendet, die ausgeführt oder bereitgestellt werden soll. Diese Option wird für kompilierte Projekte verwendet, die Projektdateien in einem Unterordner generieren. Beispiel: Wenn Sie ein C#-Klassenbibliotheksprojekt erstellen, werden die Dateien „host.json“, „local.settings.json“ und „function.json“ in einem Unterordner des *Stammverzeichnisses* mit einem Pfad wie etwa dem folgenden generiert: `MyProject/bin/Debug/netstandard2.0`. Legen Sie in diesem Fall als Präfix `--script-root MyProject/bin/Debug/netstandard2.0` fest. Dies ist bei der Ausführung in Azure der Stamm der Funktions-App. |
| **`--timeout`**, **`-t`** | Das Timeout für den zu startenden Functions-Host in Sekunden. Standardwert: 20 Sekunden.|
| **`--useHttps`** | Erstellen Sie eine Bindung an `https://localhost:{port}` statt an `http://localhost:{port}`. Standardmäßig erstellt diese Option ein vertrauenswürdiges Zertifikat auf Ihrem Computer.|

Wenn der Functions-Host startet, gibt er die URL der HTTP-ausgelösten Funktionen aus:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Bei lokaler Ausführung wird die Autorisierung für HTTP-Endpunkte nicht erzwungen. Das bedeutet, dass alle lokalen HTTP-Anforderungen wie `authLevel = "anonymous"` behandelt werden. Weitere Informationen finden Sie im Artikel über [HTTP-Bindungen](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Übergeben von Testdaten an eine Funktion

Um Ihre Funktionen lokal zu testen, [starten Sie den Functions-Host](#start), und rufen Sie Endpunkte auf dem lokalen Server mithilfe von HTTP-Anforderungen auf. Der aufgerufene Endpunkt hängt vom Typ der Funktion ab.

>[!NOTE]
> Beispiele in diesem Thema verwenden das Tool cURL, um HTTP-Anforderungen vom Terminal oder einer Eingabeaufforderung zu senden. Sie können ein Tool Ihrer Wahl verwenden, um HTTP-Anforderungen an den lokalen Server zu senden. Das Tool cURL ist auf Linux-basierten Systemen sowie im Windows 10 Build 17063 und höher standardmäßig verfügbar. Unter einem älteren Windows müssen Sie das [cURL-Tool](https://curl.haxx.se/) erst herunterladen und installieren.

Allgemeinere Informationen zum Testen von Funktionen finden Sie unter [Strategien zum Testen Ihres Codes in Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Über HTTP und Webhook ausgelöste Funktionen

Sie rufen die folgenden Endpunkte auf, um mit HTTP und Webhooks ausgelöste Funktionen lokal auszuführen:

```
http://localhost:{port}/api/{function_name}
```

Achten Sie darauf, den gleichen Servernamen und Port zu verwenden, auf die der Functions-Host lauscht. Dies können Sie der generierten Ausgabe entnehmen, wenn der Functions-Host gestartet wird. Sie können diese URL mit jeder HTTP-Methode aufrufen, die vom Trigger unterstützt wird.

Der folgende cURL-Befehl löst die Schnellstart-Funktion `MyHttpTrigger` von einer GET-Anforderung aus, wobei der _name_-Parameter in der Abfragezeichenfolge übergeben wird.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Im folgenden Beispiel wird dieselbe Funktion über eine POST-Anforderung aufgerufen, die _name_ im Hauptteil der Anforderung übergibt:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Sie können GET-Anforderungen über einen Browser ausführen, indem Sie Daten in der Abfragezeichenfolge übergeben. Für alle anderen HTTP-Methoden müssen Sie cURL, Fiddler, Postman oder ein ähnliches HTTP-Testtool verwenden.

#### <a name="non-http-triggered-functions"></a>Nicht über HTTP ausgelöste Funktionen

Für alle Arten von Funktionen mit Ausnahme von HTTP-Triggern, Webhooks und Event Grid-Triggern können Sie Funktionen lokal testen, indem Sie einen Verwaltungsendpunkt aufrufen. Wenn Sie diesen Endpunkt mit einer HTTP POST-Anforderung auf dem lokalen Server aufrufen, wird die Funktion ausgelöst. 

Informationen zum lokalen Testen der durch Event Grid ausgelösten Funktionen finden Sie unter [Lokales Testen mit Viewer-Web-App](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Optional können Sie Testdaten im Hauptteil der POST-Anforderung an die Ausführung übergeben. Diese Funktionalität ähnelt der Registerkarte **Test** im Azure-Portal.

Sie rufen den folgenden Administratorendpunkt zum Auslösen von Nicht-HTTP-Funktionen auf:

```
http://localhost:{port}/admin/functions/{function_name}
```

Wenn Sie Testdaten an den Administratorendpunkt einer Funktion übergeben möchten, müssen Sie die Daten im Hauptteil einer POST-Anforderungsnachricht bereitstellen. Der Nachrichtentext muss das folgende JSON-Format aufweisen:

```JSON
{
    "input": "<trigger_input>"
}
```

Der Wert `<trigger_input>` enthält Daten in einem von der Funktion erwarteten Format. Das folgende cURL-Beispiel wird per POST an eine `QueueTriggerJS`-Funktion übergeben. In diesem Fall ist die Eingabe eine Zeichenfolge, die der in der Warteschlange erwarteten Nachricht entspricht.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Verwenden des Befehls `func run` (nur Version 1.x)

>[!IMPORTANT]
> Der Befehl `func run` wird nur in Version 1.x der Tools unterstützt. Weitere Informationen finden Sie im Thema [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md).

In Version 1.x können sie eine Funktion mithilfe von `func run <FunctionName>` auch direkt aufrufen und Eingabedaten für die Funktion bereitstellen. Dieser Befehl ähnelt der Ausführung einer Funktion mithilfe der Registerkarte **Test** im Azure-Portal.

`func run` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Inlineinhalt. |
| **`--debug`**, **`-d`** | Anfügen eines Debuggers an den Hostprozess vor dem Ausführen der Funktion.|
| **`--timeout`**, **`-t`** | Wartezeit (in Sekunden), bis der lokale Functions-Host bereit ist.|
| **`--file`**, **`-f`** | Der als Inhalt zu verwendende Dateiname.|
| **`--no-interactive`** | Fordert keine Eingabe an. Für Automatisierungsszenarien nützlich.|

Um z.B. eine HTTP-ausgelöste Funktion aufzurufen und Inhaltstext zu übergeben, führen Sie folgenden Befehl aus:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Veröffentlichen in Azure

Azure Functions Core Tools unterstützt zwei Arten von Bereitstellungen: die Bereitstellung der Funktionsprojektdateien direkt in Ihrer Funktions-App über [ZIP-Bereitstellung](functions-deployment-technologies.md#zip-deploy) und die [Bereitstellung eines benutzerdefinierten Docker-Containers](functions-deployment-technologies.md#docker-container). Sie müssen bereits [eine Funktions-App im Azure-Abonnement erstellt haben](functions-cli-samples.md#create), für die Sie den Code bereitstellen. Projekte, für die eine Kompilierung erforderlich ist, müssen so erstellt werden, dass die Binärdateien bereitgestellt werden können.

>[!IMPORTANT]
>Die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) muss lokal installiert sein, um von Core Tools aus Veröffentlichungsvorgänge für Azure durchführen zu können.  

Ein Projektordner kann sprachspezifische Dateien und Verzeichnisse enthalten, die nicht veröffentlicht werden sollen. Ausgeschlossene Elemente werden in einer .funcignore-Datei im Stammprojektordner aufgelistet.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Bereitstellen von Projektdateien

Um Ihren lokalen Code in einer Funktions-App in Azure zu veröffentlichen, verwenden Sie den `publish`-Befehl:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java verwendet Maven, um das lokale Projekt in Azure zu veröffentlichen. Verwenden Sie den folgenden Befehl zum Veröffentlichen in Azure: `mvn azure-functions:deploy`. Azure-Ressourcen werden während der ersten Bereitstellung erstellt.

Mit diesem Befehl wird in eine vorhandene Funktionen-App in Azure veröffentlicht. Sie erhalten eine Fehlermeldung, wenn Sie versuchen, an einen `<FunctionAppName>` zu veröffentlichen, der in Ihrem Abonnement nicht vorhanden ist. Informationen zum Erstellen einer Funktions-App über die Eingabeaufforderung oder ein Terminalfenster mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Erstellen einer Funktions-App für die serverlose Ausführung](./scripts/functions-cli-create-serverless.md). Dieser Befehl verwendet standardmäßig die [Remotebuildfunktion](functions-deployment-technologies.md#remote-build) und stellt Ihre App für die [Ausführung über das Bereitstellungspaket](run-functions-from-deployment-package.md) bereit. Dieser empfohlene Bereitstellungsmodus kann auf Wunsch mithilfe der Option `--nozip` deaktiviert werden.

>[!IMPORTANT]
> Wenn Sie eine Funktions-App im Azure-Portal erstellen, verwendet sie automatisch Version 3.x der Functions-Runtime. Damit die Funktions-App Version 1.x der Laufzeit verwendet, befolgen Sie die Anweisungen unter [Run on version 1.x](functions-versions.md#creating-1x-apps) (Ausführen unter Version 1.x).
> Sie können die Runtimeversion für eine Funktions-App, die über Funktionen verfügt, nicht ändern.

Die folgenden Veröffentlichungsoptionen gelten für alle Versionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Einstellungen zur Veröffentlichung in Azure in „local.settings.json“. Wenn die Einstellung bereits vorhanden ist, werden Sie gefragt, ob sie überschrieben werden soll. Wenn Sie den Microsoft Azure-Speicheremulator verwenden, ändern Sie zunächst die App-Einstellung in eine [tatsächliche Speicherverbindung](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Unterdrückt die Aufforderung zum Überschreiben von App-Einstellungen bei Verwendung von `--publish-local-settings -i`.|

Die folgenden Veröffentlichungsoptionen werden nur ab Version 2.x unterstützt:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Veröffentlicht nur die Einstellungen, der Inhalt wird übersprungen. Standard ist die Eingabeaufforderung. |
|**`--list-ignored-files`** | Zeigt eine Liste mit Dateien an, die bei der Veröffentlichung ignoriert werden (basierend auf der Datei vom Typ „.funcignore“). |
| **`--list-included-files`** | Zeigt eine Liste mit Dateien an, die veröffentlicht werden (basierend auf der Datei vom Typ „.funcignore“). |
| **`--nozip`** | Deaktiviert den Standardmodus `Run-From-Package`. |
| **`--build-native-deps`** | Überspringt das Generieren des Ordners „.wheels“ beim Veröffentlichen von Python-Funktions-Apps. |
| **`--build`**, **`-b`** | Führt beim Bereitstellen in einer Linux-Funktions-App eine Buildaktion durch. Akzeptiert: `remote` und `local`. |
| **`--additional-packages`** | Liste der zu installierenden Pakete beim Erstellen nativer Abhängigkeiten. Beispiel: `python3-dev libevent-dev`. |
| **`--force`** | In bestimmten Szenarien wird die Überprüfung vor der Veröffentlichung ignoriert. |
| **`--csx`** | Veröffentlicht ein C#-Skriptprojekt (.csx). |
| **`--no-build`** | Das Projekt wird während der Veröffentlichung nicht erstellt. Für Python wird `pip install` nicht ausgeführt. |
| **`--dotnet-cli-params`** | Beim Veröffentlichen kompilierter C#-Funktionen (.csproj) rufen die Core Tools „dotnet build --output bin/publish“ auf. Alle daran übergebenen Parameter werden an die Befehlszeile angefügt. |

### <a name="deploy-custom-container"></a>Bereitstellen benutzerdefinierter Container

Mit Azure Functions können Sie Ihr Funktionsprojekt in einem [benutzerdefinierten Docker-Container](functions-deployment-technologies.md#docker-container) bereitstellen. Weitere Informationen finden Sie unter [Erstellen einer Funktion in Linux mit einem benutzerdefinierten Image (Vorschau)](functions-create-function-linux-custom-image.md). Benutzerdefinierte Container müssen ein Dockerfile enthalten. Um eine App mit einer Docker-Datei zu erstellen, verwenden Sie die Option „--dockerfile“ für `func init`.

```
func deploy
```

Die folgenden Optionen für die Bereitstellung benutzerdefinierter Container sind verfügbar:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Der Name einer Docker-Registrierung, bei der der aktuelle Benutzer angemeldet ist |
| **`--platform`** | Hostingplattform für die Funktions-App Gültige Optionen: `kubernetes` |
| **`--name`** | Name der Funktions-App |
| **`--max`**  | Legt optional die maximale Anzahl von Funktions-App-Instanzen fest, in denen die Bereitstellung erfolgen soll. |
| **`--min`**  | Legt optional die Mindestanzahl von Funktions-App-Instanzen fest, in denen die Bereitstellung erfolgen soll. |
| **`--config`** | Legt eine optionale Bereitstellungskonfigurationsdatei fest. |

## <a name="monitoring-functions"></a>Überwachen von Funktionen

Die empfohlene Methode zum Überwachen der Ausführung Ihrer Funktionen ist die Integration in Azure Application Insights. Sie können auch Ausführungsprotokolle an Ihren lokalen Computer streamen. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights-Integration

Application Insights-Integration sollte standardmäßig aktiviert sein, wenn Sie Ihre Funktions-App im Azure-Portal erstellen. Wenn Ihre Funktions-App aus irgendeinem Grund nicht mit einer Application Insights-Instanz verbunden ist, lässt sich diese Integration einfach über das Azure-Portal durchführen. Weitere Informationen finden Sie unter [Aktivieren der Application Insights-Integration](configure-monitoring.md#enable-application-insights-integration).

### <a name="enable-streaming-logs"></a>Aktivieren von Streamingprotokollen

Sie können in einer Befehlszeilensitzung auf Ihrem lokalen Computer einen Stream von Protokolldateien anzeigen, die von Ihren Funktionen generiert werden. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Diese Art von Streamingprotokollen erfordert, dass die Application Insights-Integration für Ihre Funktions-App aktiviert ist.   


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Azure Functions mithilfe Azure Functions Core Tools entwickeln, testen und veröffentlichen: [Microsoft Learn-Modul](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/). Azure Functions Core Tools ist [Open Source und wird auf GitHub gehostet](https://github.com/azure/azure-functions-cli).  
Um einen Fehler zu melden oder ein Feature anzufordern, [öffnen Sie ein GitHub-Problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Erweiterungsbundles]: functions-bindings-register.md#extension-bundles
