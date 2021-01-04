---
title: 'Tutorial: Anomalieerkennung mit Cognitive Services'
description: Tutorial zur Verwendung von Cognitive Services zur Anomalieerkennung in Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463920"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Tutorial: Anomalieerkennung mit Cognitive Services (Vorschau)

In diesem Tutorial erfahren Sie, wie Sie Ihre Daten in Azure Synapse mit [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492) problemlos anreichern können. Wir führen eine [Anomalieerkennung](https://go.microsoft.com/fwlink/?linkid=2147493) mithilfe des entsprechenden Diensts durch. Ein Benutzer in Azure Synapse kann einfach eine Tabelle auswählen, um sie für die Erkennung von Anomalien anzureichern.

In diesem Lernprogramm wird Folgendes behandelt:

> [!div class="checklist"]
> - Schritte zum Abrufen eines Spark-Tabellendatasets mit Zeitreihendaten
> - Verwenden eines Assistenten in Azure Synapse, um Daten mithilfe des Cognitive Service für Anomalieerkennung zu anzureichern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Als Standardspeicher konfigurierter [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem ADLS Gen2-Speicherkonto. Sie müssen **Mitwirkender an Storage-Blobdaten** des ADLS Gen2-Dateisystems sein, mit dem Sie arbeiten.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Bevor Sie dieses Tutorial verwenden können, müssen Sie auch die in diesem Tutorial beschriebenen Schritte vor der Konfiguration ausführen. [Konfigurieren von Cognitive Services in Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Erstellen einer Spark-Tabelle

Für dieses Tutorial benötigen Sie eine Spark-Tabelle.

1. Laden Sie die folgende Notebook-Datei mit Code zum Generieren einer Spark-Tabelle herunter: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Laden Sie die Datei in ihren Azure Synapse-Arbeitsbereich hoch.
![Hochladen des Notebooks](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Öffnen Sie die Notebook-Datei, und wählen Sie für die Zellen **Alle ausführen** aus.
![Spark-Tabelle erstellen](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. In der Spark-Standarddatenbank sollte nun eine Spark-Tabelle mit dem Namen **anomaly_detector_testing_data** angezeigt werden.

## <a name="launch-cognitive-services-wizard"></a>Starten des Cognitive Services-Assistenten

1. Klicken Sie mit der rechten Maustaste auf die im vorherigen Schritt erstellte Spark-Tabelle. Wählen Sie „Machine Learning“ -> „Mit vorhandenem Modell anreichern“ aus.
![Starten des Bewertungsassistenten](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Ein Konfigurationsbereich wird angezeigt, und Sie werden aufgefordert, ein Cognitive Services-Modell auszuwählen. Wählen Sie „Anomalieerkennung“ aus.

![Starten des Bewertungsassistenten – Schritt 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Angeben von Authentifizierungsdetails

Für die Authentifizierung bei Cognitive Services müssen Sie auf das Geheimnis verweisen, das in Ihrer Key Vault-Instanz verwendet werden soll. Die folgenden Eingaben sind abhängig von [erforderlichen Schritten](tutorial-configure-cognitive-services-synapse.md), die Sie vor diesem Schritt abgeschlossen haben sollten.

- **Azure-Abonnement**: Wählen Sie das Abonnement aus, zu dem Ihre Key Vault-Instanz gehört.
- **Cognitive Services-Konto**: Dies ist die Textanalyse-Ressource, mit der Sie eine Verbindung herstellen möchten.
- **Mit Azure Key Vault verknüpfter Dienst**: Im Rahmen der erforderlichen Schritte haben Sie einen verknüpften Dienst für Ihre Textanalyse Ressource erstellt. Wählen Sie ihn hier aus.
- **Geheimnisname**: Dies ist der Name des Geheimnisses in Ihrem Schlüsseltresor, der den Schlüssel zum Authentifizieren bei ihrer Cognitive Services-Ressource enthält.

![Angeben von Azure Key Vault-Details](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Konfigurieren der Anomalieerkennung

Als nächstes müssen Sie die Anomalieerkennung konfigurieren. Geben Sie die folgenden Details an:

- Granularität: Die Rate, mit der die Daten abgetastet werden. Wenn Ihre Daten beispielsweise jede Minute einen Wert liefern, entspricht dies einer minütlichen Granularität. Wählen Sie **monatlich** aus. 

- Timestamp: Diese Spalte gibt die Uhrzeit für die Reihe an. Wählen Sie die Spalte **Zeitstempel** aus.

- Zeitreihenwert: Diese Spalte gibt den Wert der Reihe zu dem Zeitpunkt an, der in der Spalte „Zeitstempel“ angegeben ist. Wählen Sie die Spalte **Wert** aus.

- Gruppierung: Diese Spalte dient der Gruppierung der Reihe. Dies bedeutet, dass alle Zeilen mit gleichem Wert in dieser Spalte eine Zeitreihe bilden. Wählen Sie die Spalte **Gruppe** aus.

Wenn Sie fertig sind, wählen Sie **Notebook öffnen** aus. Dadurch wird ein Notebook für Sie mit PySpark-Code generiert, der die Anomalieerkennung mithilfe von Azure Cognitive Services ausführt.

![Konfigurieren der Anomalieerkennung](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Öffnen und Ausführen des Notebooks

Das soeben geöffnete Notebook verwendet die [mmlspark-Bibliothek](https://github.com/Azure/mmlspark), um eine Verbindung mit Cognitive Services herzustellen.

Mit den von Ihnen bereitgestellten Azure Key Vault-Details können Sie auf sichere Weise auf Ihre Geheimnisse verweisen, ohne sie offenzulegen.

Sie können jetzt **Alle Zellen ausführen**, um die Anomalieerkennung auszuführen. Weitere Informationen zu [Cognitive Services – Anomalieerkennung](https://go.microsoft.com/fwlink/?linkid=2147493)

![Ausführen der Anomalieerkennung](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Stimmungsanalyse mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Assistent für die Modellbewertung in dedizierten SQL-Pools von Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)