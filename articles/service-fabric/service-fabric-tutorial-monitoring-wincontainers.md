---
title: Überwachen und Diagnostizieren von Windows-Containern
description: In diesem Tutorial konfigurieren Sie Azure Monitor-Protokolle für die Überwachung und Diagnose von Windows-Containern in Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588247"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Tutorial: Überwachen von Windows-Containern unter Service Fabric mit Azure Monitor-Protokollen

Dieser dritte Teil einer Tutorialreihe führt Sie schrittweise durch das Konfigurieren von Azure Monitor-Protokollen für die Überwachung Ihrer in Service Fabric orchestrierten Windows-Container.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren von Azure Monitor-Protokollen für Ihren Service Fabric-Cluster
> * Verwenden eines Log Analytics-Arbeitsbereichs zum Anzeigen und Abfragen von Protokollen Ihrer Container und Knoten
> * Konfigurieren des Log Analytics-Agents für die Erfassung von Container- und Knotenmetriken

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

* Sie besitzen einen Cluster in Azure oder [erstellen ihn mit diesem Tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Sie stellen eine Containeranwendung dafür bereit](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Einrichten von Azure Monitor-Protokollen mit Ihrem Cluster in der Resource Manager-Vorlage

Falls Sie die im ersten Teil dieses Tutorials [bereitgestellte Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) verwendet haben, sollte sie die folgenden Ergänzungen einer generischen Service Fabric Azure Resource Manager-Vorlage enthalten. Falls Sie über einen eigenen Cluster verfügen, den Sie zur Überwachung von Containern mit Azure Monitor-Protokollen einrichten möchten, gehen Sie wie folgt vor:

* Nehmen Sie die folgenden Änderungen an Ihrer Resource Manager-Vorlage vor.
* Stellen Sie sie mithilfe von PowerShell bereit, um Ihren Cluster durch [Bereitstellen der Vorlage](./service-fabric-cluster-creation-via-arm.md) zu aktualisieren. Azure Resource Manager erkennt, dass die Ressource vorhanden ist, und gibt sie als Upgrade aus.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Hinzufügen von Azure Monitor-Protokollen zu Ihrer Clustervorlage

Nehmen Sie die folgenden Änderungen in Ihrer *template.json*-Datei vor:

1. Fügen Sie den Standort und den Namen des Log Analytics-Arbeitsbereichs dem Abschnitt *parameters* hinzu:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Um einen beliebigen der beiden Werte zu ändern, fügen Sie dieselben Parameter Ihrer *template.parameters.json*-Datei hinzu, und ändern Sie die dort verwendeten Werte.

2. Fügen Sie den Namen der Lösung und die Lösung Ihren *Variablen* hinzu:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Fügen Sie Microsoft Monitoring Agent als VM-Erweiterung hinzu. Suchen Sie die Ressource für VM-Skalierungsgruppen: *resources* >  *"apiVersion": "[variables('vmssApiVersion')]"* . Fügen Sie unter *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* die folgende Erweiterungsbeschreibung unter der Erweiterung *ServiceFabricNode* hinzu: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Fügen Sie den Log Analytics-Arbeitsbereich als einzelne Ressource hinzu. Fügen Sie in *resources* nach der VM-Skalierungsgruppenressource Folgendes hinzu:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Hier](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) finden Sie eine (in Teil 1 dieses Tutorials verwendete) Beispielvorlage, die alle diese Änderungen enthält und Ihnen bei Bedarf als Referenz zur Verfügung steht. Diese Änderungen fügen Ihrer Ressourcengruppe einen Log Analytics-Arbeitsbereich hinzu. Der Arbeitsbereich wird zum Erfassen von Service Fabric-Plattformereignissen aus den Speichertabellen konfiguriert, die mit dem [Microsoft Azure-Diagnose](service-fabric-diagnostics-event-aggregation-wad.md)-Agent konfiguriert wurden. Der Log Analytics-Agent (Microsoft Monitoring Agent) wurde auch jedem Knoten in Ihrem Cluster als VM-Erweiterung hinzugefügt. Das bedeutet Folgendes: Wenn Sie Ihren Cluster skalieren, wird der Agent automatisch auf jedem Computer konfiguriert und mit demselben Arbeitsbereich verknüpft.

Stellen Sie die Vorlage mit den neuen Änderungen zum Aktualisieren Ihres aktuellen Clusters bereit. Nach Abschluss des Vorgangs sollten die Log Analytics-Ressourcen in Ihrer Ressourcengruppe angezeigt werden. Wenn der Cluster bereit ist, stellen Sie die Containeranwendung darauf bereit. Im nächsten Schritt richten wir die Containerüberwachung ein.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Hinzufügen der Containerüberwachungslösung zu Ihrem Log Analytics-Arbeitsbereich

Um Informationen zum Einrichten der Containerlösung in Ihrem Arbeitsbereich zu erhalten, suchen Sie nach *Containerüberwachungslösung*, und erstellen Sie eine Ressource „Container“ (unter der Kategorie „Überwachung + Verwaltung“).

![Hinzufügen der Containerlösung](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Wählen Sie bei Aufforderung zur Eingabe des *Log Analytics-Arbeitsbereichs* den Arbeitsbereich aus, der in der Ressourcengruppe erstellt wurde, und wählen Sie **Erstellen** aus. Dadurch wird Ihrem Arbeitsbereich eine *Containerüberwachungslösung* hinzugefügt und der von der Vorlage bereitgestellte Log Analytics-Agent veranlasst, mit dem Sammeln von Docker-Protokollen und -Statistiken zu beginnen.

Navigieren Sie zurück zu *Ihrer Ressourcengruppe*, wo Sie jetzt die neu hinzugefügte Überwachungslösung finden sollten. Wenn Sie sie auswählen, sollte auf der Landing Page die Anzahl der von Ihnen ausgeführten Containerimages angezeigt werden.

*Beachten Sie, dass fünf Instanzen des fabrikam-Containers aus [Teil 2](service-fabric-host-app-in-a-container.md) des Tutorials ausgeführt wurden.*

![Landing Page für Containerlösung](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Wenn Sie **Container Monitor Solution** auswählen, gelangen Sie zu einem ausführlicheren Dashboard, auf dem Sie durch mehrere Bereiche scrollen sowie Abfragen in Azure Monitor-Protokollen ausführen können.

Da der Agent Docker-Protokolle erfasst, werden standardmäßig *stdout* und *stderr* angezeigt. Wenn Sie horizontal scrollen, sehen Sie Containerimagebestand, Status, Metriken und Beispielabfragen, die Sie ausführen könnten, um weitere nützliche Daten zu erhalten.

![Containerlösungsdashboard](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Durch Klicken auf einen der folgenden Bereiche gelangen Sie zu der Kusto-Abfrage, die den angezeigten Wert generiert. Ändern Sie die Abfrage in *\** , um die verschiedenen Arten von Protokollen anzuzeigen, die erfasst werden. Von hier aus können Sie nach Containerleistung und Protokollen abfragen oder filtern bzw. Service Fabric-Plattformereignisse betrachten. Die Agents geben auch ständig einen Heartbeat von jedem Knoten aus, den Sie sich ansehen können, um sicherzustellen, dass immer noch Daten von allen Computern gesammelt werden, wenn sich Ihre Clusterkonfiguration ändert.

![Containerabfrage](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurieren des Log Analytics-Agents zum Erfassen von Leistungsindikatoren

Ein weiterer Vorteil der Verwendung des Log Analytics-Agent besteht darin, dass die Leistungsindikatoren geändert werden können, die Sie über die Log Analytics-Benutzeroberfläche erfassen möchten, anstatt jedes Mal den Azure-Diagnose-Agent konfigurieren und ein Upgrade auf Basis der Resource Manager-Vorlage durchführen zu müssen. Wählen Sie hierzu auf der Landing Page Ihrer Containerüberwachungslösung (oder Service Fabric-Lösung) die Option **OMS-Arbeitsbereich** aus.

Daraufhin gelangen Sie zu Ihrem Log Analytics-Arbeitsbereich. Dort können Sie Ihre Lösungen anzeigen, benutzerdefinierte Dashboards erstellen sowie den Log Analytics-Agent konfigurieren. 
* Wählen Sie zum Öffnen des Menüs mit den erweiterten Einstellungen **Erweiterte Einstellungen** aus.
* Wählen Sie **Verbundene Quellen** > **Windows-Server** aus, um sicherzustellen, dass *fünf Windows-Computer verbunden sind*.
* Wählen Sie **Daten** > **Windows-Leistungsindikatoren** aus, um neue Leistungsindikatoren zu suchen und hinzuzufügen. Hier sehen Sie eine Liste mit den Empfehlungen aus Azure Monitor-Protokollen für Leistungsindikatoren, die Sie erfassen können, sowie die Option zum Suchen nach anderen Indikatoren. Vergewissern Sie sich, dass die Leistungsindikatoren **Processor(_Total)\%Prozessorzeit** und **Memory(*)\Verfügbare MB** erfasst werden.

**Aktualisieren** Sie Ihre Containerüberwachungslösung nach einigen Minuten. Daraufhin sollten Daten zur *Computerleistung* eingehen. Dies hilft Ihnen zu verstehen, wie Ihre Ressourcen verwendet werden. Sie können diese Metriken auch für entsprechende Entscheidungen zur Skalierung Ihrer Cluster verwenden, oder um zu überprüfen, ob ein Cluster Ihre Last wie erwartet ausgleicht.

*Hinweis: Stellen Sie sicher, dass Ihre Zeitfilter gemäß Ihrer Nutzung dieser Metriken festgelegt sind.*

![Leistungsindikatoren 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren von Azure Monitor-Protokollen für Ihren Service Fabric-Cluster
> * Verwenden eines Log Analytics-Arbeitsbereichs zum Anzeigen und Abfragen von Protokollen Ihrer Container und Knoten
> * Konfigurieren des Log Analytics-Agents für die Erfassung von Container- und Knotenmetriken

Nun haben Sie die Überwachung für Ihre Containeranwendung konfiguriert und können Folgendes versuchen:

* Konfigurieren Sie mithilfe einer ähnlichen Vorgehensweise wie in diesem Tutorial Azure Monitor-Protokolle für einen Linux-Cluster. Nutzen Sie [diese Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS), um Änderungen in der Resource Manager-Vorlage vorzunehmen.
* Konfigurieren Sie Azure Monitor-Protokolle für die Einrichtung von [automatisierten Warnungen](../azure-monitor/alerts/alerts-overview.md) zur Unterstützung bei der Erkennung und Diagnose.
* Untersuchen Sie die Service Fabric-Liste der [empfohlen Leistungsindikatoren](service-fabric-diagnostics-event-generation-perf.md), um Ihre Cluster zu konfigurieren.
* Machen Sie sich mit den Features zur [Protokollsuche und -abfrage](../azure-monitor/logs/log-query-overview.md) in Azure Monitor-Protokollen vertraut.
