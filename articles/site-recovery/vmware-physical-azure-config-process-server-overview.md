---
title: Informationen zur Azure Site Recovery-Konfiguration, zu Prozessen und Masterzielservern | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Konfiguration, Prozesse und Masterzielserver, die beim Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit Azure Site Recovery verwendet werden.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417741"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informationen zu Site Recovery-Komponenten (Konfiguration, Prozess und Masterziel)

Dieser Artikel beschreibt die Konfiguration, Prozesse und Masterzielserver, die beim Replizieren von virtuellen VMware-Computern und physischen Servern in Azure mit dem [Site Recovery](site-recovery-overview.md)-Dienst verwendet werden.

## <a name="configuration-server"></a>Konfigurationsserver

Für die Notfallwiederherstellung von lokalen virtuellen VMware-Computern und physischen Servern benötigen Sie einen lokal bereitgestellten Site Recovery-Konfigurationsserver.

**Einstellung** | **Details** | **Links**
--- | --- | ---
**Komponenten**  | Auf dem Konfigurationsservercomputer werden alle lokalen Site Recovery-Komponenten ausgeführt, einschließlich Konfigurationsserver, Prozessserver und Masterzielserver.<br/><br/> Wenn Sie den Konfigurationsserver einrichten, werden alle Komponenten automatisch installiert. | [Häufig gestellte Fragen](vmware-azure-common-questions.md#configuration-server) zum Konfigurationsserver
**Rolle** | Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation. | Weitere Informationen zur Architektur für die Notfallwiederherstellung von [VMware](vmware-azure-architecture.md) und [physischen Servern](physical-azure-architecture.md) in Azure
**VMware-Anforderungen** | Für die Notfallwiederherstellung von lokalen virtuellen VMware-Computern müssen Sie den Konfigurationsserver als lokalen und hochverfügbaren virtuellen VMware-Computer installieren und ausführen. | [Weitere Informationen](vmware-azure-deploy-configuration-server.md#prerequisites) zu den Voraussetzungen
**VMware-Bereitstellung** | Es wird empfohlen, den Konfigurationsserver mit einer OVA-Vorlage bereitzustellen. Diese Methode bietet eine einfache Möglichkeit, den Konfigurationsserver so einzurichten, dass er alle Anforderungen und Voraussetzungen erfüllt.<br/><br/> Falls Sie aus irgendeinem Grund keinen virtuellen VMware-Computer mit einer OVA-Vorlage bereitstellen können, können Sie die Konfigurationsservercomputer manuell einrichten. Dies wird nachfolgend für die Notfallwiederherstellung von physischen Computern beschrieben. | [Bereitstellen](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) mit einer OVA-Vorlage
**Anforderungen physischer Server** | Für die Notfallwiederherstellung auf lokalen physischen Servern stellen Sie den Konfigurationsserver manuell bereit. | [Weitere Informationen](physical-azure-set-up-source.md#prerequisites) zu den Voraussetzungen
**Physische Serverbereitstellung** | Wenn das Installieren des Konfigurationsservers als virtueller VMware-Computer nicht möglich ist, können Sie ihn auf einem physischen Server installieren. | [Manuelles Bereitstellen](physical-azure-set-up-source.md#set-up-the-source-environment) des Konfigurationsservers


## <a name="process-server"></a>Prozessserver

**Einstellung** | **Details** | **Links**
--- | --- | ---
**Bereitstellung**  | Für die Notfallwiederherstellung und Replikation von lokalen virtuellen VMware-Computern und physischen Servern benötigen Sie einen lokalen Prozessserver. Der Prozessserver wird bei der Bereitstellung standardmäßig auf dem Konfigurationsserver installiert. | [Weitere Informationen](vmware-azure-architecture.md?#architectural-components)
**Rolle (lokal)** | – Empfängt Replikationsdaten von für die Replikation aktivierten Computern<br/> – Optimiert Replikationsdaten durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage.<br/> – Führt eine Pushinstallation von Site Recovery Mobility Service auf lokalen virtuellen VMware-Computern und physischen Servern aus.<br/> – Führt die automatische Ermittlung von lokalen Computern durch. | [Weitere Informationen](vmware-physical-azure-config-process-server-overview.md#process-server) 
**Rolle (Failback von Azure)** | Nach dem Failover Ihrer lokalen Website richten Sie in Azure einen Prozessserver als virtuellen Azure-Computer ein, um ein Failback zu Ihrem lokalen Speicherort zu ermöglichen.<br/><br/> Der Prozessserver in Azure ist temporär. Der virtuelle Azure-Computer kann nach Abschluss des Failbacks gelöscht werden. | [Weitere Informationen](vmware-azure-set-up-process-server-azure.md)
**Skalieren** | Für größere Bereitstellungen können Sie lokal zusätzliche horizontal skalierbare Prozessserver einrichten. Zusätzliche Server skalieren die Kapazität horizontal, indem eine größere Anzahl von replizierenden Computern und größere Mengen von Replikationsdatenverkehr verarbeitet werden.<br/><br/> Sie können Computer zwischen zwei Prozessservern verschieben, um einen Lastenausgleich für Replikationsdatenverkehr vorzunehmen. | [Weitere Informationen](vmware-azure-set-up-process-server-scale.md)


## <a name="master-target-server"></a>Masterzielserver

Der Masterzielserver verarbeitet die Replikationsdaten während des Failbacks von Azure.

- Er wird standardmäßig auf dem Konfigurationsserver installiert.
- Bei größeren Bereitstellungen können Sie einen zusätzlichen, separaten Masterzielserver für das Failback hinzufügen.


## <a name="next-steps"></a>Nächste Schritte
- Überprüfen Sie die [Architektur](vmware-azure-architecture.md) für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern.
- Sehen Sie sich die [Anforderungen und Voraussetzungen](vmware-physical-azure-support-matrix.md) für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure an. 
