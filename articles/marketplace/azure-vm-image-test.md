---
title: Testen eines Azure-VM-Images für Azure Marketplace
description: Hier erfahren Sie, wie Sie ein Azure-VM-Angebot im Azure Marketplace testen und übermitteln.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 10/15/2020
ms.openlocfilehash: 36eebb218ed2b2d9a48cf7d970896115af5cf6f8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424855"
---
# <a name="test-a-virtual-machine-image"></a>Testen eines VM-Images

In diesem Artikel wird beschrieben, wie Sie ein VM-Image im kommerziellen Marketplace testen und übermitteln, um sicherzustellen, dass es die aktuellen Anforderungen für die Veröffentlichung im Azure Marketplace erfüllt.

Führen Sie die folgenden Schritte aus, bevor Sie Ihr VM-Angebot übermitteln:

- Stellen Sie mithilfe Ihres generalisierten Images eine Azure-VM bereit. Weitere Informationen hierzu finden Sie unter [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md).
- Durchführen von Überprüfungen

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Bereitstellen einer Azure-VM mit Ihrem generalisierten Image

In diesem Abschnitt wird beschrieben, wie Sie ein generalisiertes VHD-Image (Virtual Hard Disk, virtuelle Festplatte) für die Erstellung einer neuen Azure-VM-Ressource bereitstellen. Für diesen Prozess verwenden wir die bereitgestellte Azure Resource Manager-Vorlage und das Azure PowerShell-Skript.

### <a name="prepare-an-azure-resource-manager-template"></a>Vorbereiten einer Azure Resource Manager-Vorlage

In diesem Abschnitt wird beschrieben, wie Sie ein vom Benutzer bereitgestelltes VM-Image erstellen und bereitstellen. Dazu können Sie Betriebssystem- und Datenträger-VHD-Images von einer in Azure bereitgestellten virtuellen Festplatte (VHD) bereitstellen. Mit diesen Schritten wird die VM mithilfe einer generalisierten VHD bereitgestellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Laden Sie Ihre generalisierten Betriebssystem- und Datenträger-VHDs in Ihr Azure-Speicherkonto hoch.
3. Wählen Sie auf der Startseite **Ressource erstellen** aus, suchen Sie nach „Vorlagenbereitstellung“, und wählen Sie **Erstellen** aus.
4. Wählen Sie **Eigene Vorlage im Editor erstellen** aus.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Zeigt die Auswahl einer Vorlage.":::

5. Fügen Sie die folgende JSON-Vorlage in den Editor ein, und wählen Sie **Speichern** aus.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Geben Sie die Parameterwerte für die angezeigten Eigenschaftenseiten für die benutzerdefinierte Bereitstellung ein.

    | ResourceGroupName | Name einer vorhandenen Azure-Ressourcengruppe. Normalerweise verwenden Sie dieselbe Ressourcengruppe wie für Ihren Schlüsseltresor. |
    | --- | --- |
    | TemplateFile | Vollständiger Pfadname der Datei „VHDtoImage.json“. |
    | userStorageAccountName | Name des Speicherkontos. |
    | dnsNameForPublicIP | DNS-Name für die öffentliche IP-Adresse. Muss in Kleinbuchstaben angegeben werden. |
    | subscriptionId | Die Azure-Abonnement-ID. |
    | Standort | Standardmäßiger geografischer Azure-Standort der Ressourcengruppe. |
    | vmName | Der Name des virtuellen Computers. |
    | vhdUrl | Die Webadresse der virtuellen Festplatte. |
    | vmSize | Die Größe der VM-Instanz. |
    | publicIPAddressName | Der Name der öffentlichen IP-Adresse. |
    | virtualNetworkName | Der Name des virtuellen Netzwerks. |
    | nicName | Der Name der Netzwerkschnittstellenkarte für das virtuelle Netzwerk. |
    | adminUserName | Der Benutzername des Administratorkontos. |
    | adminPassword | Das Administratorkennwort. |


7. Nachdem Sie diese Werte angegeben haben, wählen Sie **Kaufen** aus.

Azure startet die Bereitstellung. Im angegebenen Speicherkontopfad wird eine neue VM mit der angegebenen nicht verwalteten VHD erstellt. Sie können den Fortschritt im Azure-Portal nachverfolgen, indem Sie auf der linken Seite **Virtuelle Computer** auswählen. Nachdem die VM erstellt wurde, ändert sich der Status von „Wird gestartet“ in „Wird ausgeführt“.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Verwenden Sie für die Bereitstellung von VMs der 2. Generation diese Vorlage:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Bereitstellen einer Azure-VM mithilfe von PowerShell

Kopieren Sie das folgende Skript, und bearbeiten Sie es, um Werte für die Variablen `$storageaccount` und `$vhdUrl` anzugeben. Führen Sie das Skript aus, um eine Azure-VM-Ressource von Ihrer vorhandenen generalisierten VHD zu erstellen.

```PowerShell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
```

## <a name="run-validations"></a>Durchführen von Überprüfungen

Es gibt zwei Möglichkeiten, Überprüfungen für das bereitgestellte Image auszuführen.

### <a name="use-certification-test-tool-for-azure-certified"></a>Verwenden des „Certification Test Tool for Azure Certified“

#### <a name="download-and-run-the-certification-test-tool"></a>Herunterladen und Ausführen des Tools zum Testen der Zertifizierung

Das Certification Test Tool for Azure Certified wird auf einem lokalen Windows-Computer ausgeführt, aber es wird eine Azure-basierte Windows- oder Linux-VM getestet. Das Tool zertifiziert, ob Ihr VM-Benutzerimage mit Microsoft Azure genutzt werden kann und ob während der Vorbereitung Ihrer virtuellen Festplatte die Richtlinien und Anforderungen erfüllt wurden.

1. Laden Sie das neueste [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299) herunter, und installieren Sie es.
2. Öffnen Sie das Zertifizierungstool, und wählen Sie dann **Neuen Test starten** aus.
3. Geben Sie auf dem Bildschirm Testinformationen einen **Testnamen** für den Testlauf ein.
4. Wählen Sie die Plattform für Ihre VM aus (entweder **Windows Server** oder **Linux** ). Die Auswahl der Plattform wirkt sich auf die restlichen Optionen aus.
5. Aktivieren Sie das Kontrollkästchen **Für Azure SQL-Datenbank testen** , wenn Ihre VM diesen Datenbankdienst verwendet.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Verbinden des Zertifizierungstools mit einem VM-Image

1. Wählen Sie den Modus für die SSH-Authentifizierung aus: „Kennwortauthentifizierung“ oder „Authentifizierung per Schlüsseldatei“.
2. Geben Sie bei Verwendung der Authentifizierung per Kennwort Werte für den **VM-DNS-Namen** , den **Benutzernamen** und das **Kennwort** ein. Sie können auch die Standardnummer für den SSH-Port ändern.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Zeigt die Auswahl von VM-Testinformationen.":::

3. Wenn Sie die Authentifizierung per Schlüsseldatei verwenden, geben Sie Werte für den VM-DNS-Namen, den Benutzernamen und den Speicherort des privaten Schlüssels ein. Sie können auch eine Passphrase angeben oder die Standardnummer für den SSH-Port ändern.
4. Geben Sie den vollqualifizierten VM-DNS-Namen ein (z. B. MyVMName.Cloudapp.net).
5. Geben Sie **Benutzername** und **Kennwort** ein.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Zeigt die Auswahl des VM-Benutzernamens und -Kennworts.":::

6. Wählen Sie **Weiter** aus.

#### <a name="run-a-certification-test"></a>Ausführen eines Zertifizierungstests

Nachdem Sie die Parameterwerte für Ihr VM-Image im Zertifizierungstool angegeben haben, wählen Sie „Verbindung testen“ aus, um eine gültige Verbindung mit Ihrer VM herzustellen. Klicken Sie nach dem Überprüfen der Verbindung auf **Weiter** , um den Test zu starten. Nach Abschluss des Tests werden die Ergebnisse in einer Tabelle angezeigt. In der Spalte „Status“ wird für jeden Test „Bestanden“, „Fehler“ oder „Warnung“ angezeigt. Wenn einer der Tests nicht erfolgreich durchgeführt wird, wird das Image nicht zertifiziert. Überprüfen Sie in diesem Fall die Anforderungen und Fehlermeldungen, nehmen Sie die vorgeschlagenen Änderungen vor, und führen Sie den Test erneut durch.

Geben Sie nach Abschluss des automatisierten Tests auf dem Bildschirm Fragebogen auf den Registerkarten Allgemeine Bewertung und Kernelanpassung weitere Informationen zu Ihrem VM-Image an, und wählen Sie anschließend Weiter aus.

Im letzten Bildschirm können Sie zusätzliche Informationen angeben, z. B. SSH-Zugriffsinformationen für ein Linux-VM-Image und Erläuterungen für nicht erfolgreiche Bewertungen bei Ausnahmen.

Wählen Sie abschließend Bericht generieren aus, um zusätzlich zu Ihren Antworten im Fragebogen auch die Testergebnisse und Protokolldateien für die ausgeführten Testfälle herunterzuladen. 
> [!Note]
> Einige Herausgeber verfügen über Szenarien, in denen VMs gesperrt werden müssen, da Software wie Firewalls auf der VM installiert ist. Laden Sie in diesem Fall das [Certified-Testtool](https://aka.ms/AzureCertificationTestTool) herunter, und übermitteln Sie den Bericht an den Partner Center-[Support](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Nutzen der Selbsttest-API mithilfe von PowerShell

### <a name="on-linux-os"></a>Linux-Betriebssystem

Aufrufen der API in PowerShell:

1. Verwenden Sie den Befehl „Invoke-WebRequest“, um die API aufzurufen.
2. Die Methode ist Post und der Inhaltstyp JSON, wie im folgenden Codebeispiel und der Bildschirmaufnahme dargestellt.
3. Geben Sie die Textparameter im JSON-Format an.

Das folgende Beispiel veranschaulicht einen PowerShell-Aufruf der API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Im Folgenden ein Beispiel für das Aufrufen der API in PowerShell:

[![Bildschirm mit einem Beispiel für das Aufrufen der API in PowerShell](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In diesem Beispielbildschirm mit `$res.Content` sind die Details der Testergebnisse im JSON-Format dargestellt:

[![Bildschirm mit einem Beispiel für das Aufrufen der API in PowerShell mit Details der Testergebnisse](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Hier ein Beispiel für JSON-Testergebnisse, die in einem JSON-Onlineviewer angezeigt werden (z. B. [Code Beautify](https://codebeautify.org/jsonviewer) oder [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Weitere Testergebnisse in einem JSON-Onlineviewer](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows-Betriebssystem

Aufrufen der API in PowerShell:

1. Verwenden Sie den Befehl „Invoke-WebRequest“, um die API aufzurufen.
2. Die Methode lautet „Post“ und der Inhaltstyp JSON, wie im folgenden Codebeispiel und Beispielbildschirm dargestellt.
3. Erstellen Sie die Body-Parameter im JSON-Format.

Das folgende Codebeispiel veranschaulicht einen PowerShell-Aufruf der API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Diese Beispielbildschirme zeigen ein Beispiel für das Aufrufen der API in PowerShell:

**Mit SSH-Schlüssel** :

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Aufrufen der API in PowerShell mit einem SSH-Schlüssel":::

**Mit Kennwort** :

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Aufrufen der API in PowerShell mit einem Kennwort":::

<br>Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In diesem Bildschirm mit `$res.Content` sind die Details der Testergebnisse im JSON-Format dargestellt:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Details der Testergebnisse im JSON-Format":::

<br>Hier ein Beispiel für Testergebnisse, die in einem JSON-Onlineviewer angezeigt werden (z. B. [Code Beautify](https://codebeautify.org/jsonviewer) oder [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Testergebnisse in einem JSON-Onlineviewer](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Nutzen der Selbsttest-API unter dem Linux-Betriebssystem mithilfe von CURL

In diesem Beispiel wird CURL verwendet, um einen POST-API-Aufruf an Azure Active Directory und die Selfhost-VM auszuführen.

1. Anfordern eines Azure AD-Tokens für die Authentifizierung bei einer Selfhost-VM

   Stellen Sie sicher, dass die Werte in der CURL-Anforderung durch die richtigen Werte ersetzt werden.

   ```
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```
   Im Folgenden ein Beispiel für die Antwort auf die Anforderung:
   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Übermitteln einer Anforderung für die Selbsttest-VM

   Stellen Sie sicher, dass das Bearertoken und die Parameter durch die richtigen Werte ersetzt werden.

   ```
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Beispielantwort auf den API-Aufruf der Selbsttest-VM
   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Abrufen des SAS-URI](azure-vm-get-sas-uri.md)
