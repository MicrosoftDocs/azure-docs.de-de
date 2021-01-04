---
title: Identifizieren von Szenarien für Azure Machine Learning – Team Data Science-Prozess
description: Wählen Sie die geeigneten Szenarien für den erweiterten Predictive Analytics-Prozess im Team Data Science-Prozess.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319307"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Szenarien für die erweiterte Analyse in Azure Machine Learning
Dieser Artikel beschreibt die verschiedenen Beispieldatenquellen und Zielszenarien, die mit dem [Team Data Science-Prozess (TDSP)](overview.md)verarbeitet werden können. Der TDSP bietet einen systematischen Ansatz für Teams zur Zusammenarbeit bei der Entwicklung intelligenter Anwendungen. Die hier gezeigten Szenarien veranschaulichen einige der Optionen im Datenverarbeitungs-Workflow, die von den Datenmerkmalen, Quellspeicherorten und Zielrepositorys in Azure abhängen.

Die **Entscheidungsstruktur** , mit deren Hilfe Sie die für Ihre Daten und Ziele geeigneten Beispielszenarien auswählen, wird im letzten Abschnitt vorgestellt.

In jedem der folgenden Abschnitte wird ein Beispielszenario vorgestellt. Für jedes Szenario werden ein möglicher Ablauf für Datenwissenschaft oder erweiterte Analyse sowie die unterstützenden Azure-Ressourcen aufgeführt.

> [!NOTE]
> **Für alle folgenden Szenarien müssen Sie diese Schritte ausführen:**
> <br/>
> 
> * [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md)
>   <br/>
> * [Erstellen eines Azure Machine Learning-Arbeitsbereichs](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Szenario \#1: Kleines bis mittelgroßes tabellarisches Dataset in lokalen Dateien
![Kleine bis mittelgroße lokale Dateien][1]

#### <a name="additional-azure-resources-none"></a>Zusätzliche Azure-Ressourcen: Keine
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Laden Sie ein Dataset hoch.
1. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren hochgeladenen Datasets beginnt.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Szenario \#2: Kleines bis mittelgroßes Dataset lokaler Dateien, die verarbeitet werden müssen
![Kleine bis mittelgroße lokale Dateien mit Verarbeitung][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (IPython Notebook-Server)
1. Erstellen Sie eine Azure-VM, auf der IPython Notebook ausgeführt wird.
1. Laden Sie Daten in einen Azure Storage-Container hoch.
1. Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus dem Azure Storage-Container zugreifen.
1. Transformieren Sie die Daten in ein bereinigtes Tabellenformat.
1. Speichern Sie die transformierten Daten in Azure-Blobs.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten aus den Azure-Blobs mithilfe des Moduls [Import Data][import-data].
1. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren aufgenommenen Datasets beginnt.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Szenario \#3: Großes Dataset lokaler Dateien, die auf Azure-Blobs ausgerichtet sind
![Große lokale Dateien][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (IPython Notebook-Server)
1. Erstellen Sie eine Azure-VM, auf der IPython Notebook ausgeführt wird.
1. Laden Sie Daten in einen Azure Storage-Container hoch.
1. Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.
1. Transformieren Sie die Daten bei Bedarf in ein bereinigtes Tabellenformat.
1. Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf.
1. Extrahieren Sie ein kleines bis mittelgroßes Datenbeispiel.
1. Speichern Sie die Beispieldaten in Azure-Blobs.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten aus den Azure-Blobs mithilfe des Moduls [Import Data][import-data].
1. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren erfassten Datasets beginnt.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Szenario \#4: Kleines bis mittelgroßes Dataset lokaler Dateien, das auf SQL Server in einer Azure-VM ausgerichtet ist
![Kleine bis mittelgroße lokale Dateien in SQL DB in Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (SQL Server/IPython Notebook-Server)
1. Erstellen Sie eine Azure-VM, auf der SQL Server und IPython Notebook ausgeführt werden.
1. Laden Sie Daten in einen Azure Storage-Container hoch.
1. Bereiten Sie die Daten in einem Azure Storage-Container mithilfe von IPython Notebook vor, und bereinigen Sie sie.
1. Transformieren Sie die Daten bei Bedarf in ein bereinigtes Tabellenformat.
1. Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).
1. Laden Sie Daten in einer SQL Server-Datenbank, die auf einem virtuellen Azure-Computer ausgeführt wird.
   
   Option \#1: Verwenden von SQL Server Management Studio
   
   * Melden Sie sich beim virtuellen SQL Server-Computer an.
   * Führen Sie SQL Server Management Studio aus.
   * Erstellen Sie eine Datenbank und Zieltabellen.
   * Verwenden Sie eine der Methoden für den Massenimport, um Daten aus lokalen VM-Dateien zu laden.
   
   Option \#2: Verwenden von IPython Notebook – für mittlere und größere Datasets nicht empfehlenswert
   
   <!-- -->    
   * Verwenden Sie die ODBC-Verbindungszeichenfolge, um von der VM auf SQL Server zuzugreifen.
   * Erstellen Sie eine Datenbank und Zieltabellen.
   * Verwenden Sie eine der Methoden für den Massenimport, um Daten aus lokalen VM-Dateien zu laden.
1. Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Die Features müssen nicht in den Datenbanktabellen materialisiert werden. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.
1. Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten mithilfe des Moduls [Import Data][import-data] direkt aus der SQL Server-Instanz. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die Abfrage [Import Data][import-data] ein.
1. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren erfassten Datasets beginnt.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Szenario \#5: Großes Dataset in lokalen Dateien, SQL Server-Zielinstanz auf virtuellem Azure-Computer
![Große lokale Dateien in SQL DB in Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (SQL Server/IPython Notebook-Server)
1. Erstellen Sie eine Azure-VM, auf der SQL Server und der IPython Notebook-Server ausgeführt werden.
1. Laden Sie Daten in einen Azure Storage-Container hoch.
1. (Optional) Bereiten Sie Daten vor, und bereinigen Sie sie.
   
    a.  Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.
   
    b.  Transformieren Sie die Daten bei Bedarf in ein bereinigtes Tabellenformat.
   
    c.  Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).
1. Laden Sie Daten in einer SQL Server-Datenbank, die auf einem virtuellen Azure-Computer ausgeführt wird.
   
    a.  Melden Sie sich beim virtuellen SQL Server-Computer an.
   
    b.  Falls die Daten noch nicht gespeichert wurden, laden Sie Datendateien aus einem Azure-Speichercontainer in den lokalen VM-Ordner herunter.
   
    c.  Führen Sie SQL Server Management Studio aus.
   
    d.  Erstellen Sie eine Datenbank und Zieltabellen.
   
    e.  Verwenden Sie eine Massenimportmethoden zum Laden der Daten.
   
    f.  Wenn Tabellenverknüpfungen erforderlich sind, erstellen Sie Indizes, um Verknüpfungen zu beschleunigen.
   
   > [!NOTE]
   > Zum schnelleren Laden großer Datenmengen empfiehlt es sich, die Erstellung partitionierter Tabellen und den Massenimport der Daten parallel durchzuführen. Weitere Informationen finden Sie unter [Paralleler Datenimport in partitionierte SQL-Tabellen](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Die Features müssen nicht in den Datenbanktabellen materialisiert werden. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.
1. Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten mithilfe des Moduls [Import Data][import-data] direkt aus der SQL Server-Instanz. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die Abfrage [Import Data][import-data] ein.
1. Einfacher Azure Machine Learning-Versuchsablauf, der mit hochgeladenem Dataset beginnt

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Szenario \#6: Großes Dataset in einer lokalen SQL Server-Datenbank, das auf SQL Server auf einem virtuellen Azure-Computer ausgerichtet ist
![Große lokale SQL DB in SQL DB in Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (SQL Server/IPython Notebook-Server)
1. Erstellen Sie eine Azure-VM, auf der SQL Server und der IPython Notebook-Server ausgeführt werden.
1. Verwenden Sie eine der Datenexportmethoden, um die Daten aus SQL Server in Dumpdateien zu exportieren.
   
   > [!NOTE]
   > Wenn Sie sich dazu entschließen, alle Daten aus der lokalen Datenbank zu verschieben, wählen Sie eine alternative (schnellere) Methode zum Verschieben der vollständigen Datenbank in die SQL Server-Instanz in Azure aus. Überspringen Sie die Schritte zum Exportieren von Daten, zum Erstellen der Datenbank und zum Laden/Importieren der Daten in die Zieldatenbank, und führen Sie die alternative Methode durch.
   > 
   > 
1. Laden Sie Dumpdateien in den Azure Storage-Container hoch.
1. Laden Sie die Daten in eine SQL Server-Datenbank auf einem virtuellen Azure-Computer.
   
   a.  Melden Sie sich beim virtuellen SQL Server-Computer an.
   
   b.  Laden Sie Datendateien aus einem Azure Storage-Container in den lokalen VM-Ordner herunter.
   
   c.  Führen Sie SQL Server Management Studio aus.
   
   d.  Erstellen Sie eine Datenbank und Zieltabellen.
   
   e.  Verwenden Sie eine Massenimportmethoden zum Laden der Daten.
   
   f.  Wenn Tabellenverknüpfungen erforderlich sind, erstellen Sie Indizes, um Verknüpfungen zu beschleunigen.
   
   > [!NOTE]
   > Zum schnelleren Laden großer Datenmengen führen Sie die Erstellung partitionierter Tabellen und den Massenimport der Daten parallel durch. Weitere Informationen finden Sie unter [Paralleler Datenimport in partitionierte SQL-Tabellen](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Die Features müssen nicht in den Datenbanktabellen materialisiert werden. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.
1. Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten mithilfe des Moduls [Import Data][import-data] direkt aus der SQL Server-Instanz. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die Abfrage [Import Data][import-data] ein.
1. Einfacher Azure Machine Learning-Versuchsablauf, der mit hochgeladenem Dataset beginnt

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternative Methode zum Kopieren einer vollständigen Datenbank aus einem lokalen SQL Server in Azure SQL-Datenbank
![Trennen lokaler DB und Anfügen an SQL DB in Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Virtueller Azure-Computer (SQL Server/IPython Notebook-Server)
Um die gesamte SQL Server-Datenbank auf dem virtuellen SQL Server-Computer zu replizieren, kopieren Sie eine Datenbank von einem Speicherort/Server auf einen anderen, vorausgesetzt, dass die Datenbank vorübergehend offline geschaltet werden kann. Hierzu können Sie den Objekt-Explorer von SQL Server Management Studio oder die entsprechenden Transact-SQL-Befehle verwenden.

1. Trennen Sie die Datenbank am Quellspeicherort. Weitere Informationen finden Sie unter [Trennen einer Datenbank](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. Kopieren Sie im Windows-Explorer oder in der Windows-Eingabeaufforderung die getrennten Datenbankdateien und Protokolldateien in den Zielspeicherort auf dem virtuellen SQL Server-Computer in Azure.
1. Fügen Sie die kopierten Dateien an die SQL Server-Zielinstanz an. Weitere Informationen finden Sie unter [Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Verschieben einer Datenbank durch Trennen und Anfügen (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Szenario \#7: Big Data in lokalen Dateien, Zielstrukturdatenbank in Azure HDInsight Hadoop-Clustern
![Big Data in lokalen Zielstruktur][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Zusätzliche Azure-Ressourcen: Azure HDInsight Hadoop-Cluster und Azure-VM (IPython Notebook-Server)
1. Erstellen Sie einen virtuellen Azure-Computer, auf dem der IPython Notebook-Server ausgeführt wird.
1. Erstellen Sie einen Azure HDInsight Hadoop-Cluster.
1. (Optional) Bereiten Sie Daten vor, und bereinigen Sie sie.
   
   a.  Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.
   
   b.  Transformieren Sie die Daten bei Bedarf in ein bereinigtes Tabellenformat.
   
   c.  Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).
1. Laden Sie Daten in den Standardcontainer des in Schritt 2 ausgewählten Hadoop-Clusters.
1. Laden Sie Daten in die Strukturdatenbank im Azure HDInsight Hadoop-Cluster.
   
   a.  Melden Sie sich beim Hauptknoten des Hadoop-Clusters an.
   
   b.  Öffnen Sie die Hadoop-Befehlszeile.
   
   c.  Geben Sie das Strukturstammverzeichnis über den Befehl `cd %hive_home%\bin` in der Hadoop-Befehlszeile ein.
   
   d.  Führen Sie die Strukturabfragen aus, um die Datenbank und die Tabellen zu erstellen, und laden Sie Daten aus dem Blobspeicher in die Strukturtabellen.
   
   > [!NOTE]
   > Wenn es sich um große Datenmengen handelt, können Benutzer die Strukturtabelle mit Partitionen erstellen. Benutzer können dann mithilfe einer `for` -Schleife in der Hadoop-Befehlszeile auf dem Hauptknoten Daten in die partitionierte Strukturtabelle laden.
   > 
   > 
1. Erkunden Sie die Daten, und erstellen Sie in der Hadoop-Befehlszeile Features nach Bedarf. Die Features müssen nicht in den Datenbanktabellen materialisiert werden. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.
   
   a.  Melden Sie sich beim Hauptknoten des Hadoop-Clusters an.
   
   b.  Öffnen Sie die Hadoop-Befehlszeile.
   
   c.  Geben Sie das Strukturstammverzeichnis über den Befehl `cd %hive_home%\bin` in der Hadoop-Befehlszeile ein.
   
   d.  Führen Sie die Strukturabfragen in der Hadoop-Befehlszeile auf dem Hauptknoten des Hadoop-Clusters aus, um die Daten zu durchsuchen und Features nach Bedarf zu erstellen.
1. Falls erforderlich und/oder gewünscht, entnehmen Sie passende Beispieldaten für Azure Machine Learning Studio.
1. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/)an.
1. Lesen Sie die Daten direkt von `Hive Queries` mithilfe des Moduls [Import Data][import-data]. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die Abfrage [Import Data][import-data] ein.
1. Einfacher Azure Machine Learning-Versuchsablauf, der mit hochgeladenem Dataset beginnt

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Entscheidungsstruktur zur Auswahl des Szenarios
---
Das folgende Diagramm fasst die oben beschriebenen Szenarien sowie die Advanced Analytics Process and Technology-Entscheidungen zusammen, die Sie zu jedem einzelnen Szenario führen. Datenverarbeitung, Erkundung, Featureentwicklung und Stichprobenentnahme können in einer oder mehreren Methoden/Umgebungen (in der Quell-, Zwischen- und/oder Zielumgebung) erfolgen und nach Bedarf wiederholt werden. Das Diagramm dient nur zur Veranschaulichung einiger möglicher Abläufe und stellt keine erschöpfende Auflistung dar.

![Exemplarische Vorgehensweisen für den DS-Prozess][8]

### <a name="advanced-analytics-in-action-examples"></a>Erweiterte Analysen in Aktion – Beispiele
Umfassende exemplarische Vorgehensweisen zu Azure Machine Learning, die die Advanced Analytics Process and Technology (ADAPT) mit öffentlichen DataSets verwenden, finden Sie hier:

* [Der Team Data Science-Prozess in Aktion: Verwenden von SQL Server](sql-walkthrough.md)
* [Der Team Data Science-Prozess in Aktion: Verwenden von HDInsight Hadoop-Clustern](hive-walkthrough.md)

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data