---
title: 'Tutorial: Bedarfsvorhersage und AutoML'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie ein Nachfragevorhersagemodell mit automatisiertem maschinellen Lernen in Azure Machine Learning Studio trainieren und bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 07/10/2020
ms.custom: automl
ms.openlocfilehash: 8b354abb98c56a572badf2421b0d7dbbd25f7a63
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921853"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Tutorial: Vorhersage des Bedarfs mithilfe von automatisiertem maschinellem Lernen


In diesem Tutorial wird automatisiertes maschinelles Lernen (automatisiertes ML) in Azure Machine Learning Studio verwendet, um ein Zeitreihenvorhersagemodell zur Vorhersage der Mietnachfrage für einen Bike-Sharing-Dienst zu erstellen.

Ein Beispiel für ein Klassifizierungsmodell finden Sie in [Tutorial: Erstellen eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen in Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Laden eines Datasets
> * Konfigurieren und Ausführen eines Experiments mit automatisiertem maschinellem Lernen
> * Angeben der Vorhersageeinstellungen
> * Untersuchen der Ergebnisse des Experiments
> * Bereitstellen des besten Modells

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md). 

* Laden Sie die Datendatei [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) herunter.

## <a name="get-started-in-azure-machine-learning-studio"></a>Erste Schritte in Azure Machine Learning Studio

In diesem Tutorial wird ein Experiment mit automatisiertem maschinellem Lernen in Azure Machine Learning Studio erstellt. Bei Azure Machine Learning Studio handelt es sich um eine konsolidierte Webumgebung mit ML-Tools für Data Science-Szenarien, die von Data Scientists jeglicher Qualifikation verwendet werden kann. Das Azure Machine Learning-Studio wird in Internet Explorer-Browsern nicht unterstützt.

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an.

1. Wählen Sie Ihr Abonnement und den erstellten Arbeitsbereich aus.

1. Wählen Sie **Erste Schritte** aus.

1. Wählen Sie im linken Bereich im Abschnitt **Ersteller** die Option **Automatisiertes maschinelles Lernen** aus.

1. Wählen Sie **+ Neue Ausführung von automatisiertem ML** aus. 

## <a name="create-and-load-dataset"></a>Erstellen und Laden des Datasets

Laden Sie vor dem Konfigurieren Ihres Experiments Ihre Datendatei in Form eines Azure Machine Learning-Datasets in Ihren Arbeitsbereich hoch. Dadurch wird die ordnungsgemäße Formatierung der Daten für Ihr Experiment sichergestellt.

1. Wählen Sie im Formular **Dataset auswählen** in der Dropdownliste **+ Dataset erstellen** die Option **Aus lokalen Dateien** auswählen aus. 

    1. Geben Sie Ihrem Dataset im Formular **Grundlegende Informationen** einen Namen, und geben Sie optional eine Beschreibung an. Der Datasettyp sollte standardmäßig auf **Tabellarisch** festgelegt sein, da für das automatisierte maschinelle Lernen in Azure Machine Learning Studio derzeit nur tabellarische Datasets unterstützt werden.
    
    1. Wählen Sie links unten die Option **Weiter** aus.

    1. Wählen Sie im Formular **Datenspeicher- und Dateiauswahl** den Standarddatenspeicher aus, der im Zuge der Erstellung Ihres Arbeitsbereichs automatisch eingerichtet wurde: **workspaceblobstore (Azure Blob Storage)** . Hierbei handelt es sich um den Speicherort, in den Sie Ihre Datendatei hochladen. 

    1. Wählen Sie **Durchsuchen** aus. 
    
    1. Wählen Sie auf Ihrem lokalen Computer die Datei **bike-no.csv** aus. Dies ist die Datei, die Sie als [Voraussetzung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) heruntergeladen haben.

    1. Wählen Sie **Weiter** aus.

       Wenn der Upload abgeschlossen ist, wird das Formular „Einstellungen und Vorschau“ basierend auf dem Dateityp vorab aufgefüllt. 
       
    1. Überprüfen Sie, ob das Formular **Einstellungen und Vorschau** wie folgt ausgefüllt ist, und klicken Sie auf **Weiter**.
        
        Feld|BESCHREIBUNG| Wert für das Tutorial
        ---|---|---
        Dateiformat|Definiert das Layout und den Typ der in einer Datei gespeicherten Daten.| Durch Trennzeichen getrennt
        Trennzeichen|Mindestens ein&nbsp;Zeichen zum Angeben der Grenze zwischen separaten, unabhängigen Regionen in Nur-Text-Datenströmen oder anderen Datenströmen. |Komma
        Codieren|Gibt an, welche Bit-zu-Zeichen-Schematabelle verwendet werden soll, um Ihr Dataset zu lesen.| UTF-8
        Spaltenüberschriften| Gibt an, wie die Header des Datasets, sofern vorhanden, behandelt werden.| Header aus der ersten Datei verwenden
        Zeilen überspringen | Gibt an, wie viele Zeilen im Dataset übersprungen werden.| Keine

    1. Das Formular **Schema** ermöglicht eine weitere Konfiguration der Daten für dieses Experiment. 
    
        1. Wählen Sie für dieses Beispiel aus, dass die Spalten **casual** und **registered** ignoriert werden sollen. Diese Spalten werden nicht eingeschlossen, da es sich bei ihnen um eine Aufschlüsselung der Spalte **cnt** handelt.

        1. Übernehmen Sie für dieses Beispiel außerdem die Standardwerte für **Eigenschaften** und **Typ**. 
        
        1. Wählen Sie **Weiter** aus.

    1. Überprüfen Sie im Formular **Details bestätigen** die zuvor in die Formulare **Grundlegende Infos** und **Einstellungen und Vorschau** eingetragenen Informationen.

    1. Wählen Sie **Erstellen** aus, um die Erstellung Ihres Datasets abzuschließen.

    1. Wählen Sie Ihr Dataset aus, sobald es in der Liste angezeigt wird.

    1. Klicken Sie auf **Weiter**.

## <a name="configure-experiment-run"></a>Konfigurieren der Experimentausführung

Nachdem Sie Ihre Daten geladen und konfiguriert haben, müssen Sie das Remotecomputeziel einrichten und auswählen, für welche Spalte in Ihren Daten Vorhersagen erstellt werden sollen.

1. Füllen Sie das Formular **Ausführung konfigurieren** wie folgt aus:
    1. Geben Sie einen Experimentnamen ein: `automl-bikeshare`

    1. Wählen Sie **cnt** als Zielspalte aus. (Hierbei handelt es sich um die Spalte, für die Vorhersagen erstellt werden sollen.) Diese Spalte gibt die Gesamtanzahl von Bike-Sharing-Vermietungen an.

    1. Wählen Sie **Create a new compute** (Neuen Compute erstellen) aus, und konfigurieren Sie Ihr Computeziel. Von automatisiertem maschinellem Lernen wird nur Azure Machine Learning-Compute unterstützt. 

        Feld | BESCHREIBUNG | Wert für das Tutorial
        ----|---|---
        Computename |Ein eindeutiger Name, der Ihren Computekontext identifiziert.|bike-compute
        VM-Typ&nbsp;&nbsp;|Wählen Sie den VM-Typ für Ihre Compute-Umgebung aus.|CPU (Zentralprozessor)
        Größe des virtuellen&nbsp;Computers&nbsp;| Wählen Sie die Größe für Ihren Computes aus.|Standard_DS12_V2
        Min/Max nodes (Min./Max. Knoten)| Um ein Datenprofil zu erstellen, müssen Sie mindestens einen Knoten angeben.|Min. Knoten: 1<br>Max. Knoten: 6
        Leerlauf in Sekunden vor dem Herunterskalieren | Leerlaufzeit vor dem automatischen Herunterskalieren des Clusters auf die minimale Knotenanzahl|120 (Standardwert)
        Erweiterte Einstellungen | Einstellungen zum Konfigurieren und Autorisieren eines virtuellen Netzwerks für Ihr Experiment| Keine
  
        1. Wählen Sie **Erstellen** aus, um das Computeziel abzurufen. 

            **Dieser Vorgang nimmt einige Minuten in Anspruch.** 

        1. Wählen Sie nach der Erstellung in der Dropdownliste Ihr neues Computeziel aus.

    1. Wählen Sie **Weiter** aus.

## <a name="select-forecast-settings"></a>Auswählen von Vorhersageeinstellungen

Schließen Sie die Einrichtung Ihres Experiments mit automatisiertem maschinellem Lernen ab, indem Sie den ML-Aufgabentyp und die Konfigurationseinstellungen angeben.

1. Wählen Sie im Formular **Vorhersagetask auswählen** den ML-Aufgabentyp **Time series forecasting** (Zeitreihenvorhersage) aus.

1. Wählen Sie **date** als **Zeitspalte** aus, und lassen Sie **Time series identifiers** (Zeitreihenbezeichner) leer. 

1. **Forecast horizon** (Vorhersagehorizont) ist die Zeitspanne für die Zukunftsprognose.  Deaktivieren Sie „Automatische Erkennung“, und geben Sie „14“ in das Feld ein. 

1. Klicken Sie auf **Zusätzliche Konfigurationseinstellungen anzeigen**, und füllen Sie die Felder wie folgt aus. Mit diesen Einstellungen können Sie den Trainingsauftrag besser steuern und Einstellungen für die Vorhersage angeben. Andernfalls werden die Standardwerte auf Basis der Experimentauswahl und -daten angewendet.

    Zusätzliche&nbsp;Konfigurationen|BESCHREIBUNG|Wert&nbsp;für&nbsp;das Tutorial
    ------|---------|---
    Primary metric (Primäre Metrik)| Auswertungsmetrik, die zur Messung des Machine Learning-Algorithmus verwendet wird.|Wurzel der mittleren Fehlerquadratsumme (RMSE), normalisiert
    Explain best model (Bestes Modell erläutern)| Zeigt automatisch die Erklärbarkeit für das beste Modell an, das durch automatisiertes ML erstellt wurde.| Aktivieren
    Blockierte Algorithmen | Algorithmen, die Sie aus den Trainingsauftrag ausschließen möchten.| „Extreme Random Trees“ (Extreme Zufallsstrukturen)
    Zusätzliche Vorhersageeinstellungen| Diese Einstellungen tragen dazu bei, die Genauigkeit des Modells zu verbessern. <br><br> _**Zielverzögerungen für Prognose:**_ Angabe, wie weit die Verzögerungen für die Zielvariable zurückreichen sollen <br> _**Zielgröße für rollierendes Zeitfenster**_: Angabe der Größe des rollierenden Zeitfensters, in dem Features wie *max, min* und *sum* generiert werden sollen | <br><br>Zielverzögerungen&nbsp;für&nbsp;Prognose: Keine <br> Zielgröße&nbsp;für&nbsp;rollierendes&nbsp;Zeitfenster: Keine
    Beendigungskriterium| Wenn ein Kriterium erfüllt ist, wird der Trainingsauftrag angehalten. |Dauer&nbsp;des&nbsp;Trainingsauftrags (Stunden): 3 <br> Metrikschwellenwert&nbsp;&nbsp;: Keine
    Überprüfen | Wählen Sie einen Kreuzvalidierungstyp und die Anzahl von Tests aus.|Überprüfungstyp:<br>&nbsp;k-fold&nbsp;cross-validation <br> <br> Anzahl von Überprüfungen: 5
    Parallelität| Die maximale Anzahl paralleler Iterationen pro Iteration| Max.&nbsp;parallele&nbsp;Iterationen: 6
    
    Wählen Sie **Speichern** aus.

## <a name="run-experiment"></a>Ausführen des Experiments

Wählen Sie **Fertig stellen** aus, um Ihr Experiment auszuführen. Im daraufhin geöffneten Bildschirm **Ausführungsdetails** wird oben neben der Ausführungsnummer der **Ausführungsstatus** angezeigt. Dieser Status wird während des Experimentausführung entsprechend aktualisiert.

>[!IMPORTANT]
> Die Vorbereitung des Experiments nimmt **10 –15 Minuten** in Anspruch.
> Sobald es ausgeführt wird, dauert **jede Iteration mindestens zwei bis drei Minuten**.  <br> <br>
> In einer Produktionsumgebung würden Sie sich an dieser Stelle wahrscheinlich anderen Dingen zuwenden, da der Prozess eine Weile dauert. Während Sie warten, können Sie sich schon mal mit den getesteten Algorithmen auf der Registerkarte **Modelle** vertraut machen. 

##  <a name="explore-models"></a>Untersuchen von Modellen

Navigieren Sie zur Registerkarte **Modelle**, um die getesteten Algorithmen (Modelle) anzuzeigen. Standardmäßig werden die Modelle nach ihrem Abschluss nach der Metrikbewertung sortiert. In diesem Tutorial befindet sich das Modell, das für die ausgewählte Metrik **Wurzel der mittleren Fehlerquadratsumme (RMSE), normalisiert** die höchste Bewertung erhält, ganz oben auf der Liste.

Während Sie auf den Abschluss aller Experimentmodelle warten, können Sie den **Algorithmusnamen** eines abgeschlossenen Modells auswählen und sich die zugehörigen Leistungsdetails ansehen. 

Im folgenden Beispiel werden die Registerkarten **Details** und **Metriken** durchlaufen, um die Eigenschaften, Metriken und Leistungsdiagramme des ausgewählten Modells anzuzeigen. 

![Ausführungsdetails](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Durch die Verwendung von automatisiertem maschinellen Lernen im Azure Machine Learning-Studio können Sie das beste Modell in wenigen Schritten als Webdienst bereitstellen. Bei der Bereitstellung handelt es sich um die Integration des Modells, sodass neue Daten vorhergesagt und potenzielle Verkaufschancen identifiziert werden können. 

Bei diesem Experiment bedeutet die Bereitstellung für einen Webdienst, dass das Bike-Sharing-Unternehmen nun über eine iterative und skalierbare Webanwendung für die Vorhersage der Mietnachfrage im Bike-Sharing-Bereich verfügt. 

Navigieren Sie nach Abschluss der Ausführung zurück zur Seite mit der übergeordneten Ausführung, indem Sie oben auf dem Bildschirm **Ausführung 1** auswählen.

Im Abschnitt **Zusammenfassung des besten Modells** wird **StackEnsemble** basierend auf der Metrik **Wurzel der mittleren Fehlerquadratsumme (RMSE), normalisiert** als das beste Modell im Kontext dieses Experiments betrachtet.  

Wir stellen dieses Modell bereit. Die Bereitstellung dauert jedoch etwa 20 Minuten. Der Bereitstellungsprozess umfasst mehrere Schritte, einschließlich der Registrierung des Modells, der Erstellung von Ressourcen und der Konfiguration dieser Ressourcen für den Webdienst.

1. Wählen Sie **StackEnsemble** aus, um die modellspezifische Seite zu öffnen.

1. Wählen Sie die Schaltfläche **Bereitstellen** aus, die sich im oberen linken Bereich des Bildschirms befindet.

1. Füllen Sie den Bereich **Modell bereitstellen** wie folgt aus:

    Feld| Wert
    ----|----
    „Deployment name“ (Bereitstellungsname)| bikeshare-deploy
    „Deployment description“ (Bereitstellungsbeschreibung)| bike share demand deployment
    Computetyp | Azure-Compute-Instanz (ACI) auswählen
    Authentifizierung aktivieren| Deaktivieren Sie diese Option. 
    Use custom deployment assets (Benutzerdefinierte Bereitstellungsressourcen verwenden)| Deaktivieren Sie diese Option. Dies ermöglicht die automatische Erstellung der Standardtreiberdatei (Bewertungsskript) und der Umgebungsdatei. 
    
    In diesem Beispiel werden die im Menü *Erweitert* angegebenen Standardwerte verwendet. 

1. Klicken Sie auf **Bereitstellen**.  

    Am oberen Rand des Bildschirms **Ausführung** wird eine grüne Erfolgsmeldung mit dem Hinweis angezeigt, dass die Bereitstellung erfolgreich gestartet wurde. Der Status der Bereitstellung wird unter **Bereitstellungsstatus** im Bereich **Modellzusammenfassung** angezeigt.
    
Nach erfolgreichem Abschluss der Bereitstellung verfügen Sie über einen funktionierenden Webdienst zum Generieren von Vorhersagen. 

Fahren Sie mit den [**nächsten Schritten**](#next-steps) fort, um weitere Informationen zur Verwendung Ihres neuen Webdiensts zu erhalten, und testen Sie Ihre Vorhersagen mithilfe der integrierten Azure Machine Learning-Unterstützung von Power BI.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereitstellungsdateien sind größer als Daten- und Experimentdateien, sodass ihre Speicherung teurer ist. Löschen Sie nur die Bereitstellungsdateien, um die Kosten für Ihr Konto zu minimieren, oder wenn Sie den Arbeitsbereich und die Experimentdateien beibehalten möchten. Löschen Sie andernfalls die gesamte Ressourcengruppe, wenn Sie keine der Dateien verwenden möchten.  

### <a name="delete-the-deployment-instance"></a>Löschen der Bereitstellungsinstanz

Löschen Sie nur die Bereitstellungsinstanz aus dem Azure Machine Learning-Studio, wenn Sie die Ressourcengruppe und den Arbeitsbereich für andere Tutorials und Untersuchungen beibehalten möchten. 

1. Wechseln Sie zum [Azure Machine Learning-Studio](https://ml.azure.com/). Navigieren Sie zu Ihrem Arbeitsbereich, und wählen Sie links unter dem Bereich **Ressourcen** die Option **Endpunkte** aus. 

1. Wählen Sie die zu löschende Bereitstellung aus, und klicken Sie auf **Delete** (Löschen). 

1. Wählen Sie **Proceed** (Fortfahren) aus.

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie automatisiertes ML in Azure Machine Learning Studio verwendet, um ein Zeitreihenvorhersagemodell zur Vorhersage der Mietnachfrage im Bike-Sharing-Bereich zu erstellen und bereitzustellen. 

Im folgenden Artikel erfahren Sie, wie Sie ein von Power BI unterstütztes Schema erstellen, um die Nutzung Ihres neu bereitstellten Webdiensts zu vereinfachen:

> [!div class="nextstepaction"]
> [Verwenden eines Webdiensts](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Weitere Informationen zu [automatisiertem Machine Learning](concept-automated-ml.md).
+ Weitere Informationen zu Klassifizierungsmetriken und Diagrammen finden Sie im Artikel [Grundlegendes zu den Ergebnissen des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md).
+ Weitere Informationen zur [Featurebereitstellung](how-to-configure-auto-features.md#featurization)
+ Weitere Informationen zur [Datenprofilerstellung](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Dieses Bike-Sharing-Dataset wurde für das Tutorial angepasst. Dieses Dataset wurde im Rahmen eines [Kaggle-Wettbewerbs](https://www.kaggle.com/c/bike-sharing-demand/data) zur Verfügung gestellt und war ursprünglich über [Capital Bikeshare](https://www.capitalbikeshare.com/system-data) verfügbar. Es ist auch in der [Machine Learning-Datenbank von UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset) enthalten.<br><br>
> Quelle: Fanaee-T, Hadi und Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): Seiten 1–15, Springer Berlin Heidelberg.
