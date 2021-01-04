---
title: Referenzleitfaden zur Authentifizierungsverwaltung von Azure Active Directory-Vorgänge
description: In diesem Referenzleitfaden zu Vorgängen werden die Überprüfungen und Aktionen beschrieben, die Sie zum Sichern der Authentifizierungsverwaltung vornehmen sollten.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 90e215ea445c8c700e351149e9c7a91d9a595252
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859520"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referenzleitfaden zur Authentifizierungsverwaltung von Azure Active Directory-Vorgänge

In diesem Abschnitt des [Referenzleitfadens zu Azure AD-Vorgängen](active-directory-ops-guide-intro.md) werden die Überprüfungen und Aktionen beschrieben, die Sie zum Sichern und Verwalten Ihrer Anmeldeinformationen, Definieren der Authentifizierungsfunktion, Delegieren von Zuweisungen, Messen der Nutzung und zum Definieren der Zugriffsrichtlinien basierend auf dem Sicherheitsstatus des Unternehmens vornehmen sollten.

> [!NOTE]
> Diese Empfehlungen sind auf dem Stand des Veröffentlichungsdatums, können sich aber im Laufe der Zeit ändern. Organisationen sollten ihre Identitätsmethoden fortlaufend überprüfen, da die Produkte und Dienste von Microsoft mit der Zeit weiterentwickelt werden.

## <a name="key-operational-processes"></a>Wichtige Betriebsabläufe

### <a name="assign-owners-to-key-tasks"></a>Zuweisen von Besitzern zu wichtigen Aufgaben

Für die Verwaltung von Azure Active Directory ist die kontinuierliche Ausführung wichtiger betrieblicher Aufgaben und Prozesse erforderlich, die möglicherweise nicht Teil eines Rolloutprojekts sind. Es ist außerdem wichtig, dass Sie diese Aufgaben einrichten, um Ihre Umgebung zu optimieren. Im Folgenden werden die wichtigen Aufgaben und empfohlene Besitzer für diese aufgeführt:

| Aufgabe | Besitzer |
| :- | :- |
| Verwalten des Lebenszyklus für die SSO-Konfiguration (Single Sign-On, einmaliges Anmelden) in Azure AD | Team für IAM-Vorgänge (Identity & Access Management, Identitäts- und Zugriffsverwaltung) |
| Entwerfen von Richtlinien für bedingten Zugriff auf Azure AD-Anwendungen | Team für die Informationssicherheitsarchitektur |
| Archivieren von Anmeldeaktivitäten in einem SIEM-System | Team für Informationssicherheitvorgänge |
| Archivieren von Risikoereignissen in einem SIEM-System | Team für Informationssicherheitvorgänge |
| Triage und Untersuchung von Sicherheitsberichten | Team für Informationssicherheitvorgänge |
| Triage und Untersuchung von Risikoereignissen | Team für Informationssicherheitvorgänge |
| Triage und Untersuchung von Benutzern, die von Azure AD Identity Protection für Risiko- und Sicherheitsberichte gekennzeichnet wurden | Team für Informationssicherheitvorgänge |

> [!NOTE]
> Für Azure AD Identity Protection ist eine Azure AD Premium P2-Lizenz erforderlich. Die richtige Lizenz für Ihre Anforderungen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

Beim Überprüfen Ihrer Liste stellen Sie ggf. fest, dass Sie entweder einen Besitzer für Aufgaben zuweisen müssen, denen kein Besitzer zugeteilt ist, oder Aufgaben anpassen müssen, deren Besitzer nicht den obigen Empfehlungen entspricht.

#### <a name="owner-recommended-reading"></a>Empfohlene Artikel für Besitzer

- [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Verwaltung von Anmeldeinformationen

### <a name="password-policies"></a>Kennwortrichtlinien

Die sichere Verwaltung von Kennwörtern ist einer der wichtigsten Aspekte der Identitäts- und Zugriffsverwaltung und oft auch das größte Ziel von Angriffen. Azure AD unterstützt mehrere Features, mit denen Angriffe verhindert werden können.

Anhand der folgenden Tabelle können Sie die empfohlene Lösung zur Behebung eines Problems finden:

| Problem | Empfehlung |
| :- | :- |
| Es liegt kein Mechanismus zum Schutz vor schwachen Kennwörtern vor. | Aktivieren Sie die [Self-Service-Kennwortzurücksetzung (SSPR)](../authentication/concept-sspr-howitworks.md) und den [Kennwortschutz](../authentication/concept-password-ban-bad-on-premises.md) von Azure AD. |
| Es liegt kein Mechanismus zur Ermittlung von kompromittierten Kennwörtern vor. | Aktivieren Sie die [Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md), um Informationen zu erhalten. |
| Es werden Active Directory-Verbunddienste (AD FS) verwendet, und eine Migration zu einer verwalteten Authentifizierung ist nicht möglich. | Aktivieren Sie [AD FS Extranet Smart Lockout](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) und/oder [Azure AD Smart Lockout](../authentication/howto-password-smart-lockout.md). |
| Die Kennwortrichtlinie nutzt komplexitätsbasierte Regeln wie Länge, mehrere Zeichensätze oder Ablaufdatum. | Erwägen Sie [von Microsoft empfohlene bewährte Methoden](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf), wechseln Sie von Ihrem Ansatz zur Kennwortverwaltung, und stellen Sie den [Azure AD-Kennwortschutz](../authentication/concept-password-ban-bad.md) bereit. |
| Benutzer sind nicht für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) registriert. | [Registrieren Sie die Sicherheitsinformationen aller Benutzer](../identity-protection/howto-identity-protection-configure-mfa-policy.md), damit diese mit dem Kennwort zum Verifizieren der Benutzeridentität verwendet werden können. |
| Kennwörter werden nicht basierend auf dem Benutzerrisiko gesperrt. | Stellen Sie die [Benutzerrisikorichtlinien von Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) bereit, um Kennwortänderungen bei kompromittierten Anmeldeinformationen mit SSPR zu erzwingen. |
| Es gibt keinen Smart Lockout-Mechanismus für den Schutz vor böswilligen Authentifizierungsanforderungen durch schädliche Akteure, die von identifizierten IP-Adressen stammen. | Stellen Sie die cloudverwaltete Authentifizierung mit Kennworthashsynchronisierung oder [Pass-Through-Authentifizierung](../hybrid/how-to-connect-pta-quick-start.md) bereit. |

#### <a name="password-policies-recommended-reading"></a>Empfohlene Artikel zu Kennwortrichtlinien

- [Azure AD und AD FS – Best Practices: Verteidigung gegen Kennwort-Spray-Angriffe – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Aktivieren der Self-Service-Kennwortzurücksetzung und des Kennwortschutzes

Benutzer, die ihre Kennwörter ändern oder zurücksetzen müssen, sind eine der größten Quellen für Helpdesk-Anrufe. Neben den Kosten, ist die Kennwortänderung ein Tool zum Mindern des Benutzerrisikos und ein wichtiger Schritt zur Verbesserung des Sicherheitsstatus Ihrer Organisation.

Es wird mindestens empfohlen, dass Sie die [Self-Service-Kennwortzurücksetzung](../authentication/concept-sspr-howitworks.md) und den lokalen [Kennwortschutz](../authentication/howto-password-ban-bad-on-premises-deploy.md) von Azure AD bereitstellen, um Folgendes zu erzielen:

- Reduzieren der Helpdesk-Anrufe
- Ersetzen der Verwendung von temporären Kennwörtern
- Ersetzen vorhandener Self-Service-Kennwortverwaltungslösungen, die von lokalen Lösungen abhängig sind
- [Beseitigen schwacher Kennwörter](../authentication/concept-password-ban-bad.md) in Ihrer Organisation

> [!NOTE]
> Organisationen mit einem Azure AD Premium P2-Abonnement wird empfohlen die Self-Service-Kennwortzurücksetzung bereitzustellen und im Rahmen einer [Identity Protection-Richtlinie für das Benutzerrisiko](../identity-protection/howto-identity-protection-configure-risk-policies.md) zu verwenden.

### <a name="strong-credential-management"></a>Verwaltung von sicheren Anmeldeinformationen

Kennwörter allein sind nicht sicher genug, um böswillige Akteure davon abzuhalten, sich Zugriff auf Ihre Umgebung zu verschaffen. Jeder Benutzer mit einem privilegierten Konto sollte zumindest die mehrstufige Authentifizierung nutzen. Im Idealfall sollten Sie die [kombinierte Registrierung](../authentication/concept-registration-mfa-sspr-combined.md) aktivieren und die mehrstufige Authentifizierung sowie die Self-Service-Kennwortzurücksetzung von allen Benutzern erfordern, die die [Funktion für die kombinierte Registrierung](../user-help/security-info-setup-signin.md) nutzen. Es wird empfohlen, dass Sie eventuell eine Strategie zum [Bereitstellen von Resilienz](../authentication/concept-resilient-controls.md) umsetzen, um das Risiko einer Sperrung aufgrund unvorhersehbarer Umstände zu reduzieren.

![Ablauf der Funktion für kombinierte Benutzer](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Lokale Authentifizierungsresilienz für Ausfälle

Abgesehen von der Einfachheit und der Aktivierung der Ermittlung kompromittierter Anmeldeinformationen, bieten die Azure AD-Kennworthashsynchronisierung und Azure AD MFA Benutzern die vorteilhafte Möglichkeit, trotz Ausfällen aufgrund von Cyberangriffen wie [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) auf SaaS-Anwendungen und Microsoft 365 zugreifen zu können. Es ist auch möglich, die Kennworthashsynchronisierung im Zusammenhang mit Verbunddiensten zu aktivieren. Das Aktivieren der Kennworthashsynchronisierung ermöglicht ein Fallback für die Authentifizierung, wenn Verbunddienste nicht verfügbar sind.

Wenn Ihre lokale Organisation nicht über eine Resilienzstrategie für Ausfälle oder über eine Strategie verfügt, die nicht mit Azure AD integriert ist, sollten Sie die Azure AD-Kennworthashsynchronisierung bereitstellen und einen Notfallwiederherstellungsplan definieren, der die Kennworthashsynchronisierung enthält. Indem Sie die Azure AD-Kennworthashsynchronisierung aktivieren, ermöglichen Sie Benutzern die Authentifizierung bei Azure AD, wenn Ihre lokale Active Directory-Lösung nicht verfügbar ist.

![Ablauf der Kennworthashsynchronisierung](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Weitere Informationen zu den verfügbaren Authentifizierungsoptionen finden Sie unter [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Programmgesteuerte Nutzung von Anmeldeinformationen

Azure AD-Skripts mit PowerShell oder Anwendungen, die die Microsoft Graph-API nutzen, erfordern sichere Authentifizierung. Bei unzureichender Verwaltung von Anmeldeinformationen kann die Ausführung dieser Skripts und Tools das Risiko des Diebstahls von Anmeldeinformationen erhöhen. Wenn Sie Skripts oder Anwendungen verwenden, die auf hartcodierten Kennwörtern oder Kennworteingabeaufforderungen basieren, sollten Sie zunächst die Kennwörter in den Konfigurationsdateien oder im Quellcode überprüfen. Ersetzen Sie diese Abhängigkeiten anschließend, und verwenden Sie nach Möglichkeit verwaltete Azure-Identitäten, die integrierte Windows-Authentifizierung oder [Zertifikate](../reports-monitoring/tutorial-access-api-with-certificates.md). Bei Anwendungen, für die die genannten Lösungen nicht möglich sind, sollten Sie [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) in Betracht ziehen.

Wenn Sie feststellen, dass Dienstprinzipale mit Kennwortanmeldeinformationen vorhanden sind, und Sie sich nicht sicher sind, wie diese Kennwortanmeldeinformationen durch Skripts oder Anwendungen geschützt werden, sollten Sie beim Besitzer der Anwendung weitere Informationen zu den Nutzungsmustern erfragen.

Außerdem wird von Microsoft empfohlen, dass Sie Anwendungsbesitzer kontaktieren, um Informationen zu Nutzungsmustern zu erhalten, wenn Dienstprinzipale mit Kennwortanmeldeinformationen vorliegen.

## <a name="authentication-experience"></a>Authentifizierungsvorgang

### <a name="on-premises-authentication"></a>Lokale Authentifizierung

Verbundauthentifizierung mit der integrierten Windows-Authentifizierung oder der verwalteten Authentifizierung mit nahtlosem einmaligen Anmelden mit Kennworthashsynchronisierung oder Pass-Through-Authentifizierung sind am benutzerfreundlichsten, wenn im Unternehmensnetzwerk mit Sichtverbindung mit den lokalen Domänencontrollern gearbeitet wird. Dadurch werden die Komplexität der Eingabeaufforderungen für Anmeldeinformationen und das Risiko reduziert, dass Benutzer auf Phishingangriffe hereinfallen. Wenn Sie bereits eine in der Cloud verwaltete Authentifizierung mit Kennworthashsynchronisierung oder Pass-Through-Authentifizierung verwenden, die Benutzer aber weiterhin ihre Kennwörter bei der lokalen Authentifizierung eingeben müssen, sollten Sie umgehend [nahtloses SSO bereitstellen](../hybrid/how-to-connect-sso.md). Wenn Sie jedoch derzeit im Verbund mit Plänen für eine eventuelle Migration zu einer in der Cloud verwalteten Authentifizierung sind, sollten Sie das nahtlose SSO im Rahmen des Migrationsprojekts implementieren.

### <a name="device-trust-access-policies"></a>Zugriffsrichtlinien für Geräte mit Vertrauensstellung

Ähnlich wie ein Benutzer in Ihrer Organisation ist auch ein Gerät eine zentrale Identität, die Sie schützen möchten. Über die Identität eines Geräts können Sie Ihre Ressourcen jederzeit und von überall aus schützen.  Die Authentifizierung des Geräts und die Rücksichtnahme auf dessen Vertrauenstyps verbessert Ihren Sicherheitsstatus und die Benutzerfreundlichkeit wie folgt:

- Wenn das Gerät über eine Vertrauensstellung verfügt, werden diverse Schwierigkeiten vermieden, beispielsweise mit MFA.
- Der Zugriff über nicht vertrauenswürdige Geräte wird blockiert.
- Windows 10-Geräte erhalten [nahtloses einmaliges Anmelden bei lokalen Ressourcen](../devices/azuread-join-sso.md).

Dafür können Sie Geräteidentitäten mit einer der folgenden Methoden in Azure AD bereitstellen und verwalten:

- Organisationen können [Microsoft Intune](/intune/what-is-intune) verwenden, um das Gerät zu verwalten und Konformitätsrichtlinien zu erzwingen, die Geräteintegrität zu überprüfen und Richtlinien für bedingten Zugriff anhand der Konformität des Geräts festzulegen. Microsoft Intune kann iOS-Geräte, Mac-Computer (per JAMF-Integration), Windows-Computer (nativ per mobile Geräteverwaltung bei Windows 10 und per Co-Verwaltung mit Microsoft Endpoint Configuration Manager) und mobile Android-Geräte verwalten.
- [Azure AD Hybrid Join](../devices/hybrid-azuread-join-managed-domains.md) bietet Verwaltung mit Gruppenrichtlinien oder Microsoft Endpoint Configuration Manager in einer Umgebung mit in einer Azure Active Directory-Domäne eingebundenen Computern. Organisationen können eine verwaltete Umgebung entweder über Kennworthashsynchronisierung oder Pass-Through-Authentifizierung mit nahtlosem SSO bereitstellen. Die Migration Ihrer Geräte zu Azure AD maximiert die Produktivität der Benutzer mithilfe von SSO für Ihre Cloud- und lokalen Ressourcen, während Ihnen gleichzeitig das Sichern Ihres Zugriffs auf diese Ressourcen mithilfe des [bedingten Zugriffs](../conditional-access/overview.md) ermöglicht wird.

Gehen Sie wie folgt vor, falls Sie über in eine Domäne eingebundene Windows-Geräte verfügen, die nicht in der Cloud registriert sind, oder über in eine Domäne eingebundene Windows-Geräte, die in der Cloud registriert sind, aber keine Richtlinien für bedingten Zugriff aufweisen: Registrieren Sie die nicht registrierten Geräte, und verwenden Sie in beiden Fällen in Ihren Richtlinien für bedingten Zugriff [Azure AD Hybrid Join als Kontrolle](../conditional-access/require-managed-devices.md).

![Screenshot: Zuweisung zum Erfordern von Hybridgeräten in Richtlinien für bedingten Zugriff](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Wenn Sie Geräte mit der mobilen Geräteverwaltung oder Microsoft Intune verwalten, aber keine Geräteüberprüfungen in Ihren Richtlinien für bedingten Zugriff verwenden, wird empfohlen, dass Sie [Markieren des Geräts als kompatibel erforderlich](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) in diesen Richtlinien verwenden.

![Screenshot: Zuweisung zum Erfordern der Gerätekonformität in Richtlinien für bedingten Zugriff](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Empfohlene Artikel zu Gerätezugriffsrichtlinien mit Vertrauensstellung

- [Vorgehensweise: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](../devices/hybrid-azuread-join-plan.md)
- [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

Unter Windows 10 ersetzt [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) Kennwörter durch eine sichere zweistufige Authentifizierung auf Computern. Windows Hello for Business bietet eine optimierte MFA-Funktion für Benutzer und reduziert Ihre Abhängigkeit von Kennwörtern. Wenn Sie noch nicht mit der Bereitstellung von Windows 10-Geräten begonnen oder diese nur teilweise bereitgestellt haben, empfehlen wir Ihnen, ein Upgrade auf Windows 10 durchzuführen und [Windows Hello for Business auf allen Geräten zu aktivieren](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization).

Weitere Informationen zur kennwortlosen Authentifizierung finden Sie unter [Eine Welt ohne Kennwörter mit Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Anwendungsauthentifizierung und -zuweisung

### <a name="single-sign-on-for-apps"></a>Einmaliges Anmelden für Apps

Ein standardisiertes Verfahren für das einmalige Anmelden im gesamten Unternehmen ist nicht nur für eine optimale Benutzererfahrung wichtig, sondern auch für das Verringern von Risiken, das Erstellen von Berichten und die Governance. Wenn Sie Anwendungen verwenden, die SSO mit Azure AD unterstützen, die derzeit jedoch zur Verwendung lokaler Konten konfiguriert sind, sollten Sie diese Anwendungen neu konfigurieren, damit diese SSO mit Azure AD verwenden. Ebenso sollten Sie diese Anwendungen zur Verwendung von SSO mit Azure AD neu konfigurieren, wenn Sie Anwendungen verwenden, die SSO mit Azure AD unterstützen, die jedoch einen anderen Identitätsanbieter nutzen. Für Anwendungen, die keine Verbundprotokolle unterstützen, sondern nur die formularbasierte Authentifizierung, empfehlen wir Ihnen, die Anwendung für die Verwendung von [Kennworttresoren](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) mit Azure Active Directory-Anwendungsproxys zu konfigurieren.

![Kennwortbasierte Anmeldung mit Anwendungsproxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Wenn Sie nicht über einen Mechanismus zum Ermitteln nicht verwalteter Anwendungen in Ihrer Organisation verfügen, empfehlen wir Ihnen, einen Ermittlungsprozess mithilfe einer CASB-Lösung (Cloud Access Security Broker) wie [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security) zu implementieren.

Letztlich wird empfohlen, dass Sie Ihre [Anwendungen im App-Katalog aufführen](../develop/v2-howto-app-gallery-listing.md), wenn Sie über einen Azure AD-App-Katalog verfügen und Anwendungen verwenden, die SSO mit Azure AD unterstützen.

#### <a name="single-sign-on-recommended-reading"></a>Empfohlene Artikel zu SSO

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migration von AD FS-Anwendungen zu Azure AD

Die [Migration von AD FS-Apps zu Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) ermöglicht zusätzliche Sicherheitsfunktionen, konsistentere Verwaltbarkeit sowie eine bessere Zusammenarbeit. Wenn Sie über in AD FS konfigurierte Anwendungen verfügen, die SSO mit Azure AD unterstützen, sollten Sie diese Anwendungen zur Verwendung von SSO mit Azure AD neu konfigurieren. Wenn Sie Anwendungen mit ungewöhnlichen nicht von Azure AD unterstützten Konfigurationen in AD FS konfiguriert haben, sollten Sie die Besitzer der Apps kontaktieren, um in Erfahrung zu bringen, ob die spezielle Konfiguration absolut erforderlich ist. Wenn sie nicht erforderlich ist, sollten Sie die Anwendung neu konfigurieren, damit SSO mit Azure AD genutzt wird.

![Azure AD als primärer Identitätsanbieter](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health für AD FS](../hybrid/how-to-connect-health-adfs.md) kann zum Erfassen von Konfigurationsdetails zu allen Anwendungen verwendet werden, die zu Azure AD migriert werden können.

### <a name="assign-users-to-applications"></a>Zuweisen von Benutzern zu Anwendungen

Das [Zuweisen von Benutzern zu Anwendungen](../manage-apps/assign-user-or-group-access-portal.md) erfolgt am besten mithilfe von Gruppen, da sie mehr Flexibilität sowie die Möglichkeit zu einer bedarfsorientierten Verwaltung bieten. Zu den Vorteilen der Verwendung von Gruppen gehören [attributbasierte dynamische Gruppenmitgliedschaften](../enterprise-users/groups-dynamic-membership.md) und die [Delegierung an App-Besitzer](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Wenn Sie bereits Gruppen verwenden und verwalten, wird daher empfohlen, dass Sie die folgenden Aktionen durchführen, um die bedarfsorientierte Verwaltung zu verbessern:

- Delegieren Sie die Verwaltung und Governance von Gruppen an Anwendungsbesitzer.
- Erlauben Sie den Self-Service-Zugriff auf die Anwendung.
- Definieren Sie dynamische Gruppen, wenn die Benutzerattribute den Zugriff auf Anwendungen konsistent bestimmen können.
- Implementieren Sie Gruppennachweise für den Anwendungszugriff mithilfe von [Azure AD-Zugriffsüberprüfungen](../governance/access-reviews-overview.md).

Wenn Sie jedoch Anwendungen finden, die individuellen Benutzern zugewiesen sind, sollten Sie sicherstellen, dass Sie [Governance](../governance/index.yml) für diese Anwendungen einrichten.

#### <a name="assign-users-to-applications-recommended-reading"></a>Empfohlene Artikel zum Zuweisen von Benutzern zu Anwendungen

- [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegieren von App-Registrierungsberechtigungen in Azure Active Directory](../roles/delegate-app-roles.md)
- [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Zugriffsrichtlinien

### <a name="named-locations"></a>Benannte Orte

Mit [benannten Standorten](../reports-monitoring/quickstart-configure-named-locations.md) in Azure AD können Sie in Ihrer Organisation vertrauenswürdige IP-Adressbereiche bezeichnen. Azure AD verwendet benannte Orte zum:

- Vermeiden von False Positives bei Risikoereignissen Die Anmeldung über einen vertrauenswürdigen Netzwerkstandort mindert das Anmelderisiko eines Benutzers.
- Konfigurieren des [standortbasierten bedingten Zugriffs](../reports-monitoring/quickstart-configure-named-locations.md).

![Benannter Ort](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Verwenden Sie die folgende Tabelle, um je nach Priorität die empfohlene Lösung für die Anforderungen Ihrer Organisation zu finden:

| **Priority** | **Szenario** | **Empfehlung** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Wenn Sie die Kennworthashsynchronisierung oder Pass-Through-Authentifizierung verwenden und keine benannten Standorte definiert wurden. | Definieren Sie benannte Standorte, um die Erkennung von Risikoereignissen zu verbessern. |
| 2 | Wenn Sie sich in einem Verbund befinden, den Anspruch „insideCorporateNetwork“ nicht verwenden und keine benannten Standorte definiert wurden. | Definieren Sie benannte Standorte, um die Erkennung von Risikoereignissen zu verbessern. |
| 3 | Wenn Sie keine benannten Standorte in Richtlinien für bedingten Zugriff verwenden und kein Risiko bzw. keine Geräteüberprüfungen in Richtlinien für bedingten Zugriff vorhanden sind. | Konfigurieren Sie die Richtlinie für bedingten Zugriff, sodass benannte Standorte enthalten sind. |
| 4 | Wenn Sie sich in einem Verbund befinden, den Anspruch „insideCorporateNetwork“ verwenden und keine benannten Standorte definiert wurden. | Definieren Sie benannte Standorte, um die Erkennung von Risikoereignissen zu verbessern. |
| 5 | Wenn Sie vertrauenswürdige IP-Adressen mit MFA verwenden, anstatt benannte Standorte zu verwenden und diese als vertrauenswürdig zu markieren. | Definieren Sie benannte Standorte, und kennzeichnen Sie diese als vertrauenswürdig, um die Erkennung von Risikoereignissen zu verbessern. |

### <a name="risk-based-access-policies"></a>Risikobasierte Zugriffsrichtlinien

Azure AD kann das Risiko für jede Anmeldung und jeden Benutzer definieren. Die Verwendung von Risiken als Kriterium in Zugriffsrichtlinien kann eine bessere Benutzererfahrung, indem beispielsweise weniger Authentifizierungseingabeaufforderungen aufgerufen werden, und bessere Sicherheit bieten, indem Benutzer z. B. nur bei Bedarf zur Eingabe aufgefordert und die Reaktion und Behebung automatisiert werden.

![Richtlinie zum Anmelderisiko](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Wenn Sie bereits Azure AD Premium P2-Lizenzen besitzen, die die Verwendung von Risiken in Zugriffsrichtlinien unterstützen, diese aber nicht verwendet werden, empfehlen wir Ihnen dringend das Hinzufügen von Risiken zu Ihren Sicherheitsmaßnahmen.

#### <a name="risk-based-access-policies-recommended-reading"></a>Empfohlene Artikel zu risikobasierten Zugriffsrichtlinien

- [Vorgehensweise: Konfigurieren der Anmelderisiko-Richtlinie](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Vorgehensweise: Konfigurieren der Benutzerrisiko-Richtlinie](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Zugriffsrichtlinien für Clientanwendungen

Die Microsoft Intune-Anwendungsverwaltung bietet die Möglichkeit, Datenschutzüberprüfungen für die Speicherverschlüsselung, PIN, Remotespeicherbereinigung usw. an kompatible mobile Clientanwendungen wie Outlook Mobile zu pushen. Darüber hinaus können Richtlinien für bedingten Zugriff zum [Einschränken des Zugriffs](../conditional-access/app-based-conditional-access.md) auf Clouddienste wie Exchange Online für genehmigte oder kompatible Apps erstellt werden.

Wenn Ihre Mitarbeiter MAM-fähige (mobile Anwendungsverwaltung) Anwendungen wie mobile Office-Apps installieren können, um auf Unternehmensressourcen wie Exchange Online oder SharePoint Online zuzugreifen, und Sie BYOD (Bring-Your-Own-Device) unterstützen, wird empfohlen, dass Sie MAM-Richtlinien bereitstellen, um die Anwendungskonfiguration von persönlichen Geräten ohne MDM-Registrierung (mobile Geräteverwaltung) zu verwalten, und anschließend Ihre Richtlinien für bedingten Zugriff anpassen, um den Zugriff nur über MAM-fähige Clients zuzulassen.

![Zuweisung der Überprüfung des bedingten Zugriffs](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Wenn Mitarbeiter MAM-fähige Anwendungen für Unternehmensressourcen installieren und der Zugriff auf von Intune verwaltete Geräte beschränkt ist, sollten Sie in Erwägung ziehen, MAM-Richtlinien zum Verwalten der Anwendungskonfiguration für persönliche Geräte bereitzustellen und die Richtlinien für bedingten Zugriff anzupassen, sodass der Zugriff nur über MAM-fähige Clients zugelassen wird.

### <a name="conditional-access-implementation"></a>Implementieren des bedingten Zugriffs

Der bedingte Zugriff ist ein wichtiges Tool zur Verbesserung der Sicherheit Ihrer Organisation. Daher ist es wichtig, dass Sie die folgenden bewährten Methoden befolgen:

- Stellen Sie sicher, dass für alle SaaS-Anwendungen mindestens eine Richtlinie angewendet wurde.
- Vermeiden Sie es, den Filter **Alle Apps** mit dem Steuerelement **Blockieren** zu kombinieren, um das Risiko einer Sperrung zu umgehen.
- Vermeiden Sie es, versehentlich **Guests** (Gäste) hinzuzufügen, wenn Sie den Filter **Alle Benutzer** verwenden.
- **Migrieren Sie alle alten Richtlinien zum Azure-Portal**.
- Erfassen Sie alle Kriterien für Benutzer, Geräte und Anwendungen.
- Verwenden Sie Richtlinien für bedingten Zugriff, um [MFA zu implementieren](../conditional-access/plan-conditional-access.md), anstatt eine **MFA pro Benutzer** zu verwenden.
- Nutzen Sie eine kleine Gruppe wichtiger Richtlinien an, die auf mehrere Anwendungen angewendet werden können.
- Definieren Sie leere Ausnahmegruppen, und fügen Sie diese zu den Richtlinien hinzu, um eine Ausnahmestrategie zu erhalten.
- Planen Sie Konten ohne MFA-Kontrollen für den [Notfallzugriff](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency).
- Stellen Sie eine konsistente Umgebung zwischen Microsoft 365-Clientanwendungen (z. B. Teams, OneDrive, Outlook usw.) sicher, indem Sie dieselben Kontrollen für Dienste wie Exchange Online und SharePoint Online implementieren.
- Die Zuweisung zu Richtlinien sollte nur über Gruppen erfolgen, nicht über Einzelpersonen.
- Führen Sie regelmäßige Überprüfungen der Ausnahmegruppen durch, die in Richtlinien verwendet werden, um die Zeit zu begrenzen, in der die Benutzer nicht durch die errichtete Sicherheit geschützt werden. Wenn Sie über Azure AD Premium P2 verfügen, können Sie Zugriffsüberprüfungen verwenden, um diesen Prozess zu automatisieren.

#### <a name="conditional-access-recommended-reading"></a>Empfohlene Artikel zum bedingten Zugriff

- [Best Practices für den bedingten Zugriff in Azure Active Directory](../conditional-access/overview.md)
- [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referenz zu den Einstellungen für den bedingten Azure Active Directory-Zugriff](../conditional-access/concept-conditional-access-conditions.md)
- [Allgemeine Richtlinien für bedingten Zugriff](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Angriffsoberfläche beim Zugriff

### <a name="legacy-authentication"></a>Legacyauthentifizierung

Sichere Anmeldeinformationen wie MFA können Apps mit Legacyauthentifizierungsprotokollen nicht schützen, weshalb sie den bevorzugten Angriffsvektor für böswillige Akteure darstellen. Das Sperren von Legacyauthentifizierung ist beim Verbessern der Zugriffssicherheit wichtig.

Der Begriff Legacyauthentifizierung bezieht sich auf die Authentifizierungsprotokolle, die von Apps wie die folgenden verwendet werden:

- Ältere Office-Clients, die keine moderne Authentifizierung nutzen (z. B. Office 2010-Clients)
- Clients, die Mailprotokolle wie IMAP/SMTP/POP verwenden

Angreifer bevorzugen diese Protokolle, tatsächlich nutzen nahezu [100 % der Kennwort-Spray-Angriffe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) Legacyauthentifizierungsprotokolle. Hacker verwenden Legacyauthentifizierungsprotokolle, weil sie keine interaktive Anmeldemethoden unterstützen, die für zusätzliche Sicherheitsherausforderungen wie MFA und Geräteauthentifizierung erforderlich sind.

Wenn die Legacyauthentifizierung in Ihrer Umgebung umfassend verwendet wird, sollten Sie frühestmöglich eine Migration Ihrer Legacyclients zu Clients planen, die die [moderne Authentifizierung](/office365/enterprise/modern-auth-for-office-2013-and-2016) unterstützen. Wenn einige Ihrer Benutzer bereits eine moderne Authentifizierung verwenden, aber einige andere immer noch eine Legacyauthentifizierung verwenden, sollten Sie die folgenden Schritte im selben Token ausführen, um Clients mit Legacyauthentifizierung zu sperren:

1. Verwenden Sie [Anmeldeaktivitätsberichte](../reports-monitoring/concept-sign-ins.md), um Benutzer zu identifizieren, die immer noch eine Legacyauthentifizierung verwenden, und planen Sie eine Behebung:

   a. Führen Sie für betroffene Benutzer ein Upgrade auf eine moderne Authentifizierung durch.
   
   b. Planen Sie einen Übernahmezeitraum für die im Folgenden beschriebenen Schritte für die Sperrung.
   
   c. Identifizieren Sie, welche Legacyanwendungen eine harte Abhängigkeit von der Legacyauthentifizierung aufweisen. Siehe Schritt 3 unten.

2. Deaktivieren Sie die Quellen von Legacyprotokollen (z. B. Exchange Mailbox) für Benutzer, die keine Legacyauthentifizierung verwenden, um die Offenlegung zu vermeiden.
3. Verwenden Sie für die restlichen Konten (im Idealfall nicht menschliche Identitäten wie Dienstkonten) den [bedingten Zugriff zum Einschränken von Legacyprotokollen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) nach der Authentifizierung.

#### <a name="legacy-authentication-recommended-reading"></a>Empfohlene Artikel zur Legacyauthentifizierung

- [Aktivieren und Deaktivieren des POP3- oder IMAP4-Zugriffs auf E-Mail-Postfächer in Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Zuweisung von Einwilligungen

Bei Angriffen zur Zuweisung unrechtmäßiger Einwilligung erstellen Angreife eine in Azure AD registrierte Anwendung, die Zugriff auf Daten wie Kontaktinformationen, E-Mails oder Dokumente anfordert. Benutzer können schädlichen Anwendung im Rahmen von Phishingangriffen ihre Einwilligung erteilen, wenn sie auf schädlichen Websites landen.

Im Anschluss sind Apps mit Berechtigungen aufgeführt, die Sie für Microsoft Cloud Services überprüfen sollten:

- Apps mit App-basierten oder delegierten \*.ReadWrite-Berechtigungen
- Apps mit delegierten Berechtigungen, die E-Mails im Namen des Benutzers lesen, senden oder verwalten können
- Apps, denen die folgenden Berechtigungen gewährt wurden:

| Resource | Berechtigung |
| :- | :- |
| Exchange Online | EAS.AccessAsUser.All |
| | EWS.AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph-API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Apps mit der Berechtigung zum uneingeschränkten Benutzeridentitätswechsel für den angemeldeten Benutzer. Beispiel:

|Resource | Berechtigung |
| :- | :- |
| Microsoft Graph-API| Directory.AccessAsUser.All |
| Azure-REST-API | user_impersonation |

Informationen zur Vermeidung dieses Szenarios sowie zur Identifizierung und Behandlung von Anwendungen mit unrechtmäßigen Einwilligungen oder Anwendungen mit mehr Einwilligungen als nötig finden Sie unter [Ermitteln und Beheben unrechtmäßiger Gewährungen von Einwilligungen in Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants). Als Nächstes sollten Sie [Self-Service vollständig entfernen](../manage-apps/configure-user-consent.md) und [Governanceverfahren einrichten](../manage-apps/configure-admin-consent-workflow.md). Planen Sie abschließend regelmäßige Überprüfungen der App-Berechtigungen, und entfernen Sie sie, wenn sie nicht erforderlich sind.

#### <a name="consent-grants-recommended-reading"></a>Empfohlene Artikel zu Einwilligungsgewährungen

- [Microsoft Graph-API-Berechtigungen](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Einstellungen für Benutzer und Gruppen

Im Folgenden sind Benutzer- und Gruppeneinstellungen aufgeführt, die gesperrt werden können, wenn keine explizite Geschäftsanforderung vorliegt:

#### <a name="user-settings"></a>Benutzereinstellungen

- **Externe Benutzer:** die externe Zusammenarbeit im Unternehmen kann mithilfe von Diensten wie Teams, Power BI, Sharepoint Online und Azure Information Protection auf organische Weise erfolgen. Wenn Sie über explizite Einschränkungen bei der Überprüfung der von Benutzern initiierten externen Zusammenarbeit verfügen, wird empfohlen, dass Sie externe Benutzer mithilfe der [Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-overview.md) oder einem kontrollierten Vorgang erlauben, beispielsweise über Ihren Helpdesk. Wenn Sie die organische, externe Zusammenarbeit für Dienste nicht zulassen möchten, [können Sie Mitgliedern das Einladen externer Benutzer vollständig verbieten](../external-identities/delegate-invitations.md). Alternativ können Sie [spezifische Domänen in externen Benutzereinladungen zulassen oder blockieren](../external-identities/allow-deny-list.md).
- **App-Registrierungen:** Wenn App-Registrierungen erlaubt sind, können Endbenutzer Anwendungen eigenständig integrieren und diesen Zugriff auf ihre Daten gewähren. Ein typisches Beispiel für App-Registrierungen sind Benutzer, die Outlook-Plug-Ins oder Sprachassistenten wie Alexa oder Siri aktivieren, die ihre E-Mails und Kalender vorlesen oder E-Mails für sie senden. Wenn der Kunde sich dazu entscheidet, die App-Registrierung zu deaktivieren, müssen die Informationssicherheits- und IAM-Teams bei der Verwaltung von Ausnahmen involviert werden (App-Registrierungen, die basierend auf Geschäftsanforderungen erforderlich sind), da die Anwendungen mithilfe eines Administratorkontos registriert werden müssen und da vermutlich ein Prozess zum Operationalisieren des Verfahrens entworfen werden muss.
- **Verwaltungsportal:** Organisationen können das Azure AD-Blatt im Azure-Portal sperren, damit Benutzer ohne Administratorrechte nicht über das Azure-Portal auf die Azure AD-Verwaltung zugreifen können. Navigieren Sie im Azure AD-Verwaltungsportal zu den Benutzereinstellungen, um den Zugriff einzuschränken:

![Eingeschränkter Zugriff im Verwaltungsportal](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Benutzer ohne Administratorrechte können weiterhin über befehlsorientierte Benutzerschnittstellen auf die Verwaltungsoberflächen von Azure AD zugreifen.

#### <a name="group-settings"></a>Gruppeneinstellungen

**Self-Service-Gruppenverwaltung/Benutzer können Sicherheitsgruppen/Microsoft 365-Gruppen erstellen.** Wenn derzeit keine Self-Service-Initiative für Gruppen in der Cloud vorliegt, entscheiden sich Kunden möglicherweise dazu, diese zu deaktivieren, bis sie bereit sind, die Funktion zu verwenden.

#### <a name="groups-recommended-reading"></a>Empfohlene Artikel zu Gruppen

- [Was ist die Azure Active Directory B2B-Zusammenarbeit?](../external-identities/what-is-b2b.md)
- [Integrieren von Anwendungen in Azure Active Directory](../develop/quickstart-register-app.md)
- [Apps, Berechtigungen und Einwilligung in Azure Active Directory](../develop/quickstart-register-app.md)
- [Verwenden von Gruppen zum Verwalten des Zugriffs auf Ressourcen in Azure Active Directory](./active-directory-manage-groups.md)
- [Einrichten der Self-Service-Verwaltung des Anwendungszugriffs in Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Datenverkehr von unerwarteten Standorten

Angreifer stammen aus verschiedenen Teilen der Welt. Sie können dieses Risiko mithilfe von Richtlinien für bedingten Zugriff mit dem Standort als Bedingung verwalten. Die [Standortbedingung](../conditional-access/location-condition.md) einer Richtlinie für bedingten Zugriff ermöglich Ihnen, den Zugriff für Standorte zu blockieren, für die es keinen geschäftlichen Grund für Anmeldungen gibt.

![Erstellen eines neuen benannten Standorts](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Verwenden Sie nach Möglichkeit eine SIEM-Lösung (Security Information & Event Management), um den Zugriff verschiedener Regionen zu analysieren und Muster zu ermitteln. Wenn Sie kein SIEM-Produkt verwenden oder damit keine Authentifizierungsinformationen von Azure AD erfasst werden, empfehlen wir Ihnen die Verwendung von [Azure Monitor](../../azure-monitor/overview.md), um Zugriffsmuster aus verschiedenen Regionen zu identifizieren.

## <a name="access-usage"></a>Nutzung des Zugriffs

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Mit Notfallreaktionsplänen archivierte und integrierte Azure AD-Protokolle

Der Zugriff auf Anmeldeaktivitäten, Überwachungen und Risikoereignisse für Azure AD ist für die Problembehandlung, Nutzungsanalysen und forensische Untersuchungen von entscheidender Bedeutung. Azure AD stellt den Zugriff auf diese Quellen über REST-APIs bereit, die einen begrenzten Aufbewahrungszeitraum aufweisen. Ein SIEM-System oder eine ähnliche Archivierungstechnologie ist für die langfristige Speicherung von Überprüfungen und Unterstützbarkeit unabdingbar. Zum Aktivieren der langfristigen Speicherung von Azure AD-Protokollen müssen Sie diese entweder zu Ihrer vorhandenen SIEM-Lösung hinzufügen oder [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) verwenden. Archivprotokolle können im Rahmen Ihrer Notfallreaktionspläne und Untersuchungen verwendet werden.

#### <a name="logs-recommended-reading"></a>Empfohlene Artikel zu Protokollen

- [Referenz zur Azure Active Directory-Überwachungs-API](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Referenz zur Azure Active Directory-Anmeldeaktivitätsbericht-API](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Abrufen von Daten per Azure AD Reporting-API mit Zertifikaten](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph für Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Referenz zur Office 365-Verwaltungsaktivitäts-API](/office/office-365-management-api/office-365-management-activity-api-reference).
- [Verwenden des Power BI-Inhaltspakets für Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Zusammenfassung

Sichere Identitätsinfrastrukturen weisen 12 Aspekte auf. Die Schritte der folgenden Liste unterstützen Sie beim Ausbauen der Sicherheit und Verwalten Ihrer Anmeldeinformationen, Definieren der Authentifizierungsfunktion, Delegieren von Zuweisungen, Messen der Nutzung und Definieren von Zugriffsrichtlinien basierend auf dem Sicherheitsstatus Ihres Unternehmens.

- Zuweisen von Besitzern zu wichtigen Aufgaben
- Implementieren Sie Lösungen zum Ermitteln schwacher oder kompromittierter Kennwörter, zum Verbessern der Verwaltung und des Schutzes von Kennwörtern und zum Erweitern der Sicherheit des Benutzerzugriffs auf Ressourcen.
- Verwalten Sie die Identitäten von Geräten, um Ihre Ressourcen jederzeit und überall zu schützen.
- Implementieren Sie kennwortlose Authentifizierung.
- Stellen Sie einen standardisierten SSO-Mechanismus für die gesamte Organisation bereit.
- Migrieren Sie Apps aus AD FS zu Azure AD, um bessere Sicherheit und konsistentere Verwaltbarkeit zu erzielen.
- Weisen Sie Anwendungen Benutzer zu, indem Sie Gruppen verwenden, um mehr Flexibilität und bedarfsorientierte Verwaltung zu ermöglichen.
- Konfigurieren Sie risikobasierte Zugriffsrichtlinien.
- Sperren Sie Legacyauthentifizierungsprotokolle.
- Ermitteln Sie unrechtmäßige Zuweisungen von Einwilligungen, und beheben Sie diese.
- Sperren Sie Benutzer- und Gruppeneinstellungen.
- Aktivieren Sie die langfristige Speicherung von Azure AD-Protokollen für die Problembehandlung, Nutzungsanalysen und forensische Untersuchungen.

## <a name="next-steps"></a>Nächste Schritte

Nehmen Sie Ihre ersten Schritte mit den [Überprüfungen und Aktionen für Azure AD Identity Governance](active-directory-ops-guide-govern.md).
