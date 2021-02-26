---
title: Entwickeln von Azure Functions mithilfe von Visual Studio Code
description: In diesem Artikel erhalten Sie Informationen über das Entwickeln und Testen von Azure Functions mithilfe der Azure Functions-Erweiterung für Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: d4353e6be313d61716933879efa930e22472781b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493944"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Entwickeln von Azure Functions mithilfe von Visual Studio Code

Mit der [Azure Functions-Erweiterung für Visual Studio Code] können Sie Funktionen lokal entwickeln und in Azure bereitstellen. Wenn Sie zum ersten Mal mit Azure Functions arbeiten, finden Sie unter [Einführung in Azure Functions](functions-overview.md) weitere Informationen.

Die Azure Functions-Erweiterung bietet folgende Vorteile:

* Bearbeiten, Erstellen und Ausführen von Funktionen auf Ihrem lokalen Entwicklungscomputer.
* Veröffentlichen von Azure Functions-Projekten direkt in Azure.
* Schreiben von Funktionen in verschiedenen Sprachen, während Sie von allen Vorteilen der Entwicklung mit Visual Studio Code profitieren.

Die Erweiterung kann mit den folgenden Sprachen verwendet werden, die ab Version 2.x der Azure Functions-Runtime unterstützt werden:

* [C# kompiliert](functions-dotnet-class-library.md)
* [C# Skript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Erfordert, dass Sie [C#-Skript als Standardsprache des Projekts](#c-script-projects) festlegen.

In diesem Artikel stehen Beispiele derzeit nur für JavaScript- (Node.js) und C#-Klassenbibliotheksfunktionen zur Verfügung.  

Dieser Artikel erläutert detailliert, wie Sie die Azure Functions-Erweiterung verwenden, um Funktionen zu entwickeln und in Azure zu veröffentlichen. Bevor Sie diesen Artikel lesen, sollten Sie [Ihre erste Funktion mit Visual Studio Code erstellen](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Kombinieren Sie lokale Entwicklung und Portalentwicklung nicht in einer Funktions-App. Bei der Veröffentlichung aus einem lokalen Projekt in einer Funktions-App überschreibt der Bereitstellungsprozess alle Funktionen, die Sie im Portal entwickelt haben.

## <a name="prerequisites"></a>Voraussetzungen

Zum Installieren und Ausführen der [Azure Functions-Erweiterung][Azure Functions-Erweiterung für Visual Studio Code] müssen die folgenden Anforderungen erfüllt werden:

* [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms) installiert.

* Ein aktives Azure-Abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Weitere Ressourcen, die Sie benötigen, wie beispielsweise ein Azure-Speicherkonto, werden in Ihrem Abonnement erstellt, wenn Sie [mithilfe von Visual Studio Code veröffentlichen](#publish-to-azure). 

### <a name="run-local-requirements"></a>Anforderungen für die lokale Ausführung

Diese Voraussetzungen sind nur erforderlich, wenn Sie Ihre [Funktionen lokal ausführen und debuggen](#run-functions-locally). Sie sind nicht für das Erstellen oder Veröffentlichen von Projekten in Azure Functions erforderlich.

# <a name="c"></a>[C\#](#tab/csharp)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Version 2 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

+ [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code 

+ [.NET Core CLI-Tools](/dotnet/core/tools/?tabs=netcore2x)  

# <a name="java"></a>[Java](#tab/java)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Version 2 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

+ [Debugger für Java-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) empfohlen. Informationen zu anderen unterstützten Versionen finden Sie unter [Java-Versionen](functions-reference-java.md#java-versions).

+ [Maven 3 oder höher](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Version 2 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

+ [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (Empfehlung: 10.14.1). Verwenden Sie den Befehl `node --version`, um Ihre Version zu überprüfen. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Version 2 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) empfohlen. Versionsinformationen finden Sie unter [PowerShell-Versionen](functions-reference-powershell.md#powershell-versions).

+ [.NET Core 3.1 Runtime](https://www.microsoft.com/net/download) und [.NET Core 2.1 Runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [PowerShell-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)  

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) Version 2 oder höher. Das Core Tools-Paket wird für Sie automatisch heruntergeladen und installiert, wenn Sie das Projekt lokal starten. Die Core Tools beinhalten die gesamte Azure Functions-Runtime, daher können Download und Installation einige Zeit in Anspruch nehmen.

+ [Python 3.x](https://www.python.org/downloads/). Versionsinformationen finden Sie unter den von der Azure Functions-Runtime unterstützten [Python-Versionen](functions-reference-python.md#python-version).

+ [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python) für Visual Studio Code

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Erstellen eines Azure Functions-Projekts

Mithilfe der Functions-Erweiterung können Sie zugleich mit Ihrer ersten Funktion ein Funktions-App-Projekt erstellen. Die folgenden Schritte zeigen, wie Sie eine über HTTP ausgelöste Funktion in einem neuen Functions-Projekt erstellen. [HTTP-Trigger](functions-bindings-http-webhook.md) ist die einfachste Funktionstriggervorlage und eignet sich daher gut zur Veranschaulichung.

1. Wählen Sie in **Azure Functions** das Symbol zum **Erstellen einer Funktion** aus:

    ![Erstellen einer Funktion](./media/functions-develop-vs-code/create-function.png)

1. Wählen Sie den Ordner für Ihr Funktions-App-Projekt und dann **wählen Sie eine Sprache für Ihr Funktions-App-Projekt** aus.

1. Wählen Sie die Funktionsvorlage **HTTP-Trigger** aus. Sie können sich aber auch alternativ für **Vorerst überspringen** entscheiden, um ein Projekt ohne eine Funktion zu erstellen. Sie können später jederzeit [Ihrem Projekt eine Funktion hinzufügen](#add-a-function-to-your-project).

    ![Auswählen der Vorlage für den HTTP-Trigger](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Geben Sie **HttpExample** als Funktionsnamen ein und drücken Sie die EINGABETASTE, und wählen Sie dann die Autorisierung **Funktion** aus. Für diese Autorisierungsebene müssen Sie beim Aufrufen des Funktionsendpunkts einen [Funktionsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) angeben.

    ![Auswählen von Funktionsautorisierung](./media/functions-develop-vs-code/create-function-auth.png)

    Eine Funktion wird in Ihrer gewählten Sprache und in der Vorlage für eine über HTTP ausgelöste Funktion erstellt.

    ![Über HTTP ausgelöst Funktionsvorlage in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Generierte Projektdateien

Die Projektvorlage erstellt ein Projekt in Ihrer gewählten Sprache und installiert die erforderlichen Abhängigkeiten. Für jede Sprache weist das neue Projekt die folgenden Dateien auf:

* **host.json**: Ermöglicht das Konfigurieren des Functions-Hosts. Diese Einstellungen gelten, wenn Sie Funktionen lokal ausführen und sie in Azure ausführen. Weitere Informationen finden Sie in der [host.json-Referenz](functions-host-json.md).

* **local.settings.json**: Behält Einstellungen beim lokalen Ausführen von Funktionen bei. Diese Einstellungen werden nur beim lokalen Ausführen von Funktionen verwendet. Weitere Informationen finden Sie unter [Datei für lokale Einstellungen](#local-settings-file).

    >[!IMPORTANT]
    >Da die Datei „local.settings.json“ Geheimnisse enthalten kann, müssen Sie sie aus der Quellcodeverwaltung Ihres Projekts ausschließen.

Abhängig von Ihrer Sprache werden diese anderen Dateien erstellt:

# <a name="c"></a>[C\#](#tab/csharp)

* [Klassenbibliotheksdatei „HttpExample.cs“](functions-dotnet-class-library.md#functions-class-library-project) zur Implementierung der Funktion.

# <a name="java"></a>[Java](#tab/java)

+ Eine Datei „pom.xml“ im Stammordner, in der die Projekt- und Bereitstellungsparameter definiert sind, einschließlich der Projektabhängigkeiten und der [Java-Version](functions-reference-java.md#java-versions). Die Datei „pom.xml“ enthält darüber hinaus Informationen zu den Azure-Ressourcen, die während einer Bereitstellung erstellt werden.   

+ Eine Datei [Functions.java](functions-reference-java.md#triggers-and-annotations) in Ihrem src-Pfad, die die Funktion implementiert

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Eine Datei „package.json“ im Stammordner.

* Einen Ordner „HttpExample“ mit der [Definitionsdatei „function.json“](functions-reference-node.md#folder-structure) und der [Datei „index.js“](functions-reference-node.md#exporting-a-function), eine Node.js-Datei mit dem Funktionscode.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Einen Ordner „HttpExample“ mit der [Definitionsdatei „function.json“](functions-reference-powershell.md#folder-structure) und der Datei „run.ps1“ mit dem Funktionscode
 
# <a name="python"></a>[Python](#tab/python)
    
* Eine Datei „requirements.txt“ mit den von Functions benötigten Paketen (auf Projektebene)
    
* Einen Ordner „HttpExample“ mit der Definitionsdatei [function.json](functions-reference-python.md#folder-structure) und der Datei „\_\_init\_\_.py“ mit dem Funktionscode

---

An diesem Punkt können Sie Ihrer Funktion [Eingabe- und Ausgabebindungen hinzufügen](#add-input-and-output-bindings). Ferner können Sie [Ihrem Projekt eine neue Funktion hinzufügen](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installieren von Bindungserweiterungen

Mit Ausnahme von HTTP- und Timertriggern werden Bindungen in Erweiterungspaketen implementiert. Sie müssen die Erweiterungspakete für die Trigger und Bindungen installieren, die sie benötigen. Der Vorgang der Installation der Bindungserweiterungen hängt von der Sprache Ihres Projekts ab.

# <a name="c"></a>[C\#](#tab/csharp)

Führen Sie den Befehl [dotnet add package](/dotnet/core/tools/dotnet-add-package) im Terminalfenster aus, um die Erweiterungspakete zu installieren, die Sie in Ihrem Projekt benötigen. Der folgende Befehl installiert die Azure Storage-Erweiterung, die Bindungen für Blob, Queue und Table Storage implementiert.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Hinzufügen einer Funktion zu Ihrem Projekt

Sie können einem Projekt eine neue Funktion hinzufügen, indem Sie eine der vordefinierten Functions-Triggervorlagen verwenden. Um einen neuen Funktionstrigger hinzuzufügen, drücken Sie F1, um die Befehlspalette zu öffnen. Suchen Sie dann nach dem Befehl **Azure Functions: Funktion erstellen**. Befolgen Sie die Anweisungen, um Ihren Triggertyp auszuwählen und die erforderlichen Attribute des Triggers zu definieren. Wenn eine Zugriffstaste oder eine Verbindungszeichenfolge erforderlich ist, damit Ihr Trigger eine Verbindung mit einem Dienst herstellen kann, bereiten Sie dies vor, bevor Sie den Funktionstrigger erstellen.

Die Ergebnisse dieser Aktion hängen von Ihrer Projektsprache ab:

# <a name="c"></a>[C\#](#tab/csharp)

Eine neue C#-Klassenbibliotheksdatei (CS) wird Ihrem Projekt hinzugefügt.

# <a name="java"></a>[Java](#tab/java)

Dem Projekt wird eine neue Java-Datei („.java“) hinzugefügt.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Im Projekt wird ein neuer Ordner erstellt. Er enthält eine neue Datei „function.json“ und die neue JavaScript-Codedatei.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Im Projekt wird ein neuer Ordner erstellt. Er enthält die neue Datei „function.json“ und die neue PowerShell-Codedatei.

# <a name="python"></a>[Python](#tab/python)

Im Projekt wird ein neuer Ordner erstellt. Er enthält die neue Datei „function.json“ und die neue Python-Codedatei.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Verbinden mit Diensten

Sie können Ihre Funktion mit anderen Azure-Diensten verbinden, indem Sie Eingabe- oder Ausgabebindungen hinzufügen. Bindungen verbinden Ihre Funktion mit anderen Diensten, ohne dass Sie entsprechenden Code schreiben müssen. Der Vorgang des Hinzufügens von Bindungen hängt von der Sprache Ihres Projekts ab. Weitere Informationen zu Bindungen finden Sie unter [Azure Functions-Trigger und Bindungskonzepte](functions-triggers-bindings.md).

Stellen Sie in den folgenden Beispielen eine Verbindung mit einer Speicherwarteschlange mit dem Namen `outqueue` her, wobei die Verbindungszeichenfolge für das Speicherkonto in der Anwendungseinstellung `MyStorageConnection` in „local.settings.json“ festgelegt wird.

# <a name="c"></a>[C\#](#tab/csharp)

Aktualisieren Sie die Funktionsmethode, um der `Run`-Methodendefinition den folgenden Parameter hinzuzufügen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Der Parameter `msg` ist ein `ICollector<T>`-Typ und stellt eine Sammlung von Nachrichten dar, die in eine Ausgabebindung geschrieben werden, wenn die Funktion abgeschlossen wird. Der folgende Code fügt der Sammlung eine Meldung hinzu:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Meldungen werden an die Warteschlange gesendet, wenn die Funktion abgeschlossen ist.

Weitere Informationen finden Sie in der Dokumentation im Referenzartikel zur [Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md?tabs=csharp). Weitere Informationen darüber, welche Bindungen einer Funktion hinzugefügt werden können, finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Aktualisieren Sie die Funktionsmethode, um der `Run`-Methodendefinition den folgenden Parameter hinzuzufügen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Der `msg`-Parameter ist vom Typ `OutputBinding<T>`. Dabei ist `T` eine Zeichenfolge, die in die Ausgabebindung geschrieben wird, wenn die Funktion abgeschlossen wird. Mit dem folgenden Code wird die Meldung in der Ausgabebindung festgelegt:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Diese Meldung wird an die Warteschlange gesendet, wenn die Funktion abgeschlossen ist.

Weitere Informationen finden Sie in der Dokumentation im Referenzartikel zur [Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md?tabs=java). Weitere Informationen darüber, welche Bindungen einer Funktion hinzugefügt werden können, finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

In Ihrem Funktionscode erfolgt der Zugriff auf die `msg`-Bindung über den `context`, wie das folgende Beispiel zeigt:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Diese Meldung wird an die Warteschlange gesendet, wenn die Funktion abgeschlossen ist.

Weitere Informationen finden Sie in der Dokumentation im Referenzartikel zur [Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md?tabs=javascript). Weitere Informationen darüber, welche Bindungen einer Funktion hinzugefügt werden können, finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Diese Meldung wird an die Warteschlange gesendet, wenn die Funktion abgeschlossen ist.

Weitere Informationen finden Sie in der Dokumentation im Referenzartikel zur [Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md?tabs=powershell). Weitere Informationen darüber, welche Bindungen einer Funktion hinzugefügt werden können, finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Fügen Sie in der `Main`-Definition den Ausgabeparameter `msg: func.Out[func.QueueMessage]` hinzu, damit die Definition wie im folgenden Beispiel aussieht:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Der folgende Code fügt der Ausgabewarteschlange Zeichenfolgendaten aus der Anforderung hinzu:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Diese Meldung wird an die Warteschlange gesendet, wenn die Funktion abgeschlossen ist.

Weitere Informationen finden Sie in der Dokumentation im Referenzartikel zur [Queue Storage-Ausgabebindung](functions-bindings-storage-queue-output.md?tabs=python). Weitere Informationen darüber, welche Bindungen einer Funktion hinzugefügt werden können, finden Sie unter [Hinzufügen von Bindungen zu einer vorhandenen Funktion in Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Visual Studio Code ermöglicht die Veröffentlichung Ihres Functions-Projekts direkt in Azure. Bei diesem Vorgang erstellen Sie eine Funktions-App und zugehörige Ressourcen in Ihrem Azure-Abonnement. Die Funktions-App bietet einen Ausführungskontext für Ihre Funktionen. Das Projekt wird verpackt und in der neuen Funktions-App in Ihrem Azure-Abonnement bereitgestellt.

Wenn Sie aus Visual Studio Code in einer neuen Funktions-App in Azure veröffentlichen, können Sie entweder einen schnellen Pfad zur Funktions-App erstellen, indem Sie Standardwerte übernehmen, oder Sie erstellen einen erweiterten Pfad, bei dem Sie mehr Kontrolle über die erstellten Remoteressourcen haben. 

Wenn Sie aus Visual Studio Code veröffentlichen, nutzen Sie die [ZIP Deploy](functions-deployment-technologies.md#zip-deploy)-Technologie. 

### <a name="quick-function-app-create"></a>Schnelle Erstellung von Funktions-Apps

Wenn Sie **+ Neue Funktions-App in Azure erstellen...** auswählen, generiert die Erweiterung automatisch Werte für die Azure-Ressourcen, die von ihrer Funktions-App benötigt werden. Diese Werte basieren auf dem von Ihnen ausgewählten Namen der Funktions-App. Ein Beispiel für die Verwendung von Standardwerten zum Veröffentlichen Ihres Projekts in einer neuen Funktions-App in Azure finden Sie im [Artikel zum Schnellstart für Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Wenn Sie für die erstellten Ressourcen aussagekräftige Namen bereitstellen möchten, müssen Sie den erweiterten Erstellungspfad auswählen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Veröffentlichen eines Projekts in einer neuen Funktions-App in Azure mit erweiterten Optionen

Mit den folgenden Schritten wird Ihr Projekt in einer neuen Funktions-App veröffentlicht, die mit erweiterten Erstellungsoptionen erstellt wurde:

1. Geben Sie in der Befehlspalette **Azure Functions: In Funktions-App bereitstellen** aus.

1. Wenn Sie nicht angemeldet sind, werden Sie aufgefordert, sich **bei Azure anzumelden**. Sie können auch ein **kostenloses Azure-Konto erstellen**. Nach der Anmeldung über den Browser wechseln Sie zurück zu Visual Studio Code.

1. Wenn Sie mehrere Abonnements besitzen, **wählen Sie ein Abonnement für die Funktions-App aus**, und wählen Sie dann **+ Neue Funktions-App erstellen... _Erweitert_** aus. Diese Option _Erweitert_ ermöglicht Ihnen eine bessere Kontrolle über die Ressourcen, die Sie in Azure erstellen. 

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen ein:

    | Prompt | Wert | BESCHREIBUNG |
    | ------ | ----- | ----------- |
    | Funktions-App in Azure auswählen | Neue Funktions-App in Azure erstellen | Geben Sie an der nächsten Eingabeaufforderung einen eindeutigen Namen ein, der Ihre neue Funktions-App identifiziert, und drücken Sie dann die EINGABETASTE. Gültige Zeichen für den Namen einer Funktions-App sind `a-z`, `0-9` und `-`. |
    | Betriebssystem auswählen | Windows | Die Funktions-App wird unter Windows ausgeführt. |
    | Hostingplan auswählen | Verbrauchsplan | Es wird serverloses [Hosting mit Verbrauchsplan](consumption-plan.md) verwendet. |
    | Auswählen einer Runtime für Ihre neue App | Ihre Projektsprache | Die Laufzeit muss mit dem Projekt übereinstimmen, das Sie veröffentlichen. |
    | Auswählen einer Ressourcengruppe für neue Ressourcen | Erstellen einer neuen Ressourcengruppe | Geben Sie an der nächsten Eingabeaufforderung einen Ressourcengruppennamen ein (etwa `myResourceGroup`), und drücken Sie dann die EINGABETASTE. Sie können auch eine vorhandene Ressourcengruppe auswählen. |
    | Auswählen eines Speicherkontos | Erstellen eines neuen Speicherkontos | Geben Sie an der nächsten Eingabeaufforderung einen global eindeutigen Namen für das neue Speicherkonto ein, das von Ihrer Funktions-App verwendet wird, und drücken Sie dann die EINGABETASTE. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Ziffern und Kleinbuchstaben enthalten. Sie können auch ein vorhandenes Konto auswählen. |
    | Auswählen eines Speicherorts für neue Ressourcen | region | Wählen Sie einen Standort in einer [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe anderer Dienste aus, auf die Ihre Funktionen zugreifen. |

    Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet. Wählen Sie in dieser Benachrichtigungen **Ausgabe anzeigen** aus, um die Erstellungs- und Bereitstellungsergebnisse (auch für die von Ihnen erstellten Azure-Ressourcen) anzuzeigen.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Abrufen der URL einer über HTTP ausgelösten Funktion in Azure

Um eine über HTTP ausgelöste Funktion von einem Client aus aufzurufen, benötigen Sie die URL der Funktion, wenn sie in Ihrer Funktions-App bereitgestellt ist. Diese URL beinhaltet alle erforderlichen Funktionstasten. Sie können die Erweiterung verwenden, um diese URLs für Ihre bereitgestellten Funktionen abzurufen. Wenn Sie nur die Remotefunktion in Azure ausführen möchten, nutzen Sie die Option [Funktion jetzt ausführen](#run-functions-in-azure) der Erweiterung.

1. Drücken Sie die F1-Taste, um die Befehlspalette zu öffnen. Suchen Sie dann den Befehl **Azure Functions: Funktions-URL kopieren**, und führen Sie ihn aus.

1. Folgen Sie den Aufforderungen, um Ihre Funktions-App in Azure und dann den spezifischen HTTP-Auslöser auszuwählen, den Sie aufrufen möchten.

Die Funktions-URL wird in die Zwischenablage kopiert, zusammen mit allen erforderlichen Schlüsseln, die mithilfe des Abfrageparameters `code` übergeben werden. Verwenden Sie ein HTTP-Tool, um POST-Anforderungen zu senden, oder einen Browser für GET-Anforderungen an die Remotefunktion.  

Beim Abrufen der URL der Funktionen in Azure verwendet die Erweiterung Ihr Azure-Konto, um automatisch die zum Starten der Funktion erforderlichen Schlüssel abzurufen. [Erfahren Sie mehr über Zugriffsschlüssel für Funktionen.](security-concepts.md#function-access-keys) Zum Starten von nicht über HTTP ausgelösten Funktionen muss der Administratorschlüssel verwendet werden.

## <a name="republish-project-files"></a>Erneutes Veröffentlichen von Projektdateien

Wenn Sie [Continuous Deployment](functions-continuous-deployment.md) einrichten, wird Ihre Funktions-App in Azure immer dann aktualisiert, wenn die Quelldateien am verbundenen Quellspeicherort aktualisiert werden. Wir empfehlen Continuous Deployment. Sie können Ihre Projektdateiaktualisierungen aber auch erneut aus Visual Studio Code veröffentlichen.

> [!IMPORTANT]
> Beim Veröffentlichen in einer vorhandenen Funktions-App wird der Inhalt dieser App in Azure überschrieben.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Ausführen von Funktionen

Mit der Azure Functions-Erweiterung können Sie einzelne Funktionen ausführen – in Ihrem Projekt auf dem lokalen Entwicklungscomputer oder in Ihrem Azure-Abonnement. 

Für Funktionen, die über HTTP ausgelöst werden, ruft die Erweiterung den HTTP-Endpunkt auf. Bei anderen Arten von Triggern werden Admin-APIs aufgerufen, um die Funktion zu starten. Der Meldungstext der Anforderung, die an die Funktion gesendet wird, hängt vom Typ des Auslösers ab. Wenn ein Trigger Testdaten erfordert, werden Sie aufgefordert, Daten in einem bestimmten JSON-Format einzugeben.

### <a name="run-functions-in-azure"></a>Ausführen von Funktionen in Azure

So führen Sie eine Funktion in Azure aus Visual Studio Code aus 

1. Geben Sie in der Befehlspalette **Azure Functions: Funktion jetzt ausführen** ein, und wählen Sie Ihr Azure-Abonnement aus. 

1. Wählen Sie Ihre Funktions-App in Azure in der Liste aus. Wenn Ihre Funktions-App nicht angezeigt wird, stellen Sie sicher, dass Sie beim richtigen Abonnement angemeldet sind. 

1. Wählen Sie die gewünschte Funktion in der Liste aus, und geben Sie den Meldungstext der Anforderung in **Enter request body** (Anforderungstext eingeben) ein. Drücken Sie die EINGABETASTE, um diese Anforderungsmeldung an Ihre Funktion zu senden. Der Standardtext in **Enter request body** (Anforderungstext eingeben) sollte das Format des Texts angeben. Wenn Ihre Funktions-App keine Funktionen enthält, wird ein Benachrichtigungsfehler mit dieser Fehlermeldung angezeigt. 

1. Wenn die Funktion in Azure ausgeführt wird und eine Antwort zurückgibt, wird in Visual Studio Code eine Benachrichtigung ausgelöst.
 
Sie können Ihre Funktion auch über den Bereich **Azure: Funktionen** ausführen. Klicken Sie dazu mit der rechten Maustaste (STRG + Klicken auf einem Mac) auf die Funktion, die Sie in Ihrer Funktions-App in Ihrem Azure-Abonnement ausführen möchten, und wählen Sie **Funktion jetzt ausführen** aus.

Beim Ausführen der Funktionen in Azure verwendet die Erweiterung Ihr Azure-Konto, um automatisch die zum Starten der Funktion erforderlichen Schlüssel abzurufen. [Erfahren Sie mehr über Zugriffsschlüssel für Funktionen.](security-concepts.md#function-access-keys) Zum Starten von nicht über HTTP ausgelösten Funktionen muss der Administratorschlüssel verwendet werden.

### <a name="run-functions-locally"></a>Lokales Ausführen von Funktionen

Die lokale-Runtime ist die gleiche Runtime, die Ihre Funktions-App in Azure hostet. Lokale Einstellungen werden aus der [local.settings.json-Datei](#local-settings-file) gelesen. Um Ihr Functions-Projekt lokal ausführen zu können, müssen einige [zusätzliche Anforderungen](#run-local-requirements) erfüllt sein.

#### <a name="configure-the-project-to-run-locally"></a>Konfigurieren des Projekts für die lokale Ausführung

Die Functions-Runtime verwendet intern ein Azure Storage-Konto für alle Triggertypen außer HTTP und Webhooks. Daher müssen Sie den Schlüssel **Values.AzureWebJobsStorage** auf die Verbindungszeichenfolge eines gültigen Azure Storage-Kontos festlegen.

In diesem Abschnitt wird die [Azure Storage-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) mit [Azure Storage-Explorer](https://storageexplorer.com/) verwendet, um eine Verbindung mit dem Speicher herzustellen und seine Verbindungszeichenfolge abzurufen.

So legen Sie die Speicherkonto-Verbindungszeichenfolge fest:

1. Öffnen Sie in Visual Studio den **Cloud-Explorer**, erweitern Sie **Speicherkonto** > **Ihr Speicherkonto**, wählen Sie **Eigenschaften** aus, und kopieren Sie dann den Wert von **Primäre Verbindungszeichenfolge**.

2. Öffnen Sie in Ihrem Projekt die Datei „local.settings.json“, und legen Sie den Wert des Schlüssels **AzureWebJobsStorage** auf die kopierte Verbindungszeichenfolge fest.

3. Wiederholen Sie den vorherigen Schritt zum Hinzufügen von eindeutigen Schlüsseln zum **Values**-Array für alle anderen Verbindungen, die von Ihren Funktionen benötigt werden.

Weitere Informationen finden Sie unter [Datei für lokale Einstellungen](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Lokales Debuggen von Funktionen  

Drücken Sie F5, um Ihre Funktionen zu debuggen. Wenn Sie [Core Tools][Azure Functions Core Tools] noch nicht heruntergeladen haben, werden Sie jetzt dazu aufgefordert. Wenn Core Tools installiert sind und ausgeführt werden, wird die Ausgabe im Terminal angezeigt. Dies entspricht dem Ausführen des Core Tools-Befehls `func host start` im Terminal, jedoch mit zusätzlichen Buildaufgaben und einem angefügten Debugger.  

Wenn das Projekt ausgeführt wird, können Sie Ihre Funktionen mit dem Feature **Funktion jetzt ausführen** der Erweiterung auslösen, wie dies auch bei der Bereitstellung in Azure geschehen würde. Bei der Ausführung des Projekts im Debugmodus werden in Visual Studio Code erwartungsgemäß Breakpoints erreicht. 

1. Geben Sie in der Befehlspalette **Azure Functions: Funktion jetzt ausführen** ein, und wählen Sie **Lokales Projekt** aus. 

1. Wählen Sie die gewünschte Funktion in Ihrem Projekt aus, und geben Sie den Meldungstext der Anforderung in **Enter request body** (Anforderungstext eingeben) ein. Drücken Sie die EINGABETASTE, um diese Anforderungsmeldung an Ihre Funktion zu senden. Der Standardtext in **Enter request body** (Anforderungstext eingeben) sollte das Format des Texts angeben. Wenn Ihre Funktions-App keine Funktionen enthält, wird ein Benachrichtigungsfehler mit dieser Fehlermeldung angezeigt. 

1. Wenn die Funktion lokal ausgeführt wird und die Antwort empfangen wurde, wird in Visual Studio Code eine Benachrichtigung ausgelöst. Informationen zur Funktionsausführung werden im Bereich **Terminal** angezeigt.

Das lokale Ausführen von Funktionen erfordert keine Verwendung von Schlüsseln. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standardmäßig werden diese Einstellungen nicht automatisch migriert, wenn das Projekt in Azure veröffentlicht wird. Nachdem die Veröffentlichung abgeschlossen ist, besteht die Option, Einstellungen aus „local.settings.json“ in Ihrer Funktions-App in Azure zu veröffentlichen. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungseinstellungen](#publish-application-settings).

Werte in **ConnectionStrings** werden niemals veröffentlicht.

Die Werte für Funktionsanwendungseinstellungen können auch im Code als Umgebungsvariablen gelesen werden. Weitere Informationen finden Sie in den Abschnitten „Umgebungsvariablen“ dieser sprachspezifischen Referenzartikel:

* [Vorkompilierter C#-Code](functions-dotnet-class-library.md#environment-variables)
* [C#-Skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Anwendungseinstellungen in Azure

Die Einstellungen in der Datei „local.settings.json“ in Ihrem Projekt sollten die gleichen wie die Anwendungseinstellungen in der Funktions-App in Azure sein. Alle Einstellungen, die Sie „local.settings.json“ hinzufügen, müssen auch der Funktions-App in Azure hinzugefügt werden. Diese Einstellungen werden nicht automatisch hochgeladen, wenn Sie das Projekt veröffentlichen. Analog müssen alle Einstellungen, die Sie in Ihrer Funktions-App [im Portal](functions-how-to-use-azure-function-app-settings.md#settings) erstellen, in Ihr lokales Projekt heruntergeladen werden.

### <a name="publish-application-settings"></a>Veröffentlichen von Anwendungseinstellungen

Die einfachste Möglichkeit, die erforderlichen Einstellungen in Ihrer Funktions-App in Azure zu veröffentlichen, besteht darin, den Link **Einstellungen hochladen** zu verwenden, der nach der Veröffentlichung Ihres Projekts angezeigt wird:

![Anwendungseinstellungen hochladen](./media/functions-develop-vs-code/upload-app-settings.png)

Sie können Einstellungen auch mit dem Befehl **Azure Functions: Lokale Einstellung hochladen** in der Befehlspalette veröffentlichen. Sie können den Anwendungseinstellungen in Azure individuelle Einstellungen hinzufügen, indem Sie den Befehl **Azure Functions: Neue Einstellung hinzufügen** verwenden.

> [!TIP]
> Achten Sie darauf, Ihre local.settings.json-Datei zu speichern, bevor Sie sie veröffentlichen.

Wenn die lokale Datei verschlüsselt ist, wird sie entschlüsselt, veröffentlicht und erneut verschlüsselt. Wenn Einstellungen vorhanden sind, bei denen die Werte an den beiden Speicherorten einen Konflikt verursachen, werden Sie aufgefordert, die Vorgehensweise auszuwählen.

Anzeigen der vorhandenen App-Einstellungen im Bereich **Azure: Functions** durch Aufklappen Ihres Abonnements, Ihrer Funktions-App und der **Anwendungseinstellungen**.

![Anzeigen der Einstellungen der Funktions-App in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Herunterladen der Einstellungen von Azure

Wenn Sie Anwendungseinstellungen in Azure erstellt haben, können Sie sie in Ihre Datei „local.settings.json“ herunterladen, indem Sie den Befehl **Azure Functions: Remoteeinstellungen herunterladen** auszuwählen.

Wie beim Hochladen wird die lokale Datei, wenn sie verschlüsselt ist, entschlüsselt, veröffentlicht und erneut verschlüsselt. Wenn Einstellungen vorhanden sind, bei denen die Werte an den beiden Speicherorten einen Konflikt verursachen, werden Sie aufgefordert, die Vorgehensweise auszuwählen.

## <a name="monitoring-functions"></a>Überwachen von Funktionen

Bei der [lokalen Ausführung](#run-functions-locally) von Funktionen werden Protokolldaten an die Terminalkonsole gestreamt. Sie können Protokolldaten auch abrufen, wenn Ihr Functions-Projekt in einer Funktions-App in Azure ausgeführt wird. Sie können entweder eine Verbindung mit Streamingprotokollen in Azure herstellen, um Protokolldaten nahezu in Echtzeit anzuzeigen, oder Application Insights aktivieren, um ein vollständigeres Bild vom Verhalten Ihrer Funktions-App zu erhalten.

### <a name="streaming-logs"></a>Streamingprotokolle

Beim Entwickeln einer Anwendung ist es häufig nützlich, Protokollinformationen nahezu in Echtzeit anzuzeigen. Sie können einen Datenstrom von Protokolldateien anzeigen, die von Ihren Funktionen generiert werden. Die folgende Ausgabe ist ein Beispiel für Streamingprotokolle für eine Anforderung an eine über HTTP ausgelöste Funktion:

![Streamingprotokollausgabe für HTTP-Trigger](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Weitere Informationen finden Sie unter [Streamingprotokolle](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Streamingprotokolle unterstützen nur eine einzige Instanz des Azure Functions-Hosts. Wenn Ihre Funktion auf mehrere Instanzen skaliert wird, werden Daten aus anderen Instanzen nicht im Protokollstream angezeigt. Der [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights unterstützt mehrere Instanzen. Obwohl sie auch in nahezu Echtzeit erfolgt, basiert die Streamanalyse auf [Stichprobendaten](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Es wird empfohlen, die Ausführung Ihrer Funktionen durch die Integration Ihrer Funktions-App in Application Insights zu überwachen. Wenn Sie eine Funktions-App im Azure-Portal erstellen, erfolgt diese Integration standardmäßig. Wenn Sie Ihre Funktions-App während der Visual Studio-Veröffentlichung erstellen, müssen Sie Application Insights selbst integrieren. Lesen Sie die Anleitung unter [Aktivieren der Application Insights-Integration](configure-monitoring.md#enable-application-insights-integration).

Weitere Informationen zum Überwachen mithilfe von Application Insights finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\#-Skriptprojekte

Standardmäßig werden alle C#-Projekte als [C#-kompilierte Klassenbibliotheksprojekte](functions-dotnet-class-library.md) erstellt. Wenn Sie stattdessen die Arbeit mit C#-Skriptprojekten vorziehen, müssen Sie „C#Script“ als Standardsprache in den Einstellungen der Azure Functions-Erweiterung auswählen:

1. Wählen Sie **Datei** > **Einstellungen** > **Einstellungen** aus.

1. Navigieren Sie zu **Benutzereinstellungen** > **Erweiterungen** > **Azure Functions**.

1. Wählen Sie **C#Script** in **Azure-Funktion: Projektsprache** aus.

Nachdem Sie diese Schritte ausgeführt haben, enthalten Aufrufe an die zugrunde liegenden Core Tools die Option `--csx`, die C#Script-Projektdateien (CSX-Dateien) generiert und veröffentlicht. Wenn Sie diese Standardsprache angegeben haben, sind alle erstellten Projekte standardmäßig C#Script-Projekte. Sie werden nicht zur Auswahl einer Projektsprache aufgefordert, wenn eine Standardsprache festgelegt ist. Um Projekte in anderen Sprachen zu erstellen, müssen Sie diese Einstellung ändern oder sie aus der Datei „settings.json“ des Benutzers entfernen. Nachdem Sie diese Einstellung entfernt haben, werden Sie beim Erstellen eines Projekts erneut aufgefordert, Ihre Sprache auszuwählen.

## <a name="command-palette-reference"></a>Befehlspalettenreferenz

Die Azure Functions-Erweiterung bietet eine nützliche grafische Benutzeroberfläche im Bereich für die Interaktion mit Ihren Funktions-Apps in Azure. Die gleiche Funktionalität steht auch in Form von Befehlen in der Befehlspalette (F1) zur Verfügung. Diese Azure Functions Befehle sind verfügbar:

|Azure Functions-Befehl  | BESCHREIBUNG  |
|---------|---------|
|**Neue Einstellungen hinzufügen**  |  Erstellt eine neue Anwendungseinstellung in Azure. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungseinstellungen](#publish-application-settings). Möglicherweise müssen Sie [diese Einstellung in Ihre lokalen Einstellungen herunterladen](#download-settings-from-azure). |
| **Bereitstellungsquelle konfigurieren** | Verbindet Ihre Funktions-App in Azure mit einem lokalen Git-Repository. Weitere Informationen finden Sie unter [Continuous Deployment für Azure Functions](functions-continuous-deployment.md). |
| **Mit GitHub-Repository verbinden** | Verbindet Ihre Funktions-App mit einem GitHub-Repository. |
| **Kopieren der Funktions-URL** | Ruft die Remote-URL einer über HTTP ausgelösten Funktion ab, die in Azure ausgeführt wird. Weitere Informationen finden Sie unter [Abrufen der URL der bereitgestellten Funktion](#get-the-url-of-the-deployed-function). |
| **Funktions-App in Azure erstellen** | Erstellt eine neue Funktions-App in Ihrem Abonnement in Azure. Weitere Informationen finden Sie im Abschnitt zum [Veröffentlichen in einer neuen Funktions-App in Azure](#publish-to-azure).        |
| **Einstellungen entschlüsseln** | Entschlüsselt [lokale Einstellungen](#local-settings-file), die mithilfe von **Azure Functions: Einstellungen verschlüsseln** verschlüsselt wurden.  |
| **Funktions-App löschen** | Entfernt eine Funktions-App aus Ihrem Abonnement in Azure. Wenn keine anderen Apps im App Service-Plan vorhanden sind, wird Ihnen die Option eingeräumt, diesen ebenfalls zu löschen. Andere Ressourcen, etwa Speicherkonten und Ressourcengruppen, werden nicht gelöscht. Um alle Ressourcen zu entfernen, sollten Sie stattdessen [die Ressourcengruppe löschen](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Ihr lokales Projekt ist nicht betroffen. |
|**Funktion löschen**  | Entfernt eine vorhandene Funktion aus einer Funktions-App in Azure. Da sich diese Löschung nicht auf Ihr lokales Projekt auswirkt, ziehen Sie in Erwägung, stattdessen die Funktion lokal zu entfernen und dann [das Projekt neu zu veröffentlichen](#republish-project-files). |
| **Proxy löschen** | Entfernt einen Azure Functions-Proxy aus Ihrer Funktions-App in Azure. Weitere Informationen über Proxys finden Sie unter [Arbeiten mit Azure Functions-Proxys](functions-proxies.md). |
| **Einstellung löschen** | Löscht eine Einstellung einer Funktions-App in Azure. Dieser Löschvorgang wirkt sich nicht auf Einstellungen in Ihrer Datei „local.settings.json“ aus. |
| **Vom Repository trennen**  | Entfernt die Verbindung für [Continuous Deployment](functions-continuous-deployment.md) zwischen einer Funktions-App in Azure und einem Repository für die Quellcodeverwaltung. |
| **Remoteeinstellungen herunterladen** | Lädt Einstellungen aus der gewählten Funktions-App in Azure in Ihre local.settings.json-Datei herunter. Wenn die lokale Datei verschlüsselt ist, wird sie entschlüsselt, aktualisiert und erneut verschlüsselt. Wenn Einstellungen vorhanden sind, bei denen die Werte an den beiden Speicherorten einen Konflikt verursachen, werden Sie aufgefordert, die Vorgehensweise auszuwählen. Achten Sie darauf, Änderungen an Ihrer Datei „local.settings.json“ zu speichern, bevor Sie diesen Befehl ausführen. |
| **Einstellungen bearbeiten** | Ändert den Wert einer vorhandenen Funktions-App in Azure. Dieser Befehl wirkt sich nicht auf Einstellungen in Ihrer Datei „local.settings.json“ aus.  |
| **Einstellungen verschlüsseln** | Verschlüsselt einzelne Elemente im `Values`-Array in den [lokalen Einstellungen](#local-settings-file). In dieser Datei wird außerdem `IsEncrypted` auf `true` festgelegt. Damit wird angegeben, dass die lokale Runtime Einstellungen entschlüsselt, bevor sie verwendet werden. Verschlüsseln Sie lokale Einstellungen, um die Gefahr des Verlusts von wertvollen Informationen zu verringern. In Azure werden Anwendungseinstellungen immer verschlüsselt gespeichert. |
| **Funktion jetzt ausführen** | Startet eine Funktion mithilfe der Admin-APIs manuell. Dieser Befehl dient zum Testen – sowohl lokal während des Debuggens und für Funktionen, die in Azure ausgeführt werden. Wenn eine Funktion in Azure ausgelöst wird, ruft die Erweiterung zunächst automatisch einen Administratorschlüssel ab, mit dem die Remoteadmin-APIs aufgerufen werden, die Funktionen in Azure starten. Der Meldungstext, der an die API gesendet wird, ist vom Typ des Auslösers abhängig. Für Zeitgebertrigger müssen Sie keine Daten übergeben. |
| **Projekt für die Verwendung mit VS Code initialisieren** | Fügt einem vorhandenen Functions-Projekt die erforderlichen Visual Studio Code-Projektdateien hinzu. Verwenden Sie diesen Befehl, um mit einem Projekt zu arbeiten, das Sie mithilfe von Core Tools erstellt haben. |
| **Azure Functions Core Tools installieren oder aktualisieren** | Installiert oder aktualisiert die [Azure Functions Core Tools], die für die lokale Ausführung von Funktionen verwendet werden. |
| **Erneute Bereitstellung**  | Ermöglicht die erneute Bereitstellung von Projektdateien aus einem verbundenen Git-Repository in einer bestimmten Bereitstellung in Azure. Um lokale Updates aus Visual Studio Code erneut zu veröffentlichen, [veröffentlichen Sie Ihr Projekt erneut](#republish-project-files). |
| **Einstellungen umbenennen** | Ändert den Schlüsselnamen einer vorhandenen Funktions-App-Einstellung in Azure. Dieser Befehl wirkt sich nicht auf Einstellungen in Ihrer Datei „local.settings.json“ aus. Nach dem Umbenennen von Einstellungen in Azure sollten Sie [diese Änderungen in das lokale Projekt herunterladen](#download-settings-from-azure). |
| **Neu starten** | Führt einen Neustart der Funktions-App in Azure aus. Beim Bereitstellen von Updates wird ebenfalls ein Neustart der Funktions-App durchgeführt. |
| **AzureWebJobsStorage festlegen**| Legt den Wert der Anwendungseinstellung `AzureWebJobsStorage` fest. Diese Einstellung ist für Azure Functions erforderlich. Sie wird festgelegt, wenn eine Funktions-App in Azure erstellt wird. |
| **Starten** | Startet eine beendete Funktions-App in Azure. |
| **Streamingprotokolle starten** | Startet die Streamingprotokolle für die Funktions-App in Azure. Verwenden Sie Streamingprotokolle während der Remoteproblembehandlung in Azure, wenn Sie Protokollierungsinformationen nahezu in Echtzeit anzeigen müssen. Weitere Informationen finden Sie unter [Streamingprotokolle](#streaming-logs). |
| **Beenden** | Beendet eine in Azure ausgeführte Funktions-App. |
| **Streamingprotokolle beenden** | Beendet die Streamingprotokolle für die Funktions-App in Azure. |
| **Toggle as Slot Setting** (Als Sloteinstellung umschalten) | Stellt bei Aktivierung sicher, dass eine Anwendungseinstellung für einen bestimmten Bereitstellungsslot persistent gespeichert wird. |
| **Azure Functions Core Tools deinstallieren** | Entfernt Azure Functions Core Tools. Dies ist für die Erweiterung erforderlich. |
| **Lokale Einstellungen hochladen** | Lädt Einstellungen aus Ihrer local.settings.json-Datei in die ausgewählte Funktions-App in Azure hoch. Wenn die lokale Datei verschlüsselt ist, wird sie entschlüsselt, hochgeladen und erneut verschlüsselt. Wenn Einstellungen vorhanden sind, bei denen die Werte an den beiden Speicherorten einen Konflikt verursachen, werden Sie aufgefordert, die Vorgehensweise auszuwählen. Achten Sie darauf, Änderungen an Ihrer Datei „local.settings.json“ zu speichern, bevor Sie diesen Befehl ausführen. |
| **Commit in GitHub anzeigen** | Zeigt Ihnen das aktuellste Commit in einer bestimmtem Bereitstellung, wenn Ihre Funktions-App mit einem Repository verbunden ist. |
| **Bereitstellungsprotokolle anzeigen** | Zeigt die Protokolle für eine bestimmte Bereitstellung in der Funktions-App in Azure an. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Functions Core Tools finden Sie unter [Arbeiten mit Azure Functions Core Tools](functions-run-local.md).

Weitere Informationen zum Entwickeln von Funktionen als .NET-Klassenbibliotheken finden Sie unter [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md). Dieser Artikel enthält auch Links zu Beispielen für die Verwendung von Attributen zum Deklarieren der verschiedenen Typen von Bindungen, die von Azure Functions unterstützt werden.

[Azure Functions-Erweiterung für Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md