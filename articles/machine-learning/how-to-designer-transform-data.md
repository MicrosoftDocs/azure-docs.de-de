---
title: Transformieren von Daten im Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Daten in Azure Machine Learning-Designer importieren und transformieren, um eigene Datasets zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: be2921f88ad2ecf88c555daf8385f1bd6733e836
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554014"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformieren von Daten in Azure Machine Learning-Designer


In diesem Artikel erfahren Sie, wie Sie Datasets in Azure Machine Learning-Designer transformieren und speichern, um Ihre eigenen Daten für Machine Learning vorzubereiten.

Verwenden Sie das Datasetbeispiel [Adult Census Income Binary Classification](./samples-designer.md) (Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene), um zwei Datasets vorzubereiten: ein Dataset mit Erhebungsinformationen von Erwachsenen nur aus den USA und ein weiteres Dataset mit Erhebungsinformationen von Erwachsenen außerhalb der USA.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

1. Transformieren eines Datasets, um es für das Training vorzubereiten
1. Exportieren der resultierenden Datasets in einen Datenspeicher
1. Anzeigen der Ergebnisse

Die Ausführung der in dieser Anleitung beschriebenen Schritte ist Voraussetzung für die Schritte im Artikel [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer (Vorschau)](how-to-retrain-designer.md). In diesem Artikel erfahren Sie, wie Sie mithilfe von transformierten Datasets mehrere Modelle mit Pipelineparametern trainieren.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformieren eines Datasets

In diesem Abschnitt erfahren Sie, wie Sie das Beispieldataset importieren und die Daten in US-Datasets und Nicht-US-Datasets unterteilen. Weitere Informationen zum Importieren eigener Daten in den Designer finden Sie unter [Importieren von Daten im Designer](how-to-designer-import-data.md).

### <a name="import-data"></a>Daten importieren

Führen Sie die folgenden Schritte aus, um das Beispieldataset zu importieren.

1. Melden Sie sich bei <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> an, und wählen Sie den gewünschten Arbeitsbereich aus.

1. Wechseln Sie zum Designer. Wählen Sie **Easy-to-use-prebuild modules** (Benutzerfreundliche vorgefertigte Module) aus, um eine neue Pipeline zu erstellen.

1. Wählen Sie ein Standardcomputeziel zum Ausführen der Pipeline aus.

1. Links neben der Pipelinecanvas befindet sich eine Palette mit Datasets und Modulen. Wählen Sie die Option **Datasets**. Sehen Sie sich dann den Abschnitt **Beispiele** an.

1. Ziehen Sie das Dataset **Adult Census Income Binary classification** (Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene) per Drag & Drop auf die Canvas.

1. Wählen Sie das Datasetmodul **Adult Census Income** (Einkommen von Erwachsenen) aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Details die Option **Ausgaben** aus.

1. Wählen Sie das ![Symbol „Visualisieren“ aus](media/how-to-designer-transform-data/visualize-icon.png)erforderlich.

1. Verwenden Sie das Fenster „Datenvorschau“, um das Dataset zu untersuchen. Beachten Sie besonders die Werte in der Spalte „native-country“.

### <a name="split-the-data"></a>Teilen der Daten

In diesem Abschnitt verwenden Sie das Modul [Split Data](algorithm-module-reference/split-data.md) (Daten aufteilen), um Zeilen zu identifizieren und aufzuteilen, die „United-States“ in der Spalte „native-country“ enthalten. 

1. Erweitern Sie in der Modulpalette auf der linken Seite Canvas den Abschnitt **Datentransformation**, und suchen Sie nach dem Modul **Split Data** (Daten aufteilen).

1. Ziehen Sie das Modul **Split Data** (Daten aufteilen) per Drag & Drop unter das Datasetmodul auf der Canvas.

1. Verbinden Sie das Datasetmodul mit dem Modul **Split Data** (Daten aufteilen).

1. Wählen Sie das Modul **Split Data** (Daten aufteilen) aus.

1. Legen Sie rechts neben der Canvas im Bereich mit den Moduldetails die Option **Splitter Modus** (Aufteilungsmodus) auf **Regulärer Ausdruck** fest.

1. Geben Sie den **regulären Ausdruck** ein: `\"native-country" United-States`.

    Der Modus **Regulärer Ausdruck** überprüft eine einzelne Spalte auf einen Wert. Weitere Informationen zum Modul „Split Data“ (Daten aufteilen) finden Sie auf der entsprechenden [Referenzseite für Algorithmen und Module](algorithm-module-reference/split-data.md).

Ihre Pipeline sollte wie folgt aussehen:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="Screenshot: Konfigurieren der Pipeline und des Moduls „Split Data“ (Daten aufteilen)":::


## <a name="save-the-datasets"></a>Speichern der Datasets

Nachdem die Pipeline nun so eingerichtet ist, dass die Daten aufgeteilt werden, müssen Sie angeben, wo die Datasets gespeichert werden sollen. Verwenden Sie für dieses Beispiel das Modul **Export Data** (Daten exportieren), um das Dataset in einem Datenspeicher zu speichern. Weitere Informationen zu Datenspeichern finden Sie unter [Herstellen einer Verbindung mit Azure-Speicherdiensten](how-to-access-data.md)

1. Erweitern Sie in der Modulpalette auf der linken Seite Canvas den Abschnitt **Data Input and Output** (Dateneingabe und -ausgabe), und suchen Sie nach dem Modul **Export Data** (Daten exportieren).

1. Ziehen Sie zwei Module zum Exportieren von Daten (**Export Data**) per Drag &Drop unter das Modul **Split Data** (Daten aufteilen).

1. Verbinden Sie jeden Ausgabeport des Moduls **Split Data** (Daten aufteilen) mit jeweils einem **Export Data**-Modul (Daten exportieren).

    Ihre Pipeline sollte in etwa wie folgt aussehen:

    ![Screenshot: Verbinden der Module zum Exportieren von Daten](media/how-to-designer-transform-data/export-data-pipeline.png)erforderlich.

1. Wählen Sie das Modul **Export Data** (Daten exportieren) aus, das mit dem Port ganz *links* des Moduls zum Aufteilen von Dateien (**Split Data**) verbunden ist.

    Die Reihenfolge der Ausgabeports ist für das Modul **Split Data** (Daten aufteilen) von Bedeutung. Der erste Ausgabeport enthält die Zeilen, in denen der reguläre Ausdruck „true“ ist. In diesem Fall enthält der erste Port Zeilen für ein US-bezogenes Einkommen, und der zweite Port enthält Zeilen für Nicht-US-bezogenes Einkommen.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails die folgenden Optionen aus:
    
    **Datenspeichertyp**: Azure Blob Storage

    **Datenspeicher**: Wählen Sie einen vorhandenen Datenspeicher aus, oder klicken Sie auf „Neuer Datenspeicher“, um einen neuen Datenspeicher zu erstellen.

    **Pfad**: `/data/us-income`

    **Dateiformat**: CSV

    > [!NOTE]
    > In diesem Artikel wird davon ausgegangen, dass Sie Zugriff auf einen Datenspeicher haben, der für den aktuellen Azure Machine Learning-Arbeitsbereich registriert ist. Anweisungen zum Einrichten eines Datenspeichers finden Sie unter [Herstellen einer Verbindung mit Azure-Speicherdiensten](how-to-connect-data-ui.md#create-datastores).

    Wenn Sie über keinen Datenspeicher verfügen, können Sie nun einen erstellen. In diesem Artikel werden die Datasets beispielsweise im standardmäßigen BLOB-Speicherkonto gespeichert, das dem Arbeitsbereich zugeordnet ist. Die Datasets werden im `azureml`-Container in einem neuen Ordner mit dem Namen `data` gespeichert.

1.  Wählen Sie das Modul **Export Data** (Daten exportieren) aus, das mit dem Port ganz *rechts* des Moduls zum Aufteilen von Dateien (**Split Data**) verbunden ist.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails die folgenden Optionen aus:
    
    **Datenspeichertyp**: Azure Blob Storage

    **Datenspeicher**: Wählen Sie den gleichen Datenspeicher wie oben aus.

    **Pfad**: `/data/non-us-income`

    **Dateiformat**: CSV

1. Überprüfen Sie, ob das Modul **Export Data** (Daten exportieren), das mit dem linken Port des Moduls zum **Aufteilen von Dateien** (Split Data) verbunden ist, den **Pfad** `/data/us-income` aufweist.

1. Überprüfen Sie, ob das Modul **Export Data** (Daten exportieren), das mit dem rechten Port verbunden ist, den **Pfad** `/data/non-us-income` aufweist.

    Die Pipeline und die Einstellungen sollten wie folgt aussehen:
    
    ![Screenshot: Konfigurieren der Module zum Exportieren von Daten](media/how-to-designer-transform-data/us-income-export-data.png)erforderlich.

### <a name="submit-the-run"></a>Übermitteln der Ausführung

Nachdem die Pipeline nun so eingerichtet ist, dass die Daten aufgeteilt und exportiert werden, übermitteln Sie die Pipelineausführung.

1. Wählen Sie im oberen Bereich der Canvas die Option **Senden** aus.

1. Wählen Sie im Dialogfeld **Pipelineausführung einrichten** die Option **Neu erstellen** aus, um ein Experiment zu erstellen.

    Mit Experimenten werden zugehörige Pipelineausführungen logisch gruppiert. Wenn Sie diese Pipeline in Zukunft ausführen, sollten Sie für die Protokollierung und Nachverfolgung dasselbe Experiment verwenden.

1. Geben Sie einen beschreibenden Namen für das Experiment wie „split-census-data“ an.

1. Klicken Sie auf **Submit** (Senden).

## <a name="view-results"></a>Anzeigen der Ergebnisse

Nach der Pipelineausführung können Sie die Ergebnisse anzeigen, indem Sie im Azure-Portal zu Ihrem BLOB-Speicher navigieren. Anhand der Zwischenergebnisse des Moduls **Split Data** (Dateien aufteilen) können Sie zudem überprüfen, ob Ihre Daten korrekt aufgeteilt wurden.

1. Wählen Sie das Modul **Split Data** (Daten aufteilen) aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails die Option **Ausgaben und Protokolle** aus. 

1. Wählen Sie das Symbol zum ![Visualisieren](media/how-to-designer-transform-data/visualize-icon.png) neben **Results dataset1** (Ergebnisse von Dataset 1) aus. 

1. Vergewissern Sie sich, dass die Spalte „native-country“ nur den Wert „United-States“ enthält.

1. Wählen Sie das Symbol zum ![Visualisieren](media/how-to-designer-transform-data/visualize-icon.png) neben **Results dataset2** (Ergebnisse von Dataset 2) aus. 

1. Vergewissern Sie sich, dass die Spalte „native-country“ nicht den Wert „United-States“ enthält.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie mit Teil 2 der Anleitung unter [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer](how-to-retrain-designer.md) weitermachen möchten.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie ein Dataset transformieren und in einem registrierten Datenspeicher speichern.

Machen Sie mit dem nächsten Teil der Anleitung unter [Erneutes Trainieren von Modellen mit Azure Machine Learning-Designer](how-to-retrain-designer.md) weiter, um die transformierten Datasets und Pipelineparameter zum Trainieren von Machine Learning-Modellen zu verwenden.