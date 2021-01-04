---
title: Beschränken des Zugriffs mit Shared Access Signatures – Azure HDInsight
description: Erfahren Sie, wie Sie mit Shared Access Signatures den HDInsight-Zugriff auf Daten in Azure-Blobspeicher einschränken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 141db7feee987b7fffc578e19c60bd94ad56d239
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511636"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Verwenden von Shared Access Signatures von Azure-Blobspeicher zum Einschränken des Zugriffs auf Daten in HDInsight

HDInsight hat vollen Zugriff auf Daten in Azure-Blobspeicherkonten, die mit dem Cluster verbunden sind. Sie können Shared Access Signatures für den Blobcontainer verwenden, um den Zugriff auf die Daten einzuschränken. Shared Access Signatures (SAS) sind ein Feature von Azure-Blobspeicherkonten, das das Einschränken des Zugriffs auf Daten ermöglicht. Sie können beispielsweise einen schreibgeschützten Zugriff auf Daten bieten.

> [!IMPORTANT]  
> Erwägen Sie für eine Lösung mit Apache Ranger die Verwendung von in die Domäne eingebundenem HDInsight. Weitere Informationen finden Sie im Dokument [Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern (Vorschau)](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

> [!WARNING]  
> HDInsight benötigt vollen Zugriff auf den Standardspeicher für den Cluster.

## <a name="prerequisites"></a>Voraussetzungen

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Ein vorhandener [Speichercontainer](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/).

* Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

* Bei Verwendung von [Python](https://www.python.org/downloads/), Version 2.7 oder höher.

* Bei Verwendung von C# muss die Version von Visual Studio 2013 oder höher sein.

* Das URI-Schema für Ihr Speicherkonto. Dieses Schema ist `wasb://` für Azure-Blobspeicher, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure-Blobspeicher aktiviert ist, lautet der URI `wasbs://`.

* Ein vorhandener HDInsight-Cluster, dem eine Shared Access Signature hinzugefügt werden kann. Falls nicht, können Sie mit Azure PowerShell einen Cluster erstellen und während der Clustererstellung eine Shared Access Signature hinzufügen.

* Die Beispieldateien von [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Dieses Repository enthält die folgenden Elemente:

  * Ein Visual Studio-Projekt, in dem ein Speichercontainer, eine gespeicherte Richtlinie und SAS für die Verwendung mit HDInsight erstellt werden kann.
  * Ein Python-Skript zum Erstellen eines Speichercontainers, einer gespeicherten Richtlinie und einer SAS für die Verwendung mit HDInsight.
  * Ein PowerShell-Skript zum Erstellen eines HDInsight-Clusters und seiner Konfiguration für die Verwendung von SAS. Eine aktualisierte Version wird weiter unten verwendet.
  * Eine Beispieldatei: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Shared Access Signatures

Es gibt zwei Arten von Shared Access Signatures:

* `Ad hoc`: Startzeit, Ablaufzeit und Berechtigungen für die SAS werden direkt im SAS-URI angegeben.

* `Stored access policy`: Eine gespeicherte Zugriffsrichtlinie wird für einen Ressourcencontainer definiert (also etwa für einen Blobcontainer). Eine Richtlinie kann verwendet werden, um Einschränkungen für eine oder mehrere SAS zu verwalten. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen) dieser gespeicherten Zugriffsrichtlinie.

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Eine SAS ist eine URL, daher kann sie von jedem Benutzer verwendet werden, der die SAS erhält. Es spielt keine Rolle, wer sie ursprünglich angefordert hat. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Auf diese Weise verteilte SAS sind gültig, bis eines von vier Ereignissen eintritt:

1. Die Ablaufzeit der SAS wird erreicht.

2. Die angegebene Ablaufzeit für die gespeicherte Zugriffsrichtlinie, auf die SAS verweist, wurde erreicht. Folgende Szenarien bewirken, dass die Ablaufzeit erreicht wird:

    * Das Zeitintervall ist abgelaufen.
    * Die gespeicherte Zugriffsrichtlinie wird so geändert, dass die Ablaufzeit auf einen Zeitpunkt in der Vergangenheit gesetzt ist. Die Änderung der Ablaufzeit ist eine Möglichkeit zum Widerrufen der SAS.

3. Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit dem gleichen Namen neu erstellen, sind alle SAS-Token für die vorherige Richtlinie gültig (wenn die Ablaufzeit für die SAS nicht überschritten wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.

4. Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert. Das Neugenerieren des Schlüssels lässt die Authentifizierung bei allen Anwendungen fehlschlagen, die den vorherigen Schlüssel verwenden. Aktualisieren Sie alle Komponenten auf den neuen Schlüssel.

> [!IMPORTANT]  
> Ein SAS-URI wird dem Kontoschlüssel, mit dem die Signatur erstellt wurde, und der zugehörigen gespeicherten Zugriffsrichtlinie (sofern vorhanden) zugeordnet. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, kann eine SAS nur durch Änderung des Kontoschlüssels aufgehoben werden.

Es wird empfohlen, stets gespeicherte Zugriffsrichtlinien zu verwenden. Bei Verwendung gespeicherter Richtlinien können Sie Signaturen aufheben oder bei Bedarf das Ablaufdatum verlängern. In den Schritten in diesem Dokument werden gespeicherte Zugriffsrichtlinien zum Generieren von SAS verwendet.

Weitere Informationen zu Shared Access Signatures finden Sie unter [Grundlagen zum SAS-Modell](../storage/common/storage-sas-overview.md).

## <a name="create-a-stored-policy-and-sas"></a>Erstellen einer gespeicherte Richtlinie und einer SAS

Speichern Sie das SAS-Token, das am Ende jeder Methode erstellt wird. Das Token ähnelt der folgenden Ausgabe:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>PowerShell

Ersetzen Sie `RESOURCEGROUP`, `STORAGEACCOUNT` und `STORAGECONTAINER` durch die entsprechenden Werte für Ihren vorhandenen Speichercontainer. Ändern Sie das Verzeichnis in `hdinsight-dotnet-python-azure-storage-shared-access-signature-master`, oder überarbeiten Sie den `-File`-Parameter so, dass er den absoluten Pfad für `Set-AzStorageblobcontent` enthält. Geben Sie den folgenden PowerShell-Befehl ein:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Die Verwendung von Variablen in diesem Abschnitt basiert auf einer Windows-Umgebung. Leichte Variationen werden für bash- oder andere Umgebungen erforderlich.

1. Ersetzen Sie `STORAGEACCOUNT` und `STORAGECONTAINER` durch die entsprechenden Werte für Ihren vorhandenen Speichercontainer.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Legen Sie den abgerufenen Primärschlüssel auf eine Variable zur späteren Verwendung fest. Ersetzen Sie `PRIMARYKEY` durch den im vorherigen Schritt abgerufenen Wert, und geben Sie dann den folgenden Befehl ein:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Ändern Sie das Verzeichnis in `hdinsight-dotnet-python-azure-storage-shared-access-signature-master`, oder überarbeiten Sie den `--file`-Parameter so, dass er den absoluten Pfad für `az storage blob upload` enthält. Führen Sie die verbleibenden Befehle aus:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Verwenden von Python

Öffnen Sie die Datei `SASToken.py`, und ersetzen Sie `storage_account_name`, `storage_account_key` und `storage_container_name` durch die entsprechenden Werte für Ihren vorhandenen Speichercontainer, und führen Sie dann das Skript aus.

Möglicherweise müssen Sie `pip install --upgrade azure-storage` ausführen, wenn Sie die Fehlermeldung `ImportError: No module named azure.storage` erhalten.

### <a name="using-c"></a>Verwenden von C\#

1. Öffnen Sie die Projektmappe in Visual Studio.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SASExample**, und wählen Sie **Eigenschaften** aus.

3. Wählen Sie **Einstellungen** aus, und fügen Sie Werte für die folgenden Einträge hinzu:

    |Element |BESCHREIBUNG |
    |---|---|
    |StorageConnectionString|Die Verbindungszeichenfolge für das Speicherkonto, für das eine gespeicherte Richtlinie und SAS erstellt werden soll. Das Format muss `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` sein, wobei `myaccount` der Name Ihres Speicherkontos und `mykey` der Schlüssel des Speicherkontos ist.|
    |ContainerName|Der Container im Speicherkonto, auf den Sie den Zugriff beschränken möchten.|
    |SASPolicyName|Der zu verwendende Name für die gespeicherte Richtlinie, die erstellt wird.|
    |FileToUpload|Der Pfad zu einer Datei, die in den Container hochgeladen wird.|

4. Führen Sie das Projekt aus. Speichern Sie die SAS-Richtlinientoken, den Speicherkontonamen und den Containernamen. Diese Werte werden verwendet, wenn Sie das Speicherkonto mit Ihrem HDInsight-Cluster verknüpfen.

## <a name="use-the-sas-with-hdinsight"></a>Verwenden der SAS mit HDInsight

Beim Erstellen eines HDInsight-Clusters müssen Sie ein primäres Speicherkonto angeben. Sie können auch zusätzliche Speicherkonten angeben. Beide Methoden zum Hinzufügen von Speicher benötigen Vollzugriff auf die Speicherkonten und Container, die verwendet werden.

Verwenden Sie eine Shared Access Signature, um den Containerzugriff einzuschränken. Fügen Sie der Konfiguration **core-site** für den Cluster einen benutzerdefinierten Eintrag hinzu. Sie können den Eintrag während der Erstellung des Clusters mithilfe von PowerShell hinzufügen oder nach der Clustererstellung mittels Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Erstellen eines Clusters, der die SAS verwendet

Ersetzen Sie `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT` und `TOKEN` durch die entsprechenden Werte. Geben Sie diese PowerShell-Befehle ein:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Wenn Sie zur Angabe des HTTP/S- oder SSH-Benutzernamens und Kennworts aufgefordert werden, müssen Sie ein Kennwort angeben, das die folgenden Kriterien erfüllt:
>
> * Mindestens 10 Zeichen.
> * Mindestens eine Ziffer.
> * Mindestens ein nicht alphanumerisches Zeichen.
> * Mindestens ein Groß- oder Kleinbuchstaben.

Die Ausführung dieses Skripts dauert eine Weile, meist etwa 15 Minuten. Wenn das Skript ohne Fehler abgeschlossen wird, wurde der Cluster erstellt.

### <a name="use-the-sas-with-an-existing-cluster"></a>Verwenden des SAS mit einem bestehenden Cluster

Wenn Sie bereits einen Cluster haben, können Sie die SAS der **core-site**-Konfiguration mithilfe der folgenden Schritte hinzufügen:

1. Öffnen Sie die Ambari-Webbenutzeroberfläche für den Cluster. Die Adresse für diese Seite lautet `https://YOURCLUSTERNAME.azurehdinsight.net`. Authentifizieren Sie sich bei Aufforderung am Cluster mithilfe des Administratornamens (admin) und des Kennworts, das Sie beim Erstellen des Clusters verwendet haben.

1. Navigieren Sie zu **HDFS** > **Configs** > **Advanced** > **Custom core-site** (HDFS > Konfigurationen > Erweitert > Benutzerdefinierte core-site).

1. Erweitern Sie den Abschnitt **Custom core-site** (Benutzerdefinierte core-site), scrollen Sie zum Seitenende, und wählen Sie **Add property...** (Eigenschaft hinzufügen) aus. Verwenden Sie für **Key** (Schlüssel) und **Value** (Wert) die folgenden Werte:

    * **Schlüssel**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Value**: Die von einer der zuvor ausgeführten Methoden zurückgegebene SAS.

    Ersetzen Sie `CONTAINERNAME` durch den Containernamen, den Sie mit der C#- oder SAS-Anwendung verwendet haben. Ersetzen Sie `STORAGEACCOUNTNAME` durch den Namen des von Ihnen verwendeten Speicherkontos.

    Wählen Sie **Hinzufügen** aus, um diesen Schlüssel und diesen Wert zu speichern.

1. Klicken Sie auf die Schaltfläche **Speichern**, um die Konfigurationsänderungen zu speichern. Fügen Sie bei Aufforderung eine Beschreibung der Änderung hinzu (z. B. „Hinzufügen des SAS-Speicherzugriffs“), und wählen Sie anschließend **Speichern** aus.

    Wählen Sie **OK** aus, wenn die Änderungen abgeschlossen sind.

   > [!IMPORTANT]  
   > Sie müssen mehrere Dienste neu starten, damit die Änderung wirksam wird.

1. Die Dropdownliste **Neu starten** wird angezeigt. Wählen Sie in der Dropdownliste **Restart All Affected** (Alle betroffenen neu starten) und anschließend die Option __Confirm Restart All__ (Alle neu starten bestätigen) aus.

    Wiederholen Sie diesen Vorgang für **MapReduce2** und **YARN**.

1. Sobald diese Dienste neu gestartet wurden, wählen Sie sie nacheinander aus und deaktivieren den Wartungsmodus in der Dropdownliste **Service Actions** (Dienstaktionen).

## <a name="test-restricted-access"></a>Testen des eingeschränkten Zugriffs

Befolgen Sie die folgenden Schritte zum Überprüfen, ob Sie Elemente im SAS-Speicherkonto ausschließlich lesen und auflisten können.

1. Verbinden mit dem Cluster Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters, und geben Sie den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Um die Inhalte des Containers aufzulisten, führen Sie an der Eingabeaufforderung den folgenden Befehl aus:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersetzen Sie `SASCONTAINER` durch den Namen des Containers, den Sie für das SAS-Speicherkonto erstellt haben. Ersetzen Sie `SASACCOUNTNAME` durch den Namen des Speicherkontos, das für die SAS verwendet wird.

    Die Liste enthält die Datei, die bei der Erstellung des Containers und der SAS hochgeladen wurde.

3. Geben Sie den folgenden Befehl an, um sicherzustellen, dass Sie den Inhalt der Datei lesen können. Ersetzen Sie `SASCONTAINER` und `SASACCOUNTNAME` wie im vorherigen Schritt. Ersetzen Sie `sample.log` durch den Namen der Datei, die im vorherigen Befehl angezeigt wurde:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Dieser Befehl listet den Inhalt der Datei auf.

4. Geben Sie den folgenden Befehl an, um die Datei in das lokale Dateisystem herunterzuladen:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Damit wird die Datei in eine lokale Datei mit dem Namen **testfile.txt** heruntergeladen.

5. Geben Sie zum Hochladen der lokalen Datei in eine neue Datei mit dem Namen **testupload.txt** in den SAS-Speicher folgenden Befehl an:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Sie erhalten eine Meldung wie den folgenden Text:

    ```output
    put: java.io.IOException
    ```

    Dieser Fehler tritt auf, weil der Speicherort nur einen Lese- und Auflistungszugriff zulässt. Geben Sie den folgenden Befehl an, um die Daten im Standardspeicher des Clusters abzulegen, der Schreibzugriff zulässt:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Dieses Mal sollte der Vorgang erfolgreich abgeschlossen werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Ihrem HDInsight-Cluster Speicher mit eingeschränktem Zugriff hinzufügen, können Sie sich mit anderen Möglichkeiten des Arbeitens mit Daten in Ihrem Cluster vertraut machen:

* [Verwenden von SSH mit HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Autorisieren von Benutzern für Apache Ambari-Ansichten](hdinsight-authorize-users-to-ambari.md)
