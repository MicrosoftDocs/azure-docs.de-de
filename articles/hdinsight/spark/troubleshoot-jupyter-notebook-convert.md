---
title: Jupyter Notebook kann in Azure HDInsight nicht erstellt werden
description: Hier erhalten Sie Informationen über Problembehandlungsschritte und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 41eefd18419969c6e8c54ba68ce6c0d1eeb7832b
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519194"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Jupyter Notebook kann in Azure HDInsight nicht erstellt werden

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Wenn Sie ein Jupyter Notebook starten, erhalten Sie eine Fehlermeldung, die Folgendes enthält:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Ursache

Ein Versionskonflikt.

## <a name="resolution"></a>Lösung

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öffnen Sie `_version.py`, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Ändern Sie **5** in **4**, sodass die geänderte Zeile wie folgt aussieht:

    ```python
    version_info = (4, 0, 3)
    ```

    Speichern Sie die Änderungen durch Eingabe von **STRG+X**, **Y**, **EINGABETASTE**.

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Starten Sie den Jupyter-Dienst neu.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support**. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.
