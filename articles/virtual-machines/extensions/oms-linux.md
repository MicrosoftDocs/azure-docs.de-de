---
title: Log Analytics-VM-Erweiterung für Linux
description: Stellen Sie den Log Analytics-Agent mithilfe einer VM-Erweiterung auf einem virtuellen Linux-Computer bereit.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: cb1e2337d5a5214c4e748e5b0f45f223b8bcb445
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967991"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics-VM-Erweiterung für Linux

## <a name="overview"></a>Übersicht

Azure Monitor-Protokolle bieten Überwachungs- und Warnungsfunktionen sowie Funktionen zum Beheben von Warnungen für cloudbasierte und lokale Ressourcen. Die Log Analytics-VM-Erweiterung für Linux wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert virtuelle Computer in einem vorhandenen Log Analytics-Arbeitsbereich. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Log Analytics-VM-Erweiterung für Linux.

>[!NOTE]
>Im Rahmen der laufenden Umstellung von der Microsoft Operations Management Suite (OMS) auf Azure Monitor wird der OMS-Agent für Windows bzw. Linux nun als Log Analytics-Agent für Windows bzw. Log Analytics-Agent für Linux bezeichnet.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Ausführliche Informationen zu den unterstützten Linux-Distributionen finden Sie im Artikel [Übersicht über Azure Monitor-Agents](../../azure-monitor/platform/agents-overview.md#supported-operating-systems).

### <a name="agent-and-vm-extension-version"></a>Version des Agents und der VM-Erweiterung
Die folgende Tabelle enthält eine Zuordnung der Version der Log Analytics-VM-Erweiterung und des Log Analytics-Agent-Pakets für jede Version. Ein Link zu den Anmerkungen zur jeweiligen Log Analytics-Agent-Paketversion ist enthalten. Anmerkungen zur Version enthalten Details zu Fehlerbehebungen und neuen Features, die für eine bestimmte Agentversion verfügbar sind.  

| Version der Log Analytics-VM-Erweiterung unter Linux | Log Analytics-Agent-Paketversion | 
|--------------------------------|--------------------------|
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center stellt den Log Analytics-Agent automatisch bereit und verbindet ihn mit einem standardmäßigen Log Analytics-Arbeitsbereich, der von ASC in Ihrem Azure-Abonnement erstellt wird. Wenn Sie Azure Security Center verwenden, führen Sie nicht die Schritte in diesem Dokument aus. Andernfalls überschreiben Sie den konfigurierten Arbeitsbereich und unterbrechen die Verbindung mit Azure Security Center.

### <a name="internet-connectivity"></a>Internetkonnektivität

Um die Log Analytics-Agent-Erweiterung für Linux verwenden zu können, muss der virtuelle Zielcomputer mit dem Internet verbunden sein. 

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Log Analytics-Agent-Erweiterung. Für die Erweiterung werden die Arbeitsbereichs-ID und der Arbeitsbereichsschlüssel aus dem Log Analytics-Zielarbeitsbereich benötigt. Diese Werte sind [im Log Analytics-Arbeitsbereich](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) im Azure-Portal zu finden. Da der Arbeitsbereichsschlüssel als vertrauliche Information behandelt werden muss, sollte er in einer geschützten Einstellungskonfiguration gespeichert werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt. Bitte beachten Sie, dass bei **workspaceId** und **workspaceKey** die Groß-/Kleinschreibung beachtet wird.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Bei dem oben angegebenen Schema wird davon ausgegangen, dass es auf der Stammebene der Vorlage platziert wird. Wenn Sie es in der Ressource des virtuellen Computers in der Vorlage einfügen, sollten die Eigenschaften `type` und `name` geändert werden, wie [weiter unten](#template-deployment) beschrieben.

### <a name="property-values"></a>Eigenschaftswerte

| Name | Wert/Beispiel |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| workspaceId (z.B.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (z.B.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Bereitstellung von Vorlagen

>[!NOTE]
>Einige Komponenten der Log Analytics-VM-Erweiterung werden auch mit der [VM-Erweiterung für Diagnosen](./diagnostics-linux.md) geliefert. Aufgrund dieser Architektur können Konflikte auftreten, wenn beide Erweiterungen in derselben ARM-Vorlage instanziiert werden. Um solche Konflikte zur Installationszeit zu vermeiden, verwenden Sie die [`dependsOn`-Anweisung](../../azure-resource-manager/templates/define-resource-dependency.md#dependson), um sicherzustellen, dass die Erweiterungen nacheinander installiert werden. Die Erweiterungen können in beliebiger Reihenfolge installiert werden.

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie mindestens einen virtuellen Computer bereitstellen, der eine Konfiguration nach der Bereitstellung erfordert, wie z. B. Onboarding bei Azure Monitor-Protokollen. Eine Resource Manager-Beispielvorlage mit der VM-Erweiterung für den Log Analytics-Agent finden Sie im [Azure-Schnellstartkatalog](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Die JSON-Konfiguration für eine VM-Erweiterung kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung der JSON-Konfiguration wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen). 

Im folgenden Beispiel wird davon ausgegangen, dass die VM-Erweiterung in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Beim Platzieren des JSON-Codes für die Erweiterung im Stamm der Vorlage enthält der Name der Ressource einen Verweis auf die übergeordnete VM, und der Typ spiegelt die geschachtelte Konfiguration wider.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle

Sie können die Log Analytics-Agent-VM-Erweiterung mithilfe der Azure-Befehlszeilenschnittstelle auf einem vorhandenen virtuellen Computer bereitstellen. Ersetzen Sie unten den Wert *myWorkspaceKey*  durch Ihren Arbeitsbereichsschlüssel und den Wert *myWorkspaceId*  durch Ihre Arbeitsbereichs-ID. Diese Werte finden Sie im Azure-Portal im Log Analytics-Arbeitsbereich unter *Erweiterte Einstellungen*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Fehlercodes und ihre Bedeutung

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 9 | Aktivierung zu früh aufgerufen | [Aktualisieren Sie den Azure Linux-Agent](./update-linux-agent.md) auf die neueste verfügbare Version. |
| 10 | VM ist bereits mit einem Log Analytics-Arbeitsbereich verbunden | Zum Verbinden der VM mit dem im Erweiterungsschema angegebenen Arbeitsbereich legen Sie „stopOnMultipleConnections“ in den öffentlichen Einstellungen auf FALSE fest, oder entfernen Sie diese Eigenschaft. Diese VM wird für jeden Arbeitsbereich, mit dem Sie verbunden ist, einmal in Rechnung gestellt. |
| 11 | Ungültige Konfiguration der Erweiterung bereitgestellt | Folgen Sie den vorherigen Beispielen, um alle für die Bereitstellung erforderlichen Eigenschaftswerte festzulegen. |
| 17 | Fehler bei der Log Analytics-Paketinstallation | 
| 19 | Fehler bei der OMI-Paketinstallation | 
| 20 | Fehler bei der SCX-Paketinstallation |
| 51 | Diese Erweiterung wird vom Betriebssystem der VM nicht unterstützt. | |
| 52 | Diese Erweiterung ist aufgrund einer fehlenden Abhängigkeit fehlgeschlagen. | Überprüfen Sie die Ausgabe und die Protokolle, um weitere Informationen zur fehlenden Abhängigkeit zu erhalten. |
| 53 | Diese Erweiterung ist aufgrund fehlender oder falscher Konfigurationsparameter fehlgeschlagen. | Überprüfen Sie die Ausgabe und die Protokolle, um weitere Informationen zum Fehler zu erhalten. Überprüfen Sie zusätzlich die Richtigkeit der Arbeitsbereichs-ID, und vergewissern Sie sich, dass der Computer mit dem Internet verbunden ist. |
| 55 | Es kann keine Verbindung mit dem Azure Monitor-Dienst hergestellt werden, oder die erforderlichen Pakete fehlen, oder der dpkg-Paket-Manager ist gesperrt.| Stellen Sie sicher, dass das System entweder Internetzugriff hat oder dass ein gültiger HTTP-Proxy bereitgestellt wurde. Überprüfen Sie außerdem die Richtigkeit der Arbeitsbereichs-ID, und stellen Sie sicher, dass die Hilfsprogramme cURL und Tar installiert sind. |

Weitere Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung für den Log Analytics-Agent für Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
