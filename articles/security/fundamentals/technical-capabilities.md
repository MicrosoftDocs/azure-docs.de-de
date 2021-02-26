---
title: Technische Sicherheitsfunktionen in Azure – Microsoft Azure
description: Einführung in die Sicherheitsdienste in Azure, die helfen, Ihre Daten, Ressourcen und Anwendungen in der Cloud zu schützen.
services: security
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 02/04/2021
ms.author: terrylan
ms.openlocfilehash: 6e0642a2124d58bb3af483313ac824568b6bbb39
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585142"
---
# <a name="azure-security-technical-capabilities"></a>Technische Funktionen der Azure-Sicherheit
Dieser Artikel enthält eine Einführung in die Sicherheitsdienste in Azure, die helfen, Ihre Daten, Ressourcen und Anwendungen in der Cloud zu schützen, und die Sicherheitsanforderungen Ihres Unternehmens erfüllen.

## <a name="azure-platform"></a>Azure Platform

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) ist eine Cloudplattform mit Infrastruktur- und Anwendungsdiensten, integrierten Datendiensten und erweiterten Analysefunktionen sowie Entwicklertools und -diensten, die in den öffentlichen Cloud-Rechenzentren von Microsoft gehostet werden. Kunden verwenden Azure für viele verschiedene Kapazitäten und Szenarien: von einfachen Compute-, Netzwerk- und Speicheraufgaben über Dienste für mobile Apps und Web-Apps bis hin zu vollständigen Cloudszenarien wie dem Internet der Dinge. Azure kann mit Open Source-Technologie genutzt und als Hybrid Cloud bereitgestellt oder im Datencenter eines Kunden gehostet werden. In Azure wird Cloudtechnologie in Form von Bausteinen bereitgestellt, damit Unternehmen Kosten sparen, Innovationen schnell umsetzen und Systeme proaktiv verwalten können. Wenn Sie IT-Ressourcen erstellen oder zu einem Cloudanbieter migrieren, verlassen Sie sich darauf, dass die jeweilige Organisation Ihre Anwendungen und Daten mit den Diensten und Steuerungsmöglichkeiten schützen kann, die zum Verwalten der Sicherheit Ihrer cloudbasierten Ressourcen bereitgestellt werden.

Microsoft Azure ist der einzige Cloud Computing-Anbieter, der eine sichere, einheitliche Anwendungsplattform und Infrastructure-as-a-Service bereitstellt, damit Teams mit unterschiedlichen Cloudkenntnissen die jeweiligen Ebenen der Projektkomplexität bewältigen können. Hierfür sind integrierte Datendienste und Analysefunktionen vorhanden, mit denen umfassende Erkenntnisse aus Daten gewonnen werden können – auf Microsoft-Plattformen und anderen Plattformen und für offene Frameworks und Tools. Sie können die Cloud in lokale Datencenter integrieren und Azure-Clouddienste in lokalen Datencentern bereitstellen. Im Rahmen der vertrauenswürdigen Cloud von Microsoft (Microsoft Trusted Cloud) nutzen Kunden Azure, weil sie branchenführende Sicherheit, Zuverlässigkeit, Konformität, Datenschutz und ein großes Netzwerk mit Ansprechpartnern, Partnerunternehmen und Prozessen als Unterstützung für Organisationen in der Cloud erhalten.

Mit Microsoft Azure haben Sie folgende Möglichkeiten:

- Beschleunigen der Innovation mit der Cloud

- Nutzen von Erkenntnissen für Unternehmensentscheidungen und -Apps

- Freies Erstellen und Bereitstellen an jedem Ort

- Schützen Ihres Unternehmens

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Technische Funktionen zur Erfüllung Ihrer Sicherheitspflichten

Microsoft Azure bietet Dienste, mit denen Sie ihre Sicherheits-, Datenschutz- und Compliance-Anforderungen erfüllen können. In der folgenden Abbildung werden verschiedene Azure-Dienste erläutert, die Sie zum Aufbau einer sicheren und konformen Anwendungsinfrastruktur gemäß Branchenstandards nutzen können.

![Verfügbare technische Sicherheitsfunktionen – Gesamtübersicht](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Identitäten und Benutzerzugriff verwalten und kontrollieren

Azure unterstützt Sie beim Schützen von geschäftlichen und persönlichen Informationen, indem Sie in die Lage versetzt werden, Benutzeridentitäten und Anmeldeinformationen zu verwalten und den Zugriff zu steuern.

### <a name="azure-active-directory"></a>Azure Active Directory

Lösungen zur Identitäts- und Zugriffsverwaltung von Microsoft unterstützen IT-Profis dabei, den Zugriff auf Anwendungen und Ressourcen über das Unternehmensrechenzentrum und in der Cloud zu schützen, wobei zusätzliche Überprüfungsebenen aktiviert werden, z.B. mehrstufige Authentifizierung und Richtlinien für bedingten Zugriff. Die Überwachung verdächtiger Aktivitäten über erweiterte Sicherheitsberichtserstellung, Überwachung und Warnung trägt dazu bei, potenzielle Sicherheitsprobleme zu verringern. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) ermöglicht einmaliges Anmelden bei Tausenden von Cloudanwendungen und Zugriff auf Webanwendungen, die Sie lokal ausführen.

Zu den Sicherheitsvorteilen von Azure Active Directory (Azure AD) zählen folgende Möglichkeiten:

- Erstellen und Verwalten einer einzelnen Identität für jeden Benutzer in Ihrer gesamten hybriden Unternehmensumgebung, wobei Benutzer, Gruppen und Geräte synchronisiert werden

- Bereitstellen des Zugriffs auf Ihre Anwendungen mit einmaligem Anmelden, inklusive Tausender bereits integrierter SaaS-Apps

- Aktivieren der Sicherheit für den Anwendungszugriff durch Erzwingen der regelbasierten Multi-Factor Authentication für lokale Anwendungen und Cloudanwendungen

- Bereitstellen des sicheren Remotezugriffs auf lokale Webanwendungen mit dem Azure AD-Anwendungsproxy

Das [Azure Active Directory-Portal](https://aad.portal.azure.com/) ist über das Azure-Portal zugänglich. Über dieses Dashboard erhalten Sie einen Überblick über den Zustand Ihrer Organisation und können den Zugriff auf Verzeichnisse, Benutzer oder Anwendungen schnell und einfach verwalten.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Hier sind die wichtigsten Funktionen der Azure-Identitätsverwaltung aufgeführt:

- Einmaliges Anmelden

- Multi-Factor Authentication

- Sicherheitsüberwachung, Warnungen und Machine Learning-basierte Berichte

- Kundenidentitäts- und Kundenzugriffsverwaltung

- Geräteregistrierung

- Privileged Identity Management

- Schutz der Identität (Identity Protection)

#### <a name="single-sign-on"></a>Einmaliges Anmelden

[Einmaliges Anmelden](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) (Single Sign-On, SSO) bedeutet, dass Sie Zugriff auf sämtliche für Ihre Geschäftsaktivitäten benötigten Anwendungen und Ressourcen erhalten, indem Sie sich nur einmal mit einem einzigen Benutzerkonto anmelden. Nach der Anmeldung können Sie auf alle benötigten Anwendungen zugreifen, ohne sich ein zweites Mal (z.B. durch Eingabe eines Kennworts) authentifizieren zu müssen.

Viele Organisationen nutzen SaaS-Anwendungen (Software-as-a-Service), z. B. Microsoft 365, Box und Salesforce, um die Endbenutzerproduktivität zu steigern. In der Vergangenheit musste das IT-Personal Benutzerkonten in jeder SaaS-Anwendung individuell erstellen und aktualisieren, und Benutzer mussten sich für jede SaaS-Anwendung ein Kennwort merken.

[Azure AD weitet das lokale Active Directory auf die Cloud aus](../../active-directory/manage-apps/what-is-single-sign-on.md) und ermöglicht den Benutzern auf diese Weise, sich mit ihrem primären Organisationskonto nicht nur bei den mit ihrer Domäne verknüpften Geräten und Unternehmensressourcen anzumelden, sondern auch bei sämtlichen Web- und SaaS-Anwendungen, die sie für ihre Arbeit benötigen.

Benutzer müssen sich keine Vielzahl von Benutzernamen und Kennwörtern mehr merken, und der Anwendungszugriff für Benutzer kann basierend auf Organisationsgruppen sowie ihrem Mitarbeiterstatus automatisch bereitgestellt oder deaktiviert werden. [Azure AD stellt Funktionen zum Steuern von Sicherheit und Zugriff bereit](../../active-directory/manage-apps/view-applications-portal.md), mit denen Sie den Benutzerzugriff auf SaaS-Anwendungen zentral verwalten können.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure AD Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) ist eine Authentifizierungsmethode, die die Verwendung von mehr als einer Überprüfungsmethode erfordert und eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen einführt. [MFA hilft beim Schützen](../../active-directory/authentication/concept-mfa-howitworks.md) des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Sie bietet eine leistungsfähige Authentifizierung mit verschiedenen Überprüfungsoptionen – Telefonanruf, SMS oder per Benachrichtigung bzw. Überprüfungscode in einer mobilen App sowie OAuth-Token von Drittanbietern.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Sicherheitsüberwachung, Warnungen und Machine Learning-basierte Berichte

Sicherheitsüberwachung und Warnungen sowie Berichte auf Machine Learning-Basis, die inkonsistente Zugriffsmuster erkennen und Ihnen helfen können, Ihr Unternehmen zu schützen. Sie können die Zugriffs- und Nutzungsberichte von Azure Active Directory verwenden, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen. Mithilfe dieser Informationen kann ein Verzeichnisadministrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können.

Im Azure-Portal oder [Azure Active Directory-Portal](https://aad.portal.azure.com/) sind [Berichte](../../active-directory/reports-monitoring/overview-reports.md) wie folgt kategorisiert:

- Anomalieberichte: Enthalten Anmeldeereignisse, die wir als anomal eingestuft haben. Unser Ziel ist, Sie auf solche Aktivitäten aufmerksam zu machen und es Ihnen zu ermöglichen, eine Entscheidung zu treffen, ob ein Ereignis verdächtig ist.

- Integrierte Anwendungsberichte: Bieten Einblicke, wie Cloudanwendungen in Ihrer Organisation verwendet werden. Azure Active Directory ermöglicht die Integration in Tausende von Cloudanwendungen.

- Fehlerberichte: Enthalten Hinweise auf Fehler, die bei der Bereitstellung von Konten für externe Anwendungen auftreten können.

- Benutzerspezifische Berichte: Enthalten Geräte- und Anmeldeaktivitätsdaten für einen bestimmten Benutzer.

- Aktivitätsprotokolle – Enthalten eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, 7 Tagen oder 30 Tagen sowie geänderte Gruppenaktivitäten und Kennwortzurücksetzungs- und Registrierungsaktivitäten

#### <a name="consumer-identity-and-access-management"></a>Kundenidentitäts- und Kundenzugriffsverwaltung

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) ist ein hoch verfügbarer, globaler Identitätsverwaltungsdienst für kundenorientierte Anwendungen, der für Hunderte Millionen von Identitäten skaliert werden kann. Er kann über mobile und Webplattformen integriert werden. Ihre Kunden können sich über eine anpassbare Oberfläche bei all Ihren Anwendungen anmelden und zu diesem Zweck entweder vorhandene Konten aus sozialen Netzwerken nutzen oder neue Anmeldeinformationen festlegen.

In der Vergangenheit mussten Anwendungsentwickler ihren eigenen Code schreiben, wenn sie [Verbraucher in ihren Anwendungen registrieren und anmelden](../../active-directory-b2c/overview.md) wollten. Und sie hätten lokale Datenbanken oder Systeme zum Speichern von Benutzernamen und Kennwörtern verwendet. Azure Active Directory B2C bietet Ihrer Organisation eine bessere Lösung, um die Verwaltung von Kundenidentitäten in ihre Anwendungen zu integrieren. Dabei kommen eine sichere standardbasierte Plattform und ein umfassender Satz von erweiterbaren Richtlinien zum Einsatz.

Wenn Sie Azure Active Directory B2C verwenden, können sich Ihre Kunden mit vorhandenen Konten in sozialen Netzwerken (Facebook, Google, Amazon, LinkedIn) oder durch Erstellen neuer Anmeldeinformationen (E-Mail-Adresse und Kennwort oder Benutzername und Kennwort) bei Ihren Anwendungen registrieren.

#### <a name="device-registration"></a>Geräteregistrierung

Die [Azure AD-Geräteregistrierung](../../active-directory/devices/overview.md) ist die Grundlage gerätebasierter Szenarien für den [bedingten Zugriff](../../active-directory/devices/overview.md). Wenn ein Gerät registriert wird, stellt die Azure AD-Geräteregistrierung eine Identität für das Gerät bereit, die bei der Anmeldung des Benutzers zum Authentifizieren des Geräts dient. Das authentifizierte Gerät und die Attribute des Geräts können anschließend verwendet werden, um bedingte Zugriffsrichtlinien für Anwendungen zu erzwingen, die in der Cloud und lokal gehostet werden.

In Kombination mit einer Lösung für die [mobile Geräteverwaltung](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft), z.B. Intune, werden die Geräteattribute in Azure Active Directory mit zusätzlichen Informationen über das Gerät aktualisiert. So können Sie Regeln für den bedingten Zugriff erstellen, die erzwingen, dass der Zugriff von Geräten Ihren Standards für Sicherheit und Kompatibilität entspricht.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

Mithilfe von [Azure Active Directory (AD) Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) können Sie Ihre privilegierten Identitäten und deren Zugriff auf Ressourcen in Azure AD und anderen Microsoft Online Services wie Microsoft 365 oder Microsoft Intune verwalten, steuern und überwachen.

Manchmal müssen Benutzer privilegierte Vorgänge in Azure- oder Microsoft 365-Ressourcen oder anderen SaaS-Apps ausführen. Dies bedeutet häufig, dass Organisationen diesen Benutzern in Azure AD dauerhaften privilegierten Zugriff gewähren müssen. Dies stellt ein wachsendes Sicherheitsrisiko für die in der Cloud gehosteten Ressourcen dar, da Organisationen die Aktionen, die diese Benutzer mit ihren Administratorberechtigungen ausführen, nicht ausreichend überwachen können. Darüber hinaus kann die Sicherheit der gesamten Cloud in Gefahr sein, wenn ein Benutzerkonto mit privilegiertem Zugriff kompromittiert wird. Mit Azure AD Privileged Identity Management können Sie dieses Risiko in den Griff bekommen.

Azure AD Privileged Identity Management ermöglicht Ihnen Folgendes:

- Ermitteln, welche Benutzer Azure AD-Administratoren sind

- Aktivieren von bedarfsgesteuertem Just-In-Time-Administratorzugriff auf Microsoft Online Services wie Microsoft 365 und Intune

- Abrufen von Berichten zum Administratorzugriffsverlauf und zu Änderungen bei Administratorzuweisungen

- Aktivieren von Benachrichtigungen zum Zugriff auf eine privilegierte Rolle

#### <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) ist ein Sicherheitsdienst, der eine umfassende Übersicht über erkannte Risiken und potenzielle Sicherheitsrisiken für die Identitäten Ihrer Organisation bietet. Für Identity Protection werden die vorhandenen Azure Active Directory-Funktionen zur Anomalieerkennung genutzt (über die Berichte zu anomalen Aktivitäten von Azure AD), und es werden neue Risikoerkennungstypen eingeführt, mit denen Anomalien in Echtzeit erkannt werden können.

## <a name="secure-resource-access"></a>Sicherer Ressourcenzugriff

Die Zugriffssteuerung in Azure unterliegt zunächst den Abrechnungsaspekten. Der Besitzer eines Azure-Kontos, auf das über das [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) zugegriffen wird, ist der Kontoadministrator (Account Administrator, AA). Abonnements fungieren nicht nur als Container für die Abrechnung, sondern auch als Sicherheitsgrenze: Jedes Abonnement verfügt über einen Dienstadministrator (SA), der Azure-Ressourcen für dieses Abonnement mit dem Azure-Portal hinzufügen, entfernen und ändern kann. Der standardmäßige Dienstadministrator eines neuen Abonnements ist der Kontoadministrator. Der Kontoadministrator kann den Dienstadministrator jedoch im Azure-Kontocenter ändern.

![Zugriff auf geschützte Ressourcen in Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Außerdem sind Abonnements einem Verzeichnis zugeordnet. Durch das Verzeichnis wird eine Gruppe von Benutzern definiert. Dies können Benutzer aus der Organisation (Unternehmen, Uni oder Schule) sein, die das Verzeichnis erstellt hat, oder externe Benutzer (also Microsoft-Konten). Auf Abonnements kann über eine Teilmenge dieser Verzeichnisbenutzer zugegriffen werden, die entweder als Dienstadministrator (SA) oder Co-Administrator (CA) zugewiesen wurden. Die einzige Ausnahme besteht darin, dass Microsoft-Konten (früher Windows Live ID) – aus rechtlichen Gründen – als SA oder CA zugewiesen werden können, ohne im Verzeichnis vorhanden zu sein.

Sicherheitsorientierte Unternehmen müssen sich darauf konzentrieren, Mitarbeitern genau die Berechtigungen zuzuweisen, die sie benötigen. Zu viele Berechtigungen können ein Konto zum leichten Angriffsziel machen. Wenn ihre Berechtigungen nicht ausreichen, können Mitarbeiter nicht effizient arbeiten. Die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) begegnet diesem Problem dadurch, dass eine präzise Zugriffsverwaltung für Azure ermöglicht wird.

![Zugriff auf geschützte Ressourcen](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Anstatt allen uneingeschränkte Berechtigungen in Ihrem Azure-Abonnement oder Ihren Ressourcen zu gewähren, können Sie die Berechtigungen auf bestimmte Aktionen beschränken. Gestatten Sie z. B. mit Azure RBAC einem Mitarbeiter die Verwaltung virtueller Computer in einem Abonnement, während ein anderer im gleichen Abonnement SQL-Datenbanken verwalten kann.

![Zugriff auf geschützte Ressourcen in Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Datensicherheit und -verschlüsselung

Einer der Schlüssel zum Schutz von Daten in der Cloud ist die Berücksichtigung der möglichen Zustände, in denen Ihre Daten auftreten können. Außerdem sollten Sie die Steuerungsmöglichkeiten beachten, die für diesen Zustand verfügbar sind. Im Rahmen der empfohlenen Vorgehensweisen für Datensicherheit und Verschlüsselung in Azure befassen sich die Empfehlungen mit den folgenden Datenzuständen:

- Ruhende Daten: Dies umfasst alle Informationsspeicherobjekte, Container und Typen, die statisch in physischen Medien existieren, sei es auf Magnetplattenspeichern oder optischen Datenträgern.
- Während der Übertragung: Wenn Daten zwischen Komponenten, Speicherorten oder Programmen übertragen werden, beispielsweise über das Netzwerk, über einen Service Bus (von dem lokalen Computer in die Cloud und umgekehrt, inklusive Hybridverbindungen wie ExpressRoute) oder während eines Eingabe-/Ausgabevorganges, werden Sie als „in Bewegung befindlich“ betrachtet.

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Die Verschlüsselung ruhender Daten wird ausführlich unter [Azure-Datenverschlüsselung ruhender Daten](encryption-atrest.md) erläutert.

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

Der Schutz von Daten während der Übertragung sollte ein wesentlicher Bestandteil Ihrer Datenschutzstrategie sein. Da die Daten zwischen vielen verschiedenen Speicherorten übertragen werden, empfiehlt es sich im Allgemeinen, immer SSL/TLS-Protokolle zu verwenden, um Daten zwischen verschiedenen Speicherorten auszutauschen. In einigen Fällen sollten Sie den gesamten Kommunikationskanal zwischen Ihrer lokalen und Ihrer Cloudinfrastruktur isolieren, indem Sie ein virtuelles privates Netzwerk (VPN) verwenden.

Für Daten, die sich zwischen Ihrer lokalen Infrastruktur und Azure bewegen, sollten Sie passende Sicherheitsmaßnahmen in Betracht ziehen, beispielsweise HTTPS oder VPN.

Verwenden Sie eine [Azure-Site-to-Site-VPN-Verbindung ](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) für Organisationen, die den Zugriff von mehreren lokalen Arbeitsstationen auf Azure sichern müssen.

Verwenden Sie eine [Punkt-zu-Standort-VPN-Verbindung](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) für Organisationen, die den Zugriff von einer lokalen Arbeitsstation auf Azure sichern müssen.

Größere Datasets können über dedizierte Hochgeschwindigkeits-WAN-Verbindungen wie [ExpressRoute](https://azure.microsoft.com/services/expressroute/) verschoben werden. Falls Sie sich für die Verwendung von ExpressRoute entscheiden, können Sie die Daten auch auf Anwendungsebene verschlüsseln, indem Sie [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) oder andere Protokolle für zusätzlichen Schutz verwenden.

Falls Sie mit Azure Storage über das Azure-Portal interagieren, werden alle Transaktionen über HTTPS durchgeführt. [Speicher-REST-API](/rest/api/storageservices/) über HTTPS kann auch verwendet werden, um mit [Azure Storage](https://azure.microsoft.com/services/storage/) und [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) zu interagieren.

Organisationen, die die Daten während der Übertragung nicht schützen, sind anfälliger für [Man-in-the-Middle-Angriffe](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [Abhöraktionen](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14)) und Session Hijacking. Bei diesen Angriffen kann es sich um den ersten Schritt zur Zugriffsgewinnung auf vertrauliche Daten handeln.

Erfahren Sie mehr über die Azure-VPN-Option im Artikel [Planung und Entwurf für VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Erzwingen der Datenverschlüsselung auf Dateiebene

[Azure RMS](/azure/information-protection/what-is-azure-rms) verwendet Verschlüsselungs-, Identitäts- und Autorisierungsrichtlinien, um Ihre Dateien und E-Mails zu schützen. Azure RMS funktioniert über mehrere Geräte (Handys, Tablets und PCs), indem Schutz sowohl innerhalb als auch außerhalb Ihrer Organisation geboten wird. Diese Funktion ist möglich, weil Azure RMS eine Schutzebene hinzufügt, die bei den Daten verbleibt, selbst wenn diese die Grenzen Ihrer Organisation verlassen.

Wenn Sie Azure RMS verwenden, um Ihre Dateien zu schützen, verwenden Sie eine Kryptographie, die dem Branchenstandard entspricht und [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) voll unterstützt. Wenn Sie Azure RMS für den Datenschutz einsetzen, haben Sie die Garantie, dass die Datei geschützt bleibt, auch wenn diese in einen Speicher kopiert wird, der nicht von der IT kontrolliert wird, beispielsweise einem Cloudspeicherdienst. Dasselbe gilt für Dateien, die mittels E-Mail freigegeben werden. Die Datei ist als Anhang der E-Mail geschützt, und es wird eine Anleitung mitgeschickt, die erklärt, wie der geschützt Anhang geöffnet wird.
Bei der Planung der Einführung von Azure RMS empfehlen wir Folgendes:

- Installieren Sie die [RMS-Freigabe-App](/azure/information-protection/rms-client/sharing-app-windows). Diese App integriert sich mit Office-Anwendungen, indem sie ein Office-Add-In installiert, damit die Benutzer die Dateien einfach direkt schützen können.

- Konfigurieren Sie Ihre Anwendungen und Dienste für Azure RMS

- Erstellen Sie [benutzerdefinierte Vorlagen](/azure/information-protection/configure-policy-templates), entsprechend Ihrer Geschäftsanforderungen. Beispiel: Eine Vorlage für streng geheime Daten, die für alle E-Mails im Zusammenhang mit streng geheimen Informationen verwendet werden soll.

Organisationen, die in Hinsicht auf die [Datenklassifizierung (in englischer Sprache)](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) und den Dateischutz Schwächen haben, sind möglicherweise anfälliger für Datenlecks. Ohne richtigen Dateischutz ist es Organisationen nicht möglich, Einblicke in das Unternehmen zu erhalten, eine Überwachung in Hinsicht auf Missbrauch durchzuführen und böswilligen Zugriff auf Dateien zu verhindern.

> [!Note]
> Erfahren Sie mehr über Azure RMS, indem Sie den Artikel [Erste Schritte mit Azure Rights Management](/azure/information-protection/requirements) lesen.

## <a name="secure-your-application"></a>Anwendung schützen
Während Azure dafür verantwortlich ist, die Infrastruktur und Plattform zu sichern, auf der Ihre Anwendung ausgeführt wird, ist es Ihre Aufgabe die Anwendung selbst zu sichern. Das heißt, Sie müssen Ihren Anwendungscode und den Inhalt in einer sicheren Weise entwickeln, bereitstellen und verwalten. Ansonsten kann Ihr Anwendungscode oder -inhalt noch immer anfällig für Sicherheitsrisiken sein.

### <a name="web-application-firewall"></a>Web Application Firewall
[Web Application Firewall (WAF)](../../web-application-firewall/ag/ag-overview.md) ist ein Feature von [Application Gateway](../../application-gateway/overview.md), das zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet.

Der Web Application Firewall liegen Regeln aus den [OWASP-Kernregelsätzen](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) (3.0 oder 2.2.9) zugrunde. Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Zu diesen Sicherheitslücken (Exploits) gehören üblicherweise Angriffe durch Einschleusung von SQL-Befehlen oder Angriffe durch websiteübergreifende Skripts, um nur einige zu nennen. Das Verhindern solcher Angriffe im Anwendungscode ist oft schwierig und erfordert strenge Wartung, Patching und Überwachung auf verschiedenen Ebenen der Anwendungstopologie. Eine zentrale Web Application Firewall vereinfacht die Sicherheitsverwaltung erheblich und bietet Anwendungsadministratoren einen besseren Schutz vor Bedrohungen und Angriffen. Mit einer WAF-Lösung können Sie ebenfalls schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways lassen sich problemlos in ein Anwendungsgateway mit Web Application Firewall konvertieren.

Die Web Application Firewall schützt unter anderem vor folgenden gängigen Sicherheitslücken im Web:

- Schutz vor Einschleusung von SQL-Befehlen

- Schutz vor websiteübergreifenden Skripts

- Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion

- Schutz vor Verletzungen des HTTP-Protokolls

- Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts

- Verhindern von Bots, Crawlern und Scannern

- Erkennung häufiger Fehler bei der Anwendungskonfiguration (d. h. Apache, IIS usw.)

> [!Note]
> Eine ausführlichere Liste mit Regeln und Informationen zum entsprechenden Schutz finden Sie in den folgenden [Kernregelsätzen](../../web-application-firewall/ag/ag-overview.md):

Azure bietet Ihnen für Ihre App zudem zahlreiche benutzerfreundliche Funktionen zum Schutz von eingehendem und ausgehendem Datenverkehr. Bei Azure werden Kunden auch dabei unterstützt, ihren Anwendungscode zu schützen, indem extern bereitgestellte Funktionen zur Verfügung gestellt werden, mit denen Ihre Webanwendungen nach Sicherheitsrisiken durchsucht werden.

- [Einrichten der Azure Active Directory-Authentifizierung für Ihre App](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Sichern des Datenverkehrs zu Ihrer App durch Aktivierung von Transport Layer Security (TLS/SSL) – HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Erzwingen des gesamten eingehenden Datenverkehrs über HTTPS-Verbindungen](http://microsoftazurewebsitescheatsheet.info/)

  - [Aktivieren von Strict Transport Security (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Einschränken des Zugriffs auf Ihre App durch die IP-Adresse des Clients](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Einschränken des App-Zugriffs auf der Grundlage des Clientverhaltens (Anforderungshäufigkeit und -parallelität)](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Überprüfen Ihres Web-App-Codes auf Schwachstellen mithilfe der Tinfoil Security-Überprüfung](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurieren der gegenseitigen TLS-Authentifizierung mit obligatorischer Clientzertifikatverwendung für die Verbindungsherstellung mit Ihrer Web-App](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurieren eines Clientzertifikats, das Ihre App zum Herstellen einer sicheren Verbindung mit externen Ressourcen verwendet](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Entfernen von Serverstandardheadern, um zu verhindern, dass Tools Fingerabdrücke Ihrer App erfassen](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Herstellen einer sicheren Verbindung zwischen Ihrer App und Ressourcen in einem privaten Netzwerk mithilfe eines Punkt-zu-Standort-VPNs](../../app-service/web-sites-integrate-with-vnet.md)

- [Herstellen einer sicheren Verbindung zwischen Ihrer App und Ressourcen in einem privaten Netzwerk mithilfe von Hybridverbindungen](../../app-service/app-service-hybrid-connections.md)

Azure App Service nutzt die gleiche Antischadsoftwarelösung, die auch von Azure Cloud Services und Virtual Machines verwendet wird. Weitere Informationen dazu finden Sie in der [Dokumentation zu Antischadsoftware](antimalware.md).

## <a name="secure-your-network"></a>Netzwerk schützen
Microsoft Azure verfügt über eine robuste Netzwerkinfrastruktur zur Unterstützung der Konnektivitätsanforderungen Ihrer Anwendungen und Dienste. Netzwerkkonnektivität ist zwischen Ressourcen in Azure, zwischen lokalen und in Azure gehosteten Ressourcen und zu und aus dem Internet und Azure möglich.

Mit der [Azure-Netzwerkinfrastruktur](/previous-versions/azure/virtual-machines/windows/infrastructure-example) können Sie Azure-Ressourcen über [virtuelle Netzwerke (VNets)](../../virtual-network/virtual-networks-overview.md) sicher miteinander verbinden. Ein VNet ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Bei einem VNet handelt es sich um eine logische Isolation vom dedizierten Azure-Cloudnetzwerk für Ihr Abonnement. Sie können VNets mit Ihren lokalen Netzwerken verbinden.

![Schützen Ihres Netzwerks (Schutz)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Wenn Sie eine einfache Zugriffssteuerung auf Netzwerkebene benötigen (basierend auf IP-Adresse und TCP- oder UDP-Protokollen), können Sie [Netzwerksicherheitsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) verwenden. Eine Netzwerksicherheitsgruppe (NSG) ist eine einfache Firewall, die zustandsbehaftete Pakete filtert und die Zugriffssteuerung auf Grundlage eines [5-Tupels](https://www.techopedia.com/definition/28190/5-tuple)ermöglicht.

Azure-Netzwerke unterstützen die Fähigkeit, das Routingverhalten für den Netzwerkverkehr in Ihrem virtuellen Azure-Netzwerk benutzerdefiniert anzupassen. Sie erreichen dies über die Konfiguration der [benutzerdefinierten Routen](../../virtual-network/virtual-networks-udr-overview.md) in Azure.

Mithilfe der [Tunnelerzwingung](https://www.petri.com/azure-forced-tunneling) können Sie sicherstellen, dass Ihre Dienste nicht über die Erlaubnis verfügen, eine Verbindung mit Geräten im Internet zu initiieren.

Azure unterstützt eine dedizierte WAN-Linkkonnektivität mit Ihrem lokalen Netzwerk und ein Azure Virtual Network mit [ExpressRoute](../../expressroute/expressroute-introduction.md). Für den Link zwischen Azure und Ihrem Standort wird eine dedizierte Verbindung verwendet, die nicht über das öffentliche Internet verläuft. Wenn Ihre Azure-Anwendung in mehreren Datencentern ausgeführt wird, können Sie den [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) verwenden, um Anfragen von Benutzern auf intelligente Weise zwischen Instanzen der Anwendung weiterzuleiten. Sie können Datenverkehr auch an Dienste leiten, die nicht in Azure ausgeführt werden, sofern sie über das Internet zugänglich sind.

## <a name="virtual-machine-security"></a>Sicherheit virtueller Computer

Mit [Azure Virtual Machines](../../virtual-machines/index.yml) können Sie sehr flexibel eine Vielzahl unterschiedlicher Computinglösungen bereitstellen. Mit Unterstützung für Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP und Azure BizTalk Services können Sie jede Workload und jede Sprache auf fast jedem Betriebssystem bereitstellen.

Mit Azure können Sie zum Schützen der virtuellen Computer vor Dateien mit schädlichem Inhalt, Adware und anderen Bedrohungen [Antischadsoftware](antimalware.md) von Anbietern wie Microsoft, Symantec, Trend Micro und Kaspersky verwenden.

Microsoft Antimalware for Azure Cloud Services and Virtual Machines ist eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware. Microsoft Antimalware gibt konfigurierbare Warnungen aus, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen.

[Azure Backup](../../backup/backup-overview.md) ist eine skalierbare Lösung, die Ihre Anwendungsdaten schützt – ganz ohne Investitionskosten und mit sehr niedrigen Betriebskosten. Anwendungsfehler können Ihre Daten beschädigen, und menschliche Fehler können Bugs in Ihren Anwendungen verursachen. Mit Azure Backup sind Ihre virtuellen Windows- und Linux-Computer geschützt.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) unterstützt die Orchestrierung von Replikation, Failover und Wiederherstellung von Workloads und Apps, damit diese Komponenten über einen sekundären Standort zur Verfügung stehen, wenn der primäre Standort ausfällt.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Konformität sicherstellen: Checkliste zur Kaufprüfung von Clouddiensten

Microsoft hat eine [Cloud Services Due Diligence Checklist](https://aka.ms/cloudchecklist.download) (Checkliste zur Kaufprüfung von Clouddiensten) entwickelt, damit Organisationen eine etwaige Umstellung auf die Cloud genau prüfen können. Die Checkliste enthält eine Struktur für Organisationen jeder Größe und jedes Typs – Privatunternehmen und Organisationen des öffentlichen Sektors, z.B. auch Behörden und Non-Profit-Organisationen –, mit deren Hilfe die Ziele und Anforderungen in Bezug auf Leistung, Service, Datenverwaltung und Governance ermittelt werden können. Dies ermöglicht einen Vergleich der Angebote unterschiedlicher Clouddienstanbieter, die letztendlich die Basis für einen Clouddienstvertrag bilden.

Die Checkliste stellt ein Gerüst dar, das Klausel für Klausel an einem neuen internationalen Standard für Clouddienstvereinbarungen (ISO/IEC 19086) ausgerichtet ist. Dieser Standard umfasst einen vereinheitlichten Satz von Aspekten für Organisationen, damit Entscheidungen zur Einführung der Cloud leichter getroffen werden können und eine gemeinsame Basis zum Vergleichen von Clouddienstangeboten vorhanden ist.

Mit der Checkliste wird für eine gründlich geprüfte Umstellung auf die Cloud gesorgt, und Sie erhalten eine strukturierte Anleitung und einen einheitlichen, wiederholbaren Ansatz für die Auswahl eines Clouddienstanbieters.

Die Umstellung auf die Cloud ist somit nicht mehr nur eine Technologieentscheidung. Da die Anforderungen der Checkliste alle Aspekte einer Organisation betreffen, werden alle wichtigen internen Entscheider einbezogen: CIO und CISO sowie die Bereiche Rechtsabteilung, Risikomanagement, Einkauf und Konformität (Compliance). Dies erhöht die Effizienz des Entscheidungsfindungsprozesses, und Entscheidungen werden basierend auf fundierten Argumenten getroffen. So wird die Wahrscheinlichkeit des Auftretens von unvorhergesehenen Hindernissen bei der Umstellung reduziert.

Außerdem bietet die Checkliste Folgendes:

- Bereitstellung von wichtigen Diskussionsthemen für Entscheider zu Beginn des Prozesses zur Cloudeinführung

- Unterstützung von eingehenden Unternehmensdiskussionen zu den Bestimmungen und den eigenen Zielen der Organisation in Bezug auf Datenschutz, personenbezogene Informationen und Datensicherheit

- Hilfestellung für Organisationen beim Identifizieren von potenziellen Problemen, die sich auf ein Cloudprojekt auswirken können

- Bereitstellung eines einheitlichen Satzes mit Fragen mit den gleichen Bedingungen, Definitionen, Metriken und Ergebnissen für jeden Anbieter, um das Vergleichen der Angebote verschiedener Clouddienstanbieter zu vereinfachen

## <a name="azure-infrastructure-and-application-security-validation"></a>Überprüfung der Azure-Infrastruktur- und Anwendungssicherheit

[Azure Operational Security](operational-security.md) bezieht sich auf die Dienste, Steuerelemente und Features, die für Benutzer zum Schützen ihrer Daten, Anwendungen und anderen Ressourcen in Microsoft Azure zur Verfügung stehen.

![Sicherheitsüberprüfung (Erkennung)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure Operational Security basiert auf einem Framework, mit dem die über verschiedene einzigartige Microsoft-Funktionen erworbenen Kenntnisse einbezogen werden, z.B. Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-Programm und umfassende Informationen zur Bedrohungslage hinsichtlich der Sicherheit im Internet.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[Azure Monitor](../../azure-monitor/index.yml) ist die IT-Verwaltungslösung für die Hybrid Cloud. Azure Monitor-Protokolle kann eigenständig oder zur Erweiterung Ihrer vorhandenen System Center-Bereitstellung verwendet werden und bietet Ihnen maximale Flexibilität und Kontrolle für die cloudbasierte Verwaltung Ihrer Infrastruktur.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Mit Azure Monitor können Sie beliebige Instanzen in beliebigen Clouds (einschließlich lokaler Cloud, Azure, AWS, Windows Server, Linux, VMware und OpenStack) verwalten – und das zu geringeren Kosten als mit einer Lösung eines Mitbewerbers. Azure Monitor wurde für cloudorientierte Umgebungen konzipiert und bietet dank eines neuen Ansatzes für die Verwaltung Ihres Unternehmens die schnellste und kostengünstigste Möglichkeit, um sich neuen geschäftlichen Herausforderungen zu stellen und neue Workloads, Anwendungen und Cloudumgebungen zu nutzen.

### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](https://azure.microsoft.com/documentation/services/log-analytics) sammelt Daten von verwalteten Ressourcen in einem zentralen Repository und stellt so Überwachungsdienste bereit. Bei diesen Daten kann es sich um Ereignisse, Leistungsdaten oder benutzerdefinierte Daten handeln, die über die API bereitgestellt wurden. Die gesammelten Daten können für Warnungen und Analysen genutzt und exportiert werden.

![Azure Monitor-Protokolle](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Mithilfe dieser Methode können Sie Daten aus einer Vielzahl von Quellen zusammenfassen, um Daten aus Ihren Azure-Diensten mit Ihrer vorhandenen lokalen Umgebung zu kombinieren. Die Datensammlung wird außerdem klar von der Aktion getrennt, die für diese Daten ausgeführt wird, sodass alle Aktionen für alle Arten von Daten verfügbar sind.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anhand einer Liste mit Empfehlungen werden Sie durch den Prozess des Konfigurierens der erforderlichen Sicherheitsmechanismen geführt.

Beispiele:

- Bereitstellung von Antischadsoftware, um Schadsoftware zu erkennen und zu entfernen

- Konfigurieren von Netzwerksicherheitsgruppen und Regeln, um Datenverkehr zu virtuellen Computern zu steuern

- Bereitstellung von Web Application Firewalls als Schutz vor Angriffen, die auf Ihre Webanwendungen abzielen

- Bereitstellen von fehlenden Systemupdates

- Behandeln von Betriebssystemkonfigurationen, die nicht den empfohlenen Basisregeln entsprechen

Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls. Wurden Bedrohungen erkannt, wird eine Sicherheitswarnung erstellt. Beispiele für eine Erkennung sind:

- Gefährdete virtuelle Computer, die mit bekannten bösartigen IP-Adressen kommunizieren

- Erweiterte Schadsoftware, die mithilfe der Windows-Fehlerberichterstattung erkannt wurde

- Brute-Force-Angriffe gegen virtuelle Computer

- Sicherheitswarnungen von integrierter Antischadsoftware und integrierten Firewalls

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) enthält Verweise auf Informationen zu bestimmten Ressourcentypen. Es bietet Visualisierung, Abfrage, Weiterleitung, Warnung, automatische Skalierung und Automatisierung für Daten sowohl aus der Azure-Infrastruktur (Aktivitätsprotokoll) als auch aus jeder einzelnen Azure-Ressource (Diagnoseprotokolle).

Cloudanwendungen sind komplexe Systeme mit zahlreichen Variablen. Die Überwachung stellt Daten bereit, auf deren Grundlage die ordnungsgemäße Ausführung der Anwendung sichergestellt werden kann. Sie trägt auch zur Vermeidung potenzieller Probleme bei und hilft bei der Behandlung bereits aufgetretener Probleme.

![Diagramm, das zeigt, dass Sie anhand von Überwachungsdaten umfassende Erkenntnisse über Ihre Anwendung gewinnen können](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Darüber hinaus können Sie auf der Grundlage von Überwachungsdaten umfassende Erkenntnisse über Ihre Anwendung gewinnen. Mithilfe dieser Kenntnisse können Sie die Leistung oder Wartungsfreundlichkeit der Anwendung verbessern oder Aktionen automatisieren, die andernfalls manuell ausgeführt werden müssten.

Die Überwachung der Netzwerksicherheit ist ein entscheidender Faktor zur Erkennung von Sicherheitsrisiken im Netzwerk und zur Compliance mit Ihrem Modell für die IT-Sicherheit und bestimmende Governance. Mithilfe der Ansicht für Sicherheitsgruppen können Sie die konfigurierte Netzwerksicherheitsgruppe und Sicherheitsregeln sowie die jeweils geltenden Sicherheitsregeln abrufen. Mit den angewendeten Regeln der Liste können Sie die offenen Ports ermitteln und das Netzwerksicherheitsrisiko bewerten.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) ist ein regionaler Dienst, mit dem Sie Bedingungen auf Netzwerkebene in Azure überwachen und diagnostizieren können. Die Tools zur Netzwerkdiagnose und -visualisierung von Network Watcher helfen Ihnen dabei, Ihr Netzwerk in Azure zu verstehen, Diagnosen durchzuführen und Einblicke zu gewinnen. Dieser Dienst umfasst die Paketerfassung, „Nächster Hop“, die IP-Datenflussüberprüfung, die Sicherheitsgruppenansicht und NSG-Datenflussprotokolle. Die Überwachung auf Szenarioebene bietet im Gegensatz zur Überwachung einzelner Netzwerkressourcen eine End-to-End-Ansicht der Netzwerkressourcen.

### <a name="storage-analytics"></a>Speicheranalyse

Bei der [Speicheranalyse](/rest/api/storageservices/fileservices/storage-analytics) können Metriken gespeichert werden, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst gesendeten Anforderungen zählen. Berichte zu Transaktionen werden sowohl auf API-Vorgangsebene als auch auf Speicherdienstebene erstellt, während Berichte zur Kapazität nur auf Speicherdienstebene erstellt werden. Mit den Metrikdaten können die Speicherdienstnutzung analysiert, Probleme mit Anforderungen für den Speicherdienst diagnostiziert und die Leistung von Anwendungen verbessert werden, die einen Dienst verwenden.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen. Verwenden Sie ihn, um Ihre aktiven Webanwendung zu überwachen. Der Dienst erkennt automatisch Leistungsanomalien. Er verfügt über leistungsstarke Analysetools, mit denen Sie Probleme diagnostizieren und nachvollziehen können, wie Ihre App von den Benutzern verwendet wird. Der Dienst unterstützt Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer App. Er lässt sich für Apps auf einer Vielzahl von Plattformen einsetzen, z.B. .NET, Node.js oder Java EE – lokal gehostet oder in der Cloud. Der Dienst lässt sich in Ihren DevOps-Prozess integrieren und verfügt über Verbindungspunkte mit verschiedenen Entwicklungstools.

Der Dienst überwacht:

- **Anforderungsraten, Antwortzeiten und Fehlerraten**: Finden Sie heraus, welche Seiten zu welchen Tageszeiten am häufigsten verwendet werden und wo Ihre Benutzer sind. Stellen Sie fest, welche Seiten die beste Leistung aufweisen. Wenn die Antwortzeiten und Fehlerraten bei mehr Anforderungen ansteigen, haben Sie möglicherweise ein Problem mit den Ressourcen.

- **Abhängigkeitsraten, Antwortzeiten und Fehlerraten**: Finden Sie heraus, ob Sie von externen Diensten verlangsamt werden.

- **Ausnahmen**: Analysieren Sie die aggregierten Statistiken, oder wählen Sie bestimmte Instanzen aus, und untersuchen Sie die Stapelüberwachung und die zugehörigen Anforderungen. Sowohl die Server- als auch die Browserausnahmen werden gemeldet.

- **Seitenansichten und Ladeleistung**: Von den Browsern der Benutzer gemeldet.

- **AJAX-Aufrufe von Webseiten**: Raten, Antwortzeiten und Fehlerraten.

- **Anzahl von Benutzern und Sitzungen**.

- **Leistungsindikatoren** von Ihren Windows- oder Linux-Servercomputern, z.B. CPU, Arbeitsspeicher und Netzwerkverwendung.

- **Hostdiagnose** von Docker oder Azure.

- **Diagnose-Ablaufverfolgungsprotokolle** aus Ihrer App, sodass Sie Ablaufverfolgungsereignisse mit Anforderungen korrelieren können.

- **Benutzerdefinierte Ereignisse und Metriken**, die Sie selbst im Client- oder Servercode schreiben, um Geschäftsereignisse zu verfolgen, z.B. verkaufte Artikel oder gewonnene Spiele.

Die Infrastruktur für Ihre Anwendung besteht normalerweise aus vielen Komponenten: womöglich ein virtueller Computer, ein Speicherkonto und ein virtuelles Netzwerk oder eine Web-App, eine Datenbank, ein Datenbankserver und Drittanbieterdienste. Sie sehen diese Komponenten nicht als separate Entitäten, sondern als verwandte und voneinander abhängige Teile einer einzelnen Entität. Diese möchten Sie als Gruppe bereitstellen, verwalten und überwachen. Mit dem [Azure Resource Manager](../../azure-resource-manager/management/overview.md) können Sie als Gruppe mit den Ressourcen in Ihrer Lösung arbeiten.

Sie können alle Ressourcen für Ihre Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z.B. Testing, Staging und Produktion. Der Ressourcen-Manager bietet Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Sie Ihre Ressourcen nach der Bereitstellung verwalten können.

**Vorteile der Verwendung des Resource Managers**

Der Ressourcen-Manager bietet mehrere Vorteile:

- Sie können alle Ressourcen für Ihre Lösung als Gruppe bereitstellen, verwalten und überwachen, anstatt diese Ressourcen einzeln zu verarbeiten.

- Sie können die Lösung während des gesamten Entwicklungslebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.

- Sie können zum Verwalten Ihrer Infrastruktur anstelle von Skripts auch deklarative Vorlagen verwenden.

- Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.

- Sie können die Zugriffssteuerung auf alle Dienste in der Ressourcengruppe anwenden, da die rollenbasierte Zugriffssteuerung von (Azure RBAC) standardmäßig in die Verwaltungsplattform integriert ist.

- Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.

- Indem Sie die Kosten für eine Gruppe mit Ressourcen anzeigen, für die das gleiche Tag verwendet wird, erhalten Sie die Abrechnungsinformationen für Ihre Organisation.

> [!Note]
> Der Ressourcen-Manager stellt eine neue Möglichkeit zur Bereitstellung und Verwaltung Ihrer Lösungen bereit. Weitere Informationen zu den Änderungen an dem von Ihnen verwendeten früheren Bereitstellungsmodell finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-step"></a>Nächster Schritt

Das Programm [Azure-Sicherheitsvergleichstest](../benchmarks/introduction.md) umfasst eine Sammlung von Sicherheitsempfehlungen, mit denen Sie die von Ihnen in Azure genutzten Dienste sichern können.