---
title: Lokales Entwickeln und Ausführen von Azure Functions
description: Erfahren Sie, wie Sie Azure Functions auf dem lokalen Computer codieren und testen, bevor Sie sie in Azure Functions ausführen.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168133"
---
# <a name="code-and-test-azure-functions-locally"></a>Lokales Codieren und Testen von Azure Functions

Obwohl Azure Functions im [Azure portal] entwickelt und getestet werden kann, bevorzugen viele Entwickler jedoch dafür eine lokale Entwicklungsumgebung. Mit Functions können Sie einfach Ihren bevorzugten Code-Editor und Ihre bevorzugten Entwicklungstools zum Erstellen und Testen von Funktionen auf dem lokalen Computer verwenden. Die lokalen Funktionen können mit Live-Azure-Diensten verbunden werden, und Sie können sie unter Verwendung der vollständigen Functions-Runtime auf dem lokalen Computer debuggen.

## <a name="local-development-environments"></a>Lokale Entwicklungsumgebungen

Die Art und Weise, wie Sie Funktionen auf dem lokalen Computer entwickeln, hängt von der verwendeten [Sprache](supported-languages.md) und den Tooleinstellungen ab. Die lokale Entwicklung wird in den in der folgenden Tabelle aufgeführten Umgebungen unterstützt:

|Environment                              |Languages         |BESCHREIBUNG|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (Klassenbibliothek)](functions-dotnet-class-library.md), [C#-Skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](./create-first-function-vs-code-powershell.md), [Python](functions-reference-python.md) | Die [Azure Functions-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) erweitert die Functions-Unterstützung um VS Code. Erfordert Core Tools. Unterstützt die Entwicklung unter Linux, macOS und Windows bei Verwendung von Version 2.x von Core Tools. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion mit Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Eingabeaufforderung oder Terminal](functions-run-local.md) | [C# (Klassenbibliothek)](functions-dotnet-class-library.md), [C#-Skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] umfasst die Core-Runtime und Vorlagen zum Erstellen von Funktionen, die die lokale Entwicklung ermöglichen. In Version 2.x wird die Entwicklung unter Linux, macOS und Windows unterstützt. Für die lokale Functions-Runtime basieren alle Umgebungen auf Core Tools. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (Klassenbibliothek)](functions-dotnet-class-library.md) | Die Azure Functions-Tools sind in der **Azure-Entwicklungsworkload** in [Visual Studio 2019](https://www.visualstudio.com/vs/) und höheren Versionen enthalten. Hiermit können Sie Funktionen in einer Klassenbibliothek kompilieren und die DLL-Datei in Azure veröffentlichen. Enthält die Core Tools für lokale Tests. Weitere Informationen finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (verschiedene) | [Java](functions-reference-java.md) | Kann in Core Tools integriert werden, um die Entwicklung von Java-Funktionen zu ermöglichen. In Version 2.x wird die Entwicklung unter Linux, macOS und Windows unterstützt. Weitere Informationen finden Sie unter [Erstellen der ersten Funktion mit Java und Maven](./create-first-function-cli-java.md). Unterstützt auch die Entwicklung mit [Eclipse](functions-create-maven-eclipse.md) und [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

In jeder dieser lokalen Entwicklungsumgebungen können Sie Funktions-App-Projekte erstellen und vordefinierte Functions-Vorlagen zum Erstellen neuer Funktionen verwenden. In jeder wird Core Tools verwendet, sodass Sie Ihre Funktionen in der echten Functions-Runtime auf Ihrem eigenen Computer testen und debuggen können, so wie Sie dies für alle anderen Apps auch tun. Außerdem können Sie Ihr Funktions-App-Projekt in jeder dieser Umgebungen in Azure veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

+ Weitere Informationen zur lokalen Entwicklung von kompilierten C#-Funktionen mithilfe von Visual Studio 2019 finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](functions-develop-vs.md).
+ Weitere Informationen zur lokalen Entwicklung von Funktionen mithilfe von VS Code auf einem Mac-, Linux- oder Windows-Computer finden Sie unter [Bereitstellen von Azure Functions aus VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Weitere Informationen zum Entwickeln von Funktionen über die Eingabeaufforderung oder das Terminal finden Sie unter [Arbeiten mit Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows