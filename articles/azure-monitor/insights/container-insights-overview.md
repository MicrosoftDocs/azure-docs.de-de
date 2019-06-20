---
title: Übersicht zu Azure Monitor für Container | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Azure Monitor für Container, das die AKS Container Insights-Lösung überwacht, und den Wert, den es durch die Überwachung der Integrität Ihrer AKS-Cluster und Containerinstanzen in Azure bietet.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521835"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor für Container – Übersicht

Azure Monitor für Container ist ein Feature zum Überwachen der Leistung von Containerworkloads, die entweder in Azure Container Instances oder in Managed Kubernetes-Clustern bereitgestellt sind, die im Azure Kubernetes Service (AKS) gehostet werden. Die Überwachung Ihrer Container ist vor allem dann entscheidend, wenn Sie einen umfangreichen Produktionscluster mit mehreren Anwendungen ausführen.

Azure Monitor für Container visualisiert die Leistung, indem anhand der Metrik-API die in Kubernetes verfügbaren Speicher- und Prozessormetriken von Controllern, Knoten und Containern erfasst werden. Auch Containerprotokolle werden erfasst.  Nach der Aktivierung der Überwachung auf Kubernetes-Clustern werden Metriken und Protokolle für Sie automatisch mittels einer Containerversion des Log Analytics-Agents für Linux erfasst. Metriken werden in den Metrikspeicher geschrieben, und Protokolldaten werden in den Protokollspeicher geschrieben, der Ihrem [Log Analytics](../log-query/log-query-overview.md)-Arbeitsbereich zugeordnet ist. 

![Architektur von Azure Monitor für Container](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Was bietet Azure Monitor für Container?

Azure Monitor für Container bietet umfassende Überwachung mithilfe verschiedener Funktionen von Azure Monitor, damit Sie Kenntnis über die Leistung und Integrität Ihres Kubernetes-Clusters und die Containerworkloads haben. Azure Monitor für Container bietet Ihnen folgende Möglichkeiten:

* Sie können auf dem Knoten ausgeführte AKS-Container sowie deren durchschnittliche Prozessor- und Speicherauslastung ermitteln. Mit diesem Wissen können Sie Ressourcenengpässe erkennen.
* Ermitteln Sie die Prozessor- und Arbeitsspeichernutzung von Containergruppen und den zugehörigen Containern, die in Azure Container Instances gehostet werden.  
* Sie können feststellen, wo im Controller bzw. im Pod sich der Container befindet. Dadurch können Sie die Gesamtleistung des Controllers bzw. Pods anzeigen.
* Sie können die Ressourcenauslastung von Workloads überprüfen, die unabhängig von den Standardprozessen, die den Pod unterstützen, im Host ausgeführt werden.
* Sie bekommen Einblicke in das Verhalten des Clusters bei durchschnittlichen und schwersten Lasten. So können Sie benötigte Kapazitäten ermitteln und die maximale Last bestimmen, die der Cluster toleriert. 
* Konfigurieren Sie Warnungen, um Sie proaktiv zu benachrichtigen oder aufzuzeichnen, wenn die CPU- und Arbeitsspeicherauslastung auf Knoten oder in Containern die Schwellenwerte überschreitet.  

## <a name="how-do-i-access-this-feature"></a>Wie greife ich auf dieses Feature zu?
Sie können auf zwei Arten auf Azure Monitor für Container zugreifen, aus Azure Monitor oder direkt aus dem ausgewählten AKS-Cluster. In Azure Monitor haben Sie eine globale Perspektive aller bereitgestellten Container, der überwachten wie auch der nicht überwachten, was es Ihnen ermöglicht, über Ihre Abonnements und Ressourcengruppen zu suchen und zu filtern und dann Drilldowns aus einem ausgewählten Container in Azure Monitor für Container auszuführen.  Andernfalls können Sie von der AKS-Seite aus einem ausgewählten AKS-Container auf das Feature direkt zugreifen.  

![Übersicht der Methoden für den Zugriff auf Azure Monitor für Container](./media/container-insights-overview/azmon-containers-experience.png)

Wenn Sie mehr über die Überwachung und Verwaltung Ihrer Docker- und Windows-Containerhosts für die Anzeige von Informationen zur Konfiguration, Überwachung und Ressourcenverwendung erfahren möchten, lesen Sie den Artikel zur [Containerüberwachungslösung](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nächste Schritte
Um in die Überwachung Ihrer AKS-Cluster einzusteigen, lesen Sie [Aktivieren von Azure Monitor für Container](container-insights-onboard.md), um ein Verständnis für die Anforderungen und verfügbaren Methoden zum Aktivieren von Überwachung zu entwickeln.  
