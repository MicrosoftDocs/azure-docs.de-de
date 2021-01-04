---
title: 'Tutorial: Erste Schritte mit Jupyter Notebooks (Python)'
titleSuffix: Azure Machine Learning
description: Einrichtung für Jupyter Notebook-Tutorials. Erstellen Sie einen Arbeitsbereich, klonen Sie Notebooks in den Arbeitsbereich, und erstellen Sie eine Compute-Instanz, in der Sie die Notebooks ausführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ebde8048c26364d77d95b9c7b34f9d1d7a4a20ee
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326263"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Tutorial: Machen Sie sich mit den ersten Schritten mit Azure Machine Learning in Jupyter Notebooks vertraut.

In diesem Tutorial führen Sie die Schritte zum Einstieg in Azure Machine Learning mit Jupyter Notebooks in einer [verwalteten cloudbasierten Arbeitsstation (Compute-Instanz)](concept-compute-instance.md) aus. Dieses Tutorial ist Voraussetzung für alle weiteren Jupyter Notebook-Tutorials.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen eines [Azure Machine Learning-Arbeitsbereichs](concept-workspace.md) für weitere Jupyter Notebook-Tutorials
> * Klonen Sie das Tutorial-Notebook in Ihrem Ordner im Arbeitsbereich.
> * Erstellen einer cloudbasierten Compute-Instanz, in der das Azure Machine Learning Python SDK installiert und vorkonfiguriert ist

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Ein Azure Machine Learning-Arbeitsbereich ist eine grundlegende Cloudressource zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen. Er verknüpft Ihr Azure-Abonnement und Ihre Ressourcengruppe mit einem einfach nutzbaren Objekt im Dienst.

Springen Sie zu [Klonen eines Notebook-Ordners](#clone), wenn Sie bereits über einen Azure Machine Learning-Arbeitsbereich verfügen.  

Es gibt viele [Möglichkeiten, einen Arbeitsbereich zu erstellen](how-to-manage-workspace.md).  In diesem Tutorial erstellen Sie einen Arbeitsbereich über das Azure-Portal, einer webbasierten Konsole zum Verwalten Ihrer Azure-Ressourcen.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Notieren Sie sich Ihren *Arbeitsbereich* und Ihr *Abonnement*. Sie benötigen diese Informationen, um sicherzustellen, dass Sie Ihr Experiment an der richtigen Stelle erstellen.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Ausführen eines Notebooks in Ihrem Arbeitsbereich

Azure Machine Learning enthält einen cloudbasierten Notebook-Server in Ihrem Arbeitsbereich als vorkonfigurierte Umgebung ohne Installation. Verwenden Sie [Ihre eigene Umgebung](tutorial-1st-experiment-sdk-setup-local.md), wenn Sie Ihre Umgebung, Pakete und Abhängigkeiten lieber selbst gestalten möchten.

 Sehen Sie sich das folgende Video an, oder führen Sie die angegebenen detaillierten Schritte aus, um das Notebook für das Tutorial über Ihren Arbeitsbereich zu klonen und auszuführen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Klonen eines Notebook-Ordners

Sie führen die folgenden Schritte zum Einrichten und Ausführen des Experiments in Azure Machine Learning Studio aus. Diese konsolidierte Benutzeroberfläche umfasst Tools für maschinelles Lernen zur Durchführung von Data Science-Szenarien für Datenwissenschaftler aller Qualifikationsstufen.

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.

1. Wählen Sie Ihr Abonnement und den erstellten Arbeitsbereich aus.

1. Wählen Sie links **Notebooks** aus.

1. Wählen Sie oben die Registerkarte **Beispiele** aus.

1. Öffnen Sie den Ordner **Python**.

1. Öffnen Sie den Ordner mit Versionsnummer. Diese Zahl stellt die aktuelle Version des Python SDK dar.

1. Wählen Sie rechts vom Ordner **Tutorials** die Auslassungspunkte ( **...** ) und anschließend **Klonen** aus.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Screenshot: Klonen des Ordners „Tutorials“":::

1. Eine Liste der Ordner mit den einzelnen Benutzern wird angezeigt, die auf den Arbeitsbereich zugreifen. Wählen Sie Ihren Ordner aus, um den Ordner **Tutorials** dort zu klonen.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Öffnen des geklonten Notebooks

1. Öffnen Sie den Ordner **Tutorials**, den Sie im Abschnitt **Benutzerdateien** geschlossen haben.

    > [!IMPORTANT]
    > Im Ordner **Beispiele** können Notebooks angezeigt, aber nicht ausgeführt werden. Öffnen Sie zum Ausführen eines Notebooks die geklonte Version des Notebooks unbedingt im Abschnitt **Benutzerdateien**.
    
1. Wählen Sie die Datei **img-classification-part1-training.ipynb** im Ordner **tutorials/image-classification-mnist-data** aus.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Screenshot: Öffnen des Ordners „Tutorials“":::

1. Wählen Sie auf der oberen Leiste eine Computeinstanz aus, die zum Ausführen des Notebooks verwendet werden soll. Diese virtuellen Computer werden [mit allen Komponenten vorkonfiguriert, die Sie zum Ausführen von Azure Machine Learning benötigen](concept-compute-instance.md#contents).

1. Werden keine virtuellen Computer gefunden, wählen Sie **+ Hinzufügen** aus, um den virtuellen Computer mit der Compute-Instanz zu erstellen.

    1. Befolgen Sie diese Regeln, wenn Sie eine VM erstellen:
 
        + Ein Name ist erforderlich, und das Feld darf nicht leer sein.
        + Der Name muss unter allen vorhandenen Compute-Instanzen in der Azure-Region des Arbeitsbereichs oder der Compute-Instanz eindeutig sein (ohne Beachtung der Groß-/Kleinschreibung). Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.
        + Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern (0 bis 9) und Bindestriche (-).
        + Der Name muss zwischen 3 und 24 Zeichen lang sein.
        + Der Name muss mit einem Buchstaben beginnen (nicht mit einer Ziffer oder einem Bindestrich).
        + Wenn ein Bindestrich im Namen verwendet wird, muss darauf mindestens ein Buchstabe folgen. „Test-“, „test-0“ und „test-01“ sind beispielsweise ungültig, während „test-a0“ und „test-0a“ gültige Namen sind.

    1. Wählen Sie die VM-Größe aus den verfügbaren Optionen aus. Für die Tutorials ist die Standard-VM eine gute Wahl.

    1. Klicken Sie anschließend auf **Erstellen**. Das Einrichten Ihres virtuellen Computers dauert ungefähr fünf Minuten.

1. Wenn der virtuelle Computer verfügbar ist, wird er auf der oberen Symbolleiste angezeigt. Sie können das Notebook jetzt entweder über **Alle ausführen** auf der Symbolleiste oder unter Verwendung von **UMSCHALT+EINGABE** in den Codezellen des Notebooks ausführen.

Wenn Sie über benutzerdefinierte Widgets verfügen oder die Verwendung von Jupyter oder JupyterLab bevorzugen, wählen Sie ganz rechts das Dropdownmenü **Jupyter** aus. Wählen Sie dann **Jupyter** oder **JupyterLab** aus. Das neue Browserfenster wird geöffnet.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie eine Entwicklungsumgebung eingerichtet haben, fahren Sie mit dem Trainieren eines Modells in einer Jupyter Notebook-Instanz fort.

> [!div class="nextstepaction"]
> [Tutorial: Trainieren von Bildklassifizierungsmodellen mit MNIST-Daten und scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Falls Sie mit keinen der anderen Tutorials fortfahren möchten, beenden Sie den virtuellen Computer für den cloudbasierten Notebook-Server, wenn Sie ihn gerade nicht verwenden, um Kosten zu sparen:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
