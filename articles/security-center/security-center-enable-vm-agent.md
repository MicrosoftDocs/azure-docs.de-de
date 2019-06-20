---
title: Aktivieren des VM-Agents in Azure Security Center | Microsoft Docs
description: In diesem Dokument wird erläutert, wie Sie die Azure Security Center-Empfehlung **VM-Agent aktivieren** umsetzen.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60916277"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Aktivieren des VM-Agents in Azure Security Center
Der VM-Agent muss auf virtuellen Computern installiert sein, um [die Datensammlung zu aktivieren](security-center-enable-data-collection.md).  Im Azure Security Center können Sie prüfen, welche VMs den VM-Agent benötigen. Es wird eine Empfehlung angezeigt, den VM-Agent auf diesen VMs zu aktivieren.

Der VM-Agent wird standardmäßig für virtuelle Maschinen installiert, die vom Azure Marketplace bereitgestellt werden. Der Artikel [VM-Agent und Erweiterungen – Teil 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) enthält Informationen zum Installieren des VM-Agents.

> [!NOTE]
> Der Dienst wird anhand einer Beispielbereitstellung vorgestellt. Es ist keine schrittweise Anleitung.
>
>

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **VM-Agent aktivieren**.
   ![VM-Agent aktivieren][1]
2. Daraufhin wird das Blatt **VM-Agent fehlt oder reagiert nicht**geöffnet. Auf diesem Blatt sind die VMs aufgeführt, die den VM-Agent erfordern. Befolgen Sie die Anweisungen auf dem Blatt, um den VM-Agent zu installieren.
   ![VM-Agent fehlt][2]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md)Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md)Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md)Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md)Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center:](security-center-partner-solutions.md) Erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md)Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/)(Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
