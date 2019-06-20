---
title: Ausführen von Apache Sqoop-Aufträgen mit .NET und HDInsight – Azure
description: Erfahren Sie, wie Sie mit dem HDInsight .NET-SDK einen Apache Sqoop-Import und -Export zwischen einem Apache Hadoop-Cluster und einer Azure SQL-Datenbank durchführen können.
keywords: Sqoop-Auftrag
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ac0890be0abccb316bffc4d9bdd6868a80173e18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710121"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Ausführen von Apache Sqoop-Aufträgen mit .NET SDK für Apache Hadoop in HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie das Azure HDInsight .NET-SDK zum Ausführen von Apache Sqoop-Aufträgen in HDInsight zum Importieren und Exportieren zwischen HDInsight-Cluster und Azure SQL-Datenbank oder SQL Server-Datenbank verwendet werden kann.

> [!NOTE]
> Sie können die in diesem Artikel beschriebenen Verfahren zwar entweder mit einem Windows- oder Linux-basierten HDInsight-Cluster verwenden, doch sie funktionieren nur von einem Windows-Client aus. Um andere Methoden auszuwählen, verwenden Sie die Registerkartenauswahl am Anfang dieses Artikels.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* Einen Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erstellen von Cluster und SQL-Datenbank](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Verwenden von Sqoop auf HDInsight-Clustern mit dem .NET-SDK
Das HDInsight .NET-SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. In diesem Abschnitt erstellen Sie eine C#-Konsolenanwendung zum Exportieren der „hivesampletable“ in die Azure SQL-Datenbanktabelle, die Sie zuvor in diesem Tutorial erstellt haben.

## <a name="submit-a-sqoop-job"></a>Übermitteln eines Sqoop-Auftrags

1. Erstellen Sie in Visual Studio eine C#-Konsolenanwendung.

2. Führen Sie in der Paket-Manager-Konsole von Visual Studio den folgenden NuGet-Befehl aus, um das Paket zu importieren:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Verwenden Sie den folgenden Code in der Datei „Program.cs“:
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Wählen Sie zum Ausführen des Programms die Taste **F5**. 

## <a name="limitations"></a>Einschränkungen
Für Linux-basiertes HDInsight gelten die folgenden Einschränkungen:

* Massenexport: Derzeit unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector keine Masseneinfügungen.

* Batchverarbeitung: Wenn Sie den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="next-steps"></a>Nächste Schritte
Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Oozie mit HDInsight](../hdinsight-use-oozie-linux-mac.md): Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Weitere Methoden zum Hochladen von Daten in HDInsight oder Azure Blob Storage.

