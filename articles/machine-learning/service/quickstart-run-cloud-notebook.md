---
title: 'Schnellstart: Ausführen eines Notebooks in der Cloud'
titleSuffix: Azure Machine Learning service
description: Erste Schritte mit dem Azure Machine Learning Service. Verwenden Sie einen verwalteten Notebook-Server in der Cloud, um Ihren Arbeitsbereich auszuprobieren.  Ihr Arbeitsbereich bildet die Grundlage in der Cloud zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3e360b019a0c275c5ce0f9986fabd5dfc847f130
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015282"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Schnellstart: Verwenden eines cloudbasierten Notebook-Servers für die ersten Schritte mit Azure Machine Learning

Es ist keine Installation erforderlich.  Beginnen Sie mithilfe eines verwalteten Notebookservers in der Cloud mit Azure Machine Learning Service. Wenn Sie stattdessen das SDK in Ihrer eigenen Python-Umgebung installieren möchten, finden Sie weitere Informationen unter [Schnellstart: Verwenden Ihres eigenen Notebook-Servers für die ersten Schritte mit Azure Machine Learning](quickstart-run-local-notebook.md).

In diesem Schnellstart erfahren Sie, wie Sie den [Arbeitsbereich für Azure Machine Learning Service](concept-azure-machine-learning-architecture.md) zum Nachverfolgen Ihrer Experimente mit maschinellem Lernen verwenden können.  Sie erstellen eine [Notebook-VM (Vorschauversion)](how-to-configure-environment.md#notebookvm), eine sichere, cloudbasierte Azure-Arbeitsstation, die einen Jupyter Notebook-Server, JupyterLab und eine vollständig vorbereitete Umgebung für maschinelles Lernen bereitstellt. Anschließend führen Sie ein Python-Notebook auf dieser VM aus, das Werte im Arbeitsbereich protokolliert.

Diese Schnellstartanleitung umfasst folgende Aktionen:

* Erstellen eines Arbeitsbereichs
* Erstellen Sie in Ihrem Arbeitsbereich eine Notebook-VM.
* Starten der Jupyter-Weboberfläche
* Öffnen eines Notebooks, das Code zum Schätzen des Pi-Werts enthält und Fehler bei jeder Iteration protokolliert.
* Ausführen des Notebooks.
* Anzeigen der protokollierten Fehlerwerte in Ihrem Arbeitsbereich Dieses Beispiel zeigt, wie der Arbeitsbereich Sie bei der Nachverfolgung der in einem Skript generierten Informationen unterstützt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version des Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Wenn Sie über einen Azure Machine Learning Service-Arbeitsbereich verfügen, fahren Sie mit dem [nächsten Abschnitt](#create-notebook) fort. Andernfalls erstellen Sie jetzt einen Arbeitsbereich.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Erstellen einer Notebook-VM

 Erstellen Sie in Ihrem Arbeitsbereich eine Cloudressource, um erste Schritte mit Jupyter-Notebooks auszuführen. Diese Ressource bietet Ihnen eine cloudbasierte Plattform, die mit allen Funktionen vorkonfiguriert ist, die Sie für die Ausführung von Azure Machine Learning Service benötigen.

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  Wenn Sie sich nicht sicher sind, wie Sie Ihren Arbeitsbereich im Portal finden können, lesen Sie [Suchen nach Ihren Arbeitsbereich](how-to-manage-workspace.md#view).

1. Wählen Sie auf der Arbeitsbereichseite im Azure-Portal links **Notebook-VMs** aus.

1. Wählen Sie **+Neu** aus, um eine Notebook-VM zu erstellen.

     ![Auswählen der neuen VM](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Geben Sie einen Namen für Ihre VM an. Klicken Sie anschließend auf **Erstellen**.

    > [!NOTE]
    > Der Notebook-VM-Name muss zwischen 2 und 16 Zeichen lang sein. Gültige Zeichen sind Buchstaben, Ziffern und Bindestriche.  Der Name muss darüber hinaus im Azure-Abonnement eindeutig sein.

    ![Erstellen eines neuen virtuellen Computers](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Warten Sie ungefähr vier bis fünf Minuten, bis sich der Status in **Wird ausgeführt** ändert.


## <a name="launch-jupyter-web-interface"></a>Starten der Jupyter-Webbenutzeroberfläche

Nachdem Ihre VM ausgeführt wird, verwenden Sie den Abschnitt **Notebook-VMs**, um die Jupyter-Webbenutzeroberfläche zu starten.

1. Wählen Sie **Jupyter** in der Spalte **URI** für Ihre VM aus.  

    ![Starten des Jupyter Notebook-Servers](./media/quickstart-run-cloud-notebook/start-server.png)

    Der Link startet Ihren Notebook-Server und öffnet die Jupyter Notebook-Webseite im Browser auf einer neuen Registerkarte.  Dieser Link funktioniert nur für die Person, die die VM erstellt.

1. Auf der Jupyter Notebook-Webseite ist der oberste Ordnername Ihr Benutzername.  Wählen Sie diesen Ordner aus.

1. Der Beispielordnername enthält eine Versionsnummer, z. B. **samples-1.0.33.1**.  Wählen Sie den Beispielordner aus.

1. Wählen Sie das Notebook **Schnellstart** aus.

## <a name="run-the-notebook"></a>Ausführen des Notebooks

Führen Sie ein Notebook aus, das den Wert von Pi schätzt und den Fehler in Ihrem Arbeitsbereich protokolliert.

1. Wählen Sie **01.run-experiment.ipynb**, um das Notebook zu öffnen.

1. Klicken Sie in der ersten Codezelle ein, und wählen Sie **Run** (Ausführen) aus.

    > [!NOTE]
    > Codezellen sind eckige Klammern vorangestellt. Wenn die eckigen Klammern leer sind ( __[  ]__ ), wurde der Code nicht ausgeführt. Während der Code ausgeführt wird, sehen Sie ein Sternchen ( __[*]__ ). Nachdem der Code abgeschlossen wurde, wird eine Zahl **[1]** angezeigt.  Die Zahl informiert Sie über die Reihenfolge, in der die Zellen ausgeführt wurden.
    >
    > Verwenden Sie **UMSCHALT+EINGABETASTE** als Tastenkombination zum Ausführen einer Zelle.

    ![Ausführen der ersten Codezelle](media/quickstart-run-cloud-notebook/cell1.png)

1. Führen Sie die zweite Codezelle aus. Wenn Anweisungen zur Authentifizierung angezeigt werden, kopieren Sie den Code, und folgen Sie dem Link, um sich anzumelden. Nachdem Sie sich anmeldet haben, speichert Ihr Browser diese Einstellung.  

    ![Authentifizieren](media/quickstart-run-cloud-notebook/authenticate.png)

1. Wenn Sie fertig sind, wird die Zellennummer __[2]__ angezeigt.  Wenn Sie sich anmelden mussten, wird eine Statusmeldung zur erfolgreichen Authentifizierung angezeigt.   Wenn Sie sich nicht anmelden mussten, wird keine Ausgabe für diese Zelle angezeigt. Es wird nur die Zellennummer angezeigt, um anzugeben, dass die Zelle erfolgreich ausgeführt wurde.

    ![Erfolgsmeldung](media/quickstart-run-cloud-notebook/success.png)

1. Führen Sie die restlichen Codezellen aus.  Nach der Ausführung jeder Zelle wird ihre Zellennummer angezeigt. Nur die letzte Zelle zeigt eine andere Ausgabe an.  

    In der größten Codezelle sehen Sie an mehreren Stellen `run.log`. Jedes `run.log` fügt Ihrem Arbeitsbereich seinen Wert hinzu.

## <a name="view-logged-values"></a>Anzeigen protokollierter Werte

1. Die Ausgabe der Zelle `run` enthält einen Link zum Azure-Portal, in dem Sie die Experimentergebnisse in Ihrem Arbeitsbereich anzeigen können.

    ![Anzeigen von Experimenten](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klicken Sie auf den **Link zum Azure-Portal**, um Informationen zur Ausführung in Ihrem Arbeitsbereich anzuzeigen.  Über diesen Link wird Ihr Arbeitsbereich im Azure-Portal geöffnet.

1. Die angezeigten Plots protokollierter Werte wurden automatisch im Arbeitsbereich erstellt. Wenn Sie mehrere Werte mit dem gleichen Namensparameter protokollieren, wird für Sie automatisch ein Plot generiert.

   ![Anzeigen des Verlaufs](./media/quickstart-run-cloud-notebook/web-results.png)

Da der Code für die Pi-Annäherung willkürliche Werte verwendet, enthalten Ihre Plots andere Werte.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="stop-the-notebook-vm"></a>Beenden der Notebook-VM

Beenden Sie die Notebook-VM, wenn Sie sie nicht mehr verwenden, um Kosten zu sparen.  

1. Wählen Sie in Ihrem Arbeitsbereich **Notebook-VMs** aus.

   ![Beenden des VM-Servers](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Wählen Sie den virtuellen Computer in der Liste aus.

1. Wählen Sie **Stop** (Beenden) aus.

1. Wenn Sie bereit sind, den Server erneut zu verwenden, wählen Sie **Starten** aus.

### <a name="delete-everything"></a>Alles löschen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstart haben Sie die folgenden Aufgaben erledigt:

* Erstellen eines Arbeitsbereichs
* Erstellen einer Notebook-VM
* Starten der Jupyter-Weboberfläche
* Öffnen eines Notebooks, das Code zum Schätzen des Pi-Werts enthält und Fehler bei jeder Iteration protokolliert.
* Ausführen des Notebooks.
* Anzeigen der protokollierten Fehlerwerte in Ihrem Arbeitsbereich  Dieses Beispiel zeigt, wie der Arbeitsbereich Sie bei der Nachverfolgung der in einem Skript generierten Informationen unterstützt. 

Durchsuchen Sie auf der Jupyter Notebook-Webseite andere Notebooks im Beispielordner, um mehr über Azure Machine Learning Service zu erfahren.

Führen Sie Machine Learning-Tutorials zum Trainieren und Bereitstellen eines Modells aus, um sich ausführlicher mit dem Workflow zu beschäftigen:  

> [!div class="nextstepaction"]
> [Tutorial: Trainieren eines Bildklassifizierungsmodells](tutorial-train-models-with-aml.md)
