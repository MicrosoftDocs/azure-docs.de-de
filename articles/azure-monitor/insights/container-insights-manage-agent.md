---
title: Verwalten des Agent für Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird die Verwaltung der häufigsten Wartungsaufgaben mit dem containerbasierten Log Analytics-Agent beschrieben, der von Azure Monitor für Container verwendet wird.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: e1d47be159d4721aed4b055a51acf675688b855e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071796"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Verwalten des Agent für Azure Monitor für Container
Für Azure Monitor für Container wird eine Containerversion des Log Analytics-Agents für Linux verwendet. Nach der ersten Bereitstellung gibt es Routine- oder optionale Aufgaben, die Sie während des Lebenszyklus ausführen müssen. In diesem Artikel wird beschrieben, wie Sie das Upgrade des Agent manuell durchführen und die Sammlung von Umgebungsvariablen aus einem bestimmten Container deaktivieren können. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Durchführen eines Upgrades für den Agent für Azure Monitor für Container
Für Azure Monitor für Container wird eine Containerversion des Log Analytics-Agents für Linux verwendet. Wenn eine neue Version des Agents veröffentlicht wird, wird automatisch ein Upgrade des Agents in Ihren Managed Kubernetes-Clustern ausgeführt, die im Azure Kubernetes Service (AKS) gehostet werden.  

In diesem Artikel wird der Prozess für das manuelle Upgrade des Agent beschrieben, falls beim Agent-Upgrade ein Fehler auftritt. Informationen zu den freigegebenen Versionen finden Sie unter [Agent-Versionsankündigungen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Aktualisieren des Agents im überwachten Kubernetes-Cluster
Der Prozess für das Upgrade des Agents besteht aus zwei einfachen Schritten. Der erste Schritt umfasst das Deaktivieren der Überwachung mit Azure Monitor für Container per Azure CLI.  Führen Sie die Schritte aus, die im Artikel [Deaktivieren der Überwachung](container-insights-optout.md?#azure-cli) beschrieben sind. Indem wir die Azure CLI verwenden, können wir den Agent von den Knoten im Cluster entfernen, ohne dass sich Auswirkungen auf die Lösung und die entsprechenden Daten ergeben, die im Arbeitsbereich gespeichert sind. 

>[!NOTE]
>Während der Durchführung dieser Wartungsaktivität leiten die Knoten im Cluster keine gesammelten Daten weiter, und die Leistungsansichten zeigen keine Daten für den Zeitraum an, in dem Sie den Agent entfernen und die neue Version installieren. 
>

Führen Sie zum Installieren der neuen Agent-Version die Schritte aus, die im Artikel zum [Aktivieren der Überwachung mit der Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli) beschrieben sind, um diesen Vorgang abzuschließen.  

Nach dem erneuten Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis aktualisierte Integritätsmetriken für den Cluster angezeigt werden. Um zu überprüfen, ob das Agent-Upgrade erfolgreich war, führen Sie den folgenden Befehl aus: `kubectl logs omsagent-484hw --namespace=kube-system`

Der Status sollte dem folgenden Beispiel ähneln, wobei der Wert für *omi* und *omsagent* mit der neuesten Version übereinstimmen sollte, die im [Agent-Versionsverlauf](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) angegeben ist.  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Deaktivieren der Sammlung von Umgebungsvariablen für einen Container
Azure Monitor für Container sammelt Umgebungsvariablen von den in einem Pod ausgeführten Containern und stellt sie im Eigenschaftenbereich des ausgewählten Containers in der Ansicht **Container** dar. Sie können dieses Verhalten steuern, indem Sie die Sammlung für einen bestimmten Container entweder während der Bereitstellung des AKS-Clusters oder danach deaktivieren, indem Sie die Umgebungsvariable *AZMON_COLLECT_ENV* festlegen. Dieses Feature ist ab der Agent-Version ciprod11292018 verfügbar.  

Um die Sammlung von Umgebungsvariablen für einen neuen oder vorhandenen Container zu deaktivieren, legen Sie für die Variable **AZMON_COLLECT_ENV** den Wert **False** in Ihrer yaml-Konfigurationsdatei für die Kubernetes-Bereitstellung fest.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Führen Sie den folgenden Befehl aus, um die Änderung auf Ihren AKS-Container anzuwenden: `kubectl apply -f  <path to yaml file>`.

Um zu überprüfen, ob die Konfigurationsänderung wirksam wurde, wählen Sie einen Container in der Ansicht **Container** in Azure Monitor für Container aus, und erweitern Sie dann im Eigenschaftenbereich die Option **Umgebungsvariablen**.  Der Abschnitt sollte nur die zuvor erstellte Variable anzeigen: **AZMON_COLLECT_ENV=FALSE**. Für alle anderen Container sollte der Abschnitt „Umgebungsvariablen“ alle erkannten Umgebungsvariablen auflisten.   

Um die Erkennung der Umgebungsvariablen wieder zu aktivieren, wenden Sie denselben vorherigen Prozess an, und ändern Sie den Wert von **False** in **True**. Anschließend wiederholen Sie den Befehl `kubectl`, um den Container zu aktualisieren.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Nächste Schritte
Falls beim Aktualisieren des Agents Probleme auftreten, hilft Ihnen der [Leitfaden zur Problembehandlung](container-insights-troubleshoot.md) weiter.