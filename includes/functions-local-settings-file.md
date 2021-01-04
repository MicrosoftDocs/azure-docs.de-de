---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: d944d1d3e9c72471fab2435430a7d13e1770e807
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010481"
---
## <a name="local-settings-file"></a>Datei für lokale Einstellungen

Die Datei „local.settings.json“ speichert App-Einstellungen, Verbindungszeichenfolgen und Einstellungen, die von lokalen Entwicklungstools verwendet werden. Einstellungen in der Datei „local.settings.json“ werden nur bei der lokalen Ausführung von Projekten verwendet. Die Datei mit den lokalen Einstellungen hat folgende Struktur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

Diese Einstellungen werden bei der lokalen Ausführung von Projekten unterstützt:

| Einstellung      | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Wenn diese Einstellung auf `true` festgelegt wird, werden alle Werte mithilfe eines Schlüssels des lokalen Computers verschlüsselt. Wird mit `func settings`-Befehlen verwendet. Der Standardwert ist `false`. Es empfiehlt sich ggf., die Datei „local.settings.json“ auf dem lokalen Computer zu verschlüsseln, wenn sie Geheimnisse (etwa Dienstverbindungszeichenfolgen) enthält. Der Host entschlüsselt die Einstellungen bei der Ausführung automatisch. Verwenden Sie den Befehl `func settings decrypt`, bevor Sie versuchen, lokal verschlüsselte Einstellungen zu lesen. |
| **`Values`** | Ein Array von Anwendungseinstellungen und Verbindungszeichenfolgen, die bei der lokalen Ausführung eines Projekts verwendet werden. Diese Schlüssel-Wert-Paare (Zeichenfolge-Zeichenfolge) entsprechen den Anwendungseinstellungen in Ihrer Funktions-App in Azure, etwa [`AzureWebJobsStorage`]. Viele Trigger und Bindungen verfügen über eine Eigenschaft, die auf eine App-Einstellung für die Verbindungszeichenfolge verweist, z. B. `Connection` für den [Blob Storage-Trigger](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Für diese Eigenschaften muss eine Anwendungseinstellung im Array `Values` definiert sein. Eine Liste der häufig verwendeten Einstellungen finden Sie in der nachfolgenden Tabelle. <br/>Werte müssen Zeichenfolgen und dürfen nicht JSON-Objekte oder Arrays sein. Einstellungsnamen dürfen weder einen Doppelpunkt (`:`) noch einen doppelten Unterstrich (`__`) enthalten. Doppelte Unterstriche sind von der Runtime reserviert, und der Doppelpunkt ist für die Unterstützung der [Abhängigkeitsinjektion](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings) reserviert. |
| **`Host`** | Die Einstellungen in diesem Abschnitt passen den Hostprozess von Functions bei der lokalen Ausführung von Projekten an. Diese Einstellungen sind getrennt von den host.json-Einstellungen, die auch bei der Ausführung von Projekten in Azure angewendet werden. |
| **`LocalHttpPort`** | Legt den Standardport fest, der bei der Ausführung des lokalen Functions-Host verwendet wird (`func host start` und `func run`). Die Befehlszeilenoption `--port` hat Vorrang vor dieser Einstellung. |
| **`CORS`** | Definiert die für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) zulässigen Ursprünge. Ursprünge werden als durch Trennzeichen getrennte Liste ohne Leerzeichen bereitgestellt. Den Platzhalterwert (\*) wird unterstützt, wodurch Anforderungen von einem beliebigen Ursprung zulässig sind. |
| **`CORSCredentials`** |  Wird `true` festgelegt, sind Anforderungen vom Typ `withCredentials` zulässig. |
| **`ConnectionStrings`** | Eine Auflistung. Verwenden Sie diese Sammlung nicht für die Verbindungszeichenfolgen, die von Ihren Funktionsbindungen verwendet werden. Diese Sammlung wird nur von Frameworks verwendet, die Verbindungszeichenfolgen üblicherweise aus dem Abschnitt `ConnectionStrings` einer Konfigurationsdatei abrufen, z. B. [Entity Framework](/ef/ef6/). Verbindungszeichenfolgen in diesem Objekt werden der Umgebung mit dem Anbietertyp [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) hinzugefügt. Elemente in dieser Sammlung werden nicht mit anderen App-Einstellungen in Azure veröffentlicht. Sie müssen diese Werte explizit zur Sammlung `Connection strings` in den Einstellungen Ihrer Funktions-App hinzufügen. Bei der Erstellung einer [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)-Klasse in Ihrem Funktionscode sollten Sie den Verbindungszeichenfolgenwert zusammen mit den anderen Verbindungen in den **Anwendungseinstellungen** im Portal speichern. |

Bei lokaler Ausführung können die folgenden Anwendungseinstellungen in das Array **`Values`** aufgenommen werden:

| Einstellung | Werte | BESCHREIBUNG |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Verbindungszeichenfolge für das Speicherkonto oder<br/>`UseDevelopmentStorage=true`| Enthält die Verbindungszeichenfolge für ein Azure-Speicherkonto. Erforderlich, wenn andere Trigger als HTTP verwendet werden. Weitere Informationen finden Sie in der Referenz zu [`AzureWebJobsStorage`].<br/>Wenn der [Azure-Speicheremulator](../articles/storage/common/storage-use-emulator.md) lokal installiert ist und Sie [`AzureWebJobsStorage`] auf `UseDevelopmentStorage=true` festlegen, verwendet Core Tools den Emulator. Der Emulator ist während der Entwicklung hilfreich, doch sollten Sie vor der Bereitstellung einen Test mit einer tatsächlichen Speicherverbindung durchführen.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Wenn Sie bei lokaler Ausführung eine Funktion deaktivieren möchten, fügen Sie der Sammlung `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` hinzu, wobei `<FUNCTION_NAME>` der Name der Funktion ist. Weitere Informationen zum Deaktivieren von Funktionen in Azure Functions finden Sie [hier](../articles/azure-functions/disable-function.md#localsettingsjson). |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Gibt die Zielsprache der Functions-Runtime an. Erforderlich für Version 2.x und höher der Functions-Runtime. Diese Einstellung wird von Core Tools für Ihr Projekt generiert. Weitere Informationen finden Sie in der Referenz zu [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime).|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Gibt an, dass bei lokaler Ausführung PowerShell 7 verwendet wird. Wird kein Wert festgelegt, wird PowerShell Core 6 verwendet. Diese Einstellung wird nur bei lokaler Ausführung verwendet. Bei der Ausführung in Azure wird die PowerShell-Runtimeversion durch die Einstellung der `powerShellVersion`-Sitekonfiguration bestimmt, [die im Portal festgelegt werden kann](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage