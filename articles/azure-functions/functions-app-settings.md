---
title: Referenz zu App-Einstellungen für Azure Functions
description: Referenzdokumentation für die App-Einstellungen für Azure Functions oder Umgebungsvariablen.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2b71bee620ab7d5b1ef98b60013d1978f49d127f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505885"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenz zu App-Einstellungen für Azure Functions

App-Einstellungen in einer Funktionen-App enthalten globale Konfigurationsoptionen, die sich auf alle Funktionen dieser Funktionen-App auswirken. Bei der lokalen Ausführung wird auf diese Einstellungen als lokale [Umgebungsvariablen](functions-run-local.md#local-settings-file) zugegriffen. In diesem Artikel werden die in Funktionen-Apps verfügbaren App-Einstellungen aufgelistet.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Es gibt andere globale Konfigurationsoptionen in der Datei [host.json](functions-host-json.md) und in der Datei [local.settings.json](functions-run-local.md#local-settings-file).

> [!NOTE]  
> Sie können Anwendungseinstellungen verwenden, um host.json-Einstellungswerte zu überschreiben, ohne die Datei „host.json“ ändern zu müssen. Dies ist hilfreich für Szenarien, in denen bestimmte host.json-Einstellungen für eine bestimmte Umgebung konfiguriert oder geändert werden müssen. So können Sie auch host.json-Einstellungen ändern, ohne das Projekt erneut veröffentlichen zu müssen. Weitere Informationen finden Sie im [Referenzartikel zu „host.json“](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Der Instrumentierungsschlüssel für Application Insights. Verwenden Sie jeweils nur `APPINSIGHTS_INSTRUMENTATIONKEY` oder `APPLICATIONINSIGHTS_CONNECTION_STRING`. Wenn Application Insights in einer Sovereign Cloud ausgeführt wird, verwenden Sie `APPLICATIONINSIGHTS_CONNECTION_STRING`. Weitere Informationen finden Sie unter [Konfigurieren der Überwachung für Azure Functions](configure-monitoring.md). 

|Schlüssel|Beispielwert|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Die Verbindungszeichenfolge für Application Insights. Verwenden Sie in den folgenden Fällen `APPLICATIONINSIGHTS_CONNECTION_STRING` anstelle von `APPINSIGHTS_INSTRUMENTATIONKEY`:

+ Für Ihre Funktions-App sind die zusätzlichen Anpassungen erforderlich, die bei Verwendung der Verbindungszeichenfolge unterstützt werden. 
+ Ihre Application Insights-Instanz wird in einer Sovereign Cloud ausgeführt, für die ein benutzerdefinierter Endpunkt erforderlich ist.

Weitere Informationen finden Sie unter [Verbindungszeichenfolgen](../azure-monitor/app/sdk-connection-string.md). 

|Schlüssel|Beispielwert|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standardmäßig nutzen [Functions-Proxys](functions-proxies.md) eine Verknüpfung, um API-Aufrufe von Proxys direkt an Funktionen in der gleichen Functions-App zu senden. Die Verknüpfung wird anstelle einer neuen HTTP-Anforderung verwendet. Diese Einstellung ermöglicht das Deaktivieren dieses Verknüpfungsverhaltens.

|Schlüssel|Wert|BESCHREIBUNG|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Aufrufe mit einer Back-End-URL, die auf eine Funktion in der lokalen Funktions-App verweist, werden nicht direkt an die Funktion gesendet. Stattdessen werden die Anforderungen wieder an das HTTP-Front-End für die Funktions-App zurückgeleitet.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Aufrufe mit einer Back-End-URL, die auf eine Funktion in der lokalen Funktions-App verweist, werden direkt an die Funktion weitergeleitet. Dies ist der Standardwert. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Diese Einstellung steuert, ob die Zeichen `%2F` in Routenparametern als Schrägstrich decodiert werden, wenn sie in die Back-End-URL eingefügt werden. 

|Schlüssel|Wert|BESCHREIBUNG|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Routenparameter mit codierten Schrägstrichen werden decodiert. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Alle Routenparameter werden unverändert weitergegeben (Standardverhalten). |

Sehen Sie sich beispielsweise die Datei „proxies.json“ für eine Funktions-App in der Domäne `myfunction.com` an.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Wenn `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` auf `true` festgelegt ist, wird die URL `example.com/api%2ftest` in `example.com/api/test` aufgelöst. Standardmäßig bleibt die URL unverändert (`example.com/test%2fapi`). Weitere Informationen finden Sie unter [Verwenden von Azure-Funktionsproxys](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Konfiguriert in Version 2.x oder höher der Functions-Runtime das App-Verhalten auf der Grundlage der Runtimeumgebung. Dieser Wert ist [während der Initialisierung lesen](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Sie können `AZURE_FUNCTIONS_ENVIRONMENT` auf beliebige Werte festlegen, aber [drei Werte](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) werden unterstützt: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) und [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Wenn `AZURE_FUNCTIONS_ENVIRONMENT` nicht festgelegt ist, wird als Standardwert in einer lokalen Umgebung `Development` und in Azure `Production` verwendet. Diese Einstellung sollte anstelle von `ASPNETCORE_ENVIRONMENT` verwendet werden, um die Laufzeitumgebung festzulegen. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

In Version 2.x und höheren Versionen der Functions-Runtime können Anwendungseinstellungen Einstellungen in [host.json](functions-host-json.md) in der aktuellen Umgebung außer Kraft setzen. Diese Außerkraftsetzungen werden als Anwendungseinstellungen namens `AzureFunctionsJobHost__path__to__setting` ausgedrückt. Weitere Informationen finden Sie unter [Außerkraftsetzung der Werte in der Datei „host.json“](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optionale Verbindungszeichenfolge für das Speicherkonto zum Speichern von Protokollen und zu deren Anzeige auf der Registerkarte **Überwachen** im Portal. Diese Einstellung ist nur für Apps gültig, die für Version 1.x der Azure Functions-Runtime gedacht sind. Das Speicherkonto muss ein allgemeines Konto sein, das BLOBs, Warteschlangen und Tabellen unterstützt. Weitere Informationen finden Sie unter [Speicherkontoanforderungen](storage-considerations.md#storage-account-requirements).

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Für bessere Leistung und Benutzerfreundlichkeit verwenden dir Runtimeversion 2.x und höhere Versionen APPINSIGHTS_INSTRUMENTATIONKEY und App Insights anstelle von `AzureWebJobsDashboard` für die Überwachung.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` bedeutet, dass die standardmäßige Landing Page deaktiviert wird, die für die Stamm-URL einer Funktionen-App angezeigt wird. Der Standardwert ist `false`.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Wenn diese App-Einstellung ausgelassen oder auf `false` gesetzt wird, erfolgt als Reaktion auf die URL `<functionappname>.azurewebsites.net` die Anzeige einer Seite, die dem folgenden Beispiel ähnelt.

![Landing Page der Funktionen-App](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` bedeutet, dass beim Kompilieren von .NET-Code der Releasemodus verwendet wird, während `false` bedeutet, dass der Debugmodus verwendet wird. Der Standardwert ist `true`.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Eine durch Trennzeichen getrennte Liste der zu aktivierenden Features der Betaversion. Durch diese Flags aktivierten Features der Betaversion sind nicht bereit für die Produktion, können aber zur experimentellen Verwendung aktiviert werden, bevor sie live geschaltet werden.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsFeatureFlags|Feature1,Feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Gibt das Repository oder den Anbieter an, das bzw. der zum Speichern von Schlüsseln verwendet wird. Aktuell sind die unterstützten Repositorys der Blobspeicher („Blob“) und das lokale Dateisystem („Dateien“). In Version 2 ist der Blobspeicher die Standardeinstellung, in Version 1 das Dateisystem.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsSecretStorageType|Dateien|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Die Azure Functions-Runtime verwendet diese Verbindungszeichenfolge für das Speicherkonto für den Normalbetrieb. Einige Verwendungsmöglichkeiten für dieses Speicherkonto umfassen Schlüsselverwaltung, Timer-Triggerverwaltung und Event Hubs-Prüfpunkte. Das Speicherkonto muss ein allgemeines Konto sein, das BLOBs, Warteschlangen und Tabellen unterstützt. Weitere Informationen finden Sie unter [Speicherkonto](functions-infrastructure-as-code.md#storage-account) und [Anforderungen an das Speicherkonto](storage-considerations.md#storage-account-requirements).

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[Name];AccountKey=[Schlüssel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Der Pfad zum Compiler, der für TypeScript verwendet wird. Bei Bedarf können Sie den Standardwert außer Kraft setzen.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Bestimmt, ob die Bearbeitung im Azure-Portal aktiviert ist. Gültige Werte sind „readwrite“ und „readonly“.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Die in dieser Funktionen-App zu verwendende Version der Functions-Runtime. Eine Tilde mit Hauptversion bedeutet, dass die neueste Version dieser Hauptversion verwendet wird (z.B. „~2“). Wenn neue Versionen für dieselbe Hauptversion verfügbar sind, werden sie automatisch in der Funktionen-App installiert. Verwenden Sie die vollständige Versionsnummer (z.B. „2.0.12345“), um die App an eine bestimmte Version anzuheften. Der Standardwert ist „~2“. Der Wert `~1` heftet Ihre App an Version 1.x der Runtime an.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

Diese Einstellung ermöglicht die Ausführung Ihrer Funktions-App in einem mit Version 2.x kompatiblen Modus unter Runtimeversion 3.x. Verwenden Sie diese Einstellung nur, wenn beim [Aktualisieren Ihrer Funktions-App von Runtimeversion 2.x auf 3.x](functions-versions.md#migrating-from-2x-to-3x) Probleme auftreten. 

>[!IMPORTANT]
> Diese Einstellung ist nur als kurzfristige Problemumgehung gedacht, während Sie Ihre App für die ordnungsgemäße Ausführung unter Version 3.x aktualisieren. Diese Einstellung wird unterstützt, solange die [Runtime 2.x unterstützt wird](functions-versions.md). Wenn Probleme auftreten, die die Ausführung Ihrer App unter Version 3.x ohne diese Einstellung verhindern, [melden Sie Ihr Problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

[FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) muss dafür auf `~3` festgelegt werden.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|true|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

Gibt die maximale Anzahl von Sprachworkerprozessen mit einem Standardwert von `1` an. Der zulässige Höchstwert ist `10`. Funktionsaufrufe werden gleichmäßig zwischen Sprachworkerprozessen verteilt. Sprachworkerprozesse werden so lange alle 10 Sekunden erzeugt, bis die von FUNCTIONS\_WORKER\_PROCESS\_COUNT festgelegte Anzahl erreicht ist. Die Verwendung mehrerer Sprachworkerprozesse ist nicht dasselbe wie [Skalierung](functions-scale.md). Erwägen Sie die Verwendung dieser Einstellung, wenn Ihr Workload aus einer Mischung von CPU-gebundenen und E/A-gebundenen Aufrufen besteht. Diese Einstellung gilt für alle .NET-Sprachen.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Die Sprachworkerruntime, die in der Funktionen-App geladen werden soll.  Dies entspricht der Sprache, die in Ihrer Anwendung verwendet wird (z.B. „dotnet“). Bei Funktionen in mehreren Sprachen müssen Sie diese in verschiedenen Apps mit dem jeweils passenden Workerruntimewert veröffentlichen.  Gültige Werte sind `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java) und `powershell` (PowerShell) und `python` (Python).

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

Durch den Wert für diese Einstellung wird eine benutzerdefinierte Paketindex-URL für Python-Apps angegeben. Verwenden Sie diese Einstellung, wenn Sie einen Remotebuild mit benutzerdefinierten Abhängigkeiten ausführen müssen, die sich in einem zusätzlichen Paketindex befinden.   

|Schlüssel|Beispielwert|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|http://my.custom.package.repo/simple |

Weitere Informationen finden Sie in der Python-Entwicklerreferenz unter [Benutzerdefinierte Abhängigkeiten](functions-reference-python.md#remote-build-with-extra-index-url).

## <a name="scale_controller_logging_enable"></a>SCALE\_CONTROLLER\_LOGGING\_ENABLE

_Diese Einstellung befindet sich derzeit in der Vorschauphase._  

Diese Einstellung dient zum Steuern der Protokollierung über den Azure Functions-Skalierungscontroller. Weitere Informationen finden Sie unter [Skalierungscontrollerprotokolle (Vorschau)](functions-monitoring.md#scale-controller-logs).

|Schlüssel|Beispielwert|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights:Verbose|

Der Wert für diesen Schlüssel wird im Format `<DESTINATION>:<VERBOSITY>` bereitgestellt, das wie folgt definiert ist:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

Nur für Verbrauchs- und Premium-Tarife. Die Verbindungszeichenfolge für das Speicherkonto, in dem der Code der Funktionen-App und die Konfiguration gespeichert werden. Weitere Informationen finden Sie unter [Erstellen einer Funktionen-App](functions-infrastructure-as-code.md#create-a-function-app).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[Name];AccountKey=[Schlüssel]|

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

Nur für Premium-Pläne. Der Wert `1` ermöglicht die Skalierung Ihrer Funktions-App, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk beschränken. Sie sollten diese Einstellung aktivieren, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk einschränken. Weitere Informationen finden Sie unter [Einschränken Ihres Speicherkontos auf ein virtuelles Netzwerk](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network-preview). 

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Nur für Verbrauchs- und Premium-Tarife. Der Dateipfad zum Code der Funktionen-App und zur Konfiguration. Wird mit WEBSITE_CONTENTAZUREFILECONNECTIONSTRING verwendet. Standardmäßig wird eine eindeutige Zeichenfolge verwendet, die mit dem Namen der Funktionen-App beginnt. Weitere Informationen finden Sie unter [Erstellen einer Funktionen-App](functions-infrastructure-as-code.md#create-a-function-app).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Die maximale Anzahl der Instanzen, auf denen die Funktionen-App aufskaliert werden kann. Dieser Wert ist standardmäßig unbegrenzt.

> [!IMPORTANT]
> Diese Einstellung befindet sich in der Vorschauphase.  Eine [App-Eigenschaft für das maximale Aufskalieren einer Funktion](./functions-scale.md#limit-scale-out) wurde hinzugefügt und ist die empfohlene Methode zum Begrenzen des Aufskalierens.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Nur Windows._  
Legt die Version von Node.js fest, die beim Ausführen Ihrer Funktions-App unter Windows verwendet werden soll. Verwenden Sie eine Tilde (~), damit die Laufzeit die neueste verfügbare Version der Zielhauptversion verwendet. Wenn Sie beispielsweise auf `~10` festlegen, wird die neueste Version von Node.js 10 verwendet. Wenn eine Hauptversion mittels Tilde vorgegeben wird, müssen Sie die Nebenversion nicht manuell aktualisieren. 

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Ermöglicht es Ihrer Funktions-App, über eine bereitgestellte Paketdatei ausgeführt zu werden.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Gültige Werte sind entweder eine URL, die in den Speicherort einer Bereitstellungspaketdatei aufgelöst werden kann, oder `1`. Bei einer Festlegung auf `1` muss sich das Paket im Ordner `d:\home\data\SitePackages` befinden. Wenn Sie die Zip-Bereitstellung mit dieser Einstellung verwenden, wird das Paket automatisch an diesen Speicherort hochgeladen. In der Vorschau wurde diese Einstellung als `WEBSITE_RUN_FROM_ZIP` bezeichnet. Weitere Informationen finden Sie unter [Ausführen Ihrer Azure Functions aus einem Paket](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

Ermöglicht das Festlegen der Zeitzone für Ihre Funktions-App. 

|Schlüssel|OS|Beispielwert|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|Eastern Normalzeit|
|WEBSITE\_TIME\_ZONE|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Nächste Schritte

[Informationen zum Aktualisieren von App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings)

[Weitere Informationen finden Sie unter den globalen Einstellungen in der Datei host.json](functions-host-json.md)

[Weitere App-Einstellungen für App Service-Apps](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
