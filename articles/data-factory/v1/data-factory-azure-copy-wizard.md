---
title: Kopier-Assistent in Azure Data Factory | Microsoft Docs
description: Erfahren Sie, wie Sie den Kopier-Assistenten von Azure Data Factory verwenden, um Daten aus unterstützten Datenquellen in Senken zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60567672"
---
# <a name="azure-data-factory-copy-wizard"></a>Kopier-Assistent in Azure Data Factory
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. 

Der Kopier-Assistent in Azure Data Factory vereinfacht den Datenerfassungsvorgang (üblicherweise der erste Schritt in einem Szenario mit End-to-End-Datenintegration). Zur Arbeit mit dem Kopier-Assistenten in Azure Data Factory sind Kenntnisse hinsichtlich der JSON-Definitionen für verknüpfte Dienste, Datasets und Pipelines nicht erforderlich. Der Assistent erstellt automatisch eine Pipeline, um Daten aus der ausgewählten Datenquelle in das gewünschte Ziel zu kopieren. Darüber hinaus können Sie mit dem Kopier-Assistenten die erfassten Daten zum Zeitpunkt der Erstellung überprüfen. Dies spart insbesondere dann Zeit, wenn Sie Daten zum ersten Mal aus der Datenquelle erfassen. Klicken Sie zum Starten des Kopier-Assistenten auf der Startseite Ihrer Data Factory auf die Kachel **Daten kopieren** .

![Kopier-Assistent](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Für Big Data konzipiert
Mit diesem Assistenten können Sie problemlos Daten in wenigen Minuten aus einer Vielzahl von Quellen an verschiedene Ziele verschieben. Nachdem Sie den Assistenten ausgeführt haben, werden eine Pipeline mit einer Kopieraktivität sowie einige davon abhängige Data Factory-Entitäten (verknüpfte Dienste und Datasets) automatisch erstellt. Keine weiteren Schritte sind erforderlich, um die Pipeline zu erstellen.   

![Auswählen einer Datenquelle](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Im [Tutorial zum Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie schrittweise Anweisungen zum Erstellen einer Beispielpipeline, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbanktabelle zu kopieren.
>
>

Der Assistent wurde von Beginn an für Big Data konzipiert und bietet Unterstützung für verschiedene Daten- und Objekttypen. Sie können Data Factory-Pipelines erstellen, die Hunderte von Ordnern, Dateien oder Tabellen verschieben können. Der Assistent unterstützt die automatische Datenvorschau, Schemaerfassung und -zuordnung sowie das Filtern von Daten.

## <a name="automatic-data-preview"></a>Automatische Datenvorschau
Sie können eine Vorschau für einen Teil der Daten aus der ausgewählten Datenquelle anzeigen, um zu überprüfen, ob es tatsächlich die Daten sind, die Sie kopieren möchten. Wenn die Datenquelle eine Textdatei ist, analysiert der Kopier-Assistent darüber hinaus diese Datei und erfasst Trennzeichen für Zeilen und Spalten sowie das Schema automatisch.

![Dateiformateinstellungen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schemaerfassung und -zuordnung
Das Schema der Eingabedaten stimmt nicht in jedem Fall mit dem Schema der Ausgabedaten überein. In diesem Szenario müssen die Spalten des Quellschemas den Spalten des Zielschemas zugeordnet werden.

> [!TIP]
> Beim Kopieren von Daten aus SQL Server oder Azure SQL-Datenbank in Azure SQL Data Warehouse unterstützt Data Factory die automatische Tabellenerstellung anhand des Quellschemas, wenn die Tabelle im Zielspeicher nicht vorhanden ist. Weitere Informationen finden Sie unter [Verschieben von Daten in und aus Azure SQL Data Warehouse mithilfe von Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Verwenden Sie eine Dropdownliste, um eine Spalte aus dem Quellschema auszuwählen, die einer Spalte im Zielschema zugeordnet werden soll. Der Kopier-Assistent versucht, das Muster für die Spaltenzuordnung zu verstehen. Er wendet das gleiche Muster auf die restlichen Spalten an, damit Sie nicht jede Spalte einzeln auswählen müssen, um die Schemazuordnung abzuschließen. Falls gewünscht, können Sie diese Zuordnungen überschreiben, indem Sie mithilfe der Dropdownlisten die Spalten einzeln zuordnen. Das Muster wird genauer, wenn Sie mehr Spalten zugeordnet haben. Der Kopier-Assistent aktualisiert das Muster ständig und erreicht letztlich das richtige Muster für die Spaltenzuordnung, die Sie erreichen möchten.     

![Schemazuordnung](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtern von Daten
Sie können Quelldaten filtern, wenn nur bestimmte Daten in den Senkendatenspeicher kopiert werden sollen. Durch Filtern wird das Datenvolumen der in den Senkendatenspeicher kopierten Daten verringert. Der Durchsatz des Kopiervorgangs steigt. Der Assistent bietet flexible Möglichkeiten, die Daten einer relationalen Datenbank mittels der SQL-Abfragesprache oder Dateien in einem Azure-Blobordner mittels [Data Factory-Funktionen und -Variablen](data-factory-functions-variables.md) zu filtern.   

### <a name="filtering-of-data-in-a-database"></a>Filtern von Datenbankdaten
Der folgende Screenshot zeigt eine SQL-Abfrage mit der Funktion `Text.Format` und der Variablen `WindowStart`.

![Überprüfen von Ausdrücken](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtern von Daten in einem Azure-Blobordner
Sie können im Ordnerpfad Variablen verwenden, um Daten aus einem Ordner zu kopieren, der zur Laufzeit auf der Grundlage von [Systemvariablen](data-factory-functions-variables.md#data-factory-system-variables) bestimmt wird. Folgende Variablen werden unterstützt: **{year}** , **{month}** , **{day}** , **{hour}** , **{minute}** und **{custom}** . Beispiel: Eingabeordner/{year}/{month}/{day}.

Angenommen, Sie haben Eingabeordner im folgenden Format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem der Ordner (z.B. 2016->03->01->02), und klicken Sie auf **Auswählen**. Das Textfeld sollte jetzt `2016/03/01/02` enthalten. Ersetzen Sie nun **2016** durch **{year}** , **03** durch **{month}** , **01** durch **{day}** , **02** durch **{hour}** , und drücken Sie die **TAB**-TASTE. Daraufhin werden Dropdownlisten zum Auswählen des Formats für diese vier Variablen angezeigt:

![Verwenden von Systemvariablen](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Sie können auch eine **benutzerdefinierte** Variable und beliebige [unterstützte Formatzeichenfolgen](https://msdn.microsoft.com/library/8kb3ddd4.aspx)verwenden (siehe Screenshot unten). Um einen Ordner mit dieser Struktur auszuwählen, verwenden Sie zunächst die Schaltfläche **Durchsuchen** . Ersetzen Sie dann einen Wert mit **{custom}** , und drücken Sie die **TAB**-TASTE, um das Textfeld anzuzeigen, in dem Sie die Formatzeichenfolge eingeben können.     

![Verwenden von benutzerdefinierten Variablen](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Planungsoptionen
Sie können den Kopiervorgang einmal oder nach einem Zeitplan (stündlich, täglich usw.) ausführen. Beide Optionen können für die verschiedensten Connectors in unterschiedlichen Umgebungen verwendet werden, einschließlich lokaler Kopien, Cloudkopien und Kopien auf dem lokalen Desktop.

Ein einmaliger Kopiervorgang ermöglicht nur einmal das Verschieben von Daten aus einer Quelle in ein Ziel. Er gilt für Daten jeder Größe in jedem unterstützten Format. Das Kopieren nach einem Zeitplan ermöglicht Ihnen das Kopieren von Daten nach einem vorgeschriebenen Wiederholungszyklus. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen usw.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

![Planen von Eigenschaften](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nächste Schritte
Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mit einer Kopieraktivität unter Verwendung des Data Factory-Kopier-Assistenten finden Sie im [Tutorial: Erstellen einer Pipeline mithilfe des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).
