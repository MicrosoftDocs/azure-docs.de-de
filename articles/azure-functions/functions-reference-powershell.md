---
title: PowerShell-Entwicklerreferenz für Azure Functions
description: Erfahren Sie, wie Sie mithilfe von PowerShell Funktionen entwickeln können.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: af9490433c344c712da55e9b29bf9df364380736
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422534"
---
# <a name="azure-functions-powershell-developer-guide"></a>PowerShell-Entwicklerhandbuch für Azure Functions

Dieser Artikel enthält Informationen zum Schreiben von Funktionen in Azure Functions mithilfe von PowerShell.

Eine PowerShell-Azure-Funktion (Funktion) wird als ein PowerShell-Skript dargestellt, das durch Auslösen ausgeführt wird. Jedes Funktionsskript verfügt über eine zugehörige Datei `function.json`, in der definiert ist, wie sich die Funktion verhält, also z. B. wie sie ausgelöst wird und welche Ein- und Ausgabeparameter verwendet werden. Weitere Informationen finden Sie im Artikel zu [Triggern und Bindungen](functions-triggers-bindings.md). 

Wie andere Arten von Funktionen akzeptieren PowerShell-Skriptfunktionen Parameter, die den Namen aller Eingabebindungen entsprechen, die in der Datei `function.json` definiert sind. Ein `TriggerMetadata`-Parameter wird ebenfalls übergeben. Dieser enthält zusätzliche Informationen zu dem Trigger, der die Funktion gestartet hat.

In diesem Artikel wird davon ausgegangen, dass Sie bereits die [Entwicklerreferenz zu Azure Functions](functions-reference.md)gelesen haben. Sie sollten auch den [Schnellstart für Functions und PowerShell](./create-first-function-vs-code-powershell.md) abgeschlossen haben, in dem Sie Ihre erste PowerShell-Funktion erstellen.

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

Bestimmte Bindungen erfordern das Vorhandensein einer Datei mit dem Namen `extensions.csproj`. Die in [Version 2.x](functions-versions.md) oder höher der Functions-Runtime erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](functions-bindings-register.md#extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

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

| Eigenschaft   | BESCHREIBUNG                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Zeitpunkt der Auslösung der Funktion in UTC        | Datetime |
| MethodName | Der Name der Funktion, die ausgelöst wurde     | Zeichenfolge   |
| RandGuid   | Eine eindeutige GUID für diese Ausführung der Funktion | Zeichenfolge   |

Jeder Triggertyp verfügt über einen anderen Satz von Metadaten. Die `$TriggerMetadata` für `QueueTrigger` enthalten neben anderen Informationen z. B. Werte für `InsertionTime`, `Id` und `DequeueCount`. Weitere Informationen zu den Metadaten von Warteschlangentriggern finden Sie in der [offiziellen Dokumentation zu Warteschlangentriggern](functions-bindings-storage-queue-trigger.md#message-metadata). Sehen Sie in der Dokumentation zu den von Ihnen verwendeten [Triggern](functions-triggers-bindings.md) nach, welche Informationen in den Metadaten der Trigger enthalten sind.

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

| Name | type | Position | BESCHREIBUNG |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Der Name der Ausgabebindung, die Sie festlegen möchten |
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

Weitere Informationen finden Sie unter [About CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters) (Informationen zu CommonParameters).

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

`Push-OutputBinding` dient zum Senden von Daten an Ausgabebindungen, z. B. eine [Azure Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md). Im folgenden Beispiel hat die in die Warteschlange geschriebene Nachricht den Wert „output #1“:

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
| Fehler | **`Write-Error`** |
| Warnung | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Schreibt in die Protokollierung auf _Informationsebene_ |
| Debuggen | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Zusätzlich zu diesen Cmdlets wird alles, was in die Pipeline geschrieben wird, auf die Protokollebene `Information` umgeleitet und mit der Standardformatierung von PowerShell angezeigt.

> [!IMPORTANT]
> Die Verwendung der Cmdlets `Write-Verbose` oder `Write-Debug` reicht nicht für die Anzeige von Protokollen der Ebenen „Ausführlich“ und „Debuggen“. Sie müssen auch den Schwellenwert für die Protokollebene konfigurieren, mit dem Sie angeben, welche Protokollebene Sie tatsächlich benötigen. Weitere Informationen finden Sie unter [Konfigurieren der Protokollebene für Funktions-Apps](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurieren der Protokollebene für Funktions-Apps

Sie können in Azure Functions den Schwellenwert definieren und damit steuern, was Functions in die Protokolle schreibt. Verwenden Sie die `logging.logLevel.default`-Eigenschaft in der [Datei `host.json`][host.json-Referenz], um den Schwellenwert für alle Ablaufverfolgungen festzulegen, die an der Konsole geschrieben werden. Diese Einstellung gilt für alle Funktionen in Ihrer Funktionen-App.

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
* byte[]
* INT
* double
* HttpRequestContext
* HttpResponseContext

Die ersten fünf Typen in dieser Liste sind Standardtypen von .NET. Die letzten beiden werden nur für den Trigger [HttpTrigger](#http-triggers-and-bindings) verwendet.

Jeder Bindungsparameter in Ihren Funktionen muss einen dieser Typen aufweisen.

### <a name="http-triggers-and-bindings"></a>HTTP: Trigger und Bindungen

HTTP- und Webhooktrigger und HTTP-Ausgabebindungen verwenden Request- und Response-Objekte, um das HTTP-Messaging darzustellen.

#### <a name="request-object"></a>Anforderungsobjekt

Das Anforderungsobjekt, das an das Skript übergeben wird, ist vom Typ `HttpRequestContext`, der über die folgenden Eigenschaften verfügt:

| Eigenschaft  | BESCHREIBUNG                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ein Objekt, das den Hauptteil der Anforderung enthält. `Body` wird basierend auf den Daten in den am besten geeigneten Typ serialisiert. Bei JSON-Daten wird z. B. eine Hashtabelle übergeben. Wenn es sich bei den Daten um eine Zeichenfolge handelt, erfolgt die Übergabe auch als Zeichenfolge. | Objekt (object) |
| **`Headers`** | Ein Wörterbuch mit den Headern der Anforderung.                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | Die HTTP-Methode der Anforderung.                                | Zeichenfolge                    |
| **`Params`**  | Ein Objekt, das die Routingparameter der Anforderung enthält. | Dictionary<string,string><sup>*</sup> |
| **`Query`** | Ein Objekt, das die Abfrageparameter enthält.                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | Die URL der Anforderung.                                        | Zeichenfolge                    |

<sup>*</sup> Bei `Dictionary<string,string>`-Schlüsseln wird nicht zwischen Groß- und Kleinschreibung unterschieden.

#### <a name="response-object"></a>Antwortobjekt

Das Antwortobjekt, das Sie zurücksenden sollten, weist den Typ `HttpResponseContext` auf, der über die folgenden Eigenschaften verfügt:

| Eigenschaft      | BESCHREIBUNG                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ein Objekt, das den Hauptteil der Antwort enthält.           | Objekt (object)                    |
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

Bei PowerShell-Funktionen wird das Profilskript einmal pro PowerShell-Workerinstanz in der App bei der ersten Bereitstellung und nach dem Leerlauf ([Kaltstart](#cold-start)) ausgeführt. Wenn die Parallelität durch Festlegen des Werts [PSWorkerInProcConcurrencyUpperBound](#concurrency) aktiviert ist, wird das Profilskript für jeden erstellten Runspace ausgeführt.

Wenn Sie eine Funktions-App mit Tools wie Visual Studio Code und Azure Functions Core Tools erstellen, wird automatisch eine Standarddatei `profile.ps1` erstellt. Das Standardprofil wird im [GitHub-Repository von Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) verwaltet und bietet Folgendes:

* Automatische MSI-Authentifizierung in Azure
* Möglichkeit der Aktivierung des Azure PowerShell-Alias `AzureRM` bei Bedarf

## <a name="powershell-versions"></a>PowerShell-Versionen

Die folgende Tabelle zeigt die PowerShell-Versionen, die für jede Hauptversion der Functions-Runtime verfügbar sind, sowie die erforderliche .NET-Version:

| Functions-Version | PowerShell-Version                               | .NET-Version  | 
|-------------------|--------------------------------------------------|---------------|
| 3.x (empfohlen) | PowerShell 7 (empfohlen)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

Die aktuell von der Runtime verwendete Version sehen Sie in der Ausgabe `$PSVersionTable` einer Funktion.

### <a name="running-local-on-a-specific-version"></a>Lokale Ausführung unter einer bestimmten Version

Wenn Azure Functions lokal ausgeführt wird, verwendet die Runtime standardmäßig PowerShell Core 6. Um stattdessen PowerShell 7 bei lokaler Ausführung zu verwenden, müssen Sie die Einstellung `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` zum Array `Values` in der Datei „local.setting.json“ im Projektstamm hinzufügen. Wenn die Datei „local.settings.json“ lokal unter PowerShell 7 ausgeführt wird, sieht Ihre local.settings.json-Datei wie im folgenden Beispiel aus: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>Ändern der PowerShell-Version

Ihre Funktions-App muss unter Version 3.x ausgeführt werden, um ein Upgrade von PowerShell Core 6 auf PowerShell 7 durchführen zu können. Informationen dazu finden Sie unter [Anzeigen und Aktualisieren der aktuellen Runtimeversion](set-runtime-version.md#view-and-update-the-current-runtime-version).

Verwenden Sie die folgenden Schritte, um die von Ihrer Funktions-App verwendete PowerShell-Version zu ändern. Sie können dies entweder im Azure-Portal oder mithilfe von PowerShell erledigen.

# <a name="portal"></a>[Portal](#tab/portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App.

1. Wählen Sie unter **Einstellungen** die Option **Konfiguration** aus. Suchen Sie auf der Registerkarte **Allgemeine Einstellungen** die **PowerShell-Version**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Wählen Sie die von der Funktions-App verwendete PowerShell-Version aus."::: 

1. Wählen Sie die gewünschte **PowerShell Core-Version** und anschließend **Speichern** aus. Wenn Sie vor dem anstehenden Neustart gewarnt werden, wählen Sie **Fortsetzen** aus. Die Funktions-App wird unter der gewählten PowerShell-Version neu gestartet. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie das folgende Skript aus, um die PowerShell-Version zu ändern: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Ersetzen Sie `<SUBSCRIPTION_ID>`, `<RESOURCE_GROUP>` und `<FUNCTION_APP>` durch die ID Ihres Azure-Abonnements, den Namen Ihrer Ressourcengruppe bzw. der Funktions-App.  Ersetzen Sie außerdem `<VERSION>` entweder durch `~6` oder `~7`. Sie können den aktualisierten Wert der `powerShellVersion`-Einstellung in `Properties` der zurückgegebenen Hashtabelle überprüfen. 

---

Die Funktions-App startet neu, nachdem die Änderung der Konfiguration vorgenommen wurde.

## <a name="dependency-management"></a>Verwaltung von Abhängigkeiten

Azure Functions ermöglicht Ihnen das Nutzen des [PowerShell-Katalogs](https://www.powershellgallery.com) zum Verwalten von Abhängigkeiten. Wenn die Abhängigkeitsverwaltung aktiviert ist, wird die Datei „requirements.psd1“ verwendet, um die benötigten Module automatisch herunterzuladen. Sie aktivieren dieses Verhalten, indem Sie im Stammverzeichnis der Datei [host.json](functions-host-json.md) die `managedDependency`-Eigenschaft auf `true` festlegen (siehe folgendes Beispiel):

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Wenn Sie ein neues PowerShell Functions-Projekt erstellen, ist die Abhängigkeitsverwaltung standardmäßig aktiviert, wobei das Azure[`Az`-Modul](/powershell/azure/new-azureps-module-az) enthalten ist. Die maximal unterstützte Anzahl von Modulen beträgt derzeit 10. Die unterstützte Syntax ist _`MajorNumber`_ `.*` oder eine exakte Modulversion, wie im folgenden Beispiel von „requirements.psd1“ gezeigt:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Wenn Sie die Datei „requirements.psd1“ aktualisieren, werden aktualisierte Module nach einem Neustart installiert.

> [!NOTE]
> Bei verwalteten Abhängigkeiten ist zum Herunterladen von Modulen Zugriff auf www.powershellgallery.com erforderlich. Achten Sie bei lokaler Ausführung darauf, dass die Laufzeit auf diese URL zugreifen kann, indem Sie alle erforderlichen Firewallregeln hinzufügen.

> [!NOTE]
> Verwaltete Abhängigkeiten unterstützen derzeit keine Module, bei denen der Benutzer eine Lizenz akzeptieren muss, entweder durch interaktives Akzeptieren der Lizenz oder durch Bereitstellung des `-AcceptLicense`-Schalters beim Aufruf von `Install-Module`.

Die folgenden Anwendungseinstellungen können verwendet werden, um zu ändern, wie die verwalteten Abhängigkeiten heruntergeladen und installiert werden. Ihr App-Upgrade beginnt innerhalb von `MDMaxBackgroundUpgradePeriod`, und der Upgrade-Prozess wird innerhalb von `MDNewSnapshotCheckPeriod` abgeschlossen.

| Funktions-App-Einstellung              | Standardwert             | BESCHREIBUNG                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 Tage)     | Jeder PowerShell-Workerprozess löst die Überprüfung auf Modulupgrades im PowerShell-Katalog beim Start des Prozesses und alle `MDMaxBackgroundUpgradePeriod` danach aus. Wenn im PowerShell-Katalog eine neue Modulversion verfügbar ist, wird sie im Dateisystem installiert und PowerShell-Workern zur Verfügung gestellt. Wenn Sie diesen Wert verringern, erhält Ihre Funktions-App schneller eine neuere Modulversion. Dies steigert aber auch den App-Ressourceneinsatz (Netzwerk-E/A, CPU, Speicher). Wenn Sie diesen Wert erhöhen, wird der App-Ressourceneinsatz verringert, aber auch die Bereitstellung neuer Modulversionen für Ihre App verzögert. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 Stunde)       | Nach der Installation neuer Modulversionen im Dateisystem müssen alle PowerShell-Workerprozesse neu gestartet werden. Das Neustarten von PowerShell-Workern wirkt sich auf die Verfügbarkeit der App aus, da dadurch die Ausführung der aktuellen Funktion unterbrochen werden kann. Bis zum Abschluss des Neustarts aller PowerShell-Workerprozesse können Funktionsaufrufe entweder die alte oder neue Modulversion verwenden. Alle PowerShell-Worker werden innerhalb von `MDNewSnapshotCheckPeriod` vollständig neu gestartet. Wenn Sie diesen Wert erhöhen, wird die Häufigkeit von Unterbrechungen verringert, aber es kann auch zu einer Verlängerung des Zeitraums kommen, in dem für Funktionsaufrufe nicht bestimmt werden kann, ob die alte oder die neue Modulversion verwendet wird. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 Tag)     | Um übermäßige Modulupgrades bei häufigen Workerneustarts zu vermeiden, wird die Überprüfung auf Modulupgrades nicht durchgeführt, wenn ein Worker diese Prüfung bereits innerhalb der letzten `MDMinBackgroundUpgradePeriod` ausgelöst hat. |

Wenn Sie Ihre eigenen benutzerdefinierten Module verwenden möchten, müssen Sie ein wenig anders vorgehen als gewohnt.

Auf Ihrem lokalen Computer wird das Modul in einem der global verfügbaren Ordner in Ihrem `$env:PSModulePath` installiert. Bei Ausführung in Azure haben Sie keinen Zugriff auf die auf Ihrem Computer installierten Module. Aus diesem Grund muss sich der `$env:PSModulePath` für eine PowerShell-Funktions-App vom `$env:PSModulePath` eines regulären PowerShell-Skripts unterscheiden.

In Functions enthält `PSModulePath` zwei Pfade:

* Den Ordner `Modules` im Stammverzeichnis Ihrer Funktions-App
* Einen Pfad zum Ordner `Modules`, der vom PowerShell-Sprachworker gesteuert wird


### <a name="function-app-level-modules-folder"></a>Ordner `Modules` auf Ebene der Funktions-App

Um benutzerdefinierte Module zu verwenden, können Sie die Module, von denen Ihre Funktionen abhängen, im Ordner `Modules` speichern. Module in diesem Ordner stehen in der Functions-Runtime automatisch zur Verfügung. Jede Funktion in der Funktions-App kann diese Module verwenden. 

> [!NOTE]
> In der Datei „requirements.psd1“ angegebene Module werden automatisch heruntergeladen und in den Pfad eingeschlossen, sodass Sie sie nicht in den Ordner „modules“ einschließen müssen. Diese werden bei Ausführung in der Cloud lokal im Ordner `$env:LOCALAPPDATA/AzureFunctions` und im Ordner `/data/ManagedDependencies` gespeichert.

Um dieses benutzerdefinierte Modulfeature nutzen zu können, erstellen Sie den Ordner `Modules` im Stammverzeichnis der Funktions-App. Kopieren Sie die Module, die Sie in Ihren Funktionen verwenden möchten, an diesen Speicherort.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Mit dem Ordner `Modules` sollte Ihre Funktions-App folgende Ordnerstruktur aufweisen:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Wenn Sie Ihre Funktions-App starten, fügt der PowerShell-Sprachworker diesen Ordner `Modules` dem `$env:PSModulePath` hinzu, damit die Module wie bei normalen PowerShell-Skripts automatisch geladen werden.

### <a name="language-worker-level-modules-folder"></a>Ordner `Modules` auf Ebene des Sprachworkers

Mehrere Module werden häufig vom PowerShell-Sprachworker verwendet. Diese Module werden im `PSModulePath` an letzter Stelle definiert. 

Die aktuelle Liste der Module lautet wie folgt:

* [Microsoft.PowerShell.Archive:](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive) Modul für die Arbeit mit Archiven, z. B. `.zip`, `.nupkg` und anderen
* **ThreadJob** : Eine threadbasierte Implementierung der PowerShell-Auftrags APIs

Von Azure Functions wird standardmäßig die neueste Version dieser Module verwendet. Um eine bestimmte Modulversion zu verwenden, legen Sie diese bestimmte Version im Ordner `Modules` Ihrer Funktionsanwendung ab.

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

Es gibt einige Parallelitätsmodelle, die Sie je nach Art der Workload untersuchen könnten:

* Erhöhung von ```FUNCTIONS_WORKER_PROCESS_COUNT```. Dadurch können Funktionsaufrufe in mehreren Prozessen innerhalb derselben Instanz behandelt werden, was einen gewissen CPU- und Arbeitsspeicheroverhead mit sich bringt. Im Allgemeinen werden E/A-gebundene Funktionen nicht durch diesen Overhead beeinträchtigt. Bei CPU-gebundenen Funktionen können die Auswirkungen erheblich sein.

* Erhöhen Sie den Einstellungswert ```PSWorkerInProcConcurrencyUpperBound``` für die App. Dies ermöglicht die Erstellung mehrerer Runspaces innerhalb desselben Prozesses, was den CPU- und Arbeitsspeicheroverhead erheblich reduziert.

Sie legen diese Umgebungsvariable in den [App-Einstellungen](functions-app-settings.md) Ihrer Funktions-App fest.

Abhängig von Ihrem Anwendungsfall kann Durable Functions die Skalierbarkeit erheblich verbessern. Weitere Informationen finden Sie unter [Anwendungsmuster von Durable Functions](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> Möglicherweise erhalten Sie die Warnung „Anforderungen werden aufgrund nicht verfügbarer Runspaces in die Warteschlange gestellt“. Beachten Sie, dass dies kein Fehler ist. Die Nachricht teilt Ihnen mit, dass sich Anforderungen in der Warteschlange befinden und diese bearbeitet werden, wenn die vorherigen Anforderungen abgeschlossen sind.

### <a name="considerations-for-using-concurrency"></a>Überlegungen zur Verwendung von Parallelität

PowerShell ist standardmäßig eine _Singlethread_ -Skriptsprache. Parallelität kann jedoch mithilfe mehrerer PowerShell-Runspaces im gleichen Prozess hinzugefügt werden. Die Anzahl der erstellten Runspaces entspricht der Anwendungseinstellung ```PSWorkerInProcConcurrencyUpperBound```. Der Durchsatz hängt von der im ausgewählten Plan verfügbaren CPU-Anzahl und Arbeitsspeichergröße ab.

Azure PowerShell verwendet einige Kontexte und Status auf _Prozessebene_ , die Ihnen viel Eingabearbeit ersparen können. Wenn Sie jedoch Parallelität in Ihrer Funktions-App aktivieren und Aktionen aufrufen, die den Zustand ändern, könnte es zu Racebedingungen kommen. Diese Racebedingungen sind schwierig zu debuggen, da ein Aufruf von einem bestimmten Zustand abhängt, während ein anderer Aufruf diesen Zustand ändert.

Parallelität hat in Azure PowerShell einen enormen Wert, da einige Vorgänge sehr viel Zeit in Anspruch nehmen können. Sie müssen dabei jedoch umsichtig vorgehen. Wenn Sie vermuten, dass eine Racebedingung vorliegt, legen Sie die App-Einstellung „PSWorkerInProcConcurrencyUpperBound“ auf `1` fest, und verwenden Sie stattdessen [Isolation auf Sprachworkerprozess-Ebene](functions-app-settings.md#functions_worker_process_count) für Parallelität.

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

Ihr Skript wird bei jedem Aufruf ausgeführt. Vermeiden Sie die Verwendung von `Install-Module` in Ihrem Skript. Verwenden Sie stattdessen `Save-Module` vor der Veröffentlichung, damit Ihre Funktion keine Zeit für das Herunterladen des Moduls aufbringen muss. Falls Kaltstarts Auswirkungen auf Ihre Funktionen haben, sollten Sie erwägen, Ihre Funktions-App in einem [App Service-Plan](functions-scale.md#app-service-plan) mit *Always On* oder einem [Premium-Plan](functions-scale.md#premium-plan) bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)

[host.json-Referenz]: functions-host-json.md