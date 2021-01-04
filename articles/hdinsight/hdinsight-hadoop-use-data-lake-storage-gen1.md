---
title: Verwenden von Data Lake Storage Gen1 mit Hadoop in Azure HDInsight
description: Erfahren Sie, wie Sie Daten in Azure Data Lake Storage Gen1 abfragen und die Ergebnisse Ihrer Analyse speichern.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 5949bab7bdf11b11e0ff71f9054098ed83d95ab4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539835"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern

> [!Note]
> Stellen Sie neue HDInsight-Cluster mithilfe von [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) für verbesserte Leistung und neue Funktionen bereit.

Zum Analysieren von Daten in einem HDInsight-Cluster können Sie die Daten in [`Azure Blob storage`](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) oder [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) speichern. Alle Speichervarianten ermöglichen das sichere Löschen von HDInsight-Clustern, die für Berechnungen verwendet werden, ohne dass Benutzerdaten verloren gehen.

In diesem Artikel erfahren Sie, wie Data Lake Storage Gen1 mit HDInsight-Clustern funktioniert. Weitere Informationen zur Funktionsweise von Azure Blob Storage mit HDInsight-Clustern finden Sie unter [Verwenden von Azure Storage mit Azure HDInsight-Clustern](hdinsight-hadoop-use-blob-storage.md). Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Da auf Data Lake Storage Gen1 immer über einen sicheren Kanal zugegriffen wird, ist kein `adls`-Dateisystem-Schemaname vorhanden. Sie verwenden immer `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Verfügbarkeit für HDInsight-Cluster

Apache Hadoop unterstützt eine Variante des Standarddateisystems. Das Standarddateisystem gibt ein Standardschema und eine Standardautorität vor. Es kann auch zur Auflösung relativer Pfade verwendet werden. Geben Sie während der Erstellung des HDInsight-Clusters einen Blobcontainer in Azure Storage als Standarddateisystem an. Mit HDInsight 3.5 und neueren Versionen können Sie alternativ bis auf wenige Ausnahmen Azure Blob Storage oder Azure Data Lake Storage Gen1 als Standarddateisystem auswählen. Cluster und Speicherkonto müssen sich in der gleichen Region befinden.

Für HDInsight-Cluster kann Data Lake Storage Gen1 auf zwei Arten verwendet werden:

* Als Standardspeicher
* Als zusätzlicher Speicher mit Azure Blob Storage als Standardspeicher

Derzeit unterstützen nur einige HDInsight-Clustertypen/-versionen Data Lake Storage Gen1 als Konten für Standardspeicher und Zusatzspeicher:

| HDInsight-Clustertyp | Data Lake Storage Gen1 als Standardspeicher | Data Lake Storage Gen1 als zusätzlicher Speicher| Notizen |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight-Version 4.0 | Nein | Nein |ADLS Gen1 wird mit HDInsight 4.0 nicht unterstützt |
| HDInsight-Version 3.6 | Ja | Ja | Mit Ausnahme von HBase|
| HDInsight-Version 3.5 | Ja | Ja | Mit Ausnahme von HBase|
| HDInsight-Version 3.4 | Nein | Ja | |
| HDInsight, Version 3.3 | Nein | Nein | |
| HDInsight, Version 3.2 | Nein | Ja | |
| Storm | | |Sie können Data Lake Storage Gen1 verwenden, um dort Daten aus einer Storm-Topologie zu schreiben. Sie können Data Lake Storage Gen1 auch für Verweisdaten verwenden, die anschließend von einer Storm-Topologie gelesen werden können.|

> [!WARNING]  
> HDInsight HBase wird mit Azure Data Lake Storage Gen1 nicht unterstützt.

Die Verwendung von Data Lake Storage Gen1 als Konto für zusätzlichen Speicher hat keine Auswirkungen auf die Leistung. Ebenso wenig ist die Fähigkeit zum Lesen oder Schreiben in Azure Blob Storage über den Cluster betroffen.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Verwenden von Data Lake Storage Gen1 als Standardspeicher

Wenn HDInsight mit Data Lake Storage Gen1 als Standardspeicher bereitgestellt wird, werden die clusterbezogenen Dateien in `adl://mydatalakestore/<cluster_root_path>/` gespeichert, wobei `<cluster_root_path>` der Name eines Ordners ist, den Sie in Data Lake Storage erstellen. Indem Sie einen Stammpfad für jeden Cluster angeben, können Sie dasselbe Data Lake Storage-Konto für mehrere Cluster verwenden. Beispielsweise können Sie das folgende Setup verwenden:

* Cluster1 kann den Pfad `adl://mydatalakestore/cluster1storage` nutzen.
* Cluster2 kann den Pfad `adl://mydatalakestore/cluster2storage` nutzen.

Beachten Sie, dass für beide Cluster dasselbe Data Lake Storage Gen1-Konto **mydatalakestore** verwendet wird. Jeder Cluster hat in Data Lake Storage Zugriff auf sein eigenes Stammdateisystem. Bei der Bereitstellung im Azure-Portal werden Sie aufgefordert, für den Stammpfad einen Ordnernamen wie **/clusters/\<clustername>** zu verwenden.

Um Data Lake Storage Gen1 als Standardspeicher zu verwenden, müssen Sie dem Dienstprinzipal Zugriff auf die folgenden Pfade gewähren:

* Den Data Lake Storage Gen1-Kontostamm.  Beispiel: adl://mydatalakestore/.
* Den Ordner für alle Clusterordner.  Beispiel: adl://mydatalakestore/clusters.
* Den Ordner für den Cluster.  Beispiel: adl://mydatalakestore/clusters/cluster1storage.

Weitere Informationen zum Erstellen von Dienstprinzipalen und zum Gewähren des Zugriffs für diese finden Sie unter „Konfigurieren des Data Lake Storage-Zugriffs“.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahieren eines Zertifikats aus Azure Key Vault für die Verwendung in der Clustererstellung

Wenn das Zertifikat für den Dienstprinzipal in Azure Key Vault gespeichert ist, müssen Sie das Zertifikat in das richtige Format konvertieren. Die folgenden Codeausschnitte veranschaulichen die Durchführung der Konvertierung.

Laden Sie zuerst das Zertifikat aus Key Vault herunter, und extrahieren Sie den `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Konvertieren Sie als Nächstes `SecretValueText` in ein Zertifikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Anschließend können Sie mit dem `$identityCertificate` einen neuen Cluster wie im folgenden Codeausschnitt bereitstellen:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Verwenden von Data Lake Storage Gen1 als zusätzlicher Speicher

Sie können Data Lake Storage Gen1 zudem als zusätzlichen Speicher für den Cluster verwenden. In solchen Fällen kann der Cluster-Standardspeicher ein Azure Blob Storage- oder Azure Data Lake Storage Gen1-Konto sein. Wenn Sie HDInsight-Aufträge für die in Azure Data Lake Storage Gen1 als zusätzlichem Speicher gespeicherten Daten ausführen, verwenden Sie den vollqualifizierten Pfad. Beispiel:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Die URL enthält jetzt keinen **cluster_root_path** . Dies liegt daran, dass Data Lake Storage in diesem Fall kein Standardspeicher ist. Sie müssen daher nur den Pfad zu den Dateien angeben.

Um Data Lake Storage Gen1 als zusätzlichen Speicher zu verwenden, gewähren Sie dem Dienstprinzipal Zugriff auf die Pfade, in denen Ihre Dateien gespeichert sind.  Beispiel:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Weitere Informationen zum Erstellen von Dienstprinzipalen und zum Gewähren des Zugriffs für diese finden Sie unter „Konfigurieren des Data Lake Storage-Zugriffs“.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Verwenden mehrerer Data Lake Storage Gen1-Konten

Es ist möglich, ein Data Lake Storage-Konto als zusätzlichen Speicher hinzuzufügen und auch mehrere Data Lake Storage-Konten hinzuzufügen. Erteilen Sie dem HDInsight-Cluster Berechtigungen für Daten in mindestens einem Data Lake Storage-Konto. Weitere Informationen finden Sie im folgenden Abschnitt „Konfigurieren des Data Lake Storage Gen1-Zugriffs“.

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurieren des Data Lake Storage Gen1-Zugriffs

Sie benötigen einen Azure Active Directory-Dienstprinzipal (Azure AD), um den Zugriff auf Azure Data Lake Storage Gen1 von Ihrem HDInsight-Cluster aus zu konfigurieren. Nur ein Azure AD-Administrator kann einen Dienstprinzipal erstellen. Der Dienstprinzipal muss mit einem Zertifikat erstellt werden. Weitere Informationen finden Sie unter [Quickstart: Set up clusters in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) (Schnellstart: Einrichten von Clustern in HDInsight) und [Erstellen eines Dienstprinzipals mit selbstsigniertem Zertifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Wenn Sie Azure Data Lake Storage Gen1 als zusätzlichen Speicher für HDInsight-Cluster verwenden, wird dringend empfohlen, diesen Vorgang beim Erstellen des Clusters auszuführen, wie in diesem Artikel beschrieben. Das Hinzufügen von Azure Data Lake Storage Gen1 zu einem vorhandenen HDInsight-Cluster als zusätzlicher Speicher wird nicht unterstützt.

Weitere Informationen zum Zugriffssteuerungsmodell finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Zugreifen auf Dateien aus dem Cluster

Es gibt mehrere Möglichkeiten, wie Sie auf die Dateien in Data Lake Storage über einen HDInsight-Cluster zugreifen können.

* **Verwenden des vollqualifizierten Namens** Bei diesem Ansatz geben Sie den vollständigen Pfad zu der Datei an, auf die Sie zugreifen möchten.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Verwenden des verkürzten Pfadformats** Bei diesem Ansatz ersetzen Sie den Pfad bis zum Clusterstamm durch:

    ```
    adl:///<file path>
    ```

* **Verwenden des relativen Pfads** Bei diesem Ansatz geben Sie nur den relativen Pfad zu der Datei an, auf die Sie zugreifen möchten.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Beispiele für Datenzugriff

Die Beispiele basieren auf einer [ssh-Verbindung](./hdinsight-hadoop-linux-use-ssh-unix.md) mit dem Hauptknoten des Clusters. In den Beispielen werden alle drei URI-Schemas verwendet. Ersetzen Sie `DATALAKEACCOUNT` und `CLUSTERNAME` durch die entsprechenden Werte.

#### <a name="a-few-hdfs-commands"></a>Ein paar HDFS-Befehle

1. Erstellen von Dateien im lokalen Speicher.

    ```bash
    touch testFile.txt
    ```

1. Erstellen von Verzeichnissen im Clusterspeicher.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopieren von Daten aus dem lokalen Speicher in den Clusterspeicher.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Auflisten von Verzeichnisinhalten im Clusterspeicher.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Erstellen einer Hive-Tabelle

Zu Zwecken der Veranschaulichung werden drei Speicherorte gezeigt. Verwenden Sie für die tatsächliche Ausführung nur einen der `LOCATION`-Einträge.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Bestimmen des Speicherpfads aus Ambari

Um den vollständigen Pfad zum konfigurierten Standardspeicher zu ermitteln, navigieren Sie zu **HDFS** > **Konfigurationen** , und geben Sie `fs.defaultFS` in das Filtereingabefeld ein.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1

Unter den folgenden Links finden Sie eine ausführliche Anleitung, wie Sie HDInsight-Cluster mit Zugriff auf Data Lake Storage Gen1 erstellen.

* [Verwenden des Portals](./hdinsight-hadoop-provision-linux-clusters.md)
* [Erstellen von HDInsight-Clustern mit Azure Data Lake Storage Gen1 als Standardspeicher mithilfe von PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen1 (als zusätzlichem Speicher) mithilfe von Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Erstellen eines HDInsight-Clusters mit Data Lake Store mithilfe einer Azure Resource Manager-Vorlage](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aktualisieren des HDInsight-Zertifikats für den Data Lake Storage Gen1-Zugriff

Im folgenden Beispiel liest PowerShell-Code ein Zertifikat aus einer lokalen Datei oder Azure Key Vault und aktualisiert Ihren HDInsight-Cluster mit dem neuen Zertifikat für den Zugriff auf Azure Data Lake Storage Gen1. Geben Sie Ihren eigenen HDInsight-Clusternamen, den Ressourcengruppennamen, die Abonnement-ID, die `app ID` und den lokalen Pfad zum Zertifikat an. Geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie HDFS-kompatibles Azure Data Lake Storage Gen1 mit HDInsight verwenden. Mit diesem Speicher können Sie anpassbare, langfristige Archivierungslösungen für die Datenerfassung erstellen. Mit HDInsight können Sie zudem die Informationen der gespeicherten strukturierten und unstrukturierten Daten verarbeiten.

Weitere Informationen finden Sie unter

* [Schnellstart: Set up clusters in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) (Schnellstart: Einrichten von Clustern in HDInsight)
* [Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen1 (als zusätzlichem Speicher) mithilfe von Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Hochladen von Daten in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Azure Blob Storage-Shared Access Signatures zum Einschränken des Zugriffs auf Daten mit HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)