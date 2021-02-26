---
title: Schützen von Microsoft 365 vor lokalen Angriffen
description: Leitfaden zum Sicherstellen, dass lokale Angriffe sich nicht auf Microsoft 365 auswirken
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e2e87196f9d4d38743847ee68983216b8790e0b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257269"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Schützen von Microsoft 365 vor lokalen Angriffen

Viele Kunden verbinden ihre privaten Unternehmensnetzwerke mit Microsoft 365, um Vorteile für ihre Benutzer, Geräte und Anweisungen nutzbar zu machen. Es gibt jedoch viele gut dokumentierte Möglichkeiten, wie diese privaten Netzwerke kompromittiert werden können. Da Microsoft 365 für viele Organisationen als eine Art „Nervensystem“ fungiert, ist es wichtig, es vor den Folgen von Schäden an der lokalen Infrastruktur zu schützen.

In diesem Artikel erfahren Sie, wie Sie Ihre Systeme so konfigurieren, dass Ihre Microsoft 365-Cloudumgebung vor lokalen Gefährdungen geschützt ist. Wir konzentrieren uns hauptsächlich auf Folgendes: 

- Konfigurationseinstellungen für Azure Active Directory-Mandanten (Azure AD)
- Sicheres Verbinden von Azure AD-Mandanten mit lokalen Systemen
- Kompromisse bei der Ausführung Ihrer Systeme, damit Ihre Cloudsysteme vor lokalen Risiken geschützt sind

Es wird dringend empfohlen, diesen Leitfaden zu implementieren, um Ihre Microsoft 365 Cloud-Umgebung zu schützen.
> [!NOTE]
> Dieser Artikel wurde ursprünglich als Blogbeitrag veröffentlicht. Er wurde aus Gründen der Langlebigkeit und Wartung an die neue Stelle verschoben.
>
> Verwenden Sie die PDF-Druckfunktion Ihres Browsers, um eine Offlineversion dieses Artikels zu erstellen. Prüfen Sie diese Seite häufig auf Aktualisierungen.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primäre Angriffsvektoren von kompromittierten lokalen Umgebungen


Ihre Microsoft 365 Cloud-Umgebung profitiert von einer umfassenden Überwachungs- und Sicherheitsinfrastruktur. Mit einer Kombination aus maschinellem Lernen und menschlicher Intelligenz untersucht Microsoft 365 den weltweiten Datenverkehr. Auf diese Weise können Angriffe schnell erkannt werden, sodass Sie nahezu in Echtzeit Ihre Konfiguration anpassen können. 

In Hybridbereitstellungen, die eine lokale Infrastruktur mit Microsoft 365 verbinden, delegieren viele Organisationen die Vertrauensstellung an lokale Komponenten, sofern es kritische Entscheidungen zu Authentifizierung und der Verwaltung des Verzeichnisobjektstatus angeht.
Wenn die lokale Umgebung kompromittiert wird, führen diese Vertrauensbeziehungen leider dazu, dass Angreifer die Möglichkeit haben, Ihre Microsoft 365-Umgebung zu kompromittieren.

Die zwei primären Angriffsvektoren sind *Verbundvertrauensbeziehungen* und *Kontosynchronisierung.* Beide Vektoren können einem Angreifer Administratorzugriff auf Ihre Cloud ermöglichen.

* **Verbundvertrauensbeziehungen**, wie etwa die SAML-Authentifizierung, werden für die Authentifizierung bei Microsoft 365 mithilfe Ihrer lokalen Identitätsinfrastruktur verwendet. Wenn ein SAML-Tokensignaturzertifikat kompromittiert ist, kann jeder Benutzer mit diesem Zertifikat die Identität eines beliebigen Benutzers in Ihrer Cloud annehmen. *Es wird empfohlen, Verbundvertrauensbeziehungen für die Authentifizierung bei Microsoft 365 nach Möglichkeit zu deaktivieren.*

* **Kontosynchronisierung** kann verwendet werden, um privilegierte Benutzer (einschließlich ihrer Anmeldeinformationen) oder Gruppen zu ändern, die in Microsoft 365 Administratorberechtigungen haben. *Es empfiehlt sich, darauf zu achten, dass synchronisierte Objekte in Microsoft 365 keine über Benutzer hinausgehenden Rechte haben*, entweder direkt oder über ihre Aufnahme in vertrauenswürdige Rollen oder Gruppen. Stellen Sie sicher, dass diese Objekte keine direkte oder geschachtelte Zuweisung in vertrauenswürdigen Cloudrollen oder Gruppen aufweisen.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Schützen von Microsoft 365 vor Beschädigung durch lokale Angriffe


Um den oben beschriebenen Angriffsvektoren Rechnung zu tragen, empfiehlt es sich, die im folgenden Diagramm dargestellten Prinzipien zu befolgen:

![Referenzarchitektur zum Schutz von Microsoft 365](media/protect-m365/protect-m365-principles.png)

1. **Isolieren Sie Ihre Microsoft 365-Administratorkonten vollständig.** Auf sie sollte Folgendes zutreffen:

    * Sie werden in Azure AD verwaltet.

     * Sie werden über die mehrstufige Authentifizierung authentifiziert.

     *  Sie sind durch bedingten Azure AD-Zugriff geschützt.

     *  Der Zugriff erfolgt nur über verwaltete Azure-Arbeitsstationen.

    Diese Administratorkonten sind Konten mit eingeschränktem Anwendungsbereich. *In Microsoft 365 sollten keine lokalen Konten mit Administratorrechten vorhanden sein.* 

    Weitere Informationen finden Sie in der [Übersicht der Administratorrollen in Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide). Lesen Sie außerdem [Rollen für Microsoft 365 in Azure AD](../roles/m365-workload-docs.md).

1. **Verwalten von Geräten aus Microsoft 365.** Verwenden Sie den Beitritt zu Azure AD und die cloudbasierte mobile Geräteverwaltung (Mobile Device Management, MDM), um Abhängigkeiten von Ihrer lokalen Infrastruktur für die Geräteverwaltung zu beseitigen. Diese Abhängigkeiten können Geräte- und Sicherheitskontrollen gefährden.

1. **Stellen Sie sicher, dass kein lokales Konto über erhöhte Rechte für Microsoft 365 verfügt.**
    Einige Konten greifen auf lokale Anwendungen zu, für die eine NTLM-, LDAP- oder Kerberos-Authentifizierung erforderlich ist. Diese Konten müssen sich in der lokalen Identitätsinfrastruktur der Organisation befinden. Stellen Sie sicher, dass diese Konten (einschließlich Dienstkonten) nicht in privilegierten Cloudrollen oder Gruppen enthalten sind. Achten Sie außerdem darauf, dass sich Änderungen an diesen Konten nicht auf die Integrität Ihrer Cloudumgebung auswirken können. Privilegierte lokale Software darf privilegierte Konten oder Rollen von Microsoft 365 nicht beeinträchtigen können.

1. **Verwenden Sie Azure AD-Cloudauthentifizierung**, um Abhängigkeiten von Ihren lokalen Anmeldeinformationen auszuschließen. Verwenden Sie immer starke Authentifizierung, z. B. Windows Hello, FIDO, Microsoft Authenticator oder Azure AD MFA.

## <a name="specific-security-recommendations"></a>Spezifische Sicherheitsempfehlungen


Die folgenden Abschnitte enthalten spezifische Anweisungen zum Implementieren der oben beschriebenen Prinzipien.

### <a name="isolate-privileged-identities"></a>Isolieren privilegierter Identitäten


In Azure AD bilden Benutzer mit privilegierten Rollen, z. B. Administratoren, die Quelle von Vertrauensstellungen zum Erstellen und Verwalten der restlichen Umgebung. Implementieren Sie die folgenden Verfahren, um die Auswirkungen einer Gefährdung zu minimieren.

* Verwenden Sie reine Cloudkonten für privilegierte Rollen in Azure AD und Microsoft 365.

* Stellen Sie [privilegierte Zugriffsgeräte](/security/compass/privileged-access-devices#device-roles-and-profiles) für den privilegierten Zugriff zum Verwalten von Microsoft 365 und Azure AD bereit.

*  Stellen Sie [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) für den Just-In-Time-Zugriff (JIT) auf alle menschlichen Konten bereit, die über privilegierte Rollen verfügen. Erzwingen Sie für die Aktivierung von Rollen starke Authentifizierung.

* Stellen Sie administrative Rollen so bereit, dass sie die [geringsten benötigten Berechtigungen zum Ausführen der erforderlichen Aufgaben](../roles/delegate-by-task.md) umfassen.

* Zum Aktivieren einer umfassenderen Umgebung zur Rollenzuweisung, die Delegierung und mehrere Rollen gleichzeitig umfasst, sollten Sie die Verwendung von Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen in Erwägung ziehen. Diese Gruppen werden zusammen als *Cloudgruppen* bezeichnet. Aktivieren Sie auch die [rollenbasierte Zugriffssteuerung](../roles/groups-assign-role.md). Sie können [Verwaltungseinheiten](../roles/administrative-units.md) verwenden, um den Umfang der Rollen auf einen Teil der Organisation zu beschränken.

* Stellen Sie [Konten für den Notfallzugriff](../roles/security-emergency-access.md) bereit. Verwenden Sie *keine* lokalen Kennworttresore zum Speichern von Anmeldeinformationen.

Weitere Informationen finden Sie unter [Sichern des privilegierten Zugriffs](/security/compass/overview). Lesen Sie außerdem [Sichere Zugriffsmethoden für Administratoren in Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Verwenden von Cloudauthentifizierung 

Anmeldeinformationen stellen einen primären Angriffsvektor dar. Implementieren Sie die folgenden Verfahren, um die Sicherheit der Anmeldeinformationen zu steigern:

* [Stellen Sie die kennwortlose Authentifizierung bereit.](../authentication/howto-authentication-passwordless-deployment.md) Verringern Sie die Verwendung von Kennwörtern so weit wie möglich, indem Sie kennwortlose Anmeldeinformationen bereitstellen. Diese Anmeldeinformationen werden nativ in der Cloud verwaltet und überprüft. Zur Auswahl stehen diese Authentifizierungsmethoden:

   * [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator-App](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2-Sicherheitsschlüssel](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Stellen Sie die mehrstufige Authentifizierung bereit.](../authentication/howto-mfa-getstarted.md) Stellen Sie [mehrere starke Anmeldeinformationen mithilfe von Azure AD MFA](../fundamentals/resilience-in-credentials.md) bereit. Auf diese Weise sind für den Zugriff auf Cloudressourcen Anmeldeinformationen erforderlich, die in Azure AD verwaltet werden, zusätzlich zu einem lokalen Kennwort, das bearbeitet werden kann. Weitere Informationen finden Sie unter [Erzielen von Resilienz für Identitäts- und Zugriffsverwaltung mit Azure Active Directory](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Einschränkungen und Kompromisse

* Für die Kennwortverwaltung für Hybridkonten sind Hybridkomponenten erforderlich, wie etwa Agents für den Kennwortschutz und das Kennwortrückschreiben. Wenn Ihre lokale Infrastruktur beschädigt ist, können Angreifer die Computer steuern, auf denen diese Agents ausgeführt werden. Dieses Sicherheitsrisiko beeinträchtigt die Cloudinfrastruktur nicht. Ihre Cloudkonten schützen diese Komponenten jedoch nicht vor lokalen Kompromittierungen.

*  Lokale Konten, die von Active Directory synchronisiert werden, sind in Azure AD so gekennzeichnet, dass sie nie ablaufen. Diese Einstellung wird in der Regel durch lokale Active Directory-Kennworteinstellungen umgangen. Wenn jedoch die lokale Instanz von Active Directory kompromittiert wurde und die Synchronisierung deaktiviert ist, müssen Sie die Option [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) festlegen, um Kennwortänderungen zu erzwingen.

## <a name="provision-user-access-from-the-cloud"></a>Bereitstellen des Benutzerzugriffs aus der Cloud

Die *Bereitstellung* bezieht sich auf die Erstellung von Benutzerkonten und Gruppen in Anwendungen oder bei Identitätsanbietern.

![Diagramm der Bereitstellungsarchitektur](media/protect-m365/protect-m365-provision.png)

Die folgenden Bereitstellungsmethoden werden empfohlen:

* **Bereitstellung aus HR-Cloud-Apps in Azure AD:** Durch diese Bereitstellungsmethode kann eine lokale Gefährdung isoliert werden, ohne dass Ihr Zyklus aus Einstellungen, Wechseln und Kündigungen aus Ihren Cloud-Apps zur Personalverwaltung zu Azure AD unterbrochen wird.

* **Cloudanwendungen:** Setzen Sie nach Möglichkeit die [Azure AD-App-Bereitstellung](../app-provisioning/user-provisioning.md) anstelle von lokalen Bereitstellungslösungen ein. Diese Methode schützt einige Ihrer SaaS-Apps (Software-as-a-Service) vor der Auswirkung böswilliger Hackerprofile bei lokalen Sicherheitsverletzungen. 

* **Externe Identitäten:** Verwenden Sie [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md).
    Durch diese Methode wird die Abhängigkeit von lokalen Konten für die externe Zusammenarbeit mit Partnern, Kunden und Lieferanten reduziert. Bewerten Sie sorgfältig jeden direkten Verbund mit anderen Identitätsanbietern. Es empfiehlt sich, B2B-Gastkonten in folgender Weise einzuschränken:

   *  Schränken Sie den Gastzugriff auf die Navigation von Gruppen und anderen Eigenschaften im Verzeichnis ein. Verwenden Sie die Einstellungen für externe Zusammenarbeit, um die Fähigkeit von Gästen einzuschränken, Gruppen zu lesen, bei denen sie nicht Mitglied sind. 

    *   Blockieren Sie den Zugriff auf das Azure-Portal. Seltene erforderliche Ausnahmen können jedoch festgelegt werden.  Erstellen Sie eine Richtlinie für bedingten Zugriff, die alle Gäste und externen Benutzer einschließt. [Implementieren Sie dann eine Richtlinie zum Blockieren des Zugriffs.](../../role-based-access-control/conditional-access-azure-management.md) 

* **Getrennte Gesamtstrukturen:** Verwenden Sie die [Azure AD-Cloudbereitstellung](../cloud-provisioning/what-is-cloud-provisioning.md). Diese Methode ermöglicht Ihnen das Herstellen einer Verbindung mit nicht verbundenen Gesamtstrukturen. Dadurch entfällt die Notwendigkeit, gesamtstrukturübergreifende Verbindungen oder Vertrauensstellungen einzurichten, mit denen die Auswirkungen einer lokalen Sicherheitsverletzung ausgeweitet werden können. 
 
### <a name="limitations-and-tradeoffs"></a>Einschränkungen und Kompromisse

Bei der Verwendung zum Bereitstellen von Hybridkonten stützt sich Azure AD aus Cloudsystemen für die Personalverwaltung auf die lokale Synchronisation, um den Datenfluss von Active Directory zu Azure AD durchzuführen. Wenn die Synchronisation unterbrochen wird, stehen keine neuen Mitarbeiterdatensätze in Azure AD zur Verfügung.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Verwenden von Cloudgruppen für Zusammenarbeit und Zugriff

Cloudgruppen ermöglichen es Ihnen, Zusammenarbeit und Zugriff von Ihrer lokalen Infrastruktur zu entkoppeln.

* **Zusammenarbeit:** Verwenden Sie Microsoft 365-Gruppen und Microsoft Teams für die moderne Zusammenarbeit. Setzen Sie lokale Verteilerlisten außer Kraft, und führen Sie ein [Upgrade von Verteilerlisten auf Microsoft 365-Gruppen in Outlook](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide) durch.

* **Zugriff**: Verwenden Sie Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen, um den Zugriff auf Anwendungen in Azure AD zu autorisieren.
* **Office 365-Lizenzierung:** Verwenden Sie die gruppenbasierte Lizenzierung, um Office 365 mithilfe von reinen Cloudgruppen bereitzustellen. Durch diese Methode wird die Steuerung der Gruppenmitgliedschaft von der lokalen Infrastruktur entkoppelt.

Benutzer von Gruppen, die für den Zugriff verwendet werden, sollten als privilegierte Identitäten angesehen werden, um eine Übernahme der Mitgliedschaft bei lokalen Gefährdungen zu vermeiden.
Eine Übernahme schließt die direkte Manipulation der lokalen Gruppenmitgliedschaft oder die Manipulation lokaler Attribute ein, die sich auf die dynamische Gruppenmitgliedschaft in Microsoft 365 auswirken kann.

## <a name="manage-devices-from-the-cloud"></a>Verwalten von Geräten aus der Cloud


Verwenden Sie Azure AD-Funktionen für die sichere Geräteverwaltung.

-   **Verwenden von Windows 10-Arbeitsstationen:** [Stellen Sie Geräte mit Azure AD-Mitgliedschaft](../devices/azureadjoin-plan.md) mithilfe von MDM-Richtlinien bereit. Aktivieren Sie [Windows Autopilot](/mem/autopilot/windows-autopilot), um eine vollständig automatisierte Bereitstellung zu ermöglichen.

    -   Markieren Sie Computer mit Windows 8.1 und früheren Versionen als veraltet.

    -   Stellen Sie keine Computer mit Serverbetriebssystemen als Arbeitsstationen bereit.

    -   Verwenden Sie [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) als Autorisierungsquelle für alle Workloads zur Geräteverwaltung.

-   [**Bereitstellen von Geräten mit privilegiertem Zugriff:** ](/security/compass/privileged-access-devices#device-roles-and-profiles) Verwenden Sie den privilegierten Zugriff zum Verwalten von Microsoft 365 und Azure AD.

## <a name="workloads-applications-and-resources"></a>Workloads, Anwendungen und Ressourcen 

-   **Lokale Systeme für einmaliges Anmelden (Single-Sign-On, SSO)** 

    Markieren Sie jede lokale Verbundinfrastruktur und Webzugriffsverwaltungs-Infrastruktur als veraltet. Konfigurieren Sie Anwendungen für die Verwendung von Azure AD.  

-   **SaaS- und Branchenanwendungen, die moderne Authentifizierungsprotokolle unterstützen** 

    [Verwenden Sie Azure AD für SSO.](../manage-apps/what-is-single-sign-on.md) Je mehr Apps Sie für die Verwendung von Azure AD für die Authentifizierung konfigurieren, desto geringer ist das Risiko bei einer lokalen Gefährdung.


* **Legacyanwendungen** 

   * Sie können für ältere Anwendungen, die keine moderne Authentifizierung unterstützen, Authentifizierung, Autorisierung und Remotezugriff aktivieren. Verwenden Sie den [Azure AD-Anwendungsproxy](../manage-apps/application-proxy.md). Sie können auch über eine Controllerlösung für das Netzwerk oder die Anwendungsbereitstellung mithilfe von [sicheren Hybridzugriffspartner-Integrationen](../manage-apps/secure-hybrid-access.md) aktiviert werden.   

   * Wählen Sie einen VPN-Anbieter aus, der die moderne Authentifizierung unterstützt. Integrieren Sie die kennwortlose Authentifizierung mit Azure AD. Bei einer lokalen Gefährdung können Sie Azure AD verwenden, um den Zugriff durch Deaktivierung des VPN zu deaktivieren oder zu sperren.

*  **Anwendungs- und Workloadserver**

   * Anwendungen oder Ressourcen, die Server erfordern, können zu Azure-IaaS (Infrastructure-as-a-Service) migriert werden. Verwenden Sie [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS), um Vertrauensstellungen und Abhängigkeiten von lokalen Instanzen von Active Directory zu entkoppeln. Zum Erreichen dieser Entkoppelung dürfen virtuelle Netzwerke, die für Azure AD DS verwendet werden, keine Verbindung mit Unternehmensnetzwerken aufweisen.

   * Befolgen Sie den Leitfaden zur [Berechtigungseinstufung](/security/compass/privileged-access-access-model#ADATM_BM). Anwendungsserver gelten in der Regel als Ressourcen der Ebene 1.

## <a name="conditional-access-policies"></a>Richtlinien für bedingten Zugriff

Verwenden Sie den bedingten Zugriff von Azure AD, um Signale zu interpretieren und auf ihrer Grundlage Authentifizierungsentscheidungen zu treffen. Weitere Informationen finden Sie unter [Bereitstellungsplan für bedingten Zugriff](../conditional-access/plan-conditional-access.md).

* Verwenden Sie bedingten Zugriff, um [ältere Authentifizierungsprotokolle](../conditional-access/howto-conditional-access-policy-block-legacy.md) wo immer möglich zu blockieren. Deaktivieren Sie darüber hinaus Legacy-Authentifizierungsprotokolle auf Anwendungsebene mithilfe einer anwendungsspezifischen Konfiguration.

   Weitere Informationen finden Sie unter [Ältere Authentifizierungsprotokolle](../fundamentals/auth-sync-overview.md). Mehr dazu finden Sie in den spezifischen Details für [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) und [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementieren Sie die empfohlenen [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide).

* Wenn Sie eine Version von Azure AD verwenden, die keinen bedingten Zugriff beinhaltet, achten Sie darauf, dass Sie die [Azure AD-Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) verwenden.

   Weitere Informationen zur Lizenzierung von Azure AD-Features finden Sie im [Azure AD-Preisleitfaden](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Überwachen 

Nachdem Sie Ihre Umgebung so konfiguriert haben, dass Ihr Microsoft 365 vor lokaler Gefährdung geschützt ist, [überwachen Sie die Umgebung proaktiv](../reports-monitoring/overview-monitoring.md).
### <a name="scenarios-to-monitor"></a>Szenarien für die Überwachung

Überwachen Sie die folgenden wichtigen Szenarien, zusätzlich zu den Szenarien, die für Ihre Organisation spezifisch sind. Beispielsweise sollten Sie den Zugriff auf unternehmenskritische Anwendungen und Ressourcen proaktiv überwachen.

* **Verdächtige Aktivitäten** 

    Überwachen Sie alle [Azure AD-Risikoereignisse](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) auf verdächtige Aktivitäten. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) ist nativ in das Azure Security Center integriert.

    Definieren Sie die [benannten Standorte](../reports-monitoring/quickstart-configure-named-locations.md) im Netzwerk, um die aufwändige Erkennung anhand von standortbasierten Signalen zu vermeiden. 
*  **UEBA-Warnungen (User and Entity Behavior Analytics)** 

    Verwenden Sie UEBA, um Erkenntnisse aus der Anomalieerkennung zu gewinnen.
    * Microsoft Cloud App Security (MCAS) bietet [UEBA in der Cloud](/cloud-app-security/tutorial-ueba).

    * Sie können das [lokale UEBA aus Azure ATP (Advanced Threat Protection) integrieren](/defender-for-identity/install-step2). MCAS liest Signale aus der Azure AD Identity Protection. 

* **Aktivität bei Konten für den Notfallzugriff** 

    Überwachen Sie jeden Zugriff, bei dem [Konten für den Notfallzugriff](../roles/security-emergency-access.md) verwendet werden. Erstellen Sie Warnungen für Untersuchungen. Diese Überwachung muss Folgendes umfassen: 

   * Anmeldungen. 

   * Verwaltung von Anmeldeinformationen. 

   * Alle Aktualisierungen von Gruppenmitgliedschaften. 

   * Zuweisungen von Anwendungen 
* **Aktivität bei privilegierten Rollen**

    Konfigurieren und überprüfen Sie die [von Azure AD Privileged Identity Management (PIM) generierten Sicherheitswarnungen](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Überwachen Sie die direkte Zuweisung von privilegierten Rollen außerhalb von PIM, indem Sie Warnungen generieren, wenn eine direkte Zuweisung an Benutzer erfolgt.

* **Mandantenweite Konfigurationen in Azure AD**

    Für jede Änderung an mandantenweiten Konfigurationen sollten Warnungen im System generiert werden. Diese Änderungen umfassen u. a. Folgendes:

  *  Aktualisierte benutzerdefinierte Domänen  

  * Azure AD B2B-Änderungen an Positivlisten und Sperrlisten

  * Änderungen an den von Azure AD B2B zugelassenen Identitätsanbietern (SAML-Identitätsanbieter über direkte Verbundanmeldungen oder Anmeldungen in sozialen Netzwerken)  

  * Änderungen an Richtlinien für bedingten Zugriff oder zum Benutzerrisiko 

* **Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory (Azure AD)**
    
   * Neue Anwendungs- oder Dienstprinzipale, die möglicherweise Richtlinien für den bedingten Zugriff benötigen. 

   * Anmeldeinformationen, die Dienstprinzipalen hinzugefügt werden
   * Anwendungszustimmungsaktivitäten. 

* **Benutzerdefinierte Rollen**
   * Aktualisierungen der benutzerdefinierten Rollendefinitionen 

   * Neu erstellte benutzerdefinierte Rollen 

### <a name="log-management"></a>Protokollverwaltung

Definieren Sie eine Strategie für die Protokollspeicherung und -aufbewahrung, und entwerfen und implementieren Sie diese, um ein konsistentes Toolset bereitzustellen. Sie können z. B. SIEM-Systeme (Security Information & Event Management) wie Azure Sentinel, allgemeine Abfragen, Untersuchungen und Forensik-Playbooks in Erwägung ziehen.

* **Azure AD-Protokolle:** Erfassen Sie die generierten Protokolle und Signale durch konsistente Einhaltung der bewährten Methoden für Einstellungen wie Diagnose, Protokollaufbewahrung und SIEM-Erfassung. 

    Die Protokollstrategie muss die folgenden Azure AD-Protokolle beinhalten:
   * Anmeldeaktivität 

   * Überwachungsprotokolle 

   * Risikoereignisse 

    Azure AD bietet [Azure Monitor-Integration](../reports-monitoring/concept-activity-logs-azure-monitor.md) für die Protokolle zu Anmeldeaktivitäten und Überwachungsprotokolle. Risikoereignisse können mithilfe der [Microsoft Graph-API](/graph/api/resources/identityriskevent) erfasst werden. Sie können [Azure AD-Protokolle zu Azure Monitor-Protokollen streamen](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Sicherheitsprotokolle des Betriebssystems bei Hybridinfrastrukturen:** Bei einer Hybrididentitätsinfrastruktur sollten angesichts der Auswirkungen auf die Oberfläche alle Betriebssystemprotokolle archiviert und sorgfältig als System der Ebene 0 überwacht werden. Schließen Sie folgende Elemente ein: 

   *  Azure AD Connect. [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) muss bereitgestellt werden, um die Identitätssynchronisation zu überwachen.

   *  Anwendungsproxy-Agents 


   * Agents für das Kennwortrückschreiben 

   * Kennwortschutzgateway-Computer  

   * Netzwerkrichtlinienserver (NPS) mit der RADIUS-Erweiterung für die mehrstufige Authentifizierung in Azure AD 

## <a name="next-steps"></a>Nächste Schritte
* [Erzielen von Resilienz für Identitäts- und Zugriffsverwaltung mit Azure Active Directory](resilience-overview.md)

* [Sicherer externer Zugriff auf Ressourcen](secure-external-access-resources.md) 
* [Integrieren aller Ihrer Apps in Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)