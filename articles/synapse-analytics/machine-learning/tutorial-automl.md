---
title: 'Tutorial: Training von Machine Learning-Modellen mit automatisiertem ML'
description: Tutorial zum Trainieren eines Machine Learning-Modells in Azure Synapse mit automatisiertem ML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463938"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Tutorial: Training für Machine Learning-Modelle in Azure Synapse mit automatisiertem ML (Vorschauversion) ohne Code

Es wird beschrieben, wie Sie Ihre Daten in Spark-Tabellen leicht mit neuen Machine Learning-Modellen anreichern können, die Sie mit [automatisiertem maschinellem Lernen in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) trainieren.  Ein Benutzer in Synapse kann einfach eine Spark-Tabelle im Azure Synapse-Arbeitsbereich auswählen, um sie als Trainingsdataset zum Erstellen von Machine Learning-Modellen in einer Umgebung ohne Code zu verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Trainieren von Machine Learning-Modellen in einer Umgebung ohne Code in Azure Synapse Studio, in der automatisiertes maschinelles Lernen in Azure ML verwendet wird. Der Typ des trainierten Modells hängt von dem Problem ab, das Sie lösen möchten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Als Standardspeicher konfigurierter [Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem ADLS Gen2-Speicherkonto. Sie müssen **Mitwirkender an Storage-Blobdaten** des ADLS Gen2-Dateisystems sein, mit dem Sie arbeiten.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Verknüpfter Azure Machine Learning-Dienst in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines verknüpften Azure Machine Learning-Diensts in Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Erstellen einer Spark-Tabelle für das Trainingsdataset

Für dieses Tutorial benötigen Sie eine Spark-Tabelle. Mit dem folgenden Notebook wird eine Spark-Tabelle erstellt.

1. Laden Sie das Notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229) herunter.

1. Importieren Sie das Notebook in Azure Synapse Studio.
![Importieren des Notebooks](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Wählen Sie den gewünschten Spark-Pool aus, und klicken Sie auf `Run all`. Wenn Sie dieses Notebook ausführen, werden Taxidaten aus New York aus dem geöffneten Dataset abgerufen und in Ihrer Spark-Standarddatenbank gespeichert.
![Run all (Alle ausführen)](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Nachdem die Ausführung des Notebooks abgeschlossen ist, wird in der Spark-Standarddatenbank eine neue Spark-Tabelle erstellt. Navigieren Sie zum Datenhub, und suchen Sie nach der Tabelle mit dem Namen `nyc_taxi`.
![Spark-Tabelle](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Starten des Assistenten für automatisiertes ML zum Trainieren eines Modells

Klicken Sie mit der rechten Maustaste auf die im vorherigen Schritt erstellte Spark-Tabelle. Wählen Sie „Machine Learning“ -> „Mit neuem Modell anreichern“ aus, um den Assistenten zu öffnen.
![Starten des Assistenten für automatisiertes ML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Ein Konfigurationsbereich wird angezeigt, und Sie werden aufgefordert, Konfigurationsdetails für die Erstellung eines Experiments für automatisiertes ML anzugeben, das in Azure Machine Learning ausgeführt wird. Bei dieser Ausführung werden mehrere Modelle trainiert, und das beste Modell einer erfolgreichen Ausführung wird in der Azure ML-Modellregistrierung registriert:

![Konfigurieren der Ausführung: Schritt 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure ML-Arbeitsbereich**: Für die Erstellung einer Experimentausführung für automatisiertes ML wird ein Azure ML-Arbeitsbereich benötigt. Darüber hinaus müssen Sie Ihren Azure Synapse-Arbeitsbereich mit dem Azure ML-Arbeitsbereich verknüpfen, indem Sie einen [verknüpften Dienst](quickstart-integrate-azure-machine-learning.md) verwenden. Wenn alle Voraussetzungen erfüllt sind, können Sie den Azure ML-Arbeitsbereich angeben, den Sie für diese Ausführung des automatisierten maschinellen Lernens verwenden möchten.

- **Experimentname**: Geben Sie den Namen des Experiments an. Beim Übermitteln einer Ausführung des automatisierten maschinellen Lernens müssen Sie einen Experimentnamen angeben. Die Informationen für die Ausführung werden unter diesem Experiment im Azure ML-Arbeitsbereich gespeichert. Hierdurch wird standardmäßig ein neues Experiment erstellt und ein vorgeschlagener Name generiert. Sie können aber auch den Namen eines vorhandenen Experiments angeben.

- **Bestes Modell**: Geben Sie den Namen des besten Modells aus der Ausführung des automatisierten maschinellen Lernens an. Dem besten Modell wird dieser Name zugewiesen und nach dieser Ausführung automatisch in der Azure ML-Modellregistrierung gespeichert. Bei einer Ausführung des automatisierten maschinellen Lernens werden viele Machine Learning-Modelle erstellt. Basierend auf der primären Metrik, die Sie in einem späteren Schritt auswählen, können diese Modelle verglichen werden, und es kann eine Auswahl des besten Modells erfolgen.

- **Zielspalte**: Für die Vorhersage dieses Werts wird das Modell trainiert. Wählen Sie die Spalte aus, die Sie vorhersagen möchten.

- **Spark-Pool**: Der Spark-Pool, den Sie für die Experimentausführung des automatisierten maschinellen Lernens verwenden möchten. Die Berechnungen werden für den von Ihnen angegebenen Pool ausgeführt.

- **Details zur Spark-Konfiguration**: Zusätzlich zum Spark-Pool haben Sie auch die Möglichkeit, Details zur Sitzungskonfiguration anzugeben.

In diesem Tutorial wählen wir die numerische Spalte `fareAmount` als Zielspalte aus.

Klicken Sie auf „Weiter“.

## <a name="choose-task-type"></a>Auswählen des Aufgabentyps

Wählen Sie den Typ des Machine Learning-Modells für das Experiment basierend auf der zu beantwortenden Frage aus. Da wir `fareAmount` als Zielspalte ausgewählt haben und es sich um einen numerischen Wert handelt, wählen wir *Regression* aus.

Klicken Sie auf „Weiter“, um zusätzliche Einstellungen zu konfigurieren.

![Auswahl des Aufgabentyps](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Zusätzliche Konfigurationen

Bei Auswahl des Typs *Klassifizierung* oder *Regression* lauten die zusätzlichen Konfigurationen wie folgt:

- **Primary metric** (Primäre Metrik): Die Metrik, mit der gemessen wird, wie gut die Leistung des Modells ist. Anhand dieser Metrik werden verschiedene Modelle verglichen, die bei der Ausführung des automatisierten maschinellen Lernens erstellt wurden, um zu ermitteln, welches Modell die beste Leistung aufweist.

- **Training job time (hours)** Trainingsauftragszeit (Stunden): Der maximale Zeitraum (in Stunden), in dem ein Experiment ausgeführt und das Training von Modellen durchgeführt werden kann. Beachten Sie, dass Sie auch Werte unter 1 angeben können. Beispiel: `0.5`.

- **Max concurrent iterations** (Maximale Anzahl gleichzeitiger Iterationen): Steht für die maximale Anzahl von Iterationen, die parallel ausgeführt werden.

- **ONNX model compatibility** (Kompatibilität des ONNX-Modells): Wenn diese Option aktiviert ist, werden die per automatisiertem ML trainierten Modelle in das ONNX-Format konvertiert. Dies ist vor allem relevant, wenn Sie das Modell für die Bewertung in Azure Synapse SQL-Pools nutzen möchten.

Diese Einstellungen verfügen alle über einen Standardwert, den Sie anpassen können.
![Zusätzliche Konfigurationen](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Beachten Sie, dass weitere Konfigurationen erforderlich sind, wenn Sie die Option für „Zeitreihenvorhersagen“ auswählen. Für Vorhersagen wird darüber hinaus die ONNX-Modellkompatibilität nicht unterstützt.

Nachdem alle erforderlichen Konfigurationen abgeschlossen sind, können Sie die Ausführung des automatisierten maschinellen Lernens starten.

Es gibt zwei Möglichkeiten, eine Ausführung des automatisierten maschinellen Lernens in Azure Synapse zu starten. Eine Vorgehensweise ohne Code ist die direkte Auswahl von **Create run** (Ausführung erstellen). Falls Sie den Ansatz mit Code bevorzugen, können Sie die Option **Open in notebook** (In Notebook öffnen) auswählen. Hiermit können Sie den Code anzeigen, mit dem die Ausführung erstellt wird, und das Notebook dann ausführen.

### <a name="create-run-directly"></a>Direktes Erstellen der Ausführung

Klicken Sie auf „Start Run“ (Ausführung starten), um die Ausführung des automatisierten maschinellen Lernens direkt zu starten. Es wird eine Benachrichtigung mit dem Hinweis angezeigt, dass die Ausführung des automatisierten maschinellen Lernens gestartet wird.

Nachdem die Ausführung des automatisierten maschinellen Lernens erfolgreich gestartet wurde, wird eine weitere entsprechende Benachrichtigung angezeigt. Sie können auch auf die Benachrichtigungsschaltfläche klicken, um den Status der Übermittlung zu überprüfen.
Klicken Sie in der Benachrichtigung zur erfolgreichen Ausführung auf den Link, um die Azure ML-Informationen anzuzeigen.
![Benachrichtigung zum erfolgreichen Start](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Erstellen einer Ausführung per Notebook

Wählen Sie *Open In Notebook* (In Notebook öffnen) aus, um ein Notebook zu generieren. Klicken Sie auf *Run all* (Alle ausführen), um das Notebook auszuführen.
Dies ermöglicht es Ihnen auch, Ihrer Ausführung des automatisierten maschinellen Lernens zusätzliche Einstellungen hinzuzufügen.

![Öffnen des Notebooks](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Nachdem die Ausführung aus dem Notebook erfolgreich übermittelt wurde, ist in der Ausgabe des Notebooks ein Link zur Experimentausführung im Azure ML-Arbeitsbereich angegeben. Sie können auf den Link klicken, um Ihre Ausführung des automatisierten maschinellen Lernens in Azure ML zu überwachen.
![Notebook: Alle ausführen](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png))

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Assistent für die Modellbewertung in dedizierten SQL-Pools von Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Schnellstart: Erstellen eines neuen verknüpften Azure Machine Learning-Diensts in Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)