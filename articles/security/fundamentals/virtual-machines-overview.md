---
title: Mit Azure-VMs verwendete Sicherheitsfeatures
titleSuffix: Azure security
description: Dieser Artikel bietet eine Übersicht über die wichtigsten Sicherheitsfunktionen von Azure, die mit Azure Virtual Machines verwendet werden können.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: ed2cb967b24ce5abdebadfe29847ae1a16b4e745
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498743"
---
# <a name="azure-virtual-machines-security-overview"></a>Virtuelle Azure-Computer – Sicherheitsübersicht
Dieser Artikel bietet eine Übersicht über die wichtigsten Sicherheitsfunktionen von Azure, die mit virtuellen Computern verwendet werden können.

Mit Azure Virtual Machines können Sie sehr flexibel eine Vielzahl unterschiedlicher Computinglösungen bereitstellen. Der Dienst unterstützt Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP und Azure BizTalk Services. Daher können Sie jede Workload und jede Sprache für nahezu alle Betriebssysteme bereitstellen.

Ein virtueller Azure-Computer bietet Ihnen die Flexibilität der Virtualisierung, ohne Zeit und Geld für den Kauf und das Verwalten der Hardware aufwenden zu müssen, die den virtuellen Computer hostet. Sie können Ihre Anwendungen in der Gewissheit erstellen und bereitstellen, dass Ihre Daten in hochsicheren Datencentern geschützt und abgesichert sind.

Mit Azure können Sie richtlinienkonforme Lösungen mit erweiterter Sicherheit erstellen, die:

* Ihre virtuellen Computer vor Viren und Schadsoftware schützen.
* Ihre sensiblen Daten verschlüsseln.
* Netzwerkdatenverkehr absichern.
* Bedrohungen erkennen.
* Konformitätsvorgaben einhalten.  

## <a name="antimalware"></a>Antimalware

Mit Azure können Sie Antischadsoftware von Sicherheitsanbietern wie Microsoft, Symantec, Trend Micro und Kaspersky verwenden. Diese Software dient dem Schutz Ihrer virtuellen Computer vor Dateien mit schädlichem Inhalt, Adware und anderen Bedrohungen.

Microsoft Antimalware for Azure Cloud Services and Virtual Machines ist eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware.  Microsoft Antimalware für Azure gibt konfigurierbare Warnungen aus, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen.

Microsoft Antimalware für Azure ist eine Lösung mit einem einzelnen Agent für Anwendungen und Mandantenumgebungen, die im Hintergrund ohne Eingreifen des Benutzers ausgeführt wird. Sie können Schutz basierend auf den Anforderungen der Anwendungsworkloads bereitstellen, entweder mit der einfachen Konfiguration, die Schutz durch die Standardeinstellungen bietet, oder mit einer erweiterten benutzerdefinierten Konfiguration, einschließlich Antischadsoftwareüberwachung.

Erfahren Sie mehr über [Microsoft Antimalware für Azure](antimalware.md) und die verfügbaren Kernfunktionen.

Weitere Informationen zu Antischadsoftware zum Schutz Ihrer virtuellen Computer:

* [Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern, in englischer Sprache)](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Windows-Computer](../../virtual-machines/extensions/trend.md)
* [Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Windows-Computer](../../virtual-machines/extensions/symantec.md)
* [Willkommen im Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Für einen noch leistungsfähigeren Schutz verwenden Sie [Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Windows Defender ATP bietet:

* [Reduzierung der Angriffsfläche](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Schutz der nächsten Generation](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection und Antwort](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatisierte Untersuchung und Wartung](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Sicherheitsbewertung](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Erweiterte Erkennung](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Verwaltung und APIs](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Weitere Informationen:

* [Erste Schritte mit Windows Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Übersicht über Windows Defender ATP-Funktionen](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

Verschlüsselungs- und Authentifizierungsschutzmaßnahmen können durch eine höhere Sicherheit verbessert werden. Sie können die Verwaltung und Sicherheit Ihrer geschäftskritischen geheimen Daten und Schlüssel vereinfachen, indem Sie diese in Azure Key Vault speichern.

Mit Key Vault können Sie Ihre Schlüssel in Hardwaresicherheitsmodulen (Hardware Security Modules, HSMs) speichern, die gemäß FIPS 140-2 Level 2-Standards zertifiziert sind. Sie können Ihre SQL Server-Verschlüsselungsschlüssel für Sicherungen oder [transparente Datenverschlüsselung](/sql/relational-databases/security/encryption/transparent-data-encryption) gemeinsam mit den Schlüsseln oder geheimen Daten Ihrer Anwendungen in Key Vault speichern. Der Zugriff auf diese geschützten Elemente sowie die zugehörigen Berechtigungen werden über [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)verwaltet.

Weitere Informationen:

* [Was ist der Azure-Schlüsseltresor?](../../key-vault/general/overview.md)
* [Azure Key Vault-Blog](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Verschlüsselung der Datenträger virtueller Computer

Azure Disk Encryption ist eine neue Funktion zur Verschlüsselung der Datenträger virtueller Windows- und Linux-Computer. Azure Disk Encryption verwendet das Branchenstandardfeature [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) von Windows und das Feature [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) von Linux, um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen.

Die Lösung ist in Azure Key Vault integriert, damit Sie die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement steuern und verwalten können. Sie stellt außerdem sicher, dass alle ruhenden Daten auf den Datenträgern der virtuellen Computer in Azure Storage verschlüsselt sind.

Weitere Informationen:

* [Azure Disk Encryption für virtuelle IaaS-Computer](./azure-disk-encryption-vms-vmss.md)
* [Schnellstart: Verschlüsseln eines virtuellen Windows-IaaS-Computers mit Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Sicherung virtueller Computer

Azure Backup ist eine skalierbare Lösung, die Ihre Anwendungsdaten schützt – ganz ohne Investitionskosten und mit sehr niedrigen Betriebskosten. Anwendungsfehler können Ihre Daten beschädigen, und menschliche Fehler können Bugs in Ihren Anwendungen verursachen. Mit Azure Backup sind Ihre virtuellen Windows- und Linux-Computer geschützt.

Weitere Informationen:

* [Was ist Azure Backup?](../../backup/backup-overview.md)
* [Azure Backup-Dienst – Häufig gestellte Fragen](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Ein wichtiger Teil der BCDR-Strategie Ihrer Organisation ist die Ermittlung, wie geschäftliche Workloads und Apps verfügbar gehalten werden können, wenn geplante und ungeplante Ausfälle auftreten. Azure Site Recovery unterstützt die Orchestrierung von Replikation, Failover und Wiederherstellung von Workloads und Apps, damit diese Komponenten über einen sekundären Standort zur Verfügung stehen, wenn der primäre Standort ausfällt.

Site Recovery:

* **Vereinfachung Ihrer BCDR-Strategie**: Site Recovery vereinfacht die Replikation, das Failover und die Wiederherstellung mehrerer Geschäftsworkloads und -Apps von einem einzelnen Standort. Site Recovery orchestriert die Replikation und das Failover, aber das Tool fängt keine Anwendungsdaten ab und besitzt auch keinerlei Informationen hierzu.
* **Ermöglicht flexible Replikation**: Mithilfe von Site Recovery können Sie Workloads auf virtuellen Hyper-V-Computern, virtuellen VMware-Computern und physischen Windows-/Linux-Servern replizieren.
* **Einfaches Failover und leichte Wiederherstellung werden unterstützt**: Site Recovery verfügt über Testfailover, mit denen Übungen zur Notfallwiederherstellung unterstützt werden, ohne dass sich dies auf Produktionsumgebungen auswirkt. Außerdem können Sie geplante Failover ohne Datenverlust für erwartete Ausfälle oder ungeplante Failover mit minimalem Datenverlust (je nach Replikationsfrequenz) für unerwartete Notfälle ausführen. Nach dem Failover können Sie ein Failback zu Ihren primären Standorten durchführen. Site Recovery verfügt über Wiederherstellungspläne, die Skripts und Azure Automation-Arbeitsmappen enthalten können, sodass Sie das Failover und die Wiederherstellung von Anwendungen mit mehreren Ebenen anpassen können.
* **Beseitigung sekundärer Datencenter**: Sie können Daten an einem sekundären lokalen Standort oder in Azure replizieren. Wenn Sie Azure als Ziel für die Notfallwiederherstellung nutzen, vermeiden Sie die Kosten und Komplexität, die mit der Verwaltung eines sekundären Standorts verbunden sind. Replizierte Daten werden in Azure Storage gespeichert.
* **Integration in vorhandene BCDR-Technologien**: Site Recovery wird zusammen mit den BCDR-Features anderer Anwendungen eingesetzt. Beispielsweise können Sie Site Recovery verwenden, um das SQL Server-Back-End von geschäftlichen Workloads zu schützen. Dies umfasst auch die native Unterstützung für SQL Server Always On zum Verwalten des Failovers von Verfügbarkeitsgruppen.

Weitere Informationen:

* [Was ist Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Wie funktioniert Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Welche Workloads werden durch Azure Site Recovery geschützt?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuelle Netzwerke

Virtuelle Computer benötigen Netzwerkkonnektivität. Azure erfordert von einem virtuellen Computer eine Verbindung mit einem virtuellen Azure-Netzwerk, damit die Konnektivitätsanforderung unterstützt wird.

Ein virtuelles Azure-Netzwerk ist ein logisches Konstrukt, das auf dem physischen Azure-Netzwerkfabric basiert. Jedes logische virtuelle Azure-Netzwerk ist von allen anderen virtuellen Azure-Netzwerken isoliert. Mit dieser Isolierung wird sichergestellt, dass der Netzwerkverkehr in Ihren Bereitstellungen nicht für andere Microsoft Azure-Kunden zugänglich ist.

Weitere Informationen:

* [Die Netzwerksicherheit in Azure in der Übersicht](network-overview.md)
* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)
* [Netzwerkfeatures und Partnerschaften für Enterpriseszenarien](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Verwaltung von Sicherheitsrichtlinien und Berichtserstellung

Mit Azure Security Center können Sie Bedrohungen verhindern, erkennen und beheben. Security Center sorgt für eine größere Transparenz und bessere Kontrolle der Sicherheit Ihrer Azure-Ressourcen. Es bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements. Es hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum von Sicherheitslösungen verwendet werden.

Mit Security Center können Sie die Sicherheit Ihrer virtuellen Computer durch folgende Aktionen optimieren und überwachen:

* Bereitstellen von [Sicherheitsempfehlungen](../../security-center/security-center-recommendations.md) für die virtuellen Computer. Empfehlungen beziehen sich beispielsweise auf das Anwenden von Systemupdates, das Konfigurieren von ACL-Endpunkten, das Aktivieren von Antischadsoftware, das Aktivieren von Netzwerksicherheitsgruppen und das Anwenden der Datenträgerverschlüsselung.
* Überwachen des Status Ihrer virtuellen Computer.

Weitere Informationen:

* [Einführung in Azure Security Center](../../security-center/security-center-introduction.md)
* [Azure Security Center – Häufig gestellte Fragen](../../security-center/faq-general.md)
* [Planungs- und Betriebshandbuch für Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Compliance

Azure Virtual Machines ist für FISMA, FedRAMP, HIPAA, PCI DSS Level 1 und andere Complianceprogramme zertifiziert. So können Sie auch mit Ihren eigenen Azure-Anwendungen die verschiedenen Complianceanforderungen besser erfüllen, und Ihr Unternehmen kann sicherstellen, dass diverse nationale und internationale rechtliche Vorgaben eingehalten werden.

Weitere Informationen:

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/en-us/trustcenter/compliance).
* [Die vertrauenswürdige Cloud: Sicherheit, Datenschutz und Compliance von Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential Computing

Confidential Computing gehört technisch betrachtet zwar nicht zum Thema „Sicherheit virtueller Computer“, doch die Sicherheit von VMs ist Teil des übergeordneten Themas Computesicherheit. Confidential Computing fällt in die Kategorie der Computesicherheit.

Confidential Computing stellt sicher, dass Daten im unverschlüsselten Zustand, der zur effizienten Verarbeitung erforderlich ist, in einer Trusted Execution Environment (TEE, auch Enclave) geschützt werden (s. https://en.wikipedia.org/wiki/Trusted_execution_environment ). Ein Beispiel finden Sie in der Abbildung unten.  

Wenn Sie TEEs verwenden, können externe Benutzer interne Daten und Vorgänge auch nicht mit einem Debugger anzeigen. TEEs gewährleisten sogar, dass nur autorisierter Code für den Zugriff auf Daten zugelassen wird. Wenn der Code geändert oder manipuliert wird, werden die Vorgänge verweigert, und die Umgebung wird deaktiviert. Die TEE erzwingt diese Schutzmaßnahmen, während der Code in der Umgebung ausgeführt wird.

Weitere Informationen:

* [Einführung in Confidential Computing in Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Confidential Computing in Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Nächste Schritte

Informationen zu [bewährten Sicherheitsmethoden](iaas.md) für VMs und Betriebssysteme.