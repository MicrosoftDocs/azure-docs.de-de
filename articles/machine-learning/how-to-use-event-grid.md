---
title: Auslöserereignisse in ML-Workflows (Vorschau)
titleSuffix: Azure Machine Learning
description: Richten Sie in Azure Machine Learning ereignisgesteuerte Anwendungen, Prozesse oder CI/CD-Machine Learning-Workflows ein.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 1fd177273c9dafb04add64d8a8bfef1d81cc65d0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319324"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Auslösen von Anwendungen, Prozessen oder CI/CD-Workflows basierend auf Azure Machine Learning-Ereignissen (Vorschau)

In diesem Artikel erfahren Sie, wie Sie ereignisgesteuerte Anwendungen, Prozesse oder CI/CD-Workflows basierend auf Azure Machine Learning-Ereignissen (z. B. E-Mails mit Fehlerbenachrichtigungen oder Ausführungen der ML-Pipeline) einrichten, wenn von [Azure Event Grid](../event-grid/index.yml) bestimmte Bedingungen erkannt werden.

Azure Machine Learning verwaltet den gesamten Lebenszyklus von Prozessen für maschinelles Lernen, wozu Modelltraining, Modellimplementierung und Überwachung gehören. Sie können Event Grid zum Reagieren auf Azure Machine Learning-Ereignisse, z. B. das Abschließen von Trainingsausführungen, das Registrieren und Bereitstellen von Modellen und das Erkennen von Datendrift, mithilfe moderner serverloser Architekturen verwenden. Anschließend können Sie Ereignisse wie einen geänderten Ausführungsstatus, den Abschluss einer Ausführung, Modellregistrierungen, Modellimplementierungen und Datendrifterkennungen in einem Arbeitsbereich abonnieren und verarbeiten.

Szenarien für die Verwendung von Event Grid für ereignisgesteuerte Aktionen:
* Senden von E-Mails nach fehlerhafter und abgeschlossener Ausführung
* Verwenden einer Azure-Funktion nach dem Registrieren eines Modells
* Streaming von Ereignissen aus Azure Machine Learning an verschiedene Endpunkte
* Auslösen einer ML-Pipeline bei Drifterkennung

> [!NOTE] 
> Zurzeit werden runStatusChanged-Ereignisse nur beim Status **Fehler** ausgelöst.

## <a name="prerequisites"></a>Voraussetzungen
Für die Verwendung von Event Grid benötigen Sie Mitwirkender- oder Besitzerzugriff auf den Azure Machine Learning-Arbeitsbereich, für den Sie Ereignisse erstellen.

## <a name="the-event-model--types"></a>Das Ereignismodell und die Ereignistypen

Azure Event Grid liest Ereignisse aus Quellen, z. B. Azure Machine Learning und andere Azure-Dienste. Diese Ereignisse werden dann an Ereignishandler wie Azure Event Hubs, Azure Functions, Logic Apps und weitere gesendet. In der folgenden ist Abbildung dargestellt, wie Event Grid Quellen und Handler verbindet, diese Darstellung ist jedoch keine vollständige Liste der unterstützten Integrationen.

![Azure Event Grid – funktionales Modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Weitere Informationen zu Ereignisquellen und Ereignishandlern finden Sie unter [Was ist Event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Ereignistypen für Azure Machine Learning

Azure Machine Learning stellt Ereignisse an den verschiedenen Punkten eines Lebenszyklus für maschinelles Lernen bereit: 

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Wird ausgelöst, wenn eine Ausführung eines Experiments für maschinelles Lernen abgeschlossen ist |
| `Microsoft.MachineLearningServices.ModelRegistered` | Wird ausgelöst, wenn ein Machine Learning-Modell im Arbeitsbereich registriert wird |
| `Microsoft.MachineLearningServices.ModelDeployed` | Wird ausgelöst, wenn eine Bereitstellung eines Rückschlussdiensts mit mindestens einem Modell abgeschlossen ist |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Wird ausgelöst, wenn ein Datendrifterkennungsauftrag für zwei Datasets abgeschlossen ist |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Wird ausgelöst, wenn sich der Ausführungsstatus geändert hat. Wird derzeit nur ausgelöst, wenn der Ausführungsstatus „Fehler“ lautet. |

### <a name="filter--subscribe-to-events"></a>Filtern und Abonnieren von Ereignissen

Diese Ereignisse werden über Azure Event Grid veröffentlicht. Über das Azure-Portal, PowerShell oder Azure CLI können Kunden problemlos Ereignisse abonnieren, [indem sie mindestens einen Ereignistyp sowie Filterbedingungen angeben](../event-grid/event-filtering.md). 

Beim Einrichten von Ereignissen können Sie Filter anwenden, damit nur bei bestimmten Ereignisdaten eine Auslösung erfolgt. Im nachstehenden Beispiel können Sie Ereignisse bei geändertem Ausführungsstatus nach Ausführungstypen filtern. Das Ereignis wird nur ausgelöst, wenn die Kriterien erfüllt sind. Weitere Informationen zu Ereignisdaten, nach denen Sie filtern können, finden Sie im [Event Grid-Schema für Azure Machine Learning](../event-grid/event-schema-machine-learning.md). 

Abonnements für Azure Machine Learning-Ereignisse werden durch rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) geschützt. Nur ein [Mitwirkender oder Besitzer](how-to-assign-roles.md#default-roles) eines Arbeitsbereichs kann Ereignisabonnements erstellen, aktualisieren und löschen.  Auf Ereignisabonnements können während der [Erstellung](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true) des Ereignisabonnements oder zu einem späteren Zeitpunkt Filter angewendet werden. 


1. Wechseln Sie zum Azure-Portal, und wählen Sie ein neues oder ein vorhandenes Abonnement aus. 

1. Wählen Sie die Registerkarte „Filter“ aus, und scrollen Sie zu „Erweiterte Filter“. Geben Sie unter **Schlüssel** und **Wert** die Eigenschaftstypen an, nach denen Sie filtern möchten. Hier können Sie sehen, dass das Ereignis nur ausgelöst wird, wenn der Ausführungstyp eine Pipelineausführung oder eine Pipeline-Schrittausführung ist.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtern von Ereignissen":::


+ **Filtern nach Ereignistyp** : In einem Ereignisabonnement können ein oder mehrere Azure Machine Learning-Ereignistypen angegeben sein.

+ **Filtern nach Ereignisgegenstand** : Azure Event Grid unterstützt Gegenstandsfilter auf Grundlage von __beginnt mit__ - und __endet mit__ -Übereinstimmungen, sodass Ereignisse mit einem übereinstimmenden Gegenstand an den Abonnenten übermittelt werden. Unterschiedliche Machine Learning-Ereignisse haben unterschiedliche Gegenstandsformate.

  | Ereignistyp | Gegenstandsformat | Beispielgegenstand |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Erweiterte Filterung** : Azure Event Grid unterstützt auch erweitertes Filtern anhand eines veröffentlichten Ereignisschemas. Ausführliche Informationen zum Azure Machine Learning-Ereignisschema finden Sie unter [Azure Event Grid-Ereignisschema für Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Einige Beispiele für erweitertes Filtern, das Sie ausführen können, sind:

  Für das `Microsoft.MachineLearningServices.ModelRegistered`-Ereignis Filtern des Tagwerts eines Modells:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Weitere Informationen zum Anwenden von Filtern finden Sie unter [Filtern von Ereignissen für Event Grid](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Nutzen von Machine Learning-Ereignissen

Anwendungen, die Machine Learning-Ereignisse behandeln, sollten einigen empfohlene Vorgehensweisen entsprechen:

> [!div class="checklist"]
> * Da mehrere Abonnements zum Weiterleiten von Ereignissen an den gleichen Ereignishandler konfiguriert werden können, ist es wichtig, nicht davon auszugehen, dass Ereignisse von einer bestimmten Quelle stammen, sondern das Thema der Nachricht zu überprüfen, um sicherzugehen, dass es aus dem erwarteten Machine Learning-Arbeitsbereich stammt.
> * Überprüfen Sie auf ähnliche Weise, ob Sie auf die Verarbeitung des eventType vorbereitet sind, und gehen Sie nicht davon aus, dass alle Ereignisse, die Sie empfangen, den von Ihnen erwarteten Typen entsprechen.
> * Da Nachrichten in falscher Reihenfolge und mit Verzögerung eintreffen können, verwenden Sie die etag-Felder, um zu verstehen, ob Ihre Informationen zu Objekten weiterhin auf dem neuesten Stand ist.  Verwenden Sie auch die sequencer-Felder, um die Reihenfolge der Ereignisse für ein bestimmtes Objekt zu verstehen.
> * Ignorieren Sie Felder, die Sie nicht verstehen. So müssen Sie sich nicht mit neuen Features auseinandersetzen, die in der Zukunft hinzugefügt werden könnten.
> * Fehlgeschlagene oder abgebrochene Azure Machine Learning-Vorgänge lösen kein Ereignis aus. Wenn beispielsweise bei einer Modellimplementierung ein Fehler auftritt, wird Microsoft.MachineLearningServices.ModelDeployed nicht ausgelöst. Berücksichtigen Sie solche Fehlermodi, wenn Sie Ihre Anwendungen entwerfen. Sie können jederzeit über das Azure Machine Learning SDK, die CLI oder das Portal den Status eines Vorgangs überprüfen und die detaillierten Fehlerursachen ermitteln.

Mit Azure Event Grid können Kunden entkoppelte Meldungshandler erstellen, die durch Azure Machine Learning-Ereignisse ausgelöst werden können. Einige relevante Beispiele für Meldungshandler sind:
* Azure-Funktionen
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory-Pipeline
* Generische Webhooks, die auf der Azure-Plattform oder an anderer Stelle gehostet werden

## <a name="set-up-in-azure-portal"></a>Einrichtung im Azure-Portal

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zu Ihrem Azure Machine Learning-Arbeitsbereich.

1. Wählen Sie in der linken Leiste die Option __Ereignisse__ aus, und wählen Sie dann **Ereignisabonnements** aus. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Wählen Sie den Typ des zu nutzenden Ereignisses aus. Im folgenden Screenshot sind beispielsweise __Modell registriert__ , __Model bereitgestellt__ , __Ausführung abgeschlossen__ und __Datasetdrift erkannt__ aktiviert:

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Wählen Sie den Endpunkt aus, für den das Ereignis veröffentlicht werden soll. Im folgenden Screenshot ist __Event Hub__ der ausgewählte Endpunkt:

    ![Screenshot: Bereich „Ereignisabonnement erstellen“ mit geöffnetem Fenster „Event Hub-Instanz auswählen“](./media/how-to-use-event-grid/select-event-handler.png)

Nachdem Sie Ihre Auswahl bestätigt haben, klicken Sie auf __Erstellen__. Nach der Konfiguration werden diese Ereignisse per Pushvorgang an ihren Endpunkt übermittelt.


### <a name="set-up-with-the-cli"></a>Einrichtung mit der CLI

Sie können entweder die neueste [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) installieren oder die Azure Cloud Shell verwenden, die als Bestandteil Ihres Azure-Abonnements bereitgestellt wird.

Um die Event Grid-Erweiterung zu installieren, verwenden Sie den folgenden Befehl in der CLI (Command-Line Interface, Befehlszeilenschnittstelle):

```azurecli-interactive
az add extension --name eventgrid
```

Im folgenden Beispiel wird veranschaulicht, wie Sie ein Azure-Abonnement auswählen und dann ein neues Ereignisabonnement für Azure Machine Learning erstellen:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Beispiele

### <a name="example-send-email-alerts"></a>Beispiel: Senden von E-Mail-Benachrichtigungen

Verwenden Sie [Azure Logic Apps](../logic-apps/index.yml), um E-Mails für Ihre Ereignisse zu konfigurieren. Passen Sie diese mit Bedingungen an, und geben Sie Empfänger an, um die Zusammenarbeit und Beachtung zwischen Teams zu ermöglichen, die zusammenarbeiten.

1. Wechseln Sie im Azure-Portal zu Ihrem Azure Machine Learning-Arbeitsbereich, und wählen Sie in der linken Leiste die Registerkarte „Ereignisse“ aus. Wählen Sie dort die Option __Logik-Apps__ aus. 

    ![Screenshot: Seite „Ereignisse“ für Machine Learning-Arbeitsbereich mit Option „Logik-Apps“](./media/how-to-use-event-grid/select-logic-ap.png)

1. Melden Sie sich bei der Benutzeroberfläche für Logik-Apps an, und wählen Sie „MachineLearningServices“ als Thementyp aus. 

    ![Screenshot: Dialogfeld „When a resource event occurs“ (Bei Auftreten eines Ressourcenereignisses), bei dem Machine Learning als Ressourcentyp ausgewählt ist](./media/how-to-use-event-grid/select-topic-type.png)

1. Wählen Sie die Ereignisse aus, für die eine Benachrichtigung erfolgen soll. Im folgenden Screenshot ist beispielsweise __RunCompleted__ ausgewählt.

    ![Screenshot: Dialogfeld „When a resource event occurs“ (Bei Auftreten eines Ressourcenereignisses) mit ausgewähltem Ereignistyp](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Sie können die Filtermethode im Abschnitt oben verwenden oder Filter hinzufügen, um die Logik-App nur für eine Teilmenge von Ereignistypen auszulösen. Im folgenden Screenshot wird ein __Präfixfilter__ von __/datadriftID/runs/__ verwendet.

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Fügen Sie nun einen Schritt hinzu, um dieses Ereignis zu nutzen und nach E-Mails zu suchen. Es gibt mehrere unterschiedliche E-Mail-Konten, die Sie zum Empfangen von Ereignissen verwenden können. Außerdem können Sie Bedingungen dazu festlegen, wann eine E-Mail-Benachrichtigung gesendet werden soll.

    ![Screenshot: Dialogfeld „Aktion auswählen“ mit „E-Mail“ im Suchfeld](./media/how-to-use-event-grid/select-email-action.png)

1. Wählen Sie __E-Mail senden__ aus, und geben Sie Werte für die Parameter ein. In den Betreff können Sie den __Ereignistyp__ und das __Thema__ einfügen, um das Filtern von Ereignissen zu vereinfachen. Sie können auch in den Nachrichtentext einen Link zur Arbeitsbereichsseite für Ausführungen einfügen. 

    ![Screenshot: Dialogfeld „E-Mail senden“, bei dem „Thema“ und „Ereignistyp“ aus der Liste rechts in der Betreffzeile hinzugefügt wurden](./media/how-to-use-event-grid/configure-email-body.png)

1. Um diese Aktion zu speichern, wählen Sie **Speichern unter** in der linken Ecke der Seite aus. Bestätigen Sie in der rechten Leiste, die angezeigt wird, die Erstellung dieser Aktion.

    ![Screenshot: Schaltflächen „Speichern unter“ und „Erstellen“ im Designer für Logik-Apps](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Beispiel: Erneutes Trainieren von Datendriftauslösern

Modelle werden mit der Zeit zu veralteten Modellen, sodass sie in dem Kontext, in dem sie ausgeführt werden, nicht länger nützlich sind. Eine Möglichkeit zum Feststellen, ob es an der Zeit ist, das Modell erneut zu trainieren, ist das Erkennen von Datendrift. 

In diesem Beispiel wird gezeigt, wie Event Grid mit einer Azure-Logik-App verwendet werden kann, um ein erneute Trainieren auszulösen. Im Beispiel wird eine Azure Data Factory-Pipeline ausgelöst, wenn Datendrift zwischen dem Trainings- und dem Nutzungsdataset eines Modells auftritt.

Führen Sie zur Vorbereitung zunächst die folgenden Aktionen aus:

* Einrichten einer Datasetüberwachung zum [Erkennen von Datendrift](how-to-monitor-datasets.md) in einem Arbeitsbereich
* Erstellen einer veröffentlichten [Azure Data Factory-Pipeline](../data-factory/index.yml)

In diesem Beispiel wird eine einfache Data Factory-Pipeline verwendet, um Dateien in einen Blobspeicher zu kopieren und eine veröffentlichte Machine Learning-Pipeline auszuführen. Weitere Informationen zu diesem Szenario finden Sie unter [Ausführen von Azure Machine Learning Service-Pipelines in Azure Data Factory-Pipelines](../data-factory/transform-data-machine-learning-service.md).

![Screenshot: Pipeline „Training Pipeline“ in „Factory Resources“ (Factory-Ressourcen), bei der „Copy data1“ Daten an „ML Execute Pipeline1“ sendet](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Beginnen Sie mit dem Erstellen der Logik-App. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), suchen Sie nach „Logik-Apps“, und wählen Sie „Erstellen“ aus.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Geben Sie die erforderlichen Informationen ein. Um die Umgebung zu vereinfachen, verwenden Sie dasselbe Abonnement und dieselbe Ressourcengruppe wie Ihre Azure Data Factory-Pipeline und Ihr Azure Machine Learning-Arbeitsbereich.

    ![Screenshot: Bereich „Erstellen“ für Logik-Apps](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Nachdem Sie die Logik-App erstellt haben, wählen Sie __Bei Auftreten eines Event Grid-Ressourcenereignisses__ aus. 

    ![Screenshot: Designer für Logik-Apps mit Optionen für „Starten Sie mit einem gängigen Trigger“ einschließlich „Bei Auftreten eines Event Grid-Ressourcenereignisses“](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Melden Sie sich an, und geben Sie die Details für das Ereignis ein. Legen Sie __Ressourcenname__ auf den Namen des Arbeitsbereichs fest. Legen Sie __Ereignistyp__ auf __DatasetDriftDetected__ fest.

    ![Screenshot: Dialogfeld „When a resource event occurs“ (Bei Auftreten eines Ressourcenereignisses) mit ausgewähltem Ereignistypelement](./media/how-to-use-event-grid/login-and-add-event.png)

1. Fügen Sie einen neuen Schritt hinzu, und suchen Sie nach __Azure Data Factory__. Wählen Sie __Pipelineausführung erstellen__ aus. 

    ![Screenshot: Bereich „Aktion auswählen“ mit ausgewählter Option „Create a pipeline run“ (Pipelineausführung erstellen)](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Melden Sie sich an, und geben die veröffentlichte Azure Data Factory-Pipeline an, die ausgeführt werden soll.

    ![Screenshot: Bereich „Create a pipeline run“ (Pipelineausführung erstellen) mit verschiedenen Werten](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Speichern und erstellen Sie die Logik-App über die **Speichern** -Schaltfläche oben links auf der Seite. Um Ihre App anzuzeigen, wechseln Sie in Ihrem Arbeitsbereich in das [Azure-Portal](https://portal.azure.com), und klicken Sie auf **Ereignisse**.

    ![Screenshot: Ereignisse mit hervorgehobener Logik-App](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Ab jetzt wird die Data Factory-Pipeline ausgelöst, wenn eine Datendrift auftritt. Die Details zu Ihrer Datendriftausführung und Machine Learning-Pipeline werden im [neuen Arbeitsbereichsportal](https://ml.azure.com) angezeigt. 

![Screenshot: Pipelineendpunkte](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Beispiel: Bereitstellen eines Modells auf der Grundlage von Tags

Ein Azure Machine Learning-Modellobjekt enthält Parameter, über die Sie Bereitstellungen abwickeln können, so z. B. Modellname, Version, Tag und Eigenschaft. Das Modellregistrierungsereignis kann einen Endpunkt auslösen, und Sie können eine Azure-Funktion verwenden, um ein Modell entsprechend den Werten dieser Parameter bereitzustellen.

Ein Beispiel finden Sie im Repository unter [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid). Führen Sie die Schritte aus, die in der **README** -Datei angegeben sind.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid, und probieren Sie Azure Machine Learning-Ereignisse aus:

- [Einführung in Azure Event Grid](../event-grid/overview.md)

- [Ereignisschema für Azure Machine Learning](../event-grid/event-schema-machine-learning.md)