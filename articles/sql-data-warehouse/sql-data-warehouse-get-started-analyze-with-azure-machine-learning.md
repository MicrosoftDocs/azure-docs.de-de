---
title: Analysieren von Daten mit Azure Machine Learning | Microsoft Docs
description: Azure Machine Learning wird verwendet, um ein Predictive Machine Learning-Modell basierend auf in Azure SQL Data Warehouse gespeicherten Daten zu erstellen.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cae2acf98f39030f4ff340d32f1911bb2b5763ae
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860840"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analysieren von Daten mit Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

In diesem Tutorial wird Azure Machine Learning verwendet, um ein Predictive Machine Learning-Modell basierend auf in Azure SQL Data Warehouse gespeicherten Daten zu erstellen. Es wird eine gezielte Marketingkampagne für Adventure Works, einen Fahrradladen, erstellt und dabei vorhergesagt, ob ein Kunde ein Fahrrad kauft.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Lernprogramm ist Folgendes erforderlich:

* Für eine SQL Data Warehouse-Instanz werden AdventureWorksDW-Beispieldaten vorab geladen. Informationen zur Bereitstellung finden Sie unter [Erstellen eines SQL Data Warehouse][Create a SQL Data Warehouse]. Wählen Sie die Option zum Laden der Beispieldaten. Wenn Sie bereits über ein Data Warehouse verfügen, aber noch keine Beispieldaten besitzen, können Sie [Beispieldaten manuell laden][load sample data manually].

## <a name="1-get-the-data"></a>1. Abrufen von Daten
Die Daten befinden sich in der Sicht „dbo.vTargetMail“ in der AdventureWorksDW-Datenbank. Gehen Sie wie folgt vor, um diese Daten zu lesen:

1. Melden Sie sich bei [Azure Machine Learning Studio][Azure Machine Learning studio] an, und klicken Sie auf „Meine Experimente“.
2. Klicken Sie unten links im Bildschirm auf **+ Neu**, und wählen Sie **Leeres Experiment** aus.
3. Geben Sie einen Namen für Ihr Experiment ein: Gezieltes Marketing.
4. Ziehen Sie unter **Dateneingabe und -ausgabe** das Modul **Import Data** aus dem Modulbereich in den Zeichenbereich.
5. Geben Sie im Eigenschaftenbereich ausführliche Informationen zu Ihrer SQL Data Warehouse-Datenbank ein.
6. Geben Sie die **Datenbankabfrage** zum Lesen der für Sie interessanten Daten an.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf **Ausführen** klicken.
![Ausführen des Experiments][1]

Klicken Sie nach Abschluss des Experiments auf den Ausgabeport im unteren Bereich des Reader-Moduls, und wählen Sie **Visualisieren** , um die importierten Daten anzuzeigen.
![Anzeigen der importierten Daten][3]

## <a name="2-clean-the-data"></a>2. Bereinigen der Daten
Löschen Sie einige Spalten, die für das Modell nicht relevant sind, um die Daten zu bereinigen. Gehen Sie dazu folgendermaßen vor:

1. Ziehen Sie unter **Datentransformation > Manipulation** das Modul **Select Columns in Dataset** in den Zeichenbereich. Verbinden Sie dieses Modul mit dem Modul **Import Data**.
2. Klicken Sie im Eigenschaftenbereich auf **Spaltenauswahl starten** , um die zu löschenden Spalten anzugeben.
   ![Project Columns][4]
3. Schließen Sie zwei Spalten aus: „CustomerAlternateKey“ und „GeographyKey“.
   ![Entfernen überflüssiger Spalten][5]

## <a name="3-build-the-model"></a>3. Erstellen des Modells
Wir teilen die Daten im Verhältnis 80:20: 80 % zum Trainieren eines Machine Learning-Modells und 20 % zum Testen des Modells. Für dieses binäre Klassifizierungsproblem nutzen wir die Zwei-Klassen-Algorithmen.

1. Ziehen Sie das Modul **Split** in den Zeichenbereich.
2. Geben Sie im Eigenschaftenbereich für den Anteil der Zeilen im ersten Ausgabedatensatz „0,8“ ein.
   ![Aufteilen von Daten in Trainings- und Testsätze][6]
3. Ziehen Sie das Modul **Two-Class Boosted Decision Tree** in den Zeichenbereich.
4. Ziehen Sie das Modul **Train Model** in den Zeichenbereich, und machen Sie Eingaben, indem Sie es mit den Modulen **Two-Class Boosted Decision Tree** (ML-Algorithmus) und **Split Data** (zum Trainieren des Algorithmus) verbinden. 
     ![Verbinden des „Modell trainieren“-Moduls][7]
5. Klicken Sie im Eigenschaftenbereich auf **Spaltenauswahl starten** . Wählen Sie die Spalte **BikeBuyer** als die vorherzusagende Spalte aus.
   ![Auswählen der vorherzusagenden Spalte][8]

## <a name="4-score-the-model"></a>4. Bewertung des Modells
Wir testen nun die Leistung des Modells mithilfe von Testdaten. Wir vergleichen zwei Algorithmen, um zu ermitteln, welcher Algorithmus die bessere Leistung erzielt.

1. Ziehen Sie das Modul **Score Model** in den Zeichenbereich, und verbinden Sie es mit den Modulen **Train Model** und **Split Data**.
   ![Bewerten des Modells][9]
2. Ziehen Sie das Modul **Two-Class Bayes Point Machine** in den Experimentbereich. Wir sehen uns an, wie dieser Algorithmus im Vergleich zum Modul „Two-Class Boosted Decision Tree“ abschneidet.
3. Kopieren Sie die Module „Train Model“ und „Score Model“, und fügen Sie sie im Zeichenbereich ein.
4. Ziehen Sie das Modul **Evaluate Model** in den Zeichenbereich, um die beiden Algorithmen zu vergleichen.
5. **Ausführen** aus.
   ![Ausführen des Experiments][10]
6. Klicken Sie unten im Modul „Modell evaluieren“ auf den Ausgabeport und anschließend auf „Visualisieren“.
   ![Anzeigen der Auswertungsergebnisse][11]

Folgende Metriken stehen zur Verfügung: ROC-Kurve, Precision-Recall-Diagramm und Lorenz-Kurve. Anhand dieser Metriken erkennen wir, dass mit dem ersten Modell eine bessere Leistung als mit dem zweiten Modell erzielt wird. Wenn Sie die Vorhersage des ersten Modells anzeigen möchten, klicken Sie im „Bewertungsmodell“ auf den Ausgabeport und anschließend auf „Visualisieren“.
![Anzeigen der Bewertungsergebnisse][12]

Ihrem Testdatensatz werden zwei weitere Spalten hinzugefügt.

* Bewertete Wahrscheinlichkeiten: Die Wahrscheinlichkeit, dass es sich bei einem Kunden um einen Fahrradkäufer handelt.
* Bewertete Beschriftungen: die vom Modell vorgenommene Klassifizierung – Fahrradkäufer (1) oder kein Fahrradkäufer (0). Der Wahrscheinlichkeitsschwellenwert für die Beschriftung ist auf 50 Prozent festgelegt, kann aber angepasst werden.

Durch einen Vergleich der Spalte „BikeBuyer“ (tatsächliche Werte) mit „Bewertete Beschriftungen“ (Vorhersage) können Sie die Leistung des Modells ermitteln. Als Nächstes können Sie mit diesem Modell Vorhersagen für neue Kunden treffen und das Modell als Webdienst veröffentlichen oder Ergebnisse zurück in SQL Data Warehouse schreiben.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Erstellen von Machine Learning-Vorhersagemodellen finden Sie unter [Einführung in Machine Learning in der Cloud][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
