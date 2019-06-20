---
title: Azure Monitor-VM-Erweiterung für Windows | Microsoft-Dokumentation
description: Stellen Sie den Log Analytics-Agent mithilfe einer VM-Erweiterung auf einem virtuellen Windows-Computer bereit.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: 270b3ae49a815c9e12fce9377c8298192237f28a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790371"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Azure Monitor-VM-Erweiterung für Windows

Azure Monitor-Protokolle bietet Überwachungsfunktionen für cloudbasierte und lokale Ressourcen. Die Log Analytics-Agent-VM-Erweiterung für Windows wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert virtuelle Computer in einem vorhandenen Log Analytics-Arbeitsbereich. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Azure Monitor-Protokolle-VM-Erweiterung für Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Die Log Analytics-Agent-Erweiterung für Windows unterstützt die folgenden Versionen des Windows-Betriebssystems:

- Windows Server 2019
- Windows Server 2008 R2, 2012, 2012 R2, 2016, Version 1709 und 1803

### <a name="agent-and-vm-extension-version"></a>Version des Agents und der VM-Erweiterung
Die folgende Tabelle enthält eine Zuordnung der Version der Azure Monitor-VM-Erweiterung und des Log Analytics-Agent-Pakets für jede Version. 

| Version der Azure Monitor-VM-Erweiterung unter Linux | Log Analytics-Agent-Paketversion | Herausgabedatum | Versionsinformationen |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 8.0.11049.0 | 1.0.11049.1 | Februar 2017 | |
| 8.0.11072.0 | 1.0.11072.1 | September 2017 | |
| 8.0.11081.0 | 1.0.11081.5 | November 2017 | | 
| 8.0.11103.0 | – |  April 2018 | |
| 8.0.11136.0 | – | September 2018 |  <ul><li> Unterstützung für die Erkennung einer Änderung der Ressourcen-ID bei der VM-Verschiebung hinzugefügt </li><li> Unterstützung für Melden der Ressourcen-ID bei Verwendung einer Installation ohne Erweiterung hinzugefügt </li></ul>| 
| 10.19.10006.0 | – | Dezember 2018 | <ul><li> Kleinere Stabilisierungskorrekturen </li></ul> | 
| 10.19.13515.0 | 1.0.13515.1 | März 2019 | <ul><li>Kleinere Stabilisierungskorrekturen </li></ul> |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center stellt den Log Analytics-Agent automatisch bereit und verbindet ihn mit dem Standardarbeitsbereich von Log Analytics für das Azure-Abonnement. Wenn Sie Azure Security Center verwenden, führen Sie nicht die Schritte in diesem Dokument aus. Andernfalls überschreiben Sie den konfigurierten Arbeitsbereich und unterbrechen die Verbindung mit Azure Security Center.

### <a name="internet-connectivity"></a>Internetkonnektivität
Um die Log Analytics-Agent-Erweiterung für Windows verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. 

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Log Analytics-Agent-Erweiterung. Für die Erweiterung werden die Arbeitsbereichs-ID und der Arbeitsbereichsschlüssel aus dem Log Analytics-Zielarbeitsbereich benötigt. Diese sind in den Einstellungen des Arbeitsbereichs im Azure-Portal zu finden. Da der Arbeitsbereichsschlüssel als vertrauliche Information behandelt werden muss, sollte er in einer geschützten Einstellungskonfiguration gespeichert werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt. Bitte beachten Sie, dass bei **workspaceId** und **workspaceKey** die Groß-/Kleinschreibung beachtet wird.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Eigenschaftswerte

| NAME | Wert/Beispiel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (z.B.)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (z.B.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* In der Log Analytics-API heißt die „workspaceId“ „consumerId“.

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt erläuterte JSON-Schema kann in einer Azure Resource Manager-Vorlage zum Ausführen der Log Analytics-Agent-Erweiterung im Rahmen einer Azure Resource Manager-Bereitstellung verwendet werden. Eine Beispielvorlage mit der Log Analytics-Agent-VM-Erweiterung finden Sie im [Azure-Schnellstartkatalog](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Die Vorlage unterstützt nicht die Angabe mehrerer Arbeitsbereichs-IDs und Arbeitsbereichsschlüssel, wenn Sie den Agent zum Berichten an mehrere Arbeitsbereiche konfigurieren möchten. Informationen zum Konfigurieren des Agents zum Berichten an mehrere Arbeitsbereiche finden Sie unter [Hinzufügen oder Entfernen von Arbeitsbereichen](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

Der JSON-Code für eine Erweiterung des virtuellen Computers kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung des JSON-Codes wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources) (Festlegen von Name und Typ für untergeordnete Ressourcen). 

Im folgenden Beispiel wird davon ausgegangen, dass die Azure Monitor-Erweiterung in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Beim Platzieren des JSON-Codes für die Erweiterung im Stamm der Vorlage enthält der Name der Ressource einen Verweis auf die übergeordnete VM, und der Typ spiegelt die geschachtelte Konfiguration wider. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-Bereitstellung

Mit dem Befehl `Set-AzVMExtension` können Sie die Log Analytics-Agent-VM-Erweiterung auf einem vorhandenen virtuellen Computer bereitstellen. Vor dem Ausführen des Befehls müssen die öffentliche und die private Konfiguration in einer PowerShell-Hashtabelle gespeichert werden. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe des Azure PowerShell-Moduls abgerufen werden. Führen Sie über das Azure PowerShell-Modul den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Die Ausgabe der Erweiterungsausführung wird in Dateien im folgenden Verzeichnis protokolliert:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
