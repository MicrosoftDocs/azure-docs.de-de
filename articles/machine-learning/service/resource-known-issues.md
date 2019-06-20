---
title: Bekannte Probleme und Problembehandlung
titleSuffix: Azure Machine Learning service
description: Hier finden Sie eine Liste mit bekannten Problemen, Problemumgehungen und Problembehandlungsinformationen für Azure Machine Learning Service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 80bb7af0f7ed20336ab08d4f3ca9639057b9c67f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149757"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Bekannte Probleme und Problembehandlung für Azure Machine Learning Service

Dieser Artikel hilft Ihnen, Fehler oder Ausfälle zu ermitteln und zu beheben, die beim Verwenden von Azure Machine Learning Service auftreten können.

## <a name="visual-interface-issues"></a>Probleme mit der grafischen Benutzeroberfläche

Probleme mit der grafischen Benutzeroberfläche für Machine Learning Service

### <a name="long-compute-preparation-time"></a>Lange Compute-Vorbereitungszeit

Das Erstellen eines neuen Computevorgangs oder das Beenden eines Computevorgangs nimmt einige Minuten oder sogar länger in Anspruch. Das Team arbeitet an der Optimierung.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Ein Experiment, das nur ein Dataset enthält, kann nicht ausgeführt werden 

Möglicherweise möchten Sie ein Experiment ausführen, das nur ein Dataset enthält, um das Dataset zu visualisieren. Derzeit ist das Ausführen eines Experiments, das nur ein Dataset enthält, jedoch nicht zulässig. Wir arbeiten aktiv an der Behebung dieses Problems.
 
Als Zwischenlösung können Sie das Dataset mit einem beliebigen Datentransformationsmodul verbinden (Auswählen von Spalten im Dataset, Bearbeiten von Metadaten, Teilen von Daten usw.) und das Experiment ausführen. Anschließend können Sie das Dataset visualisieren. 

Die folgende Abbildung zeigt die Vorgehensweise: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK-Installationsprobleme

**Fehlermeldung: 'PyYAML' kann nicht deinstalliert werden.**

Azure Machine Learning SDK für Python: Bei PyYAML handelt es sich um ein mit distutils installiertes Projekt. Daher können wir bei einer teilweisen Deinstallation nicht genau bestimmen, welche Dateien zu ihm gehören. Verwenden Sie diesen Befehl, um die Installation des SDKs fortzusetzen und diesen Fehler zu ignorieren:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem beim Erstellen von Azure Machine Learning Compute

In seltenen Fällen kann es vorkommen, dass Benutzer, die vor dem allgemein verfügbaren Release ihren Azure Machine Learning-Arbeitsbereich über das Azure-Portal erstellt haben, Azure Machine Learning Compute nicht in diesem Arbeitsbereich erstellen können. In einem solchen Fall können Sie entweder eine Supportanfrage für den Dienst erstellen oder über das Portal oder das SDK einen neuen Arbeitsbereich erstellen und die Blockierung dadurch umgehend aufheben.

## <a name="image-building-failure"></a>Fehler bei der Image-Erstellung

Fehler bei der Image-Erstellung beim Bereitstellen des Webdiensts. Die Problemumgehung besteht darin „pynacl==1.2.1“ als pip-Anhängigkeit zur Conda-Datei für die Imagekonfiguration hinzuzufügen.

## <a name="deployment-failure"></a>Fehler bei der Bereitstellung

Wenn `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>` angezeigt wird, ändern Sie die SKU für virtuelle Computer in Ihrer Bereitstellung in eine Variante mit mehr Arbeitsspeicher.

## <a name="fpgas"></a>FPGAs

Um Modelle auf FPGAs bereitzustellen, müssen Sie zuerst ein FPGA-Kontingent anfordern und dessen Genehmigung abwarten. Füllen Sie das Formular zur Kontingentanforderung aus, um Zugriff anzufordern: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatisiertes maschinelles Lernen

Das automatisierte maschinelle Lernen von Tensor Flow unterstützt derzeit nicht die Tensor Flow Version 1.13. Die Installation dieser Version führt dazu, dass Paketabhängigkeiten nicht mehr funktionieren. Wir arbeiten daran, dieses Problem in einer zukünftigen Version zu beheben. 

### <a name="experiment-charts"></a>Diagramm mit Experimenten

Seit dem 12. April werden Diagramme für die binäre Klassifizierung (Genauigkeit und Trefferquote, ROC, Gewinnkurve usw.), die in automatisierten ML-Experimentiterationen angezeigt werden, auf der Benutzeroberfläche nicht richtig gerendert. In Diagrammen werden derzeit invertierte Ergebnisse angezeigt, was dazu führt, dass Modelle mit besserer Leistung mit schlechteren Ergebnissen angezeigt werden. Derzeit wird an einer Lösung gearbeitet.

## <a name="databricks"></a>Databricks

Probleme mit Databricks und Azure Machine Learning:

### <a name="failure-when-installing-packages"></a>Fehler beim Installieren von Paketen

Bei der Installation des Azure Machine Learning SDK tritt in Azure Databricks ein Fehler auf, wenn mehrere Pakete installiert werden. Einige Pakete, z.B. `psutil`, können Konflikte verursachen. Um Fehler bei der Installation zu vermeiden, frieren Sie die Bibliotheksversion beim Installieren der Pakete ein. Dieses Problem hängt mit Databricks und nicht mit dem Azure Machine Learning Service SDK zusammen. Es kann auch mit anderen Bibliotheken auftreten. Beispiel:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Falls bei Python-Bibliotheken immer wieder Installationsprobleme auftreten, können Sie alternativ Initialisierungsskripts verwenden. Dieser Ansatz wird nicht offiziell unterstützt. Weitere Informationen finden Sie unter [Initialisierungsskripts im Clusterbereich](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Abbrechen einer automatisierten Ausführung des maschinellen Lernens

Wenn Sie Funktionen für automatisiertes maschinelles Lernen in Azure Databricks verwenden um eine Ausführung abzubrechen und eine neue Experimentausführung zu starten, starten Sie Ihren Azure Databricks-Cluster neu.

### <a name="10-iterations-for-automated-machine-learning"></a>Mehr als 10 Iterationen von automatisiertem maschinellem Lernen

Sofern in den Einstellungen für automatisiertes maschinelles Lernen mehr als 10 Iterationen vorgesehen sind, legen Sie `show_output` auf `False` fest, wenn Sie die Ausführung übermitteln.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget für das Azure Machine Learning SDK/automatisiertes maschinelles Lernen

Das Azure Machine Learning SDK-Widget wird in Databricks-Notebooks nicht unterstützt, da die Notebooks keine HTML-Widgets analysieren können. Sie können das Widget im Portal anzeigen, indem Sie diesen Python-Code in die Zelle Ihres Azure Databricks-Notebooks einfügen:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importfehler: Kein Modul mit dem Namen „pandas.core.indexes“

Wenn diese Fehlermeldung bei Verwendung von automatisiertem maschinellem Lernen angezeigt wird, gehen Sie folgendermaßen vor:

1. Führen Sie diesen Befehl aus, um zwei Pakete in Ihrem Azure Databricks-Cluster zu installieren: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Trennen Sie den Cluster auf, und fügen Sie ihn dann wieder an Ihr Notebook an. 

Wenn diese Schritte das Problem nicht beheben, versuchen Sie, den Cluster neu zu starten.

## <a name="azure-portal"></a>Azure-Portal

Wenn Sie Ihren Arbeitsbereich direkt über einen Freigabelink aus dem SDK oder dem Portal anzeigen, können Sie die normale Übersichtsseite mit Abonnementinformationen in der Erweiterung nicht anzeigen. Außerdem können Sie nicht zu einem anderen Arbeitsbereich wechseln. Wenn Sie einen anderen Arbeitsbereich anzeigen möchten, müssen Sie direkt zum [Azure-Portal](https://portal.azure.com) navigieren und nach dem Namen des Arbeitsbereichs suchen.

## <a name="diagnostic-logs"></a>Diagnoseprotokolle

Manchmal kann es hilfreich sein, Diagnoseinformationen bereitstellen zu können, wenn Sie um Hilfe bitten. Um einige Protokolle anzuzeigen, besuchen Sie das [Azure-Portal](https://portal.azure.com), wechseln Sie zu Ihrem Arbeitsbereich, und wählen Sie **Arbeitsbereich > Experiment > Ausführen > Protokolle** aus.

## <a name="resource-quotas"></a>Ressourcenkontingente

Erfahren Sie mehr über die [Ressourcenkontingente](how-to-manage-quotas.md), die Ihnen im Rahmen von Azure Machine Learning begegnen können.

## <a name="authentication-errors"></a>Authentifizierungsfehler

Wenn Sie einen Verwaltungsvorgang innerhalb eines Remoteauftrags auf ein Computeziel anwenden, erhalten Sie eine der folgenden Fehlermeldungen:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Sie erhalten beispielsweise eine Fehlermeldung, wenn Sie versuchen, ein Computeziel anhand einer ML-Pipeline zu erstellen oder anzuhängen, die zur Remoteausführung übermittelt wird.
