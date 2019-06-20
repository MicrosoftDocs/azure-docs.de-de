---
title: 'Auffächerungsszenarios (nach innen und außen) in Durable Functions: Azure'
description: In diesem Artikel wird beschrieben, wie ein Auffächerungsszenario (nach innen und außen) in der Erweiterung Durable Functions für Azure Functions implementiert wird.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0bef5f1b64ec9f322070ba5c36cab138c7327da2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60741272"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Auffächerungsszenario (nach innen und außen) in Durable Functions – Beispiel der Cloudsicherung

*Auffächern/Auffächern nach innen* bezieht sich auf das Muster, bei dem mehrere Funktionen gleichzeitig ausgeführt werden und die Ergebnisse im Anschluss zusammengefasst werden. In diesem Artikel wird ein Beispiel beschrieben, in dem [Durable Functions](durable-functions-overview.md) zum Implementieren eines Auffächerungsszenarios (nach innen und außen) verwendet wird. Das Beispiel stellt eine langlebige Funktion dar, die alle oder einige der Websiteinhalte in Azure Storage sichert.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Übersicht über das Szenario

In diesem Beispiel laden die Funktionen alle Dateien unter einem angegebenen Verzeichnis rekursiv in den Blobspeicher hoch. Zudem zählen sie die Gesamtzahl der hochgeladenen Bytes.

Es ist möglich, eine einzelne Funktion zu schreiben, die alles übernimmt. Das Hauptproblem, mit dem Sie sich auseinandersetzen müssten, ist die **Skalierbarkeit**. Eine einzelne Funktion kann nur auf einem einzelnen virtuellen Computer ausgeführt werden. Der Durchsatz wird folglich durch den Durchsatz dieses virtuellen Computers beschränkt. Ein weiteres Problem stellt die **Zuverlässigkeit** dar. Wenn während des Vorgangs ein Fehler auftritt oder der gesamte Prozess mehr als 5 Minuten in Anspruch nimmt, könnte die Sicherung in einem teilweise abgeschlossenen Zustand fehlschlagen. Sie müsste anschließend neu gestartet werden.

Ein stabilerer Ansatz bestünde darin, zwei reguläre Funktionen zu schreiben: eine zum Aufzählen der Dateien und zum Hinzufügen der Dateinamen zu einer Warteschlange, und die andere zum Lesen aus der Warteschlange und zum Hochladen der Dateien in den Blobspeicher. Im Hinblick auf den Durchsatz und die Zuverlässigkeit ist dies besser, Sie müssen hierfür jedoch eine Warteschlange bereitstellen und verwalten. Im Hinblick auf die **Zustandsverwaltung** und die **Koordinierung** bringt dies vor allem eine hohe Komplexität mit sich, falls Sie weitere Funktionen ausführen möchten, wie z.B. die Gesamtzahl der hochgeladenen Bytes melden.

Ein Durable Functions-Ansatz bietet Ihnen alle genannten Vorteile mit sehr geringem Mehraufwand.

## <a name="the-functions"></a>Die Funktionen

In diesem Artikel werden die folgenden Funktionen in der Beispiel-App beschrieben:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

In den folgenden Abschnitten werden die Konfiguration und der Code beschrieben, die für C#-Skripts verwendet werden. Der Code für die Visual Studio-Entwicklung ist am Ende des Artikels angegeben.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Die Orchestrierung der Cloudsicherung (Visual Studio Code und Azure-Portal-Beispielcode)

Die Funktion `E2_BackupSiteContent` verwendet die Standarddatei *function.json* für Orchestratorfunktionen.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Im Folgenden wird der Code dargestellt, der die Orchestratorfunktion implementiert:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Diese Orchestratorfunktion führt im Wesentlichen Folgendes aus:

1. Sie akzeptiert einen Wert des Typs `rootDirectory` als Eingabeparameter.
2. Sie ruft eine Funktion auf, um unter `rootDirectory` eine rekursive Liste mit Dateien abzurufen.
3. Sie führt mehrere parallele Funktionsaufrufe durch, um die einzelnen Dateien in Azure Blob Storage hochzuladen.
4. Sie wartet, bis alle Uploads abgeschlossen wurden.
5. Sie gibt die Gesamtzahl der Bytes zurück, die in Azure Blob Storage hochgeladen wurden.

Beachten Sie die Zeilen `await Task.WhenAll(tasks);` (C#) und `yield context.df.Task.all(tasks);` (JavaScript). Alle einzelnen Aufrufe der `E2_CopyFileToBlob`-Funktion wurden *nicht* erwartet. Dies ist beabsichtigt, damit sie parallel ausgeführt werden können. Wenn dieses Array von Aufgaben an `Task.WhenAll` (C#) oder `context.df.Task.all` (JavaScript) übergeben wird, wird eine Aufgabe zurückgegeben, die erst *nach Abschluss aller Kopiervorgänge* abgeschlossen wird. Wenn Sie mit der Task Parallel Library (TPL) in .NET oder [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) in JavaScript vertraut sind, ist dies nicht neu für Sie. Der Unterschied besteht darin, dass diese Aufgaben auf mehreren virtuellen Computern gleichzeitig ausgeführt werden könnten. Die Durable Function-Erweiterung stellt dabei sicher, dass die End-to-End-Ausführung gegenüber der Prozesswiederverwendung robust ist.

> [!NOTE]
> Obwohl Aufgaben ein ähnliches Konzept zugrunde liegt wie JavaScript-Zusagen, sollten für Orchestratorfunktionen `context.df.Task.all` und `context.df.Task.any` anstelle von `Promise.all` und `Promise.race` verwendet werden, um die Parallelisierung von Aufgaben zu verwalten.

Nach dem Warten in `Task.WhenAll` (oder der Rückgabe über `context.df.Task.all`) wissen wir, dass alle Funktionsaufrufe abgeschlossen wurden und Werte zurückgegeben haben. Jeder Aufruf von `E2_CopyFileToBlob` gibt die Anzahl von hochgeladenen Bytes zurück. Die Summe der Gesamtbytezahl wird folglich berechnet, indem alle diese Rückgabewerte addiert werden.

## <a name="helper-activity-functions"></a>Hilfsaktivitätsfunktionen

Bei den Hilfsaktivitätsfunktionen handelt es sich, wie bei anderen Beispielen, um normale Funktionen, die die Triggerbindung `activityTrigger` verwenden. Die Datei *function.json* für `E2_GetFileList` sieht wie folgt aus:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Und hier ist die Implementierung:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Bei der JavaScript-Implementierung von `E2_GetFileList` wird das Modul `readdirp` verwendet, um die Verzeichnisstruktur rekursiv zu lesen.

> [!NOTE]
> Sie wundern sich vielleicht, weshalb Sie diesen Code nicht einfach direkt in die Orchestratorfunktion einfügen konnten. Das können Sie, aber damit würden Sie eine der Grundregeln der Orchestratorfunktionen brechen. Diese besagt, dass die Funktionen niemals eine Eingabe/Ausgabe vornehmen dürfen, den lokalen Dateisystemzugriff inbegriffen.

Die Datei *function.json* für `E2_CopyFileToBlob` ist ebenso einfach:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Auch die C#-Implementierung ist ziemlich unkompliziert. Es werden einige erweiterte Features von Azure Functions-Bindungen verwendet (d.h. die Verwendung des Parameters `Binder`). Sie müssen sich zum Zwecke dieser ausführlichen Anleitung jedoch nicht um diese Details kümmern.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

Die JavaScript-Implementierung hat keinen Zugriff auf das `Binder`-Feature von Azure Functions, daher wird sie durch [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) ersetzt.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Bei der Implementierung wird die Datei vom Datenträger geladen, und die Inhalte werden asynchron in ein Blob mit dem gleichen Namen im Container „backups“ gestreamt. Der Rückgabewert ist die Anzahl der Bytes, die in den Speicher kopiert und anschließend von der Orchestratorfunktion für die Berechnung der Summe verwendet werden.

> [!NOTE]
> Dies ist ein gutes Beispiel für das Verschieben von Eingabe-/Ausgabevorgängen in die Funktion `activityTrigger`. Die Arbeit kann nicht nur auf viele verschiedene virtuelle Computer verteilt werden, sondern Sie können auch den Fortschritt überprüfen. Wenn der Hostprozess aus irgendeinem Grund beendet wird, wissen Sie, welche Uploads bereits abgeschlossen wurden.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Sie können die Orchestrierung starten, indem Sie die folgende HTTP POST-Anforderung senden.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Die Funktion `HttpStart`, die Sie aufrufen, kann nur mit JSON-formatiertem Inhalt verwendet werden. Aus diesem Grund ist der Header `Content-Type: application/json` erforderlich und der Verzeichnispfad als JSON-Zeichenfolge codiert. Darüber hinaus wird im HTTP-Ausschnitt davon ausgegangen, dass ein Eintrag in der Datei `host.json` vorhanden ist, der das Standardpräfix `api/` aus den URLs aller HTTP-Triggerfunktionen entfernt. Sie finden das Markup für diese Konfiguration in der Datei `host.json` in den Beispielen.

Diese HTTP-Anforderung löst den Orchestrator `E2_BackupSiteContent` aus und übergibt die Zeichenfolge `D:\home\LogFiles` als Parameter. Die Antwort enthält einen Link, über den der Status des Sicherungsvorgangs abgerufen werden kann:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Je nachdem, wie viele Protokolldateien in Ihrer Funktions-App enthalten sind, könnte dieser Vorgang einige Minuten in Anspruch nehmen. Sie können den aktuellen Status abrufen, indem Sie die URL im Header `Location` der vorherigen HTTP 202-Antwort abfragen.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

In diesem Fall wird die Funktion weiterhin ausgeführt. Sie können die Eingabe, die in den Orchestratorzustand gespeichert wurde, sowie die Zeit der letzten Aktualisierung anzeigen. Sie können weiterhin die `Location`-Headerwerte zum Abfragen der Beendigung verwenden. Wenn der Status „Abgeschlossen“ lautet, wird Ihnen ein HTTP-Antwortwert wie der folgende angezeigt:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Jetzt können Sie sehen, dass die Orchestrierung abgeschlossen ist und wie viel Zeit für den Abschluss benötigt wurde. Zudem wird Ihnen ein Wert für das Feld `output` angezeigt, der angibt, dass etwa 450 KB Protokolle hochgeladen wurden.

## <a name="visual-studio-sample-code"></a>Visual Studio-Beispielcode

So sieht die Orchestrierung als einzelne C#-Datei in einem Visual Studio-Projekt aus:

> [!NOTE]
> Sie müssen das `Microsoft.Azure.WebJobs.Extensions.Storage`-NuGet-Paket installieren, um den unten stehenden Beispielcode auszuführen.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel wurde das Implementieren das Auffächerungsmusters angezeigt. Das nächste Beispiel zeigt, wie man das Überwachungsmuster mithilfe von [dauerhaften Timern](durable-functions-timers.md) implementiert.

> [!div class="nextstepaction"]
> [Führen Sie den Überwachungsbeispiels](durable-functions-monitor.md)
