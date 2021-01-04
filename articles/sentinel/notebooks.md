---
title: Verwenden von Notebooks mit Azure Sentinel für Security Hunting
description: In diesem Artikel wird beschrieben, wie Notebooks mit den Ermittlungsfunktionen von Azure Sentinel verwendet werden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092190"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Aufspüren von Sicherheitsrisiken mit Jupyter Notebook

Grundlage für Azure Sentinel ist der Datenspeicher. Dieser zeichnet sich durch Hochleistungsabfragen, ein dynamisches Schema und die Skalierung auf große Datenvolumen aus. Das Azure-Portal und alle Azure Sentinel-Tools greifen über eine gemeinsame API auf diesen Datenspeicher zu. Die gleiche API ist auch für externe Tools wie [Jupyter Notebooks](https://jupyter.org/) und Python verfügbar. Viele gängige Aufgaben können im Portal durchgeführt werden, Jupyter erweitert jedoch den Rahmen der Verarbeitung dieser Daten. Jupyter bietet sowohl umfassende Programmierbarkeit als auch eine große Sammlung von Bibliotheken für Machine Learning, Visualisierung und Datenanalyse. Dies macht Jupyter zu einem überzeugenden Tool für Sicherheitsuntersuchungen und Aufspüren von Sicherheitsrisiken.

![Beispielnotebook](./media/notebooks/sentinel-notebooks-map.png)

Wir haben die Jupyter-Benutzeroberfläche in das Azure-Portal integriert, sodass Sie Notebooks auf einfache Weise erstellen und mit diesen Notebooks Daten analysieren können. Mit der *Kqlmagic*-Bibliothek als verbindende Komponente können Sie Abfragen von Azure Sentinel annehmen und diese direkt in einem Notebook ausführen. Für Abfragen wird die Abfragesprache [Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html) verwendet. Verschiedene von Microsoft-Sicherheitsanalysten entwickelte Notebooks werden mit Azure Sentinel verpackt. Einige diese Notebooks wurden auf ein bestimmtes Szenario ausgelegt, und sie sind ohne weitere Änderungen sofort verwendungsfähig. Andere sollen beispielhaft Techniken und Funktionen veranschaulichen, die Sie kopieren oder für die Verwendung in Ihren eigenen Notebooks anpassen können. Andere Notebooks können auch über die Community-GitHub-Seite für Azure Sentinel importiert werden.

Diese integrierte Jupyter-Benutzererfahrung verwendet [Azure Notebooks](https://notebooks.azure.com/) zum Speichern, Freigeben und Ausführen von Notebooks. Sie können diese Notebooks auch lokal (wenn Sie über eine Python-Umgebung verfügen und Jupyter auf Ihrem Computer installiert ist) oder in anderen JupyterHub-Umgebungen wie Azure Databricks ausführen.

Notebooks verfügen über zwei Komponenten:

- Die browserbasierte Benutzeroberfläche, in der Sie Abfragen und Code eingeben und ausführen und in der die Ergebnisse der Ausführung angezeigt werden.
- Einen *Kernel*, der für das Analysieren und Ausführen des Codes selbst zuständig ist.

Der Kernel des Notebooks in Azure Sentinel wird auf einer Azure-VM ausgeführt. Wenn Ihre Notebooks komplexe Machine Learning-Modelle umfassen, stehen auch weitere Lizenzierungsoptionen zur Verfügung, um leistungsfähigere VMs nutzen zu können.

Die Azure Sentinel-Notebooks nutzen viele beliebte Python-Bibliotheken wie pandas, matplotlib, bokeh und andere. Zahlreiche weitere Python-Pakete stehen zur Auswahl, die u.a. die folgenden Bereiche abdecken:

- Visualisierungen und Grafiken
- Datenverarbeitung und -analyse
- Statistiken und numerisches Computing
- Machine Learning und Deep Learning

Im Paket [msticpy](https://github.com/Microsoft/msticpy/) haben wir zudem einige Open Source-Jupyter-Sicherheitstools veröffentlicht. Dieses Paket wird in vielen der enthaltenen Notebooks verwendet. Msticpy-Tools sollen Sie insbesondere beim Erstellen von Notebooks zum Aufspüren und Untersuchen von Sicherheitsrisiken unterstützen, und wir arbeiten aktiv an neuen Features und Verbesserungen.

Im [GitHub-Repository der Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) finden sich alle künftig von Microsoft erstellten oder von der Community beigesteuerten Azure Sentinel-Notebooks.

Sie müssen zuerst einen Azure Machine Learning-Arbeitsbereich (ML) erstellen, um Notebooks verwenden zu können.

## <a name="create-an-azure-ml-workspace"></a>Erstellen eines Azure ML-Arbeitsbereichs

1. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, und klicken Sie dort auf **Notebook starten**.

    > [!div class="mx-imgBorder"]
    > ![Starten eines Notebooks zum Starten eines Arbeitsbereichs in Azure Machine Learning](./media/notebooks/sentinel-notebooks-launch.png)

1. Klicken Sie unter **AzureML-Arbeitsbereich** auf **Neu erstellen**.

    > [!div class="mx-imgBorder"]
    > ![Erstellen des Arbeitsbereichs](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Geben Sie auf der Seite **Machine Learning** die folgenden Informationen an, und klicken Sie anschließend auf **Überprüfen + erstellen**.

    |Feld|BESCHREIBUNG|
    |--|--|
    |Subscription|Wählen Sie das gewünschte Azure-Abonnement aus.|
    |Resource group|Verwenden Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe enthält verwandte Ressourcen für eine Azure-Lösung. In diesem Beispiel wird **AzureMLRG** verwendet.|
    |Arbeitsbereichname|Geben Sie einen eindeutigen Namen ein, der Ihren Arbeitsbereich identifiziert. In diesem Beispiel wird **testworkspace1** verwendet. Namen müssen in der Ressourcengruppe eindeutig sein. Verwenden Sie einen Namen, der leicht zu merken ist und sich von den von anderen Benutzern erstellten Arbeitsbereichen unterscheidet.|
    |Region|Wählen Sie den Standort aus, der Ihren Benutzern und den Datenressourcen am nächsten ist, um Ihren Arbeitsbereich zu erstellen.|
    |Arbeitsbereichs-Edition|Wählen Sie **Basic** als Arbeitsbereichstyp für dieses Beispiel aus. Der Arbeitsbereichstyp (Basic und Enterprise) bestimmt die Features, auf die Sie Zugriff haben, und den Preis.|

    > [!div class="mx-imgBorder"]
    > ![Angeben der Arbeitsbereichsdetails](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Sehen Sie sich die Informationen an, überprüfen Sie, ob sie korrekt sind, und klicken Sie dann auf **Erstellen**, um die Bereitstellung Ihres Arbeitsbereichs zu starten.

    > [!div class="mx-imgBorder"]
    > ![Überprüfen der Arbeitsbereichsdetails](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Es kann einige Minuten dauern, bis der Arbeitsbereich in der Cloud erstellt wurde. Während dieser Zeit wird auf der Seite **Übersicht** der aktuelle Bereitstellungsstatus angezeigt.

    > [!div class="mx-imgBorder"]
    > ![Arbeitsbereichsbereitstellung](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Sobald Ihre Bereitstellung abgeschlossen ist, können Sie Notebooks in Ihrem neuen Azure Machine Learning-Arbeitsbereich starten.

> [!div class="mx-imgBorder"]
> ![Erfolgreiche Arbeitsbereichsbereitstellung](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Starten eines Notebooks mithilfe Ihres Arbeitsbereichs in Azure Machine Learning

1. Navigieren Sie vom Azure-Portal aus zu **Azure Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, wo die von Azure Sentinel bereitgestellten Notebooks angezeigt werden.

    > [!TIP]
    > Klicken Sie auf **Guides & Feedback** (Anleitungen und Feedback), um einen Bereich zu öffnen, in dem Sie zusätzliche Hilfe und Anleitungen zu Notebooks erhalten können.
    > ![Anleitung zum Anzeigen des Notebooks](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Wählen Sie einzelne Notebooks aus, um deren Beschreibungen, erforderliche Datentypen und Datenquellen anzuzeigen.

    > [!div class="mx-imgBorder"]
    > ![Details zum Anzeigen des Notebooks](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Wählen Sie das gewünschte Notebook aus, und klicken Sie anschließend auf **Notebook starten**, um das Notebook in ein neues Azure Notebooks-Projekt, das mit Ihrem Azure Sentinel-Arbeitsbereich verbunden ist, zu klonen und zu konfigurieren. Sobald der Prozess abgeschlossen ist, wird das Notebook in Azure Notebooks geöffnet, und Sie können es ausführen.

    > [!div class="mx-imgBorder"]
    > ![Auswählen des Notebooks](./media/notebooks/sentinel-azure-notebooks-select.png)

1. Wählen Sie unter „AzureML-Arbeitsbereich“ Ihren Azure ML-Arbeitsbereich aus, und klicken Sie dann auf **Starten**.

    > [!div class="mx-imgBorder"]
    > ![Notebook starten](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Wählen Sie eine Compute-Instanz aus. Wenn Sie über keine Compute-Instanz verfügen, tun Sie Folgendes:
    1. Klicken Sie auf das Pluszeichen (+), um den Assistenten **Neue Compute-Instanz** zu starten.

        > [!div class="mx-imgBorder"]
        > ![Assistent zum Starten einer Compute-Instanz](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Geben Sie auf der Seite **Neue Compute-Instanz** die erforderlichen Informationen ein, und klicken Sie dann auf **Erstellen**.

        > [!div class="mx-imgBorder"]
        > ![Erstellen der Compute-Instanz](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Sobald Ihr Notebookserver erstellt wurde, klicken Sie in jeder Zelle auf das Symbol zum Ausführen, um den Code in den Notebooks auszuführen.

    > [!div class="mx-imgBorder"]
    > ![Ausführen des Notebooks](./media/notebooks/sentinel-azure-notebooks-run.png)

Empfehlungen:

- Eine kurze Einführung zum Abfragen von Daten in Azure Sentinel finden Sie in der Anleitung [Erste Schritte mit Notebooks in Azure Machine Learning und Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb).

- Weitere Beispiel-Notebooks finden sich im GitHub-Unterordner [**Beispiel-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks). Diese Beispiel-Notebooks wurden mit Daten gespeichert, sodass die gewünschte Ausgabe leichter überprüft werden kann. Wir empfehlen für die Anzeige dieser Notebooks den [nbviewer](https://nbviewer.jupyter.org/).

- Der GitHub-Unterordner [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) enthält Notebooks, die beispielsweise Folgendes beschreiben: Festlegen der Python-Standardversion, Konfigurieren einer DSVM, Erstellen von Azure Sentinel-Lesezeichen in einem Notebook sowie andere Themen.

Die bereitgestellten Notebooks fungieren einerseits als hilfreiche Tools, Sie dienen andererseits aber auch zur Veranschaulichung mit Codebeispielen, die Sie in der Umgebung Ihrer eigenen Notebooks verwenden können.

Wir freuen uns über Feedback, egal ob es sich um Vorschläge, Empfehlungen für Funktionen, eigene Beiträge von Notebooks, Fehlerberichte oder Verbesserungen und Ergänzungen zur vorhandenen Notebooks handelt. Besuchen Sie die [Azure Sentinel-Community GitHub](https://github.com/Azure/Azure-Sentinel), um ein Problem zu melden oder eine Verzweigung anzulegen oder um einen Beitrag hochzuladen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die ersten Schritte beim Verwenden von Jupyter Notebook in Azure Sentinel erläutert. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Behalten des Überblicks über Daten bei einer Bedrohungssuche](bookmarks.md)
