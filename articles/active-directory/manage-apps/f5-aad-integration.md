---
title: Sicherer Hybridzugriff mit F5 und Azure AD | Microsoft-Dokumentation
description: Sicherer Hybridzugriff mit dem F5 BIG-IP Access Policy Manager und dessen Integration in Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e011417b936ed83b4658e6dad25bf8e8ee88aed
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317595"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>Sicherer Hybridzugriff mit dem F5 BIG-IP Access Policy Manager und dessen Integration in Azure Active Directory

Die zunehmende Mobilität und die dadurch entstehende Bedrohungslandschaft machen eine zusätzliche Überprüfung des Zugriffs auf Ressourcen und der Governance erforderlich, bei der das [Zero Trust](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)-Modell an den Anfang und in den Mittelpunkt aller Modernisierungsprogramme gestellt wird.
Wir bei Microsoft und F5 haben realisiert, dass diese digitale Transformation für jedes Unternehmen in der Regel ein mehrjähriges Unterfangen bedeutet, sodass möglicherweise kritische Ressourcen bis zur Modernisierung gefährdet sind. Das Grundkonzept, das dem sicheren Hybridzugriff (Secure Hybrid Access, SHA) mit F5 BIG-IP und Azure Active Directory zugrunde liegt, hat nicht nur eine Verbesserung des Remotezugriffs auf lokale Anwendungen zum Ziel, sondern auch eine Stärkung der allgemeinen Sicherheitslage dieser gefährdeten Dienste.

Untersuchungen zufolge sind in diesem Zusammenhang schätzungsweise 60-80 % der lokalen Anwendungen älteren Datums. Das heißt, dass sie nicht direkt in Azure Active Directory (AD) integriert werden können. Die gleiche Studie weist auch darauf hin, dass große Teile dieser Systeme auf nicht aktuellen Versionen von SAP, Oracle, SAGE und anderen bekannten Workloads, die wichtige Dienste bereitstellen, ausgeführt werden.

Der sichere Hybridzugriff (SHA) wurde für diesen wunden Punkt konzipiert und bietet Unternehmen die Möglichkeit, Ihre F5-Investitionen weiterhin für eine hochwertige Netzwerk- und Anwendungsbereitstellung zu nutzen. In Kombination mit Azure AD überbrückt er die Kluft in der heterogenen Anwendungslandschaft durch eine moderne Identitätssteuerungsebene.

Die Vorabauthentifizierung des Zugriffs auf veröffentlichte BIG-IP-Dienste durch Azure AD bietet viele Vorteile:

- Kennwortlose Authentifizierung durch [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install), [Fast Identity Online (FIDO)-Schlüssel](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) und [zertifikatbasierte Authentifizierung](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)

- [Bedingter Zugriff](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) und [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) als Maßnahmen im Vorfeld

- [Identitätsschutz](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#:~:text=Identity%20Protection%20is%20a%20tool%20that%20allows%20organizations,detection%20data%20to%20third-party%20utilities%20for%20further%20analysis) als adaptive Steuerung durch Risikoprofilerstellung für Benutzer und Sitzung

- [Erkennung von kompromittierten Anmeldeinformationen](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

- [Self-Service-Kennwortzurücksetzung (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr)

- [Partnerzusammenarbeit](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users) mit Berechtigungsverwaltung für den kontrollierten Gastzugriff

- [Cloud App Security (CASB)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) für umfassende App-Erkennung und -Steuerung

- [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) für die Bedrohungsüberwachung und die erweiterte Bedrohungsanalyse

- [Azure AD-Portal](https://azure.microsoft.com/features/azure-portal/) als zentrale Steuerungsebene zum Verwalten von Identität und Zugriff

## <a name="scenario-description"></a>Beschreibung des Szenarios

Als ADC (Application Delivery Controller) und SSL-VPN stellt ein BIG-IP-System lokalen und Remotezugriff auf alle Diensttypen bereit. Dazu zählen unter anderem:

- Moderne und ältere Webanwendungen

- Nicht-webbasierte Anwendungen

- REST- und SOAP-Web-API-Dienste

Der Local Traffic Manager (LTM) ermöglicht die sichere Veröffentlichung von Diensten über die Reverseproxy-Funktionalität. Gleichzeitig wird das BIG-IP-System durch einen ausgereiften Access Policy Manager (APM) um viele Funktionen erweitert, die einen Verbund und einmaliges Anmelden (Single Sign-on, SSO) ermöglichen.

Die Integration basiert auf einer standardmäßigen Verbundvertrauensstellung zwischen dem APM und Azure AD. Dies gilt für die meisten SHA-Anwendungsfälle mit einem [SSL-VPN-Szenario](f5-aad-password-less-vpn.md). Security Assertion Markup Language (SAML)-, OAuth- und Open ID Connect (OICD)-Ressourcen stellen ebenfalls keine Ausnahme dar, da auch sie für den Remotezugriff gesichert werden können. Es könnte aber auch Szenarien geben, bei denen ein BIG-IP-System zu einem Engpass für den „Zero Trust“-Zugriff auf alle Dienste (einschließlich SaaS-Apps) wird.

Durch die mögliche Integration von BIG-IP mit Azure AD wird der zum Sichern von älteren oder nicht in Azure AD integrierten Diensten erforderliche Protokollübergang mit modernen Steuerelementen wie [Kennwortlose Authentifizierung](https://www.microsoft.com/security/business/identity/passwordless) und [Bedingter Zugriff](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) ermöglicht. In diesem Szenario erfüllt ein BIG-IP-System weiterhin seine Rolle als Reverseproxy, während Vorabauthentifizierung und Autorisierung auf Dienstbasis an Azure AD übergeben werden.

Die Schritte 1-4 im Diagramm veranschaulichen den Front-End-Austausch zur Vorabauthentifizierung zwischen einem Benutzer, dem BIG-IP-System und Azure AD in einem vom Dienstanbieter initiierten Flow. Die Schritte 5-6 zeigen die nachfolgende Anreicherung der APM-Sitzung und einmaliges Anmelden (SSO) für einzelne Back-End-Dienste.

![Die Abbildung zeigt die allgemeine Architektur](./media/f5-aad-integration/integration-flow-diagram.png)

| Schritt | BESCHREIBUNG |
|:------|:-----------|
| 1. | Der Benutzer wählt im Portal ein Anwendungssymbol aus, die URL wird aufgelöst, und der SAML-Dienstanbieter (BIG-IP) wird aufgerufen. |
| 2. | BIG-IP leitet den Benutzer zur Vorabauthentifizierung an den SAML-Identitätsanbieter (Azure AD) um.|
| 3. | Azure AD verarbeitet für die Autorisierung die Zertifizierungsstellenrichtlinien und die [Sitzungssteuerelemente](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session).|
| 4. | Der Benutzer wird zurück an BIG-IP geleitet und präsentiert die von Azure AD ausgestellten SAML-Ansprüche. |
| 5. | BIG-IP fordert alle zusätzlichen Sitzungsinformationen an, die in [einmaliges Anmelden (SSO)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) und die [rollenbasierte Zugriffssteuerung (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) für den veröffentlichten Dienst einbezogen werden sollen. |
| 6. | BIG-IP leitet die Clientanforderung an den Back-End-Dienst weiter.

## <a name="user-experience"></a>Benutzererfahrung

Die meisten Benutzer (direkter Mitarbeiter, Partner, Consumer o.ä.) sind bereits mit der Anmeldeumgebung von Office 365 vertraut, sodass der Zugriff auf die BIG-IP-Dienste über den sicheren Hybridzugriff (SHA) größtenteils bekannt ist.

Benutzer finden jetzt, unabhängig vom Gerätetyp oder Standort, ihre veröffentlichten BIG-IP-Dienste zusammen mit Self-Service-Funktionen für eine größere Anzahl von Diensten unter [MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) oder [O365-Launchpads](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf). Benutzer können bei Bedarf sogar weiterhin direkt über das proprietäre Webtop-Portal von BIG-IP auf veröffentlichte Dienste zugreifen. Der sichere Hybridzugriff (SHA) stellt bei der Abmeldung sicher, dass die Sitzung des Benutzers an beiden Endpunkten (BIG-IP und Azure AD) beendet wird, und sorgt so dafür, dass die Dienste vollständig vor unbefugtem Zugriff geschützt bleiben.  

Die gezeigten Screenshots stammen aus dem App-Portal von Azure AD, auf das Benutzer auf sichere Weise zugreifen können, um nach ihren veröffentlichten BIG-IP-Diensten zu suchen und ihre Kontoeigenschaften zu verwalten.  

![Screenshot mit dem Woodgrove-Katalog „My Apps“](media/f5-aad-integration/woodgrove-app-gallery.png)

![Screenshot mit der Woodgrove-Self-Service-Seite „My Account“](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Einblicke und Analyse

BIG-IP spielt für jedes Unternehmen eine wichtige Rolle. Daher sollten bereitgestellte BIG-IP-Instanzen überwacht werden, um sicherzustellen, dass veröffentlichte Dienste sowohl auf SHA- als auch auf Betriebsebene hochverfügbar sind.

Für die Protokollierung von Ereignissen stehen mehrere Optionen zur Verfügung, und zwar entweder lokal oder remote über eine SIEM-Lösung (Security Information and Event Management), die ein separates Speichern und Verarbeiten von Telemetriedaten ermöglichen. Eine äußerst effektive Lösung für die Überwachung von Azure AD- und SHA-spezifischen Aktivitäten ist die gemeinsame Verwendung von [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) und [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview), die Folgendes bietet:

- Ausführliche Übersicht über Ihre Organisation, möglicherweise über mehrere Clouds und lokale Standorte hinweg (einschließlich der BIG-IP-Infrastruktur)

- Zentrale Steuerungsebene, die eine kombinierte Ansicht aller Signale bereitstellt, um die Abhängigkeit von komplexen und dezentralen Tools zu vermeiden

![Die Abbildung zeigt den Überwachungsflow](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Voraussetzungen

Für die Integration von F5 BIG-IP mit Azure AD für den sicheren Hybridzugriff (SHA) sind folgende Voraussetzungen erforderlich:

- Eine F5 BIG-IP-Instanz, die auf einer der folgenden Plattformen ausgeführt wird:

  - Physisches Gerät

  - Virtuelle Hypervisor-Edition wie Microsoft Hyper-V, VMware ESXi, Linux KVM und Citrix Hypervisor

  - Virtuelle Cloud-Edition wie Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, Openstack und Google Cloud

    Eine BIG-IP-Instanz kann entweder lokal oder auf einer beliebigen unterstützten Cloudplattform (einschließlich Azure) ausgeführt werden, sofern eine Internetverbindung, veröffentlichte Ressourcen und alle anderen erforderlichen Dienste (z. B. Active Directory) verfügbar sind.  

- Eine aktive F5 BIG-IP APM-Lizenz über eine der folgenden Optionen:

   - F5 BIG-IP®-Paket „Best“ (oder)

   - Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™

   - Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ (APM) für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM)

   - Lizenz für [90-Tage-Testversion](https://www.f5.com/trial/big-ip-trial.php) von F5 BIG-IP Access Policy Manager™ (APM)

- Azure AD-Lizenzierung über eine der folgenden Optionen:

   - Ein [kostenloses Azure AD-Abonnement](https://docs.microsoft.com/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) stellt die Mindestanforderungen für die Implementierung des sicheren Hybridzugriffs (SHA) mit der kennwortlosen Authentifizierung bereit

   - Ein [Premium-Abonnement](https://azure.microsoft.com/pricing/details/active-directory/) bietet alle zusätzlichen wertsteigernden Anwendungen, die oben erläutert wurden, einschließlich [Bedingter Zugriff](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) und [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

Zum Implementieren des sicheren Hybridzugriffs (SHA) sind keine besonderen Kenntnisse oder Erfahrung mit F5 BIG-IP erforderlich. Es wird jedoch empfohlen, sich mit der F5 BIG-IP-Terminologie vertraut zu machen. Die umfangreiche [Knowledge Base](https://www.f5.com/services/resources/glossary) von F5 ist ebenfalls ein guter Ausgangspunkt, um sich BIG-IP-Kenntnisse anzueignen.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

In den folgenden Tutorials finden Sie eine ausführliche Anleitung zum Implementieren einiger allgemeiner Muster für den sicheren Hybridzugriff (SHA) mit BIG-IP und Azure AD:

- [Exemplarische Vorgehensweise: Bereitstellen von F5 BIG-IP in Azure](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM und Azure AD – einmaliges Anmelden (SSO) bei Kerberos-Anwendungen](https://docs.microsoft.com/azure/active-directory/saas-apps/kerbf5-tutorial#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM und Azure AD – einmaliges Anmelden (SSO) bei headerbasierten Anwendungen](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial#configure-f5-single-sign-on-for-header-based-application)

- [Schützen des SSL-VPN von F5 BIG-IP durch die Integration mit Azure AD und den sicheren Hybridzugriff (SHA)](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Das Ende der Kennwörter: Umsteigen auf die kennwortlose Anmeldung](https://www.microsoft.com/security/business/identity/passwordless)

- [Sicherer Hybridzugriff in Azure Active Directory](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [„Zero Trust“-Framework von Microsoft zum Ermöglichen von Remotearbeit](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Erste Schritte mit Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Nächste Schritte

Ziehen Sie mithilfe Ihrer vorhandenen BIG-IP-Infrastruktur oder durch Bereitstellen einer Testinstanz das Ausführen einer Machbarkeitsstudie für den sicheren Hybridzugriff (SHA) in Betracht. Das [Bereitstellen eines virtuellen BIG-IP-Computers der Virtual Edition (VE) in Azure](f5-bigip-deployment-guide.md) dauert ungefähr 30 Minuten. Dann haben Sie:

- eine vollständig gesicherte Plattform zum Modellieren einer SHA-Machbarkeitsstudie

- eine Vorproduktionsinstanz, eine vollständig gesicherte Plattform zum Testen neuer BIG-IP-Systemupdates und -Hotfixes

Gleichzeitig sollten Sie eine oder zwei Anwendungen identifizieren, die für die Veröffentlichung über BIG-IP und den Schutz mit SHA ausgewählt werden können.  

Wir empfehlen, mit einer Anwendung zu beginnen, die noch nicht über eine BIG-IP-Instanz veröffentlicht wurde, um potenzielle Unterbrechungen bei Produktionsdiensten zu vermeiden. Die in diesem Artikel erwähnten Richtlinien helfen Ihnen, sich mit der allgemeinen Vorgehensweise beim Erstellen der verschiedenen BIG-IP-Konfigurationsobjekte und beim Einrichten des sicheren Hybridzugriffs (SHA) vertraut zu machen. Danach sollten Sie in der Lage sein, denselben Vorgang mit jedem anderen, neuen Dienst zu wiederholen. Außerdem sollten Sie über ausreichende Kenntnisse verfügen, um mit wenig Aufwand vorhandene veröffentlichte BIG-IP-Dienste zu SHA zu konvertieren.

Der folgende interaktive Leitfaden führt Sie durch die allgemeine Vorgehensweise beim Implementieren von SHA und beim Anzeigen der Endbenutzerumgebung.

[![Die Abbildung zeigt das Titelbild des interaktiven Leitfadens](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
