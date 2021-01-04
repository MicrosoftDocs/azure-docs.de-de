---
title: IntelliSense in Azure Stream Analytics-Tools für Visual Studio Code
description: In diesem Artikel wird beschrieben, wie Sie die IntelliSense-Features in den Azure Stream Analytics-Tools für Visual Studio Code verwenden.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491569"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense in Azure Stream Analytics-Tools für Visual Studio Code

IntelliSense ist für die [Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) unter [Azure Stream Analytics-Tools für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) verfügbar. IntelliSense ist eine hilfreiche Anwendung zur Codevervollständigung mit Funktionen wie: Auflisten von Elementen, Parameterinformationen, QuickInfo und Wort vervollständigen. IntelliSense-Funktionen werden manchmal auch anders bezeichnet, z. B. als „Codevervollständigung“, „Inhaltshilfe“ und „Codehinweis“.

![IntelliSense-Demo](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-Funktionen

Die IntelliSense-Funktionen in Stream Analytics-Tools für VS Code basieren auf einem Sprachdienst. Ein Sprachdienst analysiert Ihren Quellcode und stellt eine intelligente Codevervollständigung bereit, die auf Sprachsemantik basiert. Wenn ein Sprachdienst mögliche Vervollständigungen erkennt, werden bei der Eingabe IntelliSense-Vorschläge angezeigt. Wenn Sie die Eingabe fortsetzen, wird eine Liste mit Einträgen, z. B. Variablen und Methoden, so gefiltert, dass nur Einträge mit den von Ihnen eingegebenen Zeichen angezeigt werden. Wenn Sie die Tasten `Tab` oder `Enter` drücken, fügt IntelliSense den von Ihnen ausgewählten Eintrag ein.

Sie können IntelliSense in allen Editor-Fenstern auslösen, indem Sie ein Zeichen für die Auslösung eingeben, z. B. einen Punkt (`.`).

![Automatische Vervollständigung von IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Das Widget für die Vorschläge unterstützt die Filterung bei gemischter Groß-/Kleinschreibung („Camel Case“). Sie können die innerhalb eines Methodennamens enthaltenen Großbuchstaben eingeben, um die Anzahl von Vorschlägen einzugrenzen. Die Eingabe von „cra“ führt beispielsweise schnell zu „createApplication“.

### <a name="types-of-completions"></a>Arten von Vervollständigungen

IntelliSense für Stream Analytics-Tools für VS Code verfügt über verschiedene Arten von Vervollständigungen, z. B. Sprachservervorschläge, Codeausschnitte und einfache Textvervollständigungen auf Wortbasis.

|Completion     |  type       |
| ----- | ------- |
| Keywords | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Datasetname| `input`, `output`, `intermediate result set`|
| Name der Datasetspalte|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Namensvervollständigung

Zusätzlich zur automatischen Vervollständigung von Schlüsselwörtern können die Stream Analytics-Tools für VS Code die Liste mit den Namen für die Auftragsein- und -ausgabe lesen. Dies gilt auch für die Namen der Spalten in Ihren Datenquellen, wenn diese konfiguriert werden. Die Erweiterung speichert diese Informationen, um Funktionen zur Namensvervollständigung bereitstellen zu können, die hilfreich beim Eingeben von Anweisungen mit nur wenigen Tastenanschlägen sind:

Beim Codieren müssen Sie den Editor nicht verlassen, um nach Eingabenamen, Ausgabenamen und Spaltennamen für Aufträge zu suchen. Sie können den Kontext beibehalten, die gewünschten Informationen suchen, Elemente direkt in den Code einfügen und IntelliSense die restliche Eingabe überlassen.

Beachten Sie hierbei, dass Sie entweder die lokale Eingabe oder die Liveeingabe konfigurieren und die Konfigurationsdatei speichern müssen, um die Namensvervollständigung nutzen zu können.

![Namensvervollständigung](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Parameterinformationen

Bei der Option **Parameterinformationen** von IntelliSense wird eine Parameterliste geöffnet, die Informationen über die Anzahl, Namen und Typen der Parameter enthält, die für eine Funktion erforderlich sind. Der fett formatierte Parameter gibt den nächsten Parameter an, der beim Eingeben einer Funktion erforderlich ist.

Die Parameterliste wird auch für geschachtelte Funktionen angezeigt. Wenn Sie eine Funktion als Parameter für eine andere Funktion eingeben, werden in der Parameterliste die Parameter für die innere Funktion angezeigt. Wenn die Parameterliste der inneren Funktion vollständig ist, werden in der Parameterliste anschließend die Parameter der äußeren Funktion angezeigt.

![Parameterinformationen](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>QuickInfo

Vom Sprachdienst wird für jeden Bezeichner in Ihrem Code eine **QuickInfo** angezeigt. Beispiele für Bezeichner sind Eingabe, Ausgabe, ein Zwischenresultset oder eine Funktion. Wenn Sie den Mauszeiger über einen Bezeichner bewegen, wird dessen Deklaration in einem Popupfenster angezeigt. Die Eigenschaften und Datenschemas für Eingaben (falls konfiguriert) und das Zwischendataset werden angezeigt.

![QuickInfo](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Behandeln von Problemen mit IntelliSense

Dieses Problem wird durch eine fehlende Eingabekonfiguration zur Bereitstellung von Daten verursacht. Sie können überprüfen, ob eine [lokale Eingabe](visual-studio-code-local-run.md#define-a-local-input) oder [Liveeingabe](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) richtig konfiguriert wurde.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)
* [Lokales Testen von Stream Analytics-Abfragen mit Beispieldaten mithilfe von Visual Studio Code](visual-studio-code-local-run.md)
* [Lokales Testen von Stream Analytics-Abfragen unter Verwendung einer Livestreameingabe mithilfe von Visual Studio Code](visual-studio-code-local-run-live-input.md)