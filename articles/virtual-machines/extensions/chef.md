---
title: Chef-Erweiterung für Azure-VMs
description: Stellen Sie den Chef-Client auf einem virtuellen Computer mithilfe der Chef-VM-Erweiterung bereit.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: 8977563c6b19754eda53686baf85f840a7583e77
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968365"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef-VM-Erweiterung für Linux und Windows

Chef Software ist eine DevOps-Automatisierungsplattform für Linux und Windows und ermöglicht die Verwaltung der Konfigurationen von physischen und virtuellen Servern. Die Chef-VM-Erweiterung ist eine Erweiterung, die die Verwendung von Chef auf virtuellen Computern ermöglicht.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Die Chef-VM-Erweiterung wird von allen [von Erweiterungen unterstützten Betriebssystemen](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) in Azure unterstützt.

### <a name="internet-connectivity"></a>Internetkonnektivität

Die Chef-VM-Erweiterung erfordert, dass der virtuelle Zielcomputer mit dem Internet verbunden ist, um die Chef-Client-Nutzlast aus dem CDN (Content Delivery Network) abzurufen.  

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Chef-VM-Erweiterung. Die Erweiterung erfordert mindestens die Chef-Server-URL, den Namen des Validierungsclients sowie den Validierungsschlüssel für den Chef-Server. Diese Werte finden Sie in der Datei `knife.rb` im „starter-kit.zip“, das heruntergeladen wird, wenn Sie [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) oder einen eigenständigen [Chef-Server](https://downloads.chef.io/chef-server) installieren. Da der Validierungsschlüssel als vertrauliche Information behandelt werden sollte, sollte er unter dem Element **protectedSettings** konfiguriert werden, was bedeutet, dass er nur auf dem virtuellen Zielcomputer entschlüsselt wird.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Kerneigenschaftswerte

| Name | Wert/Beispiel | Datentyp
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.13` | string (double) |

### <a name="settings"></a>Einstellungen

| Name | Wert/Beispiel | Datentyp | Erforderlich?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | J |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | J |
| settings/runlist | `recipe[mycookbook::default]` | string | J |

### <a name="protected-settings"></a>Geschützte Einstellungen

| Name | Beispiel | Datentyp | Erforderlich?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | J |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen können verwendet werden, um einen oder mehrere virtuelle Computer bereitzustellen, den Chef-Client zu installieren, eine Verbindung mit dem Chef-Server herzustellen und die anfängliche Konfiguration auf dem Server gemäß der Definition in der [Run-list](https://docs.chef.io/run_lists.html) auszuführen.

Eine Resource Manager-Beispielvorlage mit der Chef-VM-Erweiterung finden Sie im [Azure-Schnellstartkatalog](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Die JSON-Konfiguration für eine VM-Erweiterung kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung der JSON-Konfiguration wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen).

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Sie können die Chef-VM-Erweiterung mithilfe der Azure-Befehlszeilenschnittstelle auf einem vorhandenen virtuellen Computer bereitstellen. Ersetzen Sie **validation_key** durch den Inhalt Ihres Validierungsschlüssels (diese Datei als eine `.pem`-Erweiterung).  Ersetzen Sie **validation_client_name**, **chef_server_url** und **run_list** durch diese Werte aus der Datei `knife.rb` in Ihrem Starter Kit.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Problembehandlung und Support

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Fehlercodes und ihre Bedeutung

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 51 | Diese Erweiterung wird vom Betriebssystem der VM nicht unterstützt. | |

Weitere Informationen zur Problembehandlung finden Sie in der [Infodatei zur Chef-VM-Erweiterung](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Wenden Sie sich bei allen anderen Chef-Problemen an den [Chef-Support](https://www.chef.io/support/).

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
