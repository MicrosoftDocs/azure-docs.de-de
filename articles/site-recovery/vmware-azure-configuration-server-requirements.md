---
title: Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure Site Recovery
description: In diesem Artikel werden die Unterstützung und die Anforderungen für das Bereitstellen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure mit Azure Site Recovery beschrieben.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997806"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure

Sie stellen einen lokalen Konfigurationsserver bereit, wenn Sie [Azure Site Recovery](site-recovery-overview.md) für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure verwenden.

- Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen VMware-Umgebung und Azure. Darüber hinaus verwaltet er die Datenreplikation.
- [Erfahren Sie mehr](vmware-azure-architecture.md) zu den Komponenten und Prozessen von Konfigurationsservern.

## <a name="configuration-server-deployment"></a>Bereitstellung des Konfigurationsservers

Für die Notfallwiederherstellung von VMware-VMs in Azure stellen Sie den Konfigurationsserver als VMware-VM bereit.

- Site Recovery stellt eine OVA-Vorlage bereit, die Sie aus dem Azure-Portal herunterladen und in vCenter Server importieren, um die Konfigurationsserver-VM einzurichten.
- Wenn Sie den Konfigurationsserver mit der OVA-Vorlage bereitstellen, erfüllt die VM automatisch die Anforderungen, die in diesem Artikel aufgeführt sind.
- Es wird dringend empfohlen, den Konfigurationsserver mit der OVA-Vorlage einzurichten. Aber wenn Sie die Notfallwiederherstellung für VMware-VMs einrichten und die OVA-Vorlage nicht verwenden können, können Sie den Konfigurationsserver anhand [dieser Anleitung](physical-azure-set-up-source.md) bereitstellen.
- Falls Sie den Konfigurationsserver für die Notfallwiederherstellung von lokalen physischen Computern in Azure bereitstellen, hilft Ihnen die Anleitung in [diesem Artikel](physical-azure-set-up-source.md) weiter. 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Nächste Schritte
[Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)
