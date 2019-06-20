---
title: 'Azure Active Directory Domain Services: Häufig gestellte Fragen | Microsoft Docs'
description: Häufig gestellte Fragen zu den Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 89930bcc4f49a406586c2770f65cc65f81387302
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245325"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Häufig gestellte Fragen (FAQs)
Auf dieser Seite werden häufig gestellte Fragen zu Azure Active Directory Domain Services beantwortet. Die Seite wird bei Bedarf aktualisiert.

## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung
Im [Leitfaden zur Problembehandlung](troubleshoot.md) finden Sie Lösungen für häufig auftretende Probleme beim Konfigurieren oder Verwalten der Azure AD Domain Services.

## <a name="configuration"></a>Konfiguration
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kann ich mehrere verwaltete Domänen für ein einzelnes Azure AD-Verzeichnis erstellen?
 Nein. Sie können nur eine einzelne durch Azure AD Domain Services verwaltete Domäne für ein einzelnes Azure AD-Verzeichnis erstellen.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kann ich Azure AD-Domänendienste in einem virtuellen Azure Resource Manager-Netzwerk aktivieren?
Ja. Azure AD Domain Services können in einem virtuellen Azure Resource Manager-Netzwerk aktiviert werden. Klassische virtuelle Azure-Netzwerke werden zum Erstellen von neuen verwalteten Domänen nicht mehr unterstützt.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kann ich meine vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk zu einem virtuellen Resource Manager-Netzwerk migrieren?
Derzeit ist dies nicht möglich. Microsoft wird einen Mechanismus bereitstellen, mit dem Sie Ihre vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk zu einem virtuellen Resource Manager-Netzwerk migrieren können.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kann ich Azure AD Domain Services in einem Azure CSP-Abonnement (Cloud Solution Provider) aktivieren?
Ja. Erfahren Sie, wie Sie [Azure AD Domain Services in Azure CSP-Abonnements](csp.md) aktivieren können.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kann ich Azure AD Domain Services in einem Azure AD-Verbundverzeichnis aktivieren? Ich synchronisiere keine Kennworthashes mit Azure AD. Kann ich Azure AD Domain Services für dieses Verzeichnis aktivieren?
 Nein. Für Azure AD Domain Services ist der Zugriff auf die Kennworthashes von Benutzerkonten erforderlich, um Benutzer über NTLM oder Kerberos zu authentifizieren. In einem Verbundverzeichnis werden Kennworthashes im Azure AD-Verzeichnis nicht gespeichert. Daher kann Azure AD Domain Services mit diesen Azure AD-Verzeichnissen nicht verwendet werden.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kann ich Azure AD Domain Services in mehreren virtuellen Netzwerken in meinem Abonnement zur Verfügung stellen?
Der Dienst selbst bietet keine direkte Unterstützung für dieses Szenario. Ihre verwaltete Domäne ist jeweils in nur einem virtuellen Netzwerk verfügbar. Sie können jedoch die Konnektivität zwischen mehreren virtuellen Netzwerken zum Bereitstellen von Azure AD Domain Services mit anderen virtuellen Netzwerken konfigurieren. Erfahren Sie, wie Sie eine [Verbindung zu virtuellen Netzwerken in Azure herstellen](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kann ich die Azure AD Domain Services mithilfe von PowerShell aktivieren?
Ja. Informationen zum [Aktivieren von Azure AD Domain Services mithilfe von PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kann ich Azure AD Domain Services mithilfe einer Resource Manager-Vorlage aktivieren?
Nein. Derzeit ist es nicht möglich, Azure AD Domain Services mithilfe einer Vorlage zu aktivieren. Verwenden Sie stattdessen PowerShell. Informationen hierzu finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kann ich Domänencontroller zu einer verwalteten Domäne der Azure AD Domain Services hinzufügen?
 Nein. Die von den Azure AD Domain Services bereitgestellte Domäne ist eine verwaltete Domäne. Die Domänencontroller für diese Domäne müssen nicht bereitgestellt, konfiguriert oder anderweitig verwaltet werden – diese Verwaltungsschritte werden als Dienst von Microsoft bereitgestellt. Daher können Sie keine zusätzlichen Domänencontroller (weder mit Lese-/Schreibzugriff noch mit reinem Lesezugriff) für die verwaltete Domäne hinzufügen.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Können in mein Verzeichnis eingeladene Gastbenutzer Azure AD Domain Services verwenden?
 Nein. Gastbenutzer, die mit dem [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)-Einladungsprozess in Ihr Azure AD-Verzeichnis eingeladen wurden, werden in Ihrer verwalteten Azure AD Domain Services-Domäne synchronisiert. Kennwörter für diese Benutzer werden jedoch nicht in Ihrem Azure AD-Verzeichnis gespeichert. Daher gibt es in Azure AD Domain Services keine Möglichkeit zum Synchronisieren von NTLM- und Kerberos-Hashes für diese Benutzer in Ihrer verwalteten Domäne. Dies bedeutet wiederum, dass sich solche Benutzer nicht bei der verwalteten Domäne anmelden oder Computer darin einbinden können.

## <a name="administration-and-operations"></a>Verwaltung und Betrieb
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kann ich über Remotedesktop eine Verbindung zum Domänencontroller für meine verwaltete Domäne herstellen?
 Nein. Sie besitzen keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Mitglieder der Administratorengruppe für Azure AD-Domänencontroller können die verwaltete Domäne mithilfe von AD-Verwaltungstools wie dem Active Directory-Verwaltungscenter (Active Directory-Administration Center, ADAC) oder AD PowerShell verwalten. Diese Tools werden mithilfe der Remoteserver-Verwaltungstools (Remote Server Administration Tools, RSAT) auf einem Windows-Server installiert, der der verwalteten Domäne beigetreten ist.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ich habe die Azure AD Domain Services aktiviert. Welches Benutzerkonto verwende ich für den Domänenbeitritt von Computern zu dieser Domäne?
Mitglieder der administrativen Gruppe „AAD DC Administrators“ können Computer in die Domäne einbinden. Darüber hinaus erhalten Mitglieder dieser Gruppe Remotedesktopzugriff auf Computer, die der Domäne beigetreten sind.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Verfüge ich über Domänenadministratorrechte für die über Azure AD Domain Services bereitgestellte verwaltete Domäne?
 Nein. Ihnen werden keine Administratorrechte für die verwaltete Domäne gewährt. Die Berechtigungen für „Domänenadministrator“ und „Unternehmensadministrator“ stehen innerhalb der Domäne nicht zur Verfügung. Mitglieder von Domänenadministrator- oder Unternehmensadministratorgruppen in Ihrem Active Directory-Verzeichnis erhalten ebenfalls keine Domänen-/Unternehmensadministratorberechtigungen in der verwalteten Domäne.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kann ich mithilfe von LDAP oder anderen AD-Verwaltungstools Gruppenmitgliedschaften in verwalteten Domänen ändern?
 Nein. Gruppenmitgliedschaften können nicht in Domänen geändert werden, die von den Azure AD Domain Services verwaltet werden. Das gleiche gilt für Benutzerattribute. Sie können jedoch in Azure AD oder in Ihrer lokalen Domäne Gruppenmitgliedschaften und Benutzerattribute ändern. Solche Änderungen werden automatisch mit den Azure AD Domain Services synchronisiert.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Wie lange dauert es, bis Änderungen an meinem Azure AD-Verzeichnis in meiner verwalteten Domäne angezeigt werden?
Sowohl über die Benutzeroberfläche von Azure AD als auch über PowerShell vorgenommene Änderungen an Ihrem Azure AD-Verzeichnis werden mit Ihrer verwalteten Domäne synchronisiert. Diese Synchronisation erfolgt im Hintergrund. Nach Abschluss der Erstsynchronisierung dauert es in der Regel ca. 20 Minuten bis Änderungen in Azure AD in Ihrer verwalteten Domäne sichtbar werden.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kann ich das Schema der über Azure AD Domain Services bereitgestellten verwalteten Domäne erweitern?
 Nein. Das Schema für die verwaltete Domäne wird von Microsoft verwaltet. Schemaerweiterungen werden von den Azure AD Domain Services nicht unterstützt.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kann ich DNS-Einträge in meiner verwalteten Domäne ändern oder hinzufügen?
Ja. Mitgliedern der Gruppe „AAD DC Administrators“ werden DNS-Administratorrechte gewährt, damit sie DNS-Einträge in der verwalteten Domäne ändern können. Sie können die DNS-Manager-Konsole auf einem der verwalteten Domäne beigetretenen Windows Server-Computer verwenden, um DNS zu verwalten. Um die DNS-Manager-Konsole zu verwenden, installieren Sie die DNS-Servertools, die zum optionalen Feature der Remoteserver-Verwaltungstools auf dem Server gehören. Weitere Informationen zu [Hilfsprogrammen zur Verwaltung, Überwachung und Problembehandlung von DNS](https://technet.microsoft.com/library/cc753579.aspx) finden Sie in TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Was ist die Richtlinie für die Kennwortgültigkeitsdauer in einer verwalteten Domäne?
Die Standardlebensdauer von Kennwörtern in einer verwalteten Azure AD Domain Services-Domäne beträgt 90 Tage. Diese Kennwortgültigkeitsdauer wird nicht mit der in Azure AD konfigurierten Kennwortgültigkeitsdauer synchronisiert. Daher können Kennwörter von Benutzern in Ihrer verwalteten Domäne ablaufen, während sie in Azure AD weiterhin gültig sind. In solchen Szenarien müssen Benutzer ihr Kennwort in Azure AD ändern, und das neue Kennwort wird mit Ihrer verwalteten Domäne synchronisiert. Des Weiteren werden die Attribute „password-does-not-expire“ und „user-must-change-password-at-next-logon“ für Benutzerkonten nicht mit Ihrer verwalteten Domäne synchronisiert.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Bietet Azure AD Domain Services Schutz durch Sperrung von AD-Konten?
Ja. Wenn innerhalb von 2 Minuten fünf erfolglose Kennworteingaben in der verwalteten Domäne vorgenommen werden, wird das Benutzerkonto für 30 Minuten gesperrt. Nach 30 Minuten wird das Benutzerkonto automatisch entsperrt. Ungültige Kennworteingaben in der verwalteten Domäne sperren das Benutzerkonto in Azure AD nicht. Das Benutzerkonto wird nur in Ihrer verwalteten Azure AD Domain Services-Domäne gesperrt.

## <a name="billing-and-availability"></a>Abrechnung und Verfügbarkeit
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Was sind die Azure AD Domain Services?
Ja. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Gibt es eine kostenlose Testversion des Diensts?
Dieser Dienst ist in der kostenlosen Testversion von Azure enthalten. Sie können sich für eine [kostenlose einmonatige Testversion von Azure](https://azure.microsoft.com/pricing/free-trial/)registrieren.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kann ich eine durch Azure AD Domain Services verwaltete Domäne aussetzen? 
 Nein. Sobald Sie eine durch Azure AD Domain Services verwaltete Domäne aktiviert haben, ist der Dienst in dem ausgewählten virtuellen Netzwerk verfügbar, bis Sie die verwaltete Domäne deaktivieren oder löschen. Es gibt keine Möglichkeit, den Dienst auszusetzen. Die Abrechnung erfolgt weiterhin auf Stundenbasis, bis Sie die verwaltete Domäne löschen.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kann ich ein Failover von Azure AD Domain Services in eine andere Region für ein Ereignis der Notfallwiederherstellung ausführen?
 Nein.  Azure Active Directory Domain Services bietet zurzeit kein georedundantes Bereitstellungsmodell. Es ist auf ein einzelnes virtuelles Netzwerk in einer Azure-Region beschränkt. Wenn Sie mehrere Azure-Regionen verwenden möchten, müssen Sie Ihre Active Directory-Domänencontroller auf Azure IaaS-VMs ausführen.  Anleitungen zu verschiedenen Architekturen finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kann ich die Azure AD Domain Services als Teil der Enterprise Mobility Suite (EMS) erwerben? Benötige ich Azure AD Premium, um Azure AD-Domänendienste zu verwenden?
 Nein. Azure AD-Domänendienste ist ein Azure-Dienst mit nutzungsbasierter Bezahlung und nicht Teil von EMS. Azure AD Domain Services kann mit allen Editionen von Azure AD verwendet werden kann (Free, Basic und Premium). Die Abrechnung erfolgt auf Stundenbasis, je nach Verwendung.

### <a name="what-azure-regions-is-the-service-available-in"></a>In welchen Azure-Regionen ist der Dienst verfügbar?
Eine Liste der Azure-Regionen, in denen Azure AD-Domänendienste verfügbar ist, finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services/) .
