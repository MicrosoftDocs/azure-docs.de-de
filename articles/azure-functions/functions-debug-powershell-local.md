---
title: Lokales Debuggen von PowerShell Azure Functions
description: Erfahren Sie, wie Sie PowerShell-Funktionen bei lokaler Ausführung debuggen.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422959"
---
# <a name="debug-powershell-azure-functions-locally"></a>Lokales Debuggen von PowerShell Azure Functions

Mit Azure Functions können Sie Ihre Funktionen als PowerShell-Skripts entwickeln.

Sie können Ihre PowerShell-Funktionen lokal debuggen, wie Sie es mit jedem PowerShell-Skript machen würden, indem Sie die folgenden Standardentwicklungstools verwenden:

* [Visual Studio Code](https://code.visualstudio.com/): Microsofts kostenloser, schlanker und Open-Source-Text-Editor mit der PowerShell-Erweiterung, der eine vollständige PowerShell-Entwicklungsumgebung bietet.
* Eine PowerShell-Konsole: Debuggen Sie mit denselben Befehlen, die Sie zum Debuggen jedes anderen PowerShell-Prozesses verwenden würden.

[Azure Functions Core Tools](functions-run-local.md) unterstützen lokales Debuggen von Azure Functions, einschließlich PowerShell-Funktionen.

## <a name="example-function-app"></a>Beispielfunktions-App

Die in diesem Artikel verwendete Funktions-App enthält eine einzelne HTTP-ausgelöste Funktion sowie die folgenden Dateien:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Diese Funktions-App ähnelt derjenigen, die Sie erhalten, wenn die [Schnellstartanleitung zu PowerShell](./create-first-function-vs-code-powershell.md) durcharbeiten.

Der Funktionscode in `run.ps1` ähnelt dem folgenden Skript:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Festlegen des Anfügepunkts

Um eine PowerShell-Funktion zu debuggen, muss die Funktion angehalten werden, um den Debugger anzufügen. Das `Wait-Debugger`-Cmdlet hält die Ausführung an und wartet auf den Debugger.

>[!NOTE]
>Wenn Sie PowerShell 7 verwenden, müssen Sie in Ihrem Code den `Wait-Debugger`-Aufrufe nicht hinzufügen.

Sie müssen lediglich direkt über der `if`-Anweisung einen Aufruf an das Cmdlet `Wait-Debugger` hinzufügen, wie folgt:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Das Debuggen beginnt an der `if`-Anweisung. 

Mit eingefügtem `Wait-Debugger` können Sie nun die Funktionen entweder mit Visual Studio Code oder mit einer PowerShell-Konsole debuggen.

## <a name="debug-in-visual-studio-code"></a>Debuggen in Visual Studio Code

Um PowerShell-Funktionen in Visual Studio Code zu debuggen, müssen folgende Komponenten installiert sein:

* [PowerShell-Erweiterung für Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-Erweiterung für Visual Studio Code](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 oder höher](/powershell/scripting/install/installing-powershell-core-on-windows)

Laden Sie nach der Installation dieser Abhängigkeiten ein vorhandenes Projekt für PowerShell-Funktionen, oder [Erstellen Sie Ihr erstes Projekt für PowerShell-Funktionen](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Sollte Ihr Projekt nicht die erforderlichen Konfigurationsdateien enthalten, werden Sie aufgefordert, sie ihm hinzuzufügen.

### <a name="set-the-powershell-version"></a>Festlegen der PowerShell-Version

PowerShell Core wird parallel zu Windows PowerShell installiert. Legen Sie PowerShell Core als die PowerShell-Version fest, die mit der PowerShell-Erweiterung für Visual Studio Code verwendet werden soll.

1. Drücken Sie F1, um die verfügbaren Befehle anzuzeigen, und suchen Sie dann `Session`.

1. Wählen Sie die Option für **PowerShell: Menü „Sitzung“ anzeigen**.

1. Wenn die **aktuelle Sitzung** nicht **PowerShell Core 6** entspricht, wählen Sie **Wechseln zu: PowerShell Core 6** aus.

Wenn Sie eine PowerShell-Datei geöffnet haben, wird die Version unten rechts im Fenster in Grün angezeigt. Wenn Sie diesen Text auswählen, wird außerdem das Sitzungsmenü angezeigt. Weitere Informationen finden Sie unter [Auswählen einer Version von PowerShell für die Verwendung mit der Erweiterung](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Starten der Funktions-App

Überprüfen Sie, ob `Wait-Debugger` an der Stelle in der Funktion festgelegt ist, wo Sie den Debugger anfügen möchten.  Mit hinzugefügtem `Wait-Debugger` können Sie Ihre Funktions-App mit Visual Studio Code debuggen.

Wählen Sie den Bereich **Debuggen** und dann **An PowerShell-Funktion anfügen** aus.

![Debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Sie können auch die F5-Taste drücken, um das Debuggen zu starten.

Der Vorgang „Debuggen starten“ führt die folgenden Aufgaben aus:

* Ausführen von `func extensions install` im Terminal, um alle von Ihrer Funktions-App benötigten Azure Functions-Erweiterungen zu installieren.
* Ausführen von `func host start` im Terminal, um die Funktions-App im Functions-Host zu starten.
* Anfügen des PowerShell-Debuggers an den PowerShell-Runspace innerhalb der Functions Runtime.

>[!NOTE]
> Sie müssen sicherstellen, dass „PSWorkerInProcConcurrencyUpperBound“ auf „1“ festgelegt ist, um eine korrekte Debuggingerfahrung in Visual Studio Code sicherzustellen. Dies ist die Standardoption.

Während Ihre Funktions-App ausgeführt wird, benötigen Sie eine gesonderte PowerShell-Konsole, um die HTTP-ausgelöste Funktion aufzurufen.

In diesem Fall ist die PowerShell-Konsole der Client. Die `Invoke-RestMethod` wird zum Auslösen der Funktion verwendet.

Führen Sie den folgenden Befehl in einer PowerShell-Konsole aus:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Sie werden feststellen, dass nicht sofort eine Antwort zurückgegeben wird. Der Grund dafür ist, dass `Wait-Debugger` den Debugger angefügt hat und dass die PowerShell-Ausführung in den Unterbrechungsmodus (break) gewechselt ist, sobald sie konnte. Die Ursache hierfür ist das [BreakAll-Konzept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), das später noch erläutert wird. Nachdem Sie die Schaltfläche `continue` gedrückt haben, unterbricht der Debugger in der Zeile direkt hinter `Wait-Debugger`.

An diesem Punkt wird der Debugger angefügt, und Sie können alle normalen Debugvorgänge ausführen. Weitere Informationen zur Verwendung des Debuggers in Visual Studio Code finden Sie in der [offiziellen Dokumentation](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Nachdem Sie fortgefahren sind und Ihr Skript vollständig aufgerufen haben, werden Sie feststellen:

* Der PowerShell-Konsole, die die `Invoke-RestMethod` ausgeführt hat, hat ein Ergebnis zurückgegeben.
* Die integrierte PowerShell-Konsole in Visual Studio Code wartet auf die Ausführung eines Skripts.

Bei nachfolgenden Aufrufen derselben Funktion unterbricht der Debugger in der PowerShell-Erweiterung direkt nach `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debuggen in einer PowerShell-Konsole

>[!NOTE]
> In diesem Abschnitt wird vorausgesetzt, dass Sie die [Azure Functions Core Tools-Dokumentation](functions-run-local.md) gelesen haben und wissen, wie Sie mit dem Befehl `func host start` Ihre Funktions-App starten.

Öffnen Sie eine Konsole, wechseln Sie mittels `cd` in das Verzeichnis Ihrer Funktions-App, und führen Sie den folgenden Befehl aus:

```sh
func host start
```

Mit der in Ausführung befindlichen Funktions-App und bei eingefügtem `Wait-Debugger` können Sie an den Prozess anfügen. Sie benötigen zwei weitere PowerShell-Konsolen.

Eine der Konsolen fungiert als Client. Aus dieser rufen Sie `Invoke-RestMethod` auf, um die Funktion auszulösen. Sie können beispielsweise den folgenden Befehl ausführen:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Sie werden feststellen, dass er keine Antwort zurückgibt, was aus `Wait-Debugger` resultiert. Der PowerShell-Runspace wartet jetzt auf das Anfügen eines Debuggers. Fügen wir ihn also an.

In der anderen PowerShell-Konsole führen Sie den folgenden Befehl aus:

```powershell
Get-PSHostProcessInfo
```

Dieses Cmdlet gibt eine Tabelle zurück, die wie die folgende Ausgabe aussieht:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Notieren Sie sich die `ProcessId` für das Element in der Tabelle mit dem `ProcessName` als `dotnet`. Dieser Prozess ist Ihre Funktions-App.

Als Nächstes führen Sie den folgenden Codeausschnitt aus:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Nach dem Starten unterbricht der Debugger und zeigt ungefähr die folgende Ausgabe an:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Zu diesem Zeitpunkt halten Sie an einem Breakpoint (Unterbrechungspunkt) im [PowerShell-Debugger](/powershell/module/microsoft.powershell.core/about/about_debuggers). Von hier aus können Sie alle üblichen Debugvorgänge ausführen, also Prozedurschritt, schrittweises Ausführen, Fortfahren, Beenden und andere. Um den vollständigen Satz von Debugbefehlen anzuzeigen, die in der Konsole verfügbar sind, führen Sie den Befehl `h` oder `?` aus.

Sie können auf dieser Ebene auch Breakpoints mit dem Cmdlet `Set-PSBreakpoint` setzen.

Nachdem Sie fortgefahren sind und Ihr Skript vollständig aufgerufen haben, werden Sie feststellen:

* Die PowerShell-Konsole, in der Sie `Invoke-RestMethod` ausgeführt haben, hat jetzt ein Ergebnis zurückgegeben.
* Die PowerShell-Konsole, in der Sie `Debug-Runspace` ausgeführt haben, wartet auf die Ausführung eines Skripts.

Sie können dieselbe Funktion erneut aufrufen (z. B. mithilfe von `Invoke-RestMethod`), und der Debugger unterbricht unmittelbar hinter dem Befehl `Wait-Debugger`.

## <a name="considerations-for-debugging"></a>Überlegungen zum Debuggen

Denken Sie beim Debuggen Ihres Funktionscodes an die folgenden Probleme.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` kann dazu führen, dass Ihr Debugger an einer unerwarteten Stelle unterbricht.

Die PowerShell-Erweiterung verwendet `Debug-Runspace`, was wiederum auf der `BreakAll`-Funktion der PowerShell basiert. Diese Funktion weist PowerShell an, beim ersten Befehl, ausgeführt wird, anzuhalten. Dieses Verhalten gibt Ihnen die Möglichkeit, Breakpoints innerhalb des debuggten Runspace zu setzen.

Die Azure Functions Runtime führt ein paar Befehle vor dem tatsächlichen Aufrufen Ihres `run.ps1`-Skripts aus, sodass dies dazu führen kann, dass der Debugger innerhalb von `Microsoft.Azure.Functions.PowerShellWorker.psm1` oder `Microsoft.Azure.Functions.PowerShellWorker.psd1` unterbricht.

Sollte es zu dieser Unterbrechung kommen, führen Sie den Befehl `continue` oder `c` aus, um diesen Breakpoint zu überspringen. Danach halten Sie am erwarteten Breakpoint an.

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Debuggen Probleme auftreten, sollten Sie Folgendes überprüfen:

| Prüfen | Aktion |
|------|------|
| Führen Sie `func --version` im Terminal aus. Wenn Sie eine Fehlermeldung erhalten, dass `func` nicht gefunden werden kann, fehlen in der lokalen `path`-Variablen möglicherweise Core Tools (func.exe).| [Installieren Sie die Core Tools neu](functions-run-local.md#v2).|  
| In Visual Studio Code benötigt das Standardterminal Zugriff auf „func.exe“. Stellen Sie sicher, dass Sie kein Standardterminal verwenden, für das Core Tools nicht installiert sind, wie z. B. das Windows-Subsystem für Linux (WSL).  | Legen Sie die Standardshell in Visual Studio Code entweder auf PowerShell 7 (empfohlen) oder Windows PowerShell 5.1 fest.|
  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von Funktionen mithilfe von PowerShell finden Sie unter [PowerShell-Entwicklerhandbuch zu Azure Functions](functions-reference-powershell.md).
