---
title: Erste Schritte mit Azure Functions
description: Hier finden Sie erste Schritte für die Verwendung von Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 78fd13825becfa186960a0dfd3dee83c312c9bcf
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326946"
---
# <a name="getting-started-with-azure-functions"></a>Erste Schritte mit Azure Functions

## <a name="introduction"></a>Einführung

Mithilfe von [Azure Functions](./functions-overview.md) können Sie die Logik Ihres Systems in Form von jederzeit verfügbaren Codeblöcken implementieren. Diese Codeblöcke werden als „Funktionen“ bezeichnet.

Verwenden Sie die folgenden Ressourcen, um zu beginnen.

::: zone pivot="programming-language-csharp"

| Aktion | Ressourcen |
| --- | --- |
| **Erstellen Ihrer ersten Funktion** | Verwenden Sie eines der folgenden Tools:<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Befehlszeile](./create-first-function-cli-csharp.md) |
| **Ansehen einer ausgeführten Funktion** | <li>[Browser für Azure-Beispiele](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Azure-Communitybibliothek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Erkunden eines interaktiven Tutorials**| <li>[Auswählen der besten serverlosen Azure-Technologie für Ihr Geschäftsszenario](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework: Leistungseffizienz](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Ausführen einer Azure-Funktion mit Triggern](/learn/modules/execute-azure-function-with-triggers/) <br><br>Eine [vollständige Liste interaktiver Tutorials](/learn/browse/?expanded=azure&products=azure-functions) finden Sie bei Microsoft Learn.|
| **Fundierteres Lernen** | <li>Erfahren Sie, wie Instanzen durch Funktionen bei Bedarf [automatisch erhöht oder verringert](./functions-scale.md) werden.<li>Erkunden Sie die verschiedenen verfügbaren [Bereitstellungsmethoden](./functions-deployment-technologies.md).<li>Verwenden Sie integrierte [Überwachungstools](./functions-monitoring.md) zur Vereinfachung der Funktionsanalyse.<li>Lesen Sie die [C#-Entwicklerreferenz zu Azure Functions](./functions-dotnet-class-library.md).|

::: zone-end

::: zone pivot="programming-language-java"
| Aktion | Ressourcen |
| --- | --- |
| **Erstellen Ihrer ersten Funktion** | Verwenden Sie eines der folgenden Tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Java-/Maven-Funktion mit Terminal/Eingabeaufforderung](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Ansehen einer ausgeführten Funktion** | <li>[Browser für Azure-Beispiele](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Azure-Communitybibliothek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Erkunden eines interaktiven Tutorials**| <li>[Auswählen der besten serverlosen Azure-Technologie für Ihr Geschäftsszenario](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework: Leistungseffizienz](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Entwickeln einer App mithilfe des Maven-Plug-Ins für Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Eine [vollständige Liste interaktiver Tutorials](/learn/browse/?expanded=azure&products=azure-functions) finden Sie bei Microsoft Learn.|
| **Fundierteres Lernen** | <li>Erfahren Sie, wie Instanzen durch Funktionen bei Bedarf [automatisch erhöht oder verringert](./functions-scale.md) werden.<li>Erkunden Sie die verschiedenen verfügbaren [Bereitstellungsmethoden](./functions-deployment-technologies.md).<li>Verwenden Sie integrierte [Überwachungstools](./functions-monitoring.md) zur Vereinfachung der Funktionsanalyse.<li>Lesen Sie das [Java-Entwicklerhandbuch für Azure Functions](./functions-reference-java.md).|
::: zone-end

::: zone pivot="programming-language-javascript"
| Aktion | Ressourcen |
| --- | --- |
| **Erstellen Ihrer ersten Funktion** | Verwenden Sie eines der folgenden Tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminal/Eingabeaufforderung für Node.js](./create-first-function-cli-java.md) |
| **Ansehen einer ausgeführten Funktion** | <li>[Browser für Azure-Beispiele](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Azure-Communitybibliothek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Erkunden eines interaktiven Tutorials** | <li>[Auswählen der besten serverlosen Azure-Technologie für Ihr Geschäftsszenario](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework: Leistungseffizienz](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Erstellen serverloser APIs mit Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Erstellen von serverloser Logik mit Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Umgestalten von Node.js- und Express-APIs in serverlose APIs mit Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Eine [vollständige Liste interaktiver Tutorials](/learn/browse/?expanded=azure&products=azure-functions) finden Sie bei Microsoft Learn.|
| **Fundierteres Lernen** | <li>Erfahren Sie, wie Instanzen durch Funktionen bei Bedarf [automatisch erhöht oder verringert](./functions-scale.md) werden.<li>Erkunden Sie die verschiedenen verfügbaren [Bereitstellungsmethoden](./functions-deployment-technologies.md).<li>Verwenden Sie integrierte [Überwachungstools](./functions-monitoring.md) zur Vereinfachung der Funktionsanalyse.<li>Lesen Sie das [JavaScript-Entwicklerhandbuch für Azure Functions](./functions-reference-node.md) oder den Abschnitt zu [TypeScript](./functions-reference-node.md#typescript).|
::: zone-end

::: zone pivot="programming-language-powershell"
| Aktion | Ressourcen |
| --- | --- |
| **Erstellen Ihrer ersten Funktion** | <li>[Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Ansehen einer ausgeführten Funktion** | <li>[Browser für Azure-Beispiele](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Azure-Communitybibliothek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Erkunden eines interaktiven Tutorials** | <li>[Auswählen der besten serverlosen Azure-Technologie für Ihr Geschäftsszenario](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework: Leistungseffizienz](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Erstellen serverloser APIs mit Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Erstellen von serverloser Logik mit Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Ausführen einer Azure-Funktion mit Triggern](/learn/modules/execute-azure-function-with-triggers/) <br><br>Eine [vollständige Liste interaktiver Tutorials](/learn/browse/?expanded=azure&products=azure-functions) finden Sie bei Microsoft Learn.|
| **Fundierteres Lernen** | <li>Erfahren Sie, wie Instanzen durch Funktionen bei Bedarf [automatisch erhöht oder verringert](./functions-scale.md) werden.<li>Erkunden Sie die verschiedenen verfügbaren [Bereitstellungsmethoden](./functions-deployment-technologies.md).<li>Verwenden Sie integrierte [Überwachungstools](./functions-monitoring.md) zur Vereinfachung der Funktionsanalyse.<li>Lesen Sie das [PowerShell-Entwicklerhandbuch für Azure Functions](./functions-reference-powershell.md).|
::: zone-end

::: zone pivot="programming-language-python"
| Aktion | Ressourcen |
| --- | --- |
| **Erstellen Ihrer ersten Funktion** | Verwenden Sie eines der folgenden Tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminal/Eingabeaufforderung](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Ansehen einer ausgeführten Funktion** | <li>[Browser für Azure-Beispiele](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Azure-Communitybibliothek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Erkunden eines interaktiven Tutorials** | <li>[Auswählen der besten serverlosen Azure-Technologie für Ihr Geschäftsszenario](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework: Leistungseffizienz](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Erstellen serverloser APIs mit Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Erstellen von serverloser Logik mit Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Eine [vollständige Liste interaktiver Tutorials](/learn/browse/?expanded=azure&products=azure-functions) finden Sie bei Microsoft Learn.|
| **Fundierteres Lernen** | <li>Erfahren Sie, wie Instanzen durch Funktionen bei Bedarf [automatisch erhöht oder verringert](./functions-scale.md) werden.<li>Erkunden Sie die verschiedenen verfügbaren [Bereitstellungsmethoden](./functions-deployment-technologies.md).<li>Verwenden Sie integrierte [Überwachungstools](./functions-monitoring.md) zur Vereinfachung der Funktionsanalyse.<li>Lesen Sie das [Python-Entwicklerhandbuch für Azure Functions](./functions-reference-python.md).|
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Functions: Entwicklerhandbuch](./functions-reference.md)
