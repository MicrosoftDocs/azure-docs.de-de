---
title: 'ML Studio (Classic): Excel-Add-In für Webdienste – Azure'
description: Hier wird erläutert, wie Sie Azure Machine Learning-Webdienste direkt in Excel ohne Erstellung von Code verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 45d9e494b9f885cfa0680bec595aefcd4074d41e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519999"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-Add-In für (klassische) Azure Machine Learning Studio-Webdienste

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Mit Excel können Webdienste auf einfache Weise direkt aufgerufen werden, ohne dafür Code erstellen zu müssen.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Schritte zum Verwenden eines vorhandenen Webdiensts in einer Arbeitsmappe

1. Öffnen Sie die [Excel-Beispieldatei](https://aka.ms/amlexcel-sample-2), die das Excel-Add-In und Daten zu den Passagieren auf der Titanic enthält. 
 
    > [!NOTE]
    > - Es wird eine Liste der Webdienste angezeigt, die der Datei zugeordnet sind, und unten ein Kontrollkästchen für die automatische Vorhersage. Wenn Sie die automatische Vorhersage aktivieren, werden die Vorhersagen **aller** Ihrer Dienste bei jeder Änderung in den Eingaben aktualisiert. Wenn die Option nicht aktiviert ist, müssen Sie zum Aktualisieren auf "Predict All" (Alle vorhersagen) klicken. Informationen zum Aktivieren der automatischen Vorhersage auf Dienstebene finden Sie unter Schritt 6.
    > - Das Azure Machine Learning-Add-In für Excel ruft beim Laden den Office Store für Add-Ins auf. Wenn Ihre Organisation den Zugriff auf den Office Store für Add-Ins untersagt, wird beim Laden des Add-Ins ein Fehler angezeigt. Stellen Sie in diesem Fall das Azure Machine Learning-Add-In für Excel über Microsoft 365 Admin Center bereit. Rufen Sie dann das Add-In auf, und fügen Sie den Webdienst manuell hinzu, indem Sie die URL und den API-Schlüssel einfügen.

 

2. Wählen Sie den Webdienst aus, indem Sie darauf klicken – in diesem Beispiel ist das „Titanic Survivor Predictor (Excel-Add-In-Beispiel) [Ergebnis]“.
   
    ![Webdienst auswählen](./media/excel-add-in-for-web-services/image1.png)
3. Dadurch gelangen Sie zum Abschnitt **Predict**.  Diese Arbeitsmappe enthält bereits Beispieldaten. In einer leeren Arbeitsmappe können Sie jedoch auch eine Zelle in Excel auswählen und auf **Use sample data** klicken.
4. Wählen Sie die Daten mit Überschriften aus, und klicken Sie auf das Symbol für den Eingabedatenbereich.  Stellen Sie sicher, dass das Kontrollkästchen „Daten haben Überschriften“ aktiviert ist.
5. Geben Sie unter **Ausgabe** die Zellennummer ein, in der die Ausgabe erfolgen soll. Bei diesem Beispiel „H1“.
6. Klicken Sie auf **Vorhersagen**. Wenn Sie das Kontrollkästchen "auto-predict" (automatische Vorhersage) aktivieren, löst jede Änderung in den ausgewählten Bereichen (denen, die als Eingabe festgelegt sind), eine Anforderung und eine Aktualisierung der Ausgabezellen aus, ohne dass Sie auf die Vorhersageschaltfläche klicken müssten.
   
    ![Abschnitt „Predict“](./media/excel-add-in-for-web-services/image1.png)

Stellen Sie einen Webdienst bereit, oder verwenden Sie einen vorhandenen Webdienst. Weitere Informationen zum Bereitstellen eines Webdiensts finden Sie unter [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md).

Rufen Sie den API-Schlüssel Ihres Webdiensts ab. Wo diese Aktion durchgeführt wird hängt davon ab, ob Sie einen klassischen oder neuen Machine Learning-Webdienst veröffentlicht haben.

**Verwenden eines klassischen Webdiensts** 

1. Klicken Sie in Machine Learning Studio (klassisch) im linken Bereich auf den Abschnitt **WEBDIENSTE**, und wählen Sie den Webdienst aus.
   
    ![Studio – Webdienst auswählen](./media/excel-add-in-for-web-services/image4.png)
2. Kopieren Sie den API-Schlüssel für den Webdienst.
   
    ![Studio-API-Schlüssel](./media/excel-add-in-for-web-services/image5.png)
3. Klicken Sie auf der Registerkarte **DASHBOARD** für den Webdienst auf die Verknüpfung **ANFORDERUNG/ANTWORT**.
4. Wechseln Sie zum Abschnitt **Request URI** .  Kopieren und speichern Sie die URL.

> [!NOTE]
> Es ist jetzt möglich, sich beim Portal [Azure Machine Learning Web Services](https://services.azureml.net) anzumelden, um den API-Schlüssel für einen klassischen Machine Learning-Webdienst abzurufen.
> 
> 

**Verwenden eines neuen Webdiensts**

1. Klicken Sie im [Azure Machine Learning Web Services](https://services.azureml.net)-Portal auf **Web Services** (Webdienste), und wählen Sie Ihren Webdienst aus. 
2. Klicken Sie auf **Consume**.
3. Wechseln Sie zum Abschnitt **Basic consumption info** . Kopieren und speichern Sie den **Primary Key** und die URL für **Request-Response**.

## <a name="steps-to-add-a-new-web-service"></a>Schritte zum Hinzufügen eines neuen Webdiensts

1. Stellen Sie einen Webdienst bereit, oder verwenden Sie einen vorhandenen Webdienst. Weitere Informationen zum Bereitstellen eines Webdiensts finden Sie unter [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md).
2. Klicken Sie auf **Consume**.
3. Wechseln Sie zum Abschnitt **Basic consumption info** . Kopieren und speichern Sie den **Primary Key** und die URL für **Request-Response**.
4. Navigieren Sie in Excel zum Abschnitt **Web Services** (wenn Sie sich im Abschnitt **Predict** befinden, klicken Sie auf den Zurück-Pfeil, um zur Liste der Webdienste zu gelangen).
   
    ![Webdienstauswahl aufrufen](./media/excel-add-in-for-web-services/image3.png)
5. Klicken Sie auf **Webdienst hinzufügen**.
6. Fügen Sie die URL in das Textfeld des Excel-Add-Ins mit der Beschriftung **URL** ein.
7. Fügen Sie die API/den Primärschlüssel in das Textfeld mit der Beschriftung **API key** ein.
8. Klicken Sie auf **Hinzufügen**.
   
    ![URL und API-Schlüssel für einen klassischen Webdienst.](./media/excel-add-in-for-web-services/image6.png)
9. Folgen Sie den vorherigen Anweisungen unter „Schritte zum Verwenden eines vorhandenen Webdiensts“, um den Webdienst zu verwenden.

## <a name="sharing-your-workbook"></a>Freigeben Ihrer Arbeitsmappe
Wenn Sie Ihre Arbeitsmappe speichern, werden auch die API/der Primärschlüssel der hinzugefügten Webdienste gespeichert. Das bedeutet, dass Sie die Arbeitsmappe nur für Personen freigeben sollten, denen Sie vertrauen.

Im nachstehenden Kommentarabschnitt oder in unserem [Forum](/answers/topics/azure-machine-learning.html) können Sie Fragen stellen.