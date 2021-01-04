---
title: 'Tutorial: Erstellen eines Beschriftungsprojekts für die Bildklassifizierung'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie den Prozess der Beschriftung von Bildern so verwalten können, dass sie in mehrklassigen Bildklassifizierungsmodellen verwendet werden können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 5aec3d6bb2afeadda64ac19563f8334a6aef3b77
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347505"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Tutorial: Erstellen eines Beschriftungsprojekts für mehrklassige Bildklassifizierung 


Dieses Tutorial zeigt Ihnen, wie Sie den Prozess der Beschriftung (auch als Tagging bezeichnet) von Bildern verwalten, die als Daten für die Erstellung von Machine Learning-Modellen verwendet werden sollen. Die Datenbeschriftung in Azure Machine Learning ist in der Public Preview-Phase.

Wenn Sie ein Machine Learning-Modell zur Klassifizierung von Bildern trainieren möchten, benötigen Sie Hunderte oder sogar Tausende von Bildern, die ordnungsgemäß beschriftet sind.  Azure Machine Learning hilft Ihnen, den Fortschritt Ihres privaten Teams von Fachgebietsexperten bei der Beschriftung Ihrer Daten zu steuern.
 
In diesem Tutorial verwenden Sie Bilder von Katzen und Hunden.  Da jedes Bild entweder eine Katze oder ein Hund zeigt, handelt es sich um ein *mehrklassiges* Beschriftungsprojekt. Sie lernen Folgendes:

> [!div class="checklist"]
>
> * Erstellen eines Azure-Speicherkontos und Hochladen von Bildern in das Konto
> * Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.
> * Erstellen eines mehrklassigen Bildbeschriftungsprojekts
> * Beschriften Ihrer Daten.  Dieser Vorgang kann von Ihnen oder Ihren Beschriftungserstellern durchgeführt werden.
> * Abschließen des Projekts durch Prüfen und Exportieren der Daten

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://aka.ms/AMLFree) erstellen.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Ein Azure Machine Learning-Arbeitsbereich ist eine grundlegende Cloudressource zum Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen. Er verknüpft Ihr Azure-Abonnement und Ihre Ressourcengruppe mit einem einfach nutzbaren Objekt im Dienst.

Es gibt viele [Möglichkeiten, einen Arbeitsbereich zu erstellen](how-to-manage-workspace.md). In diesem Tutorial erstellen Sie einen Arbeitsbereich über das Azure-Portal, einer webbasierten Konsole zum Verwalten Ihrer Azure-Ressourcen.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Starten eines Beschriftungsprojekts

Als Nächstes verwalten Sie das Datenbeschriftungsprojekt in Azure Machine Learning Studio. Diese aufgeräumte Oberfläche bietet Tools für maschinelles Lernen zur Durchführung von Data Science-Szenarien für Datenwissenschaftler aller Qualifikationsstufen. Das Azure Machine Learning-Studio wird in Internet Explorer-Browsern nicht unterstützt.

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an.

1. Wählen Sie Ihr Abonnement und den erstellten Arbeitsbereich aus.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Erstellen eines Datenspeichers

Azure Machine Learning-Datenspeicher werden zum Speichern von Verbindungsinformationen wie z. B. Ihrer Abonnement-ID und Tokenautorisierung verwendet. Hier verwenden Sie einen Datenspeicher, um eine Verbindung mit dem Speicherkonto herzustellen, das die Bilder für dieses Tutorial enthält.

1. Wählen Sie auf der linken Seite Ihres Arbeitsbereichs **Datenspeicher** aus.

1. Wählen Sie **+ Neuer Datenspeicher** aus.

1. Füllen Sie das Formular mit den folgenden Einstellungen aus:

    Feld|BESCHREIBUNG 
    ---|---
    Datenspeichername | Benennen Sie den Datenspeicher.  Hier verwenden wir **labeling_tutorial**.
    Datenspeichertyp | Wählen Sie den Speichertyp aus.  Hier verwenden wir **Azure Blob Storage**, den bevorzugten Speicher für Bilder.
    Kontoauswahlmethode | Wählen Sie **Manuell eingeben** aus.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Authentifizierungsart | Wählen Sie **SAS-Token** aus.
    Kontoschlüssel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Wählen Sie **Erstellen** aus, um den Datenspeicher zu erstellen.

### <a name="create-a-labeling-project"></a>Erstellen eines Beschriftungsprojekts

Da Sie nun Zugriff auf die Daten haben, die Sie beschriften lassen möchten, erstellen Sie Ihr Beschriftungsprojekt.

1. Wählen Sie oben auf der Seite **Projekte** aus.

1. Wählen Sie **+ Projekt hinzufügen** aus.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Erstellen eines Projekts":::

### <a name="project-details"></a>Projektdetails

1. Verwenden Sie die folgende Eingabe für das Formular **Projektdetails**:

    Feld|BESCHREIBUNG 
    ---|---
    Projektname | Geben Sie dem Projekt einen Namen.  Hier verwenden wir **tutorial-cats-n-dogs**.
    Beschriftungsaufgabentyp | Wählen Sie **Bildklassifizierung mit mehreren Klassen** aus.  
    
    Wählen Sie **Weiter** aus, um die Erstellung des Projekts fortzusetzen.

### <a name="select-or-create-a-dataset"></a>Auswählen oder Erstellen eines Datasets

1.   Wählen Sie im Formular **Dataset auswählen oder erstellen** die zweite Option, **Dataset erstellen**, und dann den Link **Aus Datenspeicher** aus.

1. Verwenden Sie die folgende Eingabe für das Formular **Dataset aus Datenspeicher erstellen**:

    1. Fügen Sie auf dem Formular **Grundlegende Infos** einen Namen hinzu. Hier verwenden wir **images-for-tutorial**.  Fügen Sie nach Wunsch eine Beschreibung hinzu.  Wählen Sie **Weiter** aus.
    1. Verwenden Sie im Formular **Datenspeicherauswahl** die Dropdownliste, um Ihren **zuvor erstellten Datenspeicher** auszuwählen, z. B. **tutorial_images (Azure Blob Storage)** .
    1. Wählen Sie als Nächstes weiterhin im Formular **Datenspeicherauswahl** die Option **Durchsuchen** und dann **MultiClass - DogsCats** aus.  Wählen Sie **Speichern** aus, um **/MultiClass - DogsCats** als Pfad zu verwenden.
    1. Wählen Sie **Weiter** aus, um Details zu bestätigen, und dann **Erstellen**, um das Dataset zu erstellen.
    1. Wählen Sie den Kreis neben dem Namen des Datasets in der Liste aus, z. B. **images-for-tutorial**.

1. Wählen Sie **Weiter** aus, um die Erstellung des Projekts fortzusetzen.

### <a name="incremental-refresh"></a>Inkrementelle Aktualisierung

Wenn Sie dem Dataset neue Bilder hinzufügen möchten, werden diese von der inkrementellen Aktualisierung ermittelt und Ihrem Projekt hinzugefügt.  Wenn Sie diese Funktion aktivieren, sucht das Projekt regelmäßig nach neuen Bildern.  Lassen Sie diese Funktion deaktiviert, da Sie dem Datenspeicher in diesem Tutorial keine neuen Bilder hinzufügen.

Klicken Sie auf **Weiter**, um fortzufahren.

### <a name="label-classes"></a>Beschriftungsklassen

1. Geben Sie im Formular **Beschriftungsklassen** einen Beschriftungsnamen ein, und wählen Sie dann **+Beschriftung hinzufügen** aus, um die nächste Beschriftung einzugeben.  Für dieses Projekt sind die Beschriftungen **Cat**, **Dog** und **Uncertain**.

1. Wählen Sie **Weiter** aus, nachdem Sie alle Beschriftungen hinzugefügt haben.

### <a name="labeling-instructions"></a>Beschriftungsanweisungen

1. Auf dem Formular **Beschriftungsanweisungen** können Sie einen Link zu einer Website angeben, die detaillierte Anweisungen für Ihre Beschriftungsersteller enthält.  Für dieses Tutorial lassen wir es leer.

1. Sie können auch direkt auf dem Formular eine Kurzbeschreibung der Aufgabe hinzufügen.  Geben Sie **Beschriftungstutorial: Katzen und Hunde** ein.

1. Wählen Sie **Weiter** aus.

1. Lassen Sie das Kontrollkästchen im Abschnitt **Durch ML unterstützte Beschriftung** deaktiviert. Die durch ML unterstützte Beschriftung erfordert mehr Daten als in diesem Tutorial verwendet werden.

1. Wählen Sie **Projekt erstellen** aus.

Diese Seite wird nicht automatisch aktualisiert. Aktualisieren Sie die Seite nach einer Pause manuell, bis sich der Status des Projekts in **Erstellt** ändert.

## <a name="start-labeling"></a>Starten der Beschriftung

Sie haben nun Ihre Azure-Ressourcen eingerichtet und ein Datenbeschriftungsprojekt konfiguriert. Es ist an der Zeit, Ihren Daten Beschriftungen hinzuzufügen.

### <a name="tag-the-images"></a>Versehen der Bilder mit Tags

In diesem Teil des Tutorials wechseln Sie von der Rolle des *Projektadministrators* zu der eines *Beschriftungserstellers*.  Jeder Benutzer mit Zugriff für Mitwirkende für Ihren Arbeitsbereich kann zu einem Beschriftungsersteller werden.

1. Wählen Sie in [Machine Learning Studio](https://ml.azure.com) auf der linken Seite **Datenbeschriftung** aus, um Ihr Projekt zu finden.  

1. Wählen Sie **Beschriftungslink** für das Projekt aus.

1. Lesen Sie die Anweisungen, und wählen Sie dann **Aufgaben** aus.

1. Wählen Sie auf der rechten Seite eine Miniaturansicht aus, um die Anzahl der Bilder anzuzeigen, die Sie in einem Durchgang beschriften möchten. Sie müssen alle diese Bilder beschriften, bevor Sie fortfahren können. Wechseln Sie das Layout nur, wenn Sie über eine neue Seite mit Daten ohne Bezeichnung verfügen. Durch das Wechseln von Layouts werden die bereits ausgeführten Bezeichnungen der Seite gelöscht.

1. Wählen Sie ein oder mehrere Bilder und dann ein Tag aus, mit dem die Auswahl versehen werden soll. Das Tag wird unter dem Bild angezeigt.  Fahren Sie mit dem Auswählen und Versehen aller Bilder mit Tags auf der Seite fort.  Wenn Sie alle angezeigten Bilder gleichzeitig auswählen möchten, wählen Sie **Alle auswählen** aus. Wählen Sie mindestens ein Bild aus, das mit einem Tag versehen werden soll.


    > [!TIP]
    > Die ersten neun Tags können über die Zifferntasten auf der Tastatur ausgewählt werden.

1. Sobald alle Bilder auf der Seite mit Tags versehen sind, wählen Sie **Senden** aus, um diese Beschriftungen zu übermitteln.

    ![Taggen von Bildern](media/tutorial-labeling/catsndogs.gif)

1. Nach der Übermittlung von Tags für die vorliegenden Daten aktualisiert Azure die Seite mit einer neuen Gruppe von Bildern aus der Arbeitswarteschlange.

## <a name="complete-the-project"></a>Abschließen des Projekts

Kehren Sie nun zur Rolle des *Projektadministrators* für das Beschriftungsprojekt zurück.

Als Vorgesetzter möchten Sie ggf. die Arbeit Ihrer Beschriftungsersteller überprüfen.  

### <a name="review-labeled-data"></a>Überprüfen beschrifteter Daten

1. Wählen Sie in [Machine Learning Studio](https://ml.azure.com) auf der linken Seite **Datenbeschriftung** aus, um Ihr Projekt zu finden.  

1. Wählen Sie den Link mit dem Projektnamen aus.

1. Das Dashboard zeigt den Status des Projekts.

1. Wählen Sie oben auf der Seite **Daten** aus.

1. Wählen Sie auf der linken Seite **Beschriftete Daten** aus, um Ihre mit Tags versehenen Bilder zu sehen.  

1. Wenn Sie mit einer Beschriftung nicht einverstanden sind, wählen Sie das Bild und dann unten auf der Seite **Ablehnen** aus.  Die Tags werden entfernt und das Bild wieder in die Warteschlange für nicht beschriftete Bilder gestellt.

### <a name="export-labeled-data"></a>Exportieren beschrifteter Daten

Sie können die Beschriftungsdaten für Machine Learning-Experimente jederzeit exportieren. Benutzer exportieren oft mehrfach und trainieren verschiedene Modelle, anstatt darauf zu warten, dass alle Bilder beschriftet wurden.

Bildbeschriftungen können im [COCO-Format](http://cocodataset.org/#format-data) oder als Azure Machine Learning-Dataset exportiert werden. Das Datasetformat macht es einfach, es für das Training in Azure Machine Learning einzusetzen.  

1. Wählen Sie in [Machine Learning Studio](https://ml.azure.com) auf der linken Seite **Datenbeschriftung** aus, um Ihr Projekt zu finden.  

1. Wählen Sie den Link mit dem Projektnamen aus.

1. Wählen Sie **Exportieren** und **Als Azure ML-Dataset exportieren** aus. 

    Der Status des Exports wird direkt unter der Schaltfläche **Exportieren** angezeigt. 

1. Nachdem die Beschriftungen erfolgreich exportiert wurden, wählen Sie auf der linken Seite **Datasets** aus, um die Ergebnisse anzuzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Bilder beschriftet.  Verwenden Sie nun Ihre beschrifteten Daten zum:

> [!div class="nextstepaction"]
> [Trainieren eines Machine Learning-Modells zur Bilderkennung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
