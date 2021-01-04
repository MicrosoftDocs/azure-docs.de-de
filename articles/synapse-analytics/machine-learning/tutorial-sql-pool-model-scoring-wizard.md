---
title: 'Tutorial: Assistent für die Bewertung von Machine Learning-Modellen für dedizierte SQL-Pools'
description: Tutorial zur Vorgehensweise bei der Verwendung des Assistenten für die Bewertung von Machine Learning-Modellen zur Anreicherung von Daten in dedizierten SQL-Pools
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 605a5f2f74ca6fb46d851c41f60001c48a95be95
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450887"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Tutorial: Assistent für die Bewertung von Machine Learning-Modellen (Vorschauversion) für dedizierte SQL-Pools

Hier erfahren Sie, wie Sie Ihre Daten in dedizierten SQL-Pools mit Predictive Machine Learning-Modellen problemlos erweitern können.  Die Modelle, die Ihre Datenanalysten erstellen, stehen Ihren Datenexperten jetzt bequem für Predictive Analytics zur Verfügung. Ein Datenexperte in Synapse kann einfach ein Modell aus der Azure Machine Learning-Modellregistrierung für die Bereitstellung in Synapse SQL-Pools auswählen und Vorhersagen starten, um die Daten anzureichern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Training eines Predictive Machine Learning-Modells und Registrieren des Modells in der Azure Machine Learning-Modellregistrierung
> - Verwenden des Assistenten für die SQL-Bewertung zum Starten von Vorhersagen im dedizierten SQL-Pool

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Als Standardspeicher konfigurierter [Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem ADLS Gen2-Speicherkonto. Sie müssen **Mitwirkender an Storage-Blobdaten** des ADLS Gen2-Dateisystems sein, mit dem Sie arbeiten.
- Dedizierter SQL-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines dedizierten SQL-Pools](../quickstart-create-sql-pool-studio.md).
- Verknüpfter Azure Machine Learning-Dienst in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines verknüpften Azure Machine Learning-Diensts in Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Training eines Modells in Azure Machine Learning

Vergewissern Sie sich zunächst, dass Ihre Version von **sklearn** 0.20.3 ist.

Überprüfen Sie vor dem Ausführen aller Zellen im Notebook, ob die Compute-Instanz ausgeführt wird.

![Überprüfen von AML-Compute](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Navigieren Sie zu Ihrem Azure Machine Learning-Arbeitsbereich.

1. Laden Sie [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301) (Vorhersage von Taxi-Trinkgeldern in NYC) herunter.

1. Starten Sie den Azure Machine Learning-Arbeitsbereich in [Azure Machine Learning Studio](https://ml.azure.com).

1. Wechseln Sie zu **Notebooks**, klicken Sie auf **Dateien hochladen**, wählen Sie die Datei „Predict NYC Taxi Tips.ipynb“ aus, die Sie heruntergeladen haben, und laden Sie die Datei hoch.
   ![Hochladen der Datei](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Nachdem das Notebook hochgeladen und geöffnet wurde, klicken Sie auf **Alle Zellen ausführen**.

   Eine der Zellen schlägt möglicherweise fehl, und Sie werden aufgefordert, sich bei Azure zu authentifizieren. Halten Sie Ausschau danach in den Zellenausgaben, und authentifizieren Sie sich in Ihrem Browser, indem Sie dem Link folgen und den Code eingeben. Führen Sie dann das Notebook erneut aus.

1. Es erfolgt ein Training des ONNX-Modells durch das Notebook, und dieses registriert es bei MLFlow. Wechseln Sie zu **Modelle**, um zu überprüfen, ob das neue Modell ordnungsgemäß registriert wurde.
   ![Modell in Registrierung](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Wenn Sie das Notebook ausführen, werden die Testdaten auch in eine CSV-Datei exportiert. Laden Sie die CSV-Datei auf Ihr lokales System herunter. Später importieren Sie die CSV-Datei in den dedizierten SQL-Pool und verwenden die Daten zum Testen des Modells.

   Die CSV-Datei wird im selben Ordner wie Ihre Notebook-Datei erstellt. Klicken Sie im Datei-Explorer auf „Aktualisieren“, wenn sie nicht sofort angezeigt wird.

   ![CSV-Datei](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Starten von Vorhersagen mit dem Assistenten für SQL-Bewertungen

1. Öffnen Sie den Synapse-Arbeitsbereich mit Synapse Studio.

1. Navigieren Sie zu **Daten** -> **Verknüpft** -> **Speicherkonten**. Laden Sie `test_data.csv` in das Standardspeicherkonto hoch.

   ![Hochladen von Daten](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Wechseln Sie zu **Entwickeln** -> **SQL-Skripts**. Erstellen Sie ein neues SQL-Skript, um `test_data.csv` in Ihren dedizierten SQL-Pool zu laden.

   > [!NOTE]
   > Aktualisieren Sie die Datei-URL in diesem Skript, bevor Sie es ausführen.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Laden von Daten in einen dedizierten SQL-Pool](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Wechseln Sie zu **Daten** -> **Arbeitsbereich**. Öffnen Sie den Assistenten für die SQL-Bewertung, indem Sie mit der rechten Maustaste auf die Tabelle der dedizierten SQL-Pools klicken. Wählen Sie **Machine Learning** -> **Enrich with existing model** (Mit vorhandenem Modell anreichern) aus.

   > [!NOTE]
   > Die Machine Learning-Option wird nur angezeigt, wenn Sie einen verknüpften Dienst für Azure Machine Learning erstellt haben (siehe **Voraussetzungen** zu Beginn dieses Tutorials).

   ![Machine Learning-Option](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Wählen Sie einen verknüpften Azure Machine Learning-Arbeitsbereich im Dropdownfeld aus. Dadurch wird eine Liste von Machine Learning-Modellen aus der Modellregistrierung des ausgewählten Azure Machine Learning-Arbeitsbereichs geladen. Zurzeit werden nur ONNX-Modelle unterstützt, weshalb nur ONNX-Modelle angezeigt werden.

1. Wählen Sie das gerade einem Training unterzogene Modell aus, und klicken Sie dann auf **Weiter**.

   ![Auswählen eines Azure Machine Learning-Modells](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Ordnen Sie als Nächstes die Tabellenspalten den Modelleingaben zu, und geben Sie die Modellausgaben an. Wenn das Modell im MLFlow-Format gespeichert wurde und die Modellsignatur aufgefüllt ist, wird die Zuordnung automatisch für Sie durchgeführt, wobei eine auf der Ähnlichkeit von Namen basierende Logik verwendet wird. Die Schnittstelle unterstützt auch eine manuelle Zuordnung.

   Klicken Sie auf **Weiter**.

   ![Zuordnung von Tabelle zu Modell](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Der generierte T-SQL-Code wird mit einer gespeicherten Prozedur umschlossen. Aus diesem Grund müssen Sie den Namen einer gespeicherten Prozedur angeben. Die Modellbinärdatei, einschließlich der Metadaten (Version, Beschreibung usw.), wird physisch aus Azure Machine Learning in eine Tabelle mit dedizierten SQL-Pools kopiert. Daher müssen Sie angeben, in welcher Tabelle das Modell gespeichert werden soll. Sie können auswählen zwischen „Vorhandene Tabelle verwenden“ und „Neue Tabelle erstellen“. Klicken Sie nach Abschluss des Vorgangs auf **Modell bereitstellen und Editor öffnen**, um das Modell bereitzustellen und ein T-SQL-Vorhersageskript zu generieren.

   ![Erstellen der Prozedur](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Nachdem das Skript generiert wurde, klicken Sie auf „Ausführen“, um die Bewertung auszuführen und Vorhersagen zu erhalten.

   ![Ausführen von Vorhersagen](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen eines neuen verknüpften Azure Machine Learning-Diensts in Synapse](quickstart-integrate-azure-machine-learning.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)
