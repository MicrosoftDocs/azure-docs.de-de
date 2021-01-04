---
title: Einführung in die integrierte Unterstützung von Jupyter Notebooks in Azure Cosmos DB (Vorschau)
description: Es wird beschrieben, wie Sie die integrierte Unterstützung für Jupyter Notebooks in Azure Cosmos DB nutzen können, um Abfragen interaktiv auszuführen.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 8dca9e3e29796618b905c4d266eb674f82565969
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097616"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Integrierte Unterstützung von Jupyter Notebooks in Azure Cosmos DB (Vorschau)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jupyter Notebook ist eine Open-Source-Webanwendung, mit der Sie Dokumente mit Livecode, Gleichungen, Visualisierungen und narrativem Text erstellen und teilen können. 

In Azure Cosmos DB integrierte Jupyter Notebooks sind direkt in das Azure-Portal und in Ihre Azure Cosmos DB-Konten integriert, sodass sie bequem und einfach verwendet werden können. Entwickler, wissenschaftliche Fachkräfte für Daten, Techniker und Analysten können die vertraute Oberfläche der Jupyter Notebooks für Datenuntersuchungen, Datenbereinigungen, Datentransformationen, numerische Simulationen, statistische Modellierung, Datenvisualisierung und maschinelles Lernen verwenden.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Jupyter Notebook-Visualisierungen in Azure Cosmos DB":::

Azure Cosmos DB unterstützt sowohl C#- als auch Python-Notebooks für alle APIs einschließlich der Core- (SQL), Cassandra-, Gremlin-, Tabellen-API und der API für MongoDB. Im Notebook können Sie integrierte Befehle und Funktionen nutzen, die das Erstellen von Azure Cosmos DB-Ressourcen, das Hochladen von Daten sowie das Abfragen und Visualisieren Ihrer Daten in Azure Cosmos DB erleichtern. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Unterstützung von Jupyter Notebooks in Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Vorteile von Jupyter Notebooks

Jupyter Notebooks wurden ursprünglich für Data Science-Anwendungen entwickelt, die in Python und R geschrieben wurden. Sie können aber auf verschiedene Weise für andere Arten von Projekten verwendet werden, z. B.:

**Datenvisualisierung:** Mit Jupyter Notebooks können Sie Daten in Form eines freigegebenen Notebooks visualisieren, mit dem ein Dataset als Grafik gerendert wird. Sie können Visualisierungen erstellen, interaktive Änderungen am freigegebenen Code und Dataset vornehmen und die Ergebnisse freigeben.

**Codefreigabe:** Dienste wie GitHub bieten Möglichkeiten zum Freigeben von Code, sind aber größtenteils nicht interaktiv. Mit einem Jupyter Notebook können Sie Code anzeigen und ausführen und die Ergebnisse direkt im Azure-Portal darstellen.

**Liveinteraktionen mit Code:** Der Code in einem Jupyter Notebook ist dynamisch. Sie können ihn bearbeiten und die Aktualisierungen inkrementell in Echtzeit ausführen. Sie können auch Benutzersteuerelemente (z. B. Schieberegler oder Texteingabefelder) einbetten, die als Eingabequellen für Code, Demos oder Proof of Concepts (POCs) verwendet werden.

**Dokumentation zu Codebeispielen und Ergebnisse von Datenuntersuchungen:** Wenn Sie über einen Codeabschnitt verfügen und die Funktionsweise zeilenweise erläutern möchten, können Sie ihn in ein Jupyter-Notebook einbetten. Sie können mit der Interaktivität gleichzeitig die Dokumentation hinzufügen.

**Integrierte Befehle für Azure Cosmos DB:** Die integrierten Magic-Befehle von Azure Cosmos DB ermöglichen eine einfache Interaktion mit Ihrem Konto. Sie können Befehle wie „%%upload“ und „%%sql“ verwenden, um Daten in einen Container hochzuladen und mithilfe der [SQL-API-Syntax](sql-query-getting-started.md) abzufragen. Sie müssen keinen zusätzlichen benutzerdefinierten Code schreiben.

**Umgebung vom Typ „Alles an einem Ort“:** Bei Jupyter Notebooks werden Code, Rich Text, Bilder, Videos, Animationen, mathematische Gleichungen, Plots, Karten, interaktive Werte, Widgets und grafische Benutzeroberflächen zu einem Dokument kombiniert.

## <a name="components-of-a-jupyter-notebook"></a>Komponenten eines Jupyter Notebooks

Jupyter Notebooks können mehrere Typen von Komponenten enthalten, die jeweils in diskreten Blöcken oder Zellen organisiert sind:

**Text und HTML:** Nur-Text oder in der Markdownsyntax kommentierter Text zum Generieren von HTML-Code kann jederzeit in das Dokument eingefügt werden. CSS-Stile können auch inline eingefügt oder der Vorlage hinzugefügt werden, die zum Generieren des Notebooks verwendet wird.

**Code und Ausgabe:** Jupyter Notebooks unterstützen Python- und C#-Code. Die Ergebnisse des ausgeführten Codes werden direkt nach den Codeblöcken angezeigt, und die Codeblöcke können mehrfach in beliebiger Reihenfolge ausgeführt werden.

**Visualisierungen:** Mit Modulen wie z. B. Matplotlib, Plotly und Bokeh können Sie Grafiken und Diagramme aus dem Code generieren. Ähnlich wie bei der Ausgabe werden diese Visualisierungen inline neben dem Code angezeigt, mit dem sie generiert werden. Ähnlich wie bei der Ausgabe werden diese Visualisierungen inline neben dem Code angezeigt, mit dem sie generiert werden.

**Multimedia:** Da Jupyter Notebooks auf Webtechnologien basieren, können alle Multimediatypen angezeigt werden, die von einer Webseite unterstützt werden. Sie können sie als HTML-Elemente in ein Notebook einbetten oder mit dem `IPython.display`-Modul programmgesteuert generieren.

**Daten:** Sie können die Daten aus Azure Cosmos-Containern oder die Ergebnisse von Abfragen programmgesteuert in ein Jupyter Notebook importieren. Verwenden Sie integrierte Magic-Befehle, um Daten in Azure Cosmos DB hochzuladen oder abzufragen. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit integrierten Jupyter Notebooks in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Enable notebooks in an Azure Cosmos account](enable-notebooks.md) (Aktivieren von Notebooks in einem Azure Cosmos-Konto)
* [Verwenden von Python-Notebookfunktionen und -befehlen](use-python-notebook-features-and-commands.md)
* [Verwenden von C#-Notebookfunktionen und -befehlen](use-csharp-notebook-features-and-commands.md)
