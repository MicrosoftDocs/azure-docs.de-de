---
title: Verwenden von Data Lake Storage Gen1 mit Hadoop in Azure HDInsight
description: Erfahren Sie, wie Sie Daten in Azure Data Lake Storage Gen1 abfragen und die Ergebnisse Ihrer Analyse speichern.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 890cd7080447649396855bfbe051dca4470a4564
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546307"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern

> [!Note] 
> Stellen Sie neue HDInsight-Cluster mithilfe von [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) für verbesserte Leistung und neue Funktionen bereit.

Zum Analysieren von Daten im HDInsight-Cluster können Sie die Daten in [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) oder [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) speichern. Alle Speichervarianten ermöglichen das sichere Löschen von HDInsight-Clustern, die für Berechnungen verwendet werden, ohne dass Benutzerdaten verloren gehen.

In diesem Artikel erfahren Sie, wie Data Lake Storage Gen1 mit HDInsight-Clustern funktioniert. Weitere Informationen zur Funktionsweise von Azure Store mit HDInsight-Clustern finden Sie unter [Verwenden von Azure Storage mit Azure HDInsight-Clustern](hdinsight-hadoop-use-blob-storage.md). Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Da auf Data Lake Storage Gen1 immer über einen sicheren Kanal zugegriffen wird, ist kein `adls`-Dateisystem-Schemaname vorhanden. Sie verwenden immer `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Verfügbarkeit für HDInsight-Cluster

Apache Hadoop unterstützt eine Variante des Standarddateisystems. Das Standarddateisystem gibt ein Standardschema und eine Standardautorität vor. Es kann auch zur Auflösung relativer Pfade verwendet werden. Bei der Erstellung des HDInsight-Clusters können Sie einen Blobcontainer in Azure Storage als Standarddateisystem angeben. Mit HDInsight 3.5 oder einer neueren Version können Sie mit einigen wenigen Ausnahmen Azure Storage oder Azure Data Lake Storage Gen1 als Standarddateisystem auswählen. 

Für HDInsight-Cluster kann Data Lake Storage Gen1 auf zwei Arten verwendet werden:

* Als Standardspeicher
* Als zusätzlicher Speicher mit Azure Storage Blob als Standardspeicher

Zum aktuellen Zeitpunkt unterstützen nur einige der HDInsight-Clustertypen/-versionen Data Lake Storage Gen1 als Konten für Standardspeicher und Zusatzspeicher:

| HDInsight-Clustertyp | Data Lake Storage Gen1 als Standardspeicher | Data Lake Storage Gen1 als zusätzlicher Speicher| Notizen |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight-Version 4.0 | Nein | Nein |ADLS Gen1 wird mit HDInsight 4.0 nicht unterstützt |
| HDInsight-Version 3.6 | Ja | Ja | Mit Ausnahme von HBase|
| HDInsight-Version 3.5 | Ja | Ja | Mit Ausnahme von HBase|
| HDInsight-Version 3.4 | Nein | Ja | |
| HDInsight, Version 3.3 | Nein | Nein | |
| HDInsight, Version 3.2 | Nein | Ja | |
| Storm | | |Sie können Data Lake Storage Gen1 verwenden, um dort Daten aus einer Storm-Topologie zu schreiben. Sie können Data Lake Storage auch zum Speichern von Verweisdaten verwenden, die anschließend von einer Storm-Topologie gelesen werden.|

> [!WARNING]  
> HDInsight HBase wird mit Azure Data Lake Storage Gen1 nicht unterstützt.

Das Verwenden von Data Lake Storage Gen1 als zusätzliches Speicherkonto wirkt sich nicht auf die Leistung oder die Fähigkeit aus, Daten aus dem Cluster in Azure Storage zu lesen bzw. zu schreiben.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Verwenden von Data Lake Storage Gen1 als Standardspeicher

Wenn HDInsight mit Data Lake Storage Gen1 als Standardspeicher bereitgestellt wird, werden die clusterbezogenen Dateien in `adl://mydatalakestore/<cluster_root_path>/` gespeichert, wobei `<cluster_root_path>` der Name eines Ordners ist, den Sie in Data Lake Storage erstellen. Indem Sie einen Stammpfad für jeden Cluster angeben, können Sie dasselbe Data Lake Storage-Konto für mehrere Cluster verwenden. Beispielsweise können Sie das folgende Setup verwenden:

* Cluster1 kann den Pfad `adl://mydatalakestore/cluster1storage` nutzen.
* Cluster2 kann den Pfad `adl://mydatalakestore/cluster2storage` nutzen.

Beachten Sie, dass für beide Cluster dasselbe Data Lake Storage Gen1-Konto **mydatalakestore** verwendet wird. Jeder Cluster hat in Data Lake Storage Zugriff auf sein eigenes Stammdateisystem. Bei der Bereitstellung im Azure-Portal werden Sie aufgefordert, für den Stammpfad einen Ordnernamen wie **/clusters/\<Clustername>** zu verwenden.

Um Data Lake Storage Gen1 als Standardspeicher verwenden zu können, müssen Sie dem Dienstprinzipal Zugriff auf die folgenden Pfade gewähren:

- Den Data Lake Storage Gen1-Kontostamm.  Beispiel: adl://mydatalakestore/.
- Den Ordner für alle Clusterordner.  Beispiel: adl://mydatalakestore/clusters.
- Den Ordner für den Cluster.  Beispiel: adl://mydatalakestore/clusters/cluster1storage.

Weitere Informationen zum Erstellen von Dienstprinzipalen und zum Gewähren des Zugriffs für diese finden Sie unter „Konfigurieren des Zugriffs auf Data Lake Storage“.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahieren eines Zertifikats aus Azure Key Vault für die Verwendung in der Clustererstellung

Wenn Sie Azure Data Lake Storage Gen1 als Standardspeicher für einen neuen Cluster einrichten möchten und das Zertifikat für den Dienstprinzipal in Azure Key Vault gespeichert ist, sind einige zusätzliche Schritte erforderlich, um das Zertifikat in das richtige Format zu konvertieren. Die folgenden Codeausschnitte zeigen die Durchführung der Konvertierung.

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

Sie können Data Lake Storage Gen1 zudem als zusätzlichen Speicher für den Cluster verwenden. In solchen Fällen kann der Clusterstandardspeicher ein Azure Storage Blob- oder ein Data Lake Storage-Konto sein. Wenn Sie HDInsight-Aufträge mit den in Data Lake Storage gespeicherten Daten als zusätzlichem Speicher ausführen, müssen Sie den vollqualifizierten Pfad zu den Dateien verwenden. Beispiel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Beachten Sie, dass die URL jetzt keinen **cluster_root_path** enthält. Dies liegt daran, dass Data Lake Storage hier kein Standardspeicher ist. Sie müssen also nur den Pfad zu den Dateien angeben.

Um Data Lake Storage Gen1 als zusätzlichen Speicher verwenden zu können, müssen Sie lediglich dem Dienstprinzipal Zugriff auf die Pfade gewähren, in denen Ihre Dateien gespeichert sind.  Beispiel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Weitere Informationen zum Erstellen von Dienstprinzipalen und zum Gewähren des Zugriffs für diese finden Sie unter „Konfigurieren des Data Lake Storage-Zugriffs“.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Verwenden von mehreren Data Lake Storage-Konten

Das Hinzufügen eines Data Lake Storage-Kontos als Zusatz und das Hinzufügen von mehreren Data Lake Storage-Konten wird erreicht, indem Sie dem HDInsight-Cluster den Zugriff auf Daten in einem oder mehreren Data Lake Storage-Konten gewähren. Siehe „Konfigurieren des Data Lake Storage-Zugriffs“.

## <a name="configure-data-lake-storage-access"></a>Konfigurieren des Data Lake Storage-Zugriffs

Um den Zugriff auf Data Lake Storage von Ihrem HDInsight-Cluster aus zu konfigurieren, benötigen Sie einen Azure Active Directory-Dienstprinzipal (Azure AD). Nur ein Azure AD-Administrator kann einen Dienstprinzipal erstellen. Der Dienstprinzipal muss mit einem Zertifikat erstellt werden. Weitere Informationen finden Sie unter [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) (Schnellstart: Einrichten von Clustern in HDInsight) und [Erstellen eines Dienstprinzipals mit selbstsigniertem Zertifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Wenn Sie Azure Data Lake Storage Gen1 als zusätzlichen Speicher für HDInsight-Cluster verwenden, wird dringend empfohlen, diesen Vorgang beim Erstellen des Clusters auszuführen, wie in diesem Artikel beschrieben. Das Hinzufügen von Azure Data Lake Storage Gen1 zu einem vorhandenen HDInsight-Cluster als zusätzlicher Speicher wird nicht unterstützt.
>

## <a name="access-files-from-the-cluster"></a>Zugreifen auf Dateien aus dem Cluster

Es gibt mehrere Möglichkeiten, wie Sie auf die Dateien in Data Lake Storage über einen HDInsight-Cluster zugreifen können.

* **Verwenden des vollqualifizierten Namens** Bei diesem Ansatz geben Sie den vollständigen Pfad zu der Datei an, auf die Sie zugreifen möchten.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Verwenden des verkürzten Pfadformats** Bei diesem Ansatz ersetzen Sie den Pfad bis zum Clusterstamm durch „adl:///“. Im obigen Beispiel können Sie also `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` durch `adl:///` ersetzen.

        adl:///<file path>

* **Verwenden des relativen Pfads** Bei diesem Ansatz geben Sie nur den relativen Pfad zu der Datei an, auf die Sie zugreifen möchten. Der vollständige Pfad zur Datei lautet beispielsweise:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Sie können auf die gleiche Datei „sample.log“ zugreifen, indem Sie stattdessen diesen relativen Pfad verwenden:

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1

Unter den folgenden Links finden Sie eine ausführliche Anleitung, wie Sie HDInsight-Cluster mit Zugriff auf Data Lake Storage Gen1 erstellen.

* [Verwenden des Portals](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Erstellen von HDInsight-Clustern mit Azure Data Lake Storage Gen1 als Standardspeicher mithilfe von PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen1 (als zusätzlichem Speicher) mithilfe von Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Erstellen eines HDInsight-Clusters mit Data Lake Store mithilfe einer Azure Resource Manager-Vorlage](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aktualisieren des HDInsight-Zertifikats für den Data Lake Storage Gen1-Zugriff

Im folgenden Beispiel liest PowerShell-Code ein Zertifikat aus einer lokalen Datei oder Azure Key Vault und aktualisiert Ihren HDInsight-Cluster mit dem neuen Zertifikat für den Zugriff auf Azure Data Lake Storage Gen1. Geben Sie Ihren eigenen HDInsight-Clusternamen, den Ressourcengruppennamen, die Abonnement-ID, die App-ID und den lokalen Pfad zum Zertifikat an. Geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

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
In diesem Artikel wurde beschrieben, wie Sie HDFS-kompatibles Azure Data Lake Storage Gen1 mit HDInsight verwenden. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten strukturierten und unstrukturierten Daten zu entsperren.

Weitere Informationen finden Sie unter

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) (Schnellstart: Einrichten von Clustern in HDInsight)
* [Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen1 (als zusätzlichem Speicher) mithilfe von Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Apache Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Azure Storage Shared Access Signatures zum Einschränken des Zugriffs auf Daten mit HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
