---
title: Leitfaden zur Entwicklung von Azure Functions
description: Lernen Sie die Konzepte und Techniken der Azure Functions kennen, die Sie benötigen, um alle Programmiersprachen und Bindungen übergreifend Funktionen in Azure zu entwickeln.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 54bfd770fba9a1766396d66c0c263111c233c9c2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167878"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions: Entwicklerhandbuch
In Azure Functions nutzen bestimmte Funktionen einige wichtige technische Konzepte und Komponenten gemeinsam, unabhängig von der verwendeten Sprache oder Bindung. Bevor Sie sich mit den spezifischen Details einer bestimmten Sprache oder Bindung beschäftigen, sollten Sie diese Übersicht lesen, die für alle Funktionen gilt.

Dieser Artikel setzt voraus, dass Sie die [Einführung in Azure Functions](functions-overview.md) bereits gelesen haben.

## <a name="function-code"></a>Funktionscode
Ein *Funktion* ist das primäre Konzept in Azure Functions. Eine Funktion enthält zwei wichtige Komponenten: Ihren Code, der in einer Vielzahl von Programmiersprachen geschrieben sein kann, und die Datei „function.json“ mit Konfigurationsinformationen. Für kompilierte Sprachen wird diese Konfigurationsdatei automatisch aus Anmerkungen im Code generiert. Für Skriptsprachen müssen Sie die Konfigurationsdatei selbst bereitstellen.

Die Datei „function.json“ definiert den Trigger, die Bindungen und weitere Konfigurationseinstellungen der Funktion. Jede Funktion verfügt nur über einen einzigen Trigger. Anhand dieser Konfigurationsdatei ermittelt die Runtime, welche Ereignisse überwacht werden sollen und wie Daten in die Funktionsausführung übergeben und aus dieser zurückgegeben werden. Im Folgenden finden Sie eine function.json-Beispieldatei.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Weitere Informationen finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md).

In der Eigenschaft `bindings` werden sowohl Trigger als auch Bindungen konfiguriert. Einige Einstellungen gelten für alle Bindungen, einige Einstellungen sind spezifisch für einen bestimmten Bindungstyp. Jede Bindung erfordert folgende Einstellungen:

| Eigenschaft | Werte/Typen | Kommentare |
| --- | --- | --- |
| `type` |Zeichenfolge |Bindungstyp. Beispiel: `queueTrigger`. |
| `direction` |'in', 'out' |Gibt an, ob die Bindung zum Empfangen von Daten in der Funktion oder zum Senden von Daten aus der Funktion dient. |
| `name` |Zeichenfolge |Der Name, der für die gebundenen Daten in der Funktion verwendet wird. In C# ist dies ein Argumentname, in JavaScript ist es der Schlüssel in einer Schlüssel-Wert-Liste. |

## <a name="function-app"></a>Funktionen-App
Eine Funktions-App bietet einen Ausführungskontext in Azure, in dem Ihre Funktionen ausgeführt werden. Daher ist dies die Bereitstellungs- und Verwaltungseinheit für Ihre Funktionen. Eine Funktions-App besteht aus einer oder mehreren individuellen Funktionen, die zusammen verwaltet, bereitgestellt und skaliert werden. Der Tarif, die Bereitstellungsmethode und die Runtimeversion sind für alle Funktionen in einer Funktions-App gleich. Eine Funktionen-App ist somit eine Möglichkeit, mit der Sie Ihre Funktionen organisieren und kollektiv verwalten können. Weitere Informationen finden Sie unter [Verwalten einer Funktions-App](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Alle Funktionen in einer Funktions-App müssen in der gleichen Programmiersprache erstellt werden. In [Vorgängerversionen](functions-versions.md) der Azure Functions-Runtime war dies nicht erforderlich.

## <a name="folder-structure"></a>Ordnerstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Oben sehen Sie die standardmäßige (und empfohlene) Ordnerstruktur für eine Funktions-App. Falls Sie den Dateispeicherort des Codes einer Funktion ändern möchten, ändern Sie den Abschnitt `scriptFile` der Datei _function.json_. Außerdem empfehlen wir, das Projekt mithilfe der [Paketbereitstellung](deployment-zip-push.md) in Ihrer Funktions-App in Azure bereitzustellen. Sie können auch vorhandene Tools wie [Continuous Integration und Continuous Deployment](functions-continuous-deployment.md) und Azure DevOps verwenden.

> [!NOTE]
> Wenn Sie ein Paket manuell bereitstellen, müssen die Datei _host.json_ und die Funktionsordner direkt im Ordner `wwwroot` bereitgestellt werden. Schließen Sie den Ordner `wwwroot` nicht in Ihre Bereitstellungen ein. Andernfalls erhalten Sie `wwwroot\wwwroot`-Ordner.

#### <a name="use-local-tools-and-publishing"></a>Verwenden von lokalen Tools und Veröffentlichung
Funktions-Apps können mit verschiedenen Tools erstellt und veröffentlicht werden, darunter [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) und [Azure Functions Core Tools](./functions-develop-local.md). Weitere Informationen finden Sie unter [Lokales Codieren und Testen von Azure Functions](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Bearbeiten von Funktionen im Azure-Portal
Mit dem integrierten Functions-Editor im Azure-Portal können Sie Ihren Code und die Datei *function.json* direkt inline aktualisieren. Dies wird nur für kleine Änderungen oder Proof of Concept-Projekte empfohlen. Die bewährte Methode ist die Verwendung eines lokalen Entwicklungstools wie Visual Studio Code.

## <a name="parallel-execution"></a>Parallele Ausführung
Wenn die Auslösung mehrerer Ereignisse schneller erfolgt als die Runtime einer Singlethreadfunktion sie verarbeiten kann, kann die Runtime die Funktion mehrmals parallel aufrufen.  Wenn eine Funktionen-App den [verbrauchsbasierten Hostingplan](functions-scale.md#how-the-consumption-and-premium-plans-work) verwendet, kann die App automatisch aufskaliert werden.  Jede Instanz der Funktionen-App – unabhängig davon, ob die App im verbrauchsbasierten Hostingplan oder einem regulären [App Service-Hostingplan](../app-service/overview-hosting-plans.md) ausgeführt wird – kann gleichzeitige Funktionsaufrufe über mehrere Threads parallel verarbeiten.  Die maximale Anzahl gleichzeitiger Funktionsaufrufe in jeder Funktionen-App-Instanz variiert je nach Art des verwendeten Triggers sowie je nach den Ressourcen, die von anderen Funktionen innerhalb der Funktionen-App verwendet werden.

## <a name="functions-runtime-versioning"></a>Versionsverwaltung der Functions-Runtime

Sie können die Version der Functions-Runtime mit der App-Einstellung `FUNCTIONS_EXTENSION_VERSION` konfigurieren. Der Wert „~3“ bedeutet beispielsweise, dass für Ihre Funktions-App „3.x“ als Hauptversion verwendet wird. Funktions-Apps werden auf jede neue Nebenversion aktualisiert, wenn sie freigegeben werden. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md), einschließlich der Informationen zum Anzeigen der genauen Version Ihrer Funktions-App.

## <a name="repositories"></a>Repositorys
Der Code für Azure Functions ist Open Source und in GitHub-Repositorys gespeichert:

* [Azure-Funktionen](https://github.com/Azure/Azure-Functions)
* [Azure Functions-Host](https://github.com/Azure/azure-functions-host/)
* [Azure Functions-Portal](https://github.com/azure/azure-functions-ux)
* [Azure Functions-Vorlagen](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-Erweiterungen](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindungen
Hier finden Sie eine Tabelle aller unterstützten Bindungen.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Haben Sie Probleme mit Fehlern, die aus den Bindungen stammen? Lesen Sie die Dokumentation [Fehlerbehandlung in Azure Functions](functions-bindings-error-pages.md).

## <a name="reporting-issues"></a>Melden von Problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)
* [Lokales Codieren und Testen von Azure Functions](./functions-develop-local.md)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md)
* [Node.js-Entwicklerreferenz zu Azure Functions](functions-reference-node.md)