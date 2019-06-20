---
title: 'Azure Service Fabric: Einrichten von Überwachung mit Azure Monitor-Protokollen | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Monitor-Protokolle für die Visualisierung und Analyse von Ereignissen einrichten, um Ihre Azure Service Fabric-Cluster zu überwachen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: c8f7198b59a0fe7ed6775736f8b97f5b5a262640
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306858"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Einrichten von Azure Monitor-Protokollen für einen Cluster

Azure Monitor-Protokolle sind unsere Empfehlung für die Überwachung von Ereignissen auf Clusterebene. Sie können einen Log Analytics-Arbeitsbereich über den Azure Resource Manager, über PowerShell oder über den Azure Marketplace einrichten. Wenn Sie eine aktualisierte Resource Manager-Vorlage von Ihrer Bereitstellung verwalten, verwenden Sie zukünftig die gleiche Vorlage für die Einrichtung Ihrer Umgebung für Azure Monitor-Protokolle. Die Bereitstellung über den Marketplace ist einfacher, wenn Sie bereits einen Cluster mit aktivierter Diagnose bereitgestellt haben. Wenn Sie in dem Konto, für das Sie die Bereitstellung durchführen, keinen Zugriff auf Abonnementebene haben, führen Sie die Bereitstellung über PowerShell oder über die Resource Manager-Vorlage durch.

> [!NOTE]
> Zum Einrichten von Azure Monitor-Protokollen zur Überwachung Ihres Clusters muss die Diagnose aktiviert sein, um Ereignisse auf Cluster- oder Plattformebene anzeigen zu können. Weitere Informationen finden Sie unter [Ereignisaggregation und -sammlung mit der Windows Azure-Diagnose](service-fabric-diagnostics-event-aggregation-wad.md) und [Ereignisaggregation und -sammlung mit Linux-Azure-Diagnose](service-fabric-diagnostics-oms-syslog.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Bereitstellen eines Log Analytics-Arbeitsbereichs mithilfe von Azure Marketplace

Wenn Sie einen Log Analytics-Arbeitsbereich nach dem Bereitstellen eines Clusters hinzufügen möchten, navigieren Sie im Portal zum Azure Marketplace, und suchen Sie nach **Service Fabric-Analyse**. Dies ist eine benutzerdefinierte Lösung für Service Fabric-Bereitstellungen, die für Service Fabric spezifische Daten enthält. In diesem Prozess erstellen Sie die Projektmappe (das Dashboard, um Einblicke zu erhalten) und den Arbeitsbereich (die Aggregation der zugrunde liegenden Clusterdaten).

1. Wählen Sie im Navigationsmenü auf der linken Seite die Option **Neu** aus. 

2. Suchen Sie nach **Service Fabric-Analyse**. Wählen Sie die angezeigte Ressource aus.

3. Klicken Sie auf **Erstellen**.

    ![Service Fabric-Analysen im Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Wählen Sie im Fenster für die Erstellung der Service Fabric-Analyse im Feld **OMS-Arbeitsbereich** die Option **Arbeitsbereich auswählen** und dann **Neuen Arbeitsbereich erstellen** aus. Nehmen Sie die erforderlichen Eingaben vor. Einzige Anforderung hierbei ist, dass für den Service Fabric-Cluster und den Arbeitsbereich dasselbe Abonnement verwendet werden muss. Nachdem die Eingaben überprüft wurden, wird Ihr Arbeitsbereich bereitgestellt. Der Vorgang dauert nur wenige Minuten.

5. Wählen Sie anschließend unten im Fenster zur Erstellung der Service Fabric-Analyse erneut **Erstellen** aus. Stellen Sie sicher, dass der neue Arbeitsbereich unter **OMS-Arbeitsbereich** angezeigt wird. Mit dieser Aktion wird die Lösung dem erstellten Arbeitsbereich hinzugefügt.

Wenn Sie Windows verwenden, fahren Sie mit den folgenden Schritten zum Verknüpfen von Azure Monitor-Protokollen mit dem Speicherkonto fort, in dem die Clusterereignisse gespeichert werden. 

>[!NOTE]
>Die Service Fabric Analytics-Lösung wird nur für Windows-Cluster unterstützt. Für Linux-Cluster lesen Sie unseren Artikel zum [Einrichten von Azure Monitor-Protokollen für Linux-Cluster](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Verbinden des Log Analytics-Arbeitsbereichs mit Ihrem Cluster 

1. Der Arbeitsbereich muss mit den Diagnosedaten aus Ihrem Cluster verknüpft werden. Navigieren Sie zu der Ressourcengruppe, in der Sie die Lösung der Service Fabric-Analyse erstellt haben. Wählen Sie **ServiceFabric\<-Arbeitsbereichsname\>** aus, und wechseln Sie zur zugehörigen Übersichtsseite. Dort können Sie Lösungseinstellungen und Arbeitsbereichseinstellungen ändern und auf den Log Analytics-Arbeitsbereich zugreifen.

2. Wählen Sie im linken Navigationsmenü unter **Speicherkontoprotokolle** die Option **Arbeitsbereichsdatenquellen** aus.

3. Wählen Sie auf der Seite **Speicherkontoprotokolle** ganz oben die Option **Hinzufügen** aus, um dem Arbeitsbereich die Protokolle Ihres Clusters hinzuzufügen.

4. Wählen Sie **Speicherkonto** aus, um das geeignete in Ihrem Cluster erstellte Konto hinzuzufügen. Wenn Sie den Standardnamen verwendet haben, heißt das Speicherkonto **sfdg\<Ressourcengruppenname\>** . Sie können dies auch mithilfe der Azure Resource Manager-Vorlage überprüfen, die zum Bereitstellen des Clusters verwendet wurde, indem Sie den für **applicationDiagnosticsStorageAccountName** verwendeten Wert prüfen. Wenn der Name nicht angezeigt wird, scrollen Sie nach unten, und wählen Sie **Weitere laden** aus. Wählen Sie den Speicherkontonamen aus.

5. Geben Sie den Datentyp an. Legen Sie ihn auf **Service Fabric-Ereignisse** fest.

6. Vergewissern Sie sich, dass die Quelle automatisch auf **WADServiceFabric\*EventTable** festgelegt wird.

7. Wählen Sie **OK** aus, um den Arbeitsbereich mit den Protokollen Ihres Clusters zu verbinden.

    ![Hinzufügen von Speicherkontoprotokollen zu Azure Monitor-Protokollen](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Das Konto wird jetzt als Teil Ihrer Speicherkontoprotokolle in den Datenquellen Ihres Arbeitsbereichs angezeigt.

Damit haben Sie die Service Fabric-Analyse-Lösung einem Log Analytics-Arbeitsbereich hinzugefügt, der jetzt vollständig mit der Plattform Ihres Clusters sowie der Anwendungsprotokolltabelle verbunden ist. Sie können dem Arbeitsbereich zusätzliche Quellen auf die gleiche Weise hinzufügen.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Bereitstellen von Azure Monitor-Protokollen mit Azure Resource Manager

Beim Bereitstellen eines Clusters mithilfe einer Resource Manager-Vorlage wird mit der Vorlage ein neuer Log Analytics-Arbeitsbereich erstellt, diesem die Service Fabric-Lösung hinzugefügt und der Arbeitsbereich so konfiguriert, dass Daten aus den entsprechenden Speichertabellen gelesen werden.

Sie können [diese Beispielvorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) verwenden und entsprechend Ihren Anforderungen ändern. Mit dieser Vorlage werden die folgenden Aktionen ausgeführt

* Erstellen eines neuen Service Fabric-Clusters mit 5 Knoten
* Erstellen eines Log Analytics-Arbeitsbereichs und einer Service Fabric-Lösung
* Konfigurieren des Log Analytics-Agents zum Erfassen und Senden von zwei Beispielleistungsindikatoren an den Arbeitsbereich
* Konfigurieren von WAD zum Erfassen von Service Fabric und Senden an Azure Storage-Tabellen (WADServiceFabric*EventTable)
* Konfigurieren des Log Analytics-Arbeitsbereichs zum Lesen der Ereignisse aus diesen Tabellen


Sie können die Vorlage als Resource Manager-Upgrade für Ihren Cluster bereitstellen. Verwenden Sie dazu die `New-AzResourceGroupDeployment`-API im Azure PowerShell-Modul. Beispielbefehl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager erkennt, dass es sich bei diesem Befehl um ein Update für eine vorhandene Ressource handelt. Daher werden nur die Änderungen zwischen der Vorlage für die vorhandene Bereitstellung und der neu bereitgestellten Vorlage verarbeitet.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Bereitstellen von Azure Monitor-Protokollen mit Azure PowerShell

Sie können Ihre Protokollanalyseressource auch über PowerShell mit dem Befehl `New-AzOperationalInsightsWorkspace` bereitstellen. Stellen Sie hierfür sicher, dass [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) installiert ist. Erstellen Sie mithilfe dieses Skripts einen neuen Log Analytics-Arbeitsbereich, und fügen Sie diesem die Service Fabric-Lösung hinzu: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Führen Sie anschließend die Schritte im vorherigen Abschnitt aus, um Azure Monitor-Protokolle mit dem entsprechenden Speicherkonto zu verknüpfen.

Mithilfe von PowerShell können Sie zudem weitere Lösungen hinzufügen oder andere Änderungen am Log Analytics-Arbeitsbereich vornehmen. Weitere Informationen finden Sie unter [Verwalten von Azure Monitor-Protokollen mit PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Nächste Schritte
* [Stellen Sie den Log Analytics-Agent auf Ihren Knoten bereit](service-fabric-diagnostics-oms-agent.md), um Leistungsindikatoren zu erfassen und Docker-Statistiken und -Protokolle für Ihre Container zu sammeln.
* Machen Sie sich mit den Features zur [Protokollsuche und -abfrage](../log-analytics/log-analytics-log-searches.md) in Azure Monitor-Protokollen vertraut.
* [Erstellen benutzerdefinierter Ansichten in Azure Monitor-Protokollen mithilfe des Ansicht-Designers](../azure-monitor/platform/view-designer.md)
