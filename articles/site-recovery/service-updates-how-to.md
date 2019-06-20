---
title: Azure Site Recovery-Updates | Microsoft-Dokumentation
description: Bietet eine Übersicht über Dienstupdates und Upgrades von in Azure Site Recovery verwendeten Komponenten.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/25/2019
ms.author: rajanaki
ms.openlocfilehash: dc15c8935cd3c20037bbed01f0f24cd2b8642045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540926"
---
# <a name="service-updates-in-azure-site-recovery"></a>Dienstupdates in Azure Site Recovery
Als Organisation müssen Sie ermitteln, wie Sie Ihre Daten schützen und die Ausführung von Apps/Workloads sicherstellen können, wenn es zu geplanten und ungeplanten Ausfällen kommt. Azure Site Recovery trägt zu Geschäftskontinuität und Notfallwiederherstellung bei, indem Ihre Apps auf VMs weiter ausgeführt werden und physische Server verfügbar bleiben, wenn ein Standort ausfällt. Site Recovery repliziert Workloads, die auf VMs und physischen Servern ausgeführt werden, damit sie an einem sekundären Ort verfügbar bleiben, wenn die Verfügbarkeit des primären Standorts nicht mehr gegeben ist. Workloads werden am primären Standort wiederhergestellt, wenn er wieder betriebsbereit ist.

Site Recovery kann die Replikation für folgende Bereiche verwalten:

- [Replikation von virtuellen Azure-Computern zwischen Azure-Regionen.](azure-to-azure-tutorial-dr-drill.md)
- Replikation von lokalen virtuellen Computern und physischen Servern in Azure oder an einem sekundären Standort
Weitere Informationen hierzu finden Sie in der Dokumentation unter [diesem Link](https://docs.microsoft.com/azure/site-recovery).

Azure Site Recovery veröffentlicht regelmäßig Dienstupdates – einschließlich neuer Features, Verbesserungen an der Unterstützungsmatrix und ggf. Fehlerbehebungen. Benutzern wird empfohlen, immer ein Update auf die neuesten Versionen der Azure Site Recovery-Komponenten auszuführen, um alle neuen Features, Verbesserungen und ggf. Fehlerbehebungen nutzen zu können. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Supporthinweis für Azure Site Recovery 

> [!IMPORTANT]
> **Mit jeder neuen veröffentlichten Version N einer Azure Site Recovery-Komponente werden alle Versionen, die älter als N-4 sind, nicht mehr unterstützt.** Es ist daher immer ratsam, auf die neueste verfügbare Version zu aktualisieren.

> [!IMPORTANT]
> Der offizielle Support für Upgrades gilt für die Versionen N-4 bis N (wobei N die neueste Version ist). Wenn Sie N-6 verwenden, müssen Sie zunächst ein Upgrade auf N-4 ausführen und dann ein Upgrade auf N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Upgrade bei einem größeren Abstand als 4 zwischen der aktuellen Version und der neuesten veröffentlichten Version

1. Aktualisieren Sie zuerst die derzeit installierte Komponente von Version M auf M+4 und dann auf die nächste kompatible Version. Angenommen, die aktuelle Version ist 9.24, und Sie verwenden 9.16, dann führen Sie zunächst ein Upgrade auf 9.20 und dann auf 9.24 aus.
2. Gehen Sie für alle Komponenten je nach Szenario genauso vor.

### <a name="support-for-latest-oskernel-versions"></a>Unterstützung für die neuesten Betriebssystem-/Kernelversionen

> [!NOTE]
> Wenn Sie ein Wartungsfenster geplant haben und dabei ein Neustart durchgeführt werden soll, wird empfohlen, zunächst ein Upgrade der Site Recovery-Komponenten durchzuführen und dann mit dem Rest der geplanten Aktivitäten fortzufahren.

1. Bevor Sie Ihre Kernel-/Betriebssystemversionen aktualisieren, überprüfen Sie, ob die Zielversion von Azure Site Recovery unterstützt wird. Sie finden diese Informationen in der Dokumentation für Azure-VMs, [VMware-VMs](vmware-physical-azure-support-matrix.md) und Hyper-V-VMs.
2. Beziehen Sie sich auf unsere [Dienstupdates](https://azure.microsoft.com/updates/?product=site-recovery), um herauszufinden, welche Version der Site Recovery-Komponenten die Version unterstützt, auf die Sie ein Upgrade ausführen möchten.
3. Aktualisieren Sie zuerst auf die neueste Version von Site Recovery.
4. Aktualisieren Sie nun das Betriebssystem bzw. den Kernel auf die gewünschte Version.
5. Führen Sie einen Neustart durch.
6. Dadurch wird sichergestellt, dass die Betriebssystem-/Kernelversion auf Ihren Computern auf die neueste Version aktualisiert wird und dass die neuesten Site Recovery-Änderungen, die zur Unterstützung der neuen Version erforderlich sind, ebenfalls auf dem Quellcomputer geladen werden.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Notfallwiederherstellung von virtuellen Azure-Computern in Azure
In diesem Szenario wird dringend empfohlen, automatische Updates zu [aktivieren](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate). Sie können das Verwalten von Updates durch Site Recovery wahlweise folgendermaßen ermöglichen:

- Als Teil des Schritts zum Aktivieren der Replikation
- Durch Ein-/Ausschalten der Erweiterungsupdateeinstellungen im Tresor

Für den Fall, dass Sie Updates manuell verwalten, gehen Sie folgendermaßen vor:

1. Wechseln Sie zum Azure-Portal, und navigieren Sie zu Ihrem Recovery Services-Tresor.
2. Wechseln Sie im Azure-Portal zum Bereich „Replizierte Elemente“ für den Recovery Services-Tresor.
3. Klicken Sie im oberen Bereich des Bildschirms auf die folgende Benachrichtigung:
    
    *Ein neues Site Recovery-Replikations-Agent-Update ist verfügbar.*
    
    *Klicken Sie, um es zu installieren.*

4. Wählen Sie die VMs aus, auf die Sie das Update anwenden möchten, und klicken Sie dann auf **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Zwischen zwei lokalen VMM-Standorten
1. Laden Sie den aktuellen Updaterollup für den Microsoft Azure Site Recovery-Anbieter herunter.
2. Installieren Sie den Updaterollup zuerst auf dem lokalen VMM-Server, der den Wiederherstellungsstandort verwaltet.
3. Nach dem Update des Wiederherstellungsstandorts installieren Sie den Updaterollup auf dem VMM-Server, der den primären Standort verwaltet.

> [!NOTE]
> Wenn der VMM als hoch verfügbarer VMM (Cluster-VMM) konfiguriert ist, stellen Sie sicher, dass Sie das Upgrade auf allen Knoten des Clusters installieren, auf denen der VMM-Dienst installiert ist.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Zwischen einem lokalen VMM-Standort und Azure
1. Laden Sie den Updaterollup für den Microsoft Azure Site Recovery-Anbieter herunter.
2. Installieren Sie den Updaterollup auf dem lokalen VMM-Server.
3. Installieren Sie den neuesten MARS-Agent auf allen Hyper-V-Hosts.

> [!NOTE]
> Wenn Ihr VMM als hoch verfügbarer VMM (Cluster-VMM) konfiguriert ist, stellen Sie sicher, dass Sie das Upgrade auf allen Knoten des Clusters installieren, auf denen der VMM-Dienst installiert ist.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Zwischen einem lokalen Hyper-V-Standort und Azure

1. Laden Sie den Updaterollup für den Microsoft Azure Site Recovery-Anbieter herunter.
2. Installieren Sie den Anbieter auf jedem Knoten der Hyper-V-Server, die Sie in Azure Site Recovery registriert haben.

> [!NOTE]
> Wenn es sich bei Ihrem Hyper-V um einen Hyper-V-Server mit Hostclustering handelt, stellen Sie sicher, dass Sie das Upgrade auf allen Knoten des Clusters installieren.

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Zwischen einem lokalen VMware- oder physischen Standort und Azure

Bevor Sie mit Updates fortfahren, informieren Sie sich anhand des [Site Recovery-Supporthinweises](#support-statement-for-azure-site-recovery) über den Upgradepfad.

1. Abhängig von Ihrer aktuellen Version und dem vorangehenden Supporthinweis installieren Sie das Update zuerst auf Ihrem lokalen Managementserver. Befolgen Sie dazu die Anleitung in diesem [Artikel](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Es handelt sich um den Server, der die Rollen des Konfigurationsservers und des Prozessservers erfüllt.
2. Wenn Sie über Prozessserver für die horizontale Skalierung verfügen, aktualisieren Sie diese als Nächstes anhand der [hier](vmware-azure-manage-process-server.md#upgrade-a-process-server) erläuterten Richtlinien.
3. Um den Mobilitäts-Agent auf jedem geschützten Element zu aktualisieren, wechseln Sie als Nächstes zum Azure-Portal und rufen dann die Seite **Geschützte Elemente** > **Replizierte Elemente** auf. Wählen Sie auf dieser Seite einen virtuellen Computer aus. Wählen Sie die Schaltfläche **Update-Agent** aus, die im unteren Bereich der Seite für jeden virtuellen Computer angezeigt wird. Dadurch wird der Mobility Service-Agent auf allen geschützten VMs aktualisiert.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Neustart des Quellcomputers nach dem Upgrade des Mobilitäts-Agents

Nach jedem Upgrade des Mobilitäts-Agents wird ein Neustart empfohlen, um sicherzustellen, dass alle aktuellen Änderungen auf dem Quellcomputer geladen werden. Dies ist jedoch **nicht obligatorisch**. Wenn der Abstand zwischen der Agent-Version während des letzten Neustarts und der aktuellen Version größer als 4 ist, ist ein Neustart obligatorisch. Eine ausführliche Erläuterung finden Sie in der folgenden Tabelle.

|**Agent-Version während des letzten Neustarts** | **Upgrade auf** | **Neustart obligatorisch?**|
|---------|---------|---------|
|9.16 |  9.18 | Nicht obligatorisch|
|9.16 | 9.19 | Nicht obligatorisch|
| 9.16 | 9.20 | Nicht obligatorisch
 | 9.16 | 9.21 | Ja, führen Sie zunächst ein Upgrade auf 9.20 aus, und starten Sie dann neu, bevor Sie ein Upgrade auf 9.21 ausführen, da der Abstand zwischen den Versionen (9.16 beim letzten Neustart, Zielversion 9.21) > 4 ist.

## <a name="links-to-currently-supported-update-rollups"></a>Links zu den derzeit unterstützten Updaterollups

|Updaterollup  |Anbieter  |Einheitliches Setup| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Updaterollup 36](https://support.microsoft.com/en-in/help/4503156)     |   5.1.4150.0  |  9.24.5211.1   |  5.1.4150.0  | 2.0.9160.0
|[Updaterollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[Updaterollup 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) – Hotfix     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Updaterollup 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Updaterollup 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Updaterollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0

## <a name="previous-update-rollups"></a>Frühere Updaterollups

- [Updaterollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)
- [Updaterollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [Updaterollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [Updaterollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [Updaterollup 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Updaterollup 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Updaterollup 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Updaterollup 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Updaterollup 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Updaterollup 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Updaterollup 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
