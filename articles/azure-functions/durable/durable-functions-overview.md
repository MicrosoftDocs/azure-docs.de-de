---
title: Übersicht zu Durable Functions – Azure
description: Einführung in die Durable Functions-Erweiterung für Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 2228f3fe05e1021d0f87ce0b0d33a8287f048a8c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872808"
---
# <a name="what-are-durable-functions"></a>Was ist Durable Functions?

*Durable Functions* ist eine Erweiterung von [Azure Functions](../functions-overview.md), mit der Sie zustandsbehaftete Funktionen in einer serverlosen Umgebung schreiben können. Die Erweiterung verwaltet Status, Prüfpunkte und Neustarts für Sie.

## <a name="benefits"></a>Vorteile

Mit der Erweiterung können Sie mithilfe einer [*Orchestratorfunktion*](durable-functions-types-features-overview.md#orchestrator-functions) zustandsbehaftete Workflows definieren, die folgende Vorteile bieten:

* Sie können Ihre Workflows in Code definieren. Keine JSON-Schemas oder Designer sind erforderlich.
* Andere Funktionen können sowohl synchron als auch asynchron aufgerufen werden. Ausgabe von aufgerufenen Funktionen kann in lokalen Variablen gespeichert werden.
* Für den Status werden immer dann, wenn die Funktion wartet, automatisch Prüfpunkte erstellt. Der lokale Zustand geht nie verloren, wenn der Prozess oder virtuelle Computer neu gestartet wird.

## <a name="application-patterns"></a>Anwendungsmuster

Der primäre Anwendungsfall für Durable Functions ist die Vereinfachung komplexer, zustandsbehafteter Koordinationsanforderungen in serverlosen Anwendungen. Nachfolgend werden einige typische Anwendungsmuster beschrieben, die von Durable Functions profitieren können:

* [Verkettung](durable-functions-concepts.md#chaining)
* [Auffächern nach außen/innen](durable-functions-concepts.md#fan-in-out)
* [Asynchrone HTTP-APIs](durable-functions-concepts.md#async-http)
* [Überwachung](durable-functions-concepts.md#monitoring)
* [Benutzerinteraktion](durable-functions-concepts.md#human)

## <a name="language-support"></a>Unterstützte Sprachen

Durable Functions unterstützt derzeit die folgenden Sprachen:

* **C#** : sowohl [vorkompilierte Klassenbibliotheken](../functions-dotnet-class-library.md) als auch [ C#-Skript](../functions-reference-csharp.md).
* **F#** : vorkompilierte Klassenbibliotheken und F#-Skript. F#-Skript wird nur für Version 1.x der Azure Functions-Runtime unterstützt.
* **JavaScript**: nur für Version 2.x der Azure Functions-Runtime unterstützt. Erfordert mindestens Version 1.7.0 der Durable Functions-Erweiterung. 

Das Ziel von Durable Functions ist die Unterstützung aller [Azure Functions-Sprachen](../supported-languages.md). In der [Liste der Durable Functions-Probleme](https://github.com/Azure/azure-functions-durable-extension/issues) finden Sie den aktuellen Status der Arbeit, um zusätzliche Sprachen zu unterstützen.

Wie bei Azure Functions sind Vorlagen verfügbar, um Sie bei der Entwicklung von Durable Functions mit [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) und dem [Azure-Portal](durable-functions-create-portal.md) zu unterstützen.

## <a name="billing"></a>Abrechnung

Durable Functions wird genau wie Azure Functions in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Sofort loslegen

Sie können die ersten Schritte mit Durable Functions in weniger als 10 Minuten durchführen, indem Sie eines dieser sprachspezifischen Schnellstarttutorials abschließen:

* [C# mit Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript mit Visual Studio Code](quickstart-js-vscode.md)

In beiden Schnellstarts erstellen Sie lokal eine dauerhafte „Hallo Welt“-Funktion und testen diese. Anschließend veröffentlichen Sie den Funktionscode in Azure. Mit der von Ihnen erstellten Funktion werden Aufrufe anderer Funktionen orchestriert und miteinander verkettet.

## <a name="learn-more"></a>Weitere Informationen

Im folgenden Video werden die Vorteile von Durable Functions aufgezeigt:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Da es sich bei Durable Functions um eine fortgeschrittene Erweiterung für [Azure Functions](../functions-overview.md) handelt, ist sie nicht für alle Anwendungen geeignet. Weitere Informationen zu Durable Functions finden Sie im Artikel zu [Mustern und technischen Konzepten von Durable Functions](durable-functions-concepts.md). Einen Vergleich mit anderen Azure-Orchestrierungstechnologien finden Sie unter [Vergleich zwischen Azure Functions und Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Muster und technische Konzepte von Durable Functions](durable-functions-concepts.md)
