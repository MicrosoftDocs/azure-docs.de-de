---
title: Erstellen eines Azure Service Fabric-Clusters
description: Hier erfahren Sie, wie mithilfe von Azure Resource Manager ein sicherer Service Fabric-Cluster in Azure eingerichtet wird.  Sie können eine Standardvorlage oder Ihre eigene Clustervorlage zum Erstellen eines Clusters verwenden.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: fd2c472d24f305e42f1706e5fc49168ccde2a580
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008666"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ein [Azure Service Fabric-Cluster](service-fabric-deploy-anywhere.md) ist eine vernetzte Gruppe von virtuellen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden.  Bei einem Service Fabric-Cluster, der in Azure ausgeführt wird, handelt es sich um eine Azure-Ressource, die mithilfe von Azure Resource Manager bereitgestellt wird. Dieser Artikel beschreibt die Bereitstellung eines sicheren Service Fabric-Clusters in Azure mithilfe von Resource Manager. Sie können dazu eine Standardclustervorlage oder eine benutzerdefinierte Vorlage verwenden.  Wenn Sie noch keine benutzerdefinierte Vorlage haben, erfahren Sie [hier, wie eine erstellt wird](service-fabric-cluster-creation-create-template.md).

Die Art der Sicherheit, die zur Sicherung des Clusters ausgewählt wurde (z. B: Windows Identity, X509 usw.) muss für die erste Erstellung des Clusters angegeben werden und kann danach nicht mehr geändert werden. Lesen Sie vor dem Einrichten eines Clusters den Artikel [Szenarien für die Clustersicherheit in Service Fabric][service-fabric-cluster-security]. In Azure verwendet Service Fabric das X.509-Zertifikat zum Schützen Ihres Clusters und seiner Endpunkte sowie zum Authentifizieren von Clients und Verschlüsseln von Daten. Azure Active Directory wird ebenfalls empfohlen, um den Zugriff auf Verwaltungsendpunkte zu schützen. Weitere Informationen finden Sie unter [Einrichten der Azure Active Directory-Authentifizierung](service-fabric-cluster-creation-setup-aad.md).

Wenn Sie einen Produktionscluster für die Ausführung von Produktionsworkloads erstellen, empfehlen wir, davor die [Prüfliste für die Produktionsbereitschaft](service-fabric-production-readiness-checklist.md) durchzulesen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen 
Verwenden Sie in diesem Artikel die Service Fabric-RM-Powershell- oder Azure CLI- Module zum Bereitstellen eines Clusters:

* [Azure PowerShell 4.1 und höher][azure-powershell]
* [Azure CLI, Version 2.0 und höher][azure-CLI]

Die Referenzdokumentation für die Service Fabric-Module finden Sie hier:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [az SF CLI module](/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich vor der Ausführung des ersten Befehls in diesem Artikel bei Azure an.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Erstellen eines neuen Clusters mithilfe eines vom System generierten selbstsignierten Zertifikats

Verwenden Sie die folgenden Befehle zum Erstellen eines Clusters, der mit einem systemgenerierten selbstsignierten Zertifikat gesichert ist. Dieser Befehl richtet ein primäres Clusterzertifikat ein, das für die Clustersicherheit und zum Einrichten von Administratorzugriff verwendet wird, um mithilfe dieses Zertifikats Verwaltungsvorgänge auszuführen.  Selbstsignierte Zertifikate empfehlen sich für das Sichern von Testclustern.  Produktionscluster sollten mit einem Zertifikat einer Zertifizierungsstelle (ZS) gesichert werden.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Verwenden der im Modul inbegriffenen Standardvorlage

Verwenden Sie den folgenden Befehl, um anhand der Standardvorlage einen Cluster schnell und unter Angabe der minimal erforderlichen Parameter zu erstellen.

Die Vorlage, die verwendet wird, ist unter den [Azure Service Fabric-Vorlagenbeispielen: Windows-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) und [Ubuntu-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure) verfügbar.

Mit dem folgenden Befehl können entweder Windows- oder Linux-Cluster erstellt werden. Geben Sie das Betriebssystem entsprechend an. Die PowerShell-/CLI-Befehle geben außerdem das Zertifikat im angegebenen *CertificateOutputFolder*-Ordner zurück. Stellen Sie daher sicher, dass der Zertifikatordner bereits erstellt ist. Der Befehl akzeptiert auch andere Parameter wie VM-SKU.

> [!NOTE]
> Der folgende PowerShell-Befehl funktioniert nur mit dem `Az`-Modul von Azure PowerShell. Führen Sie den PowerShell-Befehl „Get-Module Az“ aus, um die aktuelle Version von Azure Resource Manager PowerShell zu überprüfen. Folgen Sie [diesem Link](/powershell/azure/install-Az-ps), um ein Upgrade für Ihre Azure Resource Manager PowerShell-Version durchzuführen. 
>
>

Bereitstellen des Clusters mithilfe von PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Bereitstellen des Clusters mithilfe der Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Verwenden einer eigenen benutzerdefinierten Vorlage

Wenn Sie eine benutzerdefinierte Vorlage für Ihre speziellen Anforderungen erstellen müssen, wird dringend empfohlen, dass Sie mit einer der Vorlagen beginnen, die unter den [Azure Service Fabric-Vorlagenbeispielen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) verfügbar sind. Erfahren Sie, wie Sie [Ihre Clustervorlage anpassen][customize-your-cluster-template].

Wenn Sie bereits über eine benutzerdefinierte Vorlage verfügen, überprüfen Sie, ob alle drei zertifikatbezogenen Parameter in der Vorlage und der Parameterdatei wie folgt benannt wurden und die Werte wie folgt NULL sind:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Bereitstellen des Clusters mithilfe von PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Bereitstellen des Clusters mithilfe der Azure CLI:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Erstellen eines neuen Clusters mit Ihrem eigenen X.509-Zertifikat

Verwenden Sie den folgenden Befehl zum Erstellen eines Clusters, wenn Sie über ein Zertifikat verfügen, das Sie zum Schützen des Clusters verwenden möchten.

Wenn dies ein von einer ZS signiertes Zertifikat ist, das Sie letztendlich auch für andere Zwecke einsetzen möchten, dann empfiehlt es sich, eine unterschiedliche Ressourcengruppe speziell für Ihren Schlüsseltresor bereitzustellen. Es empfiehlt sich, den Schlüsseltresor in einer eigenen Ressourcengruppe zu platzieren. Dadurch können Sie die Compute- und Speicherressourcengruppen (einschließlich der Ressourcengruppe mit Ihrem Service Fabric-Cluster) entfernen, ohne Ihre Schlüssel und geheimen Schlüssel zu verlieren. **Die Ressourcengruppe mit Ihrem Schlüsseltresor *muss sich in der gleichen Region* befinden wie der Cluster, der den Tresor verwendet.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Verwenden der im Modul enthaltenen standardmäßigen „5 Node 1“-Knotentypvorlage
Die verwendete Vorlage ist in den [Azure-Beispielen: Windows-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) und [Ubuntu-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure) verfügbar.

Bereitstellen des Clusters mithilfe von PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Bereitstellen des Clusters mithilfe der Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Verwenden einer eigenen benutzerdefinierten Clustervorlage
Wenn Sie eine benutzerdefinierte Vorlage für Ihre speziellen Anforderungen erstellen müssen, wird dringend empfohlen, dass Sie mit einer der Vorlagen beginnen, die unter den [Azure Service Fabric-Vorlagenbeispielen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) verfügbar sind. Erfahren Sie, wie Sie [Ihre Clustervorlage anpassen][customize-your-cluster-template].

Wenn Sie bereits über eine benutzerdefinierte Vorlage verfügen, überprüfen Sie, ob alle drei zertifikatbezogenen Parameter in der Vorlage und der Parameterdatei wie folgt benannt wurden und die Werte wie folgt NULL sind.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Bereitstellen des Clusters mithilfe von PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Bereitstellen des Clusters mithilfe der Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Verwenden eines Zeigers auf ein Geheimnis in einem Schlüsseltresor

Zur Verwendung eines vorhandenen Schlüsseltresors muss dieser [für die Bereitstellung aktiviert werden](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI), damit der Compute-Ressourcenanbieter Zertifikate daraus abrufen und ihn auf Clusterknoten installieren kann.

Bereitstellen des Clusters mithilfe von PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Bereitstellen des Clusters mithilfe der Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Nächste Schritte
Sie verfügen jetzt über einen sicheren Cluster, der in Azure ausgeführt wird. Als Nächstes [stellen Sie eine Verbindung mit dem Cluster her](service-fabric-connect-to-secure-cluster.md) und erfahren, wie Sie [Anwendungsgeheimnisse verwalten](service-fabric-application-secret-management.md).

Informationen zur JSON-Syntax und zu den Eigenschaften, die in einer Vorlage verwendet werden sollen, finden Sie in der Vorlagenreferenz [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
