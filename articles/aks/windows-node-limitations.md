---
title: Einschränkungen für Windows Server-Knotenpools in Azure Kubernetes Service (AKS)
description: Informationen zu den bekannten Einschränkungen beim Ausführen von Windows Server-Knotenpools und Anwendungsworkloads in Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475406"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuelle Einschränkungen für Windows Server-Knotenpools und Anwendungsworkloads in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) können Sie einen Knotenpool erstellen, der Windows Server als Gastbetriebssystem auf den Knoten ausführt. Auf diesen Knoten können native Windows-Containeranwendungen ausgeführt werden, z. B. die in .NET Framework integrierten Anwendungen. Da es große Unterschiede in der Art und Weise gibt, wie Container unter Linux- und Windows-Betriebssystemen unterstützt werden, sind einige allgemeine Kubernetes- und podbezogene Funktionen derzeit nicht für Windows-Knotenpools verfügbar.

In diesem Artikel werden einige Einschränkungen und Betriebssystemkonzepte für Windows Server-Knoten in AKS erläutert. Knotenpools für Windows Server befinden sich derzeit in der Vorschauphase.

> [!IMPORTANT]
> AKS-Previewfunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Sie werden zum Sammeln von Feedback und Fehlern mithilfe unserer Community bereitgestellt. In der Vorschauversion sind diese Features nicht für den Einsatz in der Produktion vorgesehen. Features in der öffentlichen Vorschau unterliegen dem Prinzip des „bestmöglichen Supports“. Unterstützung durch die Teams für den technischen AKS-Support steht nur während der Geschäftszeiten in der Zeitzone „Pacific Standard Time“ (PST) zur Verfügung. Weitere Informationen hierzu finden Sie in den folgenden Supportartikeln:
>
> * [Unterstützungsrichtlinien für Azure Kubernetes Service][aks-support-policies]
> * [Häufig gestellte Fragen zum Azure-Support][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Einschränkungen für Windows Server in Kubernetes

Windows Server-Container müssen auf einem Windows-basierten Containerhost ausgeführt werden. Sie können [einen Knotenpool erstellen, der Windows Server][windows-node-cli] als Gastbetriebssystem ausführt, um Windows Server-Container in AKS auszuführen. Die Unterstützung für Windows Server-Knotenpools umfasst einige Einschränkungen, die Teil des Upstreamprojekts „Windows Server in Kubernetes“ sind. Diese Einschränkungen sind nicht spezifisch für AKS. Weitere Informationen zu dieser Upstreamunterstützung für Windows Server in Kubernetes finden Sie unter [Windows Server containers in Kubernetes limitations][upstream-limitations] (Einschränkungen für Windows Server-Container in Kubernetes).

Die folgenden Upstreameinschränkungen für Windows Server-Container in Kubernetes sind für AKS relevant:

- Windows Server-Container können nur Windows Server 2019 verwenden, das mit dem zugrunde liegenden Windows Server-Betriebssystem für die Knoten übereinstimmt.
    - Containerimages, die mit Windows Server 2016 als Basisbetriebssystem erstellt wurden, werden nicht unterstützt.
- Privilegierte Container können nicht verwendet werden.
- Linux-spezifische Funktionen wie z. B. RunAsUser, SELinux, AppArmor oder POSIX-Funktionen stehen in Windows Server-Containern nicht zur Verfügung.
    - Linux-spezifische Dateisystemeinschränkungen, z. B. UUI/GUID oder Berechtigung auf Benutzerbasis sind in Windows Server-Containern ebenfalls nicht verfügbar.
- Azure-Datenträger und Azure Files sind die unterstützten Volumetypen, auf die als NTFS-Volumes im Windows Server-Container zugegriffen wird.
    - NFS-basierter Speicher und NFS-basierte Volumes werden nicht unterstützt.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AKS-Einschränkungen für Windows Server-Knotenpools

Die folgenden zusätzlichen Einschränkungen gelten für die Unterstützung von Windows Server-Knotenpools in AKS:

- Ein AKS-Cluster enthält immer einen Linux-Knotenpool als ersten Knotenpool. Dieser erste Linux-basierte Knotenpool kann nur gelöscht werden, wenn der AKS-Cluster selbst gelöscht wird.
- AKS unterstützt derzeit nur das grundlegenden Lastenausgleichsmodul, in dem nur ein Back-End-Pool verwendet werden kann: der standardmäßige Linux-Knotenpool. Daher wird Datenverkehr aus Windows-Pods immer [in eine von Azure verwaltete öffentliche IP-Adresse übersetzt][azure-outbound-traffic]. Da diese IP-Adresse nicht konfiguriert werden kann, ist es derzeit nicht möglich, eine Whitelist für Datenverkehr aus Windows-Pods einzurichten. 
- AKS-Cluster müssen das (erweiterte) Azure CNI-Netzwerkmodell verwenden.
    - Kubenet Basic-Netzwerke werden nicht unterstützt. Sie können keinen AKS-Cluster erstellen, der Kubenet verwendet. Weitere Informationen zu den Unterschieden der Netzwerkmodelle finden Sie unter [Netzwerkkonzepte für Anwendungen in AKS][azure-network-models].
    - Das Azure CNI-Netzwerkmodell erfordert zusätzliche Planung und Überlegungen für die Verwaltung von IP-Adressen. Weitere Informationen zum Planen und Implementieren von Azure CNI finden Sie unter [Konfigurieren von Azure CNI-Netzwerken in AKS][configure-azure-cni].
- Windows Server-Knoten in AKS müssen auf die aktuelle Windows Server 2019-Version mit den neuesten Patches und Updates *aktualisiert* werden. Windows Update ist im Basisknotenimage in AKS nicht aktiviert. Sie sollten in regelmäßigen Abständen ein Upgrade für die Windows Server-Knotenpools in Ihrem AKS-Cluster durchführen, passend zum Windows Update-Freigabezyklus und Ihrem eigenen Validierungsprozess. Weitere Informationen zum Upgrade eines Windows Server-Knotenpools finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].
    - Diese Upgrades für Windows Server-Knoten nutzen vorübergehend zusätzliche IP-Adressen im Subnetz des virtuellen Netzwerks, da neue Knoten bereitgestellt werden, bevor die alten Knoten entfernt werden.
    - Auch vCPU-Kontingente werden im Abonnement vorübergehend genutzt, da zuerst neue Knoten bereitgestellt werden, bevor die alten Knoten entfernt werden.
    - Sie können Neustarts nicht mit `kured` automatisch aktualisieren und verwalten, wie dies bei Linux-Knoten in AKS möglich ist.
- Der AKS-Cluster kann maximal acht Knotenpools umfassen.
    - In diesen acht Knotenpools können maximal 400 Knoten enthalten sein.
- Der Name des Windows Server-Knotenpools ist auf 6 Zeichen begrenzt.
- Previewfunktionen in AKS, z. B. Netzwerkrichtlinie und Autoskalierung für Cluster, werden bei Windows Server-Knoten nicht unterstützt.
- Eingangscontroller sollten nur auf Linux-Knoten mit einem NodeSelector geplant werden.
- Azure Dev Spaces ist derzeit nur für Linux-basierte Knotenpools verfügbar.
- Von Gruppen verwaltete Dienstkonten (gMSA) werden derzeit in AKS nicht unterstützt, wenn die Windows Server-Knoten zu keiner Active Directory-Domäne gehören.
    - Das Open-Source-Upstreamprojekt [aks-engine][aks-engine] bietet derzeit keine Unterstützung für gMSA, wenn Sie dieses Feature verwenden müssen.

## <a name="os-concepts-that-are-different"></a>Unterschiedliche Betriebssystemkonzepte

Kubernetes ist von jeher auf Linux ausgerichtet. Viele auf der Upstreamwebsite [Kubernetes.io][kubernetes] verwendete Beispiele sind zur Verwendung auf Linux-Knoten vorgesehen. Wenn Sie Bereitstellungen erstellen, bei denen Windows Server-Container verwendet werden, müssen Sie folgende Punkte auf Betriebssystemebene beachten:

- **Identität:** Unter Linux werden userID (UID) und groupID (GID) verwendet (dargestellt als Integer-Typen). Benutzer-und Gruppennamen werden nicht kanonisch verwendet, sie sind lediglich Aliase in */etc/groups* oder */etc/passwd* für die UID und die GID.
    - Unter Windows Server wird eine umfangreichere binäre Sicherheits-ID (SID) verwendet, die in der Windows Security Access Manager-Datenbank (SAM) gespeichert wird. Diese Datenbank wird weder zwischen dem Host und den Containern noch zwischen den einzelnen Containern freigegeben.
- **Dateiberechtigungen:** Unter Windows-Server wird eine Zugriffssteuerungsliste basierend auf SIDs anstelle einer Bitmaske von Berechtigungen sowie UID und GID verwendet.
- **Dateipfade:** Als Konvention wird unter Windows Server „\“ anstelle von „/“ verwendet.
    - Geben Sie in Podspezifikationen, in denen Volumes eingebunden werden, den Pfad für Windows Server-Container korrekt ein. Geben Sie beispielsweise anstelle des Bereitstellungspunkts */mnt/volume* in einem Linux-Container einen Laufwerkbuchstaben und Speicherort ein, z. B. */K/Volume* zum Einbinden als Laufwerk *K:* .

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie zum Einstieg in Windows Server-Container in AKS [einen Knotenpool, auf dem Windows Server in AKS ausgeführt wird][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
