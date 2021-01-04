---
title: Bewährte Sicherheitsmethoden für IaaS-Workloads in Azure | Microsoft Docs
description: " Die Workloadmigration zu Azure IaaS ist eine gute Gelegenheit zur Neubewertung unserer Designs. "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 9b9a83cf71dfa7658c34c3c98f8d12a056adad0c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698783"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Bewährte Sicherheitsmethoden für IaaS-Workloads in Azure
Dieser Artikel beschreibt bewährte Best Practices für die Sicherheit von virtuellen Computern und Betriebssystemen.

Die bewährten Methoden basieren auf einer gemeinsamen Linie und eignen sich für aktuelle Funktionen und Features der Azure-Plattform. Da sich Meinungen und Technologien im Laufe der Zeit verändern können, wird dieser Artikel regelmäßig aktualisiert, um diesen Veränderungen Rechnung zu tragen.

In den meisten IaaS-Szenarien (Infrastructure-as-a-Service, Infrastruktur als Dienst) stellen [virtuelle Azure-Computer](../../virtual-machines/index.yml) (Virtual Machines, VMs) die Hauptworkload für Organisationen dar, die Cloud Computing verwenden. Dies gilt in [Hybridszenarien](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), in denen Organisationen Workloads nach und nach in die Cloud migrieren möchten. Orientieren Sie sich in solchen Szenarien an den [allgemeinen Sicherheitsaspekten für IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), und wenden Sie bewährte Sicherheitsmethoden für alle Ihre virtuellen Computer an.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Schützen von VMs mittels Authentifizierungs- und Zugriffssteuerung
Der erste Schritt zum Schutz Ihrer virtuellen Computer ist, sicherzustellen, dass nur autorisierte Benutzer neue VMs einrichten und auf VMs zugreifen können.

> [!NOTE]
> Um die Sicherheit von Linux-VMs in Azure zu verbessern, können Sie die Azure AD-Authentifizierung integrieren. Bei Verwendung der [Azure AD-Authentifizierung für virtuelle Linux-Computer](../../virtual-machines/linux/login-using-aad.md) werden Richtlinien, mit denen der Zugriff auf die virtuellen Computer zugelassen oder verweigert wird, zentral gesteuert und erzwungen.
>
>

**Bewährte Methode**: Steuern des VM-Zugriffs.   
**Detail**: Verwenden Sie [Azure-Richtlinien](../../governance/policy/overview.md), um Konventionen für Ressourcen in Ihrer Organisation einzurichten und benutzerdefinierte Richtlinien zu erstellen. Wenden Sie diese Richtlinien auf Ressourcen wie z.B. [Ressourcengruppen](../../azure-resource-manager/management/overview.md) an. Virtuelle Computer, die einer Ressourcengruppe angehören, erben deren Richtlinien.

Wenn Ihre Organisation über viele Abonnements verfügt, benötigen Sie möglicherweise eine Möglichkeit zur effizienten Verwaltung von Zugriff, Richtlinien und Konformität für diese Abonnements. [Azure-Verwaltungsgruppen](../../governance/management-groups/overview.md) stellen einen abonnementübergreifenden Bereich bereit. Sie organisieren Abonnements in Verwaltungsgruppen (Containern) und wenden Ihre Governancebedingungen auf diese Gruppen an. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Gruppe angewendeten Bedingungen. Verwaltungsgruppen ermöglichen Ihnen – unabhängig von den Arten Ihrer Abonnements – die unternehmenstaugliche Verwaltung in großem Umfang.

**Bewährte Methode**: Verringern Sie die Variabilität in Ihrer Installation und Bereitstellung von virtuellen Computern.   
**Detail**: Verwenden Sie [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md)-Vorlagen, um Ihre Bereitstellungsoptionen zu verdeutlichen und das Verstehen und Inventarisieren der virtuellen Computer in Ihrer Umgebung zu vereinfachen.

**Bewährte Methode**: Schützen des privilegierten Zugriffs.   
**Detail**: Verwenden Sie den [Ansatz der geringsten Rechte](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) und integrierte Azure-Rollen, um Benutzern den Zugriff auf virtuelle Computer und deren Einrichtung zu ermöglichen:

- [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind.
- [Mitwirkender von klassischen virtuellen Computern](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Kann virtuelle Computer verwalten, die mit dem klassischen Bereitstellungsmodell erstellt wurden, aber nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind.
- [Sicherheitsadministrator](../../role-based-access-control/built-in-roles.md#security-admin): Nur in Security Center: Kann Sicherheitsrichtlinien und -zustände anzeigen, Sicherheitsrichtlinien bearbeiten sowie Warnungen und Empfehlungen anzeigen und verwerfen.
- [DevTest Labs-Benutzer](../../role-based-access-control/built-in-roles.md#devtest-labs-user): Kann alles anzeigen sowie virtuelle Computer verbinden, starten, neu starten und herunterfahren.

Ihre Abonnementadministratoren und Coadministratoren können diese Einstellung ändern und so zu Administratoren aller virtuellen Computer in einem Abonnement werden. Achten Sie darauf, dass alle Ihre Abonnementadministratoren und -coadministratoren für die Anmeldung bei Ihren Computern vertrauenswürdig sind.

> [!NOTE]
> Sie sollten virtuelle Computer mit gleichem Lebenszyklus in der gleichen Ressourcengruppe zusammenzufassen. Ressourcengruppen ermöglichen die Bereitstellung und Überwachung Ihrer Ressourcen sowie die Zusammenfassung der Abrechnungskosten für Ihre Ressourcen.
>
>

Organisationen, die VM-Zugriff und -Einrichtung steuern, verbessern die gesamte VM-Sicherheit.

## <a name="use-multiple-vms-for-better-availability"></a>Verwenden mehrerer virtueller Computer für eine höhere Verfügbarkeit
Falls Ihr virtueller Computer kritische Anwendungen ausführt, die Hochverfügbarkeit erfordern, empfiehlt sich die Verwendung mehrerer virtueller Computer. Verwenden Sie für eine höhere Verfügbarkeit eine [Verfügbarkeitsgruppe](../../virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) oder [Verfügbarkeitszonen](../../availability-zones/az-overview.md).

Eine Verfügbarkeitsgruppe ist eine logische Gruppierung, mit der Sie in Azure sicherstellen können, dass die darin enthaltenen VM-Ressourcen voneinander isoliert sind, wenn sie in einem Azure-Rechenzentrum bereitgestellt werden. Azure stellt sicher, dass die VMs innerhalb einer Verfügbarkeitsgruppe auf mehrere physische Server, Computeracks, Speichereinheiten und Netzwerkswitches verteilt werden. Wenn ein Hardware- oder Softwarefehler in Azure auftritt, ist nur ein Teil Ihrer VMs beeinträchtigt, und die Anwendung ist insgesamt weiterhin für Ihre Kunden verfügbar. Verfügbarkeitsgruppen haben eine wichtige Funktion bei der Erstellung zuverlässiger Cloudlösungen.

## <a name="protect-against-malware"></a>Schutz vor Malware
Installieren Sie einen Schadsoftwareschutz, um Viren, Spyware und andere Schadsoftware zu erkennen und zu entfernen. Sie können [Microsoft Antimalware](antimalware.md) oder die Endpunktschutz-Lösung eines Microsoft-Partners ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) und [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)) installieren.

Microsoft-Antischadsoftware umfasst Features wie Echtzeitschutz, geplante Überprüfungen, Malwareproblembehandlung, Signaturupdates, Engine-Updates, Beispielberichte und Sammlungen von Ausschlussereignissen. Für Umgebungen, die getrennt von Ihrer Produktionsumgebung gehostet werden, können Sie eine Antischadsoftware-Erweiterung verwenden, um den Schutz Ihrer VMs und Clouddienste zu verbessern.

Sie können Microsoft Antimalware und Partnerlösungen zur Vereinfachung der Bereitstellung und für integrierte Erkennungen (Warnungen und Vorfälle) in [Azure Security Center](../../security-center/index.yml) integrieren.

**Bewährte Methode**: Installieren Sie eine Antischadsoftware-Lösung zum Schutz vor Malware.   
**Detail**: [Installieren Sie eine Microsoft-Partnerlösung oder Microsoft Antimalware](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-).

**Bewährte Methode**: Integrieren Sie Ihre Antischadsoftware-Lösung zum Überwachen des Status Ihres Schutzes in Security Center.   
**Detail**: [Verwalten Sie Endpunktschutz-Probleme mit Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Verwalten Ihrer Updates für virtuelle Computer
Azure-VMs sollen wie alle lokalen VMs vom Benutzer verwaltet werden. Azure führt bei ihnen keine Pushübertragungen von Windows-Updates durch. Sie müssen Ihre Updates für virtuelle Computer verwalten.

**Bewährte Methode**: Halten Sie Ihre virtuellen Computer auf dem neuesten Stand.   
**Detail**: Sie können die Lösung für die [Updateverwaltung](../../automation/update-management/overview.md) in Azure Automation für Betriebssystemupdates für Ihre Windows- und Linux-Computer verwalten, die in Azure, in lokalen Umgebungen oder bei anderen Cloudanbietern bereitgestellt werden. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren.

Verwenden Sie für Computer, die mit der Updateverwaltung verwaltet werden, die folgenden Konfigurationen, um Bewertungen und Updatebereitstellungen durchzuführen:

- Microsoft Monitoring Agent (MMA) für Windows oder Linux
- PowerShell Desired State Configuration (DSC) für Linux
- Automation Hybrid Runbook Worker
- Microsoft Update oder Windows Server Update Services (WSUS) für Windows-Computer

Wenn Sie Windows Update verwenden, lassen Sie die Einstellung für automatische Windows-Updates aktiviert.

**Bewährte Methode**: Stellen Sie bei der Bereitstellung sicher, dass Images, die Sie erstellt haben, die neueste Zusammenstellung der Windows-Updates enthalten.   
**Detail**: Suchen Sie bei jeder Bereitstellung zuerst alle Windows-Updates, und installieren Sie diese. Dies ist besonders wichtig, wenn Sie Images bereitstellen, die von Ihnen selbst oder aus Ihrer eigenen Bibliothek stammen. Obwohl Images aus dem Azure Marketplace standardmäßig automatisch aktualisiert werden, kann nach einem öffentlichen Release eine Verzögerung (bis zu ein paar Wochen) eintreten.

**Bewährte Methode**: Stellen Sie in regelmäßigen Abständen Ihre virtuellen Computer erneut bereit, um eine neue Version des Betriebssystems zu erzwingen.   
**Detail**: Definieren Sie Ihren virtuellen Computer mit einer [Azure Resource Manager-Vorlage](../../azure-resource-manager/templates/template-syntax.md), sodass Sie sie problemlos erneut bereitstellen können. Mithilfe einer Vorlage erhalten Sie bei Bedarf eine gepatchte und sichere VM.

**Bewährte Methode**: Wenden Sie Sicherheitsupdates zügig auf virtuelle Computer an.   
**Detail**: Aktivieren Sie Azure Security Center (Free-Tarif oder Standard-Tarif), um [fehlende Sicherheitsupdates zu erkennen und anzuwenden](../../security-center/asset-inventory.md).

**Bewährte Methode**: Installieren Sie die neuesten Sicherheitsupdates.   
**Detail**: Zu den Workloads, die von Kunden als erste in Azure verschoben werden, zählen unter anderem Labs und Systeme mit externer Verbindung. Wenn Ihre in Azure gehosteten virtuellen Computer Anwendungen oder Dienste hosten, die über das Internet zugänglich sein sollen, müssen Sie beim Patchen aufmerksam sein. Beschränken Sie sich beim Patchen nicht nur auf das Betriebssystem. Ungepatchte Sicherheitsrisiken in Partneranwendungen können ebenfalls zu Problemen führen, die mit einer guten Patchverwaltung vermeidbar sind.

**Bewährte Methode**: Stellen Sie eine Sicherungslösung bereit und testen Sie diese.   
**Detail**: Eine Sicherung muss in gleicher Weise behandelt werden wie alle anderen Vorgänge. Dies gilt für alle Systeme in Ihrer Produktionsumgebung, die sich bis in die Cloud erstreckt.

Für Test- und Entwicklungssysteme müssen Sicherungsstrategien mit Wiederherstellungsfunktionen verwendet werden, die sich an den Erfahrungen orientieren, die Benutzer bereits mit lokalen Umgebungen gemacht haben. In Azure verschobene Workloads sollten sich möglichst in die vorhandenen Sicherheitslösungen integrieren lassen. Alternativ können Sie sich bei der Erfüllung ihrer Sicherungsanforderungen von [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) unterstützen lassen.

Organisationen, die keine Softwareupdaterichtlinien erzwingen, sind anfälliger für Angreifer, die sich bekannte, bereits korrigierte Sicherheitslücken zunutze machen. Unternehmen müssen zur Erfüllung von Branchenbestimmungen nachweisen, dass sie gewissenhaft arbeiten und geeignete Sicherheitsmaßnahmen ergreifen, um die Sicherheit ihrer Workloads in der Cloud zu gewährleisten.

Bewährte Softwareupdatemethoden für herkömmliche Rechenzentren und Azure-IaaS sind sich in vielen Punkten ähnlich. Sie sollten Ihre aktuellen Softwareupdaterichtlinien evaluieren und virtuelle Computer in Azure mit einbeziehen.

## <a name="manage-your-vm-security-posture"></a>Verwalten des Sicherheitsstatus Ihrer virtuellen Computer
Cyberbedrohungen entwickeln sich stetig weiter. Der Schutz Ihrer virtuellen Computer erfordert daher umfangreiche Überwachungsfunktionen, die Bedrohungen schnell erkennen, nicht autorisierte Zugriffe auf Ihre Ressourcen verhindern, Warnungen auslösen und falsch positive Ergebnisse verringern.

Den Sicherheitsstatus Ihrer [virtuellen Windows-Computer](../../security-center/security-center-introduction.md) und [virtuellen Linux-Computer](../../security-center/security-center-introduction.md) können Sie mithilfe von [Azure Security Center](../../security-center/security-center-introduction.md) überwachen. Zum Schutz Ihrer virtuellen Computer stehen Ihnen im Security Center folgende Funktionen zur Verfügung:

- Anwenden von Sicherheitseinstellungen des Betriebssystems mit empfohlenen Konfigurationsregeln.
- Ermitteln und Herunterladen sicherheitsrelevanter und wichtiger Updates, die möglicherweise noch fehlen.
- Bereitstellen von Empfehlungen zum Schutz von Endpunkten vor Schadsoftware.
- Überprüfen der Datenträgerverschlüsselung.
- Bewerten und Beseitigen von Sicherheitsrisiken.
- Erkennen von Bedrohungen.

Security Center kann aktiv nach Bedrohungen suchen, und potenzielle Bedrohungen werden in Sicherheitswarnungen angezeigt. Korrelierte Bedrohungen werden in einer zentralen Ansicht namens „Sicherheitsvorfall“ aggregiert.

Security Center speichert Daten in [Azure Monitor-Protokolle](../../azure-monitor/log-query/log-query-overview.md). Azure Monitor-Protokolle bietet eine Abfragesprache und eine Analyseengine, die Ihnen Einblicke in den Betrieb Ihrer Anwendungen und Ressourcen gibt. Zudem werden Daten aus [Azure Monitor](../../batch/monitoring-overview.md), Verwaltungslösungen und auf den virtuellen Computern in der Cloud oder lokal installierten Agents gesammelt. Dadurch erhalten Sie ein vollständiges Bild von Ihrer gesamten Umgebung.

Organisationen, die für ihre virtuellen Computer kein hohes Maß an Sicherheit erzwingen, bleiben potenzielle Vorfälle, bei denen nicht autorisierte Benutzer versuchen, die Sicherheitskontrollen zu umgehen, verborgen.

## <a name="monitor-vm-performance"></a>Überwachen der Leistung virtueller Computer
Ressourcenmissbrauch kann problematisch sein, wenn Prozesse von virtuellen Computern mehr Ressourcen beanspruchen als sie sollten. Leistungsprobleme bei einem virtuellen Computer können zu einer Dienstunterbrechung führen und gegen das Sicherheitsprinzip der Verfügbarkeit verstoßen. Dies ist besonders wichtig für virtuelle Computer, die IIS oder andere Webserver hosten, da hohe CPU-Auslastung oder Arbeitsspeichernutzung auf einen DoS-Angriff (Denial of Service) hinweisen. Der Zugriff auf virtuelle Computer muss nicht nur reaktiv – also wenn bereits ein Problem auftritt – sondern auch proaktiv anhand einer im Normalbetrieb ermittelten Baseline überwacht werden.

Sie sollten sich mit [Azure Monitor](../../azure-monitor/platform/data-platform.md) Einblick in den Zustand Ihrer Ressourcen verschaffen. Azure Monitor umfasst:

- [Protokolldateien zur Ressourcendiagnose](../../azure-monitor/platform/platform-logs-overview.md): Ihre VM-Ressourcen werden überwacht und potenzielle Probleme erkannt, die unter Umständen Leistung und Verfügbarkeit beeinträchtigen.
- [Azure-Diagnoseerweiterung](../../azure-monitor/platform/diagnostics-extension-overview.md): Stellt Überwachungs- und Diagnosefunktionen auf virtuellen Windows-Computern bereit. Diese Funktionen können Sie aktivieren, indem Sie die Erweiterung in die [Azure Resource Manager-Vorlage](../../virtual-machines/extensions/diagnostics-template.md) einbeziehen.

Organisationen, die die Leistung virtueller Computer nicht überwachen, können nicht ermitteln, ob bestimmte Veränderungen bei Leistungsmustern normal sind. Wenn ein virtueller Computer mehr Ressourcen beansprucht als normal, kann dies auf einen Angriff über eine externe Ressource oder die Ausführung eines kompromittierten Prozesses auf diesem virtuellen Computer hindeuten.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Verschlüsseln Ihrer VHD-Dateien
Sie sollten Ihre virtuellen Festplatten (VHDs) verschlüsseln, um Ihr Startvolume und Ihre Datenvolumes im Ruhezustand im Speicher zu schützen, zusammen mit Ihren Verschlüsselungsschlüsseln und Geheimnissen.

Mit [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) können Sie die Datenträger von virtuellen Windows- und Linux-IaaS-Computern verschlüsseln. Azure Disk Encryption verwendet das Branchenstandardfeature [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) von Windows und das Feature [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) von Linux, um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen. Die Lösung ist in [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) integriert, damit Sie die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement steuern und verwalten können. Diese Lösung stellt außerdem sicher, dass alle ruhenden Daten auf den Datenträgern der virtuellen Computer in Azure Storage verschlüsselt sind.

Folgende Methoden haben sich bei der Verwendung von Azure Disk Encryption bewährt:

**Bewährte Methode**: Aktivieren Sie Verschlüsselung auf virtuellen Computern.   
**Detail**: Azure Disk Encryption generiert die Verschlüsselungsschlüssel und schreibt sie in Ihren Schlüsseltresor. Das Verwalten von Verschlüsselungsschlüsseln im Schlüsseltresor erfordert eine Azure AD-Authentifizierung. Erstellen Sie dafür eine Azure AD-Anwendung. Zu Authentifizierungszwecken können Sie entweder die auf einem geheimen Clientschlüssel basierende Authentifizierung oder die [auf einem Clientzertifikat basierende Azure AD-Authentifizierung](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) verwenden.

**Bewährte Methode**: Sorgen Sie mit einem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) für zusätzlichen Schutz für Verschlüsselungsschlüssel. Fügen Sie Ihrem Schlüsseltresor einen KEK hinzu.   
**Detail**: Verwenden Sie das Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), um im Schlüsseltresor einen Schlüsselverschlüsselungsschlüssel zu erstellen. Sie können den KEK auch aus Ihrem lokalen Hardwaresicherheitsmodul (HSM) für die Schlüsselverwaltung importieren. Weitere Informationen finden Sie in der [Key Vault](../../key-vault/keys/hsm-protected-keys.md)-Dokumentation. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. Zusätzlichen Schutz vor versehentlichem Löschen von Schlüsseln bietet das Hinterlegen einer Kopie dieses Schlüssels in einem lokalen Schlüsselverwaltungs-HSM.

**Bewährte Methode**: Erstellen Sie eine [Momentaufnahme](../../virtual-machines/windows/snapshot-copy-managed-disk.md), und/oder sichern Sie die Datenträger, bevor diese verschlüsselt werden. Sicherungen bieten eine Wiederherstellungsoption, wenn während der Verschlüsselung ein unerwarteter Fehler auftritt.   
**Detail**: Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet **Set-AzVMDiskEncryptionExtension** verwenden, um verwaltete Datenträger durch das Angeben des Parameters *-skipVmBackup* zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie im Artikel [Azure Backup](../../backup/backup-azure-vms-encryption.md).

**Bewährte Methode**: Um sicherzustellen, dass die Verschlüsselungsgeheimnisse die Regionsgrenzen nicht verlassen, müssen der Schlüsseltresor und die VMs sich für Azure Disk Encryption in derselben Region befinden.   
**Detail**: Erstellen und verwenden Sie einen Schlüsseltresor, der sich in derselben Region wie die zu verschlüsselnde VM befindet.

Mit Azure Disk Encryption können Sie die folgenden geschäftlichen Anforderungen erfüllen:

- Virtuelle IaaS-Computer werden im Ruhezustand mit Verschlüsselungstechnologie nach Branchenstandard geschützt, um die Anforderungen an Unternehmenssicherheit und Compliance zu erfüllen.
- Virtuelle IaaS-Computer werden mit vom Kunden gesteuerten Schlüsseln und Richtlinien gestartet, und Sie können ihre Nutzung in Ihrem Schlüsseltresor überwachen.

## <a name="restrict-direct-internet-connectivity"></a>Einschränken der direkten Internetverbindung
Überwachen Sie die direkte Internetverbindung von VMs und schränken Sie diese ein. Angreifer scannen ständig öffentliche Cloud-IP-Adressbereiche nach offenen Verwaltungsports und probieren „einfache“ Angriffe mittels häufig verwendeter Kennwörter und bekannter ungepatchter Sicherheitslücken. Die folgende Tabelle enthält bewährte Methoden zum Schutz vor diesen Angriffen:

**Bewährte Methode**: Verhindern Sie eine unbeabsichtigte Offenlegung von Netzwerkrouting und -sicherheit.   
**Detail**: Verwenden Sie Azure RBAC, um sicherzustellen, dass nur die zentrale Netzwerkgruppe über Berechtigungen für Netzwerkressourcen verfügt.

**Bewährte Methode**: Identifizieren und korrigieren Sie exponierte VMs, die einen Zugriff über „alle“ Quell-IP-Adressen zulassen.   
**Detail**: Verwenden Sie Azure Security Center. Azure Security Center empfiehlt, den Zugriff über Endpunkte mit Internetzugriff einzuschränken, wenn für beliebige Ihrer Netzwerksicherheitsgruppen mindestens eine Eingangsregel gilt, die den Zugriff über „alle“ Quell-IP-Adressen zulässt. Azure Security Center empfiehlt, diese Eingangsregeln so zu ändern, dass der [Zugriff eingeschränkt](../../security-center/security-center-network-recommendations.md) wird auf Quell-IP-Adressen, die tatsächlich Zugriff benötigen.

**Bewährte Methode**: Beschränken Sie Verwaltungsports (RDP, SSH).   
**Detail**: Mit einem [JIT-VM-Zugriff](../../security-center/security-center-just-in-time.md) (Just-In-Time-VM-Zugriff) kann eingehender Datenverkehr auf den Azure-VMs gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und dennoch bei Bedarf einen einfachen Zugriff auf Verbindungen zu virtuellen Computern bereitzustellen. Wenn JIT aktiviert ist, sperrt Azure Security Center eingehenden Datenverkehr für Ihre Azure-VMs, indem eine Netzwerksicherheitsgruppen-Regel erstellt wird. Sie wählen die Ports auf dem virtuellen Computer aus, für die eingehender Datenverkehr gesperrt wird. Diese Ports werden durch die JIT-Lösung gesteuert.

## <a name="next-steps"></a>Nächste Schritte
Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).

Die folgenden Ressourcen enthalten allgemeinere Informationen zur Sicherheit in Azure und verwandten Microsoft-Diensten:
* [Blog des Azure-Sicherheitsteams](/archive/blogs/azuresecurity/): Hier finden Sie Informationen über den aktuellen Stand der Azure-Sicherheit.
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): Hier können Sie Microsoft-Sicherheitsrisiken, z.B. Probleme mit Azure, melden oder eine E-Mail an secure@microsoft.com schreiben.