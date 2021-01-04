---
title: Erhöhen der Sicherheit bei der Remoteverwaltung in Azure | Microsoft-Dokumentation
description: In diesem Artikel werden Schritte zur Verbesserung der Sicherheit bei der Remoteverwaltung während des Verwaltens von Microsoft Azure-Umgebungen erläutert, darunter auch Clouddienste, Virtual Machines und benutzerdefinierte Anwendungen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: e9eabc73c244526f0ea15b9c72b5377545f662b2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844862"
---
# <a name="security-management-in-azure"></a>Sicherheitsverwaltung in Azure
Azure-Abonnenten können ihre Cloudumgebungen über verschiedene Geräte verwalten. Hierzu zählen etwa Arbeitsstationen für die Verwaltung, Entwickler-PCs und sogar geeignete Endbenutzergeräte, die über die aufgabenspezifischen Berechtigungen verfügen. In einigen Fällen werden Administratorfunktionen über webbasierte Konsolen ausgeführt, z. B. das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/). In anderen Fällen können auch direkte Verbindungen mit Azure von lokalen Systemen über Virtual Private Networks (VPNs), Terminal Services, Clientanwendungsprotokolle oder (programmgesteuert) die Azure-Dienstverwaltungs-API (SMAPI) bestehen. Außerdem können Clientendpunkte entweder Mitglied einer Domäne oder isoliert und unverwaltet sein, z.B. Tablets oder Smartphones.

Dank mehrerer Zugriffs- und Verwaltungsfunktionen stehen zwar vielfältige Optionen zur Verfügung, diese Vielfalt kann aber auch ein erhebliches Risiko für eine Cloudbereitstellung darstellen. Administrative Aktionen lassen sich dadurch unter Umständen schwerer verwalten, nachvollziehen und überprüfen. Diese Variabilität kann auch mit Sicherheitsbedrohungen verbunden sein, wenn nicht regulierter Zugriff auf Clientendpunkte besteht, die zum Verwalten von Clouddiensten verwendet werden. Die Verwendung allgemeiner oder persönlicher Arbeitsstationen zum Entwickeln und Verwalten einer Infrastruktur geht mit unberechenbaren Bedrohungsvektoren einher. Hierzu zählen beispielsweise das Surfen im Web (etwa Watering Hole-Angriffe) oder E-Mails (etwa Social Engineering und Phishing).

![Diagramm, das die verschiedenen Möglichkeiten anzeigt, wie eine Bedrohung Angriffe einbinden könnte](./media/management/typical-management-network-topology.png)

Das Potenzial für Angriffe ist bei dieser Art von Umgebung erhöht, da es aufwändig ist, Sicherheitsrichtlinien und Mechanismen zu schaffen, mit denen der Zugriff auf Azure-Schnittstellen (z.B. SMAPI) von den unterschiedlichsten Endpunkten angemessen verwaltet werden kann.

### <a name="remote-management-threats"></a>Bedrohungen bei Remoteverwaltung
Angreifer versuchen häufig, privilegierten Zugriff zu erhalten, indem sie Anmeldeinformationen von Konten kompromittieren (z.B. per Brute-Force-Angriff auf Kennwörter, Phishing und Abschöpfung von Anmeldeinformationen) oder Benutzer zum Ausführen von Schadcode bewegen (z.B. mit schädlichen Websites per Drive-by-Download oder über schädliche E-Mail-Anhänge). In einer Cloudumgebung mit Remoteverwaltung können Kontoverletzungen aufgrund des Zugriffs von jedem Ort und zu jeder Zeit zu einem erhöhten Risiko führen.

Auch bei strenger Kontrolle von primären Administratorkonten können Benutzerkonten der unteren Ebene verwendet werden, um Schwachstellen der Sicherheitsstrategie auszunutzen. Der Mangel an geeigneten Sicherheitsschulungen kann auch dazu führen, dass aufgrund von versehentlicher Offenlegung oder Verfügbarmachung von Kontoinformationen Sicherheitsverletzungen auftreten.

Wenn eine Benutzerarbeitsstation auch für Verwaltungsaufgaben verwendet wird, kann sie an vielen verschiedenen Punkten kompromittiert werden. Beispiele hierfür sind das Surfen eines Benutzers im Web, das Verwenden von Drittanbieter- und Open-Source-Tools oder das Öffnen einer schädlichen Dokumentdatei, die einen Trojaner enthält.

Im Allgemeinen können die meisten zielgerichteten Angriffe, die zu Sicherheitsverletzungen von Daten führen, auf Browser-Exploits, Plug-Ins (z.B. Flash, PDF, Java) und Spear-Phishing (E-Mail) auf Desktopcomputern zurückgeführt werden. Diese Computer verfügen häufig über Berechtigungen auf Administrator- oder Dienstebene, um für Vorgänge auf Liveservern oder Netzwerkgeräten zuzugreifen, wenn sie für die Entwicklung oder Verwaltung anderer Assets verwendet werden.

### <a name="operational-security-fundamentals"></a>Grundlagen der betrieblichen Sicherheit
Um für eine höhere Sicherheit bei der Verwaltung und beim Betrieb zu sorgen, können Sie die Angriffsfläche eines Clients verringern, indem Sie die Anzahl von möglichen Einstiegspunkten reduzieren. Dies ist mithilfe von Sicherheitsprinzipien möglich: „Aufgabentrennung“ und „Trennung von Umgebungen“.

Isolieren Sie sensible Funktionen voneinander, um die die Wahrscheinlichkeit zu verringern, dass ein Fehler auf einer Ebene zu einer Sicherheitsverletzung auf einer anderen Ebene führt. Beispiele:

* Verwaltungsaufgaben sollten nicht mit Aktivitäten kombiniert werden, die zu einer Kompromittierung führen können (z.B. Schadsoftware in der E-Mail eines Administrators, die dann einen Infrastrukturserver infiziert).
* Eine Arbeitsstation, die für hochsensible Vorgänge verwendet wird, sollte nicht als System genutzt werden, mit dem risikoreiche Vorgänge durchgeführt werden, z.B. Surfen im Internet.

Reduzieren Sie die Angriffsfläche des Systems, indem Sie nicht benötigte Software entfernen. Beispiel:

* Standardarbeitsstationen für die Verwaltung, den Support oder die Entwicklung erfordern keine Installation eines E-Mail-Clients oder anderer Produktivitätsanwendungen, wenn die Hauptaufgabe des Geräts die Verwaltung von Clouddiensten ist.

Clientsysteme, für die Administratorzugriff auf Infrastrukturkomponenten besteht, sollten strengstmöglichen Richtlinien unterliegen, um Sicherheitsrisiken zu verringern. Beispiele:

* Sicherheitsrichtlinien können auch Gruppenrichtlinieneinstellungen, mit denen der offene Internetzugriff vom Gerät verweigert wird, sowie die Nutzung einer restriktiven Firewallkonfiguration umfassen.
* Verwenden Sie VPNs mit Internet Protocol Security (IPsec), falls direkter Zugriff erforderlich ist
* Konfigurieren Sie separate Active Directory-Domänen für die Verwaltung und Entwicklung.
* Isolieren und Filtern Sie den Netzwerkdatenverkehr für Verwaltungsarbeitsstationen.
* Verwenden Sie Antischadsoftware.
* Implementieren Sie Multi-Factor Authentication, um das Risiko von gestohlenen Anmeldeinformationen zu reduzieren.

Mit dem Konsolidieren von Zugriffsressourcen und dem Entfernen nicht verwalteter Endpunkte werden Verwaltungsaufgaben ebenfalls vereinfacht.

### <a name="providing-security-for-azure-remote-management"></a>Bereitstellen von Sicherheit für die Azure-Remoteverwaltung
In Azure werden Sicherheitsmechanismen als Hilfe für Administratoren bereitgestellt, die Azure-Clouddienste und virtuelle Azure-Computer verwalten. Diese Mechanismen sind:

* Authentifizierung und [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Überwachung, Protokollierung und Auditing
* Zertifikate und verschlüsselte Kommunikation
* Webverwaltungsportal
* Netzwerkpaketfilterung

Mit der clientseitigen Sicherheitskonfiguration und Rechenzentrumsbereitstellung eines Verwaltungsgateways ist es möglich, den Administratorzugriff auf Cloudanwendungen und -daten zu beschränken und zu überwachen.

> [!NOTE]
> Bestimmte Empfehlungen in diesem Artikel können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen sowie zu höheren Lizenz- oder Abonnementkosten führen.
>
>

## <a name="hardened-workstation-for-management"></a>Arbeitsstation mit verstärkter Sicherheit für die Verwaltung
Das Ziel einer verstärkten Sicherheit für eine Arbeitsstation ist die Beschränkung auf die wichtigsten Funktionen, die für den Betrieb erforderlich sind, um die Fläche für potenzielle Angriffe so klein wie möglich zu halten. Die Verbesserung der Sicherheit von Systemen („System Hardening“) umfasst die Verringerung der Anzahl von installierten Diensten und Anwendungen, das Einschränken der Anwendungsausführung, das Begrenzen des Netzwerkzugriffs auf das erforderliche Maß und das Halten des Systems auf dem neuesten Stand. Außerdem werden bei der Verwendung einer Verwaltungsarbeitsstation mit verstärkter Sicherheit die Tools und Aktivitäten für die Verwaltung von anderen Endbenutzeraufgaben getrennt.

In einer lokalen Unternehmensumgebung können Sie die Angriffsfläche Ihrer physischen Infrastruktur mit dedizierten Verwaltungsnetzwerken, Serverräumen mit Zugang per Karte und Arbeitsstationen, die in geschützten Bereichen des Netzwerks ausgeführt werden, verkleinern. Bei einem Cloud- oder Hybrid-IT-Modell kann die Erhöhung der Sicherheit von Verwaltungsdiensten komplexer sein, da kein physischer Zugriff auf IT-Ressourcen besteht. Für die Implementierung von Schutzlösungen sind eine sorgfältige Softwarekonfiguration, sicherheitsorientierte Prozesse und umfassende Richtlinien erforderlich.

Durch die Verwendung eines Softwareansatzes mit möglichst geringen Rechten in einer gesperrten Arbeitsstation für die Cloudverwaltung — sowie auch für die Anwendungsentwicklung — kann das Risiko von Sicherheitsvorfällen reduziert werden. Hierbei werden die Remoteverwaltung und Entwicklungsumgebungen standardisiert. Eine Arbeitsstationskonfiguration mit verstärkter Sicherheit kann der Kompromittierung von Konten vorbeugen, die zum Verwalten kritischer Cloudressourcen verwendet werden, indem viele gängige Einfallstore für Schadsoftware und Exploits geschlossen werden. So können Sie etwa [Windows AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759117(v=ws.11)) und Hyper-V-Technologie verwenden, um das Verhalten von Clientsystemen zu steuern und zu isolieren und Bedrohungen zu entschärfen, z. B. E-Mails oder das Surfen im Internet.

Auf einer Arbeitsstation mit verstärkter Sicherheit führt der Administrator ein Standardbenutzerkonto aus (mit dem die Ausführung auf Administratorebene blockiert wird), und die zugeordneten Anwendungen werden per Zulassungsliste kontrolliert. Eine Arbeitsstation mit verstärkter Sicherheit verfügt über die folgenden Elemente:

* Aktives Scannen und Patchen: Setzen Sie Antischadsoftware ein, führen Sie regelmäßige Suchen nach Sicherheitslücken durch, und halten Sie alle Arbeitsstationen aktuell, indem Sie das letzte Sicherheitsupdate immer schnell anwenden.
* Eingeschränkte Funktionalität: Deinstallieren Sie alle Anwendungen, die nicht benötigt werden, und deaktivieren Sie nicht erforderliche Dienste (Startdienste).
* Erhöhen des Netzwerkschutzes: Verwenden Sie Windows-Firewallregeln, um nur gültige IP-Adressen, Ports und URLs zuzulassen, die sich auf die Azure-Verwaltung beziehen. Stellen Sie sicher, dass eingehende Remoteverbindungen mit der Arbeitsstation ebenfalls blockiert werden.
* Einschränkung der Ausführung: Lassen Sie nur einen Satz vordefinierter ausführbarer Dateien zu, die für die Durchführung der Verwaltung benötigt werden (als „default-deny“ bezeichnet). Standardmäßig sollte Benutzern die Berechtigung zum Ausführen aller Programme verweigert werden, es sei denn, dies ist explizit in der Zulassungsliste angegeben.
* Ansatz der geringsten Rechte: Benutzer von Verwaltungsarbeitsstationen sollten auf dem lokalen Computer selbst keine Verwaltungsrechte besitzen. Es ist für sie dann nicht möglich, die Systemkonfiguration oder die Systemdateien – absichtlich oder unabsichtlich – zu ändern.

Sie können all dies erzwingen, indem Sie [Gruppenrichtlinienobjekte](../../active-directory-domain-services/manage-group-policy.md) (Group Policy Objects, GPOs) in Active Directory Domain Services (AD DS) verwenden und über Ihre lokale Verwaltungsdomäne auf alle Verwaltungskonten anwenden.

### <a name="managing-services-applications-and-data"></a>Verwalten von Diensten, Anwendungen und Daten
Die Konfiguration von Azure-Clouddiensten wird entweder über das Azure-Portal oder SMAPI, die Windows PowerShell-Befehlszeilenschnittstelle oder eine benutzerdefinierte Anwendung durchgeführt, bei der diese RESTful-Schnittstellen genutzt werden. Dienste, bei denen diese Mechanismen verwendet werden, sind Azure Active Directory (Azure AD), Azure Storage, Azure Websites und Azure Virtual Network usw.

Per VM bereitgestellte Anwendungen verfügen bei Bedarf über eigene Clienttools und Schnittstellen, z.B. Microsoft Management Console (MMC), eine Verwaltungskonsole für Unternehmen (z.B. Microsoft System Center oder Windows Intune) oder eine andere Verwaltungsanwendung, z.B. Microsoft SQL Server Management Studio. Diese Tools sind meist in einer Unternehmensumgebung oder einem Clientnetzwerk enthalten. Unter Umständen sind sie von bestimmten Netzwerkprotokollen abhängig, z.B. Remotedesktopprotokoll (RDP), für die direkte, zustandsbehaftete Verbindungen erforderlich sind. Einige verfügen ggf. über webfähige Schnittstellen, die nicht offen veröffentlicht werden oder über das Internet zugänglich sein sollten.

Sie können den Zugriff auf die Verwaltung von Infrastruktur- und Plattformdiensten in Azure beschränken, indem Sie [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md), [X.509-Verwaltungszertifikate](/archive/blogs/azuresecurity/certificate-management-in-azure-dos-and-donts) und Firewallregeln verwenden. Für das Azure-Portal und SMAPI ist Transport Layer Security (TLS) erforderlich. Für Dienste und Anwendungen, die Sie in Azure bereitstellen, müssen Sie aber je nach Ihrer Anwendung geeignete Schutzmaßnahmen treffen. Sie können diese Maßnahmen häufig einfacher treffen, wenn Sie eine standardmäßige Arbeitsstationskonfiguration mit verstärkter Sicherheit verwenden.

### <a name="management-gateway"></a>Verwaltungsgateway
Um den gesamten Verwaltungszugriff zu zentralisieren und die Überwachung und Protokollierung zu vereinfachen, können Sie einen dedizierten [Remotedesktopgateway](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560672(v=ws.10))-Server (RD-Gateway) in Ihrem lokalen Netzwerk bereitstellen, der mit Ihrer Azure-Umgebung verbunden ist.

Ein Remotedesktopgateway ist ein richtlinienbasierter RDP-Proxydienst, mit dem Sicherheitsanforderungen durchgesetzt werden. Durch die Implementierung von RD Gateway zusammen mit Windows Server-Netzwerkzugriffsschutz kann sichergestellt werden, dass nur für die Clients eine Verbindung hergestellt werden kann, die spezielle Sicherheitsintegritätskriterien gemäß den Gruppenrichtlinienobjekten (GPOs) von Active Directory-Domänendiensten erfüllen. Berücksichtigen Sie zudem Folgendes:

* Stellen Sie ein [Azure-Verwaltungszertifikat](/previous-versions/azure/gg551722(v=azure.100)) auf dem RD-Gateway bereit, sodass dies der einzige Host ist, der auf das Azure-Portal zugreifen darf.
* Verknüpfen Sie das RD-Gateway mit derselben [Verwaltungsdomäne](/previous-versions/windows/it-pro/windows-2000-server/bb727085(v=technet.10)) wie die Administratorarbeitsstationen. Dies ist erforderlich, wenn Sie eine Site-to-Site-IPsec-VPN- oder ExpressRoute-Verbindung in einer Domäne verwenden, die über eine unidirektionale Vertrauensstellung mit Azure AD verfügt, oder wenn Sie für Anmeldeinformationen zwischen Ihrer lokalen AD DS-Instanz und Azure AD einen Verbund erstellen.
* Konfigurieren Sie eine [Client-Verbindungsautorisierungsrichtlinie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753324(v=ws.11)), damit das RD-Gateway überprüfen kann, ob der Name des Clientcomputers gültig ist (der Domäne angehört) und über die Berechtigung zum Zugreifen auf das Azure-Portal verfügt.
* Verwenden Sie IPsec für [Azure VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/), um den Verwaltungsdatenverkehr besser vor Mithören und Tokendiebstahl zu schützen, oder erwägen Sie die Verwendung einer isolierten Internetverknüpfung per [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Aktivieren Sie Multi-Factor Authentication (siehe [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)) oder die Smartcard-Authentifizierung für Administratoren, die sich über das Remotedesktopgateway anmelden.
* Konfigurieren Sie [Einschränkungen für IP-Adressen](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) oder [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md) in Azure, um die Anzahl von zulässigen Verwaltungsendpunkten zu verringern.

## <a name="security-guidelines"></a>Sicherheitsrichtlinien
Im Allgemeinen ähnelt das Schützen von Administratorarbeitsstationen für die Verwendung mit der Cloud stark den Verfahren, die lokal für alle Arbeitsstationen verwendet werden, z.B. durch auf das Nötigste reduzierte Systeme und eingeschränkte Berechtigungen. Einige einzigartige Aspekte der Cloudverwaltung ähneln eher der Remoteverwaltung oder Out-of-Band-Verwaltung für Unternehmen. Hierzu gehören die Nutzung und Überwachung von Anmeldeinformationen, der geschützte Remotezugriff und die Bedrohungserkennung mit den entsprechenden Gegenmaßnahmen.

### <a name="authentication"></a>Authentifizierung
Sie können Azure-Anmeldeeinschränkungen nutzen, um IP-Quelladressen zum Zugreifen auf Verwaltungstools und Überwachen von Zugriffsanforderungen zu beschränken. Zur Unterstützung von Azure beim Identifizieren von Verwaltungsclients (Arbeitsstationen bzw. Anwendungen) können Sie sowohl SMAPI (mit von Kunden entwickelten Tools, z. B. Windows PowerShell-Cmdlets) als auch das Azure-Portal so konfigurieren, dass zusätzlich zu TLS/SSL-Zertifikaten clientseitige Verwaltungszertifikate installiert werden müssen. Wir empfehlen auch, dass für den Administratorzugriff die Multi-Factor Authentication obligatorisch gemacht wird.

Einige Anwendungen oder Dienste, die Sie in Azure bereitstellen, verfügen unter Umständen über eigene Authentifizierungsmechanismen sowohl für den Endbenutzer- als auch den Administratorzugriff, während andere vollständig auf Azure AD setzen. Je nachdem, ob Sie für Anmeldeinformationen einen Verbund über Active Directory-Verbunddienste (AD FS) erstellen, die Verzeichnissynchronisierung verwenden oder Benutzerkonten ausschließlich in der Cloud verwenden, hilft Ihnen die Verwendung von [Microsoft Identity Manager](/microsoft-identity-manager/) (Teil von Azure AD Premium) beim Verwalten von Identitätslebenszyklen zwischen den Ressourcen.

### <a name="connectivity"></a>Konnektivität
Es sind mehrere Mechanismen verfügbar, mit denen Sie Clientverbindungen für Ihre virtuellen Azure-Netzwerke schützen können. Zwei dieser Mechanismen – [Site-to-Site-VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) und [Point-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) (P2S) – ermöglichen die Verwendung von branchenüblichem IPsec (S2S) oder [Secure Socket Tunneling-Protokoll](/previous-versions/technet-magazine/cc162322(v=msdn.10)) (SSTP) (P2S) für die Verschlüsselung und das Tunneling. Wenn Azure eine Verbindung mit der öffentlich zugänglichen Azure-Dienstverwaltung herstellt, z.B. dem Azure-Portal, wird für Azure das Hypertext Transfer Protocol Secure (HTTPS) benötigt.

Eine eigenständige Arbeitsstation mit verstärkter Sicherheit, für die die Verbindung mit Azure nicht über das RD Gateway hergestellt wird, sollte die SSTP-basierte Point-to-Site-VPN-Verbindung verwenden, um die erste Verbindung mit dem Azure Virtual Network einzurichten. Anschließend wird dann die RDP-Verbindung mit einzelnen virtuellen Computern über den VPN-Tunnel hergestellt.

### <a name="management-auditing-vs-policy-enforcement"></a>Vergleich von Verwaltungsüberwachung und Richtliniendurchsetzung
Normalerweise gibt es zwei Ansätze zum Schützen von Verwaltungsprozessen: Überwachung und Richtliniendurchsetzung. Die gemeinsame Nutzung beider Ansätze ermöglicht eine umfassende Kontrolle, ist aber nicht in allen Situationen möglich. Außerdem ist jeder Ansatz in Bezug auf die Verwaltung der Sicherheit mit unterschiedlichen Risiken, Kosten und Anstrengungen verbunden. Dies gilt besonders, da sich dies auf das Vertrauen bezieht, das sowohl in Personen als auch in Systemarchitekturen gesetzt wird.

Die Überwachung, die Protokollierung und das Auditing sind eine Basis für das Nachverfolgen und Verstehen von Verwaltungsaktivitäten. Aufgrund der Menge der generierten Daten ist es unter Umständen aber nicht immer möglich, alle Aktionen bis ins kleinste Detail zu überwachen und zu überprüfen. Das Überprüfen der Effektivität von Verwaltungsrichtlinien ist aber eine bewährte Methode.

Die Richtliniendurchsetzung, die auch eine strenge Zugriffssteuerung umfasst, sorgt für programmgesteuerte Mechanismen, mit denen Administratoraktionen gesteuert werden können. Außerdem wird hiermit sichergestellt, dass alle möglichen Schutzmaßnahmen verwendet werden. Die Protokollierung ermöglicht einen Nachweis der Durchsetzung sowie die Aufzeichnung der Aktivitäten, die von Personen von einem bestimmten Ort aus und zu einem bestimmten Zeitpunkt durchgeführt werden. Darüber hinaus ermöglicht die Protokollierung Ihnen das Überprüfen und Verifizieren von Informationen zur Einhaltung der Richtlinien durch Administratoren sowie das Führen eines Aktivitätsnachweises.

## <a name="client-configuration"></a>Clientkonfiguration
Für eine Arbeitsstation mit verstärkter Sicherheit empfehlen wir drei Hauptkonfigurationen. Die größten Unterscheidungsmerkmale sind Kosten, Nutzbarkeit und Zugänglichkeit, wobei bei allen Optionen aber für ein ähnliches Sicherheitsprofil gesorgt ist. Die folgende Tabelle enthält eine kurze Analyse der jeweiligen Vorteile und Risiken. (Beachten Sie, dass mit „Unternehmens-PC“ eine standardmäßige Desktop-PC-Konfiguration gemeint ist, die unabhängig von der Rolle für alle Domänenbenutzer bereitgestellt wird.)

| Konfiguration | Vorteile | Nachteile |
| --- | --- | --- |
| Eigenständige Arbeitsstation mit verstärkter Sicherheit |Streng kontrollierte Arbeitsstation |Höhere Kosten für dedizierte Desktops |
| - | Geringeres Risiko für Anwendungs-Exploits |Erhöhter Verwaltungsaufwand |
| - | Klare Aufgabentrennung | - |
| Unternehmens-PC als virtueller Computer |Niedrigere Hardwarekosten | - |
| - | Trennung von Rolle und Anwendungen | - |

Es ist wichtig, dass die Arbeitsstation mit verstärkter Sicherheit der Host und nicht der Gast ist und zwischen dem Hostbetriebssystem und der Hardware keine weiteren Elemente vorhanden sind. Mit dem „Prinzip der sauberen Quelle“ (auch als „sicherer Ursprung“ bezeichnet) ist gemeint, dass der Host die größte Sicherheit aufweisen soll. Andernfalls ist die Arbeitsstation mit verstärkter Sicherheit (Gast) den Angriffen des Systems ausgesetzt, auf dem sie gehostet wird.

Sie können Verwaltungsfunktionen weiter trennen, indem Sie dedizierte Systemimages für jede Arbeitsstation mit verstärkter Sicherheit verwenden, die nur über die erforderlichen Tools und Berechtigungen zum Verwalten ausgewählter Azure- und Cloudanwendungen verfügen. Für die erforderlichen Aufgaben werden spezielle lokale AD DS-GPOs verwendet.

Für IT-Umgebungen ohne lokale Infrastruktur (z. B. kein Zugriff auf eine lokale AD DS-Instanz für GPOs, weil sich alle Server in der Cloud befinden) kann mit einem Dienst wie [Microsoft Intune](/mem/intune/) die Bereitstellung und Verwaltung von Arbeitsstationskonfigurationen vereinfacht werden.

### <a name="stand-alone-hardened-workstation-for-management"></a>Eigenständige Arbeitsstation mit verstärkter Sicherheit für die Verwaltung
Mit einer eigenständigen Arbeitsstation mit verstärkter Sicherheit verfügen Administratoren über einen PC oder Laptop, den sie für Verwaltungsaufgaben nutzen, und einen weiteren separaten PC oder Laptop für andere Aufgaben. Auf einer Arbeitsstation, die für die Verwaltung der Azure-Dienste bestimmt ist, müssen keine anderen Anwendungen installiert sein. Außerdem ist die Verwendung von Arbeitsstationen, die ein [Trusted Platform Module](/previous-versions/windows/it-pro/windows-vista/cc766159(v=ws.10)) (TPM) oder eine ähnliche Technologie für die Verschlüsselung auf Hardwareebene unterstützen, bei der Geräteauthentifizierung und Verhinderung von bestimmten Angriffen hilfreich. Mit TPM kann auch der vollständige Volumeschutz des Systemlaufwerks per [BitLocker-Laufwerkverschlüsselung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) unterstützt werden.

Beim Szenario mit der eigenständigen Arbeitsstation mit verstärkter Sicherheit (unten dargestellt) wird die lokale Instanz der Windows-Firewall (oder einer anderen Clientfirewall, die nicht von Microsoft stammt) so konfiguriert, dass eingehende Verbindungen blockiert werden, z.B. RDP. Der Administrator kann sich an der Arbeitsstation mit verstärkter Sicherheit anmelden und eine RDP-Sitzung starten, für die eine Verbindung mit Azure hergestellt wird, nachdem eine VPN-Verbindung mit einem Azure Virtual Network hergestellt wurde. Es ist aber nicht möglich, sich an einem Unternehmens-PC anzumelden und RDP zu verwenden, um direkt mit der Arbeitsstation mit verstärkter Sicherheit eine Verbindung herzustellen.

![Diagramm mit dem Szenario für die eigenständige Arbeitsstation mit verstärkter Sicherheit](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Unternehmens-PC als virtueller Computer
In Fällen, in denen die Nutzung einer separaten eigenständigen Arbeitsstation mit verstärkter Sicherheit aus Kostengründen oder Gründen der Benutzerfreundlichkeit nicht möglich ist, kann auf der Arbeitsstation mit verstärkter Sicherheit ein virtueller Computer gehostet werden, um nicht administrative Aufgaben durchzuführen.

![Diagramm mit der Arbeitsstation mit verstärkter Sicherheit, die einen virtuellen Computer hostet, um nicht administrative Aufgaben auszuführen](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Um verschiedene Sicherheitsrisiken zu vermeiden, die sich aus der Verwendung einer Arbeitsstation für die Systemverwaltung und andere tägliche Aufgaben ergeben, können Sie einen virtuellen Windows Hyper-V-Computer auf der Arbeitsstation mit verstärkter Sicherheit bereitstellen. Dieser virtuelle Computer kann als Unternehmens-PC verwendet werden. Die Unternehmens-PC-Umgebung kann vom Host isoliert bleiben, um die Angriffsfläche zu reduzieren und die täglichen Aktivitäten des Benutzers (z.B. E-Mail) von sensiblen Verwaltungsaufgaben zu trennen.

Der virtuelle Computer des Unternehmens-PC wird in einem geschützten Bereich ausgeführt und dient zum Bereitstellen von Benutzeranwendungen. Der Host bleibt eine „saubere Quelle“ und setzt strenge Netzwerkrichtlinien im Stammbetriebssystem durch (z.B. das Blockieren des RDP-Zugriffs vom virtuellen Computer).

## <a name="best-practices"></a>Bewährte Methoden
Berücksichtigen Sie die folgenden weiteren Richtlinien, wenn Sie Anwendungen und Daten in Azure verwalten.

### <a name="dos-and-donts"></a>Empfehlungen für die Vorgehensweise
Gehen Sie nicht davon aus, dass andere gängige Sicherheitsanforderungen nicht erfüllt werden müssen, weil eine Arbeitsstation gesperrt wurde. Das potenzielle Risiko ist höher, da Administratorkonten meist über Zugriffsebenen mit erhöhten Rechten verfügen. Die Tabelle unten enthält Beispiele für Risiken und die dazugehörigen sicheren Vorgehensweisen.

| Sie sollten auf keinen Fall | Sie sollten |
| --- | --- |
| Senden Sie Anmeldeinformationen für den Administratorzugriff oder andere geheime Daten (z. B. TLS/SSL- oder Verwaltungszertifikate) nicht per E-Mail. |Sorgen Sie für Vertraulichkeit, indem Sie Kontonamen und Kennwörter mündlich mitteilen (aber nicht per Voicemail), eine Remoteinstallation von Client-/Serverzertifikaten durchführen (per verschlüsselter Sitzung), Downloads von einer geschützten Freigabe durchführen oder diese Daten per Hand mit Wechselmedien verteilen. |
| - | Verwalten Sie die Lebenszyklen Ihrer Verwaltungszertifikate proaktiv. |
| Speichern Sie Kontokennwörter nicht unverschlüsselt oder ohne Hashing im Anwendungsspeicher (z.B. in Tabellen, auf SharePoint-Websites oder auf Dateifreigaben). |Stellen Sie Prinzipien für die Sicherheitsverwaltung und Richtlinien für die Erhöhung der Systemsicherheit auf, und wenden Sie sie auf Ihre Entwicklungsumgebung an. |
| - | Verwenden Sie die Anheftungsregeln des Zertifikats [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751), um den richtigen Zugriff auf Azure SSL/TLS-Websites sicherzustellen. |
| Vermeiden Sie es, Konten und Kennwörter für mehrere Administratoren freizugeben oder Kennwörter für mehrere Benutzerkonten und Dienste zu verwenden. Dies gilt besonders für soziale Medien oder andere nicht administrative Aktivitäten. |Erstellen Sie ein dediziertes Microsoft-Konto, um Ihr Azure-Abonnement zu verwalten (also ein Konto, das nicht für private E-Mails verwendet wird). |
| Senden Sie Konfigurationsdateien nicht per E-Mail. |Konfigurationsdateien und -profile sollten von einer vertrauenswürdigen Quelle installiert werden (z.B. einem verschlüsselten USB-Laufwerk), und nicht über einen Mechanismus, der leicht kompromittiert werden kann, z.B. E-Mail. |
| Verwenden Sie keine unsicheren oder einfachen Anmeldekennwörter. |Erzwingen Sie Richtlinien für sichere Kennwörter, Ablaufzyklen (obligatorische Änderung bei der ersten Nutzung), Konsolentimeouts und automatische Kontosperrungen. Verwenden Sie ein Verwaltungssystem für Clientkennwörter mit Multi-Factor Authentication für den Zugriff auf den Kennworttresor. |
| Machen Sie Verwaltungsports nicht im Internet verfügbar. |Sperren Sie Azure-Ports und IP-Adressen, um den Verwaltungszugriff zu beschränken. Weitere Informationen finden Sie im Whitepaper zur [Azure-Netzwerksicherheit](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| - | Verwenden Sie Firewalls, VPNs und Netzwerkzugriffsschutz für alle Verwaltungsverbindungen. |

## <a name="azure-operations"></a>Azure-Vorgänge
Im Rahmen des Azure-Betriebs von Microsoft verwenden Betriebstechniker und Supportmitarbeiter, die auf die Produktionssysteme von Azure zugreifen, [Arbeitsstation-PCs mit verstärkter Sicherheit und mit virtuellen Computern](#stand-alone-hardened-workstation-for-management), die darauf für den internen Zugriff auf das Unternehmensnetzwerk und Anwendungen (z.B. E-Mail, Intranet usw.) bereitgestellt werden. Alle Arbeitsstationen für die Verwaltung verfügen über TPMs, das Hoststartlaufwerk ist mit BitLocker verschlüsselt, und sie sind Teil einer speziellen Organisationseinheit in der primären Unternehmensdomäne von Microsoft.

Die Verstärkung der Sicherheit des Systems wird per Gruppenrichtlinie mit zentralisierten Softwareupdates durchgesetzt. Für die Überwachung und Analyse werden Ereignisprotokolle (z. B. Sicherheit und AppLocker) von Verwaltungsarbeitsstationen erfasst und an einem zentralen Ort gespeichert.

Zusätzlich werden im Netzwerk von Microsoft dedizierte Jumpboxes mit zweistufiger Authentifizierung verwendet, um eine Verbindung mit dem Produktionsnetzwerk von Azure herzustellen.

## <a name="azure-security-checklist"></a>Checkliste für Azure-Sicherheit
Die Verringerung der Anzahl von Aufgaben, die von Administratoren auf einer Arbeitsstation mit verstärkter Sicherheit durchgeführt werden können, trägt zur Reduzierung der Angriffsfläche in Ihrer Entwicklungs- und Verwaltungsumgebung bei. Verwenden Sie die folgenden Technologien, um die Arbeitsstation mit verstärkter Sicherheit zu schützen:

* Internet Explorer mit verstärkter Sicherheit: Der Internet Explorer-Browser (oder ein beliebiger anderer Webbrowser) ist aufgrund der umfangreichen Interaktionen mit externen Servern ein Haupteinfallstor für Schadcode. Überprüfen Sie Ihre Clientrichtlinien, und erzwingen Sie die Ausführung im geschützten Modus, die Deaktivierung von Add-Ons und Dateidownloads und die Verwendung der [Microsoft SmartScreen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj618329(v=ws.11))-Filterung. Stellen Sie sicher, dass Sicherheitswarnungen angezeigt werden. Nutzen Sie Internetzonen, und erstellen Sie eine Liste mit vertrauenswürdigen Websites, für die Sie die Sicherheit ausreichend verstärkt haben. Blockieren Sie alle anderen Websites und browserinternen Code, z.B. ActiveX und Java.
* Standardbenutzer: Die Ausführung als Standardbenutzer ist mit einer Reihe von Vorteilen verbunden. Der größte Vorteil ist, dass das Stehlen von Administratoranmeldeinformationen per Schadsoftware sehr schwierig wird. Außerdem verfügt ein Standardbenutzerkonto im Stammbetriebssystem nicht über erhöhte Rechte, und viele Konfigurationsoptionen und APIs sind standardmäßig gesperrt.
* AppLocker: Sie können [AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619725(v=ws.10)) verwenden, um die Programme und Skripts einzuschränken, die Benutzer ausführen können. Sie können AppLocker im Überwachungs- oder Erzwingungsmodus ausführen. AppLocker enthält standardmäßig eine Zulassungsregel, mit der Benutzer, die über ein Administratortoken verfügen, auf dem Client jeden Code ausführen können. Mit dieser Regel wird verhindert, dass sich Administratoren aussperren, und sie gilt nur für Token mit erhöhten Rechten. Sehen Sie sich hierzu auch die Informationen zur Codeintegrität im Rahmen der Windows Server [Core Security](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd348705(v=ws.10)) an.
* Codesignierung: Mit der Codesignierung für alle Tools und Skripts, die von Administratoren verwendet werden, erhalten Sie einen verwaltbaren Mechanismus zum Bereitstellen von Richtlinien für die Anwendungssperrung. Hashes werden bei schnellen Änderungen am Code nicht skaliert, und Dateipfade bieten kein hohes Maß an Sicherheit. Es ist ratsam, AppLocker-Regeln mit einer PowerShell-[Ausführungsregel](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)) zu kombinieren, die nur die [Ausführung](/powershell/module/microsoft.powershell.security/set-executionpolicy) speziell signierter Codeabschnitte und Skripts zulässt.
* Gruppenrichtlinie: Erstellen Sie eine globale Verwaltungsrichtlinie, die auf alle für die Verwaltung genutzten Domänenarbeitsstationen ( alle anderen werden blockiert), sowie auf die Benutzerkonten, die auf diesen Arbeitsstationen authentifiziert werden, angewendet wird.
* Bereitstellung mit erhöhter Sicherheit: Schützen Sie Ihr Basisimage für Arbeitsstationen mit verstärkter Sicherheit, um Manipulationen zu verhindern. Setzen Sie auf Sicherheitsmaßnahmen wie die Verschlüsselung und Isolierung, um Images, virtuelle Computer und Skripts zu speichern und den Zugriff zu beschränken (ggf. per überwachtem Aus- und Einchecken).
* Patching: Verwenden Sie einen einheitlichen Build (oder nutzen Sie separate Images für Entwicklung, Betrieb und andere Verwaltungsaufgaben), führen Sie regelmäßige Überprüfungen auf Änderungen und Schadsoftware durch, halten Sie den Build auf dem aktuellen Stand, und aktivieren Sie Computer nur, wenn sie benötigt werden.
* Verschlüsselung: Stellen Sie sicher, dass die Arbeitsstationen für die Verwaltung über ein TPM verfügen, um [Encrypting File System](/previous-versions/tn-archive/cc700811(v=technet.10)) (EFS) und BitLocker mit noch mehr Sicherheit zu ermöglichen.
* Governance (Kontrolle): Verwenden Sie AD DS-Gruppenrichtlinienobjekte, um alle Windows-Schnittstellen der Administratoren, z.B. die Dateifreigabe, zu kontrollieren. Binden Sie Verwaltungsarbeitsstationen in die Prozesse der Bereiche Auditing, Überwachung und Protokollierung ein. Verfolgen Sie für Administratoren und Entwickler den gesamten Zugriff und die Nutzung.

## <a name="summary"></a>Zusammenfassung
Die Verwendung einer Arbeitsstation mit einer Konfiguration für verstärkte Sicherheit zum Verwalten Ihrer Azure-Clouddienste, Virtual Machines und Anwendungen kann zur Vermeidung vieler Risiken und Bedrohungen beitragen, die mit der Remoteverwaltung kritischer IT-Infrastruktur verbunden sein können. Sowohl Azure als auch Windows verfügt über Mechanismen zum Schützen und Kontrollieren der Kommunikation, der Authentifizierung und des Clientverhaltens.

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Ressourcen enthalten allgemeinere Informationen zu Azure und verwandten Microsoft-Diensten sowie speziellen Komponenten, die in diesem Artikel erwähnt wurden:

* [Securing Privileged Access](/windows-server/identity/securing-privileged-access/securing-privileged-access) (Schützen des privilegierten Zugriffs): Enthält die technischen Details zum Entwerfen und Erstellen einer sicheren Verwaltungsarbeitsstation für die Azure-Verwaltung.
* [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure): Enthält Informationen zu Funktionen der Azure-Plattform, mit denen das Azure-Fabric und die unter Azure ausgeführten Workloads geschützt werden.
* [Microsoft Security Response Center](https://www.microsoft.com/msrc): Hier können Sie Microsoft-Sicherheitsrisiken, z. B. Probleme mit Azure, melden oder eine E-Mail an [secure@microsoft.com](mailto:secure@microsoft.com) schreiben.