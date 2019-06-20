---
title: 'Tutorial: Bereitstellen eines Machine Learning-Modells mithilfe der grafischen Benutzeroberfläche'
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie mithilfe der grafischen Benutzeroberfläche von Azure Machine Learning Service eine Vorhersageanalyselösung (Predictive Analytics-Lösung) erstellen. Sie erhalten Informationen zum Trainieren, Bewerten und Bereitstellen eines Machine Learning-Modells mithilfe von Drag & Drop-Modulen. Dieses Tutorial ist der zweite Teil einer zweiteiligen Reihe über das Prognostizieren von Automobilpreisen mithilfe der linearen Regression.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 8512ca2fe01c772d7e4c21a5cb09303b9804899c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389219"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Bereitstellen eines Machine Learning-Modells mithilfe der grafischen Benutzeroberfläche

In diesem Tutorial befassen Sie sich eingehend mit dem Entwickeln einer Vorhersagelösung mithilfe der grafischen Benutzeroberfläche von Azure Machine Learning Service. Dieses Tutorial ist der **zweite Teil einer zweiteiligen Reihe**. Im [ersten Teil des Tutorials](ui-tutorial-automobile-price-train-score.md) haben Sie ein Modell zum Prognostizieren von Automobilpreisen trainiert, bewertet und ausgewertet. In diesem Teil des Tutorials führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten eines Modells für die Bereitstellung
> * Bereitstellen eines Webdiensts
> * Testen eines Webdiensts
> * Verwalten eines Webdiensts
> * Nutzen des Webdiensts

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie den [ersten Teil des Tutorials](ui-tutorial-automobile-price-train-score.md) ab.

## <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung

Um anderen Personen die Möglichkeit zu bieten, das in diesem Tutorial entwickelte Vorhersagemodell zu verwenden, können Sie es als Azure-Webdienst bereitstellen.

Bis jetzt haben Sie mit dem Trainieren des Modells experimentiert. Jetzt ist es an der Zeit, neue Vorhersagen basierend auf Benutzereingaben zu generieren.

Die Bereitstellungsvorbereitung umfasst zwei Schritte:  

1. Konvertieren des von Ihnen erstellten *Trainingsexperiments* in ein *Vorhersageexperiment*
1. Bereitstellung des Vorhersageexperiments als Webdienst

Sie können zunächst eine Kopie des Experiments erstellen, indem Sie unten im Experimentbereich die Option **Save As** (Speichern unter) auswählen.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Um dieses Modell auf die Bereitstellung vorzubereiten, müssen Sie dieses Trainingsexperiment in ein Vorhersageexperiment konvertieren. Dieser Vorgang umfasst in der Regel drei Schritte:

1. Speichern des trainierten Modells und Ersetzen der Trainingsmodule
1. Optimieren des Experiments, um Module zu entfernen, die nur zu Trainingszwecken benötigt wurden
1. Festlegen, wo der Webdienst Eingabedaten akzeptiert und wo er die Ausgabe generiert

Sie können diese Schritte manuell ausführen. Sie können aber auch unten im Experimentbereich die Option **Set Up Web Service** (Webdienst einrichten) auswählen, damit die Schritte automatisch ausgeführt werden.

![Animierte GIF der Darstellung der automatischen Konvertierung eines Trainingsexperiments in ein Vorhersageexperiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Wenn Sie **Set Up Web Service** (Webdienst einrichten) auswählen, werden mehrere Vorgänge ausgeführt:

* Das trainierte Modell wird in ein einzelnes **Trained Model**-Modul konvertiert. Es wird in der Modulpalette gespeichert, die sich links neben dem Experimentbereich befindet. Sie finden das Modell unter **Trained Models** (Trainierte Modelle).
* Module, die zum Training verwendet wurden, werden entfernt. Das gilt insbesondere für:
  * Train Model (Modell trainieren)
  * Split Data (Daten aufteilen)
  * Evaluate Model (Modell auswerten)
* Das gespeicherte trainierte Modell wird dem Experiment wieder hinzugefügt
* Die Module **Web service input** und **Web service output** werden hinzugefügt. Diese Module identifizieren, wo die Daten des Benutzers in das Modell eingehen und wo die Daten zurückgegeben werden.

Das Experiment wird in zwei Teilen auf den neuen Registerkarten gespeichert, die sich am oberen Rand des Experimentbereichs befinden. Das ursprüngliche Trainingsexperiment befindet sich unter der Registerkarte **Training Experiment**. Das neu erstellte Vorhersageexperiment befindet sich unter **Predictive Experiment**. Sie möchten das Vorhersageexperiment als Webdienst bereitstellen.

Ihr Experiment sollte jetzt wie folgt aussehen:  

![Screenshot der erwarteten Konfiguration des Experiments nach dem Vorbereiten der Bereitstellung](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Führen Sie das Experiment ein letztes Mal aus, indem Sie **Run** (Ausführen) auswählen. Wählen Sie im Popupdialogfeld das Computeziel aus, in dem das Experiment ausgeführt werden soll. Um zu überprüfen, ob das Modell noch funktioniert, wählen Sie die Ausgabe des Moduls „Score Model“ aus, und wählen Sie dann **View Results** (Ergebnisse anzeigen) aus. Daraufhin werden die ursprünglichen Daten zusammen mit dem prognostizierten Preis („Scored Labels“) angezeigt.

## <a name="deploy-the-web-service"></a>Bereitstellen des Webdiensts

Gehen Sie wie folgt vor, um einen aus Ihrem Experiment abgeleiteten neuen Webdienst bereitzustellen:

1. Wählen Sie unten im Experimentbereich die Option **Deploy Web Service** (Webdienst bereitstellen) aus.
1. Wählen Sie das **Computeziel** für die Ausführung Ihres Webdiensts aus.

    Derzeit unterstützt die grafische Benutzeroberfläche nur die Bereitstellung in Azure Kubernetes Service-Computezielen (AKS). Sie können im Machine Learning Service-Arbeitsbereich eines der verfügbaren AKS-Computeziele auswählen oder eine neue AKS-Umgebung konfigurieren, indem Sie im daraufhin angezeigten Dialogfeld die entsprechenden Schritte ausführen.

    ![Screenshot einer möglichen Konfiguration für ein neues Computeziel](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Wählen Sie **Deploy Web Service** (Webdienst bereitstellen) aus. Wenn die Bereitstellung abgeschlossen ist, wird die folgende Benachrichtigung angezeigt. Die Bereitstellung kann mehrere Minuten dauern.

    ![Screenshot der Bestätigungsmeldung einer erfolgreichen Bereitstellung.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testen des Webdiensts

Die Benutzereingabedaten gehen über das Modul **Web Service Input** in Ihr bereitgestelltes Modell ein. Die Eingabe wird dann im Modul **Score Model** bewertet. Gemäß der Einrichtung des Vorhersageexperiments erwartet das Modell Daten in dem Format, das dem Format des ursprünglichen Automobilpreis-Datasets entspricht. Die Ergebnisse werden abschließend über das Modul **Web Service Output** an den Benutzer zurückgegeben.

Sie können einen Webdienst auf der grafischen Benutzeroberfläche auf der Registerkarte „Web Services“ (Webdienste) testen.

1. Wechseln Sie zum Abschnitt „Web Services“ (Webdienste). Es wird der von Ihnen bereitgestellte Webdienst mit dem Namen **Tutorial – Predict Automobile Price[Predictive Exp]** (Tutorial – Automobilpreisvorhersage[Vorhersageexperiment]) angezeigt.

     ![Screenshot der Registerkarte „Web Services“, in dem der zuletzt erstellte Webdienst hervorgehoben ist](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Wählen Sie den Namen des Webdiensts aus, um weitere Details anzuzeigen.

     ![Screenshot der zusätzlichen Details, die in der Webdienstansicht verfügbar sind](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Klicken Sie auf **Test**.

    ![Screenshot der Testseite für den Webdienst](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Geben Sie Testdaten ein, oder verwenden Sie die automatisch ausgefüllten Beispieldaten, und wählen Sie unten die Option **Test** (Testen) aus. Die Testanforderung wird an den Webdienst gesendet, und die Ergebnisse werden auf der Seite angezeigt. Für die Eingabedaten wird zwar ein Preiswert generiert, dieser wird jedoch nicht zum Generieren des Vorhersagewerts verwendet.

## <a name="manage-the-web-service"></a>Verwalten des Webdiensts

Nachdem Sie Ihren Webdienst bereitgestellt haben, können Sie ihn auf der grafischen Benutzeroberfläche auf der Registerkarte **Web Services** (Webdienste) verwalten.

Sie können einen Webdienst löschen, indem Sie auf der Seite der Webdienstdetails die Option **Delete** (Löschen) auswählen.

   ![Screenshot, in dem die Position der Schaltfläche zum Löschen des Webdiensts unten im Fenster hervorgehoben ist](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Nutzen des Webdiensts

In den vorherigen Schritten dieses Tutorials haben Sie ein Automobilpreisvorhersagemodell als Azure-Webdienst bereitgestellt. Jetzt können Benutzer Daten an den Dienst senden und Ergebnisse über die REST-API erhalten.

**Anforderung/Antwort** – Der Benutzer sendet eine oder mehrere Zeilen von Automobildaten mithilfe eines Hypertext Transfer-Protokolls (HTTP) an den Dienst. Der Dienst antwortet mit einem oder mehreren Ergebnissätzen.

REST-Beispielaufrufe finden Sie auf der Seite der Webdienstdetails auf der Registerkarte **Consume** (Nutzung).

   ![Screenshot eines REST-Beispielaufrufs, der Benutzern auf der Registerkarte „Consume“ angezeigt wird](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navigieren Sie zur Registerkarte **API Doc** (API-Dok), um weitere API-Details anzuzeigen.

  ![Screenshot der zusätzlichen API-Details, die Benutzern auf der Registerkarte „API Doc“ angezeigt werden](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Verwalten von Modellen und Bereitstellungen im Azure Machine Learning Service-Arbeitsbereich

Die von Ihnen auf der grafischen Benutzeroberfläche erstellten Modelle und Webdienstbereitstellungen können im Azure Machine Learning Service-Arbeitsbereich verwaltet werden.

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Models** (Modelle) aus. Wählen Sie dann das Experiment aus, das Sie erstellt haben.

    ![Screenshot, der zeigt, wie Sie im Azure-Portal zu Experimenten navigieren](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Auf dieser Seite werden weitere Details zum Modell angezeigt.

    ![Screenshot einer Übersicht über die Experimentstatistik im Azure-Portal](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Wählen Sie **Deployments** (Bereitstellungen) aus. Auf dieser Registerkarte werden alle Webdienste aufgeführt, die das Modell verwenden. Wählen Sie den Namen des Webdiensts aus. Daraufhin wird die Seite der Webdienstdetails angezeigt. Auf dieser Seite finden Sie ausführlichere Informationen zum Webdienst.

    ![Screenshot eines detaillierten Ausführungsberichts](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die wichtigsten Schritte zum Erstellen, Bereitstellen und Verwenden eines Machine Learning-Modells auf der grafischen Benutzeroberfläche gelernt. Wenn Sie mehr darüber erfahren möchten, wie Sie die grafische Benutzeroberfläche verwenden können, um andere Arten von Problemen zu lösen, sehen Sie sich die Beispielexperimente an.

> [!div class="nextstepaction"]
> [Beispiel für die Kreditrisikoklassifizierung](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
