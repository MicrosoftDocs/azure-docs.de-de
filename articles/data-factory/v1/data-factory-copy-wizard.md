---
title: Einfaches Kopieren von Daten mit dem Kopier-Assistenten – Azure
description: Erfahren Sie, wie Sie den Data Factory-Kopier-Assistenten verwenden, um Daten aus unterstützten Datenquellen in Senken zu kopieren.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3686a22291e8595deabe3e207ce66744c3cdd82
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376903"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Einfaches Kopieren oder Verschieben von Daten mit dem Kopier-Assistenten von Azure Data Factory
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 


Der Azure Data Factory-Kopier-Assistent vereinfacht den Datenerfassungsvorgang (üblicherweise der erste Schritt in einem Szenario mit End-to-End-Datenintegration). Zur Arbeit mit dem Azure Data Factory-Kopier-Assistenten ist die Kenntnis der JSON-Definitionen für verknüpfte Dienste, Datasets und Pipelines nicht erforderlich. Nachdem Sie alle Schritte mit dem Assistenten abgeschlossen haben, erstellt der Assistent allerdings eine Pipeline, um Daten aus der gewählten Datenquelle und in das gewünschte Ziel zu kopieren. Darüber hinaus können Sie die erfassten Daten mit dem Kopier-Assistenten zum Zeitpunkt des Erstellens überprüfen. So sparen Sie Zeit, besonders wenn Sie Daten aus einer bestimmten Datenquelle zum ersten Mal erfassen. Klicken Sie zum Starten des Kopier-Assistenten auf der Startseite Ihrer Data Factory auf die Kachel **Daten kopieren** .

![Kopier-Assistent](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Ein intuitiver Assistent zum Kopieren von Daten
Mit diesem Assistenten können Sie problemlos Daten in wenigen Minuten aus einer Vielzahl von Quellen an verschiedene Ziele verschieben. Nachdem Sie den Assistenten ausgeführt haben, werden eine Pipeline mit einer Kopieraktivität sowie einige davon abhängige Data Factory-Entitäten (verknüpfte Dienste und Datasets) automatisch erstellt. Keine weiteren Schritte sind erforderlich, um die Pipeline zu erstellen.   

![Auswählen einer Datenquelle](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Im [Tutorial zum Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie schrittweise Anweisungen zum Erstellen einer Beispielpipeline, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbanktabelle zu kopieren. 
> 
> 

Der Assistent ist von Beginn an für Big Data ausgelegt. Mit dem Assistenten zum Kopieren von Daten ist es einfach und effizient, Data Factory-Pipelines zu erstellen, die Hunderte von Ordnern, Dateien oder Tabellen verschieben können. Der Assistent unterstützt die folgenden drei Funktionen: Automatische Datenvorschau, Schemaerfassung und -zuordnung und Datenfilterung. 

## <a name="automatic-data-preview"></a>Automatische Datenvorschau
Sie können mit dem Kopier-Assistenten Teile der Daten aus der jeweiligen Datenquelle überprüfen, um festzustellen, ob es sich tatsächlich um die Daten handelt, die Sie kopieren möchten. Darüber hinaus analysiert der Kopier-Assistent, wenn die Datenquelle eine Textdatei ist, diese Datei, und erfasst Trennzeichen für Zeilen und Spalten sowie Schemata automatisch. 

![Dateiformateinstellungen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schemaerfassung und -zuordnung
Das Schema der Eingabedaten stimmt nicht in jedem Fall mit dem Schema der Ausgabedaten überein. In diesem Szenario müssen die Spalten des Quellschemas den Spalten des Zielschemas zugeordnet werden. 

Der Kopier-Assistent ordnet Quell- und Zielschemaspalten automatisch zu. Sie können diese Zuordnungen mithilfe der Dropdownlisten überschreiben oder angeben, dass eine Spalte beim Kopieren übersprungen werden soll.   

![Schemazuordnung](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtern von Daten
Mithilfe des Assistenten können Sie Quelldaten filtern, wenn nur bestimmte Daten in den Ziel-/Senkendatenspeicher kopiert werden sollen. Durch Filtern wird das Datenvolumen der in den Senkendatenspeicher kopierten Daten verringert. Der Durchsatz des Kopiervorgangs steigt. Der Assistent bietet flexible Möglichkeiten, die Daten einer relationalen Datenbank mittels der SQL-Abfragesprache oder Dateien in einem Azure-Blobordner mittels [Data Factory-Funktionen und -Systemvariablen](data-factory-functions-variables.md) zu filtern.   

### <a name="filtering-of-data-in-a-database"></a>Filtern von Datenbankdaten
In diesem Beispiel verwenden wie eine SQL-Abfrage mit der Funktion `Text.Format` und der Variablen `WindowStart`. 

![Überprüfen von Ausdrücken](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtern von Daten in einem Azure-Blobordner
Sie können im Ordnerpfad Variablen verwenden, um Daten aus einem Ordner zu kopieren, der zur Laufzeit auf der Grundlage von [Systemvariablen](data-factory-functions-variables.md#data-factory-system-variables) bestimmt wird. Folgende Variablen werden unterstützt: **{year}** , **{month}** , **{day}** , **{hour}** , **{minute}** und **{custom}** . Beispiel: Eingabeordner/{year}/{month}/{day}.

Angenommen, Sie haben Eingabeordner im folgenden Format:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem der Ordner (z.B. 2016->03->01->02), und klicken Sie auf **Auswählen**. Das Textfeld sollte jetzt `2016/03/01/02` enthalten. Ersetzen Sie nun **2016** durch **{year}** , **03** durch **{month}** , **01** durch **{day}** , **02** durch **{hour}** , und drücken Sie die TAB-TASTE. Daraufhin werden Dropdownlisten zum Auswählen des Formats für diese vier Variablen angezeigt:

![Verwenden von Systemvariablen](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Sie können auch eine **benutzerdefinierte** Variable und beliebige [unterstützte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings)verwenden (siehe Screenshot unten). Um einen Ordner mit dieser Struktur auszuwählen, verwenden Sie zunächst die Schaltfläche **Durchsuchen** . Ersetzen Sie dann einen Wert mit **{custom}** , und betätigen Sie die TAB-Taste, um das Textfeld anzuzeigen, in dem Sie die Formatzeichenfolge eingeben können.     

![Verwenden von benutzerdefinierten Variablen](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Unterstützung für unterschiedliche Daten- und Objekttypen
Indem Sie den Kopier-Assistenten verwenden, können Sie Hunderte von Ordner, Dateien oder Tabellen effizient verschieben.

![Wählen Sie die Tabellen, aus denen Daten kopiert werden sollen.](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Planungsoptionen
Sie können den Kopiervorgang einmal oder nach einem Zeitplan (stündlich, täglich usw.) ausführen. Beide Optionen können für die verschiedensten Connectors für die lokale Kopie, Cloudkopie und die Kopie auf dem lokalen Desktop verwendet werden.

Ein einmaliger Kopiervorgang ermöglicht nur einmal das Verschieben von Daten aus einer Quelle in ein Ziel. Er gilt für Daten jeder Größe in jedem unterstützten Format. Das Kopieren nach einem Zeitplan ermöglicht Ihnen das Kopieren von Daten nach einem vorgeschriebenen Wiederholungszyklus. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen usw.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

![Planen von Eigenschaften](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nächste Schritte
Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mit einer Kopieraktivität unter Verwendung des Data Factory-Kopier-Assistenten finden Sie im [Tutorial: Erstellen einer Pipeline mithilfe des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).