---
title: 'ML Studio (Classic): Entwicklung eines Modells zu einem Webdienst – Azure'
description: Ein Überblick darüber, wie Ihr (klassisches) Azure Machine Learning Studio-Modell sich von einem Entwicklungsexperiment zu einem Webdienst entwickelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: c92f8c74da76b2ac938892e27f3d6be9c70c3238
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507260"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Entwicklung eines (klassischen) Machine Learning Studio-Modells von einem Experiment zu einem Webdienst

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (klassisch) bietet eine interaktive Canvas, auf der Sie ein **_Experiment_* _ entwickeln, ausführen, testen und durchlaufen können, das ein Vorhersageanalysemodell darstellt. Es gibt eine Vielzahl von Modulen, die Folgendes ermöglichen:

_ Eingabe von Daten in das Experiment
* Manipulation der Daten
* Training eines Modells mit Machine Learning-Algorithmen
* Bewertung des Modells
* Auswertung der Ergebnisse
* Ausgabe der endgültigen Werte

Sobald Sie mit dem Experiment zufrieden sind, können Sie es als ***klassischen Azure Machine Learning-Webdienst** _ oder _*_neuen Azure Machine Learning-Webdienst_*_ bereitstellen, damit Benutzer neue Daten an diesen senden und Ergebnisse abrufen können.

In diesem Artikel geben wir einen Überblick, wie Ihr Machine Learning-Modell sich von einem Entwicklungsexperiment zu einem betriebsbereiten Webdienst entwickelt.

> [!NOTE]
> Es gibt noch weitere Methoden zum Entwickeln und Bereitstellen von Machine Learning-Modellen, aber dieser Artikel beschränkt sich auf die Verwendung von Machine Learning Studio (klassisch). Eine Erläuterung zum Erstellen eines klassischen Vorhersagewebdiensts mit R finden Sie beispielsweise im Blogbeitrag [Build & Deploy Predictive Web Apps Using RStudio and Azure Machine Learning Studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml) (Erstellen und Bereitstellen von Vorhersage-Web-Apps mit RStudio und Azure Machine Learning Studio).
>
>

Azure Machine Learning Studio (klassisch) ist darauf ausgerichtet, Sie beim Entwickeln und Bereitstellen eines _Vorhersageanalysemodells* zu unterstützen. Es ist jedoch auch möglich, mithilfe von Studio (klassisch) ein Experiment zu entwickeln, das kein Vorhersageanalysemodell umfasst. Beispielsweise kann ein Experiment nur Daten eingeben, bearbeiten und anschließend die Ergebnisse ausgeben. Genau wie ein Vorhersageanalyseexperiment können Sie dieses Nicht-Vorhersageexperiment als Webdienst bereitstellen, es ist jedoch ein einfacherer Vorgang, da durch das Experiment kein Machine Learning-Modell trainiert oder bewertet wird. Auch wenn dies nicht das typische Einsatzgebiet von Studio (Classic) ist, wird es in diesem Thema berücksichtigt, um Ihnen eine vollständige Erläuterung der Funktionsweise von Studio (Classic) zu geben.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Entwickeln und Bereitstellen eines Vorhersagewebdiensts
Im Folgenden sind die Phasen einer typischen Lösung aufgeführt, während Sie sie mit Machine Learning Studio (klassisch) entwickeln und bereitstellen:

![Bereitstellungsablauf](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Abbildung 1: Phasen eines typischen Vorhersageanalysemodells*

### <a name="the-training-experiment"></a>Das Trainingsexperiment
Das ***Trainingsexperiment** _ ist die Anfangsphase der Entwicklung Ihres Webdiensts in Machine Learning Studio (klassisch). Das Trainingsexperiment soll Ihnen einen Bereich bieten, in dem Sie ein Machine Learning-Modell entwickeln, testen, durchlaufen und schließlich trainieren. Sie können sogar mehrere Modelle gleichzeitig trainieren, während sie nach der idealen Lösung suchen. Wenn Sie mit dem Experimentieren fertig sind, wählen Sie jedoch ein einziges trainiertes Modell aus und löschen die übrigen aus dem Experiment. Ein Beispiel für die Entwicklung eines Vorhersageanalyseexperiments finden Sie unter [Entwickeln einer Lösung zur Vorhersageanalyse für die Kreditrisikobewertung in Azure Machine Learning Studio (klassisch)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Das Vorhersageexperiment
Sobald das Trainingsexperiment ein trainiertes Modell umfasst, klicken Sie in Machine Learning Studio (klassisch) auf _*Set Up Web Service** (Webdienst einrichten). Wenn Sie dann **Predictive Web Service** (Vorhersagewebdienst) auswählen, durchläuft Studio den Prozess zur Konvertierung des Trainingsexperiments in ein **_Vorhersageexperiment_*_. Das Vorhersageexperiment verwendet Ihr trainiertes Modell zur Bewertung neuer Daten und soll letztlich als Azure-Webdienst in Betrieb genommen werden.

Diese Konvertierung wird durch die folgenden Schritte durchgeführt:

_ Konvertieren des Satzes von Modulen, die für das Training verwendet werden, in ein einziges Modul und Speichern des Moduls als trainiertes Modell
* Beseitigen überflüssiger Module, die nicht im Zusammenhang mit der Bewertung stehen
* Hinzufügen von Eingabe- und Ausgabeports, die vom letztendlichen Webdienst verwendet werden

Es gibt möglicherweise weitere Änderungen, die Sie vornehmen möchten, um das Vorhersageexperiment auf die Bereitstellung als Webdienst vorzubereiten. Wenn der Webdienst zum Beispiel nur eine Teilmenge der Ergebnisse ausgeben soll, können Sie vor dem Ausgabeport ein Filtermodul hinzufügen.

In diesem Konvertierungsprozess wird das Trainingsexperiment nicht verworfen. Nach Abschluss des Prozesses werden in Studio (klassisch) zwei Registerkarten angezeigt: eine für das Trainingsexperiment und eine für das Vorhersageexperiment. So können Sie vor dem Bereitstellen Ihres Webdiensts noch Änderungen am Trainingsexperiment durchführen und das Vorhersageexperiment neu erstellen. Ebenso können Sie eine Kopie des Trainingsexperiments speichern, um eine weitere Experimentreihe zu starten.

> [!NOTE]
> Durch Klicken auf **Set Up Web Service** starten Sie einen automatischen Prozess zum Konvertieren des Trainingsexperiments in ein Vorhersageexperiment. In den meisten Fällen verläuft dieser reibungslos. Wenn das Trainingsexperiment komplex ist (z.B. mehrere Pfade für das Training verknüpft werden), möchten Sie diese Konvertierung vielleicht manuell ausführen. Weitere Informationen finden Sie unter [Vorbereiten des Modells für die Bereitstellung in Azure Machine Learning Studio (klassisch)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>Der Webdienst
Sobald Sie mit Ihrem Vorhersageexperiment zufrieden sind, können Sie Ihren Dienst entweder als klassischen Webdienst oder neuen auf Azure Resource Manager basierenden Webdienst bereitstellen. Um das Modell durch Bereitstellung als *klassischen Machine Learning-Webdienst* in Betrieb zu nehmen, klicken Sie auf **Deploy Web Service** und wählen **Deploy Web Service [Classic]** . Um es als *neuen Machine Learning-Webdienst* bereitzustellen, klicken Sie auf **Deploy Web Service** und wählen **Deploy Web Service [New]** . Benutzer können jetzt über die REST-API des Webdiensts Daten an Ihr Modell senden und Ergebnisse zurückerhalten. Weitere Informationen finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Der untypische Fall: Erstellen eines Nicht-Vorhersagewebdiensts
Wenn das Experiment kein Vorhersageanalysemodell trainiert, müssen Sie nicht sowohl ein Trainingsexperiment als auch ein Bewertungsexperiment erstellen. Es gibt nur ein Experiment, das als Webdienst bereitgestellt werden kann. Machine Learning Studio (Classic) erkennt durch Analyse der verwendeten Module, ob das Experiment ein Vorhersagemodell enthält.

Wenn Sie Ihr Experiment durchlaufen haben und damit zufrieden sind:

1. Klicken Sie auf **Set Up Web Service**, und wählen Sie **Retraining Web Service**. Eingabe- und Ausgabeknoten werden automatisch hinzugefügt.
2. Klicken Sie auf **Run**.
3. Klicken Sie auf **Deploy Web Service**, und wählen Sie **Deploy Web Service [Classic]** oder **Deploy Web Service [New]** , was von der Umgebung abhängig, in der die Bereitstellung erfolgen soll.

Ihr Webdienst wird jetzt bereitgestellt, und Sie können genau wie bei einem Vorhersagewebdienst darauf zugreifen und ihn verwalten.

## <a name="updating-your-web-service"></a>Aktualisieren des Webdiensts
Was geschieht, wenn Sie das Experiment nach seiner Bereitstellung als Webdienst aktualisieren möchten?

Das hängt davon ab, was Sie aktualisieren möchten:

**Sie möchten die Eingabe oder Ausgabe ändern, oder Sie möchten die Bearbeitung von Daten durch den Webdienst ändern**

Wenn Sie nicht das Modell ändern möchten, sondern nur die Art und Weise, wie Daten vom Webdienst verarbeitet werden, können Sie das Vorhersageexperiment bearbeiten, erneut auf **Deploy Web Service** (Webdienst bereitstellen) klicken und dann **Deploy Web Service [Classic]** (Webdienst bereitstellen [klassisch]) oder **Deploy Web Service [New]** (Webdienst bereitstellen [Neu]) auswählen. Der Webdienst wird beendet, das aktualisierte Vorhersageexperiment bereitgestellt und der Webdienst neu gestartet.

Hier sehen Sie ein Beispiel: Angenommen, das Vorhersageexperiment gibt die gesamte Zeile der Eingabedaten mit dem vorhergesagten Ergebnis zurück. Sie beschließen, dass der Webdienst nur das Ergebnis zurückgeben soll. Sie können im Vorhersageexperiment direkt vor dem Ausgabeport ein Modul **Project Columns** hinzufügen, um andere Spalten außer dem Ergebnis auszuschließen. Wenn Sie auf **Deploy Web Service** klicken und erneut **Deploy Web Service [Classic]** oder **Deploy Web Service [New]** wählen, wird der Webdienst aktualisiert.

**Sie möchten das Modell mit neuen Daten neu trainieren**

Wenn Sie Ihr Machine Learning-Modell beibehalten, es aber mit neuen Daten neu trainieren möchten, haben Sie zwei Optionen:

1. **Neutrainieren des Modells, während der Webdienst ausgeführt wird:** Wenn Sie das Modell neu trainieren möchten, während der Vorhersagewebdienst ausgeführt wird, müssen Sie dazu ein paar Änderungen am Trainingsexperiment vornehmen, um es als **_Neutrainingsexperiment_ *_ festzulegen. Anschließend können Sie es als _* _Neutrainingswebdienst_** bereitstellen. Weitere Anweisungen hierzu finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](./retrain-machine-learning-model.md).
2. **Zurückwechseln zum ursprünglichen Trainingsexperiment und Verwenden anderer Trainingsdaten zum Entwickeln Ihres Modells.** Ihr Vorhersageexperiment ist mit dem Webdienst verknüpft, aber das Trainingsexperiment ist nicht direkt auf diese Weise verknüpft. Wenn Sie das ursprüngliche Trainingsexperiment ändern und auf **Set Up Web Service** klicken, wird ein *neues* Vorhersageexperiment erstellt, das bei seiner Bereitstellung einen *neuen* Webdienst erstellt. Der ursprüngliche Webdienst wird nicht einfach aktualisiert.

   Wenn Sie daher das Trainingsexperiment ändern müssen, öffnen Sie es, und klicken Sie auf **Save as** , um eine Kopie zu erstellen. Auf diese Weise bleiben das ursprüngliche Trainingsexperiment, das Vorhersageexperiment und der Webdienst erhalten. Jetzt können Sie einen neuen Webdienst mit Ihren Änderungen erstellen. Nachdem Sie den neuen Webdienst bereitgestellt haben, können Sie entscheiden, ob Sie den vorherigen Webdienst beenden oder weiterhin parallel zu dem neuen Webdienst ausführen möchten.

**Sie möchten ein anderes Modell trainieren**

Wenn Sie am ursprünglichen Vorhersageexperiment Änderungen vornehmen möchten (z. B. einen anderen Machine Learning-Algorithmus auswählen oder eine andere Trainingsmethode ausprobieren), müssen Sie die zweite oben beschriebene Vorgehensweise zum Neutrainieren Ihres Modells befolgen: Öffnen Sie das Trainingsexperiment, klicken Sie auf **Save as**, um eine Kopie zu erstellen, und starten Sie einen neuen Pfad zur Entwicklung des Modells, zur Erstellung des Vorhersageexperiments und zur Bereitstellung des Webdiensts. Dadurch wird ein neuer Webdienst erstellt, der vom ursprünglichen unabhängig ist. Sie können entscheiden, welcher von beiden weiterhin ausgeführt wird bzw. ob beide parallel ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Prozess des Entwickelns und Experimentierens finden Sie in den folgenden Artikeln:

* Konvertieren des Experiments: [Vorbereiten des Modells für die Bereitstellung in Azure Machine Learning Studio (klassisch)](deploy-a-machine-learning-web-service.md)
* Bereitstellen des Webdiensts: [Bereitstellen eines Azure Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md)
* Neutrainieren des Modells: [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](./retrain-machine-learning-model.md)

Beispiele für den gesamten Prozess finden Sie unter:

* [Machine Learning-Tutorial: Ihr erstes Experiment in Azure Machine Learning Studio (klassisch) erstellen](create-experiment.md).
* [Exemplarische Vorgehensweise: Entwickeln einer Predictive Analytics-Lösung für die Kreditrisikobewertung in Azure Machine Learning](tutorial-part1-credit-risk.md)