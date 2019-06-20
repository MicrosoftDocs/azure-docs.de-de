---
title: Übersicht über die Runtimeversionen von Azure Functions
description: Azure Functions unterstützt mehrere Versionen der Runtime. Lernen Sie die Unterschiede kennen, und erfahren Sie, wie Sie die Version auswählen, die sich am besten für Ihre Anforderungen eignet.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036269"
---
# <a name="azure-functions-runtime-versions-overview"></a>Übersicht über die Runtimeversionen von Azure Functions

 Es gibt zwei Hauptversionen der Azure Functions-Runtime: 1.x und 2.x. Die aktuelle Version, in der neue Funktionen eingeführt und Verbesserungen vorgenommen werden, ist 2.x. Es werden jedoch beide Versionen für Produktionsszenarien unterstützt.  Im Folgenden werden einige der Unterschiede zwischen den beiden Versionen erläutert. Zudem wird beschrieben, wie Sie Apps in beiden Versionen erstellen und von 1.x auf 2.x aktualisieren.

> [!NOTE]
> Dieser Artikel bezieht sich auf den Clouddienst Azure Functions. Informationen zum Vorschauprodukt, mit dem Sie Azure Functions lokal ausführen können, finden Sie in der [Übersicht über die Azure Functions-Runtime](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Plattformübergreifende Entwicklung

Die Runtime in Version 2.x wird auf .NET Core 2 ausgeführt, kann also auf allen Plattformen ausgeführt werden, die von .NET Core unterstützt werden, einschließlich macOS und Linux. Die Ausführung auf .NET Core ermöglicht plattformübergreifende Entwicklungs- und Hostingszenarien.

Im Vergleich dazu unterstützt die Runtimeversion 1.x nur das Entwickeln und Hosten im Azure-Portal oder auf Windows-Computern.

## <a name="languages"></a>Languages

Die Runtimeversion 2.x verwendet ein neues Modell für die Erweiterbarkeit von Sprachen. In Version 2.x müssen alle Funktionen in einer Funktions-App die gleiche Sprache verwenden. Die Sprache von Funktionen in einer Funktions-App wird beim Erstellen der App ausgewählt.

Experimentelle Sprachen von Azure Functions 1.x werden nicht auf die Verwendung des neuen Modells aktualisiert und werden daher in 2.x nicht unterstützt. Die folgende Tabelle zeigt, welche Programmiersprachen derzeit in den einzelnen Runtimeversionen unterstützt werden.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Weitere Informationen finden Sie unter [Unterstützte Sprachen](supported-languages.md).

## <a name="creating-1x-apps"></a>Ausführung in Version 1.x

Im Azure-Portal erstellte Funktions-Apps sind standardmäßig auf Version 2.x festgelegt. Verwenden Sie diese Runtimeversion nach Möglichkeit, da sie auch in Zukunft um neue Features erweitert wird. Bei Bedarf können Sie eine Funktions-App auch weiterhin in der Runtimeversion 1.x ausführen. Sie können die Runtimeversion nur ändern, nachdem Sie die Funktions-App erstellt haben, aber bevor Sie Funktionen hinzufügen. Informationen dazu, wie Sie die Runtimeversion auf 1.x festlegen, finden Sie unter [Anzeigen und Aktualisieren der aktuellen Runtimeversion](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Migrieren von 1.x zu 2.x

Sie können eine vorhandene App, die für die Runtimeversion 1.x geschrieben wurde, zur Version 2.x migrieren. Die meisten Änderungen, die Sie vornehmen müssen, betreffen die Sprachruntime, z.B. die Unterschiede in der C#-API zwischen .NET Framework 4.7 und .NET Core 2. Sie müssen auch sicherstellen, dass Ihr Code und Ihre Bibliotheken mit den ausgewählten Sprachruntimes kompatibel sind. Beachten Sie nicht zuletzt auch die unten genannten Änderungen an Triggern, Bindungen und Funktionen. Um ein optimales Migrationsergebnis zu erzielen, sollten Sie eine neue Funktions-App für Version 2.x erstellen und Ihren vorhandenen Funktionscode für Version 1.x in die neue App portieren.  

### <a name="changes-in-triggers-and-bindings"></a>Änderungen an Triggern und Bindungen

In Version 2.x müssen Sie die Erweiterungen für bestimmte Trigger und Bindungen installieren, die von den Funktionen in Ihrer App verwendet werden. Die einzigen Ausnahmen sind HTTP- und Timertrigger, für die keine Erweiterung erforderlich ist.  Weitere Informationen finden Sie unter [Registrieren und Installieren von Bindungserweiterungen](./functions-bindings-register.md).

Es gibt zwischen den Versionen auch einige Unterschiede in der `function.json` und den Attributen der Funktion. Die Event Hub-Eigenschaft `path` beispielsweise lautet jetzt `eventHubName`. Links zur Dokumentation für die einzelnen Bindungen finden Sie in der [Tabelle der vorhandenen Bindungen](#bindings).

### <a name="changes-in-features-and-functionality"></a>Änderungen bei Features und Funktionalität

Einige Features wurden in der neuen Version entfernt, aktualisiert oder ersetzt. Dieser Abschnitt erläutert die Änderungen, die Sie in Version 2.x im Vergleich zu Version 1.x bemerken werden.

In Version 2.x wurden die folgenden Änderungen vorgenommen:

* Schlüssel für aufrufende HTTP-Endpunkte werden immer verschlüsselt in Azure Blob Storage gespeichert. In Version 1.x wurden die Schlüssel standardmäßig in Azure File Storage gespeichert. Beim Durchführen eines Upgrades für eine App von Version 1.x auf Version 2.x werden vorhandene Geheimnisse, die sich in File Storage befinden, zurückgesetzt.

* Version 2.x der Runtime umfasst keine integrierte Unterstützung für Webhookanbieter. Diese Änderung wurde vorgenommen, um die Leistung zu verbessern. Sie können weiterhin HTTP-Trigger als Endpunkte für Webhooks verwenden.

* Die Hostkonfigurationsdatei (host.json) muss leer sein oder die Zeichenfolge `"version": "2.0"` enthalten.

* Zur Verbesserung der Überwachung wurde das WebJobs-Dashboard im Portal, das die Einstellung [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) verwendete, durch Azure Application Insights ersetzt – hierbei wird die Einstellung [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) verwendet. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

* Alle Funktionen in einer Funktions-App müssen die gleiche Sprache verwenden. Wenn Sie eine Funktions-App erstellen, müssen Sie einen Runtimestapel für die App auswählen. Der Runtimestapel wird durch den [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime)-Wert in den Anwendungseinstellungen angegeben. Diese Anforderung wurde hinzugefügt, um den Speicherbedarf und die Startzeit zu verbessern. Bei der lokalen Entwicklung müssen Sie diese Einstellung auch in die [Datei „local.settings.json“](functions-run-local.md#local-settings-file) einschließen.

* Das Standardzeitlimit für Funktionen in einem App Service-Plan wurde zu 30 Minuten geändert. Sie können das Zeitlimit mit der [functionTimeout](functions-host-json.md#functiontimeout)-Einstellung in der host.json-Datei manuell wieder zu „unbegrenzt“ ändern.

* HTTP-Parallelitätsdrosselungen sind standardmäßig für Verbrauchsplanfunktionen implementiert. Der Standardwert beträgt 100 gleichzeitige Anforderungen pro Instanz. Sie können diesen Wert in der [`maxConcurrentRequests`](functions-host-json.md#http)-Einstellung in der host.json-Datei ändern.

* Aufgrund der [Einschränkungen von .NET Core](https://github.com/Azure/azure-functions-host/issues/3414) wurde die Unterstützung für F#-Skriptfunktionen (FSX) entfernt. Kompilierte F#-Funktionen (FS) werden weiterhin unterstützt.

* Das URL-Format von Event Grid-Triggerwebhooks wurde zu `https://{app}/runtime/webhooks/{triggerName}` geändert.

### <a name="migrating-a-locally-developed-application"></a>Migrieren einer lokal entwickelten Anwendung

Möglicherweise verfügen Sie über vorhandene Funktions-App-Projekte, die Sie lokal mit der Runtimeversion 1.x entwickelt haben. Um ein Upgrade auf Version 2.x durchführen zu können, müssen Sie ein lokales Funktions-App-Projekt mit Version 2.x erstellen und Ihren vorhandenen Code in die neue App portieren. Sie könnten das vorhandene Projekt und den vorhandenen Code auch manuell aktualisieren – in einer Art „direktem Upgrade“. Allerdings gibt es eine Vielzahl weiterer Verbesserungen in Version 2.x gegenüber Version 1.x, die Sie dann auch noch anwenden müssten. Beispielsweise wurde das Debuggingobjekt in C# von `TraceWriter` zu `ILogger` geändert. Indem Sie ein neues Projekt mit Version 2.x erstellen, stehen Ihnen die aktualisierten Funktionen basierend auf den neuesten Vorlagen der Version 2.x direkt zur Verfügung.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio-Runtimeversionen

In Visual Studio wählen Sie die Runtimeversion beim Erstellen eines Projekts aus. Azure Functions-Tools für Visual Studio unterstützen beide Hauptversionen der Runtime. Beim Debuggen und Veröffentlichen wird die richtige Version verwendet, basierend auf den Projekteinstellungen. Die Versionseinstellungen sind in der `.csproj`-Datei in den folgenden Einstellungen definiert:

##### <a name="version-1x"></a>Version 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Version 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Wenn Sie Ihr Projekt debuggen oder veröffentlichen, wird die richtige Version der Runtime verwendet.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code und Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) werden für die Entwicklung über die Befehlszeile und auch von der [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code verwendet. Um für Version 2.x zu entwickeln, installieren Sie Version 2.x der Core Tools. Für die Entwicklung für Version 1.x ist Version 1.x der Core Tools erforderlich. Weitere Informationen finden Sie unter [Installieren der Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Für die Visual Studio Code-Entwicklung müssen Sie möglicherweise auch die Benutzereinstellung für die `azureFunctions.projectRuntime` entsprechend der installierten Version der Tools aktualisieren.  Diese Einstellung aktualisiert auch die Vorlagen und Sprachen, die während der Erstellung von Funktions-Apps verwendet werden.

### <a name="changing-version-of-apps-in-azure"></a>Ändern der Version von Apps in Azure

Welche Version der Functions-Runtime von veröffentlichten Apps in Azure verwendet wird, wird durch die [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version)-Anwendungseinstellung bestimmt. Der Wert `~2` gilt für die Runtimeversion 2.x, der Wert `~1` für die Version 1.x. Ändern Sie diese Einstellung nicht unüberlegt, da sehr wahrscheinlich weitere Änderungen an App-Einstellungen sowie am Code in Ihren Funktionen erforderlich sind. Informationen zur empfohlenen Methode für die Migration Ihrer Funktions-App zu einer anderen Runtimeversion finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md).

## <a name="bindings"></a>Bindungen

Die Runtimeversion 2.x verwendet ein neues [Modell für die Erweiterbarkeit von Bindungen](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview), das folgende Vorteile bietet:

* Unterstützung für Bindungserweiterungen von Drittanbietern.

* Entkoppeln von Runtime und Bindungen. Mit dieser Änderung können Bindungserweiterungen versioniert und unabhängig freigegeben werden. Sie können z.B. ein Upgrade auf eine Version einer Erweiterung durchführen, das auf einer neueren Version des zugrunde liegenden SDKs basiert.

* Eine schlankere Ausführungsumgebung, in der nur die tatsächlich verwendeten Bindungen bekannt sind und von der Runtime geladen werden.

Mit Ausnahme von HTTP- und Timertriggern müssen alle Bindungen explizit zum Funktions-App-Projekt hinzugefügt oder im Portal registriert werden. Weitere Informationen finden Sie unter [Registrieren von Bindungserweiterungen](./functions-bindings-expressions-patterns.md).

Die folgende Tabelle zeigt, welche Bindungen in den einzelnen Runtimeversionen unterstützt werden.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Lokales Codieren und Testen von Azure Functions](functions-run-local.md)
* [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md)
* [Versionshinweise](https://github.com/Azure/azure-functions-host/releases)
