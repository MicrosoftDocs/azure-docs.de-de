---
title: 'Plattformen und Tools für Data Science-Projekte: Team Data Science-Prozess'
description: Hier finden Sie eine Liste mit verfügbaren Daten- und Analyseressourcen für Unternehmen, die den Team Data Science-Prozess als Standard einführen möchten, sowie weitere Informationen zu den Ressourcen.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453859"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plattformen und Tools für Data Science-Projekte

Microsoft bietet ein umfassendes Spektrum an Analyseressourcen sowohl für Cloud- als auch für lokale Plattformen. Durch deren Bereitstellung können Sie die Effizienz und Skalierbarkeit Ihrer Data Science-Projekte verbessern. Der [Team Data Science-Prozess](overview.md) (TDSP) ist ein Leitfaden für Teams, die Data Science-Projekte gemeinschaftlich, auf nachvollziehbare Weise und mit Versionskontrolle implementieren möchten.  Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und zugeordneten Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß diesem Prozess anstrebt.

Für Data Science-Teams, die den TDSP nutzen, stehen unter anderem folgende Analyseressourcen zur Verfügung:

- Data Science-VMs (Windows und Linux CentOS)
- HDInsight Spark-Cluster
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight Hive-Cluster
- Azure File Storage
- SQL Server 2019 R- und Python-Dienste
- Azure Databricks

Dieses Dokument enthält eine kurze Beschreibung der Ressourcen sowie Links zu den Tutorials und exemplarischen Vorgehensweisen, die TDSP-Teams veröffentlicht haben. Dort erhalten Sie detaillierte Informationen zur Verwendung, und Sie erfahren, wie Sie damit intelligente Anwendungen erstellen. Weitere Informationen zu den Ressourcen finden Sie auf der jeweiligen Produktseite. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science-VM (DSVM)

Die Data Science-VM wird von Microsoft sowohl für Windows als auch für Linux angeboten und enthält beliebte Tools für Data Science-Modelle und Entwicklungsaktivitäten. Zu den Tools zählen unter anderem folgende:

- Microsoft R Server Developer Edition 
- Anaconda Python Distribution
- Jupyter-Notebooks für Python und R 
- Visual Studio Community Edition mit Python und R-Tools (Windows)/Eclipse (Linux)
- Power BI Desktop für Windows
- SQL Server 2016 Developer Edition (Windows)/Postgres (Linux)

Es enthält auch **Machine Learning- und KI-Tools** wie xgboost, mxnet und Vowpal Wabbit.

Die Data Science-VM ist derzeit für die Betriebssysteme **Windows** und **Linux CentOS** verfügbar. Wählen Sie die Größe Ihrer Data Science-VM (Anzahl von CPU-Kernen und Größe des Arbeitsspeichers) abhängig von den Anforderungen der Data Science-Projekte aus, die Sie damit ausführen möchten. 

Weitere Informationen zur Windows-Edition der Data Science-VM finden Sie im Azure Marketplace unter [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019). Die Linux-Edition der Data Science-VM finden Sie unter [Data Science Virtual Machine for Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) (Data Science-VM für Linux (CentOS)).

Informationen zur effizienten Ausführung einiger gängiger Data Science-Aufgaben auf der Data Science-VM finden Sie unter [Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können](../data-science-virtual-machine/vm-do-ten-things.md).


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-Cluster

Apache Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die In-Memory-Verarbeitung unterstützt, um die Leistung von Analyseanwendungen für große Datenmengen zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank der In-Memory-Berechnungsfunktionen eignet sich Spark besonders für iterative Machine Learning-Algorithmen und für Graphberechnungen. Spark ist auch mit Azure Blob Storage (WASB) kompatibel, sodass Ihre vorhandenen, in Azure gespeicherten Daten problemlos mit Spark verarbeitet werden können.

Wenn Sie einen Spark-Cluster in HDInsight erstellen, erstellen Sie damit Azure-Serverressourcen, für die Spark installiert und konfiguriert ist. Die Erstellung eines Spark-Clusters in HDInsight dauert etwa zehn Minuten. Speichern Sie die zu verarbeitenden Daten in Azure Blob Storage. Informationen zur Verwendung von Azure Blob Storage mit einem Cluster finden Sie unter [Verwenden von Azure Storage mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Das TDSP-Team von Microsoft hat zwei umfassende exemplarische Vorgehensweisen zur Erstellung von Data Science-Lösungen mit Azure HDInsight Spark-Clustern veröffentlicht: eine für Python und eine für Scala. Weitere Informationen zu **Spark-Clustern** in Azure HDInsight finden Sie in der [Einführung in Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md). Informationen zum Erstellen einer Data Science-Lösung mit **Python** für einen Azure HDInsight Spark-Cluster finden Sie in der [Übersicht über Data Science mit Spark in Azure HDInsight](spark-overview.md). Informationen zum Erstellen einer Data Science-Lösung mit **Scala** für einen Azure HDInsight Spark-Cluster finden Sie unter [Data Science unter Verwendung von Scala und Spark in Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Mit Azure Synapse Analytics können Sie einfach und blitzschnell Computeressourcen bereitstellen – ohne Überdimensionierung oder Überzahlung. Darüber hinaus haben Sie nur hier die Möglichkeit, die Nutzung von Computeressourcen zu unterbrechen und so ihre Cloudkosten besser zu steuern. Dank der Möglichkeit zur Bereitstellung skalierbarer Computeressourcen können Sie alle Ihre Daten in Azure Synapse Analytics überführen. Die Speicherkosten sind äußerst gering, und Sie können die Ausführung von Computevorgängen auf die Teile der Datasets beschränken, die Sie analysieren möchten. 

Weitere Informationen zu Azure Synapse Analytics finden Sie auf der [Azure Synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse)-Website. Informationen zur Erstellung umfassender erweiterter Analyselösungen mit Azure Synapse Analytics finden Sie unter [Der Team Data Science-Prozess in Aktion: Verwenden von Azure Synapse Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake ist ein unternehmensweites Repository für alle Arten von Daten, die vor Anwendung formaler Anforderungen oder Schemas an einer zentralen Stelle gesammelt werden. Dank dieser Flexibilität können in einem Data Lake beliebige Arten von Daten gespeichert werden – unabhängig von Größe, Struktur und Erfassungsgeschwindigkeit. Organisationen können diese Data Lakes daraufhin mithilfe von Hadoop oder erweiterten Analysen auf Muster untersuchen. Data Lakes können auch als Repository für eine kostengünstigere Datenvorbereitung fungieren, bevor die Daten schließlich aufbereitet und in ein Data Warehouse verschoben werden.

Weitere Informationen zu Azure Data Lake finden Sie unter [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/) (Einführung in Azure Data Lake). Informationen zum Erstellen einer umfassenden skalierbaren Data Science-Lösung mit Azure Data Lake finden Sie unter [Skalierbare Data Science mit Azure Data Lake: lückenlose exemplarische Vorgehensweise](data-lake-walkthrough.md).


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive-Cluster (Hadoop)

Apache Hive ist ein Data Warehouse-System für Hadoop, das die Zusammenfassung, Abfrage und Analyse von Daten mit HiveQL (SQL-ähnliche Abfragesprache) ermöglicht. Hive kann verwendet werden, um Daten interaktiv zu durchsuchen oder um mehrmals durchführbare Batchverarbeitungsaufträge zu erstellen.

Hive ermöglicht Ihnen die Strukturierung größtenteils unstrukturierter Daten. Nach dem Definieren der Struktur können Sie mit Hive die Daten in einem Hadoop-Cluster ganz ohne Java oder MapReduce abfragen. Mit HiveQL (der Hive-Abfragesprache) können Sie Abfragen mit T-SQL-ähnlichen Anweisungen erstellen.

Datenspezialisten können mit Hive benutzerdefinierte Python-Funktionen in Hive-Abfragen ausführen, um Datensätze zu verarbeiten. Dadurch wird der Funktionsumfang von Hive-Abfragen in der Datenanalyse erheblich erweitert. Datenspezialisten können insbesondere skalierbare Features in Sprachen entwickeln, mit denen sie am besten vertraut sind: das SQL-ähnliche HiveQL und Python. 

Weitere Informationen zu Azure HDInsight Hive-Clustern finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](../../hdinsight/hadoop/hdinsight-use-hive.md). Informationen zum Erstellen einer umfassenden skalierbaren Data Science-Lösung mit Azure HDInsight Hive-Clustern finden Sie unter [Der Team Data Science-Prozess in Aktion: Verwenden von Azure HDInsight Hadoop-Clustern](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Beim Azure File Storage-Dienst werden Dateifreigaben in der Cloud unter Verwendung des standardmäßigen SMB-Protokolls (Server Message Block) bereitgestellt. Sowohl SMB 2.1 als auch SMB 3.0 werden unterstützt. Mit Azure File Storage können Sie Legacyanwendungen, für die Dateifreigaben benötigt werden, schnell und ohne teures Umschreiben zu Azure migrieren. Anwendungen, die auf virtuellen Azure-Maschinen, in Clouddiensten oder auf lokalen Clients ausgeführt werden, können eine Dateifreigabe genauso in der Cloud bereitstellen, wie eine Desktopanwendung eine normale SMB-Freigabe bereitstellt. Die File Storage-Freigaben können dann von beliebig vielen Anwendungskomponenten gleichzeitig eingebunden und genutzt werden.

Besonders nützlich für Data Science-Projekte ist die Möglichkeit, einen Azure-Dateispeicher für den Austausch von Projektdaten mit den Mitgliedern des Projektteams zu erstellen. Dadurch hat jedes Mitglied Zugriff auf die gleiche Kopie der Daten im Azure-Dateispeicher. Über diesen Dateispeicher können auch Featuregruppen geteilt werden, die im Zuge der Projektdurchführung generiert werden. Wenn es sich bei dem Projekt um einen Kundenauftrag handelt, können Ihre Kunden einen Azure-Dateispeicher unter ihrem eigenen Azure-Abonnement erstellen, um die Projektdaten und -features mit Ihnen zu teilen. Dadurch hat der Kunde die volle Kontrolle über die Datenressourcen des Projekts. Weitere Informationen zu Azure File Storage finden Sie unter [Entwickeln für Azure Files mit .NET](../../storage/files/storage-dotnet-how-to-use-files.md) sowie unter [Verwenden von Azure Files mit Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R- und Python-Dienste

R Services (datenbankintern) bietet eine Plattform zum Entwickeln und Bereitstellen von intelligenten Anwendungen zur Gewinnung neuer Erkenntnisse. Sie können die umfangreiche und leistungsfähige R-Sprache (einschließlich der vielen Pakete aus der R-Community) zum Erstellen von Modellen und Generieren von Vorhersagen auf der Grundlage Ihrer SQL Server-Daten nutzen. Da R Services (datenbankintern) die R-Sprache in SQL Server integriert, besteht eine enge Verbindung zwischen Analysen und Daten. Dadurch lassen sich die Kosten und Sicherheitsrisiken vermeiden, die mit dem Verschieben von Daten einhergehen.

R Services (datenbankintern) unterstützt die Open Source-R-Sprache mit einem umfassenden Satz von SQL Server-Tools und -Technologien. Dadurch profitieren Sie von überragender Leistung, Sicherheit, Zuverlässigkeit und Verwaltbarkeit. Sie können R-Lösungen mithilfe von praktischen und vertrauten Tools bereitstellen. Ihre Produktionsanwendungen können die R-Laufzeit aufrufen und Vorhersagen und visuelle Elemente mit Transact-SQL abrufen. Mit den ScaleR-Bibliotheken können Sie außerdem die Skalierung und Leistung Ihrer R-Lösungen verbessern. Weitere Informationen finden Sie unter [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services).

Das TDSP-Team von Microsoft hat zwei umfassende exemplarische Vorgehensweisen zur Erstellung von Data Science-Lösungen in SQL Server 2016 R Services veröffentlicht: eine für R-Programmierer und eine für SQL-Entwickler. Die umfassende exemplarische Vorgehensweise zu Data Science für **R-Programmierer** befindet sich [hier](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Das Tutorial zu erweiterten datenbankinternen Analysen für **SQL-Entwickler** befindet sich [hier](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Anhang: Tools zum Einrichten von Data Science-Projekten

### <a name="install-git-credential-manager-on-windows"></a>Installieren von Git Credential Manager unter Windows

Wenn Sie den TDSP unter **Windows** verwenden möchten, müssen Sie für die Kommunikation mit den Git-Repositorys **Git Credential Manager (GCM)** installieren. Um GCM installieren zu können, müssen Sie zunächst **Chocolaty** installieren. Führen Sie zum Installieren von Chocolaty und GCM in Windows PowerShell die folgenden Befehle als **Administrator** aus:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Installieren von Git auf Computern unter Linux (CentOS)

Führen Sie den folgenden Bash-Befehl aus, um Git auf Computern unter Linux (CentOS) zu installieren:

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generieren öffentlicher SSH-Schlüssel auf Computern unter Linux (CentOS)

Wenn Sie die Git-Befehle auf einem Computer unter Linux (CentOS) ausführen, müssen Sie Ihrer Azure DevOps Services-Instanz den öffentlichen SSH-Schlüssel Ihres Computers hinzufügen, damit dieser von Azure DevOps Services erkannt wird. Zunächst müssen Sie einen öffentlichen SSH-Schlüssel generieren und ihn den öffentlichen SSH-Schlüsseln auf der Seite mit der Azure DevOps Services-Sicherheitseinstellung hinzufügen. 

1. Führen Sie zum Generieren des SSH-Schlüssels die beiden folgenden Befehle aus: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Befehle zum Generieren des SSH-Schlüssels](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopieren Sie den gesamten SSH-Schlüssel (einschließlich *ssh-rsa*). 
1. Melden Sie sich bei Azure DevOps Services an. 
1. Klicken Sie rechts oben auf der Seite auf **<Ihr Name\>** und dann auf **Sicherheit**. 
    
   ![Klicken Sie auf Ihren Namen, und klicken Sie dann auf „Sicherheit“.](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klicken Sie auf **Öffentlicher SSH-Schlüssel** und anschließend auf **+ Hinzufügen**. 

   ![Klicken Sie auf „Öffentliche SSH-Schlüssel“ und anschließend auf „+ Hinzufügen“.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Fügen Sie den kopierten SSH-Schlüssel in das Textfeld ein, und speichern Sie ihn.


## <a name="next-steps"></a>Nächste Schritte

Vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar. Sie sind im Thema [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

Beispiele, die zeigen, wie Sie die Schritte im Team Data Science-Prozess mithilfe von Azure Machine Learning Studio (klassisch) ausführen, finden Sie im Lernpfad [Mit Azure ML](./index.yml).