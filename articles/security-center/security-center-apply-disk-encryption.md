---
title: Anwenden der Datenträgerverschlüsselung in Azure Security Center | Microsoft Docs
description: In diesem Dokument wird gezeigt, wie Sie die Azure Security Center-Empfehlung **Datenträgerverschlüsselung anwenden** implementieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d285461aef1e56dfd08a162e51ada0340d6eeae4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095420"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Anwenden der Datenträgerverschlüsselung in Azure Security Center
Azure Security Center empfiehlt die Anwendung der Datenträgerverschlüsselung, wenn Sie über Windows- oder Linux-VM-Datenträger verfügen, die nicht per Azure Disk Encryption verschlüsselt sind. Mit der Datenträgerverschlüsselung können Sie Ihre Windows- und Linux-IaaS-VM-Datenträger verschlüsseln.  Die Verschlüsselung wird sowohl für die Betriebssystem- als auch für die Datenvolumes auf Ihrer VM empfohlen.

Azure Disk Encryption verwendet das Branchenstandardfeature [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) von Windows und das Feature [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) von Linux. Diese Features sorgen für die Verschlüsselung von Betriebssystem und Daten, um den Schutz Ihrer Daten zu verbessern und die Sicherheits- und Complianceverpflichtungen Ihrer Organisation zu erfüllen. Die Datenträgerverschlüsselung ist in [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) integriert, damit Sie die Schlüssel für die Datenträgerverschlüsselung und die Geheimnisse unter Ihrem Key Vault-Abonnement steuern und verwalten können. Gleichzeitig können Sie sicherstellen, dass alle Daten auf den Datenträgern von virtuellen Computern im Ruhezustand in Ihrem [Azure-Speicher](https://azure.microsoft.com/documentation/services/storage/) verschlüsselt sind.

> [!NOTE]
> Azure Disk Encryption wird für die folgenden Windows Server-Betriebssysteme unterstützt: Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Die Datenträgerverschlüsselung wird für die folgenden Linux-Betriebssysteme unterstützt: Ubuntu, CentOS, SUSE und SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Datenträgerverschlüsselung anwenden** aus.
2. Auf dem Blatt **Datenträgerverschlüsselung anwenden** wird eine Liste mit den virtuellen Computern angezeigt, für die eine Datenträgerverschlüsselung empfohlen wird.
3. Führen Sie die Schritte der Anleitung aus, um die Verschlüsselung auf diese VMs anzuwenden.

![][1]

Zum Verschlüsseln virtueller Azure-Computer, für die von Security Center festgestellt wurde, dass sie verschlüsselt werden müssen, empfehlen wir die folgenden Schritte:

* Installieren und konfigurieren Sie Azure PowerShell. So können Sie die PowerShell-Befehle ausführen, die zum Einrichten der für die Verschlüsselung virtueller Azure-Computer erforderlichen Voraussetzungen notwendig sind.
* Rufen Sie das für Azure Disk Encryption erforderliche PowerShell-Skript ab, und führen Sie es aus.
* Verschlüsseln Sie Ihre virtuellen Computer.

Eine entsprechende Anleitung finden Sie unter [Verschlüsseln eines virtuellen Windows-IaaS-Computers mit Azure PowerShell](../security/quick-encrypt-vm-powershell.md). In diesem Thema wird davon ausgegangen, dass Sie einen Windows-Clientcomputer verwenden, von dem aus Sie die Datenträgerverschlüsselung konfigurieren.

Es gibt verschiedene Methoden, die für virtuelle Azure-Computer verwendet werden können. Wenn Sie bereits über Kenntnisse in Azure PowerShell oder Azure-CLI verfügen, ziehen Sie möglicherweise andere Methoden vor. Informationen zu diesen anderen Methoden erhalten Sie unter [Azure-Datenträgerverschlüsselung](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Weitere Informationen
In diesem Dokument wurde gezeigt, wie Sie die Security Center-Empfehlung „Datenträgerverschlüsselung anwenden“ implementieren. Weitere Informationen zur Datenträgerverschlüsselung finden Sie unter folgenden Themen:

* [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (Verschlüsselung und Schlüsselverwaltung mit dem Azure-Schlüsseltresor, Video, 36 Min. 39 Sek.): Erfahren Sie, wie Sie die Verwaltung der Datenträgerverschlüsselung für IaaS-VMs und Azure Key Vault nutzen, um Ihre Daten besser zu schützen.
* [Azure-Datenträgerverschlüsselung](../security/azure-security-disk-encryption-overview.md) (Dokument): Enthält Informationen zum Aktivieren der Datenträgerverschlüsselung für Windows- und Linux-VMs.

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md) : Hier erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) : Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
