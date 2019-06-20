---
title: Aktivieren von Azure Monitor (Vorschauversion) für eine Hybridumgebung | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs für eine Hybridcloudumgebung aktivieren, die einen oder mehrere virtuelle Computer enthält.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 6b8870f0a6f14536fdf3a1ff675f2fbe3ce8aeec
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524052"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion) für eine Hybridumgebung

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In diesem Artikel wird erläutert, wie Sie Azure Monitor für VMs (Vorschauversion) für virtuelle oder physische Computer aktivieren, die in Ihrem Rechenzentrum oder einer anderen Cloudumgebung gehostet sind. Nachdem Sie diesen Prozess durchgeführt haben, haben Sie erfolgreich damit begonnen, die virtuellen Computer in Ihrer Umgebung zu überwachen, und werden erfahren, ob Leistungs- oder Verfügbarkeitsprobleme bei diesen Computern auftreten. 

Bevor Sie beginnen, überprüfen Sie die [Voraussetzungen](vminsights-enable-overview.md), und stellen Sie sicher, dass Ihr Abonnement und Ihre Ressourcen die Anforderungen erfüllen. Überprüfen Sie die Anforderungen und Bereitstellungsmethoden für den [Log Analytics-Linux- und -Windows-Agent](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Der Dependency-Agent für das Zuordnungsfeature von Azure Monitor für VMs überträgt selbst keine Daten und erfordert keine Änderungen an Firewalls oder Ports. Die Zuordnungsdaten werden immer vom Log Analytics-Agent an den Azure Monitor-Dienst übertragen, entweder direkt oder über das [OMS-Gateway](../../azure-monitor/platform/gateway.md), falls Ihre IT-Sicherheitsrichtlinien es nicht zulassen, dass Computer im Netzwerk eine Verbindung mit dem Internet herstellen.

Die Schritte zur Ausführung dieser Aufgabe sind wie folgt zusammengefasst:

1. Installieren Sie den Log Analytics-Agent für Windows oder Linux.

2. Laden Sie den Dependency-Agent für das Zuordnungsfeature von Azure Monitor für VMs für [Windows](https://aka.ms/dependencyagentwindows) oder [Linux](https://aka.ms/dependencyagentlinux) herunter, und installieren Sie ihn.

3. Aktivieren Sie das Sammeln von Leistungsindikatoren.

4. Stellen Sie Azure Monitor für VMs bereit.

## <a name="install-the-dependency-agent-on-windows"></a>Installieren des Dependency-Agents unter Windows
Sie können den Dependency-Agent durch Ausführen von `InstallDependencyAgent-Windows.exe` manuell auf Windows-Computern installieren. Wenn Sie diese ausführbare Datei ohne Optionen ausführen, wird ein Setup-Assistent gestartet, mit dem Sie die Installation des Agent interaktiv durchführen können.

>[!NOTE]
>Zum Installieren oder Deinstallieren des Agent sind *Administratorrechte* erforderlich.

In der folgenden Tabelle sind die Parameter hervorgehoben, die von Setup für den Agent auf der Befehlszeile unterstützt werden.

| Parameter | BESCHREIBUNG |
|:--|:--|
| /? | Gibt eine Liste der Befehlszeilenoptionen zurück. |
| /S | Führt eine automatische Installation ohne Benutzereingriff aus. |

Um das Installationsprogramm beispielsweise mit dem Parameter `/?` auszuführen, geben Sie **InstallDependencyAgent-Windows.exe /?** ein.

Dateien für den Dependency-Agent für Windows werden standardmäßig in *C:\Programme\Microsoft Dependency Agent* installiert. Falls der Dependency-Agent nach dem Abschluss von Setup nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Das Protokollverzeichnis ist *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installieren des Dependency-Agents unter Linux
Der Dependency-Agent wird auf Linux-Servern mit *InstallDependencyAgent-Linux64.bin* installiert, einem Shellskript mit einer selbstextrahierenden Binärdatei. Sie können die Datei mit `sh` ausführen oder der Datei selbst Ausführungsberechtigungen hinzufügen.

>[!NOTE]
> Zum Installieren oder Konfigurieren des Agent ist Root-Zugriff erforderlich.
>

| Parameter | BESCHREIBUNG |
|:--|:--|
| -help | Ruft eine Liste der Befehlszeilenoptionen ab. |
| -s | Führt eine automatische Installation ohne Benutzereingaben aus. |
| --check | Überprüft Berechtigungen und das Betriebssystem, ohne den Agent zu installieren. |

Um das Installationsprogramm beispielsweise mit dem Parameter `-help` auszuführen, geben Sie **InstallDependencyAgent-Linux64.bin -help** ein.

Installieren Sie den Dependency-Agent für Linux mit Root-Berechtigungen, indem Sie den folgenden Befehl ausführen: `sh InstallDependencyAgent-Linux64.bin`.

Falls der Dependency-Agent nicht gestartet wird, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Für Linux-Agents lautet das Protokollverzeichnis */var/opt/microsoft/dependency-agent/log*.

Dateien für den Dependency-Agent werden in den folgenden Verzeichnissen gespeichert:

| Dateien | Location |
|:--|:--|
| Hauptdateien | /opt/microsoft/dependency-agent |
| Protokolldateien | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsdateien | /etc/opt/microsoft/dependency-agent/config |
| Ausführbare Dienstdateien | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binäre Speicherdateien | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Aktivieren von Leistungsindikatoren
Wenn der Log Analytics-Arbeitsbereich, auf den die Lösung verweist, noch nicht für die Erfassung der von der Lösung benötigten Leistungsindikatoren konfiguriert ist, müssen Sie diese aktivieren. Dies kann auf zwei Arten erfolgen:
* Manuell, wie in [Windows- und Linux-Leistungsindikatoren in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md) beschrieben.
* Durch Herunterladen und Ausführen eines PowerShell-Skripts, das über den [Azure PowerShell-Katalog](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) verfügbar ist.

## <a name="deploy-azure-monitor-for-vms"></a>Bereitstellen von Azure Monitor für VMs
Diese Methode umfasst eine JSON-Vorlage, die die Konfiguration zum Aktivieren der Lösungskomponenten für Ihren Log Analytics-Arbeitsbereich angibt.

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen Azure CLI 2.0.27 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Erstellen und Ausführen von Vorlagen

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Speichern Sie diese Datei als *installsolutionsforvminsights.json* in einem lokalen Ordner.

1. Erfassen Sie die Werte für *WorkspaceName*, *ResourceGroupName* und *WorkspaceLocation*. Der Wert für *WorkspaceName* ist der Name des Log Analytics-Arbeitsbereichs. Der Wert für *WorkspaceLocation* ist die Region, in der der Arbeitsbereich definiert ist.

1. Sie können diese Vorlage mithilfe des folgenden PowerShell-Befehls bereitstellen:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Die Änderung der Konfiguration kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt:

    ```powershell
    provisioningState       : Succeeded
    ```
   Nach dem Aktivieren der Überwachung kann es ca. 10 Minuten dauern, bis Integritätszustand und Metriken für den Hybridcomputer angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihre virtuellen Computer aktiviert wurde, stehen diese Informationen für die Analyse mit Azure Monitor für VMs zur Verfügung. Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md). Informationen zum Erkennen von Engpässen und Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [View Azure VM Performance](vminsights-performance.md) (Anzeigen der Leistung von Azure-VMs). Informationen zu erkannten Anwendungsabhängigkeiten finden Sie unter [View Azure Monitor for VMs Map](vminsights-maps.md) (Anzeigen der Zuordnung von Azure Monitor for VMs).