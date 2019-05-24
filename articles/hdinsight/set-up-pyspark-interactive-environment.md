---
title: 'Microsoft Azure HDInsight: Einrichten einer interaktiven PySpark-Umgebung für Visual Studio Code'
description: Hier erfahren Sie, wie Sie mithilfe von Azure HDInsight Tools for Visual Studio Code Abfragen und Skripts erstellen und übermitteln.
keywords: VScode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: d7d6bf45a8cdf22fbff0d3fda8175e8007a612d9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789281"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Einrichten einer interaktiven PySpark-Umgebung für Visual Studio Code

Die folgenden Schritte zeigen Ihnen, wie Sie die interaktive PySpark-Umgebung in VS Code einrichten.

Mit dem Befehl **python/pip** wird die virtuelle Umgebung in Ihrem Home-Pfad erstellt. Wenn Sie eine andere Version verwenden möchten, müssen Sie die Standardversion des Befehls **python/pip** manuell ändern. Weitere Informationen finden Sie unter [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installieren Sie [Python](https://www.python.org/downloads/) und [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Installieren Sie Python über [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installieren Sie pip über [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/). (Wenn nicht aus der Python-Installation installiert)
   + Überprüfen Sie mit den folgenden Befehlen, ob Python und pip erfolgreich installiert wurden. (Optional)
 
        ![Python: pip-Version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Sie sollten Python manuell installieren, anstatt die Standardversion für MacOS zu verwenden.


2. Installieren Sie **virtualenv** mit dem folgenden Befehl.
   
   ```
   pip install virtualenv
   ```

3. Installieren Sie nur für Linux die erforderlichen Pakete durch Ausführung der folgenden Befehle, wenn Sie die Fehlermeldung erhalten.
   
    ![Python: pip-Version](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Starten Sie VS Code neu, und navigieren Sie zurück zum Skript-Editor, der **HDInsight: PySpark Interactive** ausführt.

## <a name="next-steps"></a>Nächste Schritte

### <a name="demo"></a>Demo
* HDInsight for VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](hdinsight-for-vscode.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Erstellen und Übermitteln von Apache Spark Scala-Anwendungen](spark/apache-spark-intellij-tool-plugin.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](spark/apache-spark-eclipse-tool-plugin.md)
* [Verwenden der HDInsight-Tools für IntelliJ mit Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter Notebooks in einem Apache Spark-Cluster für HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
