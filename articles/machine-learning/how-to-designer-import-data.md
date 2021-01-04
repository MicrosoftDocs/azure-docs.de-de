---
title: Importieren von Daten in den Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Azure Machine Learning-Datasets und dem Modul zum Importieren von Daten in Azure Machine Learning-Designer entsprechende Daten importieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 5569b625b8f0c4ba890c0cd5b1700ca6fe83d968
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591987"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importieren von Daten in den Azure Machine Learning-Designer

In diesem Artikel erfahren Sie, wie Sie Ihre eigenen Daten in den Designer importieren, um benutzerdefinierte Lösungen zu erstellen. Es gibt zwei Möglichkeiten, wie Sie Daten in den Designer importieren können: 

* **Azure Machine Learning-Datasets** – Registrieren Sie [Datasets](concept-data.md#datasets) in Azure Machine Learning, um erweiterte Features zu aktivieren, die Sie bei der Verwaltung Ihrer Daten unterstützen.
* **Modul „Daten importieren“** – Verwenden Sie das Modul [Daten importieren](algorithm-module-reference/import-data.md), um direkt auf Daten aus Onlinedatenquellen zuzugreifen.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Verwenden von Azure Machine Learning-Datasets

Es wird empfohlen, dass Sie [Datasets](concept-data.md#datasets) verwenden, um Daten in den Designer zu importieren. Wenn Sie ein Dataset registrieren, können Sie die erweiterten Datenfeatures wie [Versionsverwaltung und Nachverfolgung](how-to-version-track-datasets.md) und [Datenüberwachung](how-to-monitor-datasets.md) in vollem Umfang nutzen.

### <a name="register-a-dataset"></a>Registrieren eines Datasets

Sie können vorhandene Datasets [programmgesteuert mit dem SDK](how-to-create-register-datasets.md#datasets-sdk) oder [visuell im Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) registrieren.

Sie können auch die Ausgabe für jedes Designermodul als Dataset registrieren.

1. Wählen Sie das Modul aus, das die zu registrierenden Daten ausgibt.

1. Wählen Sie im Bereich „Eigenschaften“ die Option **Ausgaben + Protokolle** > **Dataset registrieren** aus.

    ![Screenshot: Navigation zur Option „Dataset registrieren“](media/how-to-designer-import-data/register-dataset-designer.png)

Wenn die Modulausgabedaten in einem Tabellenformat vorliegen, müssen Sie die Ausgabe als **Dateidataset** oder **Tabellendataset** registrieren.

 - Mit **Dateidataset** wird der Ausgabeordner des Moduls als Dateidataset registriert. Der Ausgabeordner enthält eine Datendatei und Metadateien, die vom Designer intern verwendet werden. Wählen Sie diese Option aus, wenn Sie das registrierte Dataset weiterhin im Designer verwenden möchten. 

 - Mit **Tabellendataset** wird nur die Ausgabedatendatei des Moduls als Tabellendataset registriert. Dieses Format kann problemlos von anderen Tools verarbeitet werden, z. B. durch automatisiertes maschinelles Lernen oder im Python-SDK. Wählen Sie diese Option aus, wenn Sie das registrierte Dataset außerhalb des Designers verwenden möchten.  



### <a name="use-a-dataset"></a>Verwenden eines Datasets

Ihre registrierten Datasets finden Sie in der Modulpalette unter **Datasets**. Um ein Dataset zu verwenden, ziehen Sie es per Drag & Drop auf die Pipelinecanvas. Verbinden Sie dann den Ausgabeport des Datasets mit anderen Modulen auf der Canvas. 

Wenn Sie ein Dateidataset registrieren, ist der Ausgabeporttyp des Datasets **AnyDirectory**. Wenn Sie ein Tabellendataset registrieren, ist der Ausgabeporttyp des Datasets **DataFrameDirectory**. Beachten Sie, dass beim Verbinden des Ausgabeports des Datasets mit anderen Modulen im Designer der Porttyp von Datasets und Modulen ausgerichtet werden muss.

![Screenshot: Speicherort gespeicherter Datasets in der Designerpalette](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Der Designer unterstützt die [Datasetversionierung](how-to-version-track-datasets.md). Geben Sie die Datasetversion im Bereich „Eigenschaften“ des Datasetmoduls an.


## <a name="import-data-using-the-import-data-module"></a>Importieren von Daten mit dem Modul „Daten importieren“

Obwohl wir empfehlen, dass Sie Datasets zum Importieren von Daten verwenden, können Sie auch das Modul [Daten importieren](algorithm-module-reference/import-data.md) verwenden. Das Modul „Daten importieren“ überspringt die Registrierung Ihres Datasets in Azure Machine Learning und importiert Daten direkt aus einem [Datenspeicher](concept-data.md#datastores) oder einer HTTP-URL.

Ausführliche Informationen zur Verwendung des Moduls „Daten importieren“ finden Sie auf der [Referenzseite für den Datenimport](algorithm-module-reference/import-data.md).

> [!NOTE]
> Wenn das Dataset zu viele Spalten enthält, kann der folgende Fehler auftreten: „Fehler bei der Überprüfung aufgrund von Größenbeschränkung“. Um dies zu vermeiden, [registrieren Sie das Dataset in der Dataset-Schnittstelle](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Unterstützte Quellen

Dieser Abschnitt listet die vom Designer unterstützten Datenquellen auf. Die Daten kommen entweder aus einem Datenspeicher oder aus einem [Tabellendataset](how-to-create-register-datasets.md#dataset-types) in den Designer.

### <a name="datastore-sources"></a>Datenspeicherquellen
Eine Liste der unterstützten Datenspeicherquellen finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tabellendatasetquellen

Der Designer unterstützt Tabellendatasets, die aus den folgenden Quellen erstellt wurden:
 * Durch Trennzeichen getrennte Dateien
 * JSON-Dateien
 * Parquet-Dateien
 * SQL-Abfragen

## <a name="data-types"></a>Datentypen

Der Designer erkennt intern die folgenden Datentypen:

* String
* Integer
* Decimal
* Boolean
* Date

Der Designer verwendet einen internen Datentyp, um Daten zwischen Modulen zu übertragen. Mit dem Modul [Convert to Dataset](algorithm-module-reference/convert-to-dataset.md) (In Dataset konvertieren) können Sie Daten explizit in das data table-Format konvertieren. Jedes Modul, das andere Formate als das interne Format akzeptiert, konvertiert die Daten im Hintergrund vor der Übergabe an das nächste Modul.

## <a name="data-constraints"></a>Dateneinschränkungen

Die Module im Designer sind durch die Größe des Computeziels begrenzt. Für größere Datasets sollten Sie eine größere Azure Machine Learning-Computeressource verwenden. Weitere Informationen zum Machine Learning-Computing finden Sie unter [Was sind Computeziele in Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed).

## <a name="access-data-in-a-virtual-network"></a>Zugreifen auf Daten in einem virtuellen Netzwerk

Wenn sich Ihr Arbeitsbereich in einem virtuellen Netzwerk befindet, müssen Sie zusätzliche Konfigurationsschritte ausführen, um die Daten im Designer visuell darzustellen. Weitere Informationen zur Verwendung von Datenspeichern und Datasets in virtuellen Netzwerken finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Grundlagen des Designers finden Sie im [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md).
