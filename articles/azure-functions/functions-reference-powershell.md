---
title: PowerShell-Entwicklerreferenz für Azure Functions
description: Erfahren Sie, wie Sie mithilfe von PowerShell Funktionen entwickeln können.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833516"
---
# <a name="azure-functions-powershell-developer-guide"></a>PowerShell-Entwicklerhandbuch für Azure Functions

Dieser Artikel enthält Informationen zum Schreiben von Funktionen in Azure Functions mithilfe von PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Eine PowerShell-Azure-Funktion (Funktion) wird als ein PowerShell-Skript dargestellt, das durch Auslösen ausgeführt wird. Jedes Funktionsskript verfügt über eine zugehörige Datei `function.json`, in der definiert ist, wie sich die Funktion verhält, also z. B. wie sie ausgelöst wird und welche Ein- und Ausgabeparameter verwendet werden. Weitere Informationen finden Sie im Artikel zu [Triggern und Bindungen](functions-triggers-bindings.md). 

Wie andere Arten von Funktionen akzeptieren PowerShell-Skriptfunktionen Parameter, die den Namen aller Eingabebindungen entsprechen, die in der Datei `function.json` definiert sind. Ein `TriggerMetadata`-Parameter wird ebenfalls übergeben. Dieser enthält zusätzliche Informationen zu dem Trigger, der die Funktion gestartet hat.

In diesem Artikel wird davon ausgegangen, dass Sie bereits die [Entwicklerreferenz zu Azure Functions](functions-reference.md)gelesen haben. Sie sollten auch den [Schnellstart für Functions und PowerShell](functions-create-first-function-powershell.md) abgeschlossen haben, in dem Sie Ihre erste PowerShell-Funktion erstellen.

## <a name="folder-structure"></a>Ordnerstruktur

Die erforderlichen Ordnerstruktur für ein PowerShell-Projekt sieht folgendermaßen aus. Diese Standardeinstellung kann geändert werden. Weitere Informationen finden Sie im Abschnitt zu [scriptFile](#configure-function-scriptfile) weiter unten.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Im Stammverzeichnis des Projekts befindet sich eine freigegebene Datei [`host.json`](functions-host-json.md), die zum Konfigurieren der Funktions-App verwendet werden kann. Jede Funktion verfügt über einen Ordner mit einer eigenen Codedatei (PS1-Datei) und Bindungskonfigurationsdatei (`function.json`). Der Name des übergeordneten Verzeichnisses der Datei „function.json“ ist immer der Name Ihrer Funktion.

Bestimmte Bindungen erfordern das Vorhandensein einer Datei mit dem Namen `extensions.csproj`. Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

PowerShell-Funktions-Apps enthalten möglicherweise optional eine Datei `profile.ps1`, die ausgeführt wird, wenn eine Funktions-App gestartet wird (auch als *[Kaltstart](#cold-start)* bezeichnet). Weitere Informationen finden Sie unter [PowerShell-Profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definieren eines PowerShell-Skripts als Funktion

Standardmäßig sucht die Functions-Runtime in `run.ps1` nach Ihrer Funktion, wobei sich `run.ps1` im gleichen übergeordneten Verzeichnis befindet wie die entsprechende Datei `function.json`.

An das Skript wird bei der Ausführung eine Reihe von Argumenten übergeben. Um diese Parameter zu behandeln, fügen Sie oben im Skript einen `param`-Block hinzu wie im folgenden Beispiel gezeigt:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata-Parameter

Der `TriggerMetadata`-Parameter wird verwendet, um zusätzliche Informationen zum Trigger anzugeben. Die zusätzlichen Metadaten variieren von Bindung zu Bindung, sie enthalten aber alle eine `sys`-Eigenschaft mit den folgenden Daten:

```powershell
$TriggerMetadata.sys
```

| Eigenschaft   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Zeitpunkt der Auslösung der Funktion in UTC        | DateTime |
| MethodName | Der Name der Funktion, die ausgelöst wurde     | Zeichenfolge   |
| RandGuid   | Eine eindeutige GUID für diese Ausführung der Funktion | Zeichenfolge   |

Jeder Triggertyp verfügt über einen anderen Satz von Metadaten. Die `$TriggerMetadata` für `QueueTrigger` enthalten neben anderen Informationen z. B. Werte für `InsertionTime`, `Id` und `DequeueCount`. Weitere Informationen zu den Metadaten von Warteschlangentriggern finden Sie in der [offiziellen Dokumentation zu Warteschlangentriggern](functions-bindings-storage-queue.md#trigger---message-metadata). Sehen Sie in der Dokumentation zu den von Ihnen verwendeten [Triggern](functions-triggers-bindings.md) nach, welche Informationen in den Metadaten der Trigger enthalten sind.

## <a name="bindings"></a>Bindungen

In PowerShell werden [Bindungen](functions-triggers-bindings.md) in der Datei „function.json“ einer Funktion konfiguriert und definiert. Funktionen interagieren auf verschiedene Weise mit Bindungen.

### <a name="reading-trigger-and-input-data"></a>Lesen von Triggern und Eingabedaten

Die Trigger und Eingabebindungen werden als Parameter an die Funktion übergeben. Bei Eingabebindungen ist in „function.json“ für `direction` die Richtung `in` angegeben. Die in `function.json` definierte `name`-Eigenschaft stellt den Namen des Parameters im `param`-Block dar. Da PowerShell benannte Parameter für die Bindung verwendet, spielt die Reihenfolge der Parameter keine Rolle. Es hat sich jedoch bewährt, die Reihenfolge der Bindungen zu verwenden, die in der Datei `function.json` festgelegt ist.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Schreiben von Ausgabedaten

In Functions ist bei einer Ausgabebindung die `direction` in der Datei „functions.json“ auf `out` festgelegt. Sie können mit dem Cmdlet `Push-OutputBinding`, das in der Functions-Runtime verfügbar ist, in eine Ausgabebindung schreiben. In allen Fällen entspricht die `name`-Eigenschaft der Bindung gemäß der Definition in `function.json` dem `Name`-Parameter des Cmdlets `Push-OutputBinding`.

Im Folgenden wird gezeigt, wie Sie `Push-OutputBinding` in Ihrem Funktionsskript aufrufen:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Sie können über die Pipeline auch einen Wert für eine bestimmte Bindung übergeben.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

Das Verhalten von `Push-OutputBinding` hängt vom Wert für `-Name` ab:

* Wenn der angegebene Name nicht in eine gültige Ausgabebindung aufgelöst werden kann, wird ein Fehler ausgelöst.

* Wenn die Ausgabebindung eine Auflistung von Werten akzeptiert, können Sie `Push-OutputBinding` wiederholt aufrufen, um mehrere Werte zu pushen.

* Wenn die Ausgabebindung nur einen Singletonwert akzeptiert, wird durch einen zweiten Aufruf von `Push-OutputBinding` ein Fehler ausgelöst.

#### <a name="push-outputbinding-syntax"></a>Syntax von `Push-OutputBinding`

Im Folgenden sind gültige Parameter für den Aufruf von `Push-OutputBinding` angegeben:

| NAME | Type | Position | BESCHREIBUNG |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Zeichenfolge | 1 | Der Name der Ausgabebindung, die Sie festlegen möchten |
| **`-Value`** | Object | 2 | Der Wert der festzulegenden Ausgabebindung, der vom ByValue-Wert der Pipeline akzeptiert wird. |
| **`-Clobber`** | SwitchParameter | benannt | (Optional:) Durch die Angabe wird erzwungen, dass der Wert für eine angegebene Ausgabebindung festgelegt werden muss. | 

Die folgenden allgemeinen Parameter werden ebenfalls unterstützt: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Weitere Informationen finden Sie unter [About CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216) (Informationen zu CommonParameters).

#### <a name="push-outputbinding-example-http-responses"></a>Beispiel für „Push-OutputBinding“: HTTP-Antworten

Ein HTTP-Trigger gibt eine Antwort mithilfe einer Ausgabebindung mit dem Namen `response` zurück. Im folgenden Beispiel hat die Ausgabebindung von `response` den Wert „output #1“:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Da die Ausgabe über HTTP erfolgt (wobei nur ein Singletonwert akzeptiert wird), wird ein Fehler ausgelöst, wenn `Push-OutputBinding` ein zweites Mal aufgerufen wird.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Für Ausgaben, die nur Singletonwerte akzeptieren, können Sie mit dem `-Clobber`-Parameter den alten Wert überschreiben, anstatt das Hinzufügen zu einer Auflistung zu versuchen. Im folgenden Beispiel wird davon ausgegangen, dass Sie bereits einen Wert hinzugefügt haben. Mithilfe von `-Clobber` überschreibt die Antwort im folgenden Beispiel den vorhandenen Wert, um den Wert „output #3“ zurückzugeben:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Beispiel für „Push-OutputBinding“: Einreihen von Ausgabebindungen in Warteschlangen

`Push-OutputBinding` dient zum Senden von Daten an Ausgabebindungen, z. B. eine [Azure Queue Storage-Ausgabebindung](functions-bindings-storage-queue.md#output). Im folgenden Beispiel hat die in die Warteschlange geschriebene Nachricht den Wert „output #1“:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Die Ausgabebindung für eine Speicherwarteschlange akzeptiert mehrere Ausgabewerte. In diesem Fall wird durch Aufrufen des folgenden Beispiels nach dem ersten Beispiel eine Liste mit den beiden Elementen „output #1“ und „output #2“ in die Warteschlange geschrieben.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Wenn das folgende Beispiel nach den ersten beiden aufgerufen wird, werden der Ausgabeauflistung zwei weitere Werte hinzugefügt:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Beim Schreiben in die Warteschlange enthält die Nachricht diese vier Werte: „output #1“, „output #2“, „output #3“ und „output #4“.

#### <a name="get-outputbinding-cmdlet"></a>Cmdlet `Get-OutputBinding`

Sie können das Cmdlet `Get-OutputBinding` verwenden, um die Werte abzurufen, die derzeit für Ihre Ausgabebindungen festgelegt sind. Dieses Cmdlet ruft eine Hashtabelle mit den Namen der Ausgabebindungen und ihren jeweiligen Werten ab. 

Das folgende Beispiel zeigt die Verwendung von `Get-OutputBinding` für das Zurückgeben der aktuellen Bindungswerte:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` umfasst auch den Parameter `-Name`, der zum Filtern der zurückgegebenen Bindungen verwendet werden kann, wie im folgenden Beispiel gezeigt:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Platzhalter (*) werden in `Get-OutputBinding` unterstützt.

## <a name="logging"></a>Protokollierung

Die Protokollierung funktioniert bei PowerShell-Funktionen wie die reguläre PowerShell-Protokollierung. Sie können die Cmdlets für die Protokollierung verwenden, um in beliebige Ausgabestreams zu schreiben. Jedes Cmdlet ist einer der von Functions verwendeten Protokollebenen zugeordnet.

| Protokollierungsebene von Functions | Protokollierungs-Cmdlet |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| Warnung | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Schreibt in die Protokollierung auf _Informationsebene_ |
| Debuggen | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Zusätzlich zu diesen Cmdlets wird alles, was in die Pipeline geschrieben wird, auf die Protokollebene `Information` umgeleitet und mit der Standardformatierung von PowerShell angezeigt.

> [!IMPORTANT]
> Die Verwendung der Cmdlets `Write-Verbose` oder `Write-Debug` reicht nicht für die Anzeige von Protokollen der Ebenen „Ausführlich“ und „Debuggen“. Sie müssen auch den Schwellenwert für die Protokollebene konfigurieren, mit dem Sie angeben, welche Protokollebene Sie tatsächlich benötigen. Weitere Informationen finden Sie unter [Konfigurieren der Protokollebene für Funktions-Apps](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurieren der Protokollebene für Funktions-Apps

Sie können in Functions den Schwellenwert definieren und damit steuern, was Functions in die Protokolle schreibt. Verwenden Sie die `logging.logLevel.default`-Eigenschaft in der [Datei `host.json`][host.json-Referenz], um den Schwellenwert für alle Ablaufverfolgungen festzulegen, die an der Konsole geschrieben werden. Diese Einstellung gilt für alle Funktionen in Ihrer Funktionen-App.

Im folgenden Beispiel wird der Schwellenwert zum Aktivieren der ausführlichen Protokollierung für alle Funktionen festgelegt. Für die `MyFunction`-Funktion wird hingegen der Schwellenwert für die Debugprotokollierung festgelegt:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Weitere Informationen finden Sie in der [host.json-Referenz].

### <a name="viewing-the-logs"></a>Anzeigen der Protokolle

Wenn Ihre Funktions-App in Azure ausgeführt wird, können Sie sie mit Application Insights überwachen. Lesen Sie [Überwachen von Azure Functions](functions-monitoring.md), um weitere Informationen zum Anzeigen und Abfragen von Funktionsprotokollen zu erhalten.

Wenn Sie Ihre Funktions-App für die Entwicklung lokal ausführen, erfolgt die Protokollierung standardmäßig entsprechend dem Dateisystem. Legen Sie zum Anzeigen der Protokolle an der Konsole die Umgebungsvariable `AZURE_FUNCTIONS_ENVIRONMENT` auf `Development` fest, bevor Sie die Funktions-App starten.

## <a name="triggers-and-bindings-types"></a>Typen von Triggern und Bindungen

Sie können für Ihre Funktions-Apps eine Reihe unterschiedlicher Trigger und Bindungen verwenden. Die vollständige Liste der Trigger und Bindungen [finden Sie hier](functions-triggers-bindings.md#supported-bindings).

Alle Trigger und Bindungen werden im Code als echte Datentypen dargestellt:

* Hashtable
* Zeichenfolge
* Byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Die ersten fünf Typen in dieser Liste sind Standardtypen von .NET. Die letzten beiden werden nur für den Trigger [HttpTrigger](#http-triggers-and-bindings) verwendet.

Jeder Bindungsparameter in Ihren Funktionen muss einen dieser Typen aufweisen.

### <a name="http-triggers-and-bindings"></a>HTTP: Trigger und Bindungen

HTTP- und Webhooktrigger und HTTP-Ausgabebindungen verwenden Request- und Response-Objekte, um das HTTP-Messaging darzustellen.

#### <a name="request-object"></a>Anforderungsobjekt

Das Anforderungsobjekt, das an das Skript übergeben wird, ist vom Typ `HttpRequestContext`, der über die folgenden Eigenschaften verfügt:

| Eigenschaft  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ein Objekt, das den Hauptteil der Anforderung enthält. `Body` wird basierend auf den Daten in den am besten geeigneten Typ serialisiert. Bei JSON-Daten wird z. B. eine Hashtabelle übergeben. Wenn es sich bei den Daten um eine Zeichenfolge handelt, erfolgt die Übergabe auch als Zeichenfolge. | object |
| **`Headers`** | Ein Wörterbuch mit den Headern der Anforderung.                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | Die HTTP-Methode der Anforderung.                                | Zeichenfolge                    |
| **`Params`**  | Ein Objekt, das die Routingparameter der Anforderung enthält. | Dictionary<string,string><sup>*</sup> |
| **`Query`** | Ein Objekt, das die Abfrageparameter enthält.                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | Die URL der Anforderung.                                        | Zeichenfolge                    |

<sup>*</sup> Bei `Dictionary<string,string>`-Schlüsseln wird nicht zwischen Groß- und Kleinschreibung unterschieden.

#### <a name="response-object"></a>Antwortobjekt

Das Antwortobjekt, das Sie zurücksenden sollten, weist den Typ `HttpResponseContext` auf, der über die folgenden Eigenschaften verfügt:

| Eigenschaft      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ein Objekt, das den Hauptteil der Antwort enthält.           | object                    |
| **`ContentType`** | Einstellungsmöglichkeit für den Inhaltstyp der Antwort | Zeichenfolge                    |
| **`Headers`** | Ein Objekt, das die Header der Antwort enthält.               | Wörterbuch oder Hashtabelle   |
| **`StatusCode`**  | Der HTTP-Statuscode der Antwort.                       | Zeichenfolge oder ganze Zahl             |

#### <a name="accessing-the-request-and-response"></a>Zugreifen auf Anforderung und Antwort

Bei der Arbeit mit HTTP-Triggern können Sie auf die HTTP-Anforderung auf die gleiche Weise wie auf andere Eingabebindungen zugreifen. Dies ist der `param`-Block.

Verwenden Sie ein `HttpResponseContext`-Objekt, um eine Antwort zurückzugeben, wie im Folgenden dargestellt:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Das Ergebnis eines Aufrufs dieser Funktion wäre:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Typumwandlung für Trigger und Bindungen

Für bestimmte Bindungen wie Blobbindungen können Sie den Typ des Parameters angeben.

Damit z. B. Daten aus Blob Storage als Zeichenfolge bereitgestellt werden, fügen Sie dem `param`-Block die folgende Typumwandlung hinzu:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-Profil

In PowerShell gibt es das Konzept von PowerShell-Profilen. Wenn Sie nicht mit PowerShell-Profilen vertraut sind, lesen Sie unter [Informationen zu Profilen](/powershell/module/microsoft.powershell.core/about/about_profiles) nach.

Bei PowerShell-Funktionen wird das Profilskript beim Start der Funktions-App ausgeführt. Funktions-Apps starten bei der ersten Bereitstellung und nach einem Leerlauf ([Kaltstart](#cold-start)).

Wenn Sie eine Funktions-App mit Tools wie Visual Studio Code und Azure Functions Core Tools erstellen, wird automatisch eine Standarddatei `profile.ps1` erstellt. Das Standardprofil wird im [GitHub-Repository von Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) verwaltet und bietet Folgendes:

* Automatische MSI-Authentifizierung in Azure
* Möglichkeit der Aktivierung des Azure PowerShell-Alias `AzureRM` bei Bedarf

## <a name="powershell-version"></a>PowerShell-Version

Die folgende Tabelle zeigt die jeweilige von den Hauptversionen von Functions Runtime verwendete PowerShell-Version:

| Functions-Version | PowerShell-Version                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (durch Runtime gesperrt) |
| 2.x               | PowerShell Core 6                              |

Die aktuell von der Runtime verwendete Version sehen Sie in der Ausgabe `$PSVersionTable` einer Funktion.

## <a name="dependency-management"></a>Verwaltung von Abhängigkeiten

PowerShell-Funktionen unterstützen die Verwaltung von Azure-Modulen durch den Dienst. Wenn Sie die Datei „host.json“ ändern und die Eigenschaft „managedDependency“ auf „true“ festlegen, wird die Datei „requirements.psd1“ verarbeitet. Es werden automatisch die neuesten Azure-Module heruntergeladen und für die Funktion verfügbar gemacht.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Wenn Sie Ihre eigenen benutzerdefinierten Module oder Module aus dem [PowerShell-Katalog](https://powershellgallery.com) verwenden möchten, müssen Sie ein wenig anders vorgehen, als Sie es gewohnt sind.

Wenn Sie das Modul auf dem lokalen Computer installieren, wird es in einem der global verfügbaren Ordner in Ihrem `$env:PSModulePath` installiert. Da die Funktion in Azure ausgeführt wird, haben Sie keinen Zugriff auf die Module, die auf Ihrem Computer installiert sind. Aus diesem Grund muss sich der `$env:PSModulePath` für eine PowerShell-Funktions-App vom `$env:PSModulePath` eines regulären PowerShell-Skripts unterscheiden.

In Functions enthält `PSModulePath` zwei Pfade:

* Einen Ordner `Modules` im Stammverzeichnis der Funktions-App
* Einen Pfad zu einem Ordner `Modules` im PowerShell-Sprachworker

### <a name="function-app-level-modules-folder"></a>Ordner `Modules` auf Ebene der Funktions-App

Um benutzerdefinierte Module oder PowerShell-Module aus dem PowerShell-Katalog zu verwenden, können Sie die Module, von denen Ihre Funktionen abhängen, im Ordner `Modules` speichern. Module in diesem Ordner stehen in der Functions-Runtime automatisch zur Verfügung. Jede Funktion in der Funktions-App kann diese Module verwenden.

Um dieses Feature nutzen zu können, erstellen Sie den Ordner `Modules` im Stammverzeichnis der Funktions-App. Speichern Sie die Module, die Sie in Ihren Funktionen verwenden möchten, an diesem Speicherort.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Verwenden Sie `Save-Module`, um alle von Ihren Funktionen verwendeten Module zu speichern, oder kopieren Sie Ihre eigenen benutzerdefinierten Module in den Ordner `Modules`. Mit dem Ordner „Modules“ sollte Ihre Funktions-App folgende Ordnerstruktur aufweisen:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Wenn Sie Ihre Funktions-App starten, fügt der PowerShell-Sprachworker diesen Ordner `Modules` dem `$env:PSModulePath` hinzu, damit die Module wie bei normalen PowerShell-Skripts automatisch geladen werden.

### <a name="language-worker-level-modules-folder"></a>Ordner `Modules` auf Ebene des Sprachworkers

Mehrere Module werden häufig vom PowerShell-Sprachworker verwendet. Diese Module werden im `PSModulePath` an letzter Stelle definiert. 

Die aktuelle Liste der Module lautet wie folgt:

* [Microsoft.PowerShell.Archive:](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive) Modul für die Arbeit mit Archiven, z. B. `.zip`, `.nupkg` und anderen
* **ThreadJob**: Eine threadbasierte Implementierung der PowerShell-Auftrags APIs

Von Functions wird die neueste Version dieser Module verwendet. Um eine bestimmte Version dieser Module zu verwenden, können Sie die betreffende Version in den Ordner `Modules` der Funktions-App einfügen.

## <a name="environment-variables"></a>Umgebungsvariablen

In Functions werden [App-Einstellungen](functions-app-settings.md), z.B. Dienstverbindungszeichenfolgen, während der Ausführung als Umgebungsvariablen verfügbar gemacht. Auf diese Einstellungen können Sie über `$env:NAME_OF_ENV_VAR` zugreifen, wie im folgenden Beispiel gezeigt:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wenn App-Einstellungen lokal ausgeführt werden, werden sie über die Projektdatei [local.settings.json](functions-run-local.md#local-settings-file) gelesen.

## <a name="concurrency"></a>Parallelität

Standardmäßig kann die PowerShell-Runtime von Functions nur einen Aufruf einer Funktion zu einem Zeitpunkt verarbeiten. Dieser Grad an Parallelität ist in den folgenden Situationen jedoch möglicherweise nicht ausreichend:

* Wenn Sie versuchen, eine große Anzahl von Aufrufen gleichzeitig zu verarbeiten
* Wenn Ihre Funktionen andere Funktionen innerhalb derselben Funktions-App aufrufen

Sie können dieses Verhalten ändern, indem Sie die folgende Umgebungsvariable auf einen ganzzahligen Wert festlegen:

```
PSWorkerInProcConcurrencyUpperBound
```

Sie legen diese Umgebungsvariable in den [App-Einstellungen](functions-app-settings.md) Ihrer Funktions-App fest.

### <a name="considerations-for-using-concurrency"></a>Überlegungen zur Verwendung von Parallelität

PowerShell ist standardmäßig eine _Singlethread_-Skriptsprache. Parallelität kann jedoch mithilfe mehrerer PowerShell-Runspaces im gleichen Prozess hinzugefügt werden. Dies ist die Funktionsweise der PowerShell-Runtime für Azure Functions.

Dieser Ansatz hat jedoch einige Nachteile.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Parallelität ist nur so gut wie der Computer, auf dem sie ausgeführt wird

Wenn Ihre Funktions-App in einem [App Service-Plan](functions-scale.md#app-service-plan) ausgeführt wird, der nur einen einzigen Kern unterstützt, birgt Parallelität keine Vorteile. Da keine zusätzlichen Kerne verfügbar sind, kann auch die Last nicht ausgeglichen werden. In diesem Fall kann die Leistung variieren, wenn der einzelne Kern einen Kontextwechsel zwischen Runspaces ausführen kann.

Der [Verbrauchstarif](functions-scale.md#consumption-plan) wird mit nur einen Kern ausgeführt, sodass Sie keine Parallelität nutzen können. Wenn Sie Parallelität vollständig nutzen möchten, sollten Sie Ihre Funktionen stattdessen in einer Funktions-App bereitstellen, die in einem dedizierten App Service-Plan mit ausreichenden Kernen ausgeführt wird.

#### <a name="azure-powershell-state"></a>Azure PowerShell-Zustand

Azure PowerShell verwendet einige Kontexte und Status auf _Prozessebene_, die Ihnen viel Eingabearbeit ersparen können. Wenn Sie jedoch Parallelität in Ihrer Funktions-App aktivieren und Aktionen aufrufen, die den Zustand ändern, könnte es zu Racebedingungen kommen. Diese Racebedingungen sind schwierig zu debuggen, da ein Aufruf von einem bestimmten Zustand abhängt, während ein anderer Aufruf diesen Zustand ändert.

Parallelität hat in Azure PowerShell einen enormen Wert, da einige Vorgänge sehr viel Zeit in Anspruch nehmen können. Sie müssen dabei jedoch umsichtig vorgehen. Wenn Sie vermuten, dass eine Racebedingung vorliegt, legen Sie die Parallelität wieder auf `1` fest, und wiederholen Sie die Anforderung.

## <a name="configure-function-scriptfile"></a>Konfigurieren der `scriptFile`-Funktion

Standardmäßig wird eine PowerShell-Funktion aus der Datei `run.ps1` ausgeführt, die sich im gleichen übergeordneten Verzeichnis wie die zugehörige Datei `function.json` befindet.

Die `scriptFile`-Eigenschaft in der Datei `function.json` kann verwendet werden, um eine Ordnerstruktur zu erhalten, die wie im folgenden Beispiel aussieht:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

In diesem Fall enthält die Datei `function.json` für `myFunction` eine `scriptFile`-Eigenschaft, die auf die Datei mit der exportierten Funktion verweist, die ausgeführt werden soll.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Verwenden von PowerShell-Modulen durch Konfigurieren eines entryPoint

In diesem Artikel wurden PowerShell-Funktionen in der Standardskriptdatei `run.ps1` gezeigt, die von den Vorlagen generiert wird.
Sie können Ihre Funktionen aber auch in PowerShell-Module einfügen. Sie können auf Ihren spezifischen Funktionscode im Modul mit den Feldern `scriptFile` und `entryPoint` in der Konfigurationsdatei „function.json“ verweisen.

In diesem Fall ist `entryPoint` der Name einer Funktion oder eines Cmdlets in dem PowerShell-Modul, auf das in `scriptFile` verwiesen wird.

Gehen Sie z. B. von der folgenden Ordnerstruktur aus:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

`PSFunction.psm1` enthält dabei Folgendes:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

In diesem Beispiel umfasst die Konfiguration für `myFunction` eine `scriptFile`-Eigenschaft, die auf `PSFunction.psm1` – ein PowerShell-Modul in einem anderen Ordner – verweist.  Die `entryPoint`-Eigenschaft verweist auf die `Invoke-PSTestFunc`-Funktion, die den Einstiegspunkt für das Modul darstellt.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Bei dieser Konfiguration wird `Invoke-PSTestFunc` auf die gleiche Weise wie `run.ps1` ausgeführt.

## <a name="considerations-for-powershell-functions"></a>Überlegungen zu PowerShell-Funktionen

Beachten Sie beim Arbeiten mit PowerShell-Funktionen die Überlegungen in den folgenden Abschnitten.

### <a name="cold-start"></a>Kaltstart

Bei der Entwicklung von Azure Functions im [serverlosen Hostingmodell](functions-scale.md#consumption-plan) sind Kaltstarts Realität. *Kaltstart* bezieht sich auf den Zeitraum, den der Start Ihrer Funktions-App bis zum Verarbeiten einer Anforderung dauert. Kaltstarts treten beim Verbrauchstarif häufiger auf, da Ihre Funktions-App während inaktiver Phasen heruntergefahren wird.

### <a name="bundle-modules-instead-of-using-install-module"></a>Modulbündel statt `Install-Module`

Ihr Skript wird bei jedem Aufruf ausgeführt. Vermeiden Sie die Verwendung von `Install-Module` in Ihrem Skript. Verwenden Sie stattdessen `Save-Module` vor der Veröffentlichung, damit Ihre Funktion keine Zeit für das Herunterladen des Moduls aufbringen muss. Falls Kaltstarts Auswirkungen auf Ihre Funktionen haben, sollten Sie erwägen, Ihre Funktions-App in einem [App Service-Plan](functions-scale.md#app-service-plan) mit *Always On* oder einem [Premium-Plan](functions-scale.md#premium-plan-public-preview) bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)

[host.json-Referenz]: functions-host-json.md
