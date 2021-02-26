---
title: Unterstützte Datenplattformen
titleSuffix: Azure Data Science Virtual Machine
description: Erfahren Sie mehr über die unterstützten Datenplattformen und Tools für Azure Data Science Virtual Machine.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519540"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Unterstützte Datenplattformen für Data Science Virtual Machine

Mit einer Data Science Virtual Machine (DSVM) können Sie Analysen für ein breites Spektrum von Datenplattformen erstellen. Neben Schnittstellen für Remotedatenplattformen bietet die DSVM eine lokale Instanz für eine schnelle Entwicklung und Prototypenerstellung.

Folgende Datenplattformen werden von der DSVM unterstützt.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Category | Wert |
| ------------- | ------------- |
| Was ist das?   | Eine lokale Instanz einer relationalen Datenbank      |
| Unterstützte DSVM-Editionen      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typische Verwendung      | <ul><li>Schnelle lokale Entwicklung mit einem kleineren Dataset</li><li>Ausführen von R (datenbankintern)</li></ul> |
| Links zu Beispielen      | <ul><li>In die SQL-Datenbank wird ein kleiner Teil des New York City-Datasets geladen:<br/>  `nyctaxi`</li><li>Ein Jupyter-Beispiel zur Veranschaulichung von Microsoft Machine Learning Server und datenbankinternen Analysen finden Sie hier:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Verwandte Tools auf der DSVM       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC-Treiber</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> Die Developer-Edition von SQL Server kann nur für Entwicklungs- und Testzwecke verwendet werden. Für die Verwendung in einer Produktionsumgebung benötigen Sie eine Lizenz oder einen der virtuellen SQL Server-Computer.


### <a name="setup"></a>Einrichten

Der Datenbankserver ist bereits vorkonfiguriert, und für die Windows-Dienste für SQL Server (etwa `SQL Server (MSSQLSERVER)`) ist automatische Ausführung festgelegt. Der einzige manuelle Schritt besteht darin, datenbankinterne Analysen mithilfe von Microsoft Machine Learning Server zu aktivieren. Sie können die Analyse aktivieren, indem Sie den folgenden Befehl als einmalige Aktion in SQL Server Management Studio (SSMS) ausführen. Führen Sie diesen Befehl aus, nachdem Sie sich als Computeradministrator angemeldet haben, öffnen Sie eine neue Abfrage in SSMS, und stellen Sie sicher, dass die ausgewählte Datenbank `master` ist:

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Ersetzen Sie „%COMPUTERNAME%“ durch den Namen Ihres virtuellen Computers.)

Zum Ausführen von SQL Server Management Studio können Sie in der Programmliste oder über die Windows-Suche nach „SQL Server Management Studio“ suchen und das Programm ausführen. Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, wählen Sie **Windows-Authentifizierung** aus, und verwenden Sie den Computernamen oder ```localhost``` im Feld **SQL Server-Name**.

### <a name="how-to-use-and-run-it"></a>Verwenden und Ausführen

Standardmäßig wird der Datenbankserver mit der Datenbankstandardinstanz automatisch ausgeführt. Mithilfe von Tools wie SQL Server Management Studio können Sie lokal über den virtuellen Computer auf die SQL Server-Datenbank zugreifen. Die Konten lokaler Administratoren verfügen über Administratorzugriff auf die Datenbank.

Die DSVM verfügt außerdem über ODBC- und JDBC-Treiber für die Kommunikation mit SQL Server, Azure SQL-Datenbanken und Azure Synapse Analytics über Anwendungen, die in mehreren Sprachen (beispielsweise Python, R oder Machine Learning Server) geschrieben wurden.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Konfiguration und Installation auf der DSVM 

 SQL Server wird wie gewohnt installiert. Das Programm befindet sich unter `C:\Program Files\Microsoft SQL Server`. Die datenbankinterne Machine Learning Server-Instanz finden Sie unter `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Die DSVM verfügt auch über eine separate eigenständige Machine Learning Server-Instanz. Diese ist unter `C:\Program Files\Microsoft\R Server\R_SERVER` installiert. Diese beiden Machine Learning Server-Instanzen verwenden keine Bibliotheken gemeinsam.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (eigenständige Instanz)

| Category | Wert |
| ------------- | ------------- |
| Was ist das?   | Eine eigenständige Instanz (einzelner In-Process-Knoten) der beliebten Apache Spark-Plattform – einem System für die schnelle Verarbeitung umfangreicher Daten und für Machine Learning     |
| Unterstützte DSVM-Editionen      | Linux     |
| Typische Verwendung      | <ul><li>Schnelles lokales Entwickeln von Spark-/PySpark-Anwendungen mit einem kleineren Dataset und spätere Bereitstellung in großen Spark-Clustern wie z. B. Azure HDInsight</li><li>Testen des Spark-Kontexts von Microsoft Machine Learning Server</li><li>Erstellen von ML-Anwendungen mithilfe von SparkML oder der Open-Source-Bibliothek [MMLSpark](https://github.com/Azure/mmlspark) von Microsoft</li></ul> |
| Links zu Beispielen      |    Jupyter-Beispiel:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark-Kontext): /dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Verwandte Tools auf der DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark-/PySpark-Kernel)</li><li>Microsoft Machine Learning Server, SparkR, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Verwendung
Sie können Spark-Aufträge über die Befehlszeile übermitteln, indem Sie den Befehl `spark-submit` oder `pyspark` ausführen. Sie können auch ein neues Notebook mit dem Spark-Kernel erstellen, um ein Jupyter-Notebook zu erstellen.

Spark kann über R mit auf der DSVM verfügbaren Bibliotheken wie SparkR, Sparklyr und Microsoft Machine Learning Server verwendet werden. Hinweise zu Beispielen finden Sie in der obigen Tabelle.

### <a name="setup"></a>Einrichten
Vor dem Ausführen im Spark-Kontext in Microsoft Machine Learning Server unter der Ubuntu Linux-Edition der DSVM müssen Sie einen einmaligen Einrichtungsschritt ausführen, um eine lokale Hadoop HDFS- und Yarn-Instanz mit einem einzelnen Knoten zu aktivieren. Standardmäßig gilt, dass die Hadoop-Dienste installiert, aber auf der DSVM deaktiviert sind. Um sie zu aktivieren, müssen Sie die folgenden Befehle beim ersten Mal als „root“ ausführen:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Sie können die Hadoop-bezogenen Dienste beenden, wenn Sie sie nicht mehr benötigen, indem Sie ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` ausführen.

Ein Beispiel, in dem gezeigt wird, wie MRS in einem Spark-Remotekontext (dies ist die eigenständige Spark-Instanz auf der DSVM) entwickelt und getestet wird, wird im Verzeichnis `/dsvm/samples/MRS` bereitgestellt und ist dort verfügbar.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Konfiguration und Installation auf der DSVM 
|Plattform|Installationsort ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotheken für den Zugriff auf Daten über Azure Blob Storage oder Azure Data Lake Storage (ADLS) mithilfe der MMLSpark Machine Learning-Bibliotheken von Microsoft sind unter „$SPARK_HOME/jars“ vorinstalliert. Diese JAR-Dateien werden beim Start von Spark automatisch geladen. Standardmäßig verwendet Spark Daten auf dem lokalen Datenträger. 

Damit die Spark-Instanz auf der DSVM auf Daten zugreifen kann, die in Blob Storage oder Azure Data Lake Storage gespeichert sind, müssen Sie die Datei `core-site.xml` basierend auf der Vorlage in „$SPARK_HOME/conf/core-site.xml.template“ erstellen und konfigurieren. Außerdem müssen Sie über die entsprechenden Anmeldeinformationen für den Zugriff auf Blob Storage und Azure Data Lake Storage verfügen. (Beachten Sie, dass die Vorlagendateien Platzhalter für Blob Storage- und Azure Data Lake Storage-Konfigurationen verwenden.)

Ausführlichere Informationen zum Erstellen von Azure Data Lake Storage-Dienstanmeldeinformationen finden Sie unter [Authentifizierung mit Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Nach Eingabe der Anmeldeinformationen für Blob Storage oder Azure Data Lake Storage in die Datei „core-site.xml“ können Sie über das URI-Präfix „wasb://“ oder „adl://“ auf die in diesen Quellen gespeicherten Daten verweisen.