---
title: Verwalten von Apache Hadoop-Clustern in HDInsight mit dem .NET SDK – Azure
description: Hier erfahren Sie, wie Sie mit dem HDInsight .NET SDK administrative Aufgaben für die Apache Hadoop-Cluster in HDInsight ausführen.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 06e3178e344ee46f67cfd8a6feaf08d56d3c86e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724130"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Verwalten von Apache Hadoop-Clustern in HDInsight mit dem .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Erfahren Sie, wie Sie HDInsight-Cluster mit dem [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)verwalten.

**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Verbinden mit Azure HDInsight

Sie benötigen die folgenden NuGet-Pakete:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Das folgende Codebeispiel veranschaulicht, wie Sie eine Verbindung mit Azure herstellen, bevor Sie HDInsight-Cluster unter Ihrem Azure-Abonnement verwalten können.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Sie sehen eine Eingabeaufforderung, wenn Sie dieses Programm ausführen.  Wenn Sie diese Eingabeaufforderung nicht sehen möchten, finden Sie weitere Informationen unter [Erstellen von .NET HDInsight-Anwendungen für die nicht interaktive Authentifizierung](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Erstellen von Clustern

Siehe [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Auflisten von Clustern

Im folgenden Codeausschnitt sind Cluster und einige Eigenschaften aufgeführt:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Löschen von Clustern

Verwenden Sie den folgenden Codeausschnitt, um einen Cluster synchron oder asynchron zu löschen: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skalieren von Clustern

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

> [!NOTE]  
> Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Überprüfen Sie ggf. auf der Seite „Eigenschaften“ die Version Ihres Clusters.  Informationen hierzu finden Sie unter [Auflisten und Anzeigen von Clustern](hdinsight-administer-use-portal-linux.md#showClusters).

Auswirkungen der Änderung der Anzahl von Datenknoten für die von HDInsight unterstützten Clustertypen:

* Apache Hadoop
  
    Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.
  
    Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dies führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.
* Apache HBase
  
    Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichen Abschluss des Skalierungsvorgangs müssen Sie die Topologie neu ausgleichen.
  
    Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:
  
  * Storm-Webbenutzeroberfläche
  * Befehlszeilenschnittstelle (CLI)
    
    Weitere Informationen finden Sie in der Dokumentation zu [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .
    
    Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:
    
    ![Ausgleichen der HDInsight Storm-Skalierung](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:
    

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Der folgende Codeausschnitt veranschaulicht die synchrone oder asynchrone Größenänderung eines Clusters:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Gewähren/Entziehen des Zugriffs

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren. Zum Widerrufen:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Zum Gewähren:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Dies kann auch über das Portal durchgeführt werden. Weitere Informationen finden Sie unter [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualisieren von HTTP-Anmeldeinformationen

Dabei handelt es sich um die gleiche Vorgehensweise wie beim Gewähren/Widerrufen des HTTP-Zugriffs.  Wenn dem Cluster der HTTP-Zugriff gewährt wurde, müssen Sie ihn zunächst widerrufen.  Gewähren Sie anschließend den Zugriff mit den neuen HTTP-Anmeldeinformationen.

## <a name="find-the-default-storage-account"></a>Suchen des Standardspeicherkontos

Der folgende Codeausschnitt veranschaulicht, wie der Name und Schlüssel des Standardspeicherkontos für einen Cluster abgerufen werden.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Übermitteln von Aufträgen

**So übermitteln Sie MapReduce-Aufträge**

Weitere Informationen finden Sie unter [Ausführen von MapReduce-Beispielen in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**So übermitteln Sie Apache Hive-Aufträge** 

Weitere Informationen finden Sie unter [Ausführen von Apache Hive-Abfragen per HDInsight .NET-SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**So übermitteln Sie Apache Pig-Aufträge**

Weitere Informationen finden Sie unter [Ausführen von Apache Pig-Aufträgen mithilfe des .NET SDK für Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**So übermitteln Sie Apache Sqoop-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Apache Hadoop mit HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**So übermitteln Sie Apache Oozie-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem Azure HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Hochladen von Daten in Azure Blob Storage

Siehe [Hochladen von Daten in HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Siehe auch

* [Referenzdokumentation zum HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Verwalten von Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md)
* [Verwalten von HDInsight über eine Befehlszeilenschnittstelle][hdinsight-admin-cli]
* [Erstellen von HDInsight-Clustern][hdinsight-provision]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
