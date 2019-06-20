---
title: Erstellen und Untersuchen von Experimenten im Portal
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie automatisierte Machine Learning-Experimente im Portal erstellen und verwalten können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990149"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Erstellen und Untersuchen automatisierter Machine Learning-Experimente im Azure-Portal (Vorschau)

 In diesem Artikel erfahren Sie, wie Sie automatisierte Machine Learning-Experimente im Azure-Portal ohne eine einzige Zeile Code erstellen, ausführen und untersuchen können. Automatisiertes Machine Learning automatisiert den Prozess der Auswahl des besten Algorithmus für Ihre spezifischen Daten, sodass Sie schnell ein Modell für Machine Learning erstellen können. [Weitere Informationen zu automatisiertem Machine Learning](concept-automated-ml.md).

 Wenn Sie eine eher codebasierte Erfahrung bevorzugen, können Sie auch [Ihre automatisierten Machine Learning-Experimente in Python](how-to-configure-auto-train.md) mit dem [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

* Ein Azure Machine Learning-Dienstbereich. Siehe [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Erste Schritte

Navigieren Sie zum linken Bereich des Arbeitsbereichs. Wählen Sie „Automated Machine Learning“ (Automatisiertes Machine Learning) im Abschnitt „Authoring (Preview)“ (Erstellen (Vorschau)) aus.

![Navigationsbereich im Azure-Portal](media/how-to-create-portal-experiments/nav-pane.png)

 Wenn Sie zum ersten Mal mit automatisiertem Machine Learning experimentieren, wird Folgendes angezeigt:

![Startseite für Experimente im Azure-Portal](media/how-to-create-portal-experiments/landing-page.png)

Andernfalls wird das Automated Machine Learning-Dashboard mit einer Übersicht über alle Ihre automatisierten Machine Learning-Experimente angezeigt, einschließlich derjenigen, die mit dem SDK erstellt wurden. Hier können Sie Ihre Ausführungen nach Datum, Experimentname und Ausführungsstatus filtern und untersuchen.

![Experimentdashboard im Azure-Portal](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Erstellen eines Experiments

Wählen Sie die Schaltfläche „Create Experiment“ (Experiment erstellen) aus, um das folgende Formular mit Daten aufzufüllen.

![Formular zum Erstellen eines Experiments](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Geben Sie Ihren Experimentnamen ein.

1. Wählen Sie einen Compute für den Datenprofilerstellungs- und Trainingsauftrag aus. Eine Liste Ihrer vorhandenen Computes ist in der Dropdownliste verfügbar. Folgen Sie den Anweisungen in Schritt 3, um einen neuen Compute zu erstellen.

1. Wählen Sie die Schaltfläche „Create a new compute“ (Neuen Compute erstellen) aus, um den darunter liegenden Bereich zu öffnen, und konfigurieren Sie Ihren Computekontext für dieses Experiment.

    ![Erstellen eines neuen Computes für das Experiment](media/how-to-create-portal-experiments/create-new-compute.png)

    Feld|BESCHREIBUNG
    ---|---
    Computename| Geben Sie einen eindeutigen Namen ein, der Ihren Computekontext identifiziert.
    Größe des virtuellen Computers| Wählen Sie die Größe für Ihren Computes aus.
    Zusätzliche Einstellungen| *Min node* (Mindestanzahl von Knoten): Geben Sie die Mindestanzahl von Knoten für Ihren Compute ein. Die Mindestanzahl von Knoten für AML-Compute ist 0. Zum Aktivieren der Datenprofilerstellung müssen Sie über mindestens einen Knoten verfügen. <br> *Max node* (Maximale Anzahl von Knoten): Geben Sie die maximale Anzahl von Knoten für Ihren Compute ein. Der Standardwert ist 6 Knoten für einen AML-Compute.

      Wählen Sie zum Starten der Erstellung Ihres neuen Computes **Create** (Erstellen) aus. Dies kann einen Moment dauern.

      >[!NOTE]
      > Ihr Computename gibt an, ob für den von Ihnen ausgewählten/erstellten Compute *Profilerstellung aktiviert* ist. (Weitere Informationen zur Datenprofilerstellung finden Sie unter 7 b).

1. Wählen Sie ein Speicherkonto für Ihre Daten aus. Die öffentliche Vorschau unterstützt nur lokale Dateiuploads und Azure Blob Storage-Konten.

1. Wählen Sie einen Speichercontainer aus.

1. Wählen Sie eine Datendatei aus Ihrem Speichercontainer aus, oder laden Sie eine Datei von Ihrem lokalen Computer in den Container hoch.

    ![Auswählen der Datendatei für das Experiment](media/how-to-create-portal-experiments/select-file.png)

1. Verwenden Sie die Vorschau- und Profilregisterkarten, um Ihre Daten für dieses Experiment weiter zu konfigurieren.

    1. Geben Sie auf der Registerkarte „Preview“ (Vorschau) an, ob Ihre Daten Header enthalten, und wählen Sie die Features (Spalten) für das Training mit den Umschaltflächen **Included** (Eingeschlossen) in jeder Featurespalte aus.

        ![Datenvorschau](media/how-to-create-portal-experiments/data-preview.png)

    1. Auf der Registerkarte „Profile“ (Profil) können Sie das [Datenprofil](#profile) nach Feature sowie die Verteilungs-, Typ- und Zusammenfassungsstatistiken (Mittelwert, Median, Maximum/Minimum usw.) der einzelnen Features anzeigen.

        ![Registerkarte „Datenprofil“](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Die folgende Fehlermeldung wird angezeigt, wenn für den Computekontext **keine** Profilerstellung aktiviert ist: *Data profiling is only available for compute targets that are already running* (Datenprofilerstellung ist nur für Computeziele verfügbar, die bereits ausgeführt werden).

1. Wählen Sie den Trainingsauftragstyp aus: Klassifizierung, Regression oder Vorhersage.

1. Wählen Sie die Zielspalte aus. Dies ist die Spalte, für die die Vorhersage erfolgen soll.

1. Für Vorhersagen:
    1. Wählen Sie die Zeitspalte aus: Diese Spalte enthält die zu verwendenden Zeitdaten.
    1. Wählen Sie den Vorhersagehorizont aus: Geben Sie an, wie viele Zeiteinheiten (Minuten/Stunden/Tage/Wochen/Monate/Jahre) das Modell die Zukunft vorhersagen können soll. Je weiter das Modell die Zukunft vorhersagen muss, desto ungenauer wird es. [Weitere Informationen zu Vorhersagen und zum Vorhersagehorizont](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Optional) Erweiterte Einstellungen: Zusätzliche Einstellungen, mit denen Sie den Trainingsauftrag besser steuern können.

    Erweiterte Einstellungen|BESCHREIBUNG
    ------|------
    Primary metric (Primäre Metrik)| Die wichtigste Metrik, die für die Bewertung Ihres Modells verwendet wird. [Weitere Informationen zur Modellmetriken](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Exit Criteria (Beendigungskriterien)| Wenn eines dieser Kriterien erfüllt ist, wird der Trainingsauftrag vor dem vollständigen Abschluss beendet. <br> *Training job time (minutes)* (Dauer des Trainingsauftrags (Minuten)): Gibt an, wie lange der Trainingsauftrag ausgeführt werden soll.  <br> *Max number of iterations* (Maximale Anzahl von Iterationen): Die maximale Anzahl von Pipelines (Iterationen), die im Trainingsauftrag getestet werden. Der Auftrag wird nicht häufiger als die angegebene Anzahl von Iterationen ausgeführt. <br> *Metric score threshold* (Metrischer Bewertungsschwellenwert):  Die Metrikmindestbewertung für alle Pipelines. Auf diese Weise wird sichergestellt, dass Sie nicht mehr Zeit für den Trainingsauftrag aufwenden als nötig, wenn Sie eine definierte Zielmetrik verwenden, die Sie erreichen möchten.
    Preprocessing (Vorverarbeitung)| Aktivieren oder deaktivieren Sie mit dieser Option die Vorverarbeitung durch automatisiertes Machine Learning. Vorverarbeitung umfasst die automatische Datenbereinigung, die Vorbereitung und die Transformation, um synthetische Features zu generieren. [Weitere Informationen zur Vorverarbeitung](#preprocess).
    Überprüfen| Wählen Sie eine der Optionen für Kreuzvalidierung aus, die im Trainingsauftrag verwendet werden soll. [Weitere Informationen zur Kreuzvalidierung](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Parallelität| Wählen Sie die Multi-Core-Grenzwerte aus, die Sie verwenden möchten, wenn Multi-Core-Compute zum Einsatz kommt.
    Blocked algorithm (Blockierter Algorithmus)| Wählen Sie Algorithmen aus, die Sie aus den Trainingsauftrag ausschließen möchten.

   ![Formular für erweiterte Einstellungen](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Um weitere Informationen zu den Feldern zu erhalten, klicken Sie auf die Informationen-QuickInfo.

<a name="profile"></a>

### <a name="data-profiling"></a>Datenprofilerstellung

Sie können eine Vielzahl von Zusammenfassungsstatistiken zu Ihrem Dataset erhalten, um zu überprüfen, ob Ihr Dataset für ML bereit ist. Für nicht numerische Spalten sind nur grundlegende Statistiken wie Mindestwert, Maximalwert und Fehleranzahl enthalten. Für numerische Spalten können Sie auch ihre statistische Zeit und die geschätzten Quantile überprüfen. Unser Datenprofil umfasst Folgendes:

* **Feature**: Der Name der Spalte, die zusammengefasst wird.

* **Profile**: Eine Inlinevisualisierung basierend auf dem abgeleiteten Typ. Beispielsweise weisen Zeichenfolgen, boolesche Werte und Datumsangaben Wertanzahlen auf, während Dezimalwerte (numerische Werte) Näherungswerte für Histogramme besitzen. Dadurch können Sie einen schnellen Überblick über die Verteilung der Daten erhalten.

* **Type distribution** (Typverteilung): Eine Inlinewertanzahl von Typen in einer Spalte. NULL-Werte sind eigene Typen, sodass diese Visualisierung für die Erkennung von ungewöhnlichen oder fehlenden Werten nützlich ist.

* **Type** (Typ): Der abgeleitete Typ der Spalte. Mögliche Werte: Zeichenfolgen, boolesche Werte, Datumsangaben und Dezimalwerte.

* **Min** (Mindestwert): Der Mindestwert der Spalte. Leere Einträge werden für Features angezeigt, deren Typ keine inhärente Reihenfolge aufweist (z.B. boolesche Werte).

* **Max** (Maximalwert): Der Maximalwert der Spalte. Wie bei „Min“ werden leere Einträge für Features mit irrelevanten Typen angezeigt.

* **Count** (Anzahl): Die Gesamtzahl der fehlenden und nicht fehlenden Einträge in der Spalte.

* **Not missing count** (Anzahl nicht fehlender): Die Anzahl der Einträge in der Spalte, die nicht fehlen. Beachten Sie, dass leere Zeichenfolgen und Fehler als Werte behandelt werden, damit sie nicht zur „Anzahl nicht fehlender“ beitragen.

* **Quantiles** (Quantile, in Intervallen von 0,1, 1, 5, 25, 50, 75, 95, 99 und 99,9 %): Die geschätzten Werte in jedem Quantil, um einen Eindruck von der Verteilung der Daten bereitzustellen. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

* **Mean** (Mittelwert): Das arithmetische Mittel der Spalte. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

* **Standard Deviation** (Standardabweichung): Die Standardabweichung der Spalte. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

* **Variance** (Varianz): Die Varianz der Spalte. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

* **Skewness** (Schiefe): Die Schiefe der Spalte. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

* **Kurtosis**: Die Kurtosis der Spalte. Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Erweiterte Vorverarbeitung

Bei der Konfiguration Ihrer Experimente können Sie die erweiterte Einstellung `Preprocess` aktivieren. Dies bedeutet, dass die folgenden Schritte der Datenvorverarbeitung und Featurisierung automatisch durchgeführt werden.

|Vorverarbeitungsschritte| BESCHREIBUNG |
| ------------- | ------------- |
|Löschen von Funktionen mit hoher Kardinalität oder ohne Varianz|Löscht diese aus Trainings- und Validierungssets. Dazu gehören Funktionen, denen alle Werte fehlen, die denselben Wert für alle Zeilen haben oder die eine sehr hohe Kardinalität (z.B. Hashwerte, IDs oder GUIDs) aufweisen.|
|Imputieren von fehlenden Werten|Bei numerischen Features werden fehlende Werte mit dem Durchschnitt der Werte in der Spalte imputiert.<br/><br/>Bei kategorischen Features werden fehlende Werte mit dem am häufigsten vorkommenden Wert imputiert.|
|Generieren zusätzlicher Funktionen|Für DateTime-Funktionen: Jahr, Monat, Tag, Tag der Woche, Tag des Jahres, Quartal, Woche des Jahres, Stunde, Minute, Sekunde.<br/><br/>Für Text-Funktionen: Ausdruckshäufigkeit basierend auf Unigrammen, Bigrammen und Trigrammen.|
|Transformieren und codieren |Numerische Features mit wenigen eindeutigen Werten werden in kategorische Features transformiert.<br/><br/>One-Hot-Codierung wird für niedrige Kardinalität kategorisch durchgeführt, für hohe Kardinalität gilt One-Hot-Hashcodierung.|
|Worteinbettungen|Ein Textfeaturizer, der Vektoren von Texttoken mithilfe eines vortrainierten Modells in Satzvektoren konvertiert. Der Einbettungsvektor jedes Worts in einem Dokument wird in einem Dokumentenfeaturevektor zusammengefasst.|
|Zielcodierungen|Bei kategorischen Features wird jede Kategorie mit gemitteltem Zielwert für Regressionsprobleme und der Klassenwahrscheinlichkeit für jede Klasse für Klassifizierungsprobleme zugeordnet. Häufigkeitsbasierte Gewichtung und k-fache Kreuzvalidierung werden angewendet, um die Überanpassung der Zuordnung und das Rauschen durch dünn besetzte Datenkategorien zu verringern.|
|Textzielcodierung|Für die Texteingabe wird ein gestapeltes lineares Modell mit Bag-of-Words verwendet, um die Wahrscheinlichkeit der einzelnen Klassen zu generieren.|
|Gewichtung der Beweise (Weight of Evidence, WoE)|Berechnet den WoE-Wert als ein Maß für die Korrelation von kategorischen Spalten zur Zielspalte. Dieser Wert wird als Logarithmus des Verhältnisses von In-Class- zu Out-of-Class-Wahrscheinlichkeiten berechnet. Dieser Schritt gibt eine numerische Featurespalte pro Klasse aus und erspart die Notwendigkeit, fehlende Werte und Ausreißerbehandlung explizit zu berechnen.|
|Clusterabstand|Trainiert ein K-Means-Clusteringmodell für alle numerischen Spalten.  Gibt k neue Features aus, ein neues numerisches Feature pro Cluster, das den Abstand jeder Stichprobe zum Schwerpunkt jedes Clusters enthält.|

## <a name="run-experiment-and-view-results"></a>Ausführen des Experiments und Anzeigen der Ergebnisse

Klicken Sie auf „Start“, um das Experiment auszuführen. Der das Experiment vorbereitende Prozess nimmt einige Minuten in Anspruch.

### <a name="view-experiment-details"></a>Anzeigen von Details zum Experiment

Sobald die Vorbereitungsphase des Experiments abgeschlossen wurde, wird der Bildschirm „Run Detail“ (Ausführungsdetails) angezeigt. Dieser bietet Ihnen eine vollständige Liste der erstellten Modelle. Standardmäßig steht das Modell, das aufgrund Ihrer Parameter die höchste Bewertung erhält, ganz oben in der Liste. Wenn der Trainingsauftrag weitere Modelle testet, werden diese der Iterationsliste und dem Diagramm hinzugefügt. Verwenden Sie das Iterationsdiagramm, um einen schnellen Vergleich der Metriken für die bisher generierten Modelle zu erhalten.

Bei Trainingsaufträgen kann es eine Weile dauern, bis die Ausführung jeder Pipeline abgeschlossen wurde.

![Dashboard mit den Ausführungsdetails](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Anzeigen der Details der Trainingsausführung

Führen Sie einen Drilldown der Ausgabemodelle aus, um Details zur Trainingsausführung anzuzeigen, z.B. Leistungsmetriken und Verteilungsdiagramme. [Weitere Informationen zu Diagrammen](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Details zur Iteration](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Bereitstellen des Modells

Sobald Ihnen das beste Modell vorliegt, ist es an der Zeit, das Modell als Webdienst bereitzustellen, um Vorhersagen für neue Daten zu erstellen.

Automatisiertes maschinelles Lernen unterstützt Sie dabei, das Modell bereitzustellen, ohne Code zu schreiben:

1. Sie haben einige Optionen für die Bereitstellung. 
    1. Wenn Sie das beste Modell entsprechend den Metrikkriterien bereitstellen möchten, die Sie für das Experiment festgelegt haben, wählen Sie **Deploy Best Model** (Bestes Modell bereitstellen) auf der Seite **Run Detail** (Ausführungsdetails) aus.

        ![Schaltfläche für Bereitstellen eines Modells](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Wenn Sie eine bestimmte Modelliteration bereitstellen möchten, führen Sie einen Drilldown für das Modell aus, um dessen spezielle Seite mit Ausführungsdetails zu öffnen, und wählen Sie **Deploy Model** (Modell bereitstellen) aus.

        ![Schaltfläche für Bereitstellen eines Modells](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Der erste Schritt besteht darin, das Modell im Dienst zu registrieren. Wählen Sie „Register Model“ (Modell registrieren) aus, und warten Sie, bis der Registrierungsprozess abgeschlossen ist.

    ![Blatt „Modell bereitstellen“](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Sobald das Modell registriert ist, können Sie das Bewertungsskript (scoring.py) und das Umgebungsskript (condaEnv.yml) herunterladen, die während der Bereitstellung verwendet werden sollen.

1. Sobald das Bewertungsskript und das Umgebungsskript heruntergeladen sind, wechseln Sie zum Blatt **Assets** (Objekte) im linken Navigationsbereich, und wählen Sie **Models** (Modelle) aus.

    ![„Modelle“ im Navigationsbereich](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Wählen Sie das Modell aus, das Sie registriert haben, und wählen Sie „Create image“ (Image erstellen) aus.

    Sie können das Modell anhand seiner Beschreibung erkennen, die die Ausführungs-ID und die Iterationsnummer im folgenden Format enthält: *<Ausführung_ID>_<Iterationsnummer>_Model*.

    ![Modelle: Image erstellen](media/how-to-create-portal-experiments/model-create-image.png)

1. Geben Sie einen Namen für das Image ein. 
1. Wählen Sie die Schaltfläche **Durchsuchen** neben dem Feld „Bewertungsdatei“ aus, um die Bewertungsdatei (scoring.py) hochzuladen, Sie zuvor heruntergeladen haben.

1. Wählen Sie die Schaltfläche **Durchsuchen** neben dem Feld „Conda-Datei“ aus, um die Umgebungsdatei (condaEnv.yml) hochzuladen, Sie zuvor heruntergeladen haben.

    Sie können Ihr eigenes Bewertungsskript und Ihre eigene Conda-Datei verwenden, und Sie können weitere Dateien hochladen. [Hier finden Sie weitere Informationen über Bewertungsskripts](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script).

      >[!Important]
      > Ein Dateiname muss weniger als 32 Zeichen haben und muss mit einem alphanumerischen Zeichen beginnen und enden. Dazwischen darf ein Name Bindestriche, Unterstriche, Punkte und alphanumerische Zeichen enthalten. Leerzeichen sind nicht zulässig.

    ![Image erstellen](media/how-to-create-portal-experiments/create-image.png)

1. Wählen Sie die Schaltfläche „Erstellen“ aus, um die Imageerstellung zu starten. Dieser Vorgang wird einige Minuten dauern. Sobald er abgeschlossen ist, wird in der oberen Leiste eine Meldung angezeigt.
1. Wechseln Sie zur Registerkarte „Images“, aktivieren Sie das Kontrollkästchen neben dem Image, das Sie bereitstellen möchten, und wählen Sie „Bereitstellung erstellen“ aus. [Hier finden Sie weitere Informationen über Bereitstellungen](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

    Es gibt zwei Optionen für eine Bereitstellung.
     + Azure Container Instances (ACI): Diese Option wird überwiegend für Testzwecke statt für eine einsatzfähige skalierbare Bereitstellung verwendet. Geben Sie unbedingt die Werte für mindestens einen Kern für _CPU-Reservekapazität_ und mindestens einen GB-Wert für _Arbeitsspeicher-Reservekapazität_ ein.
     + Azure Kubernetes Service (AKS): Diese Option ist für eine skalierbare Bereitstellung vorgesehen. Sie benötigen einen vorbereiteten AKS-basierten Compute.

     ![Images: Bereitstellung erstellen](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Wählen Sie abschließend die Option **Erstellen**. Eine Bereitstellung des Modells kann für jede Pipeline einige Minuten beanspruchen, bis die Ausführung abgeschlossen ist.

1. Das ist alles! Sie haben einen einsatzfähigen Webdienst, mit dem Vorhersagen generiert werden können.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu automatisiertem Machine Learning](concept-automated-ml.md) und Azure Machine Learning.
* [Informationen zum Nutzen eines Webdiensts](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)
