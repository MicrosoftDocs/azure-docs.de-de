---
title: Erstellen eines Azure Service Fabric-Clusters mit allgemeinem Zertifikatnamen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Service Fabric-Cluster erstellen, für den ein Zertifikat mit allgemeinem Namen aus einer Vorlage verwendet wird.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: fe1adc0aef80066721ce0b80419c787fe25346a9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190813"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Bereitstellen eines Service Fabric-Clusters mit allgemeinem Zertifikatnamen anstelle eines Fingerabdrucks
Keine zwei Zertifikate können den gleichen Fingerabdruck haben, was ein Clusterzertifikatrollover oder die Verwaltung erschwert. Mehrere Zertifikate können jedoch den gleichen allgemeinen Namen oder den gleichen Antragsteller haben.  Cluster mit allgemeinen Zertifikatnamen vereinfachen die Zertifikatverwaltung. In diesem Artikel wird beschrieben, wie Sie einen Service Fabric-Cluster für die Verwendung des allgemeinen Zertifikatnamens (anstelle des Zertifikatfingerabdrucks) bereitstellen.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Abrufen eines Zertifikats
Fordern Sie zunächst ein Zertifikat von einer [Zertifizierungsstelle (CA)](https://wikipedia.org/wiki/Certificate_authority) an.  Der allgemeine Name des Zertifikats sollte für die benutzerdefinierte Domäne bestimmt sein, die Sie besitzen, und bei einer Domänenregistrierungsstelle gekauft sein. Beispiel: „azureservicefabricbestpractices.com“; da nur Microsoft-Mitarbeiter Zertifikate für MS-Domänen bereitstellen können, können Sie die DNS-Namen von Ihrem LB oder Traffic Manager nicht als allgemeine Namen für Ihr Zertifikat verwenden, und Sie müssen eine [Azure DNS-Zone](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) bereitstellen, wenn Ihre benutzerdefinierte Domäne in Azure auflösbar sein soll. Sie sollten auch Ihre benutzerdefinierte Domäne als „managementEndpoint“ Ihres Clusters deklarieren, wenn das Portal den Alias der benutzerdefinierten Domäne für Ihren Cluster reflektieren soll.

Zu Testzwecken können Sie von einer kostenlosen oder offenen Zertifizierungsstelle ein von der Zertifizierungsstelle signiertes Zertifikat abrufen.

> [!NOTE]
> Selbstsignierte Zertifikate, einschließlich der beim Bereitstellen eines Service Fabric-Clusters im Azure-Portal generierten Zertifikate, werden nicht unterstützt. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Hochladen des Zertifikats in einen Schlüsseltresor
In Azure wird ein Service Fabric-Cluster in einer VM-Skalierungsgruppe bereitgestellt.  Laden Sie das Zertifikat in einen Schlüsseltresor hoch.  Bei der Bereitstellung des Clusters wird das Zertifikat in der VM-Skalierungsgruppe installiert, in welcher der Cluster ausgeführt wird.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Herunterladen und Aktualisieren einer Beispielvorlage
In diesem Artikel werden die Beispielvorlage für einen [Sicherheitscluster mit 5 Knoten](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) und die zugehörigen Vorlagenparameter verwendet. Laden Sie die Dateien *azuredeploy.json* und *azuredeploy-parameters.json* auf Ihren Computer herunter.

### <a name="update-parameters-file"></a>Aktualisieren der Parameterdatei
Öffnen Sie zunächst die Datei *azuredeploy.parameters.json* in einem Text-Editor, und fügen Sie den folgenden Parameterwert hinzu:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Legen Sie anschließend die Parameterwerte *certificateCommonName*, *sourceVaultValue* und *certificateUrlValue* auf die Werte fest, die vom vorhergehenden Skript zurückgegeben werden:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualisieren der Vorlagendatei
Öffnen Sie als Nächstes die Datei *azuredeploy.json* in einem Text-Editor, und nehmen Sie drei Aktualisierungen vor, damit der allgemeine Zertifikatname unterstützt wird.

1. Fügen Sie im Abschnitt **parameters** einen Parameter *certificateCommonName* hinzu:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Außerdem könnten Sie *certificateThumbprint* entfernen, da dieser Parameter wahrscheinlich nicht mehr benötigt wird.

2. Legen Sie den Wert der Variablen *SfrpApiVersion* auf „2018-02-01“ fest:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Aktualisieren Sie in der Ressource **Microsoft.Compute/virtualMachineScaleSets** die VM-Erweiterung, damit in den Zertifikateinstellungen anstelle des Fingerabdrucks der allgemeine Name verwendet wird.  Fügen Sie unter **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate** den Eintrag 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    hinzu, und entfernen Sie `"thumbprint": "[parameters('certificateThumbprint')]",`.

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. Aktualisieren Sie in der Ressource **Microsoft.ServiceFabric/clusters** die API-Version auf „2018-02-01“.  Fügen Sie auch eine Einstellung **certificateCommonNames** mit der Eigenschaft **commonNames** hinzu, und entfernen Sie die Einstellung **certificate** (mit der Eigenschaft „Thumbprint“), wie im folgenden Beispiel gezeigt:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Das Feld „CertificateIssuerThumbprint“ ermöglicht die Angabe der erwarteten Aussteller von Zertifikaten mit einem allgemeinen Namen des angegebenen Antragstellers. Dieses Feld akzeptiert eine durch Trennzeichen getrennte Enumeration von SHA1-Fingerabdrücken. Beachten Sie, dass dies eine Verbesserung der Zertifikatsvalidierung ist. Für den Fall, dass der Aussteller nicht angegeben oder leer ist, wird das Zertifikat für die Authentifizierung akzeptiert, wenn seine Kette aufgebaut werden kann, und landet in einem für das Validierungssteuerelement vertrauenswürdigen Stamm. Wenn der Aussteller angegeben ist, wird das Zertifikat akzeptiert, falls der Fingerabdruck seines direkten Ausstellers mit einem der in diesem Feld angegebenen Werte übereinstimmt, und zwar unabhängig davon, ob dem Stamm vertraut wird oder nicht. Bitte beachten Sie, dass eine PKI verschiedene Zertifizierungsstellen nutzen kann, um Zertifikate für denselben Antragsteller auszustellen. Daher ist es wichtig, alle erwarteten Fingerabdrücke des Ausstellers für einen bestimmten Antragsteller anzugeben.
   >
   > Die Angabe des Ausstellers gilt als bewährte Methode. Auch wenn sie weggelassen wird, funktioniert sie bei Zertifikaten, die mit einem vertrauenswürdigen Stamm verkettet sind, weiterhin. Dieses Verhalten hat jedoch Einschränkungen und kann in naher Zukunft auslaufen. Beachten Sie auch, dass Cluster, die in Azure bereitgestellt und mit X.509-Zertifikaten geschützt werden, welche von einer privaten PKI ausgestellt und vom Antragsteller deklariert wurden, möglicherweise vom Azure Service Fabric-Dienst nicht (für die Cluster-zu-Dienst-Kommunikation) validiert werden können, wenn die Zertifikatsrichtlinie der PKI nicht ermittelbar, verfügbar und zugänglich ist. 

## <a name="deploy-the-updated-template"></a>Bereitstellen der aktualisierten Vorlage
Stellen Sie nach Abschluss der Änderungen die aktualisierte Vorlage erneut bereit.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Clustersicherheit](service-fabric-cluster-security.md).
* Erfahren Sie, wie Sie einen [Rollover für ein Cluster-Zertifikat ausführen](service-fabric-cluster-rollover-cert-cn.md).
* [Aktualisieren und Verwalten von Clusterzertifikaten](service-fabric-cluster-security-update-certs-azure.md)
* Vereinfachen der Zertifikatverwaltung durch [Ändern des Clusters aus „Fingerabdruck“ in „Allgemeiner Name“](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
