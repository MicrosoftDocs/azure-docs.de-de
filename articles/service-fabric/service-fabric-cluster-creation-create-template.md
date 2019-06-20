---
title: Erstellen einer Azure Service Fabric-Clustervorlage| Microsoft-Dokumente
description: Erfahren Sie, wie Sie eine Resource Manager-Vorlage für einen Service Fabric-Cluster erstellen. Konfigurieren der Sicherheit, von Azure Key Vault und Azure Active Directory für die Clientauthentifizierung
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 2fdea1f088dd6eabdf7d72342c837d976133a1bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386879"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Erstellen einer Resource Manager-Vorlage für Service Fabric-Cluster

Ein [Azure Service Fabric-Cluster](service-fabric-deploy-anywhere.md) ist eine vernetzte Gruppe von virtuellen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Bei einem Service Fabric-Cluster, der in Azure ausgeführt wird, handelt es sich um eine Azure-Ressource, und er wird mithilfe des Ressourcen-Managers bereitgestellt, verwaltet und überwacht.  In diesem Artikel wird beschrieben, wie eine Resource Manager-Vorlage für einen Service Fabric-Cluster, der in Azure ausgeführt wird, erstellt wird.  Wenn die Vorlage vollständig ist, können Sie [den Cluster in Azure bereitstellen](service-fabric-cluster-creation-via-arm.md).

Die Clustersicherheit wird konfiguriert, sobald der Cluster zum ersten Mal eingerichtet wird. Diese kann später nicht geändert werden. Bevor Sie einen Cluster einrichten, lesen Sie [Szenarios für die Clustersicherheit in Service Fabric][service-fabric-cluster-security]. In Azure verwendet Service Fabric das X.509-Zertifikat zum Schützen Ihres Clusters und seiner Endpunkte sowie zum Authentifizieren von Clients und Verschlüsseln von Daten. Azure Active Directory wird ebenfalls empfohlen, um den Zugriff auf Verwaltungsendpunkte zu schützen. Azure AD-Mandanten und -Benutzer müssen vor der Erstellung des Clusters erstellt werden.  Weitere Informationen finden Sie unter [Set up Azure AD to authenticate clients (Einrichten von Azure AD zum Authentifizieren von Clients)](service-fabric-cluster-creation-setup-aad.md).

Bevor Sie einen Produktionscluster für die Ausführung von Produktionsworkloads bereitstellen, lesen Sie zuerst die [Prüfliste für die Produktionsbereitschaft](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage
Resource Manager-Beispielvorlagen stehen in den [Azure-Beispielen auf GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) zur Verfügung. Diese Vorlagen können als Ausgangspunkt für Ihre Clustervorlage verwendet werden.

In diesem Artikel werden die Beispielvorlage für einen [Sicherheitscluster mit fünf Knoten][service-fabric-secure-cluster-5-node-1-nodetype] und die zugehörigen Vorlagenparameter verwendet. Laden Sie *azuredeploy.json* und *azuredeploy.parameters.json* auf Ihren Computer herunter, und öffnen Sie beide Dateien in Ihrem bevorzugten Text-Editor.

> [!NOTE]
> Für nationale Clouds (Azure Government, Azure China, Azure Deutschland) sollten Sie auch die folgenden `fabricSettings` Ihrer Vorlage hinzufügen: `AADLoginEndpoint`, `AADTokenEndpointFormat` und `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Hinzufügen von Zertifikaten
Um Zertifikate einer Resource Manager-Vorlage für einen Cluster hinzuzufügen, verweisen Sie auf den Schlüsseltresor mit den Zertifikatschlüsseln. Fügen Sie die Schlüsseltresorparameter und -werte zu einer Resource Manager-Vorlagenparameterdatei (*azuredeploy.parameters.json*) hinzu.

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Hinzufügen aller Zertifikate zur VM-Skalierungsgruppe „osProfile“
Jedes im Cluster installierte Zertifikat muss im **osProfile**-Abschnitt der Skalierungsgruppenressource (Microsoft.Compute/virtualMachineScaleSets) konfiguriert werden. Dadurch wird der Ressourcenanbieter angewiesen, das Zertifikat auf den virtuellen Computern zu installieren. Die Installation umfasst sowohl das Clusterzertifikat als auch sämtliche Anwendungssicherheitszertifikate, die Sie für Ihre Anwendungen verwenden möchten:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurieren des Service Fabric-Clusterzertifikats

Das Zertifikat für die Clusterauthentifizierung muss sowohl in der Service Fabric-Clusterressource (Microsoft.ServiceFabric/clusters) als auch in der Service Fabric-Erweiterung für VM-Skalierungsgruppen in der VM-Skalierungsgruppenressource konfiguriert werden. Dadurch kann der Service Fabric-Ressourcenanbieter das Zertifikat für die Clusterauthentifizierung und die Serverauthentifizierung für Verwaltungsendpunkte konfigurieren.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Hinzufügen der Zertifikatinformationen in der VM-Skalierungsgruppenressource

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Hinzufügen der Zertifikatinformationen zur Service Fabric-Clusterressource

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
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Hinzufügen der Azure AD-Konfiguration zum Verwenden von Azure AD für den Clientzugriff

Fügen Sie die Azure AD-Konfiguration einer Resource Manager-Vorlage für einen Cluster hinzu, indem Sie auf den Schlüsseltresor mit den Zertifikatschlüsseln verweisen. Fügen Sie die Azure AD-Parameter und -werte zu einer Resource Manager-Vorlagenparameterdatei (*azuredeploy.parameters.json*) hinzu. 

> [!NOTE]
> Azure AD-Mandanten und -Benutzer müssen vor der Erstellung des Clusters erstellt werden.  Weitere Informationen finden Sie unter [Einrichten der Azure Active Directory-Authentifizierung](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Füllen Sie die Parameterdatei mit den Werten auf.

Füllen Sie die Parameterdatei abschließend mit den Ausgabewerten aus den Schlüsseltresor- und Azure AD-PowerShell-Befehlen auf.

Wenn Sie die Azure Service Fabric-RM-PowerShell-Module verwenden möchten, müssen Sie die Clusterzertifikatinformationen nicht ausfüllen. Wenn das System das selbstsignierte Zertifikat für die Clustersicherheit generieren soll, belassen Sie sie bei NULL. 

> [!NOTE]
> Für die RM-Module, die diese leeren Parameterwerte übernehmen und auffüllen sollen, müssen die Parameternamen mit den folgenden Namen übereinstimmen.

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Wenn Sie Anwendungszertifikate oder einen vorhandenen Cluster verwenden, den Sie in den Schlüsseltresor hochgeladen haben, rufen Sie diese Informationen ab, und füllen Sie sie auf.

Die RM-Module können die Azure AD-Konfiguration nicht für Sie generieren. Wenn Sie Azure AD für den Clientzugriff verwenden möchten, müssen Sie sie eintragen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testen der Vorlage
Verwenden Sie den folgenden PowerShell-Befehl, um Ihre Resource Manager-Vorlage mit einer Parameterdatei zu testen:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Falls Probleme auftreten und Sie kryptische Nachrichten erhalten, verwenden Sie „-Debug“ als Option.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Das folgende Diagramm veranschaulicht die Platzierung der Schlüsseltresor- und Azure AD-Konfiguration in der Resource Manager-Vorlage.

![Abhängigkeiten in Resource Manager][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun eine Vorlage für Ihren Cluster besitzen, lernen Sie, wie Sie [den Cluster in Azure bereitstellen](service-fabric-cluster-creation-via-arm.md).  Wenn Sie die [Prüfliste für die Produktionsbereitschaft](service-fabric-production-readiness-checklist.md) noch nicht gelesen haben, holen Sie dies nach, bevor Sie einen Produktionscluster bereitstellen.

Informationen zur JSON-Syntax und zu den Eigenschaften für die in diesem Artikel bereitgestellten Ressourcen finden Sie unter:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
