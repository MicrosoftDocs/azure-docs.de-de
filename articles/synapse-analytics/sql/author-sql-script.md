---
title: SQL-Skripts in Synapse Studio
description: Einführung in SQL-Skripts in Synapse Studio von Azure Synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 077815d117af0328a3466346727e135caaf8ff21
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458006"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>SQL-Skripts in Synapse Studio von Azure Synapse Analytics 

Synapse Studio stellt eine Webschnittstelle für SQL-Skripts zum Erstellen von SQL-Abfragen zur Verfügung. 

## <a name="begin-authoring-in-sql-script"></a>Beginnen der Erstellung in SQL-Skripts 

Es gibt mehrere Möglichkeiten, die Benutzeroberfläche für die Erstellung in SQL-Skripts zu starten. Sie können mit einer der folgenden Methoden ein neues SQL-Skript erstellen.

1. Klicken Sie im Menü „Entwickeln“ auf das Symbol **+** , und wählen Sie **SQL-Skript** aus.

2. Klicken Sie im **Aktionsmenü** auf **New SQL script** (Neues SQL-Skript).

3. Wählen Sie unter „SQL-Skripts entwickeln“ im Menü **Aktionen** die Option **Importieren** aus. Wählen Sie in Ihrem lokalen Speicher ein vorhandenes SQL-Skript aus.
![Neues SQL-Skript: Drei Aktionen](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Erstellen Ihres SQL-Skripts

1. Geben Sie einen Namen für Ihr SQL-Skript an, indem Sie auf die Schaltfläche für die **Eigenschaften** klicken und den dem SQL-Skript zugewiesenen Standardnamen ersetzen. 
![Neues SQL-Skript: Umbenennen](media/author-sql-script/new-sql-script-rename.png)

2. Wählen Sie im Dropdown Menü **Verbinden mit** den spezifischen dedizierten SQL-Pool oder serverlosen SQL-Pool aus. Sofern erforderlich, können Sie alternativ die gewünschte Datenbank unter **Use database** (Datenbank verwenden) auswählen. 
![Neues SQL-Skript: Pool auswählen](media/author-sql-script/new-sql-choose-pool.png)

3. Beginnen Sie mit der Erstellung Ihres SQL-Skripts mithilfe des IntelliSense-Features.

## <a name="run-your-sql-script"></a>Ausführen Ihres SQL-Skripts

Klicken Sie auf die Schaltfläche **Ausführen**, um Ihr SQL-Skript auszuführen. Die Ergebnisse werden standardmäßig in einer Tabelle angezeigt.

![Neues SQL-Skript: Tabelle mit Ergebnissen](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Exportieren Ihrer Ergebnisse

Sie können die Ergebnisse in verschiedenen Formaten (einschließlich CSV, Excel, JSON, XML) in Ihren lokalen Speicher exportieren, indem Sie „Ergebnisse exportieren“ und dann die Erweiterung auswählen.

Sie können die Ergebnisse des SQL-Skripts auch in einem Diagramm visualisieren, indem Sie auf die Schaltfläche **Diagramm** klicken. Wählen Sie unter „Diagrammtyp“ und **Category column** (Kategoriespalte) die gewünschten Werte aus. Sie können das Diagramm als Bild exportieren, indem Sie auf **Save as image** (Als Bild speichern) klicken. 

![Neues SQL-Skript: Diagramm mit Ergebnissen](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Untersuchen von Daten aus einer Parquet-Datei

Sie können Parquet-Dateien in einem Speicherkonto mithilfe eines SQL-Skripts untersuchen, um eine Vorschau des Dateiinhalts anzuzeigen.

![Neues SQL-Skript: sqlod-Parquet-Datei](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-Tabellen, externe Tabellen, Sichten

Durch Klicken auf das **Aktionsmenü** unter „Daten“ können Sie verschiedene Aktionen auswählen, z. B.:

- New SQL script (Neues SQL-Skript)
- Select TOP 1000 rows (Erste 1.000 Zeilen auswählen)
- CREATE
- DROP und CREATE 
 
Untersuchen Sie die verfügbare Geste, indem Sie mit der rechten Maustaste auf die Knoten von SQL-Datenbanken klicken.
 
![Neues SQL-Skript: Datenbank](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Erstellen von Ordnern und Verschieben von SQL-Skripts in einen Ordner

Wählen Sie im Menü „Aktionen“ unter „SQL-Skripts entwickeln“ die Option „Neuer Ordner“ aus. Geben Sie den Namen des neuen Ordners im Popupfenster ein. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-create-folder.png)

Um ein SQL-Skript in einen Ordner zu verschieben, können Sie das SQL-Skript und dann im Menü „Aktionen“ die Option „Verschieben nach“ auswählen. Suchen Sie anschließend im neuen Fenster nach dem Zielordner, und verschieben Sie das SQL-Skript in den ausgewählten Ordner. Sie können das SQL-Skript auch schnell per Drag & Drop in einen Ordner verschieben.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen eines SQL-Skripts finden Sie unter [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
