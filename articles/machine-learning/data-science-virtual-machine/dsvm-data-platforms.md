---
title: Datenplattformen für die Data Science-VM – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Datenplattformen und Tools, die für Data Science Virtual Machine unterstützt werden.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 27e0deae9c35ad8fa00659e3e3e505cace6e9014
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516499"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Unterstützte Datenplattformen für Data Science Virtual Machine

Mit der Data Science-VM (DSVM) können Sie Analysen für ein breites Spektrum von Datenplattformen erstellen. Neben Schnittstellen für Remotedatenplattformen bietet die DSVM eine lokale Instanz für eine schnelle Entwicklung und Prototypenerstellung. 

Folgende Datenplattformen werden von der DSVM unterstützt: 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Was ist das?   | Eine lokale Instanz einer relationalen Datenbank      |
| Unterstützte DSVM-Editionen      | Windows      |
| Typische Verwendung      | Schnelle lokale Entwicklung mit einem kleineren Dataset <br/> Ausführen von R (datenbankintern)   |
| Links zu Beispielen      |    In die SQL-Datenbank `nyctaxi` wurde ein kleiner Teil des New York City-Datasets geladen. <br/> Ein Jupyter-Beispiel zur Veranschaulichung von Microsoft R und datenbankinternen Analysen finden Sie hier:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Verwandte Tools auf der DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-Treiber<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> Die Developer-Edition von SQL Server 2016 kann für Entwicklungs- und Testzwecke verwendet werden. Für die Verwendung in einer Produktionsumgebung benötigen Sie eine Lizenz oder einen der virtuellen SQL Server-Computer. 


### <a name="setup"></a>Einrichtung

Der Datenbankserver ist bereits vorkonfiguriert, und die Windows-Dienste für SQL Server (etwa `SQL Server (MSSQLSERVER)`) werden automatisch ausgeführt. Lediglich die datenbankinternen Analysen mit Microsoft R müssen manuell aktiviert werden. Öffnen Sie hierzu in SQL Server Management Studio (SSMS) nach der Anmeldung als Serveradministrator eine neue Abfrage, vergewissern Sie sich, dass die Datenbank `master` ausgewählt ist, und führen Sie anschließend einmalig den folgenden Befehl aus: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Zum Ausführen von SQL Server Management Studio können Sie in der Programmliste oder über die Windows-Suche nach „SQL Server Management Studio“ suchen und das Programm ausführen. Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, wählen Sie „Windows-Authentifizierung“ aus, und verwenden Sie den Computernamen oder ```localhost``` als SQL Server-Name. 

### <a name="how-to-use--run-it"></a>Verwendung/Ausführung  

Der Datenbankserver mit der Standard-Datenbankinstanz wird standardmäßig automatisch ausgeführt. Mithilfe von Tools wie SQL Server Management Studio können Sie lokal über den virtuellen Computer auf die SQL Server-Datenbank zugreifen. Lokale Administratoren haben Administratorzugriff auf die Datenbank. 

Die DSVM verfügt außerdem über ODBC- und JDBC-Treiber für die Kommunikation mit SQL Server, Azure SQL-Datenbanken und Azure SQL Data Warehouse über Anwendungen, die in mehreren Sprachen (beispielsweise Python, oder R) geschrieben wurden. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Konfiguration/Installation auf der DSVM 

SQL Server wird wie gewohnt installiert. Das Programm befindet sich unter `C:\Program Files\Microsoft SQL Server`. Die datenbankinterne -R-Instanz finden Sie unter `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Die DSVM verfügt auch über eine separate eigenständigen R Server-Instanz. Diese ist unter `C:\Program Files\Microsoft\R Server\R_SERVER` installiert. Diese beiden R-Instanzen teilen sich die Bibliotheken nicht.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (eigenständige Instanz)

| | |
| ------------- | ------------- |
| Was ist das?   | Eine eigenständige Instanz (einzelner In-Process-Knoten) der beliebten Apache Spark-Plattform – einem System für die schnelle Verarbeitung umfangreicher Daten und für Machine Learning.     |
| Unterstützte DSVM-Editionen      | Linux <br /> Windows (experimentell)      |
| Typische Verwendung      | * Schnelles lokales Entwickeln von Spark-/PySpark-Anwendungen mit einem kleineren Dataset und späteres Bereitstellen in großen Spark-Clustern (beispielsweise Azure HDInsight)<br/> * Testen des Spark-Kontexts von Microsoft R Server <br />* Erstellen von ML-Anwendungen mithilfe von SparkML oder der Open Source-Bibliothek [MMLSpark](https://github.com/Azure/mmlspark) von Microsoft  |
| Links zu Beispielen      |    Jupyter-Beispiel: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark-Kontext): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Verwandte Tools auf der DSVM       | PySpark, Scala<br/>Jupyter (Spark-/PySpark-Kernel)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Verwendung
Zum Ausführen von Spark können Sie über die Befehlszeile mithilfe der Befehle `spark-submit` oder `pyspark` Spark-Aufträge übermitteln. Sie können auch ein neues Notebook mit dem Spark-Kernel erstellen, um ein Jupyter-Notebook zu erstellen. 

Spark kann über R mit auf der DSVM verfügbaren Bibliotheken wie SparkR, Sparklyr oder Microsoft R Server verwendet werden. Hinweise zu Beispielen finden Sie in der obigen Tabelle. 

> [!NOTE]
> Das Ausführen von Microsoft R Server im Spark-Kontext der DSVM wird nur in der Ubuntu Linux-Edition der DSVM unterstützt. 



### <a name="setup"></a>Einrichtung
Vor dem Ausführen im Spark-Kontext in Microsoft R Server unter der Ubuntu Linux-Edition der DSVM müssen Sie einen einmaligen Einrichtungsschritt ausführen, um eine lokale Hadoop HDFS- und Yarn-Instanz mit einem einzelnen Knoten zu aktivieren. Standardmäßig gilt, dass die Hadoop-Dienste installiert, aber auf der DSVM deaktiviert sind. Um sie zu aktivieren, müssen Sie den folgenden Befehle beim ersten Mal als root ausführen:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Sie können die Hadoop-bezogenen Dienste beenden, wenn Sie sie nicht benötigen. Führen Sie dazu ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` aus. Ein Beispiel, das zeigt, wie MRS im Remote-Spark-Kontext (eigenständige Spark-Instanz auf der DSVM) entwickelt und getestet wird, finden Sie im Verzeichnis `/dsvm/samples/MRS`. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Konfiguration/Installation auf der DSVM 
|Plattform|Installationsort ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotheken für den Datenzugriff über Azure Blob Storage oder Azure Data Lake Storage (ADLS) und für die Verwendung der MMLSpark-Machine Learning-Bibliotheken von Microsoft sind unter „$SPARK_HOME/jars“ vorinstalliert. Diese JAR-Dateien werden beim Start von Spark automatisch geladen. Standardmäßig verwendet Spark Daten auf dem lokalen Datenträger. Wenn die Spark-Instanz auf der DSVM auf gespeicherte Daten in Azure Blob Storage oder ADLS zugreifen soll, müssen Sie die Datei `core-site.xml` auf der Grundlage der Vorlage aus „$SPARK_HOME/conf/core-site.xml.template“ (enthält Platzhalter für Blob- und ADLS-Konfigurationen) mit den entsprechenden Anmeldeinformationen für Azure Blob Storage und Azure Data Lake Storage erstellen/konfigurieren. Eine ausführlichere Anleitung zum Erstellen der Anmeldeinformationen für den ADLS-Dienst finden Sie [hier](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Nach Eingabe der Anmeldeinformationen für Azure-Blob Storage oder ADLS in die Datei „core-site.xml“ können Sie mit dem URI-Präfix „wasb://“ oder „adl://“ auf die in diesen Quellen gespeicherten Daten verweisen. 

