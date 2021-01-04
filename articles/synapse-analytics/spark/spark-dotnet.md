---
title: Verwenden von .NET für Apache Spark
description: Erfahren Sie mehr über die Verwendung von .NET und Apache Spark für Batchverarbeitung, Echtzeitstreaming, Machine Learning und das Schreiben von Ad-hoc-Abfragen in Azure Synapse Analytics-Notebooks.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 753c7ff48960711cbedbf6b5d8644a26502dd413
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023740"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Verwenden von .NET für Apache Spark mit Azure Synapse Analytics

[.NET für Apache Spark](https://dot.net/spark) bietet kostenlose, [Open-Source](https://github.com/dotnet/spark)-basierte und plattformübergreifende .NET-Unterstützung für Spark. 

Es stellt .NET-Bindungen für Spark bereit, mit denen Sie über C# und F# auf Spark-APIs zugreifen können. Mit .NET für Apache Spark haben Sie die Möglichkeit, in .NET geschriebene benutzerdefinierte Funktionen für Spark zu schreiben und auszuführen. Mit den .NET-APIs für Spark können Sie auf alle Aspekte von Spark-Datenrahmen zugreifen, die Ihnen helfen, Ihre Daten zu analysieren, einschließlich Spark SQL, Delta Lake und strukturiertem Streaming.

Sie können Daten mit .NET für Apache Spark mithilfe von Spark-Batchauftragsdefinitionen oder mit interaktiven Azure Synapse Analytics-Notebooks analysieren. In diesem Artikel erfahren Sie, wie Sie .NET für Apache Spark mit Azure Synapse mit beiden Methoden verwenden.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Übermitteln von Batchaufträgen mithilfe der Spark-Auftragsdefinition

Besuchen Sie das Tutorial, um zu erfahren, wie Sie Azure Synapse Analytics zum [Erstellen von Apache Spark-Auftragsdefinitionen für Synapse Spark-Pools](apache-spark-job-definitions.md) verwenden. Wenn Sie Ihre App für die Übermittlung an Azure Synapse nicht gepackt haben, führen Sie die folgenden Schritte aus.

1. Führen Sie die folgenden Befehle aus, um Ihre App zu veröffentlichen. Stellen Sie sicher, dass Sie *mySparkApp* durch den Pfad zu Ihrer App ersetzen.

   **Unter Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **Unter Linux:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. Zippen Sie den Inhalt des Veröffentlichungsordners, z. B. `publish.zip`, der als Ergebnis von Schritt 1 erstellt wurde. Alle Assemblys sollten sich auf der ersten Ebene der ZIP-Datei befinden und es sollte keine Zwischenordnerebene geben. Das bedeutet, dass beim Entpacken von `publish.zip` alle Assemblys in Ihr aktuelles Arbeitsverzeichnis extrahiert werden.

    **Unter Windows:**

    Verwenden Sie ein Extraktionsprogramm wie [7-Zip](https://www.7-zip.org/) oder [WinZip](https://www.winzip.com/), um die Datei mit allen veröffentlichten Binärdateien in das bin-Verzeichnis zu extrahieren.

    **Unter Linux:**

    Öffnen Sie eine Bash-Shell, wechseln Sie mit „cd“ in das Verzeichnis „bin“, das alle veröffentlichten Binärdateien enthält, und führen Sie den folgenden Befehl aus.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET für Apache Spark mit Azure Synapse Analytics-Notebooks 

Notebooks sind eine hervorragende Möglichkeit, um Prototypen Ihrer .NET für Apache Spark-Pipelines und -Szenarios zu erstellen. Sie können schnell und effizient mit der Arbeit mit Ihren Daten beginnen sowie sie verstehen, filtern, anzeigen und visualisieren. 

Data Engineers, Data Scientists, Wirtschaftsanalytiker und Machine Learning-Techniker können alle über ein gemeinsam genutztes, interaktives Dokument zusammenarbeiten. Sie sehen unmittelbare Ergebnisse der Datenuntersuchung und können Ihre Daten im selben Notebook visualisieren.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Verwenden von .NET für Apache Spark-Notebooks

Wenn Sie ein neues Notebook erstellen, wählen Sie einen Sprachkernel aus, mit dem Sie Ihre Geschäftslogik umsetzen möchten. Kernelunterstützung ist für mehrere Sprachen verfügbar, einschließlich C#.

Um .NET für Apache Spark in Ihrem Azure Synapse Analytics-Notebook zu verwenden, wählen Sie **.NET Spark (C#)** als Ihren Kernel aus, und fügen Sie das Notebook an einen vorhandenen serverlosen Apache Spark-Pool an.

Das .NET Spark-Notebook basiert auf den [interaktiven .NET](https://github.com/dotnet/interactive)-Erfahrungen und bietet interaktive C#-Erfahrungen mit der Möglichkeit zur Verwendung des standardmäßigen .NET für Spark mit bereits vordefinierter Spark-Sitzungsvariable `spark`.

### <a name="install-nuget-packages-in-notebooks"></a>Installieren von NuGet-Paketen in Notebooks

Sie können NuGet-Pakete Ihrer Wahl in Ihrem Notebook installieren, indem Sie den Magic-Befehl `#r nuget` vor dem Namen des NuGet-Pakets verwenden. Das folgende Diagramm zeigt ein Beispiel:

![Screenshot, der die Verwendung von #r zum Installieren eines NuGet-Pakets in einem Spark .NET-Notebook zeigt.](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Weitere Informationen zum Arbeiten mit NuGet-Paketen in Notebooks finden Sie in der [.NET Interactive-Dokumentation](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md).

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET für Apache Spark-Funktionen des C#-Kernels

Die folgenden Funktionen sind verfügbar, wenn Sie .NET für Apache Spark im Azure Synapse Analytics-Notebook verwenden:

* Deklaratives HTML: Generieren von Ausgaben aus Ihren Zellen mithilfe von HTML-Syntax wie Headern, Aufzählungen und sogar der Anzeige von Bildern.
* Einfache C#-Anweisungen (z. B. Zuweisungen, Ausgaben in der Konsole, Auslösen von Ausnahmen usw.).
* Mehrzeilige C#-Codeblöcke (z. B. if-Anweisungen, Foreach-Schleifen, Klassendefinitionen usw.).
* Zugriff auf die C#-Standardbibliothek (z. B. System, LINQ, Enumerables usw.).
* Unterstützung für C# 8.0-Sprachfunktionen.
* `spark` als vordefinierte Variable, um Ihnen Zugriff auf Ihre Apache Spark-Sitzung zu gewähren.
* Unterstützung für das Definieren von [benutzerdefinierten .NET-Funktionen, die in Apache Spark ausgeführt werden können](/dotnet/spark/how-to-guides/udf-guide). Um weitere Informationen zur Verwendung von UDFs in interaktiven .NET für Apache Spark-Umgebungen zu erhalten, empfehlen wir [Schreiben und Aufrufen von UDFs in interaktiven .NET für Apache Spark-Umgebungen](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue).
* Unterstützung für die Visualisierung der Ausgabe von Spark-Aufträgen mithilfe verschiedener Diagramme (z. B. Linie, Balken oder Histogramm) und Layouts (z. B. einzeln, überlagert usw.) mithilfe der `XPlot.Plotly`-Bibliothek.
* Möglichkeit zum Einschließen von NuGet-Paketen in Ihr C#-Notebook.

## <a name="next-steps"></a>Nächste Schritte

* [Dokumentation zu .NET für Apache Spark](/dotnet/spark/)
* [Leitfäden zu interaktivem .NET für Apache Spark](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET interaktiv](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
