---
title: 'Tutorial: Bereitstellen von ML-Modellen mit dem Designer'
titleSuffix: Azure Machine Learning
description: Erstellen einer Predictive Analytics-Lösung im Azure Machine Learning-Designer Sie erhalten Informationen zum Trainieren, Bewerten und Bereitstellen eines Machine Learning-Modells mithilfe von Drag & Drop-Modulen.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: e93f912915303ce903a32ceba4f079593657a4ac
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576057"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Tutorial: Bereitstellen eines Machine Learning-Modells mit dem Designer


Sie können das im [ersten Teil des Tutorials](tutorial-designer-automobile-price-train-score.md) entwickelte Vorhersagemodell bereitstellen, damit es auch von anderen verwendet werden kann. Im ersten Teil haben Sie Ihr Modell trainiert. Jetzt ist es an der Zeit, Vorhersagen basierend auf Benutzereingaben zu generieren. In diesem Teil des Tutorials führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Echtzeit-Rückschlusspipeline
> * Erstellen eines Rückschlussclusters
> * Bereitstellen des Echtzeitendpunkts
> * Testen des Echtzeitendpunkts

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie den [ersten Teil des Tutorials](tutorial-designer-automobile-price-train-score.md), um zu erfahren, wie Sie im Designer ein Machine Learning-Modell trainieren und bewerten.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Erstellen einer Echtzeit-Rückschlusspipeline

Zum Bereitstellen Ihrer Pipeline müssen Sie zunächst die Trainingspipeline in eine Echtzeit-Rückschlusspipeline konvertieren. Dadurch werden die Trainingsmodule entfernt und Webdiensteingaben und -ausgaben für die Verarbeitung von Anforderungen hinzugefügt.

### <a name="create-a-real-time-inference-pipeline"></a>Erstellen einer Echtzeit-Rückschlusspipeline

1. Wählen Sie über der Pipelinecanvas **Rückschlusspipeline erstellen** > **Echtzeit-Rückschlusspipeline** aus.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Screenshot: Position der Schaltfläche „Pipeline erstellen“":::

    Ihre Pipeline sollte nun wie folgt aussehen: 

   ![Screenshot der erwarteten Pipelinekonfiguration nach Vorbereitung der Bereitstellung](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Wenn Sie **Rückschlusspipeline erstellen** auswählen, passiert Folgendes:
    
    * Das trainierte Modell wird als Modul **Dataset** in der Modulpalette gespeichert. Sie finden es unter **My Datasets** (Meine Datasets).
    * Trainingsmodule wie **Train Model** (Modell trainieren) und **Split Data** (Daten aufteilen) werden entfernt.
    * Das gespeicherte trainierte Modell wird wieder der Pipeline hinzugefügt.
    * Die Module **Web Service Input** (Webdiensteingabe) und **Web Service Output** (Webdienstausgabe) werden hinzugefügt. Diese Module zeigen, wo Benutzerdaten in die Pipeline eingehen und wo sie zurückgegeben werden.

    > [!NOTE]
    > Die **Webdiensteingabe** erwartet das gleiche Datenschema wie die Trainingsdaten, die zum Erstellen der Vorhersagepipeline verwendet wurden. In diesem Szenario ist der Preis im Schema enthalten. Der Preis wird bei der Vorhersage jedoch nicht als Faktor verwendet.
    >

1. Wählen Sie **Übermitteln** aus, und verwenden Sie das gleiche Computeziel und Experiment wie im ersten Teil.

    Falls dies die erstmalige Ausführung ist, kann es bis zu 20 Minuten dauern, bis der Ausführungsvorgang für die Pipeline vollständig abgeschlossen ist. In den Standardcomputeeinstellungen ist eine minimale Knotengröße von 0 festgelegt. Das bedeutet, dass der Designer Ressourcen nach dem Leerlauf zuordnen muss. Wiederholte Pipelineausführungen werden schneller abgeschlossen, da die Computeressourcen bereits zugeordnet sind. Außerdem verwendet der Designer für jedes Modul zwischengespeicherte Ergebnisse, um die Effizienz weiter zu steigern.

1. Klicken Sie auf **Bereitstellen**.

## <a name="create-an-inferencing-cluster"></a>Erstellen eines Rückschlussclusters

Im angezeigten Dialogfeld können Sie auswählen, in welchen vorhandenen AKS-Clustern (Azure Kubernetes Service) Sie Ihr Modell bereitstellen möchten. Sollten Sie über keinen AKS-Cluster verfügen, gehen Sie wie folgt vor, um einen zu erstellen:

1. Wählen Sie im angezeigten Dialogfeld die Option **Compute** aus, um zur Seite **Compute** zu gelangen.

1. Wählen Sie auf dem Navigationsmenüband **Rückschlusscluster** >  **+ Neu** aus.

    ![Screenshot: Navigation zum Bereich für den neuen Rückschlusscluster](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Konfigurieren Sie im Bereich des Rückschlussclusters einen neuen Kubernetes-Dienst.

1. Geben Sie unter **Computename** den Namen *aks-compute* ein.
    
1. Wählen Sie unter **Region** eine verfügbare Region in der Nähe aus.

1. Klicken Sie auf **Erstellen**.

    > [!NOTE]
    > Die Erstellung eines neuen AKS-Diensts dauert etwa 15 Minuten. Der Bereitstellungsstatus kann auf der Seite **Rückschlusscluster** überprüft werden.
    >

## <a name="deploy-the-real-time-endpoint"></a>Bereitstellen des Echtzeitendpunkts

Kehren Sie nach Abschluss der Bereitstellung des AKS-Diensts zur Echtzeit-Rückschlusspipeline zurück, um deren Bereitstellung abzuschließen.

1. Wählen Sie über der Canvas die Option **Bereitstellen** aus.

1. Wählen Sie **Neuen Echtzeitendpunkt bereitstellen** aus. 

1. Wählen Sie den erstellten AKS-Cluster aus.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Screenshot: Einrichtung eines neuen Echtzeit-Endpunkts":::

    Sie können auch die Einstellung **Erweitert** für Ihren Echtzeitendpunkt ändern.
    
    |Einstellung „Erweitert“|BESCHREIBUNG|
    |---|---|
    |Application Insights-Diagnose und -Datensammlung aktivieren| Gibt an, ob Azure Application Ingishts zum Sammeln von Daten von den bereitgestellten Endpunkten aktiviert werden soll. </br> Standardwert: false |
    |Zeitlimit für Bewertung| Ein Timeout in Millisekunden, das für Bewertungsaufrufe an den Webdienst erzwungen werden soll</br>Standardmäßig gilt: 60000|
    |Automatische Skalierung aktiviert|   Gibt an, ob die automatische Skalierung für den Webdienst aktiviert werden soll.</br>Standardwert: true|
    |Mindestanzahl Replikate| Die Mindestanzahl von Containern, die bei der automatischen Skalierung dieses Webdiensts verwendet werden sollen.</br>Standardmäßig gilt: 1|
    |Maximale Anzahl Replikate| Die maximale Anzahl von Containern, die bei der automatischen Skalierung dieses Webdiensts verwendet werden sollen.</br> Standardmäßig gilt: 10|
    |Zielauslastung|Die Zielauslastung (in Prozent), die von der automatischen Skalierung für diesen Webdienst beibehalten werden soll.</br> Standardmäßig gilt: 70|
    |Aktualisierungszeitraum|Gibt an, wie oft (in Sekunden) die automatische Skalierung versucht, diesen Webdienst zu skalieren.</br> Standardmäßig gilt: 1|
    |CPU-Reservekapazität|Die Anzahl der CPU-Kerne, die für diesen Webdienst zuzuordnen sind.</br> Standardmäßig gilt: 0,1|
    |Arbeitsspeicher-Reservekapazität|Der Arbeitsspeicherumfang (in GB), der für diesen Webdienst zugeordnet werden soll.</br> Standardmäßig gilt: 0.5|
        

1. Klicken Sie auf **Bereitstellen**. 

    Nach Abschluss der Bereitstellung wird über der Canvas eine Erfolgsbenachrichtigung angezeigt. Dies kann einige Minuten dauern.

> [!TIP]
> Sie können auch eine Bereitstellung in **Azure Container Instances** (ACI) ausführen, wenn Sie im Feld mit den Einstellungen für den Echtzeitendpunkt unter **Computetyp** die Option **Azure Container Instances** auswählen.
> Azure Container Instances wird für Tests oder für die Entwicklung verwendet. Verwenden Sie ACI für CPU-basierte Workloads im kleinen Maßstab, die weniger als 48 GB Arbeitsspeicher erfordern.

## <a name="test-the-real-time-endpoint"></a>Testen des Echtzeitendpunkts

Nach Abschluss der Bereitstellung können Sie Ihren Echtzeitendpunkt anzeigen, indem Sie zur Seite **Endpunkte** navigieren.

1. Wählen Sie auf der Seite **Endpunkte** den bereitgestellten Endpunkt aus.

    Auf der Registerkarte **Details** werden weitere Informationen angezeigt, z. B. REST-URI, Swagger-Definition, Status und Tags.

    Auf der Registerkarte **Consume** (Nutzen) finden Sie Beispielnutzungscode, Sicherheitsschlüssel und Optionen zum Festlegen der Authentifizierungsmethoden.

    Auf der Registerkarte **Bereitstellungsprotokolle** finden Sie ausführliche Bereitstellungsprotokolle für den Echtzeitendpunkt.

1. Navigieren Sie zum Testen des Endpunkts zur Registerkarte **Test**. Hier können Sie Testdaten eingeben und **Testen** auswählen, um die Ausgabe des Endpunkts zu überprüfen.

Weitere Informationen zur Nutzung Ihres Webdiensts finden Sie unter [Nutzen eines als Webdienst bereitgestellten Modells](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden die wichtigsten Schritte zum Erstellen, Bereitstellen und Verwenden eines Machine Learning-Modells im Designer vermittelt. Weitere Informationen zur Verwendung des Designers finden Sie unter den folgenden Links:

+ [Designer-Beispiele](samples-designer.md): Erfahren Sie, wie Sie den Designer für andere Arten von Aufgaben verwenden.
+ [Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md)