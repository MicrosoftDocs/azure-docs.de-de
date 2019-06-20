---
title: Arbeiten mit Azure Functions Core Tools | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Funktionen über die Eingabeaufforderung oder das Terminal auf dem lokalen Computer codieren und testen, bevor Sie sie in Azure Functions ausführen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 3c8d64f34f01e4339b27bdeba455fac143ad53ff
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241169"
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

Es sind zwei Versionen von Azure Functions Core Tools verfügbar. Welche Version Sie verwenden, hängt von der lokalen Entwicklungsumgebung, der [Sprachauswahl](supported-languages.md) und der erforderlichen Unterstützung ab:

+ Version 1.x: unterstützt Version 1.x der Laufzeit. Diese Version der Tools wird nur auf Windows-Computern unterstützt und wird über ein [npm-Paket](https://docs.npmjs.com/getting-started/what-is-npm) installiert. Mit dieser Version können Sie Funktionen in experimentellen Sprachen erstellen, die nicht offiziell unterstützt werden. Weitere Informationen finden Sie unter [In Azure Functions unterstützte Sprachen](supported-languages.md).

+ [Version 2.x](#v2): Unterstützt [Version 2.x der Runtime](functions-versions.md). Diese Version unterstützt [Windows](#windows-npm), [macOS](#brew) und [Linux](#linux). Sie verwendet plattformspezifische Paket-Manager oder npm für die Installation.

Sofern nicht anders angegeben, gelten die Beispiele in diesem Artikel für Version 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

[Azure Functions Core Tools] umfasst eine Version der gleichen Runtime, auf der die Azure Functions-Runtime basiert, die Sie auf dem lokalen Entwicklungscomputer ausführen können. Zudem sind Befehle zum Erstellen von Funktionen, Herstellen einer Verbindung mit Azure und Bereitstellen von Functions-Projekten enthalten.

### <a name="v2"></a>Version 2.x

Die Tools der Version 2.x verwenden die Azure Functions-Laufzeit 2.x, die auf .NET Core basiert. Diese Version wird auf allen Plattformen unterstützt, die von .NET Core 2.x unterstützt werden, einschließlich [Windows](#windows-npm), [macOS](#brew) und [Linux](#linux). 

> [!IMPORTANT]
> Sie können auf das Installieren des .NET Core 2.x SDK verzichten, wenn Sie [Erweiterungsbundles] verwenden.

#### <a name="windows-npm"></a>Windows

Die folgenden Schritte verwenden npm zum Installieren der Core Tools unter Windows. Sie können auch [Chocolatey](https://chocolatey.org/) verwenden. Weitere Informationen finden Sie in der [Infodatei zu den Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installieren Sie [Node.js] (enthält npm). Für Version 2.x der Tools werden nur Node.js 8.5 und höhere Versionen unterstützt.

1. Installieren Sie das Core Tools-Paket:

    ```bash
    npm install -g azure-functions-core-tools
    ```
1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles] zu verwenden, installieren Sie das [.NET Core 2.x SDK für Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>MacOS mit Homebrew

Die folgenden Schritte verwenden Homebrew zum Installieren der Core Tools unter macOS.

1. Installieren Sie [Homebrew](https://brew.sh/), sofern dies noch nicht geschehen ist.

1. Installieren Sie das Core Tools-Paket:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```
1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles] zu verwenden, installieren Sie das [.NET Core 2.x SDK für macOS](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a> Linux (Ubuntu/Debian) mit APT

Die folgenden Schritte verwenden [APT](https://wiki.debian.org/Apt) zum Installieren der Core Tools unter Ihrer Ubuntu/Debian Linux-Distribution. Informationen zu anderen Linux-Distributionen finden Sie in der [Infodatei zu den Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Registrieren Sie den Microsoft Product Key als vertrauenswürdig:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Stellen Sie anhand der folgenden Tabelle sicher, dass auf Ihrem Ubuntu-Server eine geeignete Version ausgeführt wird. Zum Hinzufügen der Apt-Quelle führen Sie Folgendes aus:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux-Distribution | Version |
    | --------------- | ----------- |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Installieren Sie das Core Tools-Paket:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```
1. Wenn Sie nicht beabsichtigen, [Erweiterungsbundles] zu verwenden, installieren Sie das [.NET Core 2.x SDK für Linux](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Functions-Projekts

Ein Functions-Projektverzeichnis enthält die Dateien [host.json](functions-host-json.md) und [local.settings.json](#local-settings-file) sowie Unterordner, die den Code für einzelne Funktionen enthalten. Dieses Verzeichnis ist das Äquivalent zu einer Funktions-App in Azure. Weitere Informationen zur Azure Functions-Ordnerstruktur finden Sie unter [Azure Functions: Entwicklerhandbuch](functions-reference.md#folder-structure).

In Version 2.x müssen Sie eine Standardsprache für das Projekt auswählen, wenn es initialisiert wird. Zudem werden für alle hinzugefügten Funktionen Vorlagen für die Standardsprache verwendet. In Version 1.x geben Sie die Sprache bei jedem Erstellen einer Funktion an.

Führen Sie im Terminalfenster oder über eine Eingabeaufforderung den folgenden Befehl aus, um das Projekt und ein lokales Git-Repository zu erstellen:

```bash
func init MyFunctionProj
```

Wenn Sie einen Projektnamen angeben, wird ein neuer Ordner mit diesem Namen erstellt und initialisiert. Andernfalls wird der aktuelle Ordner initialisiert.  
In Version 2.x müssen Sie beim Ausführen des Befehls eine Runtime für das Projekt auswählen. Wenn Sie JavaScript-Funktionen entwickeln möchten, wählen Sie **node** aus:

```output
Select a worker runtime:
dotnet
node
```

Verwenden Sie die NACH-OBEN- oder NACH-UNTEN-TASTE, um eine Sprache auszuwählen, und drücken Sie dann die EINGABETASTE. Die Ausgabe ähnelt dem folgenden Beispiel für ein JavaScript-Projekt:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` unterstützt die folgenden Optionen, die nur unter Version 2.x verfügbar sind, sofern nicht anders angegeben:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Initialisiert ein C#-Skriptprojekt (.csx). In nachfolgenden Befehlen müssen Sie `--csx` angeben. |
| **`--docker`** | Erstellt ein Dockerfile für einen Container mithilfe eines Basisimages, das auf dem ausgewählten Wert für `--worker-runtime` basiert. Verwenden Sie diese Option, wenn Sie die Veröffentlichung in einem benutzerdefinierten Linux-Container planen. |
| **`--force`** | Initialisiert das Projekt, auch wenn es bereits Dateien enthält. Diese Einstellung überschreibt vorhandene Dateien mit dem gleichen Namen. Andere Dateien im Projektordner sind nicht betroffen. |
| **`--no-source-control -n`** | Verhindert die standardmäßige Erstellung eines Git-Repositorys in Version 1.x. In Version 2.x wird das Git-Repository nicht standardmäßig erstellt. |
| **`--source-control`** | Steuert, ob ein Git-Repository erstellt wird. Standardmäßig wird kein Repository erstellt. Im Fall von `true` wird ein Repository erstellt. |
| **`--worker-runtime`** | Legt die Runtime der Sprache für das Projekt fest. Unterstützte Werte sind `dotnet`, `node` (JavaScript), `java` und `python`. Wird kein Wert festgelegt, werden Sie während der Initialisierung zur Auswahl der Runtime aufgefordert. |

> [!IMPORTANT]
> In Version 2.x der Core-Tools werden Funktions-App-Projekte für die .NET-Runtime als [C#-Klassenprojekte](functions-dotnet-class-library.md) (.csproj) erstellt. Diese C#-Projekte, die mit Visual Studio oder Visual Studio Code verwendet werden können, werden während der Tests und beim Veröffentlichen in Azure kompiliert. Wenn Sie stattdessen dieselben C#-Skriptdateien (.csx) erstellen und verwenden möchten, die in Version 1.x und im Portal erstellt wurden, müssen Sie die `--csx`-Parameter beim Erstellen und Bereitstellen von Funktionen einschließen.

## <a name="register-extensions"></a>Registrieren von Erweiterungen

In Version 2.x der Azure Functions Runtime müssen Sie die in Ihrer Funktions-App verwendeten Bindungserweiterungen (Bindungstypen) explizit registrieren.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Weitere Informationen finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](./functions-bindings-expressions-patterns.md).

## <a name="local-settings-file"></a>Datei für lokale Einstellungen

Die Datei „local.settings.json“ speichert App-Einstellungen, Verbindungszeichenfolgen und Einstellungen für Azure Functions Core Tools. Einstellungen in der Datei „local.settings.json“ werden nur bei der lokalen Ausführung von Functions-Tools verwendet. Standardmäßig werden diese Einstellungen nicht automatisch migriert, wenn das Projekt in Azure veröffentlicht wird. Verwenden Sie den Switch `--publish-local-settings` [bei der Veröffentlichung](#publish), um sicherzustellen, dass diese Einstellungen zur Funktionen-App in Azure hinzugefügt werden. Beachten Sie, dass Werte in **ConnectionStrings** niemals veröffentlicht werden. Die Datei hat die folgende Struktur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Einstellung      | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Wenn diese Einstellung auf `true` festgelegt wird, werden alle Werte mithilfe eines Schlüssels des lokalen Computers verschlüsselt. Wird mit `func settings`-Befehlen verwendet. Der Standardwert ist `false`. |
| **`Values`** | Eine Sammlung von Anwendungseinstellungen und Verbindungszeichenfolgen, die bei der lokalen Ausführung verwendet werden. Diese Werte entsprechen App-Einstellungen in Ihrer Funktions-App in Azure, z. B. [`AzureWebJobsStorage`]. Viele Trigger und Bindungen verfügen über eine Eigenschaft, die auf eine App-Einstellung für die Verbindungszeichenfolge verweist, z. B. `Connection` für den [Blob Storage-Trigger](functions-bindings-storage-blob.md#trigger---configuration). Für solche Eigenschaften muss eine Anwendungseinstellung im Array `Values` definiert sein. <br/>[`AzureWebJobsStorage`] ist eine erforderliche App-Einstellung für andere Trigger als HTTP. <br/>Für Version 2.x der Functions-Runtime ist die Einstellung [`FUNCTIONS_WORKER_RUNTIME`] erforderlich, die mit Core Tools für Ihr Projekt generiert wird. <br/> Wenn der [Azure-Speicheremulator](../storage/common/storage-use-emulator.md) lokal installiert ist, können Sie [`AzureWebJobsStorage`] auf `UseDevelopmentStorage=true` festlegen, und Core Tools verwendet den Emulator. Dies ist während der Entwicklung hilfreich, doch sollten Sie vor der Bereitstellung einen Test mit einer tatsächlichen Speicherverbindung durchführen. |
| **`Host`** | Die Einstellungen in diesem Abschnitt passen den Hostprozess von Functions bei der lokalen Ausführung an. |
| **`LocalHttpPort`** | Legt den Standardport fest, der bei der Ausführung des lokalen Functions-Host verwendet wird (`func host start` und `func run`). Die Befehlszeilenoption `--port` hat Vorrang vor diesem Wert. |
| **`CORS`** | Definiert die für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) zulässigen Ursprünge. Ursprünge werden als durch Trennzeichen getrennte Liste ohne Leerzeichen bereitgestellt. Den Platzhalterwert (\*) wird unterstützt, wodurch Anforderungen von einem beliebigen Ursprung zulässig sind. |
| **`CORSCredentials`** |  Legen Sie diese Einstellung auf „true“ fest, um `withCredentials`-Anforderungen zuzulassen. |
| **`ConnectionStrings`** | Verwenden Sie diese Sammlung nicht für die Verbindungszeichenfolgen, die von Ihren Funktionsbindungen verwendet werden. Diese Sammlung wird nur von Frameworks verwendet, die Verbindungszeichenfolgen üblicherweise aus dem Abschnitt `ConnectionStrings` einer Konfigurationsdatei abrufen, z. B. [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Verbindungszeichenfolgen in diesem Objekt werden der Umgebung mit dem Anbietertyp [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) hinzugefügt. Elemente in dieser Sammlung werden nicht mit anderen App-Einstellungen in Azure veröffentlicht. Sie müssen diese Werte explizit zur Sammlung `Connection strings` in den Einstellungen Ihrer Funktions-App hinzufügen. Bei der Erstellung einer [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)-Klasse in Ihrem Funktionscode sollten Sie den Verbindungszeichenfolgenwert zusammen mit den anderen Verbindungen in den **Anwendungseinstellungen** im Portal speichern. |

Die Werte für Funktions-App-Einstellungen können auch im Code als Umgebungsvariablen gelesen werden. Weitere Informationen finden Sie im Abschnitt „Umgebungsvariablen“ dieser sprachspezifischen Referenzthemen:

* [Vorkompilierter C#-Code](functions-dotnet-class-library.md#environment-variables)
* [C#-Skript (.csx)](functions-reference-csharp.md#environment-variables)
* [F#-Skript (.fsx)](functions-reference-fsharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Wenn keine gültige Speicherverbindungszeichenfolge für [`AzureWebJobsStorage`] festgelegt ist und der Emulator nicht verwendet wird, wird die folgende Fehlermeldung angezeigt:

> Missing value for AzureWebJobsStorage in local.settings.json (Fehlender Wert für AzureWebJobsStorage in local.settings.json). Dies ist für alle Nicht-HTTP-Trigger erforderlich. Sie können „func azure functionapp fetch-app-settings \<functionAppName\>“ ausführen oder eine Verbindungszeichenfolge in „local.settings.json“ angeben.

### <a name="get-your-storage-connection-strings"></a>Abrufen der Speicherverbindungszeichenfolgen

Auch wenn der Speicheremulator für die Entwicklung verwendet wird, empfiehlt es sich, einen Test mit einer tatsächlichen Speicherverbindung durchzuführen. Wenn Sie bereits [ein Speicherkonto erstellt haben](../storage/common/storage-create-storage-account.md), können Sie eine gültige Speicherverbindungszeichenfolge anhand einer der folgenden Methoden abrufen:

+ Im [Azure-Portal]. Navigieren Sie zu Ihrem Speicherkonto, wählen Sie **Zugriffsschlüssel** unter **Einstellungen** aus, und kopieren Sie dann einen der Werte für **Verbindungszeichenfolge**.

  ![Kopieren der Verbindungszeichenfolge im Azure-Portal](./media/functions-run-local/copy-storage-connection-portal.png)

+ Verwenden Sie [Azure Storage-Explorer](https://storageexplorer.com/), um eine Verbindung mit Ihrem Azure-Konto herzustellen. Erweitern Sie im **Explorer** Ihr Abonnement, wählen Sie Ihr Speicherkonto aus, und kopieren Sie die primäre oder sekundäre Verbindungszeichenfolge.

  ![Kopieren der Verbindungszeichenfolge im Storage-Explorer](./media/functions-run-local/storage-explorer.png)

+ Verwenden Sie Core Tools zum Herunterladen der Verbindungszeichenfolge aus Azure mit einem der folgenden Befehle:

  + Herunterladen aller Einstellungen aus einer vorhandenen Funktions-App:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Abrufen der Verbindungszeichenfolge für ein bestimmtes Speicherkonto:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Wenn Sie noch nicht in Azure angemeldet sind, werden Sie aufgefordert, sich anzumelden.

## <a name="create-func"></a>Erstellen einer Funktion

Um eine Funktion zu erstellen, führen Sie den folgenden Befehl aus:

```bash
func new
```

In Version 2.x werden Sie beim Ausführen von `func new` aufgefordert, eine Vorlage in der Standardsprache der Funktions-App auszuwählen. Dann werden Sie außerdem aufgefordert, einen Namen für die Funktion auszuwählen. Auch in Version 1.x werden Sie aufgefordert, die Sprache auszuwählen.

```output
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
```

Der Funktionscode wird mit dem angegebenen Funktionsnamen in einem Unterordner generiert, wie dies in der folgenden Warteschlangentriggerausgabe zu sehen ist:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Sie können diese Optionen im Befehl auch mit folgenden Argumenten angeben:

| Argument     | BESCHREIBUNG                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2.x) Generiert dieselben C#-Skriptvorlagen (.csx), die in Version 1.x und im Portal verwendet wurden. |
| **`--language -l`**| Die Vorlagenprogrammiersprache, z.B. C#, F# oder JavaScript. Diese Option ist in Version 1.x erforderlich. Verwenden Sie diese Option nicht in Version 2.x, oder wählen Sie eine für die Workerruntime geeignete Sprache. |
| **`--name -n`** | Der Funktionsname. |
| **`--template -t`** | Mit dem Befehl `func templates list` können Sie sich die vollständige Liste der verfügbaren Vorlagen für jede unterstützte Sprache anzeigen lassen.   |

Führen Sie z.B. zum Erstellen eines JavaScript-HTTP-Triggers in einem einzelnen Befehl Folgendes aus:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Führen Sie zum Erstellen einer durch die Warteschlange ausgelösten Funktion in einem einzelnen Befehl Folgendes aus:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Lokales Ausführen von Funktionen

Um ein Functions-Projekt auszuführen, führen Sie den Functions-Host aus. Der Host aktiviert Trigger für alle Funktionen im Projekt:

```bash
func host start
```

Der Befehl `host` ist nur in Version 1.x erforderlich.

`func host start` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Das aktuelle Projekt wird vor der Ausführung nicht erstellt. Nur für dotnet-Projekte. Standardmäßig ist „false“ festgelegt. Nur Version 2.x. |
| **`--cert`** | Der Pfad zu einer PFX-Datei, die einen privaten Schlüssel enthält. Nur mit `--useHttps` verwendet. Nur Version 2.x. |
| **`--cors-credentials`** | Ursprungsübergreifende authentifizierte Anforderungen (d.h. Cookies und der Authentifizierungsheader) sind nur für Version 2.x zugelassen. |
| **`--cors`** | Eine durch Trennzeichen getrennte Liste der CORS-Ursprünge ohne Leerzeichen. |
| **`--language-worker`** | Argumente zum Konfigurieren des Spracharbeitsthreads. Nur Version 2.x. |
| **`--nodeDebugPort -n`** | Der Port, den der Knotendebugger verwendet. Standardwert: Ein Wert aus „launch.json“ oder 5858. Nur Version 1.x. |
| **`--password`** | Entweder das Kennwort oder eine Datei, die das Kennwort für eine PFX-Datei enthält. Nur mit `--cert` verwendet. Nur Version 2.x. |
| **`--port -p`** | Der lokale Port, auf dem gelauscht werden soll. Standardwert: 7071. |
| **`--pause-on-error`** | Vor Beenden des Prozesses für zusätzliche Eingabe anhalten. Wird nur beim Starten von Core Tools in einer integrierten Entwicklungsumgebung (IDE) verwendet.|
| **`--script-root --prefix`** | Wird zum Angeben des Pfads zum Stamm der Funktions-App verwendet, die ausgeführt oder bereitgestellt werden soll. Diese Option wird für kompilierte Projekte verwendet, die Projektdateien in einem Unterordner generieren. Beispiel: Wenn Sie ein C#-Klassenbibliotheksprojekt erstellen, werden die Dateien „host.json“, „local.settings.json“ und „function.json“ in einem Unterordner des *Stammverzeichnisses* mit einem Pfad wie etwa dem folgenden generiert: `MyProject/bin/Debug/netstandard2.0`. Legen Sie in diesem Fall als Präfix `--script-root MyProject/bin/Debug/netstandard2.0` fest. Dies ist bei der Ausführung in Azure der Stamm der Funktions-App. |
| **`--timeout -t`** | Das Timeout für den zu startenden Functions-Host in Sekunden. Standardwert: 20 Sekunden.|
| **`--useHttps`** | Erstellen Sie eine Bindung an `https://localhost:{port}` statt an `http://localhost:{port}`. Standardmäßig erstellt diese Option ein vertrauenswürdiges Zertifikat auf Ihrem Computer.|

Bei einem C#-Klassenbibliotheksprojekt (CSPROJ) müssen Sie die Option `--build` einschließen, um die Bibliotheks-DLL zu generieren.

Wenn der Functions-Host startet, gibt er die URL der HTTP-ausgelösten Funktionen aus:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Bei lokaler Ausführung wird die Authentifizierung für HTTP-Endpunkte nicht erzwungen. Das bedeutet, dass alle lokalen HTTP-Anforderungen wie `authLevel = "anonymous"` behandelt werden. Weitere Informationen finden Sie im Artikel über [HTTP-Bindungen](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Übergeben von Testdaten an eine Funktion

Um Ihre Funktionen lokal zu testen, [starten Sie den Functions-Host](#start), und rufen Sie Endpunkte auf dem lokalen Server mithilfe von HTTP-Anforderungen auf. Der aufgerufene Endpunkt hängt vom Typ der Funktion ab.

>[!NOTE]
> Beispiele in diesem Thema verwenden das Tool cURL, um HTTP-Anforderungen vom Terminal oder einer Eingabeaufforderung zu senden. Sie können ein Tool Ihrer Wahl verwenden, um HTTP-Anforderungen an den lokalen Server zu senden. Das Tool cURL ist auf Linux-basierten Systemen standardmäßig verfügbar. Unter Windows müssen Sie das [cURL-Tool](https://curl.haxx.se/) erst herunterladen und installieren.

Allgemeinere Informationen zum Testen von Funktionen finden Sie unter [Strategien zum Testen Ihres Codes in Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Über HTTP und Webhook ausgelöste Funktionen

Sie rufen die folgenden Endpunkte auf, um mit HTTP und Webhooks ausgelöste Funktionen lokal auszuführen:

    http://localhost:{port}/api/{function_name}

Achten Sie darauf, den gleichen Servernamen und Port zu verwenden, auf die der Functions-Host lauscht. Dies können Sie der generierten Ausgabe entnehmen, wenn der Functions-Host gestartet wird. Sie können diese URL mit jeder HTTP-Methode aufrufen, die vom Trigger unterstützt wird.

Der folgende cURL-Befehl löst die Schnellstart-Funktion `MyHttpTrigger` von einer GET-Anforderung aus, wobei der _name_-Parameter in der Abfragezeichenfolge übergeben wird.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Im folgenden Beispiel wird dieselbe Funktion über eine POST-Anforderung aufgerufen, die _name_ im Hauptteil der Anforderung übergibt:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Sie können GET-Anforderungen über einen Browser ausführen, indem Sie Daten in der Abfragezeichenfolge übergeben. Für alle anderen HTTP-Methoden müssen Sie cURL, Fiddler, Postman oder ein ähnliches HTTP-Testtool verwenden.

#### <a name="non-http-triggered-functions"></a>Nicht über HTTP ausgelöste Funktionen

Für alle Arten von Funktionen mit Ausnahme von HTTP-Triggern und Webhooks können Sie Funktionen lokal testen, indem Sie einen Verwaltungsendpunkt aufrufen. Wenn Sie diesen Endpunkt mit einer HTTP POST-Anforderung auf dem lokalen Server aufrufen, wird die Funktion ausgelöst. Optional können Sie Testdaten im Hauptteil der POST-Anforderung an die Ausführung übergeben. Diese Funktionalität ähnelt der Registerkarte **Test** im Azure-Portal.

Sie rufen den folgenden Administratorendpunkt zum Auslösen von Nicht-HTTP-Funktionen auf:

    http://localhost:{port}/admin/functions/{function_name}

Wenn Sie Testdaten an den Administratorendpunkt einer Funktion übergeben möchten, müssen Sie die Daten im Hauptteil einer POST-Anforderungsnachricht bereitstellen. Der Nachrichtentext muss das folgende JSON-Format aufweisen:

```JSON
{
    "input": "<trigger_input>"
}
```

Der Wert `<trigger_input>` enthält Daten in einem von der Funktion erwarteten Format. Das folgende cURL-Beispiel wird per POST an eine `QueueTriggerJS`-Funktion übergeben. In diesem Fall ist die Eingabe eine Zeichenfolge, die der in der Warteschlange erwarteten Nachricht entspricht.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Verwenden des Befehls `func run` in Version 1.x

>[!IMPORTANT]
> Der Befehl `func run` wird in Version 2.x der Tools nicht unterstützt. Weitere Informationen finden Sie im Thema [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md).

Sie können eine Funktion auch direkt aufrufen, indem Sie `func run <FunctionName>` verwenden, und Eingabedaten für die Funktion bereitstellen. Dieser Befehl ähnelt der Ausführung einer Funktion mithilfe der Registerkarte **Test** im Azure-Portal.

`func run` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Inlineinhalt. |
| **`--debug -d`** | Anfügen eines Debuggers an den Hostprozess vor dem Ausführen der Funktion.|
| **`--timeout -t`** | Wartezeit (in Sekunden), bis der lokale Functions-Host bereit ist.|
| **`--file -f`** | Der als Inhalt zu verwendende Dateiname.|
| **`--no-interactive`** | Fordert keine Eingabe an. Für Automatisierungsszenarien nützlich.|

Um z.B. eine HTTP-ausgelöste Funktion aufzurufen und Inhaltstext zu übergeben, führen Sie folgenden Befehl aus:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Veröffentlichen in Azure

Core Tools unterstützt zwei Arten von Bereitstellungen: die Bereitstellung der Funktionsprojektdateien direkt in Ihrer Funktions-App und die Bereitstellung eines benutzerdefinierten Linux-Containers (nur in Version 2.x). Sie müssen bereits [eine Funktions-App im Azure-Abonnement erstellt haben](functions-cli-samples.md#create).

In Version 2.x müssen Sie [Ihre Erweiterungen im Projekt registriert](#register-extensions) haben, bevor Sie die Veröffentlichung durchführen. Projekte, für die eine Kompilierung erforderlich ist, müssen so erstellt werden, dass die Binärdateien bereitgestellt werden können.

### <a name="project-file-deployment"></a>Bereitstellung der Projektdatei

Bei der am häufigsten verwendeten Bereitstellungsmethode wird Core Tools zum Verpacken Ihres Funktions-App-Projekts, der Binärdateien und Abhängigkeiten sowie zum Bereitstellen des Pakets in Ihrer Funktions-App verwendet. Sie können [Ihre Funktionen optional direkt aus dem Bereitstellungspaket heraus ausführen](run-functions-from-deployment-package.md).

Um ein Functions-Projekt in einer Funktions-App in Azure zu veröffentlichen, verwenden Sie den `publish`-Befehl:

```bash
func azure functionapp publish <FunctionAppName>
```

Mit diesem Befehl wird in eine vorhandene Funktionen-App in Azure veröffentlicht. Es tritt ein Fehler auf, wenn der `<FunctionAppName>` in Ihrem Abonnement nicht vorhanden ist. Informationen zum Erstellen einer Funktions-App über die Eingabeaufforderung oder ein Terminalfenster mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Erstellen einer Funktions-App für die serverlose Ausführung](./scripts/functions-cli-create-serverless.md).

Der `publish`-Befehl lädt den Inhalt des Functions-Projektverzeichnisses hoch. Wenn Sie Dateien lokal löschen, werden sie mit dem Befehl `publish` nicht aus Azure gelöscht. Sie können Dateien in Azure löschen, indem Sie das [Kudu-Tool](functions-how-to-use-azure-function-app-settings.md#kudu) im [Azure-Portal] verwenden.

>[!IMPORTANT]
> Wenn Sie eine Funktions-App im Azure-Portal erstellen, verwendet sie standardmäßig Version 2.x der Functions-Laufzeit. Damit die Funktions-App Version 1.x der Laufzeit verwendet, befolgen Sie die Anweisungen unter [Run on version 1.x](functions-versions.md#creating-1x-apps) (Ausführen unter Version 1.x).
> Sie können die Runtimeversion für eine Funktions-App, die über Funktionen verfügt, nicht ändern.

Die folgenden Optionen für die Projektveröffentlichung gelten für beide Versionen (1.x und 2.x):

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Einstellungen zur Veröffentlichung in Azure in „local.settings.json“. Wenn die Einstellung bereits vorhanden ist, werden Sie gefragt, ob sie überschrieben werden soll. Wenn Sie den Speicheremulator verwenden, ändern Sie die App-Einstellung in eine [tatsächliche Speicherverbindung](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Unterdrückt die Aufforderung zum Überschreiben von App-Einstellungen bei Verwendung von `--publish-local-settings -i`.|

Die folgenden Optionen für die Projektveröffentlichung werden nur in Version 2.x unterstützt:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Veröffentlicht nur die Einstellungen, der Inhalt wird übersprungen. Standard ist die Eingabeaufforderung. |
|**`--list-ignored-files`** | Zeigt eine Liste mit Dateien an, die bei der Veröffentlichung ignoriert werden (basierend auf der Datei vom Typ „.funcignore“). |
| **`--list-included-files`** | Zeigt eine Liste mit Dateien an, die veröffentlicht werden (basierend auf der Datei vom Typ „.funcignore“). |
| **`--nozip`** | Deaktiviert den Standardmodus `Run-From-Zip`. |
| **`--build-native-deps`** | Überspringt das Generieren des Ordners „.wheels“ beim Veröffentlichen von Python-Funktions-Apps. |
| **`--additional-packages`** | Liste der zu installierenden Pakete beim Erstellen nativer Abhängigkeiten. Beispiel: `python3-dev libevent-dev`. |
| **`--force`** | In bestimmten Szenarien wird die Überprüfung vor der Veröffentlichung ignoriert. |
| **`--csx`** | Veröffentlicht ein C#-Skriptprojekt (.csx). |
| **`--no-build`** | Überspringt das Erstellen von dotnet-Funktionen. |
| **`--dotnet-cli-params`** | Beim Veröffentlichen kompilierter C#-Funktionen (.csproj) rufen die Core Tools „dotnet build --output bin/publish“ auf. Alle daran übergebenen Parameter werden an die Befehlszeile angefügt. |

### <a name="custom-container-deployment"></a>Bereitstellen benutzerdefinierter Container

Mit Functions können Sie Ihre Funktionsprojekt in einem benutzerdefinierten Linux-Container bereitstellen. Weitere Informationen finden Sie unter [Erstellen einer Funktion in Linux mit einem benutzerdefinierten Image (Vorschau)](functions-create-function-linux-custom-image.md). Version 2.x von Core Tools unterstützt die Bereitstellung eines benutzerdefinierten Containers. Benutzerdefinierte Container müssen ein Dockerfile enthalten. Verwenden Sie die Option „--dockerfile“ für `func init`.

```bash
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

Die empfohlene Methode zum Überwachen der Ausführung Ihrer Funktionen ist die Integration in Azure Application Insights. Wenn Sie eine Funktions-App im Azure-Portal erstellen, wird diese Integration standardmäßig für Sie erledigt. Wenn Sie Ihre Funktions-App mithilfe der Azure CLI erstellen, erfolgt die Integration in Ihre Funktions-App in Azure nicht.

So aktivieren Sie Application Insights für Ihre Funktions-App

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).
## <a name="next-steps"></a>Nächste Schritte

Azure Functions Core Tools ist ein [Open Source-Programm und wird auf GitHub gehostet](https://github.com/azure/azure-functions-cli).  
Um einen Fehler zu melden oder ein Feature anzufordern, [öffnen Sie ein GitHub-Problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[FUNCTIONS_WORKER_RUNTIME]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Erweiterungsbundles]: functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles
