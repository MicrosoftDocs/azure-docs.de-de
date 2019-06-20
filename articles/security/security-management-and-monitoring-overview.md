---
title: Verwaltung und Überwachung von Sicherheitsfeatures – Microsoft Azure | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Sicherheitsfeatures und -dienste in Azure, die als Hilfe bei der Verwaltung und Überwachung von Azure-Clouddiensten und virtuellen Azure-Computern dienen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 9f741f578ea44e27814ddfcde2fadc44a0e90536
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872068"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure-Sicherheitsverwaltung und -Überwachung – Übersicht
Dieser Artikel enthält eine Übersicht über die Sicherheitsfeatures und -dienste in Azure, die als Hilfe bei der Verwaltung und Überwachung von Azure-Clouddiensten und virtuellen Azure-Computern dienen.

## <a name="shared-responsibility"></a>Gemeinsame Verantwortung

Die Sicherheit Ihrer Microsoft-Clouddienste basiert auf einer Partnerschaft zwischen Ihnen und Microsoft, und beide Parteien sind gemeinsam dafür verantwortlich. Microsoft ist für die Azure-Plattform und die physische Sicherheit seiner Rechenzentren verantwortlich (durch den Einsatz von Sicherheitsmaßnahmen wie Türen mit elektronischer Zugangskontrolle, Zäunen und Wachpersonal). Azure bietet eine umfassende Cloudsicherheit auf Softwareebene, die die Anforderungen seiner Kunden an Sicherheit, Datenschutz und Compliance erfüllt.

Sie sind der Eigentümer Ihrer Daten und Identitäten und für deren Schutz verantwortlich. Außerdem müssen Sie für die Sicherheit Ihrer lokalen Ressourcen und der Cloudkomponenten sorgen, die Ihrer Kontrolle unterliegen. Microsoft stellt Sicherheitskontrollen und -funktionen bereit, um Sie beim Schützen Ihrer Daten und Anwendungen zu unterstützen. Der Umfang Ihrer Zuständigkeit für die Sicherheit basiert auf dem Typ des Clouddiensts.

Im folgenden Diagramm sind die Informationen zur Verteilung der Zuständigkeiten für Microsoft und den Kunden zusammengefasst.

![Gemeinsame Verantwortung][1]

Ausführlichere Informationen zur Sicherheitsverwaltung finden Sie unter [Sicherheitsverwaltung in Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Rollenbasierte Access Control

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) ermöglicht eine detaillierte Verwaltung des Zugriffs für Azure-Ressourcen. Mit RBAC können Sie den Benutzern beispielsweise nur die Zugriffsrechte gewähren, die diese zum Ausführen ihrer Aufgaben benötigen. Außerdem können Sie mit RBAC sicherstellen, dass Personen, die das Unternehmen verlassen, keinen Zugriff mehr auf die Ressourcen in der Cloud haben.

Weitere Informationen:

* [Active Directory Team Blog zum Thema RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Rollenbasierte Access Control in Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Mit Azure können Sie Antischadsoftware von großen Anbietern wie Microsoft, Symantec, Trend Micro, McAfee und Kaspersky verwenden. Diese Software dient dem Schutz Ihrer virtuellen Computer vor Dateien mit schädlichem Inhalt, Adware und anderen Bedrohungen.

Bei Microsoft Antimalware für Azure Cloud Services und Virtual Machines haben Sie die Möglichkeit, einen Antischadsoftware-Agent für PaaS-Rollen und virtuelle Computer zu installieren. Dieses Feature basiert auf System Center Endpoint Protection und ermöglicht die Nutzung von bewährter lokaler Sicherheitstechnologie in der Cloud.

Darüber hinaus bieten wir die tiefe Integration für die Produkte [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) und [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) von Trend in die Azure-Plattform an. Deep Security ist eine Antivirenlösung, und SecureCloud ist eine Verschlüsselungslösung. Deep Security wird über ein Erweiterungsmodell auf virtuellen Computern bereitgestellt. Über die Benutzeroberfläche des Azure-Portals und mithilfe von PowerShell können Sie Deep Security auf neu erstellten, hochgefahrenen virtuellen Computern oder auf vorhandenen virtuellen Computern, die bereits bereitgestellt wurden, einsetzen.

Symantec Endpoint Protection (SEP) wird unter Azure ebenfalls unterstützt. Durch die Portalintegration können Sie angeben, dass Sie SEP auf einem virtuellen Computer nutzen möchten. SEP kann über das Azure-Portal auf einem neuen virtuellen Computer oder mit PowerShell auf einem vorhandenen virtuellen Computer installiert werden.

Weitere Informationen:

* [Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern, in englischer Sprache)](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware für Azure Cloud Services und Virtual Machines](azure-security-antimalware.md)
* [Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Windows-Computer](../virtual-machines/windows/classic/install-trend.md)
* [Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Windows-Computer](../virtual-machines/windows/classic/install-symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/) (Neue Antimalware-Optionen zum Schützen von Azure Virtual Machines)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication ist eine Authentifizierungsmethode, für die mehrere Überprüfungsmethoden erforderlich sind. Der Dienst fügt eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und -transaktionen hinzu.

Multi-Factor Authentication trägt zum Schutz des Zugriffs auf Daten und Anwendungen bei und bietet gleichzeitig ein einfaches Anmeldeverfahren für Benutzer. Der Dienst bietet eine leistungsfähige Authentifizierung mit verschiedenen Überprüfungsoptionen (Telefonanruf, SMS oder Benachrichtigung bzw. Überprüfungscode in einer mobilen App) sowie OATH-Token von Drittanbietern.

Weitere Informationen:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Was ist Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Funktionsweise von Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Mit Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine dedizierte private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern. Mit ExpressRoute können Sie Verbindungen mit Microsoft-Clouddiensten herstellen, z.B. Azure, Office 365 und CRM Online. Die Konnektivität kann wie folgt bereitgestellt werden:

* Über ein Any-to-Any-Netzwerk (IP VPN)
* Über ein Point-to-Point-Ethernet-Netzwerk
* Über eine virtuelle Querverbindung über einen Konnektivitätsanbieter in einer Co-Location-Einrichtung

ExpressRoute-Verbindungen nutzen nicht das öffentliche Internet. Sie können mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit bieten als herkömmliche Verbindungen über das Internet.

Weitere Informationen:

* [ExpressRoute - Technische Übersicht](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways des virtuellen Netzwerks

VPN-Gateways, die auch als Azure Virtual Network Gateways bezeichnet werden, werden zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten verwendet. Außerdem dienen sie zum Senden von Datenverkehr zwischen mehreren virtuellen Netzwerken in Azure (Netzwerk-zu-Netzwerk). VPN Gateways bieten sichere standortübergreifende Konnektivität zwischen Azure und Ihrer Infrastruktur.

Weitere Informationen:

* [Informationen zu VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Die Netzwerksicherheit in Azure in der Übersicht](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Es kann vorkommen, dass Benutzer privilegierte Vorgänge in Azure-Ressourcen oder anderen SaaS-Anwendungen ausführen müssen. Dies bedeutet häufig, dass Organisationen diesen Benutzern in Azure Active Directory (Azure AD) dauerhaften privilegierten Zugriff gewähren.

Dies stellt ein zunehmendes Sicherheitsrisiko für die in der Cloud gehosteten Ressourcen dar, da Organisationen die Aktionen, die diese Benutzer mit dem privilegierten Zugriff ausführen, nicht ausreichend überwachen können. Darüber hinaus kann die Sicherheit der gesamten Cloud in Gefahr sein, wenn ein Benutzerkonto mit privilegiertem Zugriff kompromittiert wird. Mit Azure AD Privileged Identity Management können Sie dieses Risiko minimieren, indem Sie Berechtigungen nicht unnötig lange gewähren und sich einen besseren Einblick in deren Nutzung verschaffen.  

Mit Privileged Identity Management wird das Konzept eines „temporären Administrators“ für eine Rolle bzw. Just-in-Time-Administratorzugriff eingeführt. Dieser Administrator ist ein Benutzer, der für die zugewiesene Rolle einen Aktivierungsprozess durchführen muss. Der Aktivierungsprozess ändert die Zuweisung des Benutzers zu einer Rolle in Azure AD für einen bestimmten Zeitraum von „Inaktiv“ in „Aktiv“.

Weitere Informationen:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Erste Schritte mit Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

Azure AD Identity Protection ermöglicht die einheitliche Überwachung von verdächtigen Anmeldeaktivitäten und potenziellen Sicherheitsrisiken, um Ihr Unternehmen zu schützen. Identity Protection erkennt verdächtige Aktivitäten für Benutzer und privilegierte Identitäten (Administratoren) basierend auf folgenden Signalen:

* Brute-Force-Angriffe
* Kompromittierte Anmeldeinformationen
* Anmeldeversuchen von unbekannten Standorten oder infizierten Geräten

Indem Benachrichtigungen gesendet und Abhilfemaßnahmen empfohlen werden, trägt Identity Protection zur Minimierung von Risiken in Echtzeit bei. Der Schweregrad in Bezug auf das Benutzerrisiko wird berechnet. Sie können risikobasierte Richtlinien konfigurieren, um den Anwendungszugriff automatisch vor neuen Bedrohungen zu schützen.

Weitere Informationen:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Show zu Azure AD und Identität: Schutz der Identität – Vorschauversion](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Mit Azure Security Center können Sie Bedrohungen verhindern, erkennen und beheben. Security Center sorgt für eine größere Transparenz und bessere Kontrolle der Sicherheit Ihrer Azure-Ressourcen. Es bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements. Es hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum von Sicherheitslösungen verwendet werden.

Mit Security Center können Sie die Sicherheit Ihrer Azure-Ressourcen wie folgt optimieren und überwachen:

* Sie können Richtlinien für die Ressourcen Ihres Azure-Abonnements definieren:
  * Entsprechend den Sicherheitsanforderungen Ihres Unternehmens
  * Entsprechend den Anwendungstypen oder der Vertraulichkeit der Daten in den einzelnen Abonnements
* Sie können den Status Ihrer virtuellen Azure-Computer, -Netzwerke und -Anwendungen überwachen.
* Es wird eine Liste mit priorisierten Sicherheitswarnungen bereitgestellt, z.B. Warnungen integrierter Partnerlösungen. Zudem werden Informationen bereitgestellt, die Sie zum schnellen Untersuchen von Problemen benötigen. Darüber hinaus werden Empfehlungen zum Reagieren auf einen Angriff gegeben.

Weitere Informationen:

* [Einführung in Azure Security Center](../security-center/security-center-intro.md)
* [Verbessern des Secure Score in Azure Security Center](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph bietet in Microsoft-Produkten und -Diensten Bedrohungsschutz in Echtzeit. Intelligent Security Graph verwendet erweiterte Analysen, die große Mengen von Threat Intelligence- und Sicherheitsdaten verknüpfen, um Erkenntnisse zu bieten, durch die Sie die Sicherheit der Organisation erhöhen können. Um umfassendere Erkenntnisse liefern zu können, nutzt Microsoft erweiterte Analysen, in denen mehr als 450 Milliarden Authentifizierungen pro Monat verarbeitet, 400 Milliarden E-Mails auf Malware und Phishing überprüft und eine Milliarde Geräte aktualisiert werden. Durch diese Erkenntnisse kann Ihre Organisation Angriffe schneller erkennen und darauf reagieren.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
