---
title: Avere vFXT-Clusteroptimierung – Azure
description: Übersicht über die benutzerdefinierten Einstellungen zur Leistungsoptimierung in Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409160"
---
# <a name="cluster-tuning"></a>Clusteroptimierung


Die meisten vFXT-Cluster können von benutzerdefinierten Leistungseinstellungen profitieren. Diese Einstellungen helfen dem Cluster, optimal mit Ihrem speziellen Workflow, Dataset und Ihren Tools zu arbeiten. 

Diese Anpassung sollte zusammen mit einem Supportmitarbeiter durchgeführt werden, da es in der Regel darum geht, Features zu konfigurieren, die nicht über die Avere-Systemsteuerung verfügbar sind.

In diesem Abschnitt werden einige der benutzerdefinierten Einstellungen erläutert, die vorgenommen werden können.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Allgemeine Optimierungen

Diese Änderungen können aufgrund von Datasetqualitäten oder auf Basis des Workflowstils empfohlen werden.

* Wenn der Workload sehr viele Schreibvorgänge umfasst, erhöhen Sie die standardmäßige Größe des Schreibcache von 20 %. 
* Wenn das Dataset viele kleine Dateien umfasst, erhöhen Sie den Grenzwert für die Dateianzahl des Clustercache. 
* Wenn die Arbeit das Kopieren oder Verschieben von Daten zwischen zwei Repositorys umfasst, passen Sie die Anzahl der Threads an, die zum Verschieben von Daten verwendet werden: 
  * Um die Geschwindigkeit zu erhöhen, können Sie die Anzahl der verwendeten parallelen Threads erhöhen.
  * Wenn das Back-End-Speichervolume überlastet wird, müssen Sie die Anzahl der parallel verwendeten Threads möglicherweise verringern.
* Wenn der Cluster Daten für eine Kernspeichereinheit zwischenspeichert, die NFSv4 ACLs verwendet, aktivieren Sie das Zugriffsmoduscaching, um die Dateiautorisierung für bestimmte Clients zu optimieren.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optimierungen für Cloud-NAS oder Cloudgateway

Um die Vorteile höherer Datengeschwindigkeiten zwischen vFXT-Cluster und Cloudspeicher in einem Cloud-NAS- oder Cloudgateway-Szenario zu nutzen (wobei der vFXT-Cluster einen NAS-artigen Zugriff auf einen Cloudcontainer ermöglicht), empfiehlt Ihr Berater möglicherweise, Einstellungen wie diese zu ändern, um Daten aggressiver aus dem Cache per Push auf das Speichervolumen zu übertragen:

* Erhöhen Sie die Anzahl der TCP-Verbindungen zwischen dem Cluster und dem Speichercontainer.
* Verringern Sie den REST-Timeoutwert für die Kommunikation zwischen Cluster und Speicher, um Schreibvorgänge früher zu wiederholen, wenn sie nicht sofort erfolgreich sind.  
* Vergrößern Sie die Segmentgröße, sodass jedes Back-End-Schreibsegment anstelle von 1 MB einen 8 MB großen Datenblock überträgt.

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloudbursting oder Hybrid-WAN-Optimierungen

In einem Cloudbursting-Szenario oder einem Szenario mit Hybrid-Speicher-WAN-Optimierung (bei dem der vFXT-Cluster die Integration zwischen Cloud und lokalem Hardwarespeicher ermöglicht) können die folgenden Änderungen hilfreich sein:

* Erhöhen Sie die Anzahl der TCP-Verbindungen, die zwischen Cluster und Kernspeichereinheit erlaubt sind.
* Aktivieren Sie die Einstellung zur WAN-Optimierung für die Remotekernspeichereinheit (Diese Einstellung kann für eine On-Premises-Remotespeichereinheit oder eine Cloudkernspeichereinheit in einer anderen Azure-Region verwendet werden).
* Erhöhen Sie die Größe des TCP-Socket-Puffers (je nach Workload und Leistungsanforderungen).
* Aktivieren Sie die Einstellung „immer weiterleiten“, um redundante Cachedateien zu verringern (je nach Workload und Leistungsanforderungen).

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Hilfe bei der Optimierung von Avere vFXT für Azure

Verwenden Sie das unter [Abrufen von Hilfe zu Ihrem System](avere-vfxt-open-ticket.md) beschriebene Verfahren, um den Support über diese Optimierungen zu informieren. 