---
title: 'Wichtigkeit sicherer Arbeitsstationen: Azure Active Directory'
description: Es wird beschrieben, warum Organisationen sichere Arbeitsstationen, die mit Azure verwaltet werden, erstellen sollten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357046"
---
# <a name="building-secure-workstations"></a>Erstellen von sicheren Arbeitsstationen

Geschützte isolierte Arbeitsstationen sind für die Sicherheit sensibler Rollen, z. B. Administratoren, Entwickler und Betreiber von kritischen Diensten, von entscheidender Bedeutung. Viele andere Sicherheitskontrollen und -zusagen schlagen fehl oder haben keinerlei Wirkung, wenn die zugrunde liegende Clientarbeitsstation kompromittiert wurde.

In diesem Dokument wird beschrieben, was erforderlich ist, um eine sichere Clientarbeitsstation zu erstellen. Es enthält ausführliche Schritt-für-Schritt-Anleitungen, z. B. zum Einrichten von Sicherheitskontrollen für den Start. Diese Art von Arbeitsstation wird auch als Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) bezeichnet. Sie wird in dieser Referenz verwendet und bildet die Grundlage der Beschreibungen. In der Anleitung geht es aber um cloudbasierte Technologie zum Verwalten des Diensts, und es geht um die Sicherheitsfunktionen, die mit Windows 10RS5, Microsoft Defender ATP, Azure Active Directory und Intune eingeführt wurden.

## <a name="why-securing-workstation-access-is-important"></a>Wichtigkeit des Schutzes von Arbeitsstationen

Die rasante Einführung von Clouddiensten und das Arbeiten an jedem Ort haben neue Möglichkeiten geschaffen. Angreifer nutzen schwache Sicherheitskontrollen auf Geräten aus, mit denen Administratoren arbeiten, und können sich Zugriff auf privilegierte Ressourcen verschaffen.

Im [Verizon Threat Report](https://enterprise.verizon.com/resources/reports/dbir/) und [Security Intelligence Report](https://aka.ms/sir) sind der Missbrauch von Privilegien und Angriffe auf die Lieferkette unter den fünf häufigsten Verfahren, die für Angriffe auf Organisationen verwendet werden, und sie stellen die zweithäufigste erkannte Taktik bei den gemeldeten Vorfällen im Jahr 2018 dar.

Die meisten Angreifer wählen den folgenden Weg:

* Ausführen von Schritten zur Aufklärung (häufig branchenbezogen), um einen Einbruchspfad zu ermitteln
* Analysieren von gesammelten Informationen zum Identifizieren des besten Mittels zur Erlangung von Zugriff auf eine Arbeitsstation (Infiltrierung), die als weniger wichtig betrachtet wird
* Durchführen weiterer Schritte und Ermitteln einer Möglichkeit zur [Seitwärtsbewegung](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Abschöpfen von vertraulichen und sensiblen Daten

Angreifer infiltrieren häufig Geräte, die als weniger risikobehaftet bzw. wichtig angesehen werden, um Aufklärung zu betreiben. Diese anfälligen Geräte werden dann verwendet, um eine Möglichkeit zur Seitwärtsbewegung zu ermitteln, nach Administratoren und Geräten zu suchen und höherwertige Daten zu identifizieren, um erfolgreich Informationen abschöpfen zu können, nachdem diese privilegierten Benutzerrollen gekapert wurden.

![Typisches Kompromittierungsmuster](./media/concept-azure-managed-workstation/typical-timeline.png)

Dieses Dokument enthält eine Lösung zum Schützen Ihrer Computinggeräte, indem die Verwaltung und Dienste isoliert werden. Auf diese Weise soll der Schutz vor Seitwärtsbewegungen oder Angriffen sichergestellt werden, die von weniger wichtigen Produktivitätsgeräten ausgehen. Mit dieser Anordnung wird verhindert, dass Angriffe erfolgreich sind, bei denen in die Kette eingedrungen und dann ein Gerät infiltriert wird, das für die Verwaltung oder den Zugriff auf sensible Cloudressourcen verwendet wird. Für die beschriebene Lösung werden native Azure-Dienste genutzt, die Teil des Microsoft 365 Enterprise-Stapels sind, z. B.:

* Intune für die Geräteverwaltung, einschließlich Aufnahme von Anwendungen und URLs in die Whitelist
* Autopilot für die Geräteeinrichtung und -bereitstellung und die Aktualisierung 
* Azure AD für Benutzerverwaltung, bedingten Zugriff und mehrstufige Authentifizierung
* Windows 10 (aktuelle Version) für Integritätsnachweis für Geräte und Benutzerfreundlichkeit
* Microsoft Defender Advanced Threat Protection (ATP) für Endpunktschutz, Erkennung und Reaktion mit Cloudverwaltung
* Azure AD PIM zur Verwaltung der Autorisierung, z. B. privilegierten Just-In-Time-Zugriff (JIT) auf Ressourcen

## <a name="who-benefit-from-using-a-secure-workstation"></a>Wer profitiert von der Verwendung einer sicheren Arbeitsstation?

Alle Benutzer und Betreiber profitieren von der Verwendung einer sicheren Arbeitsstation. Ein Angreifer, der einen PC oder ein Gerät kompromittiert, hat verschiedene Möglichkeiten. Er kann – während er angemeldet ist – beispielsweise alle zwischengespeicherten Konten übernehmen und Anmeldeinformationen und Token nutzen, die auf diesem Gerät verwendet werden. Dieses Risiko ist der Grund, warum das Schützen von Geräten, die für privilegierte Rollen (z. B. mit Administratorrechten) eingesetzt werden, so wichtig ist. Geräte mit einem privilegierten Konto werden als Ziele für Seitwärtsbewegungen und für Angriffe mit Rechteausweitung genutzt. Diese Konten können für viele verschiedene Ressourcen verwendet werden, z. B.:

* Administratoren von lokalen und cloudbasierten Systemen
* Entwicklerarbeitsstationen für kritische Systeme
* Administratoren von Social Media-Konten mit hohem Gefährdungsgrad
* Sehr sensible Arbeitsstationen, z. B. SWIFT-Bezahlterminals
* Verarbeitung von Geschäftsgeheimnissen auf Arbeitsstationen

Microsoft empfiehlt die Implementierung von erhöhten Sicherheitskontrollen für privilegierte Arbeitsstationen, auf denen diese Konten verwendet werden, um das Risiko zu verringern. Weitere Anleitungen finden Sie unter [Bereitstellungshandbuch für Azure Active Directory-Features](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365 Security Roadmap – die wichtigsten Prioritäten für die ersten 30 Tage, 90 Tage und darüber hinaus](https://aka.ms/o365secroadmap) und [Sichern des privilegierten Zugriffs](https://aka.ms/sparoadmap).

## <a name="why-dedicated-workstations"></a>Gründe für dedizierte Arbeitsstationen

Es ist zwar möglich, ein vorhandenes Gerät mit Sicherheitsmaßnahmen zu versehen, aber es ist besser, eine sichere Basis zu schaffen. Wenn Sie mit einem gut vorbereiteten Gerät und bekannten Sicherheitskontrollen beginnen, ist dies die beste Voraussetzung dafür, dass diese höhere Sicherheitsebene für Ihre Organisation beibehalten werden kann. Bei der ständig zunehmenden Zahl von Angriffsvektoren in Bezug auf E-Mails und das Browsen im Web wird es immer schwieriger, die Vertrauenswürdigkeit eines Geräts sicherzustellen. In diesem Leitfaden wird vorausgesetzt, dass eine dedizierte Arbeitsstation verwendet wird, die von den Standardaufgaben für Produktivität, Browsen und E-Mail getrennt ist. Die Trennung von Produktivität, Browsen und E-Mail für ein Gerät kann negative Auswirkungen auf die Produktivität haben. Diese Sicherheitsmaßnahme ist aber normalerweise für Szenarien akzeptabel, in denen die Aufgaben des Auftrags dies nicht explizit erfordern und das Risiko eines Sicherheitsvorfalls hoch ist.

> [!NOTE]
> Mit dem Browsen im Web ist hier der generelle Zugriff auf beliebige Websites gemeint. Dies stellt ein hohes Risiko dar, das sich stark von der Nutzung eines Webbrowsers zum Zugreifen auf eine kleine Zahl von bekannten Verwaltungswebsites für Dienste wie Azure, Office 365, andere Cloudanbieter und SaaS-Anwendungen unterscheidet.

Mit Beschränkungsstrategien wird für eine höhere Sicherheit gesorgt, indem die Anzahl und der Typ von Kontrollen erhöht wird, die von einem Angreifer überwunden werden müssen, um auf sensible Ressourcen zuzugreifen. Das hier entwickelte Modell ermöglicht die Beschränkung von Administratorrechten auf bestimmte Geräte, indem ein Berechtigungsmodell mit mehreren Ebenen verwendet wird.

## <a name="supply-chain-management"></a>Lieferkettenverwaltung

Entscheidend für eine geschützte Arbeitsstation ist eine Lieferkettenlösung, bei der die von Ihnen verwendete Arbeitsstation vertrauenswürdig ist (Vertrauensgrundlage). Bei dieser Lösung wird für die Vertrauensgrundlage die [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)-Technologie genutzt. Für eine geschützte Arbeitsstation ermöglicht Microsoft Autopilot die Nutzung von per Microsoft OEM optimierten Windows 10-Geräten, die seitens des Herstellers einen bekannten sicheren Zustand aufweisen. Anstatt das Reimaging für ein Gerät durchzuführen, das ggf. nicht vertrauenswürdig ist, kann Microsoft Autopilot ein Windows-Gerät in den Zustand „Für Unternehmen einsatzbereit“ versetzen. Es können Einstellungen und Richtlinien angewendet und Apps installiert werden, und es kann sogar die verwendete Windows 10-Edition geändert werden (z. B. von Windows 10 Pro in Windows 10 Enterprise, um erweiterte Features zu unterstützen).

![Ebenen von sicheren Arbeitsstationen](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Geräterollen und -profile

Im gesamten Leitfaden wird auf mehrere Sicherheitsprofile und Rollen eingegangen, um eine sicherere Lösung für Benutzer, Entwickler und das IT-Personal zu erreichen. Diese Profile wurden so angepasst, dass allgemeine Benutzer in Organisationen unterstützt werden, die von einer erweiterten bzw. sicheren Arbeitsstation profitieren können, während gleichzeitig die Balance zwischen Benutzerfreundlichkeit und Risiko im Auge behalten wird. Der Leitfaden enthält eine Konfiguration der Einstellungen anhand von akzeptierten Branchenstandards. Dieser Leitfaden wird genutzt, um eine Methode zum Erhöhen des Schutzes für Windows 10 und Verringern der Risiken in Bezug auf die Kompromittierung von Geräten oder Benutzern zu veranschaulichen. Die Methode umfasst Richtlinien und Technologie zum Verwalten von Sicherheitsfeatures und -risiken.
![Ebenen von sicheren Arbeitsstationen](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Niedrige Sicherheit**: Eine verwaltete Standardarbeitsstation ist ein guter Ausgangspunkt für die meisten Anwendungen im Heimbereich und in kleinen Unternehmen. Diese Geräte sind in Azure AD registriert und werden mit Intune verwaltet. Das Profil ermöglicht Benutzern das Ausführen von beliebigen Anwendungen und das Browsen auf beliebigen Websites. Eine Antischadsoftware-Lösung wie [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) sollte aktiviert werden.
* **Erweiterte Sicherheit**: Eine geschützte Einstiegslösung, die gut für den Heimbereich, Benutzer in kleinen Unternehmen und die allgemeine Entwicklungstätigkeit geeignet ist.
   * Arbeitsstationen mit erweiterter Sicherheit verfügen über ein richtlinienbasiertes Verfahren zum Erhöhen der Sicherheit des Profils „Niedrige Sicherheit“. Dieses Profil ermöglicht das sichere Arbeiten mit Kundendaten und das Verwenden von Produktivitätstools, z. B. Überprüfung von E-Mails und Browsen im Web. Eine Arbeitsstation mit erweiterter Sicherheit kann genutzt werden, um das Benutzerverhalten und die Profilnutzung einer Arbeitsstation zu überwachen, indem Überwachungsrichtlinien und die Protokollierung in Intune aktiviert werden. In diesem Profil werden für die Arbeitsstation die beschriebenen Sicherheitskontrollen und -richtlinien aktiviert, und die Bereitstellung wird über das Skript „Enhanced Workstation – Windows10 (1809)“ durchgeführt. Für die Bereitstellung wird auch der erweiterte Schutz vor Schadsoftware genutzt, indem [Advanced Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp) eingesetzt wird.
* **Hohe Sicherheit**: Das effektivste Mittel zum Verringern der Angriffsfläche einer Arbeitsstation besteht darin, für die Arbeitsstation die Möglichkeit zur selbstständigen Verwaltung zu beseitigen. Die Beseitigung der lokalen Administratorrechte ist ein Schritt, mit dem die Sicherheit erhöht wird und der sich auf die Produktivität auswirken kann, wenn die Implementierung fehlerhaft durchgeführt wird. Das Profil „Hohe Sicherheit“ baut auf dem Profil „Erweiterte Sicherheit“ auf, aber es wurde eine signifikante Änderung vorgenommen: die Beseitigung der lokalen Administratorrechte. Dieses Profil wurde für Benutzer konzipiert, die ggf. wichtige Aufgaben haben, z. B. eine Führungskraft oder Personen, die mit sensiblen Daten in Berührung kommen, z. B. Lohnbuchhalter oder Genehmiger von Diensten und Prozessen.
   * Für das Benutzerprofil „Hohe Sicherheit“ ist eine Umgebung mit höherer Kontrolle erforderlich, während gleichzeitig weiterhin die Produktivitätsaktivitäten durchgeführt werden können, z. B. E-Mail und Browsen im Web – und die Umgebung muss trotzdem benutzerfreundlich sein. Die Benutzer erwarten, dass Features, z. B. Cookies, Favoriten und andere Verknüpfungen, reibungslos funktionieren. Für diese Benutzer ist es aber ggf. nicht erforderlich, dass diese ihr Gerät modifizieren oder debuggen können, und es müssen keine Treiber installiert werden. Das Profil „Hohe Sicherheit“ wird mit dem Skript „High Security – Windows10 (1809)“ bereitgestellt.
* **Spezialisiert**: Entwickler und IT-Administratoren sind ein attraktives Ziel für Angreifer, da sich mit diesen Rollen Systeme im Sinne des Angreifers ändern lassen. Arbeitsstationen vom Typ „Spezialisiert“ bauen auf Arbeitsstationen vom Typ „Hohe Sicherheit“ auf, und die Sicherheit wird erweitert, indem lokale Anwendungen verwaltet, Internetwebsites begrenzt und Produktivitätsfunktionen eingeschränkt werden, die risikobehaftet sind. Beispiele hierfür sind ActiveX-, Java- und Browser-Plug-Ins sowie mehrere andere bekannte Steuerelemente mit hohem Risiko auf einem Windows-Gerät. Bei diesem Profil werden für die Arbeitsstation die beschriebenen Sicherheitskontrollen und -richtlinien aktiviert, und für die Bereitstellung wird das Skript „DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline“ verwendet.
* **Gesichert**: Ein Angreifer, der ein Administratorkonto kompromittieren kann, kann aufgrund von Datendiebstahl, Datenänderung oder Dienstunterbrechungen normalerweise auch hohen geschäftlichen Schaden anrichten. In diesem gehärteten Zustand aktiviert die Arbeitsstation alle Sicherheitskontrollen und -richtlinien, mit denen die direkte Kontrolle der lokalen Anwendungsverwaltung eingeschränkt wird, und die Produktivitätstools werden entfernt. So wird die Kompromittierung des Geräts erschwert, weil E-Mail und soziale Medien blockiert werden, über die erfolgreiche Phishing-Angriffe am häufigsten durchgeführt werden.  Die geschützte Arbeitsstation kann mit dem Skript „Secure Workstation – Windows10 (1809) SecurityBaseline“ bereitgestellt werden.

   ![Geschützte Arbeitsstation](./media/concept-azure-managed-workstation/secure-workstation.png)

   Bei einer geschützten Arbeitsstation vom Typ „Gesichert“ erhält ein Administrator eine „gehärtete“ Arbeitsstation, die über eine eindeutige Anwendungssteuerung und einen entsprechenden Anwendungsschutz verfügt. Für die Arbeitsstation werden Schutzmaßnahmen in Bezug auf die Anmeldeinformationen, das Gerät und Exploit-Angriffe verwendet, um den Host vor schädlichem Verhalten zu schützen. Darüber hinaus werden alle lokalen Datenträger per BitLocker-Verschlüsselung verschlüsselt.

* **Isoliert**: Dieses benutzerdefinierte Offlineszenario ist eine extreme Lösung (für diesen Fall werden keine Installationsskripts bereitgestellt). Organisationen müssen unter Umständen eine isolierte unternehmenskritische Komponente verwalten, z. B. eine wichtige Fertigungsstrecke oder lebenserhaltende Apparate, für die nicht unterstützte bzw. nicht gepatchte ältere Betriebssysteme erforderlich sind. Da die Sicherheit von entscheidender Bedeutung ist und Clouddienste nicht verfügbar sind, können Organisationen diese Computer entweder manuell verwalten/aktualisieren oder eine isolierte Architektur mit einer Active Directory-Gesamtstruktur (z. B. Enhanced Security Admin Environment (ESAE)) für die Verwaltung verwenden. Unter diesen Umständen sollte die Beseitigung des gesamten Zugriffs mit Ausnahme von grundlegendem Intune-Zugriff und der ATP-Integritätsprüfung erwogen werden.
   * [Anforderungen und Bandbreite: Intune-Netzwerkkonfiguration](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Konfigurieren von Endpunktproxy- und Internetkonnektivitätseinstellungen für Ihren Azure ATP-Sensor](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Nächste Schritte

[Deploy a secure workstation](howto-azure-managed-workstation.md) (Bereitstellen einer sicheren Arbeitsstation)
