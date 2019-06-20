---
title: Verwenden von Azure-Vorlagen zum Erstellen von Azure HDInsight-Clustern mit Azure Data Lake Storage Gen1 | Microsoft Docs
description: Verwenden von Azure Resource Manager-Vorlagen zum Erstellen und Verwenden von HDInsight-Clustern mit Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161384"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen1 mithilfe einer Azure Resource Manager-Vorlage
> [!div class="op_single_selector"]
> * [Verwenden des Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Verwenden von PowerShell (für Standardspeicher)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Verwenden von PowerShell (für zusätzlichen Speicher)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Verwenden von Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Erfahren Sie, wie Sie mithilfe von Azure PowerShell einen HDInsight-Cluster mit Azure Data Lake Storage Gen1 **als zusätzlichem Speicher** erstellen.

Für unterstützte Clustertypen kann Data Lake Storage Gen1 als Standardspeicher oder als zusätzliches Speicherkonto verwendet werden. Wenn Data Lake Storage Gen1 als zusätzlicher Speicher verwendet wird, ist das Standardspeicherkonto für den Cluster weiterhin Azure Storage Blob (WASB), und clusterbezogene Dateien (z.B. Protokolle usw.) werden weiterhin in den Standardspeicher geschrieben, während die Daten, die Sie verarbeiten möchten, in einem Data Lake Storage Gen1-Konto gespeichert werden können. Das Verwenden von Data Lake Storage Gen1 als zusätzliches Speicherkonto wirkt sich nicht auf Leistung oder die Fähigkeit aus, Daten über den Cluster aus dem Speicher zu lesen bzw. in ihn zu schreiben.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Verwenden von Azure Data Lake Storage Gen1 für HDInsight-Clusterspeicher

Hier finden Sie einige wichtige Überlegungen zur Verwendung von HDInsight mit Data Lake Storage Gen1:

* Eine Option zum Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1 als Standardspeicher ist für die HDInsight-Versionen 3.5 und 3.6 verfügbar.

* Eine Option zum Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1 als zusätzlichen Speicher ist für die HDInsight-Versionen 3.2, 3.4, 3.5 und 3.6 verfügbar.

In diesem Artikel stellen wir einen Hadoop-Cluster mit Data Lake Storage Gen1 als zusätzlichem Speicher bereit. Anleitungen zum Erstellen eines Hadoop-Clusters mit Data Lake Storage Gen1 als Standardspeicher finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 mithilfe des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Mindestens Azure PowerShell 1.0**. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory-Dienstprinzipal**. Die Schritte in diesem Tutorial enthalten Anweisungen zum Erstellen eines Dienstprinzipals in Azure AD. Sie müssen jedoch Azure AD-Administrator sein, um einen Dienstprinzipal erstellen zu können. Wenn Sie Azure AD-Administrator sind, können Sie diese Voraussetzung ignorieren und mit dem Tutorial fortfahren.

    **Wenn Sie kein Azure AD-Administrator sind**, können Sie die erforderlichen Schritte zum Erstellen eines Dienstprinzipals nicht ausführen. In diesem Fall muss Ihr Azure AD-Administrator zunächst einen Dienstprinzipal erstellen, bevor Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 erstellen können. Zudem muss der Dienstprinzipal mit einem Zertifikat erstellt werden, wie unter [Erstellen eines Dienstprinzipals mit Zertifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) beschrieben.

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1
Die Resource Manager-Vorlage und die Voraussetzungen für die Verwendung der Vorlage sind auf GitHub unter [Bereitstellen eines HDInsight Linux-Clusters mit dem neuen Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) verfügbar. Befolgen Sie die unter diesem Link bereitgestellten Anweisungen, um einen HDInsight-Cluster mit Data Lake Storage Gen1 als zusätzlichem Speicher zu erstellen.

Für die Anweisungen unter dem oben genannten Link ist PowerShell erforderlich. Bevor Sie mit diesen Anweisungen beginnen, müssen Sie sich bei Ihrem Azure-Konto anmelden. Öffnen Sie auf dem Desktop ein neues Azure PowerShell-Fenster, und geben Sie die folgenden Codeausschnitte ein. Stellen Sie bei der Aufforderung zum Anmelden sicher, dass Sie sich als einer der Administratoren/Besitzer des Abonnements anmelden:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Mit der Vorlage werden die folgenden Ressourcentypen bereitgestellt:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Hochladen von Beispieldaten in Data Lake Storage Gen1
Mit der Resource Manager-Vorlage wird ein neues Data Lake Storage Gen1-Konto erstellt und dem HDInsight-Cluster zugeordnet. Sie müssen nun einige Beispieldaten in Data Lake Storage Gen1 hochladen. Sie benötigen diese Daten später im Tutorial zum Ausführen von Aufträgen über einen HDInsight-Cluster, bei denen auf Daten im Data Lake Storage Gen1-Konto zugegriffen wird. Anweisungen zum Hochladen von Daten finden Sie unter [Hochladen einer Datei in Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)herunterladen.

## <a name="set-relevant-acls-on-the-sample-data"></a>Festlegen relevanter ACLs für die Beispieldaten
Damit über den HDInsight-Cluster auf die hochgeladenen Beispieldaten zugegriffen werden kann, müssen Sie sicherstellen, dass die Azure AD-Anwendung, mit der die Identität zwischen dem HDInsight-Cluster und Data Lake Storage Gen1 eingerichtet wird, Zugriff auf die Datei oder den Ordner hat, auf die bzw. den Sie zugreifen möchten. Führen Sie dazu die folgenden Schritte aus.

1. Suchen Sie den Namen der Azure AD-Anwendung, die dem HDInsight-Cluster und dem Data Lake Storage Gen1-Konto zugeordnet ist. Eine Möglichkeit zum Suchen des Namens besteht darin, das Blatt für den HDInsight-Cluster zu öffnen, den Sie mit der Resource Manager-Vorlage erstellt haben, auf die Registerkarte **Azure Active Directory-Identität für den Cluster** zu klicken und den Wert für **Dienstprinzipalname** zu suchen.
2. Legen Sie nun den Zugriff auf diese Azure AD-Anwendung für die Datei oder den Ordner fest, auf die bzw. den Sie über den HDInsight-Cluster zugreifen möchten. Informationen zum Festlegen der richtigen Zugriffssteuerungslisten für die Datei oder den Ordner in Data Lake Storage Gen1 finden Sie unter [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Ausführen von Testaufträgen im HDInsight-Cluster für die Verwendung von Data Lake Storage Gen1
Nachdem Sie einen HDInsight-Cluster konfiguriert haben, können Sie Testaufträge für den Cluster ausführen, um zu testen, ob der HDInsight-Cluster auf Data Lake Storage Gen1 zugreifen kann. Zu diesem Zweck führen Sie einen Hive-Beispielauftrag aus. Es wird eine Tabelle aus den Beispieldaten erstellt, die Sie zuvor in Ihr Data Lake Storage Gen1-Konto hochgeladen haben.

In diesem Abschnitt stellen Sie eine SSH-Verbindung mit einem HDInsight-Linux-Cluster her und führen eine Hive-Beispielabfrage aus. Wenn Sie einen Windows-Client verwenden, sollten Sie **PuTTY** verwenden, das als Download unter [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) verfügbar ist.

Weitere Informationen zum Verwenden von PuTTY finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Nachdem die Verbindung hergestellt wurde, starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl:

   ```
   hive
   ```
2. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens **vehicles** mithilfe der CLI ein, wobei Sie die Beispieldaten in Data Lake Storage Gen1 verwenden:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Es sollte eine Ausgabe angezeigt werden, die Folgendem ähnelt:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Zugreifen auf Data Lake Storage Gen1 mit HDFS-Befehlen
Nachdem Sie den HDInsight-Cluster für die Verwendung von Data Lake Storage Gen1 konfiguriert haben, können Sie die HDFS-Shellbefehle verwenden, um auf den Speicher zuzugreifen.

In diesem Abschnitt stellen Sie eine SSH-Verbindung mit einem HDInsight-Linux-Cluster her und führen die HDFS-Befehle aus. Wenn Sie einen Windows-Client verwenden, sollten Sie **PuTTY** verwenden, das als Download unter [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) verfügbar ist.

Weitere Informationen zum Verwenden von PuTTY finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Nachdem die Verbindung hergestellt wurde, listen Sie mithilfe des folgenden HDFS-Dateisystembefehls die Dateien im Data Lake Storage Gen1-Konto auf.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Hierbei sollte auch die Datei aufgeführt werden, die Sie bereits in Data Lake Storage Gen1 hochgeladen haben.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Sie können auch den Befehl `hdfs dfs -put` verwenden, um Dateien in Data Lake Storage Gen1 hochzuladen, und dann mit `hdfs dfs -ls` überprüfen, ob der Upload der Dateien erfolgreich war.


## <a name="next-steps"></a>Nächste Schritte
* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
