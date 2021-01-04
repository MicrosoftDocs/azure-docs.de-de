---
title: 'PowerShell: HDInsight-Cluster mit Data Lake Storage Gen1 – Azure'
description: Verwenden von Azure PowerShell zum Erstellen und Nutzen von HDInsight-Clustern mit Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 675f68a36963d19f42cb7c0c5d49ae8c4f0006f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010536"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Erstellen von HDInsight-Clustern mit Azure Data Lake Storage Gen1 als Standardspeicher mithilfe von PowerShell

> [!div class="op_single_selector"]
> * [Verwenden des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Verwenden von PowerShell (für Standardspeicher)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Verwenden von PowerShell (für zusätzlichen Speicher)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Verwenden des Ressourcen-Managers](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Erfahren Sie, wie Sie mithilfe von Azure PowerShell Azure HDInsight-Cluster mit Azure Data Lake Storage Gen1 als Standardspeicher konfigurieren. Anleitungen zum Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 als zusätzlichem Speicher finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 (als zusätzlichem Speicher) mithilfe von Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md).

Hier sind einige wichtige Überlegungen zur Verwendung von HDInsight mit Data Lake Storage Gen1 aufgeführt:

* Die Option zum Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1 als Standardspeicher ist für die HDInsight-Versionen 3.5 und 3.6 verfügbar.

* Die Option zum Erstellen von HDInsight-Clustern mit Zugriff auf Data Lake Storage Gen1 als Standardspeicher ist für HDInsight Premium-Cluster *nicht verfügbar*.

Befolgen Sie die Anweisungen in den nächsten fünf Abschnitten, um HDInsight zum Arbeiten mit Data Lake Storage Gen1 mithilfe von PowerShell zu konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bevor Sie mit diesem Tutorial beginnen, stellen Sie sicher, dass Sie die folgenden Anforderungen erfüllen:

* **Ein Azure-Abonnement**: Navigieren Sie zu [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 oder höher:** Siehe [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).
* **Windows Software Development Kit (SDK):** Wechseln Sie zu [Downloads und Tools für Windows 10](https://dev.windows.com/downloads), um das Windows SDK zu installieren. Das SDK dient zum Erstellen eines Sicherheitszertifikats.
* **Azure Active Directory-Dienstprinzipal:** In diesem Tutorial wird beschrieben, wie ein Dienstprinzipal in Azure Active Directory (Azure AD) erstellt wird. Sie müssen jedoch Azure AD-Administrator sein, um einen Dienstprinzipal erstellen zu können. Wenn Sie Administrator sind, können Sie diese Voraussetzung ignorieren und mit dem Tutorial fortfahren.

    >[!NOTE]
    >Sie können einen Dienstprinzipal nur dann erstellen, wenn Sie ein Azure AD-Administrator sind. Ihr Azure AD-Administrator muss zunächst einen Dienstprinzipal erstellen, bevor Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 erstellen können. Der Dienstprinzipal muss mit einem Zertifikat erstellt werden, wie unter [Erstellen eines Dienstprinzipals mit Zertifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) beschrieben.
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Erstellen eines Azure Data Lake Storage Gen1-Kontos

Führen Sie die folgenden Schritte aus, um ein Data Lake Storage Gen1-Konto zu erstellen:

1. Öffnen Sie auf dem Desktop ein PowerShell-Fenster, und geben Sie anschließend die folgenden Codeausschnitte ein. Wenn Sie zum Anmelden aufgefordert werden, melden Sie sich als einer der Abonnementadministrator oder -besitzer an. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Wenn Sie den Data Lake Storage Gen1-Ressourcenanbieter registrieren und einen Fehler erhalten, der in etwa `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` lautet, ist Ihr Abonnement unter Umständen nicht für Data Lake Storage Gen1 zugelassen. Befolgen Sie die Anleitung unter [Erste Schritte mit Data Lake Storage Gen1 mithilfe des Azure-Portals](data-lake-store-get-started-portal.md), um Ihr Azure-Abonnement für Data Lake Storage Gen1 zu aktivieren.
    >

2. Ein Data Lake Storage Gen1-Konto wird einer Azure-Ressourcengruppe zugeordnet. Beginnen Sie, indem Sie eine Ressourcengruppe erstellen.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Folgende Ausgabe sollte angezeigt werden:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Erstellen Sie ein Data Lake Storage Gen1-Konto. Der angegebene Kontoname darf nur Kleinbuchstaben und Zahlen enthalten.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Folgendes sollte angezeigt werden:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

4. Wenn Sie Data Lake Storage Gen1 als Standardspeicher verwenden, müssen Sie einen Stammpfad angeben, in den clusterspezifische Dateien während der Clustererstellung kopiert werden. Verwenden Sie folgende Cmdlets, um einen Stammpfad zu erstellen, der im Ausschnitt **/clusters/hdiadlcluster** enthalten ist.

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Einrichten der Authentifizierung für rollenbasierten Zugriff auf Data Lake Storage Gen1
Jedes Azure-Abonnement ist mit einer Azure AD-Entität verknüpft. Benutzer und Dienste, die über das Azure-Portal oder die Azure Resource Manager-API auf Abonnementressourcen zugreifen, müssen sich zunächst bei Azure AD authentifizieren. Azure-Abonnements und -Dienste erhalten Zugriff, indem ihnen die entsprechende Rolle für eine Azure-Ressource zugewiesen wird. Für Dienste wird ein Dienstprinzipal in Azure AD den Dienst identifizieren.

In diesem Abschnitt wird veranschaulicht, wie für einen Anwendungsdienst, z.B. HDInsight, Zugriff auf eine Azure-Ressource (das zuvor von Ihnen erstellte Data Lake Storage Gen1-Konto) erteilt wird. Dazu erstellen Sie einen Dienstprinzipal für die Anwendung und weisen diesem Rollen über PowerShell zu.

Sie müssen Aufgaben in den folgenden zwei Abschnitten ausführen, um die Active Directory-Authentifizierung für Data Lake Storage Gen1 einzurichten.

### <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats
Stellen Sie sicher, dass Sie das [Windows SDK](https://dev.windows.com/en-us/downloads) installiert haben, bevor Sie mit den Schritten in diesem Abschnitt fortfahren. Sie müssen auch ein Verzeichnis erstellt haben, z.B. *C:\mycertdir*, in dem das Zertifikat erstellt werden soll.

1. Navigieren Sie im PowerShell-Fenster zu dem Speicherort, an dem Sie das Windows SDK installiert haben (normalerweise *C:\Programme (x86)\Windows Kits\10\bin\x86*), und verwenden Sie das Hilfsprogramm [MakeCert][makecert], um ein selbstsigniertes Zertifikat und einen privaten Schlüssel zu erstellen. Verwenden Sie die folgenden Befehle:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Sie werden aufgefordert, das Kennwort für den privaten Schlüssel einzugeben. Nach erfolgreicher Ausführung des Befehls sollten im von Ihnen angegebenen Zertifikatsverzeichnis die Dateien **CertFile.cer** und **mykey.pvk** enthalten sein.
2. Verwenden Sie das Hilfsprogramm [Pvk2Pfx][pvk2pfx], um die von MakeCert erstellten PVK- und CER-Dateien in eine PFX-Datei zu konvertieren. Führen Sie den folgenden Befehl aus:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Geben Sie bei Aufforderung das Kennwort für den privaten Schlüssel ein, das Sie bereits angegeben haben. Der Wert, den Sie für den Parameter **-po** angeben, ist das Kennwort, das der PFX-Datei zugeordnet ist. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollte im von Ihnen angegebenen Zertifikatsverzeichnis auch die Datei **CertFile.pfx** enthalten sein.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals
In diesem Abschnitt erstellen Sie einen Dienstprinzipal für eine Azure AD-Anwendung, weisen eine Rolle für den Dienstprinzipal zu, und authentifizieren Sie den Dienstprinzipal durch Bereitstellen eines Zertifikats. Führen Sie die folgenden Befehle zum Erstellen einer Anwendung in Azure AD aus.

1. Fügen Sie die folgenden Cmdlets im PowerShell-Konsolenfenster ein. Stellen Sie sicher, dass der Wert, den Sie für die **-DisplayName**-Eigenschaft angeben, eindeutig ist. Die Werte für **-HomePage** und **-IdentiferUris** sind Platzhalterwerte und werden nicht überprüft.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Erstellen Sie einen Dienstprinzipal, indem Sie die Anwendungs-ID verwenden.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Gewähren Sie dem Data Lake Storage Gen1-Stamm und allen Ordnern unter dem Stammpfad, den Sie zuvor angegeben haben, den Dienstprinzipalzugriff. Verwenden Sie die folgenden Cmdlets:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Erstellen eines HDInsight-Linux-Clusters mit Data Lake Storage Gen1 als Standardspeicher

In diesem Abschnitt erstellen Sie einen HDInsight Hadoop-Linux-Cluster mit Data Lake Storage Gen1 als Standardspeicher. Für dieses Release müssen sich der HDInsight-Cluster und Data Lake Storage Gen1 an demselben Standort befinden.

1. Rufen Sie die Abonnementmandanten-ID auf, und speichern Sie sie, um sie später zu verwenden.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Erstellen Sie den HDInsight-Cluster, indem Sie die folgenden Cmdlets verwenden:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Nachdem Sie das Cmdlet erfolgreich abgeschlossen wurde, sollte eine Ausgabe angezeigt werden, in denen die Clusterdetails aufgeführt werden.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Ausführen von Testaufträgen im HDInsight-Cluster für die Verwendung von Data Lake Storage Gen1
Nachdem Sie einen HDInsight-Cluster konfiguriert haben, können Sie Testaufträge ausführen, um zu testen, ob er auf Daten in Data Lake Storage Gen1 zugreifen kann. Führen Sie hierzu einen Hive-Beispielauftrag aus, um eine Tabelle zu erstellen, die in Data Lake Storage Gen1 unter *\<cluster root>/example/data/sample.log* bereits verfügbar ist.

In diesem Abschnitt erstellen Sie eine Secure Shell-Verbindung (SSH) zum HDInsight Linux-Cluster her, den Sie erstellt haben, und führen anschließend eine Hive-Beispielabfrage aus.

* Wenn Sie einen Windows-Client für die SSH-Verbindung mit dem Cluster verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Wenn Sie einen Linux-Client für die SSH-Verbindung mit dem Cluster verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Nachdem Sie die Verbindung hergestellt haben, starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mithilfe des folgenden Befehls:

    ```powershell
    hive
    ```

2. Verwenden Sie die Befehlszeilenschnittstelle, um die folgenden Anweisungen anzugeben. Hiermit können Sie eine neue Tabelle mit dem Namen **vehicles** (Fahrzeuge) erstellen, indem Sie die Beispieldaten in Data Lake Storage Gen1 verwenden:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Die Abfrageausgabe sollte in der SSH-Konsole angezeigt werden.

>[!NOTE]
>Der Pfad zu den Beispieldaten im obigen Befehl CREATE TABLE ist `adl:///example/data/`, wobei `adl:///` der Clusterstamm ist. Mit dem Beispiel des Clusterstamms, der in diesem Tutorial spezifisch ist, ist der Befehl `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Daher könnten Sie entweder die kürzere Alternative verwenden oder den vollständigen Pfad zum Clusterstamm angeben.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Zugreifen auf Data Lake Storage Gen1 mit HDFS-Befehlen (Hadoop Distributed File System)
Nachdem Sie den HDInsight-Cluster für die Verwendung von Data Lake Storage Gen1 konfiguriert haben, können Sie die HDFS-Shellbefehle verwenden, um auf den Speicher zuzugreifen.

In diesem Abschnitt stellen Sie eine SSH-Verbindung zum HDInsight Linux-Cluster her, den Sie erstellt haben. Anschließend führen Sie die HDFS-Befehle aus.

* Wenn Sie einen Windows-Client für die SSH-Verbindung mit dem Cluster verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Wenn Sie einen Linux-Client für die SSH-Verbindung mit dem Cluster verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Nachdem Sie die Verbindung hergestellt haben, können Sie die Dateien in Data Lake Storage Gen1 mithilfe des folgenden HDFS-Dateisystembefehls auflisten.

```azurepowershell
hdfs dfs -ls adl:///
```

Sie können auch den Befehl `hdfs dfs -put` verwenden, um Dateien in Data Lake Storage Gen1 hochzuladen, und dann mit `hdfs dfs -ls` überprüfen, ob der Upload der Dateien erfolgreich war.

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure-Portal: Erstellen eines HDInsight-Clusters für die Verwendung von Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx