---
title: Exportieren von Azure-Ressourcengruppen, die VM-Erweiterungen enthalten
description: Exportieren von Resource Manager-Vorlagen, die Erweiterungen für virtuelle Computer enthalten.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 40b8ff01a5c8d00d3234af31877ea58c471026e8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955751"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportieren von Ressourcengruppen, die VM-Erweiterungen enthalten

Azure-Ressourcengruppen können in eine neue Resource Manager-Vorlage exportiert werden, die dann erneut bereitgestellt werden kann. Beim Exportvorgang werden vorhandene Ressourcen interpretiert und eine Resource Manager-Vorlage erstellt, deren Bereitstellung eine ähnliche Ressourcengruppe zum Ergebnis hat. Beim Verwenden der Exportoption für Ressourcengruppen für eine Ressourcengruppe, die Erweiterungen für virtuelle Computer enthält, müssen verschiedene Punkte berücksichtigt werden, etwa die Kompatibilität der Erweiterungen und geschützte Einstellungen.

Dieses Dokument behandelt ausführlich die Funktionsweise des Exportvorgangs für Ressourcengruppen im Hinblick auf Erweiterungen für virtuelle Computer, einschließlich einer Liste der unterstützen Erweiterungen und Details zur Behandlung geschützter Daten.

## <a name="supported-virtual-machine-extensions"></a>Unterstützte Erweiterungen für virtuelle Computer

Es sind viele Erweiterungen für virtuelle Computer verfügbar. Nicht alle Erweiterungen können mithilfe des Features „Automatisierungsskript“ in eine Resource Manager-Vorlage exportiert werden. Wenn eine Erweiterung für virtuelle Computer nicht unterstützt wird, muss sie manuell in die exportierte Vorlage eingefügt werden.

Die folgenden Erweiterungen können mit dem Automatisierungsskriptfeature exportiert werden.

> Acronis Backup, Acronis Backup Linux, Bg Info, BMC CTM Agent Linux, BMC CTM Agent Windows, Chef Client, Custom Script, Custom Script Extension, Custom Script for Linux, Datadog Linux Agent, Datadog Windows Agent, Docker Extension, DSC Extension, Dynatrace Linux, Dynatrace Windows, HPE Security Application Defender, IaaS Antimalware, IaaS Diagnostics, Linux Chef Client, Linux Diagnostic, OS Patching For Linux, Puppet Agent, Site 24x7 Apm Insight, Site 24x7 Linux Server, Site 24x7 Windows Server, Trend Micro DSA, Trend Micro DSA Linux, VM Access For Linux, VM Access For Linux, VM Snapshot, VM Snapshot Linux

## <a name="export-the-resource-group"></a>Exportieren der Ressourcengruppe

Um eine Ressourcengruppe in eine wiederverwendbare Vorlage zu exportieren, führen Sie die folgenden Schritte aus:

1. Melden Sie sich auf dem Azure-Portal an.
2. Klicken Sie im Hubmenü auf „Ressourcengruppen“
3. Wählen Sie in der Liste die Zielressourcengruppe aus
4. Klicken Sie auf dem Blatt „Ressourcengruppe“ auf „Automatisierungsskript“

![Exportieren von Vorlagen](./media/export-templates/template-export.png)

Das Automatisierungsskript von Azure Resource Manager erzeugt eine Resource Manager-Vorlage, eine Parameterdatei und verschiedene Beispielskripts zur Bereitstellung, etwa für PowerShell und Azure-CLI. An diesem Punkt kann die exportierte Vorlage mithilfe der Downloadschaltfläche heruntergeladen, der Vorlagenbibliothek als neue Vorlage hinzugefügt oder mithilfe der Schaltfläche „Bereitstellen“ erneut bereitgestellt werden.

## <a name="configure-protected-settings"></a>Konfigurieren von geschützten Einstellungen

Viele Erweiterungen für virtuelle Azure-Computer beinhalten eine Konfiguration mit geschützten Einstellungen, die vertrauliche Daten, wie Anmeldeinformationen und Konfigurationszeichenfolgen, verschlüsselt. Geschützte Einstellungen werden nicht mithilfe des Automatisierungsskripts exportiert. Erforderlichenfalls müssen geschützte Einstellungen erneut in die exportierte Vorlage eingefügt werden.

### <a name="step-1---remove-template-parameter"></a>Schritt 1 – Entfernen des Vorlagenparameters

Beim Exportieren der Ressourcengruppe wird ein einzelner Vorlagenparameter erstellt, um einen Wert für die exportierten geschützten Einstellungen bereitzustellen. Dieser Parameter kann entfernt werden. Um den Parameter zu entfernen, gehen Sie die Parameterliste durch, und löschen Sie den Parameter, der ähnlich wie dieses JSON-Beispiel aussieht.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Schritt 2 – Abrufen der Eigenschaften der geschützten Einstellungen

Da jede geschützte Einstellung eine Reihe erforderlicher Eigenschaften aufweist, muss eine Liste dieser Eigenschaften gesammelt werden. Jeder Parameter der geschützten Einstellungskonfiguration findet sich im [Azure Resource Manager-Schema auf GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Dieses Schema enthält nur die Parametersätze für die im Übersichtsabschnitt dieses Dokuments aufgelisteten Erweiterungen. 

Suchen Sie innerhalb des Schemarepositorys nach der gewünschten Erweiterung, in diesem Beispiel `IaaSDiagnostics`. Sobald das Erweiterungsobjekt `protectedSettings` gefunden wurde, halten Sie jeden einzelnen Parameter fest. Im Beispiel der `IaasDiagnostic`-Erweiterung sind die erforderlichen Parameter `storageAccountName`, `storageAccountKey` und `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Schritt 3 – Erneutes Erstellen der geschützten Konfiguration

Suchen Sie in der exportierten Vorlage nach `protectedSettings`, und ersetzen Sie das exportierte Objekt für die geschützten Einstellungen durch ein neues, das die erforderlichen Erweiterungsparameter mit einem Wert für jeden Parameter enthält.

Im Fall der `IaasDiagnostic`-Erweiterung sieht die neue geschützte Einstellungskonfiguration wie im folgenden Beispiel aus:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Die endgültige Erweiterungsressource sieht ähnlich wie das folgende JSON-Beispiel aus:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Wenn Sie Vorlagenparameter zum Angeben von Eigenschaftswerten verwenden, müssen diese erstellt werden. Achten Sie beim Erstellen von Vorlagenparameter für geschützte Einstellungswerte darauf, den Parametertyp `SecureString` zu verwenden, damit vertrauliche Werte geschützt sind. Weitere Informationen zum Verwenden von Parametern finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md).

Im Beispiel der `IaasDiagnostic`-Erweiterung würden im Parameterabschnitt der Resource Manager-Vorlage die folgenden Parameter erstellt.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

An diesem Punkt kann die Vorlage mithilfe einer beliebigen Methode zur Vorlagenbereitstellung bereitgestellt werden.
