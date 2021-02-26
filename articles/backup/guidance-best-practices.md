---
title: Leitfaden und bewährte Methoden
description: Bewährte Methoden und Anleitungen zum Sichern von Cloudworkloads und lokalen Workloads in der Cloud
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 7b65556d8dd9b5b12e8da25055f6e39732c83afd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258760"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Sichern von Cloudworkloads und lokalen Workloads in der Cloud

## <a name="introduction"></a>Einführung

Azure Backup schützt Ihre Datenbestände in Azure umfassend durch eine einfache, sichere und kostengünstige Lösung, die keine Infrastruktur erfordert. Azure Backup ist die integrierte Datenschutzlösung von Azure für eine breite Palette von Workloads. Azure Backup schützt Ihre unternehmenskritischen Workloads, die in der Cloud ausgeführt werden, und stellt sicher, dass Ihre Sicherungen jederzeit verfügbar sind und innerhalb Ihrer gesamten Sicherungsumgebung bedarfsorientiert verwaltet werden.

### <a name="intended-audience"></a>Zielpublikum

Die primäre Zielgruppe für diesen Artikel sind IT- und Anwendungsadministratoren sowie Implementierer großer und mittlerer Organisationen, die sich über die Möglichkeiten der in Azure integrierten Datensicherungstechnologie Azure Backup informieren und erfahren möchten, wie sie effizient Lösungen zum besseren Schutz der Bereitstellungen implementieren können. In diesem Artikel wird davon ausgegangen, dass Sie mit den Kerntechnologien von Azure und den Datenschutzkonzepten vertraut sind und Erfahrung im Umgang mit einer Sicherungslösung haben. Die in diesem Artikel behandelten Anleitungen können es Ihnen erleichtern, Ihre Sicherungslösung auf Azure nach etablierten Mustern zu gestalten und bekannte Fallstricke zu vermeiden.

### <a name="how-this-article-is-organized"></a>Wie dieser Artikel aufgebaut ist

Es ist zwar einfach, mit dem Schutz von Infrastruktur und Anwendungen auf Azure zu beginnen, aber wenn Sie sicherstellen, dass die zugrunde liegenden Azure-Ressourcen korrekt konfiguriert sind und optimal genutzt werden, können Sie die Amortisationszeit beschleunigen. Dieser Artikel gibt einen kurzen Überblick über Entwurfsüberlegungen und Anleitungen zur optimalen Konfiguration Ihrer Azure Backup-Bereitstellung. Dabei werden die Kernkomponenten (z. B. Recovery Services-Tresor, Sicherungsrichtlinie) und Konzepte (z. B. Governance) untersucht, die Sichtweisen auf diese Komponenten und zugehörigen Funktionen betrachtet sowie Links zur detaillierten Produktdokumentation bereitgestellt.

## <a name="architecture"></a>Aufbau

![Azure Backup-Architektur](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Arbeitsauslastungen

Azure Backup ermöglicht den Datenschutz für verschiedene Workloads (lokale und Cloudworkloads). Es handelt sich um einen sicheren und zuverlässigen integrierten Datenschutzmechanismus in Azure. Azure Backup kann den Schutz nahtlos über mehrere Workloads hinweg skalieren, ohne dass Ihnen Verwaltungsaufwand entsteht. Es gibt auch mehrere Automatisierungskanäle, um dies zu ermöglichen (über PowerShell, CLI, Azure Resource Manager-Vorlagen und REST-APIs).

* **Skalierbare, dauerhafte und sichere Speicher**: Azure Backup verwendet zuverlässigen BLOB-Speicher mit integrierten Sicherheits- und Hochverfügbarkeitsfeatures. Sie können LRS-, GRS- oder RA-GRS-Speicher für Ihre Sicherungsdaten wählen.  

* **Integration der nativen Workload**: Azure Backup bietet native Integration mit Azure Workloads (VMs, SAP HANA, SQL auf virtuellen Azure-Computern und sogar Azure Files), ohne dass Sie Automatisierung oder Infrastruktur verwalten müssen, um Agents bereitzustellen, neue Skripts zu schreiben oder Speicher bereitzustellen.

### <a name="data-plane"></a>Datenebene

* **Automatisierte Speicherverwaltung**: Azure Backup automatisiert die Bereitstellung und Verwaltung von Speicherkonten für die Sicherungsdaten, um sicherzustellen, dass sie beim Anwachsen der Sicherungsdaten skaliert werden.

* **Schutz vor böswilligem Löschen**: Schützen Sie sich durch vorläufiges Löschen von Sicherungen vor versehentlichen und böswilligen Versuchen, Ihre Sicherungen zu löschen. Die gelöschten Sicherungsdaten werden 14 Tage lang kostenlos aufbewahrt und können aus diesem Zustand wiederhergestellt werden.

* **Sichere verschlüsselte Sicherungen:** Azure Backup stellt sicher, dass Ihre Sicherungsdaten auf sichere Weise gespeichert werden, und nutzt dabei die integrierten Sicherheitsfunktionen der Azure-Plattform wie Azure RBAC und Verschlüsselung.

* **Verwaltung des Sicherungsdaten-Lebenszyklus:** Azure Backup bereinigt ältere Sicherungsdaten automatisch gemäß den Aufbewahrungsrichtlinien. Sie können Ihre Daten auch aus dem operativen Speicher in den Tresorspeicher übertragen.

### <a name="management-plane"></a>Verwaltungsebene

* **Zugriffssteuerung**: Tresore (Recovery Services- und Sicherungstresore) stellen die Verwaltungsfunktionen bereit, und es kann über Azure-Portal, Backup Center, Tresordashboards, SDK, Befehlszeilenschnittstelle und sogar REST-APIs darauf zugegriffen werden. Es handelt sich außerdem um eine Azure RBAC-Grenze, die Ihnen die Möglichkeit bietet, den Zugriff auf Sicherungen auf autorisierte Sicherungsadministratoren zu beschränken.

* **Richtlinienverwaltung**: Azure Backup-Richtlinien innerhalb jedes Tresors definieren, wann die Sicherungen ausgelöst und wie lange sie aufbewahrt werden müssen. Sie können diese Richtlinien auch verwalten und auf mehrere Elemente anwenden.

* **Überwachung und Berichterstellung**: Azure Backup ist in Log Analytics integriert und bietet die Möglichkeit, Berichte auch über Workbooks einzusehen.

* **Verwaltung von Momentaufnahmen**: Azure Backup erstellt Momentaufnahmen für einige native Azure-Workloads (VMs und Azure Files), verwaltet diese Momentaufnahmen und ermöglicht schnelle Wiederherstellungen aus ihnen. Diese Option verkürzt die Zeit zur Wiederherstellung Ihrer Daten auf dem Originalspeicher drastisch.

## <a name="vault-considerations"></a>Überlegungen zum Tresor

Azure Backup nutzt Tresore (Recovery Services- und Sicherungstresore) zum Orchestrieren und Verwalten von Sicherungen. Azure Backup nutzt Tresore außerdem zum Speichern von gesicherten Daten. Ein effektiver Tresorentwurf hilft Organisationen in der dabei, für die Unterstützung Ihrer geschäftlichen Prioritäten eine Struktur zum Organisieren und Verwalten von Sicherungsressourcen in Azure einzurichten und zu verwalten. Berücksichtigen Sie beim Erstellen eines Tresors die folgenden Richtlinien:  

### <a name="align-to-subscription-design-strategy"></a>Ausrichten an der Abonnemententwurfsstrategie

Da der Tresor auf ein Abonnement beschränkt ist, passen Sie den Tresorentwurf an die Strategie des Abonnemententwurfs an, z. B. die *Strategie der Anwendungskategorie*, bei der Abonnements auf der Grundlage bestimmter Anwendungen oder Dienste oder nach Anwendungsarchetypen getrennt werden. Weitere Informationen finden Sie in [diesem Artikel](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Einzelne oder mehrere Tresore

Sie können einen einzelnen Tresor oder mehrere Tresore verwenden, um Ihr Sicherung zu organisieren und zu verwalten. Berücksichtigen Sie die folgenden Richtlinien:

* Wenn Ihre Workloads alle von einem einzigen Abonnement und einer einzigen Ressource verwaltet werden, können Sie einen einzigen Tresor zum Überwachen und Verwalten Ihrer Sicherungsumgebung verwenden.

* Wenn Ihre Workloads auf Abonnements verteilt sind, können Sie mehrere Tresore erstellen, einen oder mehrere pro Abonnement.
  * Backup Center ermöglicht es Ihnen, über eine zentralisierte Benutzeroberfläche alle Aufgaben im Zusammenhang mit der Sicherung zu verwalten. [Hier erhalten Sie weitere Informationen]().
  * Sie können Ihre Ansichten mit Arbeitsmappenvorlagen anpassen. Sicherungs-Explorer ist eine solche Vorlage für virtuelle Azure-Computer. [Hier erhalten Sie weitere Informationen](monitor-azure-backup-with-backup-explorer.md).
  * Wenn Sie eine konsistente Richtlinie über mehrere Tresore hinweg benötigen, können Sie die Azure-Richtlinie verwenden, um die Sicherungsrichtlinie über mehrere Tresore hinweg weiterzugeben. Sie können eine benutzerdefinierte [Azure Policy-Definition](../governance/policy/concepts/definition-structure.md) schreiben, die den Effekt [‚deployifnotexists‘](../governance/policy/concepts/effects.md#deployifnotexists) verwendet, um eine Sicherungsrichtlinie über mehrere Tresore weiterzugeben. Sie können diese Azure Policy-Definition auch einem bestimmten Bereich (Abonnement oder RG) [zuweisen](../governance/policy/assign-policy-portal.md), sodass eine Sicherungsrichtlinienressource für alle Recovery Services-Tresore im Bereich der Azure Policy-Zuweisung bereitgestellt wird. Die Einstellungen der Sicherungsrichtlinie (wie z. B. Sicherungshäufigkeit, Aufbewahrung usw.) sollten vom Benutzer als Parameter in der Azure Policy-Zuweisung festgelegt werden.

* Wenn Ihr organisatorischer Fußabdruck wächst, möchten Sie möglicherweise aus folgenden Gründen Workloads über Abonnements hinweg verschieben: Ausrichtung nach Sicherungsrichtlinie, Konsolidierung von Tresoren, Kompromiss bezüglich geringerer Redundanz zum Einsparen von Kosten (Wechsel von GRS zu LRS).  Azure Backup unterstützt das Verschieben eines Recovery Services-Tresors über Azure-Abonnements hinweg oder in eine andere Ressourcengruppe innerhalb desselben Abonnements. [Hier erhalten Sie weitere Informationen](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Standardeinstellungen überprüfen

Vergewissern Sie sich vor dem Konfigurieren von Sicherungen im Tresor, dass die Standardeinstellungen für Speicherreplikationstyp und Sicherheitseinstellungen Ihre Anforderungen erfüllen.

* Der *Speicherreplikationstyp* ist standardmäßig auf Georedundant (GRS) festgelegt. Nachdem Sie die Sicherung konfiguriert haben, ist die Option zum Ändern deaktiviert. Führen Sie [diese](backup-create-rs-vault.md#set-storage-redundancy) Schritte aus, um die Einstellungen zu überprüfen und zu ändern.

* *Vorläufiges Löschen* ist für neu erstellte Tresore standardmäßig auf Aktiviert festgelegt, um das versehentliche oder bösartige Löschen von Sicherungsdaten zu verhindern. Führen Sie [diese](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) Schritte aus, um die Einstellungen zu überprüfen und zu ändern.

* Die *regionsübergreifende Wiederherstellung* ermöglicht Ihnen, virtuelle Azure-Computer in einer sekundären Region wiederherzustellen, bei der es sich um eine gepaarte Azure-Region handelt. Mit dieser Option können Sie Übungen durchführen, um Audit- oder Complianceanforderungen zu erfüllen und die VM oder ihre Festplatte bei einem Notfall in der primären Region wiederherzustellen. CRR ist ein optionales Feature für jeden GRS-Tresor. [Hier erhalten Sie weitere Informationen](backup-create-rs-vault.md#set-cross-region-restore).

* Bevor Sie Ihren Tresorentwurf abschließen, sollten Sie die [Tresorunterstützungsmatrizen](backup-support-matrix.md#vault-support) überprüfen, um die Faktoren zu verstehen, die Ihre Entwurfsentscheidungen beeinflussen oder einschränken könnten.

## <a name="backup-policy-considerations"></a>Überlegungen zur Sicherungsrichtlinie

Die Azure Backup-Richtlinie umfasst zwei Aspekte: *Zeitplan* (wann die Sicherung erfolgen soll) und *Aufbewahrung* (wie lange die Sicherung aufbewahrt werden soll). Sie können die Richtlinie auf der Grundlage der Art der zu sichernden Daten, der RTO-/RPO-Anforderungen, der operativen oder behördlichen Complianceanforderungen und des Workloadtyps (z. B. VM, Datenbank, Dateien) definieren. [Hier erhalten Sie weitere Informationen](backup-architecture.md#backup-policy-essentials).

Berücksichtigen Sie beim Erstellen einer Sicherungsichtlinien die folgenden Richtlinien:

### <a name="schedule-considerations"></a>Überlegungen zum Zeitplan

* Erwägen Sie, VMs, die die gleiche Startzeit, Häufigkeit und Aufbewahrungseinstellungen für den Zeitplan erfordern, innerhalb einer einzigen Richtlinie zu gruppieren.

* Stellen Sie sicher, dass die geplante Startzeit für die Sicherung nicht während der Spitzenlastzeiten der Produktionsanwendung liegt.

* Um den Sicherungsdatenverkehr zu verteilen, überlegen Sie, verschiedene virtuelle Computer zu unterschiedlichen Tageszeiten zu sichern, und achten Sie darauf, dass keine Überschneidungen auftreten.

### <a name="retention-considerations"></a>Überlegungen zur Aufbewahrung

* Kurzfristige Aufbewahrung kann „Minuten“ oder „täglich“ sein. Die Aufbewahrung für „wöchentliche“, „monatliche“ oder „jährliche“ Sicherungspunkte wird als langfristige Aufbewahrung bezeichnet.

* Langfristige Aufbewahrung:
  * Geplant (Complianceanforderungen): Wenn Sie im Voraus wissen, dass Daten noch Jahre nach dem aktuellen Zeitpunkt benötigt werden, dann verwenden Sie die Langzeitaufbewahrung.
  * Ungeplant (Bedarfsgesteuerte Anforderung): Wenn Sie es nicht im Voraus wissen, dann können Sie „Bedarfsgesteuert“ mit bestimmten benutzerdefinierten Aufbewahrungseinstellungen verwenden (die Richtlinieneinstellungen haben keinen Einfluss auf diese benutzerdefinierten Aufbewahrungseinstellungen).

* Bedarfsgesteuerte Sicherung mit benutzerdefinierter Aufbewahrung: Wenn Sie eine Sicherung erstellen müssen, die nicht über die Sicherungsrichtlinie geplant ist, können Sie eine bedarfsgesteuerte Sicherung verwenden. Dies kann nützlich sein, um Sicherungen zu erstellen, die nicht in Ihren Sicherungszeitplan passen, oder um detaillierte Sicherungen zu erstellen (z. B. mehrere IaaS VM-Sicherungen pro Tag, da geplante Sicherungen nur ein Mal pro Tag möglich sind). Beachten Sie unbedingt, dass die in der geplanten Richtlinie definierte Aufbewahrungsrichtlinie nicht für bedarfsgesteuerte Sicherung gilt.

### <a name="optimize-backup-policy"></a>Optimieren der Sicherungsrichtlinie

* Wenn sich Ihre Geschäftsanforderungen ändern, müssen Sie möglicherweise die Aufbewahrungsdauer verlängern oder verkürzen. Wenn Sie dies tun, können Sie Folgendes erwarten:  
  * Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert und gemäß der neuen Richtlinie aufbewahrt.
  * Bei einer Verkürzung der Aufbewahrung werden Wiederherstellungspunkte zum Löschen im Rahmen der nächsten Bereinigung markiert und dementsprechend gelöscht.
  * Die neuesten Aufbewahrungsregeln gelten für alle Aufbewahrungspunkte (mit Ausnahme von bedarfsgesteuerten Aufbewahrungspunkten). Wenn also der Aufbewahrungszeitraum verlängert wird (z. B. auf 100 Tage), dann werden beim Erstellen der Sicherung, gefolgt von einer Verkürzung des Aufbewahrungszeitraums (z. B. von 100 Tagen auf sieben Tage), alle Sicherungsdaten entsprechend des zuletzt angegebenen Aufbewahrungszeitraums (d. h. 7 Tage) aufbewahrt.

* Azure Backup bietet Ihnen die Flexibilität, *den Schutz zu beenden und Ihre Sicherungen zu verwalten*:
  * *Schutz beenden und Sicherungsdaten beibehalten*. Wenn Sie Ihre Datenquelle (VM, Anwendung) außer Betrieb nehmen oder einstellen, aber Daten zu Audit- oder Compliancezwecken aufbewahren müssen, können Sie diese Option verwenden, um alle zukünftigen Sicherungsaufträge zum Schutz Ihrer Datenquelle zu beenden und die gesicherten Wiederherstellungspunkte beizubehalten. Anschließend können Sie den VM-Schutz wiederherstellen oder fortsetzen.
  * *Schutz beenden und Sicherungsdaten löschen*. Mit dieser Option werden alle zukünftigen Sicherungsaufträge zum Schutz Ihres virtuellen Computers beendet und alle Wiederherstellungspunkte gelöscht. Sie können den virtuellen Computer nicht wiederherstellen und auch nicht die Option Sicherung fortsetzen verwenden.

  * Wenn Sie den Schutz (einer Datenquelle, der mit „Daten beibehalten“ gestoppt wurde) wieder aufnehmen, gelten die Aufbewahrungsregeln. Abgelaufene Wiederherstellungspunkte werden (zum geplanten Zeitpunkt) entfernt.

* Bevor Sie Ihren Richtlinienentwurf fertigstellen, ist es wichtig, sich der folgenden Faktoren bewusst zu sein, die Ihre Entwurfsentscheidungen beeinflussen könnten.
  * Eine Sicherungsrichtlinie wird auf den Bereich eines Tresor bezogen.
  * Es gibt eine Begrenzung der Anzahl der Elemente pro Richtlinie (z. B. 100 VMs). Zum Skalieren können Sie doppelte Richtlinien mit gleichen oder unterschiedlichen Zeitplänen erstellen.
  * Sie können bestimmten Wiederherstellungspunkte nicht selektiv löschen.
  * Sie können die geplante Sicherung nicht vollständig deaktivieren und die Datenquelle in einem geschützten Zustand halten. Die geringste Häufigkeit, die Sie für Sicherungen mit der Richtlinie konfigurieren können, ist eine wöchentliche geplante Sicherung. Eine Alternative bestünde darin, den Schutz mit „Daten beibehalten“ zu stoppen und den Schutz jedes Mal zu aktivieren, wenn Sie eine Sicherung erstellen möchten, dann eine bedarfsgesteuerte Sicherung zu erstellen, und den Schutz anschließend wieder zu deaktivieren, die Sicherungsdaten aber aufzubewahren. [Hier erhalten Sie weitere Informationen](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Sicherheitshinweise

Zum Schutz Ihrer Sicherungsdaten zur Einhaltung der Sicherheitsanforderungen Ihres Unternehmens bietet Azure Backup Vertraulichkeit, Integrität und Verfügbarkeit gegenüber vorsätzlichen Angriffen und Missbrauch Ihrer wertvollen Daten und Systeme. Beachten Sie die folgenden Sicherheitsrichtlinien für Ihre Azure Backup-Lösung:

### <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

* Die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) ermöglicht eine differenzierte Zugriffsverwaltung, eine Trennung der Aufgaben innerhalb Ihres Teams und die Gewährung von Zugriffsrechten für Benutzer nur in dem Umfang, der für die Ausführung ihrer Aufgaben erforderlich ist. [Hier erhalten Sie weitere Informationen](backup-rbac-rs-vault.md).

* Azure Backup bietet drei integrierte Rollen, um Vorgänge der Sicherungsverwaltung zu steuern: Sicherungsmitwirkende, Sicherungsoperatoren und Sicherungsleser. [Hier erhalten Sie weitere Informationen](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup verfügt über mehrere integrierte Sicherheitskontrollen, um Sicherheitsrisiken zu verhindern, zu erkennen und darauf zu reagieren (weitere Informationen).

* Von Recovery Services-Tresoren verwendete Speicherkonten sind isoliert, sodass böswillige Akteure keinen Zugriff darauf erhalten. Der Zugriff ist nur über Azure Backup-Verwaltungsvorgänge, z. B. eine Wiederherstellung, zulässig.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Verschlüsselung von Daten während der Übertragung und im Ruhezustand

Die Verschlüsselung schützt Ihre Daten und unterstützt Sie beim Einhalten der Sicherheits- und Complianceanforderungen Ihrer Organisation.

* In Azure werden Daten bei der Übertragung zwischen Azure Storage und dem Tresor per HTTPS geschützt. Diese Daten bleiben innerhalb des Azure-Backbone-Netzwerks.

* Sicherungsdaten werden automatisch mit von Microsoft verwalteten Schlüsseln verschlüsselt. Alternativ können Sie Ihre eigenen Schlüssel verwenden, die auch als [vom Kunden verwaltete Schlüssel](encryption-at-rest-with-cmk.md) bekannt sind.

* Azure Backup unterstützt die Sicherung und Wiederherstellung von Azure-VMs, deren Datenträger für Betriebssystem/Daten mit Azure Disk Encryption (ADE) verschlüsselt wurden. [Hier erhalten Sie weitere Informationen](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Schutz von Sicherungsdaten vor unbeabsichtigten Löschungen

Azure Backup verfügt über Sicherheitsfeatures für den Schutz von Sicherungsdaten auch nach dem Löschen. Beim vorläufigen Löschen werden die Sicherungsdaten 14 Tage länger aufbewahrt, damit das jeweilige Sicherungselement auch dann ohne Datenverluste wiederhergestellt werden kann, wenn ein Benutzer die Sicherung (einer VM, SQL Server-Datenbank, Azure-Dateifreigabe, SAP HANA-Datenbank) löscht. Für die zusätzlichen 14 Tage der Aufbewahrung von Sicherungsdaten mit dem Status „Vorläufiges Löschen“ fallen für Sie keine Kosten an. [Hier erhalten Sie weitere Informationen](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Überwachung und Warnungen zu verdächtigen Aktivitäten

Azure Backup bietet integrierte Überwachungs- und Warnungsfunktionen zum Anzeigen und Konfigurieren von Aktionen für Ereignisse im Zusammenhang mit Azure Backup. [Hier erhalten Sie weitere Informationen](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Sicherheitsfeatures für den Schutz von Hybridsicherungen

Azure Backup-Dienst verwendet den Microsoft Azure Recovery Services-Agent (MARS) zum Sichern und Wiederherstellen von Dateien, Ordnern und des Volume- oder Systemstatus von einem lokalen Computer in Azure. MARS bietet jetzt Sicherheitsfeatures: eine Passphrase zum Verschlüsseln vor dem Hochladen und Entschlüsseln nach dem Herunterladen von Azure Backup, gelöschte Sicherungsdaten werden für weitere 14 Tage ab dem Datum der Löschung aufbewahrt, und kritische Vorgänge (z. B. das Ändern einer Passphrase) können nur von Benutzern mit gültigen Azure-Anmeldeinformationen durchgeführt werden. [Hier erhalten Sie weitere Informationen](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Überlegungen zu Netzwerken

Azure Backup erfordert das Verschieben von Daten Ihrer Workload in den Recovery Services-Tresor. Azure Backup bietet mehrere Funktionen, um Sicherungsdaten vor unbeabsichtigter Offenlegung zu schützen (z. B. vor einem Man-in-the-Middle-Angriff auf das Netzwerk). Berücksichtigen Sie die folgenden Richtlinien:

### <a name="internet-connectivity"></a>Internetkonnektivität

* *Azure VM-Sicherung*: Die gesamte erforderliche Kommunikation und Datenübertragung zwischen Speicher und Azure Backup-Dienst erfolgt im Azure-Netzwerk, ohne dass auf Ihr virtuelles Netzwerk zugegriffen werden muss. Daher müssen Sie bei der Sicherung von virtuellen Azure-Computern, die sich in geschützten Netzwerken befinden, keinen Zugriff auf IP-Adressen oder FQDNs gewähren.

* *SAP HANA-Datenbanken auf einem virtuellen Azure-Computer, SQL Server-Datenbanken auf einem virtuellen Azure-Computer*: Benötigen eine Verbindung zum Azure Backup-Dienst, zu Azure Storage und zu Azure Active Directory. Dies kann durch die Verwendung privater Endpunkte oder durch die Gewährung des Zugriffs auf die erforderlichen öffentlichen IP-Adressen oder FQDNs erreicht werden. Wenn keine ordnungsgemäße Verbindung zu den erforderlichen Azure-Diensten zugelassen wird, kann dies zu Fehlern bei Operationen wie der Datenbankermittlung, der Konfiguration von Sicherungen, der Durchführung von Sicherungen und der Wiederherstellung von Daten führen. Eine vollständige Anleitung bei Verwendung von NSG-Tags, Azure-Firewall und HTTP-Proxy im Netzwerk finden Sie in diesen Artikeln: [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) und [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity).

* *Hybrid*: der MARS-Agent (Microsoft Azure Recovery Services) benötigt Netzwerkzugriff für alle wichtigen Vorgänge wie Installation, Konfiguration, Sicherung und Wiederherstellung. Der MARS-Agent kann eine Verbindung zum Azure Backup-Dienst über [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) herstellen, indem er öffentliches Peering (verfügbar für alte Leitungen) und Microsoft-Peering, über [private Endpunkte](install-mars-agent.md#private-endpoints) bzw. über [Proxy/Firewall mit entsprechenden Zugriffssteuerungen](install-mars-agent.md#verify-internet-access) nutzt.

### <a name="private-endpoints-for-azure-backup"></a>Private Endpunkte für Azure Backup

Ein [privater Endpunkt](../private-link/private-endpoint-overview.md) in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Azure Backup ermöglicht Ihnen die sichere Sicherung und Wiederherstellung Ihrer Daten aus Ihren Recovery Services-Tresoren unter Verwendung privater Endpunkte.

* Wenn Sie private Endpunkte für den Tresor aktivieren, werden sie nur für die Sicherung und Wiederherstellung von SQL- und SAP HANA-Workloads in Sicherungen virtueller Azure-Computer und MARS-Agents verwendet.  Sie können den Tresor auch für die Sicherung anderer Workloads einsetzen (die allerdings keine privaten Endpunkte benötigen). Neben der Sicherung von SQL- und SAP HANA-Workloads und einer Sicherung mit dem MARS-Agent werden private Endpunkte im Fall einer Azure VM-Sicherung auch für die Dateiwiederherstellung verwendet. [Hier erhalten Sie weitere Informationen](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory unterstützt derzeit keine privaten Endpunkte. Daher müssen IP-Adressen und FQDNs, die für Azure Active Directory erforderlich sind, ein ausgehender Zugriff aus dem abgesicherten Netzwerk gestattet werden, wenn eine Sicherung von Datenbanken auf virtuellen Azure-Computern und eine Sicherung mit dem MARS-Agent erfolgt. Sie können ggf. auch NSG- und Azure Firewall-Tags verwenden, um Zugriff auf Azure AD zuzulassen. [Weitere Informationen zu erforderlichen Komponenten finden Sie hier.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Governanceüberlegungen

Die Governance in Azure wird in erster Linie mit [Azure Policy](../governance/policy/overview.md) und [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) implementiert. [Azure-Richtlinien](../governance/policy/overview.md) ermöglicht es Ihnen, Richtliniendefinitionen zu erstellen, zuzuweisen und zu verwalten, um Regeln für Ihre Ressourcen durchzusetzen. Dieses Feature gewährleistet die Konformität dieser Ressourcen mit Ihren Unternehmensstandards. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) ermöglicht es Ihnen, die Cloudnutzung und Ausgaben für Ihre Azure-Ressourcen und andere Cloudanbieter nachzuverfolgen. Auch die folgenden Tools wie der [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) und [Azure Advisor](../advisor/advisor-overview.md) spielen eine wichtige Rolle im Kostenmanagementprozess.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup unterstützt zwei Schlüsselszenarien über das integrierte Azure Policy

* Stellen Sie sicher, dass neu erstellte geschäftskritische Computer automatisch mit den richtigen Aufbewahrungseinstellungen gesichert werden. Azure Backup bietet eine integrierte Richtlinie (unter Verwendung von Azure Policy), die allen Azure-VMS an einem bestimmten Standort innerhalb eines Abonnements oder einer Ressourcengruppe zugewiesen werden kann. Wird diese Richtlinie einem bestimmten Bereich zugewiesen, werden alle in diesem Bereich erstellten neuen virtuellen Computer automatisch für die Sicherung in einem vorhandenen Tresor am gleichen Speicherort und im gleichen Abonnement konfiguriert. Der Benutzer kann den Tresor und die Aufbewahrungsrichtlinie angeben, die den gesicherten VMs zugeordnet werden sollen. [Hier erhalten Sie weitere Informationen](backup-azure-auto-enable-backup.md).

* Stellen Sie sicher, dass für neu erstellte Tresore die Diagnose aktiviert ist. Häufig ist das manuelle Hinzufügen einer Diagnoseeinstellung pro Tresor eine mühselige Aufgabe. Außerdem müssen für jeden neu erstellten Tresor auch Diagnoseeinstellungen aktiviert sein, damit Sie für diesen Tresor Berichte anzeigen können. Um das Erstellen von Diagnoseeinstellungen nach Maß (mit Log Analytics als Ziel) zu vereinfachen, bietet Azure Backup eine integrierte Azure Policy. Diese Richtlinie fügt allen Tresoren in jedem Abonnement bzw. jeder Ressourcengruppe eine LA-Diagnoseeinstellung hinzu. Die folgenden Abschnitte enthalten Anweisungen zur Verwendung dieser Richtlinie. [Hier erhalten Sie weitere Informationen](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Kostenbetrachtung für Azure Backup

Die Funktionen des Azure Backup-Diensts bieten Ihnen die Flexibilität, Ihre Kosten effektiv zu verwalten und trotzdem Ihre Geschäftsanforderungen bezüglich BCDR (Business Continuity & Disaster Recovery) zu erfüllen. Berücksichtigen Sie die folgenden Richtlinien:

* Verwenden Sie den Preisrechner, um die Kosten zu bewerten und zu optimieren, indem Sie verschiedene Hebel anpassen. [Weitere Informationen finden Sie hier](azure-backup-pricing.md)

* Sicherungs-Explorer: Verwenden Sie den Sicherungs-Explorer oder Sicherungsberichte, um das Wachstum des Sicherungsspeichers zu verstehen und zu optimieren und Sicherungen für unkritische Workloads oder gelöschte VMs zu beenden. Sie können eine aggregierte Ansicht Ihrer gesamten Ressourcen im Hinblick auf das Sichern anzeigen. Dazu gehören nicht nur Informationen über Ihre Sicherungselemente, sondern auch Ressourcen, die nicht für die Sicherung konfiguriert sind. Dadurch wird sichergestellt, dass Sie den Schutz kritischer Daten in Ihrem wachsenden Bestand nie aus den Augen verlieren und Ihre Sicherungen für unkritische oder gelöschte Workloads optimiert sind.

* Optimieren der Sicherungsrichtlinie
  * Optimieren des Zeitplans und der Aufbewahrungseinstellungen auf der Grundlage von Workloadarchetypen (z. B. aufgabenkritisch, nicht kritisch)
  * Optimieren von Aufbewahrungseinstellungen für die sofortige Wiederherstellung
  * Wählen Sie den richtigen Sicherungstyp aus, um die Anforderungen zu erfüllen, und berücksichtigen Sie dabei die unterstützten Sicherungstypen (vollständig, inkrementell, Protokoll, differentiell) je nach Workload in Azure Backup.

* Selektive Sicherung von Datenträgern: „Datenträger ausschließen“ (Previewfunktion) bietet eine effiziente und kostengünstige Möglich, kritische Daten selektiv zu sichern. Sichern Sie beispielsweise nur einen Datenträger, wenn Sie die übrigen, an einen virtuellen Computer angeschlossenen Datenträger nicht sichern möchten. Dies ist auch nützlich, wenn Sie über mehrere Sicherungslösungen verfügen. Wenn Sie Ihre Datenbanken oder Daten zum Beispiel mit einer Workloadsicherungslösung (SQL Server-Datenbank in Azure VM-Sicherung) sichern und für ausgewählte Datenträger eine Sicherung auf Azure VM-Ebene verwenden möchten.

* Azure Backup erstellt Momentaufnahmen von virtuellen Azure-Computern und speichert diese zur Verbesserung der Erstellung eines Wiederherstellungspunkts und zur Beschleunigung von Wiederherstellungsvorgängen zusammen mit den Datenträgern. Dies wird als „Sofortige Wiederherstellung“ bezeichnet. Standardmäßig werden Momentaufnahmen der sofortigen Wiederherstellung zwei Tage lang aufbewahrt. Mit diesem Feature kann eine Wiederherstellung über diese Momentaufnahmen mit reduzierten Wiederherstellungszeiten durchgeführt werden. Es reduziert die erforderliche Zeit zum Transformieren und Zurückkopieren von Daten aus dem Tresor. Aus diesem Grund werden Ihnen Speicherkosten angezeigt, die den Momentaufnahmen aus diesem Zeitraum entsprechen. [Hier erhalten Sie weitere Informationen](backup-instant-restore-capability.md#configure-snapshot-retention).

* Der Speicherreplikationstyp des Azure Backup-Tresors ist standardmäßig auf „Georedundant“ (GRS) festgelegt. Diese Option kann nach dem Starten des Schutzes von Elementen nicht mehr geändert werden. Georedundanter Speicher (GRS) bietet ein höheres Maß an Datenbeständigkeit als lokal redundanter Speicher (LRS), ermöglicht die Aktivierung der regionsübergreifenden Wiederherstellung und ist teurer. Überprüfen Sie die Kompromisse zwischen geringeren Kosten und einer höheren Datenbeständigkeit, und entscheiden Sie, was für Ihr Szenario am besten geeignet ist. [Weitere Informationen finden Sie hier](backup-create-rs-vault.md#set-storage-redundancy)

* Wenn Sie sowohl den in einer VM ausgeführten Workload als auch die VM selbst schützen, prüfen Sie, ob dieser doppelte Schutz erforderlich ist.

## <a name="monitoring-and-alerting-considerations"></a>Überlegungen zu Überwachung und Warnung

Als Sicherungsbenutzer oder -administrator sollten Sie alle Sicherungslösungen überwachen können und bei wichtigen Szenarien benachrichtigt werden. In diesem Abschnitt werden die Überwachungs- und Benachrichtigungsfunktionen im Azure Backup-Dienst näher erläutert.

### <a name="monitoring"></a>Überwachung

* Azure Backup bietet **integrierte Auftragsüberwachung** für Vorgänge wie das Konfigurieren der Sicherung, das Sichern, das Wiederherstellen oder das Löschen der Sicherung. Diese bezieht sich auf den Bereich eines Tresor und eignet sich ideal für die Überwachung eines einzelnen Tresors. [Hier erhalten Sie weitere Informationen](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Wenn Sie operative Aktivitäten in großem Maßstab überwachen müssen, bietet der **Sicherungs-Explorer** eine aggregierte Ansicht Ihrer gesamten Sicherungsumgebung und ermöglicht eine detaillierte Drilldownanalyse und Problembehandlung. Eine integrierte Azure Monitor-Arbeitsmappe bietet einen einzigen, zentralen Ort zur Überwachung operativer Aktivitäten in der gesamten Sicherungsumgebung von Azure über Mandanten, Standorte, Abonnements, Ressourcengruppen und Tresore hinweg. [Hier erhalten Sie weitere Informationen](monitor-azure-backup-with-backup-explorer.md).
  * Verwenden Sie sie, um Ressourcen zu identifizieren, die nicht für die Sicherung konfiguriert sind, und um sicherzustellen, dass Sie den Schutz wichtiger Daten in Ihrer wachsenden Umgebung nicht aus den Augen verlieren.
  * Das Dashboard bietet operative Aktivitäten für die letzten sieben Tage (maximal). Wenn Sie diese Daten aufbewahren müssen, können Sie sie als Excel-Datei exportieren und aufbewahren.
  * Als Azure Lighthouse-Benutzer können Sie Informationen über mehrere Mandanten hinweg anzeigen und so eine grenzenlose Überwachung ermöglichen.

* Wenn Sie die operative Aktivitäten langfristig aufbewahren und anzeigen müssen, verwenden Sie **Berichte**. Eine häufige Anforderung an Sicherungsadministratoren besteht darin, basierend auf Daten, die sich über einen längeren Zeitraum erstrecken, Einblicke in Sicherungen zu erhalten. Anwendungsfälle für eine solche Lösung sind:
  * Zuordnung und Vorhersagen des verbrauchten Cloudspeichers.
  * Überwachung von Sicherungen und Wiederherstellungen.
  * Identifizierung wichtiger Trends auf verschiedenen Granularitätsebenen.

* Außerdem:
  * Sie können Daten (z. B. Aufträge, Richtlinien usw.) an den **Log Analytics**-Arbeitsbereich senden. Auf diese Weise können Sie mit den Features von Azure Monitor Logs die Korrelation der Daten mit anderen von Azure Monitor gesammelten Überwachungsdaten ermöglichen, Protokolleinträge von mehreren Azure-Abonnements und -Mandanten an einem Ort für die gemeinsame Analyse konsolidieren, Protokollabfragen verwenden, um komplexe Analysen durchzuführen, und tiefe Einblicke in die Protokolleinträge gewinnen. [Hier erhalten Sie weitere Informationen](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace).
  * Sie können Daten an Event Hub senden, um Einträge z. B. an ein SIEM (Security Information and Event Management) eines Drittanbieters oder eine andere Protokollanalyselösung außerhalb von Azure zu senden. [Hier erhalten Sie weitere Informationen](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs).
  * Sie können Daten an ein Azure Storage Konto senden, wenn Sie die Protokolldaten für Überwachung, statische Analyse oder Sicherungszwecke länger als 90 Tage aufbewahren möchten. Falls Sie Ihre Ereignisse nur maximal 90 Tage lang aufbewahren möchten, müssen Sie keine Archive in einem Speicherkonto einrichten, da Aktivitätsprotokollereignisse in der Azure-Plattform 90 Tage lang aufbewahrt werden. [Weitere Informationen](../azure-monitor/platform/activity-log.md#send-to--azure-storage)

### <a name="alerting"></a>Warnungen

* Warnungen sind in erster Linie eine Möglichkeit, benachrichtigt zu werden, um entsprechende Maßnahmen zu ergreifen. Im Abschnitt „Sicherungswarnungen“ finden Sie die im Azure Backup-Dienst generierten Warnungen.

* Azure Backup stellt einen Benachrichtigungsmechanismus für **integrierte Warnungen** per E-Mail für Fehler, Warnungen und kritische Vorgänge bereit. Sie können einzelne E-Mail-Adressen oder Verteilerlisten angeben, die benachrichtigt werden sollen, wenn eine Warnung generiert wurde. Sie können ebenfalls Benachrichtigungen für jede einzelne Warnung aktivieren oder diese in einer stündlichen Übersicht gruppieren.
  * Diese Warnungen werden vom Dienst definiert und bieten Unterstützung für begrenzte Szenarien: Sicherungs-/Wiederherstellungsfehler, Beenden des Schutzes inklusive Beibehaltung der Daten/Beenden des Schutzes inklusive Löschung usw. [Hier erhalten Sie weitere Informationen](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Wenn ein destruktiver Vorgang wie das Beenden des Schutzes inklusive Löschung der Daten durchgeführt wird, wird eine Warnung ausgelöst und eine E-Mail an den Besitzer des Abonnements sowie an die Administratoren und Co-Admins des Abonnements gesendet, auch wenn die Benachrichtigungen für den Recovery Services-Tresor **nicht** konfiguriert wurden.
  * Bestimmte Workloads können eine hohe Fehlerfrequenz generieren (z. B. SQL Server alle 15 Minuten). Um zu verhindern, mit Warnungen überhäuft zu werden, die bei jedem Fehlerereignis ausgelöst werden, werden die Warnungen konsolidiert. [Hier erhalten Sie weitere Informationen](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Die integrierten Warnungen können nicht angepasst werden und sind auf die im Azure-Portal definierten E-Mails beschränkt.

* Wenn Sie **benutzerdefinierte Warnungen erstellen** müssen (z. B. Benachrichtigungen bei erfolgreichen Aufträgen), verwenden Sie Log Analytics. In Azure Monitor können Sie Ihre eigenen Warnungen in einem Log Analytics-Arbeitsbereich erstellen. Hybridworkloads (DPM/MABS) können auch Daten an LA senden und LA verwenden, um gemeinsame Warnungen für alle von Azure Backup unterstützten Workloads bereitzustellen.

* Sie können Benachrichtigungen auch über integrierte **Aktivitätsprotokolle** für Recovery Services-Tresore erhalten. Diese Vorgehensweise unterstützt jedoch begrenzte Szenarien und eignet sich nicht für Vorgänge wie geplante Sicherungen, die sich besser mit Ressourcenprotokollen als mit Aktivitätsprotokollen vereinbaren lassen. Weitere Informationen zu diesen Einschränkungen und darüber, wie Sie einen Log Analytics-Arbeitsbereich für die ordnungsgemäße Überwachung und Warnungserstellung für alle Ihre durch Azure Backup geschützten Workloads verwenden, finden Sie in [diesem Artikel](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Nächste Schritte

Wir empfehlen Ihnen, die folgenden Artikel als Ausgangspunkt für die Verwendung von Azure Backup zu lesen:

* [Azure Backup: Übersicht](backup-overview.md)
* [Häufig gestellte Fragen](backup-azure-backup-faq.md)
