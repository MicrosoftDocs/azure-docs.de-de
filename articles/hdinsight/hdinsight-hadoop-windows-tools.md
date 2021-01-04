---
title: Verwenden eines Windows-Computers mit Hadoop in HDInsight – Azure
description: Verwenden eines Windows-Computers mit Hadoop in HDInsight Verwalten und Abfragen von Clustern mit PowerShell, Visual Studio und Linux-Tools Entwickeln von Big Data-Lösungen mit .NET
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 6482b2ec07a0473ddbea2098d37c5d67c8b68704
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539699"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Arbeiten im Apache Hadoop-Ökosystem in HDInsight auf einem Windows-Computer

Erfahren Sie mehr über die Entwicklungs- und Verwaltungsoptionen auf einem Windows-Computer für die Arbeit im Apache Hadoop-Ökosystem in HDInsight.

HDInsight basiert auf Apache Hadoop und Hadoop-Komponenten sowie Open-Source-Technologien, die unter Linux entwickelt wurden. HDInsight 3.4 oder höher verwendet die Linux-Distribution Ubuntu als zugrunde liegendes Betriebssystem für den Cluster. Sie können aber auch von einem Windows-Client oder einer Windows-Entwicklungsumgebung aus mit HDInsight arbeiten.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Verwenden von PowerShell für Bereitstellungs- und Verwaltungsaufgaben

Azure PowerShell ist eine Skriptumgebung, mit der Sie Bereitstellungs- und Verwaltungsaufgaben in HDInsight von Windows aus steuern und automatisieren können.

Beispiele für Aufgaben, die Sie mit PowerShell erledigen können:

* [Erstellen von Clustern mit PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Ausführen von Apache Hive-Abfragen mit PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Verwalten von Clustern mit PowerShell](hdinsight-administer-use-powershell.md).

Führen Sie die Schritte zum [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) aus, um die neueste Version zu erhalten.

## <a name="utilities-you-can-run-in-a-browser"></a>Hilfsprogramme für die Ausführung in einem Browser

Die folgenden Hilfsprogramme verfügen über eine Webbenutzeroberfläche, die in einem Browser ausgeführt wird:
* **[Azure Cloud Shell](../cloud-shell/overview.md)** ist eine interaktive Befehlszeilen-Shell, die in Ihrem Browser und aus dem Azure-Portal heraus ausgeführt wird.

* Die **[Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)** ist ein Hilfsprogramm zum Verwalten und Überwachen im Azure-Portal, mit dem Sie verschiedene Arten von Aufträgen verwalten können. Beispiele:
    * [Verwenden von Apache Ambari mit der REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive-Ansicht in Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez-Ansicht in Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake-Tools (Hadoop) für Visual Studio

Verwenden Sie die Data Lake-Tools für Visual Studio zum Bereitstellen und Verwalten von Storm-Topologien. Die Data Lake-Tools installieren auch das SCP.NET SDK, mit dem Sie C#-Storm-Topologien mit Visual Studio entwickeln können.

Bevor Sie mit den folgenden Beispielen fortfahren, [installieren und testen Sie die Data Lake-Tools für Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Beispiele für Aufgaben, die Sie mit Visual Studio und den Data Lake-Tools für Visual Studio ausführen können:
* [Bereitstellen und Verwalten von Storm-Topologien in Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Entwickeln von C#-Topologien für Storm mithilfe von Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md) Die Tools enthalten Beispielvorlagen für Storm-Topologien, mit denen Sie Verbindungen mit Datenbanken wie Azure Cosmos DB und SQL-Datenbank herstellen können.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio und das .NET SDK

Sie können Visual Studio mit dem .NET SDK verwenden, um Cluster zu verwalten und Big Data-Anwendungen zu entwickeln. Sie können auch andere IDEs für die folgenden Aufgaben verwenden, in den Beispielen wird jedoch Visual Studio gezeigt.

Beispiele für Aufgaben, die Sie mit dem .NET SDK in Visual Studio ausführen können:
* [Azure HDInsight SDK für .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true)
* [Ausführen von Apache Hive-Abfragen mit dem .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Verwenden benutzerdefinierter C#-Funktionen mit Apache Hive- und Apache Pig-Streaming für Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA und Eclipse-IDE für Spark-Cluster

Sowohl [Intellij IDEA](https://www.jetbrains.com/idea/download) als auch die [Eclipse-IDE](https://www.eclipse.org/downloads/) können für Folgendes verwendet werden:
* Entwickeln und Übermitteln einer Scala Spark-Anwendung an einen HDInsight Spark-Cluster
* Zugreifen auf Spark-Clusterressourcen
* Entwickeln und lokales Ausführen einer Scala Spark-Anwendung

In diesem Artikel wird Folgendes beschrieben:
* IntelliJ IDEA: [Erstellen von Apache Spark-Anwendungen für einen HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse-IDE oder Scala-IDE für Eclipse: [Erstellen von Apache Spark-Anwendungen für HDInsight-Cluster mit dem Azure-Toolkit für Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks in Spark für Datenanalysten

Apache Spark-Cluster in HDInsight enthalten Apache Zeppelin-Notebooks und -Kernel, die mit Jupyter-Notebooks verwendet werden können.

* [Lesen Sie, wie Sie Kernel in Apache Spark-Clustern mit Jupyter-Notebooks verwenden, um Spark-Anwendungen zu testen.](spark/apache-spark-zeppelin-notebook.md)
* [Lesen Sie, wie Sie Apache Zeppelin-Notebooks in Apache Spark-Clustern verwenden, um Spark-Aufträge auszuführen.](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Ausführen von Linux-basierten Tools und Technologien unter Windows

In Situationen, in denen Sie Tools oder Technologien verwenden müssen, die nur unter Linux verfügbar sind, sollten Sie die folgenden Möglichkeiten in Erwägung ziehen:

* **Bash auf Ubuntu unter Windows 10** stellt ein Linux-Subsystem unter Windows bereit. Bash erlaubt die direkte Ausführung von Linux-Hilfsprogrammen, ohne eine dedizierte Linux-Installation verwalten zu müssen. Die Installationsschritte finden Sie unter [Windows Subsystem for Linux Installation Guide for Windows 10](/windows/wsl/install-win10) (Windows-Subsystem für Linux: Installationshandbuch für Windows 10).  Es funktionieren auch andere [Unix-Shells](https://www.gnu.org/software/bash/).
* **Docker für Windows** ermöglicht den Zugriff auf viele Linux-basierten Tools und kann direkt unter Windows ausgeführt werden. Sie können Docker beispielsweise verwenden, um den Beeline-Client für Hive direkt unter Windows auszuführen. Sie können auch Docker verwenden, um ein lokales Jupyter-Notebook auszuführen und eine Remoteverbindung mit Spark in HDInsight herzustellen. [Erste Schritte mit Docker für Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** ermöglicht das Durchsuchen des Clusterdateisystems über eine SSH-Verbindung auf einer grafischen Benutzeroberfläche.

## <a name="cross-platform-tools"></a>Plattformübergreifende Tools

Die Azure-Befehlszeilenschnittstelle (CLI) ist die plattformübergreifende Befehlszeilenumgebung von Microsoft zum Verwalten von Azure-Ressourcen.  Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nicht mit der Arbeit in Linux-basierten Clustern vertraut sind, lesen Sie die folgenden Artikel:
* [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md)
* [Tipps zu HDInsight-Clustern unter Linux](hdinsight-hadoop-linux-information.md)