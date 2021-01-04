---
title: Häufig gestellte Fragen zu Azure AD Domain Services | Microsoft-Dokumentation
description: Lesen und verstehen Sie einige der häufig gestellten Fragen zur Konfiguration, Verwaltung und Verfügbarkeit für Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: justinha
ms.openlocfilehash: e4e7a64b9f9d7283de728216934854f4ef8f8fd1
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619732"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Häufig gestellte Fragen (FAQs) zu den Azure Active Directory (AD) Domain Services

Auf dieser Seite werden häufig gestellte Fragen zu Azure Active Directory Domain Services beantwortet.

## <a name="configuration"></a>Konfiguration

* [Kann ich mehrere verwaltete Domänen für ein einzelnes Azure AD-Verzeichnis erstellen?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kann ich Azure AD Domain Services in einem klassischen virtuellen Netzwerk aktivieren?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kann ich Azure AD Domain Services in einem virtuellen Azure Resource Manager-Netzwerk aktivieren?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kann ich meine vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk zu einem virtuellen Resource Manager-Netzwerk migrieren?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kann ich Azure AD Domain Services in einem Azure CSP-Abonnement (Cloud Solution Provider) aktivieren?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kann ich Azure AD Domain Services in einem Azure AD-Verbundverzeichnis aktivieren? Ich synchronisiere keine Kennworthashes mit Azure AD. Kann ich Azure AD Domain Services für dieses Verzeichnis aktivieren?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kann ich Azure AD Domain Services in mehreren virtuellen Netzwerken in meinem Abonnement zur Verfügung stellen?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kann ich Azure AD Domain Services mithilfe von PowerShell aktivieren?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kann ich Azure AD Domain Services mithilfe einer Resource Manager-Vorlage aktivieren?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kann ich Domänencontroller zu einer verwalteten Azure AD Domain Services-Domäne hinzufügen?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Können in mein Verzeichnis eingeladene Gastbenutzer Azure AD Domain Services verwenden?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Kann ich eine vorhandene verwaltete Azure AD Domain Services-Domäne in ein anderes Abonnement, eine andere Ressourcengruppe, eine andere Region oder ein anderes virtuelles Netzwerk verschieben?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Enthält Azure AD Domain Services Hochverfügbarkeitsoptionen?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kann ich mehrere verwaltete Domänen für ein einzelnes Azure AD-Verzeichnis erstellen?
Nein. Sie können nur eine einzelne durch Azure AD Domain Services verwaltete Domäne für ein einzelnes Azure AD-Verzeichnis erstellen.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kann ich Azure AD Domain Services in einem klassischen virtuellen Netzwerk aktivieren?
Klassische virtuelle Netzwerke werden für neue Bereitstellungen nicht unterstützt. Vorhandene verwaltete Domänen, die in klassischen virtuellen Netzwerken bereitgestellt wurden, werden bis zu ihrer Einstellung am 1. März 2023 weiterhin unterstützt. Für bestehende Bereitstellungen können Sie [Azure AD Domain Services auch vom klassischen VNET-Modell zu Resource Manager migrieren](migrate-from-classic-vnet.md).

Weitere Informationen finden Sie im [offiziellen Hinweis zur Einstellung](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kann ich Azure AD-Domänendienste in einem virtuellen Azure Resource Manager-Netzwerk aktivieren?
Ja. Azure AD Domain Services können in einem virtuellen Azure Resource Manager-Netzwerk aktiviert werden. Klassische virtuelle Azure-Netzwerke stehen für das Erstellen von verwalteten Domänen nicht mehr zur Verfügung.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kann ich meine vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk zu einem virtuellen Resource Manager-Netzwerk migrieren?
Ja. Weitere Informationen finden Sie unter [Migrieren von Azure AD Domain Services vom klassischen VNET-Modell zu Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kann ich Azure AD Domain Services in einem Azure CSP-Abonnement (Cloud Solution Provider) aktivieren?
Ja. Weitere Informationen finden Sie unter [Aktivieren von Azure AD Domain Services in Azure CSP-Abonnements](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kann ich Azure AD Domain Services in einem Azure AD-Verbundverzeichnis aktivieren? Ich synchronisiere keine Kennworthashes mit Azure AD. Kann ich Azure AD Domain Services für dieses Verzeichnis aktivieren?
Nein. Für Azure AD Domain Services ist der Zugriff auf die Kennworthashes von Benutzerkonten erforderlich, um Benutzer über NTLM oder Kerberos zu authentifizieren. In einem Verbundverzeichnis werden Kennworthashes nicht im Azure AD-Verzeichnis gespeichert. Daher kann Azure AD Domain Services mit diesen Azure AD-Verzeichnissen nicht verwendet werden.

Wenn Sie jedoch Azure AD Connect für die Synchronisierung der Kennworthashes verwenden, können Sie Azure AD Domain Services nutzen, weil die Kennworthashwerte in Azure AD gespeichert werden.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kann ich Azure AD Domain Services in mehreren virtuellen Netzwerken in meinem Abonnement zur Verfügung stellen?
Der Dienst selbst bietet keine direkte Unterstützung für dieses Szenario. Ihre verwaltete Domäne ist jeweils in nur einem virtuellen Netzwerk verfügbar. Sie können jedoch die Konnektivität zwischen mehreren virtuellen Netzwerken zum Bereitstellen von Azure AD Domain Services mit anderen virtuellen Netzwerken konfigurieren. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen virtuellen Netzwerken in Azure mithilfe von VPN-Gateways](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) oder [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kann ich die Azure AD Domain Services mithilfe von PowerShell aktivieren?
Ja. Weitere Informationen finden Sie unter [Aktivieren von Azure AD Domain Services mithilfe von PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kann ich Azure AD Domain Services mithilfe einer Resource Manager-Vorlage aktivieren?
Ja, Sie können eine durch Azure AD Domain Services verwaltete Domäne mithilfe einer Resource Manager-Vorlage erstellen. Vor dem Bereitstellen der Vorlage müssen mithilfe des Azure-Portals oder mithilfe von Azure PowerShell ein Dienstprinzipal und eine Azure AD-Gruppe für die Verwaltung erstellt werden. Weitere Informationen finden Sie unter [Erstellen einer verwalteten Azure AD DS-Domäne mithilfe einer Resource Manager-Vorlage](template-create-instance.md). Wenn Sie eine durch Azure AD Domain Services verwaltete Domäne im Azure-Portal erstellen, steht dort auch eine Option zum Exportieren der Vorlage für die Verwendung mit zusätzlichen Bereitstellungen zur Verfügung.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kann ich Domänencontroller zu einer verwalteten Domäne der Azure AD Domain Services hinzufügen?
Nein. Die von den Azure AD Domain Services bereitgestellte Domäne ist eine verwaltete Domäne. Sie müssen für diese Domäne keine Domänencontroller bereitstellen, konfigurieren oder anderweitig verwalten. Diese Verwaltungsaktivitäten werden von Microsoft als Dienst bereitgestellt. Daher können Sie keine zusätzlichen Domänencontroller (weder mit Lese-/Schreibzugriff noch mit reinem Lesezugriff) für die verwaltete Domäne hinzufügen.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Können in mein Verzeichnis eingeladene Gastbenutzer Azure AD Domain Services verwenden?
Nein. Gastbenutzer, die mit dem [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md)-Einladungsprozess in Ihr Azure AD-Verzeichnis eingeladen wurden, werden in Ihrer verwalteten Azure AD Domain Services-Domäne synchronisiert. Kennwörter für diese Benutzer werden jedoch nicht in Ihrem Azure AD-Verzeichnis gespeichert. Daher gibt es in Azure AD Domain Services keine Möglichkeit zum Synchronisieren von NTLM- und Kerberos-Hashes für diese Benutzer in Ihrer verwalteten Domäne. Diese Benutzer können sich nicht anmelden und auch keine Computer in die verwaltete Domäne einbinden.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kann ich eine vorhandene verwaltete Azure AD Domain Services-Domäne in ein anderes Abonnement, eine andere Ressourcengruppe, eine andere Region oder ein anderes virtuelles Netzwerk verschieben?
Nein. Nach dem Erstellen einer verwalteten Azure AD Domain Services-Domäne können Sie diese nicht in eine andere Ressourcengruppe, ein anderes virtuelles Netzwerk, ein anderes Abonnement usw. verschieben. Wählen Sie beim Bereitstellen der verwalteten Domäne das am besten geeignete Abonnement, die am besten geeignete Ressourcengruppe und Region und das am besten geeignete virtuelle Netzwerk aus.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Enthält Azure AD Domain Services Hochverfügbarkeitsoptionen?

Ja. Jede verwaltete Azure AD Domain Services-Domäne enthält zwei Domänencontroller. Sie verwalten diese Domänencontroller nicht und stellen auch keine Verbindung zu ihnen her. Die Controller sind Bestandteil des verwalteten Diensts. Wenn Sie Azure AD Domain Services in einer Region bereitstellen, die Verfügbarkeitszonen unterstützt, werden die Domänencontroller auf die Zonen verteilt. In Regionen, die keine Verfügbarkeitszonen unterstützen, werden die Domänencontroller auf Verfügbarkeitsgruppen verteilt. Sie haben keine Möglichkeit, diese Verteilung zu konfigurieren oder deren Verwaltung zu steuern. Weitere Informationen finden Sie unter [Verfügbarkeitsoptionen für virtuelle Computer in Azure](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Verwaltung und Betrieb

* [Kann ich über Remotedesktop eine Verbindung mit dem Domänencontroller für meine verwaltete Domäne herstellen?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ich habe Azure AD Domain Services aktiviert. Welches Benutzerkonto verwende ich für den Domänenbeitritt von Computern zu dieser Domäne?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Verfüge ich über Domänenadministratorrechte für die über Azure AD Domain Services bereitgestellte verwaltete Domäne?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kann ich mithilfe von LDAP oder anderen AD-Verwaltungstools Gruppenmitgliedschaften in verwalteten Domänen ändern?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Wie lange dauert es, bis Änderungen an meinem Azure AD-Verzeichnis in meiner verwalteten Domäne angezeigt werden?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kann ich das Schema der über Azure AD Domain Services bereitgestellten verwalteten Domäne erweitern?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kann ich DNS-Einträge in meiner verwalteten Domäne ändern oder hinzufügen?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Was ist die Richtlinie für die Kennwortgültigkeitsdauer in einer verwalteten Domäne?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Bietet Azure AD Domain Services Schutz durch Sperrung von AD-Konten?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Kann ich in Azure AD Domain Services ein verteiltes Dateisystem (Distributed File System, DFS) und Replikation konfigurieren?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Wie wird Windows Update in Azure AD Domain Services angewandt?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kann ich über Remotedesktop eine Verbindung zum Domänencontroller für meine verwaltete Domäne herstellen?
Nein. Sie besitzen keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Mitglieder der *Administratorengruppe für Azure AD-Domänencontroller* können die verwaltete Domäne mithilfe von AD-Verwaltungstools wie dem Active Directory-Verwaltungscenter (Active Directory-Administration Center, ADAC) oder AD-PowerShell verwalten. Diese Tools werden mithilfe der *Remoteserver-Verwaltungstools* auf einem Windows-Server installiert, der der verwalteten Domäne beigetreten ist. Weitere Informationen finden Sie unter [Erstellen einer Verwaltungs-VM zum Konfigurieren und Verwalten einer verwalteten Azure AD Domain Services-Domäne](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ich habe Azure AD Domain Services aktiviert. Welches Benutzerkonto verwende ich für den Domänenbeitritt von Computern zu dieser Domäne?
Jedes Benutzerkonto, das Teil der verwalteten Domäne ist, kann einem virtuellen Computer beitreten. Mitglieder der Gruppe *AAD DC-Administratoren* erhalten Remotedesktopzugriff auf Computer, die der verwalteten Domäne beigetreten sind.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Verfüge ich über Domänenadministratorrechte für die über Azure AD Domain Services bereitgestellte verwaltete Domäne?
Nein. Ihnen werden keine Administratorrechte für die verwaltete Domäne gewährt. Die Berechtigungen für *Domänenadministrator* und *Unternehmensadministrator* stehen innerhalb der Domäne nicht zur Verfügung. Mitglieder von Domänenadministrator- oder Unternehmensadministratorengruppen in Ihrem Active Directory-Verzeichnis erhalten ebenfalls keine Domänen-/Unternehmensadministratorberechtigungen in der verwalteten Domäne.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kann ich mithilfe von LDAP oder anderen AD-Verwaltungstools Gruppenmitgliedschaften in verwalteten Domänen ändern?
Benutzer und Gruppen, die von Azure Active Directory mit Azure AD Domain Services synchronisiert werden, können nicht geändert werden, da sie ihren Ursprung in Azure Active Directory haben. Alle Benutzer oder Gruppen, die ihren Ursprung in der verwalteten Domäne haben, können geändert werden.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Wie lange dauert es, bis Änderungen an meinem Azure AD-Verzeichnis in meiner verwalteten Domäne angezeigt werden?
Sowohl über die Benutzeroberfläche von Azure AD als auch über PowerShell vorgenommene Änderungen an Ihrem Azure AD-Verzeichnis werden automatisch mit Ihrer verwalteten Domäne synchronisiert. Diese Synchronisation erfolgt im Hintergrund. Es gibt keinen definierten Zeitraum für die Synchronisierung, um alle Objektänderungen abzuschließen.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kann ich das Schema der über Azure AD Domain Services bereitgestellten verwalteten Domäne erweitern?
Nein. Das Schema für die verwaltete Domäne wird von Microsoft verwaltet. Schemaerweiterungen werden von Azure AD Domain Services nicht unterstützt.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kann ich DNS-Einträge in meiner verwalteten Domäne ändern oder hinzufügen?
Ja. Mitgliedern der Gruppe *AAD DC-Administratoren* werden *DNS-Administratorrechte* gewährt, damit sie DNS-Einträge in der verwalteten Domäne ändern können. Diese Benutzer können die DNS-Manager-Konsole auf einem der verwalteten Domäne beigetretenen Windows Server-Computer verwenden, um DNS zu verwalten. Um die DNS-Manager-Konsole zu verwenden, installieren Sie die *DNS-Servertools*, die zum optionalen Feature der *Remoteserver-Verwaltungstools* auf dem Server gehören. Weitere Informationen finden Sie unter [Verwalten von DNS in einer durch Azure AD Domain Services verwalteten Domäne](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Was ist die Richtlinie für die Kennwortgültigkeitsdauer in einer verwalteten Domäne?
Die Standardlebensdauer von Kennwörtern in einer verwalteten Azure AD Domain Services-Domäne beträgt 90 Tage. Diese Kennwortgültigkeitsdauer wird nicht mit der in Azure AD konfigurierten Kennwortgültigkeitsdauer synchronisiert. Daher können Kennwörter von Benutzern in Ihrer verwalteten Domäne ablaufen, während sie in Azure AD weiterhin gültig sind. In solchen Szenarien müssen Benutzer ihr Kennwort in Azure AD ändern, und das neue Kennwort wird mit Ihrer verwalteten Domäne synchronisiert. Wenn Sie die standardmäßige Kennwortgültigkeitsdauer in einer verwalteten Domäne ändern möchten, können Sie [benutzerdefinierte Kennwortrichtlinien erstellen und konfigurieren](password-policy.md).

Außerdem wird die Azure AD-Kennwortrichtlinie für *DisablePasswordExpiration* mit einer verwalteten Domäne synchronisiert. Wenn Sie in Azure AD *DisablePasswordExpiration* für einen Benutzer anwenden, wird *DONT_EXPIRE_PASSWORD* auf den Wert *UserAccountControl* für den synchronisierten Benutzer in der verwalteten Domäne angewendet.

Wenn der Benutzer sein Kennwort in Azure AD zurücksetzt, wird das Attribut *forceChangePasswordNextSignIn=True* angewendet. Eine verwaltete Domäne synchronisiert dieses Attribut von Azure AD. Wenn die verwaltete Domäne erkennt, dass *forceChangePasswordNextSignIn=True* für einen synchronisierten Benutzer von Azure AD festgelegt wurde, wird das Attribut *pwdLastSet* in der verwalteten Domäne auf *0* gesetzt, wodurch das aktuell festgelegte Kennwort ungültig wird.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Bietet Azure AD Domain Services Schutz durch Sperrung von AD-Konten?
Ja. Wenn innerhalb von 2 Minuten fünf erfolglose Kennworteingaben in der verwalteten Domäne vorgenommen werden, wird das Benutzerkonto für 30 Minuten gesperrt. Nach 30 Minuten wird das Benutzerkonto automatisch entsperrt. Ungültige Kennworteingaben in der verwalteten Domäne sperren das Benutzerkonto in Azure AD nicht. Das Benutzerkonto wird nur in Ihrer verwalteten Azure AD Domain Services-Domäne gesperrt. Weitere Informationen finden Sie unter [Kennwort- und Kontosperrungsrichtlinien in verwalteten Domänen](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Kann ich in Azure AD Domain Services ein verteiltes Dateisystem (Distributed File System, DFS) und Replikation konfigurieren?
Nein. Ein verteiltes Dateisystem (DFS) und die Replikation sind bei Verwendung von Azure AD Domain Services nicht verfügbar.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Wie wird Windows Update in Azure AD Domain Services angewandt?
Die erforderlichen Windows-Updates werden von Domänencontrollern in einer verwalteten Domäne automatisch angewandt. Sie müssen keine Konfigurations- oder Verwaltungsaufgaben ausführen. Achten Sie darauf, keine Regeln für Netzwerksicherheitsgruppen zu erstellen, die ausgehenden Datenverkehr an Windows Update blockieren. Bei eigenen virtuellen Computern, die in die verwaltete Domäne eingebunden wurden, sind Sie für das Konfigurieren und Anwenden aller erforderlichen Betriebssystem- und Anwendungsupdates verantwortlich.

## <a name="billing-and-availability"></a>Abrechnung und Verfügbarkeit

* [Ist Azure AD Domain Services ein kostenpflichtiger Dienst?](#is-azure-ad-domain-services-a-paid-service)
* [Gibt es eine kostenlose Testversion des Diensts?](#is-there-a-free-trial-for-the-service)
* [Kann ich eine durch Azure AD Domain Services verwaltete Domäne anhalten?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kann ich für die Notfallwiederherstellung ein Failover von Azure AD Domain Services in eine andere Region durchführen?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kann ich Azure AD Domain Services als Teil der Enterprise Mobility Suite (EMS) erwerben? Benötige ich Azure AD Premium, um Azure AD Domain Services zu verwenden?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In welchen Azure-Regionen ist der Dienst verfügbar?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Was sind die Azure AD Domain Services?
Ja. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Gibt es eine kostenlose Testversion des Diensts?
Azure AD Domain Services ist in der kostenlosen Testversion von Azure enthalten. Sie können sich für eine [kostenlose einmonatige Testversion von Azure](https://azure.microsoft.com/pricing/free-trial/)registrieren.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kann ich eine durch Azure AD Domain Services verwaltete Domäne aussetzen?
Nein. Sobald Sie eine durch Azure AD Domain Services verwaltete Domäne aktiviert haben, ist der Dienst in dem ausgewählten virtuellen Netzwerk verfügbar, bis Sie die verwaltete Domäne löschen. Es gibt keine Möglichkeit, den Dienst anzuhalten. Die Abrechnung erfolgt weiterhin auf Stundenbasis, bis Sie die verwaltete Domäne löschen.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kann ich für die Notfallwiederherstellung ein Failover von Azure AD Domain Services in eine andere Region durchführen?
Nein. Azure AD Domain Services bietet zurzeit kein georedundantes Bereitstellungsmodell. Es ist auf ein einzelnes virtuelles Netzwerk in einer Azure-Region beschränkt. Wenn Sie mehrere Azure-Regionen verwenden möchten, müssen Sie Ihre Active Directory-Domänencontroller auf Azure IaaS-VMs ausführen. Anleitungen zur Architektur finden Sie unter [Erweitern Ihrer lokalen Active Directory-Domäne auf Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kann ich die Azure AD Domain Services als Teil der Enterprise Mobility Suite (EMS) erwerben? Benötige ich Azure AD Premium, um Azure AD-Domänendienste zu verwenden?
Nein. Azure AD Domain Services ist ein Azure-Dienst mit nutzungsbasierter Bezahlung und nicht Teil von EMS. Azure AD Domain Services kann mit allen Editionen von Azure AD (Free und Premium) verwendet werden. Die Abrechnung erfolgt auf Stundenbasis nach Verbrauch.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welchen Azure-Regionen ist der Dienst verfügbar?
Eine Liste der Azure-Regionen, in denen Azure AD-Domänendienste verfügbar ist, finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services/) .

## <a name="troubleshooting"></a>Problembehandlung

Im [Leitfaden zur Problembehandlung](troubleshoot.md) finden Sie Lösungen für häufig auftretende Probleme beim Konfigurieren oder Verwalten der Azure AD Domain Services.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure AD Domain Services finden Sie unter [Was ist Azure Active Directory Domain Services?](overview.md).

Informationen für den Einstieg finden Sie unter [Erstellen und Konfigurieren einer verwaltete Azure Active Directory Domain Services-Domäne](tutorial-create-instance.md).
