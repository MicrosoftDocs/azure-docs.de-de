---
title: Tool zum Kopieren von Daten in Azure Data Factory | Microsoft Docs
description: Stellt Informationen zum Tool zum Kopieren von Daten der Benutzeroberfläche von Azure Data Factory bereit.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787696"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Tool zum Kopieren von Daten in Azure Data Factory
Das Tool zum Kopieren von Daten in Azure Data Factory vereinfacht und optimiert die Verarbeitung der Sammelerfassung von Daten in einem Data Lake. Dies ist normalerweise ein erster Schritt in einem End-to-End-Szenario für die Datenintegration.  Sie sparen Zeit, insbesondere bei Verwendung von Azure Data Factory für die erstmalige Sammelerfassung von Daten aus einer Datenquelle. Die Verwendung dieses Tools ist z.B. mit den folgenden Vorteilen verbunden:

- Wenn Sie das Tool zum Kopieren von Daten in Azure Data Factory verwenden, müssen Sie die Data Factory-Definitionen für verknüpfte Dienste, Datasets, Pipelines, Aktivitäten und Trigger nicht verstehen. 
- Der Fluss des Tools zum Kopieren von Daten ist für das Laden von Daten in einen Data Lake intuitiv. Das Tool erstellt automatisch alle erforderlichen Data Factory-Ressourcen zum Kopieren von Daten aus dem ausgewählten Quelldatenspeicher in den ausgewählten Ziel-/Senkendatenspeicher. 
- Das Tool zum Kopieren von Daten hilft Ihnen dabei, die Daten zu überprüfen, die zum Zeitpunkt der Erstellung des Dokuments erfasst werden, wodurch Sie mögliche Fehler bereits am Anfang vermeiden können.
- Wenn Sie komplexe Geschäftslogik implementieren müssen, um Daten in einen Data Lake zu laden, können Sie die vom Tool zum Kopieren von Daten erstellten Data Factory-Ressourcen weiterhin bearbeiten, indem Sie die Dokumenterstellung pro Aktivität in der Data Factory-Benutzeroberfläche verwenden. 

In der folgenden Tabelle finden Sie Hinweise dazu, unter welchen Umständen Sie das Tool zum Kopieren von Daten bzw. die Dokumenterstellung pro Aktivität in der Data Factory-Benutzeroberfläche verwenden sollten: 

| Tool zum Kopieren von Daten | Dokumenterstellung pro Aktivität (Kopieraktivität) |
| -------------- | -------------------------------------- |
| Sie möchten einfach einen Datenladetask erstellen, ohne sich mit den Entitäten von Azure Data Factory vertraut zu machen (verknüpfte Dienste, Datasets, Pipelines usw.). | Sie möchten komplexe und flexible Logik für das Laden von Daten in den Data Lake implementieren. |
| Sie möchten schnell eine große Anzahl von Datenartefakten in einen Data Lake laden. | Sie möchten die Kopieraktivität mit nachfolgenden Aktivitäten zur Bereinigung oder Verarbeitung von Daten verketten. |

Klicken Sie zum Starten des Tools zum Kopieren von Daten auf der Startseite Ihrer Data Factory auf die Kachel **Daten kopieren**.

![Seite „Erste Schritte“: Link zum Tool zum Kopieren von Daten](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitiver Fluss zum Laden von Daten in einen Data Lake
Mit diesem Tool können Sie mit einem intuitiven Fluss problemlos Daten in wenigen Minuten aus einer Vielzahl von Quellen an verschiedene Ziele verschieben:  

1. Konfigurieren Sie Einstellungen für die **Quelle**.
2. Konfigurieren Sie Einstellungen für das **Ziel**. 
3. Konfigurieren Sie **erweiterte Einstellungen** für den Kopiervorgang, z.B. Spaltenzuordnung, Leistungseinstellungen und Fehlertoleranzeinstellungen. 
4. Geben Sie einen **Zeitplan** für den Datenladetask an. 
5. Überprüfen Sie die **Zusammenfassung** der Data Factory-Entitäten, die erstellt werden sollen. 
6. **Bearbeiten** Sie die Pipeline, um die Einstellungen für die Kopieraktivität nach Bedarf zu aktualisieren. 

   Das Tool wurde von Beginn an für Big Data konzipiert und bietet Unterstützung für verschiedene Daten- und Objekttypen. Sie können es zum Verschieben von Hunderten von Ordnern, Dateien oder Tabellen verwenden. Das Tool unterstützt die automatische Datenvorschau, Schemaerfassung und automatische Zuordnung sowie das Filtern von Daten.

![Tool zum Kopieren von Daten](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatische Datenvorschau
Sie können einen Teil der Daten aus dem ausgewählten Quelldatenspeicher als Vorschau anzeigen. Auf diese Weise können Sie die Daten überprüfen, die kopiert werden. Wenn die Datenquelle eine Textdatei ist, analysiert das Tool zum Kopieren von Daten darüber hinaus diese Datei und erkennt Trennzeichen für Zeilen und Spalten sowie das Schema automatisch.

![Dateieinstellungen](./media/copy-data-tool/file-format-settings.png)

Nach der Erkennung:

![Erkannte Dateieinstellungen und Vorschau](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schemaerfassung und automatische Zuordnung
Das Schema der Datenquelle ist in vielen Fällen ggf. nicht mit dem Schema des Datenziels identisch. In diesem Szenario müssen die Spalten des Quellschemas den Spalten des Zielschemas zugeordnet werden.

Das Tool zum Kopieren von Daten überwacht und erlernt Ihr Verhalten beim Zuordnen von Spalten zwischen Quell- und Zielspeichern. Nachdem Sie mindestens eine Spalte aus dem Quelldatenspeicher ausgewählt und dem Zielschema zugeordnet haben, beginnt das Tool zum Kopieren von Daten das Muster für Spaltenpaare zu analysieren, die Sie auf beiden Seiten ausgewählt haben. Dann wendet es das gleiche Muster auf den Rest der Spalten an. Daher erkennen Sie bereits nach wenigen Klicks, dass alle Spalten auf die gewünschte Art und Weise dem Ziel zugeordnet wurden.  Wenn Sie mit der Auswahl der Spaltenzuordnung durch das Tool zum Kopieren von Daten nicht zufrieden sind, können Sie diese ignorieren und mit der manuellen Zuordnung der Spalten fortfahren. In der Zwischenzeit erlernt und aktualisiert das Tool zum Kopieren von Daten ständig das Muster und erreicht schließlich das richtige Muster für die gewünschte Spaltenzuordnung. 

> [!NOTE]
> Beim Kopieren von Daten aus SQL Server oder der Azure SQL-Datenbank in das Azure SQL Data Warehouse unterstützt das Tool zum Kopieren von Daten die automatische Erstellung der Tabelle unter Verwendung des Quellschemas, falls die Tabelle nicht im Zielspeicher vorhanden ist. 

## <a name="filter-data"></a>Filtern von Daten
Sie können Quelldaten filtern, wenn nur bestimmte Daten in den Senkendatenspeicher kopiert werden sollen. Durch Filtern wird das Datenvolumen der in den Senkendatenspeicher kopierten Daten verringert. Der Durchsatz des Kopiervorgangs steigt. Das Tool zum Kopieren von Daten bietet flexible Möglichkeiten, die Daten einer relationalen Datenbank mittels der SQL-Abfragesprache oder Dateien in einem Azure-Blobordner zu filtern. 

### <a name="filter-data-in-a-database"></a>Filtern von Daten in einer Datenbank
Der folgende Screenshot zeigt eine SQL-Abfrage zum Filtern der Daten.

![Filtern von Daten in einer Datenbank](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtern von Daten in einem Azure-Blobordner
Sie können Variablen im Ordnerpfad verwenden, um Daten aus einem Ordner zu kopieren. Folgende Variablen werden unterstützt: **{year}** , **{month}** , **{day}** , **{hour}** und **{minute}** . Beispiel: Eingabeordner/{year}/{month}/{day}. 

Angenommen, Sie haben Eingabeordner im folgenden Format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem der Ordner (z.B. 2016->03->01->02), und klicken Sie auf **Auswählen**. Das Textfeld sollte jetzt „2016/03/01/02“ enthalten. 

Ersetzen Sie nun **2016** durch **{year}** , **03** durch **{month}** , **01** durch **{day}** , **02** durch **{hour}** , und drücken Sie dann die **TAB**-TASTE. Daraufhin werden Dropdownlisten zum Auswählen des Formats für diese vier Variablen angezeigt:

![Filtern von Dateien oder Ordnern](./media/copy-data-tool/filter-file-or-folder.png)

Die Tool zum Kopieren von Daten generiert Parameter mit Ausdrücken, Funktionen und Systemvariablen, die verwendet werden können, um {year}, {month}, {day}, {hour} und {minute} beim Erstellen der Pipeline darzustellen. Weitere Informationen finden Sie im Artikel [Lesen oder Schreiben partitionierter Daten](how-to-read-write-partitioned-data.md).

## <a name="scheduling-options"></a>Planungsoptionen
Sie können den Kopiervorgang einmal oder nach einem Zeitplan (stündlich, täglich usw.) ausführen. Diese Optionen können für die Connectors in verschiedenen Umgebungen verwendet werden, z.B. in lokalen Umgebungen, in der Cloud und auf dem lokalen Desktop. 

Ein einmaliger Kopiervorgang ermöglicht nur einmal das Verschieben von Daten aus einer Quelle in ein Ziel. Er gilt für Daten jeder Größe in jedem unterstützten Format. Das Kopieren nach einem Zeitplan ermöglicht Ihnen das Kopieren von Daten in einer angegebenen Serie. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen usw.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

![Planungsoptionen](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit diesen Tutorials vertraut, die das Tool zum Kopieren von Daten verwenden:

- [Schnellstart: Erstellen einer Data Factory mithilfe des Tools zum Kopieren von Daten](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Kopieren von Daten in Azure mit dem Tool zum Kopieren von Daten](tutorial-copy-data-tool.md) 
- [Tutorial: Kopieren lokaler Daten nach Azure mit dem Tool zum Kopieren von Daten](tutorial-hybrid-copy-data-tool.md)
