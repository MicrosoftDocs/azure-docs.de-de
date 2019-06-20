---
title: Azure-VM-Erweiterung für die Leistungsdiagnose unter Windows | Microsoft-Dokumentation
description: Hier finden Sie eine Einführung in die Azure-VM-Erweiterung für die Leistungsdiagnose unter Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 769305cc3d838832f8f445ac9623a1724603f968
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307902"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azure-VM-Erweiterung für die Leistungsdiagnose unter Windows

Die Azure-VM-Erweiterung für die Leistungsdiagnose ermöglicht das Sammeln von Daten zur Leistungsdiagnose von Windows-VMs. Mit der Erweiterung wird eine Analyse durchgeführt und ein Bericht mit Ergebnissen und Empfehlungen bereitgestellt, um Leistungsprobleme auf dem virtuellen Computer zu identifizieren und zu beheben. Diese Erweiterung installiert ein Tool zur Problembehandlung mit dem Namen [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Wenn Sie eine Diagnose auf Ihrem virtuellen Computer aus dem Azure-Portal für nicht klassische VMs ausführen möchten, empfiehlt es sich, die neue Erfahrung zu verwenden. Weitere Informationen finden Sie unter [Leistungsdiagnose für virtuelle Azure-Computer](performance-diagnostics.md). 

## <a name="prerequisites"></a>Voraussetzungen

Diese Erweiterung kann unter Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 und Windows Server 2016 installiert werden. Außerdem ist die Installation unter Windows 8.1 und Windows 10 möglich.

## <a name="extension-schema"></a>Erweiterungsschema
Der folgende JSON-Code zeigt das Schema für die Azure-VM-Erweiterung für die Leistungsdiagnose. Für diese Erweiterung sind der Name und der Schlüssel für ein Speicherkonto erforderlich, in dem die Ausgabe der Diagnose und der Bericht gespeichert werden. Diese Werte sind vertraulich. Speicherkontoschlüssel müssen in einer geschützten Einstellungskonfiguration gespeichert werden. Die geschützten Einstellungsdaten der Azure-VM-Erweiterung werden verschlüsselt und nur auf dem virtuellen Zielcomputer entschlüsselt. Beachten Sie, dass für **storageAccountName** und **storageAccountKey** die Groß- und Kleinschreibung berücksichtigt wird. Weitere erforderliche Parameter sind im folgenden Abschnitt aufgeführt.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Eigenschaftswerte

|   **Name**   |**Wert/Beispiel**|       **Beschreibung**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Die Version der API.
|publisher|Microsoft.Azure.Performance.Diagnostics|Der Herausgebernamespace für die Erweiterung.
|type|AzurePerformanceDiagnostics|Der Typ der VM-Erweiterung.
|typeHandlerVersion|1.0|Die Version des Erweiterungshandlers.
|performanceScenario|basic|Das Leistungsszenario für die Erfassung von Daten. Gültige Werte sind: **basic**, **vmslow**, **azurefiles** und **custom**.
|traceDurationInSeconds|300|Die Dauer der Ablaufverfolgungen, wenn Optionen für die Ablaufverfolgung ausgewählt wurden.
|perfCounterTrace|p|Option zum Aktivieren der Ablaufverfolgung für Leistungsindikatoren. Gültige Werte sind **p** oder ein leerer Wert. Lassen Sie den Wert leer, wenn Sie diese Ablaufverfolgung nicht erfassen möchten.
|networkTrace|n|Option zum Aktivieren der Netzwerkablaufverfolgung. Gültige Werte sind **n** oder ein leerer Wert. Lassen Sie den Wert leer, wenn Sie diese Ablaufverfolgung nicht erfassen möchten.
|xperfTrace|x|Option zum Aktivieren der XPerf-Ablaufverfolgung. Gültige Werte sind **x** oder ein leerer Wert. Lassen Sie den Wert leer, wenn Sie diese Ablaufverfolgung nicht erfassen möchten.
|storPortTrace|s|Option zum Aktivieren der StorPort-Ablaufverfolgung. Gültige Werte sind **s** oder ein leerer Wert. Lassen Sie den Wert leer, wenn Sie diese Ablaufverfolgung nicht erfassen möchten.
|srNumber|123452016365929|Die Supportticketnummer, sofern verfügbar. Lassen Sie den Wert andernfalls leer.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktueller Wert für Datum und Uhrzeit in UTC. Sie müssen diesen Wert nicht angeben, wenn Sie das Portal verwenden, um diese Erweiterung zu installieren.
|resourceId|/subscriptions/{Abonnement-ID}/resourceGroups/{Ressourcengruppenname}/providers/{Ressourcenanbieter-Namespace}/{Ressourcentyp}/{Ressourcenname}|Der eindeutige Bezeichner einer VM.
|storageAccountName|mystorageaccount|Der Name des Speicherkontos zum Speichern der Diagnoseprotokolle und Ergebnisse.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Der Schlüssel für das Speicherkonto.

## <a name="install-the-extension"></a>Installieren der Erweiterung

Führen Sie die folgenden Anweisungen aus, um die Erweiterung auf virtuellen Windows-Computern zu installieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie den virtuellen Computer aus, auf dem Sie diese Erweiterung installieren möchten.

    ![Screenshot: Azure-Portal mit Hervorhebung von virtuellen Computern](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Wählen Sie das Blatt **Erweiterungen** und dann die Option **Hinzufügen**.

    ![Screenshot: Blatt „Erweiterungen“ mit Hervorhebung der Option „Hinzufügen“](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Wählen Sie die **Azure-Leistungsdiagnose**, lesen Sie sich die Geschäftsbedingungen durch, und wählen Sie **Erstellen**.

    ![Screenshot: Bildschirm „Neue Ressource“ mit Hervorhebung der Azure-Leistungsdiagnose](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Geben Sie die Parameterwerte für die Installation an, und wählen Sie **OK**, um die Erweiterung zu installieren. Weitere Informationen zu unterstützten Szenarien finden Sie unter [Verwenden von PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Screenshot: Dialogfeld „Erweiterung installieren“](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Wenn die Installation erfolgreich ist, wird eine entsprechende Meldung angezeigt.

    ![Screenshot: Meldung „Bereitstellung erfolgreich“](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Die Erweiterung wird ausgeführt, wenn die Bereitstellung erfolgreich war. Dieser Vorgang dauert für das grundlegende Szenario maximal zwei Minuten. Für andere Szenarien wird die Dauer bei der Installation angegeben.

## <a name="remove-the-extension"></a>Entfernen der Erweiterung
Um die Erweiterung von einem virtuellen Computer zu entfernen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie den virtuellen Computer, von dem Sie diese Erweiterung entfernen möchten, und dann das Blatt **Erweiterungen** aus. 
2. Wählen Sie in der Liste für den Eintrag der Erweiterung für die Leistungsdiagnose die Option ( **…** ) und dann **Deinstallieren**.

    ![Screenshot: Blatt „Erweiterungen“ mit Hervorhebung der Option „Deinstallieren“](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Sie können auch den Eintrag für die Erweiterung und dann die Option **Deinstallieren** auswählen.

## <a name="template-deployment"></a>Bereitstellung von Vorlagen
Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Das im vorherigen Abschnitt beschriebene JSON-Schema kann in einer Azure Resource Manager-Vorlage verwendet werden. Hierbei wird die Azure-VM-Erweiterung für die Leistungsdiagnose während einer Azure Resource Manager-Vorlagenbereitstellung ausgeführt. Hier ist eine Beispielvorlage angegeben:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell-Bereitstellung
Mit dem Befehl `Set-AzVMExtension` können Sie die Azure-VM-Erweiterung für die Leistungsdiagnose auf einem vorhandenen virtuellen Computer bereitstellen.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informationen zu den erfassten Daten
Das Tool PerfInsights erfasst abhängig vom ausgewählten Szenario verschiedene Protokolle, die Konfiguration und Diagnosedaten. Weitere Informationen finden Sie in der [PerfInsights-Dokumentation](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Anzeigen und Freigeben der Ergebnisse

Die Ausgabe von der Erweiterung kann in einer ZIP-Datei gefunden werden, die in das während der Installation angegebene Speicherkonto hochgeladen wird und für 30 Tage über [Shared Access Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) freigegeben ist. Diese ZIP-Datei enthält Diagnoseprotokolle und einen Bericht mit Ergebnissen und Empfehlungen. Ein SAS-Link zur Ausgabe-ZIP-Datei befindet sich in einer Textdatei namens *Zipdateiname*_saslink.txt im Ordner **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<Version>** . Jede Person kann mit diesem Link die ZIP-Datei herunterladen.

Zur Unterstützung des Supporttechnikers, der Ihr Supportticket bearbeitet, verwendet Microsoft diesen SAS-Link unter Umständen zum Herunterladen der Diagnosedaten.

Um den Bericht anzuzeigen, extrahieren Sie die ZIP-Datei und öffnen die Datei **PerfInsights Report.html**.

Sie sollten die ZIP-Datei auch direkt aus dem Portal herunterladen können, indem Sie die Erweiterung auswählen.

![Screenshot: Ausführlicher Status der Leistungsdiagnose](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Der im Portal angezeigte SAS-Link funktioniert in einigen Fällen nicht. Dies kann durch eine falsch formatierte URL während der Codier- und Decodiervorgänge verursacht werden. Sie können den Link stattdessen direkt der Datei „*_saslink.txt“ auf dem virtuellen Computer entnehmen.

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

- Der Bereitstellungsstatus der Erweiterung (im Infobereich) zeigt unter Umständen „Bereitstellung wird ausgeführt“ an, obwohl die Erweiterung bereits erfolgreich bereitgestellt wurde.

    Dieses Problem kann bedenkenlos ignoriert werden, solange der Erweiterungsstatus anzeigt, dass die Erweiterung erfolgreich bereitgestellt wurde.
- Sie können einige Probleme während der Installation mithilfe der Erweiterungsprotokolle lösen. Die Ausgabe der Erweiterungsausführung wird in Dateien im folgenden Verzeichnis protokolliert:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie die Option **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).
