---
title: Fünf Schritte zum Integrieren Ihrer gesamten Apps in Azure AD
description: In diesem Leitfaden wird beschrieben, wie Sie Ihre gesamten Anwendungen in Azure AD integrieren. In jedem Schritt wird der Wert beschrieben, und es sind Links zu den Ressourcen angegeben, unter denen Sie Informationen zu den technischen Details finden.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172634"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Fünf Schritte zum Integrieren Ihrer gesamten Apps in Azure AD

Azure Active Directory (Azure AD) ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Azure AD verfügt über sichere Lösungen für die Authentifizierung und Autorisierung, damit Kunden, Partner und Mitarbeiter auf die benötigten Anwendungen zugreifen können. Mit Azure AD ist die Identitäts- und Zugriffsverwaltung durch Features wie [bedingter Zugriff](../conditional-access/overview.md), [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md), [einmaliges Anmelden](../hybrid/how-to-connect-sso.md) und [automatische Benutzerbereitstellung](../app-provisioning/user-provisioning.md) einfach und sicher.

Wenn Ihr Unternehmen über ein Microsoft 365-Abonnement verfügt, ist die Wahrscheinlichkeit hoch, dass Sie Azure AD [bereits nutzen](/office365/enterprise/about-office-365-identity). Azure AD kann aber für Ihre gesamten Anwendungen verwendet werden. Indem Sie [Ihre Anwendungsverwaltung zentralisieren](../manage-apps/common-scenarios.md), können Sie dieselben Features, Tools und Richtlinien für die Identitätsverwaltung Ihres gesamten App-Portfolios verwenden. Hierbei erhalten Sie eine einheitliche Lösung, die zu einer besseren Sicherheit, reduzierten Kosten und einer Steigerung der Produktivität führt und Ihnen die Sicherstellung der Konformität ermöglicht. Darüber hinaus erhalten Sie Remotezugriff auf lokale Apps.

In diesem Leitfaden wird beschrieben, wie Sie Ihre gesamten Anwendungen in Azure AD integrieren. In jedem Schritt wird der Wert beschrieben, und es sind Links zu den Ressourcen angegeben, unter denen Sie Informationen zu den technischen Details finden. Diese Schritte sind hier in der von uns empfohlenen Reihenfolge angegeben. Sie können aber auch zu jedem Teil des Prozesses springen, um mit dem Schritt zu starten, der für Sie den größten Nutzen bringt.

Weitere Ressourcen zu diesem Thema, z. B. ausführliche Whitepaper zum Geschäftsprozess, finden Sie auf der Seite [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](../manage-apps/migration-resources.md).

## <a name="1-use-azure-ad-for-new-applications"></a>1. Verwenden von Azure AD für neue Anwendungen

Konzentrieren Sie sich zuerst auf neu erworbene Anwendungen. Wenn Ihr Unternehmen mit der Nutzung einer neuen Anwendung beginnt, sollten Sie sie sofort [Ihrem Azure AD-Mandanten hinzufügen](../manage-apps/add-application-portal.md). Richten Sie eine Unternehmensrichtlinie ein, damit das Hinzufügen neuer Apps zu Azure AD die Standardvorgehensweise Ihrer Organisation ist. Dies bedeutet für vorhandene Geschäftsprozesse nur eine minimale Störung und ermöglicht Ihnen die Untersuchung und Bestätigung des Nutzens, den Sie mit der Integration von Apps erzielen, ohne dass sich die derzeitigen Geschäftsabläufe für die Mitarbeiter Ihres Unternehmens ändern.

Azure Active Directory (Azure AD) enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen, um Ihnen den Einstieg zu erleichtern. Sie können [Ihrer Azure AD-Organisation eine Katalog-App hinzufügen](../manage-apps/add-application-portal.md), indem Sie die Schritt-für-Schritt-[Tutorials](../saas-apps/tutorial-list.md) für die Integration in beliebte Apps verwenden. Beispiele:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Puffer](../saas-apps/slack-tutorial.md)

Darüber hinaus können Sie auch [nicht im Katalog enthaltene Anwendungen integrieren](../manage-apps/view-applications-portal.md). Beispiele hierfür sind alle Anwendungen, die in Ihrer Organisation bereits vorhanden sind, oder alle Drittanbieteranwendungen von einem Anwender, der nicht bereits im Azure AD-Katalog enthalten ist. Sie haben auch die Möglichkeit, [Ihre App dem Katalog hinzuzufügen](../develop/v2-howto-app-gallery-listing.md), falls sie darin noch nicht enthalten ist.

Außerdem können Sie die von Ihnen intern entwickelten Apps integrieren. Dies wird in Schritt 5 dieses Handbuchs beschrieben.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. Ermitteln der vorhandenen Anwendungsnutzung und Priorisieren der Arbeit

Ermitteln Sie als Nächstes die Anwendungen, die von Mitarbeitern häufig genutzt werden, und priorisieren Sie Ihre Arbeit für die Integration in Azure AD.

Sie können beginnen, indem Sie die [Cloud Discovery-Tools](/cloud-app-security/tutorial-shadow-it) von Microsoft Cloud App Security verwenden, um für Ihr Netzwerk die Ermittlung von „Schatten-IT“ durchzuführen (Apps, die nicht von der IT-Abteilung verwaltet werden). Sie können [Microsoft Defender Advanced Threat Protection (ATP) verwenden](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection), um den Ermittlungsprozess zu vereinfachen und zu erweitern.

Außerdem können Sie den [AD FS-Anwendungsaktivitätsbericht](../manage-apps/migrate-adfs-application-activity.md) im Azure-Portal verwenden, um alle AD FS-Apps in Ihrer Organisation, die Anzahl von dafür angemeldeten eindeutigen Benutzern und die Kompatibilität in Bezug auf die Integration in Azure AD zu ermitteln.

Nachdem Sie die Details für Ihre vorhandene Landschaft ermittelt haben, sollten Sie einen [Plan erstellen](../manage-apps/migration-resources.md) und die Apps mit der höchsten Priorität für die Integration priorisieren. Hier sind einige Beispiele für hilfreiche Fragen zur Unterstützung dieses Prozesses angegeben:

- Welche Apps werden am häufigsten verwendet?
- Für welche ist das Risiko am höchsten?
- Welche Apps werden in naher Zukunft außer Betrieb genommen, sodass eine Verschiebung nicht erforderlich ist?
- Welche Apps müssen lokal bleiben und können nicht in die Cloud verschoben werden?

Sie können die größten Vorteile und Kosteneinsparungen erzielen, nachdem Ihre gesamten Apps integriert wurden und Sie nicht mehr mehrere Identitätslösungen nutzen müssen. Sie werden aber bereits eine Vereinfachung der Identitätsverwaltung und eine Erhöhung der Sicherheit feststellen, während Sie Schritt für Schritt auf dieses Ziel hinarbeiten. Es ist ratsam, diesen Zeitraum für die Priorisierung Ihrer Arbeit zu nutzen und zu entscheiden, was für Ihre Situation sinnvoll ist.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Integrieren von Apps anderer Identitätsanbieter

Während Ihres Ermittlungsprozesses haben Sie ggf. Anwendungen ermittelt, die von der IT-Abteilung nicht nachverfolgt werden, sodass Ihre Daten und Ressourcen anfällig sind. Unter Umständen verfügen Sie auch über Anwendungen, für die alternative Identitätslösungen genutzt werden, z. B. Active Directory-Verbunddienste (AD FS) oder andere Identitätsanbieter. Überlegen Sie, wie Sie Ihre Identitäts- und Zugriffsverwaltung konsolidieren können, um Kosten zu sparen und die Sicherheit zu erhöhen. Eine Reduzierung der Anzahl von Identitätslösungen führt zu Folgendem:

- Einsparung von Kosten, indem die lokale Benutzerbereitstellung und -authentifizierung sowie die Lizenzierungsgebühren wegfallen, die an andere Cloudidentitätsanbieter für den gleichen Dienst gezahlt werden müssen
- Reduzierung des Verwaltungsaufwands und Ermöglichung von strengeren Sicherheitsmaßnahmen mit weniger Redundanzen für Ihren Prozess zur Identitäts- und Zugriffsverwaltung
- Ermöglichung des Zugriffs mit sicherem einmaligem Anmelden auf ALLE benötigten Anwendungen für Mitarbeiter über das [MyApps-Portal](../manage-apps/access-panel-collections.md)
- Verbesserung der Intelligence-Ebene der Dienste für [Identity Protection](../identity-protection/overview-identity-protection.md) von Azure AD, z. B. bedingter Zugriff, indem die Datenmenge zur App-Nutzung erhöht wird und die erzielten Vorteile auf neu hinzugefügte Apps ausgedehnt werden.

Wir haben einen Leitfaden zur Verwaltung des Geschäftsprozesses für die Integration von Apps in Azure AD veröffentlicht. Darin sind beispielsweise ein [Poster](https://aka.ms/AppOnePager) und eine [Präsentation](https://aka.ms/AppGuideline) enthalten, damit Sie die für Geschäftsprozesse und Anwendungen verantwortlichen Personen informieren und deren Interesse wecken können. Sie können diese Beispiele mit Ihrem eigenen Branding versehen und für Ihre Organisation veröffentlichen, indem Sie Ihr Unternehmensportal, einen Newsletter oder ein anderes Medium verwenden, während Sie diesen Prozess durchführen.

Ein guter Ausgangspunkt ist die Auswertung Ihrer Nutzung von AD FS (Active Directory-Verbunddienste). In vielen Organisationen wird AD FS für die Authentifizierung mit SaaS-Apps, benutzerdefinierten Branchenanwendungen und Microsoft 365- und Azure AD-basierten Apps genutzt:

![Im Diagramm sind lokale Apps, Branchenanwendungen, SaaS-Apps und Office 365-Apps (über Azure AD) dargestellt, die durch gepunktete Linien jeweils mit Active Directory und AD FS verbunden sind.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Sie können diese Konfiguration aktualisieren, indem Sie [AD FS durch Azure AD als Mittelpunkt Ihrer Lösung für die Identitätsverwaltung ersetzen](../manage-apps/migrate-adfs-apps-to-azure.md). Dies ermöglicht die Anmeldung für alle Apps, auf die Ihre Mitarbeiter zugreifen möchten. Die Mitarbeiter können alle Geschäftsanwendungen, die sie benötigen, über das [MyApps-Portal](../user-help/my-apps-portal-end-user-access.md) leicht finden und kommen darüber hinaus in den Genuss der oben beschriebenen weiteren Vorteile.

![Im Diagramm sind lokale Apps (über Active Directory und AD FS), Branchenanwendungen, SaaS-Apps und Office 365-Apps dargestellt, die durch gepunktete Linien jeweils mit Azure Active Directory verbunden sind.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Nachdem Azure AD zum zentralen Identitätsanbieter geworden ist, können Sie unter Umständen die vollständige Umstellung von AD FS durchführen, anstatt eine Verbundlösung zu nutzen. Für Apps, für die zuvor AD FS für die Authentifizierung verwendet wurde, kann nun Azure AD allein genutzt werden.

![Im Diagramm sind lokale Apps, Branchenanwendungen, SaaS-Apps und Office 365-Apps dargestellt, die durch gepunktete Linien jeweils mit Azure Active Directory verbunden sind. Active Directory und AD FS sind nicht vorhanden.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Sie können auch Apps, für die ein anderer Anbieter eines cloudbasierten Identitätsdiensts genutzt wird, zu Azure AD migrieren. Unter Umständen verfügt Ihre Organisation auch über mehrere Lösungen für die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM). Die Migration zu einer Azure AD-Infrastruktur ist eine Möglichkeit zur Reduzierung der Abhängigkeiten von IAM-Lizenzen (lokal oder in der Cloud) und der Infrastrukturkosten. In Fällen, in denen Sie über M365-Lizenzen ggf. bereits für Azure AD bezahlt haben, gibt es keinen vernünftigen Grund, zusätzlich die Kosten für eine weitere IAM-Lösung zu bezahlen.

## <a name="4-integrate-on-premises-applications"></a>4. Integrieren lokaler Anwendungen

In der Vergangenheit wurde für die Sicherheit von Anwendungen gesorgt, indem der Zugriff nur zugelassen wurde, während eine Verbindung mit dem Unternehmensnetzwerk bestanden hat. In einer zunehmend vernetzten Welt soll aber der Zugriff auf Apps für Kunden, Partner und Mitarbeiter unabhängig von ihrem Standort ermöglicht werden. Der [Azure AD-Anwendungsproxy](../manage-apps/what-is-application-proxy.md) (AppProxy) ist ein Feature von Azure AD, mit dem Ihre vorhandenen lokalen Apps mit Azure AD verbunden werden und bei dem es nicht erforderlich ist, dass Sie zu diesem Zweck Edgeserver oder andere zusätzliche Infrastruktur betreiben.

![Der Anwendungsproxydienst ist im Diagramm in Aktion dargestellt. Ein Benutzer greift auf „https://sales.contoso.com“ zu, und seine Anforderung wird über „https://sales-contoso.msappproxy.net“ in Azure Active Directory an die lokale Adresse „http://sales“ umgeleitet.](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Sie können die Informationen unter [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md) nutzen, um den Anwendungsproxy zu aktivieren und die lokale Anwendung Ihrem Azure AD-Mandanten hinzuzufügen.

Darüber hinaus können Sie Controller für die Anwendungsbereitstellung integrieren, z. B. F5 Big-IP APM oder Zscaler Private Access. Indem Sie diese in Azure AD integrieren, erhalten Sie die moderne Authentifizierungs- und Identitätsverwaltung von Azure AD sowie die Features für Datenverkehrsverwaltung und Sicherheit des Partnerprodukts. Wir nennen diese Lösung [Sicherer Hybridzugriff](../manage-apps/secure-hybrid-access.md). Falls Sie derzeit einen der folgenden Dienste nutzen, helfen Ihnen die Informationen in den Tutorials weiter, in denen die Integration in Azure AD Schritt für Schritt beschrieben wird.

- [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix Application Deliver Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (vormals Citrix NetScaler)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integrieren der von Ihren Entwicklern erstellten Apps

Für Apps, die in Ihrem Unternehmen erstellt werden, können Ihre Entwickler die [Microsoft Identity Platform](../develop/index.yml) verwenden, um die Authentifizierung und Autorisierung zu implementieren. In die Plattform integrierte Anwendungen werden [unter Azure AD registriert](../develop/quickstart-register-app.md) und wie jede andere App Ihres Portfolios verwaltet.

Entwickler können die Plattform sowohl für Apps zur internen Nutzung als auch für Kunden-Apps verwenden, und die Nutzung hat zudem noch weitere Vorteile. Mit der [Microsoft-Authentifizierungsbibliothek (MSAL)](../develop/msal-overview.md), die Teil der Plattform ist, können Entwickler moderne Umgebungen bereitstellen, z. B. für die mehrstufige Authentifizierung und die Verwendung von Sicherheitsschlüsseln für den Zugriff auf ihre Apps, ohne die Implementierung selbst durchführen zu müssen. Darüber hinaus besteht für Apps, die in die Microsoft Identity Platform integriert sind, Zugriff auf [Microsoft Graph](../develop/microsoft-graph-intro.md). Dies ist ein einheitlicher API-Endpunkt, über den die Microsoft 365-Daten bereitgestellt werden, mit denen die Muster für Produktivität, Identität und Sicherheit einer Organisation beschrieben werden. Entwickler können diese Informationen nutzen, um Features zu implementieren, mit denen die Produktivität für Ihre Benutzer erhöht wird. Ein Beispiel hierfür ist die Identifizierung der Personen, mit denen der Benutzer in letzter Zeit interagiert hat, und die Anzeige dieser Personen auf der Benutzeroberfläche der App.

Wir haben eine [Videoreihe](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) bereitgestellt, in der Sie eine umfassende Einführung in die Plattform sowie [viele Codebeispiele](../develop/sample-v2-code.md) in unterstützten Sprachen und für unterschiedliche Plattformen erhalten.

## <a name="next-steps"></a>Nächste Schritte

- [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](../manage-apps/migration-resources.md)