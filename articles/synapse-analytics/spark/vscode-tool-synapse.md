---
title: 'Tutorial: Spark- und Hive-Tools für VS Code (Spark-Anwendung)'
description: In diesem Tutorial erfahren Sie, wie Sie Spark- und Hive-Tools für VS Code verwenden, um in Python geschriebene Spark-Anwendungen zu entwickeln und an einen serverlosen Apache Spark-Pool zu übermitteln.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: eb4a4c2c8d1d52690a07b784640d20d96ff2d600
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445673"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Tutorial: Erstellen einer Apache Spark-Anwendung mit VS Code mithilfe eines Synapse-Arbeitsbereichs

Erfahren Sie, wie Sie Apache Spark- und Hive-Tools für Visual Studio Code verwenden. Verwenden Sie die Tools zum Erstellen und Übermitteln von Batchaufträgen für Apache Hive, interaktiven Hive-Abfragen und PySpark-Skripts für Apache Spark. Zunächst wird beschrieben, wie Sie Spark- und Hive-Tools in Visual Studio Code installieren. Anschließend werden die Schritte zum Übermitteln von Aufträgen an Spark- und Hive-Tools erläutert.

Spark- und Hive-Tools können auf allen von Visual Studio Code unterstützten Plattformen installiert werden. Die folgenden Voraussetzungen gelten für die verschiedenen Plattformen.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Elemente sind zum Ausführen der Schritte in diesem Artikel erforderlich:

- Serverloser Apache Spark-Pool. Informationen zum Erstellen eines serverlosen Apache Spark-Pools finden Sie unter [Schnellstart: Erstellen eines neuen serverlosen Apache Spark-Pools über das Azure-Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono wird nur für Linux und macOS benötigt.
- [Eine interaktive PySpark-Umgebung für Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Ein lokales Verzeichnis. In diesem Artikel wird **C:\HD\Synaseexample** verwendet.

## <a name="install-spark--hive-tools"></a>Installieren von Spark & Hive Tools

Wenn die Voraussetzungen erfüllt sind, können Sie Spark- und Hive-Tools für Visual Studio Code installieren, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie Visual Studio Code.

2. Navigieren Sie über die Menüleiste zu **Ansicht** > **Erweiterungen**.

3. Geben Sie **Spark & Hive** in das Suchfeld ein.

4. Wählen Sie in den Suchergebnissen **Spark & Hive Tools** aus, und wählen Sie dann **Installieren** aus:

     ![Installation der Python-Erweiterung für Spark- und Hive-Tools für Visual Studio Code](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Wählen Sie bei Bedarf **Erneut laden** aus.

## <a name="open-a-work-folder"></a>Öffnen eines Arbeitsordners

Um einen Arbeitsordner zu öffnen und eine Datei in Visual Studio Code zu erstellen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie in der Menüleiste zu **Datei** > **Ordner öffnen...**  > **C:\HD\Synaseexample**, und klicken Sie dann auf die Schaltfläche **Ordner auswählen**. Daraufhin wird der Ordner links in der Ansicht **Explorer** angezeigt.

2. Wählen Sie in der **Explorer**-Ansicht den Ordner **Synaseexample** aus, und wählen Sie dann neben dem Arbeitsordner das Symbol **Neue Datei** aus:

     ![Visual Studio Code: Symbol „Neue Datei“](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Geben Sie der neuen Datei einen Namen mit der Erweiterung `.py` (Spark-Skript). In diesem Beispiel lautet der Name der Datei **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Herstellen einer Verbindung mit Ihren Spark-Pools

Melden Sie sich beim Azure-Abonnement an, um eine Verbindung mit Ihren Spark-Pools herzustellen.

### <a name="sign-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

Führen Sie diese Schritte aus, um eine Verbindung mit Azure herzustellen:

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette...** , und geben Sie **Azure: Anmelden** ein:

     ![Spark- und Hive-Tools für Visual Studio Code: Anmeldung](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Befolgen Sie die Anleitung für die Anmeldung, um sich bei Azure anzumelden. Nach dem Herstellen der Verbindung wird Ihr Azure-Kontoname unten im Visual Studio Code-Fenster in der Statusleiste angezeigt.

## <a name="set-the-default-spark-pool"></a>Festlegen des Spark-Standardpools

1. Öffnen Sie erneut den Ordner **Synaseexample**, der [weiter oben](#open-a-work-folder) beschrieben wurde (sofern er geschlossen ist).  

2. Wählen Sie die Datei **HelloWorld.py** aus, die [zuvor](#open-a-work-folder) bereits erstellt wurde. Sie wird im Skript-Editor geöffnet.

3. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann **Synapse: Set default Spark pool** (Spark-Standardpool festlegen) aus.  

4. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-your-spark-pools), sofern noch keine Verbindung besteht.

5. Wählen Sie einen Spark-Pool als Spark-Standardpool für die aktuelle Skriptdatei aus. Die Tools aktualisieren die Konfigurationsdatei **.VSCode\settings.json** automatisch:

     ![Standardclusterkonfiguration festlegen](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Übermitteln von interaktiven Synapse PySpark-Abfragen an den Spark-Pool

Benutzer können interaktive Synapse PySpark-Abfragen im Spark-Pool wie folgt durchführen:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Verwenden des interaktiven Synapse PySpark-Befehls in einer PY-Datei
Gehen Sie wie folgt vor, um die Abfragen mithilfe des interaktiven PySpark-Befehls zu übermitteln:

1. Öffnen Sie erneut den Ordner **Synaseexample**, der [weiter oben](#open-a-work-folder) beschrieben wurde (sofern er geschlossen ist).  

2. Erstellen Sie mit den [zuvor](#open-a-work-folder) erläuterten Schritten eine neue **HelloWorld.py**-Datei.

3. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Skriptdatei ein:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. Die Aufforderung, den PySpark/Synapse PySpark-Kernel zu installieren, wird unten rechts im Fenster angezeigt. Sie können auf **Install** (Installieren) klicken, um mit der PySpark/Synapse PySpark-Installation fortzufahren, oder auf **Skip** (Überspringen), um diesen Schritt auszulassen.

     ![PySpark-Kernel installieren](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Wenn Sie den Kernel später installieren müssen, können Sie zu **Datei** > **Einstellung** > **Einstellungen** navigieren und **HDInsight: Enable Skip PySpark Installation** (HDInsight: Überspringen der PySpark-Installation aktivieren) in den Einstellungen deaktivieren. 
    
     ![Aktivieren des Überspringens der PySpark-Installation](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Wenn die Installation in Schritt 4 erfolgreich war, wird das Nachrichtenfeld „PySpark/Synapse PySpark installed successfully“ (PySpark/Synapse PySpark erfolgreich installiert) unten rechts im Fenster angezeigt. Klicken Sie auf **Erneut laden**, um das Fenster neu zu laden.

     ![PySpark erfolgreich installiert](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Navigieren Sie in der Menüleiste zu **View** > **Command Palette...** (Ansicht > Befehlspalette...), oder verwenden Sie die Tastenkombination **UMSCHALT+STRG+P**, und geben Sie **Python: Select Interpreter to start Jupyter Server** (Interpreter auswählen, um Jupyter-Server zu starten) ein.

     ![Select Interpreter to start Jupyter Server (Interpreter auswählen, um Jupyter-Server zu starten)](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Wählen Sie unten die Python-Option aus.

     ![Auswählen der unten angegebenen Option](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Navigieren Sie in der Menüleiste zu **View** > **Command Palette...** (Ansicht > Befehlspalette...), oder verwenden Sie die Tastenkombination **UMSCHALT+STRG+P**, und geben Sie **Developer: Fenster neu laden** ein.

     ![Fenster neu laden](./media/vscode-tool-synapse/reload-window.png)

10. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-your-spark-pools), sofern noch keine Verbindung besteht.

11. Wählen Sie den gesamten Code aus, klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **Synapse: PySpark Interactive** aus, um die Abfrage zu übermitteln. 

     ![PySpark Interactive-Kontextmenü](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Wählen Sie den Spark-Pool aus, wenn Sie keinen Spark-Standardpool angegeben haben. Nach kurzer Zeit werden die **Python Interactive**-Ergebnisse in einer neuen Registerkarte angezeigt. Klicken Sie auf PySpark, um den Kernel auf **Synapse PySpark** umzustellen. Wenn Sie den ausgewählten Code dann erneut übermitteln, ist die Ausführung erfolgreich. Die Tools unterstützen es auch, dass Sie über das Kontextmenü einen Codeblock anstelle der gesamten Skriptdatei übermitteln:

     ![Interaktiv](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Ausführen einer interaktiven Abfrage in der PY-Datei mithilfe des Kommentars #%%

1. Fügen Sie vor dem Code **#%%** hinzu, um eine Notebookumgebung zu erhalten.

     ![#%% hinzugefügt](./media/vscode-tool-synapse/run-cell.png)

2. Klicken Sie auf **Zelle ausführen**. Nach kurzer Zeit werden die Python Interactive-Ergebnisse auf einer neuen Registerkarte angezeigt. Klicken Sie auf PySpark, um den Kernel auf **Synapse PySpark** umzustellen. Wenn Sie anschließend erneut auf **Zelle ausführen** klicken, ist die Ausführung erfolgreich. 

     ![Ergebnisse von „Zelle ausführen“](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Nutzen der IPYNB-Unterstützung über die Python-Erweiterung

1. Sie können eine Jupyter Notebook-Instanz per Befehl über die Befehlspalette oder durch Erstellen einer neuen IPYNB-Datei in Ihrem Arbeitsbereich erstellen. Weitere Informationen finden Sie unter [Arbeiten mit Jupyter Notebook in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

2. Klicken Sie auf **Zelle ausführen**, folgen Sie den Aufforderungen bis zu **Set the default spark pool** (Spark-Standardpool festlegen) (das Festlegen des Standardclusters/-pools vor dem Öffnen eines Notebooks ist dringend zu empfehlen), und wählen Sie dann **Neu laden** aus, um das Fenster neu zu laden.

     ![Festlegen des Spark-Standardpools und erneutes Laden](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Klicken Sie auf PySpark, um den Kernel auf **Synapse PySpark** umzustellen, und klicken Sie dann auf **Zelle ausführen**. Nach einer Weile wird das Ergebnis angezeigt.

     ![Ausführen von IPYNB-Ergebnissen](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. Version 2020.5.78807 und höher von „ms-python“ wird von dieser Erweiterung nicht unterstützt. Dies ist ein [bekanntes Problem](#known-issues).
>  
>2. Wechseln Sie zum Synapse PySpark-Kernel. Wir empfehlen Ihnen, die automatischen Einstellungen im Azure-Portal zu deaktivieren. Andernfalls kann es sehr lange dauern, den Cluster zu aktivieren und den Synapse-Kernel für die erstmalige Verwendung festzulegen. 
>
>    ![Automatische Einstellungen](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Übermitteln eines PySpark-Batchauftrags an den Spark-Pool

1. Öffnen Sie erneut den Ordner **Synaseexample**, der [weiter oben](#open-a-work-folder) beschrieben wurde (sofern er geschlossen ist).  

2. Erstellen Sie mit den [zuvor](#open-a-work-folder) erläuterten Schritten eine neue **BatchFile.py**-Datei.

3. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Skriptdatei ein:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-your-spark-pools), sofern noch keine Verbindung besteht.

5. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann **Synapse: PySpark-Batch** aus.

6. Wählen Sie einen Spark-Pool aus, an den Ihr PySpark-Auftrag übermittelt werden soll:

     ![Ergebnisausgabe für die Übermittlung des Python-Auftrags](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Nachdem Sie einen Batchauftrag an den Spark-Pool übermittelt haben, werden in Visual Studio Code im **Ausgabefenster** Übermittlungsprotokolle angezeigt. Die URL für die **Spark-Benutzeroberfläche** und die URL für die **Benutzeroberfläche der Spark-Auftragsanwendung** werden ebenfalls angezeigt. Sie können die URL in einem Webbrowser öffnen, um den Status des Auftrags zu verfolgen.

## <a name="access-and-manage-synapse-workspace"></a>Zugreifen auf und Verwalten von Synapse-Arbeitsbereichen

Im Azure-Explorer in den Spark- und Hive-Tools für VS Code können verschiedene Vorgänge durchgeführt werden. Über den Azure-Explorer:

![Azure-Abbildung](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Starten des Arbeitsbereichs

1. Navigieren Sie im Azure-Explorer zu **SYNAPSE**, erweitern Sie die Option, und zeigen Sie die Liste mit den Synapse-Abonnements an.

     ![Synapse-Explorer](./media/vscode-tool-synapse/synapse-explorer.png)

2. Klicken Sie auf das Abonnement des Synapse-Arbeitsbereichs, erweitern Sie es, und zeigen Sie die Liste mit den Arbeitsbereichen an.

3. Klicken Sie mit der rechten Maustaste auf einen Arbeitsbereich, und wählen Sie die Option **View Apache Spark applications** (Apache Spark-Anwendungen anzeigen) aus. Die Apache Spark-Anwendungsseite auf der Synapse Studio-Website wird geöffnet.

     ![Rechtsklick auf Arbeitsbereich](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Synapse Studio-Anwendung](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Erweitern Sie einen Arbeitsbereich. Die Optionen **Standardspeicher** und **Spark-Pools** werden angezeigt.

5. Klicken Sie mit der rechten Maustaste auf **Standardspeicher**. Die Optionen **Vollständigen Pfad kopieren** und **Open in Synapse Studio** (In Synapse Studio öffnen) werden angezeigt. 

     ![Rechtsklick auf Standardspeicher](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Klicken Sie auf **Vollständigen Pfad kopieren**. Die URL für das primäre ADLS Gen2-Konto wird kopiert, und Sie können sie dann an einem beliebigen Ort einfügen.

     - Klicken Sie auf **Open in Synapse Studio** (In Synapse Studio öffnen). Das primäre Speicherkonto wird in Synapse Studio geöffnet.

     ![Standardspeicher in Synapse Studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Erweitern Sie den **Standardspeicher**. Das primäre Speicherkonto wird angezeigt.

7. Erweitern Sie die Option **Spark-Pools**. Alle Spark-Pools des Arbeitsbereichs werden angezeigt.

     ![Erweitern des Speicherpools](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Bekannte Probleme

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>Version 2020.5.78807 und höher von „ms-python“ wird von dieser Erweiterung nicht unterstützt 

„Failed to connect to Jupyter notebook.“ (Fehler beim Verbinden mit Jupyter Notebook.) ist ein bekanntes Problem in Python-Version 2020.5.78807 und höher. Es wird empfohlen, dass Benutzer Version **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** von „ms-python“ verwenden, um dieses Problem zu vermeiden.

![Bekannte Probleme](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](../overview-what-is.md)
- [Erstellen eines neuen Apache Spark-Pools für einen Azure Synapse Analytics-Arbeitsbereich](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
