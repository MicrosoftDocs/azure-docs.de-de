---
title: Referenzarchitektur für Azure DevTest Labs für ein Unternehmen
description: Dieser Artikel enthält einen Leitfaden für eine Azure DevTest Labs-Referenzarchitektur in einem Unternehmen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244719"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs: Referenzarchitektur für Unternehmen
Dieser Artikel enthält eine Referenzarchitektur als Unterstützung für die Bereitstellung einer Azure DevTest Labs-basierten Lösung in einem Unternehmen. Dies umfasst Folgendes:
- Lokale Konnektivität mithilfe von Azure ExpressRoute
- Ein Remotedesktopgateway zur Remoteanmeldung bei virtuellen Computern
- Verbindungen zu einem Artefaktrepository für private Artefakte
- Weitere in Labs verwendete PaaS-Dienste

![Diagramm zur Referenzarchitektur](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
Dies sind die Schlüsselelemente der Referenzarchitektur:

- **Azure Active Directory (Azure AD)** : DevTest Labs verwendet den [Azure AD-Dienst für die Identitätsverwaltung](../active-directory/fundamentals/active-directory-whatis.md). Berücksichtigen Sie diese beiden Hauptaspekte, wenn Sie Benutzern Zugriff auf eine Umgebung gewähren, die auf DevTest Labs basiert:
    - **Ressourcenverwaltung:** Ermöglicht den Zugriff auf das Azure-Portal, um Ressourcen zu verwalten. Dies umfasst unter anderem das Erstellen virtueller Computer und das Erstellen von Umgebungen sowie das Starten, Beenden, Neustarten, Löschen und Anwenden von Artefakten. Die Ressourcenverwaltung erfolgt in Azure über die rollenbasierte Zugriffssteuerung (RBAC). Sie weisen Rollen den Benutzern zu und legen Ressourcen- und Zugriffsberechtigungen fest.
    - **Virtuelle Computer (Netzwerkebene):** In der Standardkonfiguration verwenden virtuelle Computer ein lokales Administratorkonto. Ist eine Domäne verfügbar ([Azure AD Domain Services](../active-directory-domain-services/overview.md), eine lokale Domäne oder eine cloudbasierte Domäne), können Computer in diese Domäne eingebunden werden. Benutzer können dann ihre domänenbasierte Identität verwenden, um eine Verbindung mit den virtuellen Computern herzustellen.
- **Lokale Konnektivität**: In unserem Architekturdiagramm wird [ExpressRoute](../expressroute/expressroute-introduction.md) verwendet. Sie können aber auch eine [Site-to-Site-VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)-Verbindung verwenden. Obwohl ExpressRoute für DevTest Labs nicht erforderlich ist, wird es häufig in Unternehmen verwendet. ExpressRoute ist nur erforderlich, wenn Sie Zugriff auf Unternehmensressourcen benötigen. Häufige Szenarien:
    - Sie verfügen über lokale Daten, die nicht in die Cloud verschoben werden können.
    - Sie bevorzugen das Einbinden der virtuellen Computer des Labs in die lokale Domäne.
    - Sie möchten erzwingen, dass der gesamte ein- und ausgehende Netzwerkdatenverkehr der Cloudumgebung aus Sicherheits-/Konformitätsgründen eine lokale Firewall durchläuft.
- **Netzwerksicherheitsgruppen**: Eine gängige Methode, um den Datenverkehr an die Cloudumgebung (oder innerhalb der Cloudumgebung) auf der Grundlage von Quell- und Ziel-IP-Adressen einzuschränken, ist die Verwendung einer [Netzwerksicherheitsgruppe](../virtual-network/security-overview.md). Sie möchten z. B. nur Datenverkehr, der aus dem Unternehmensnetzwerk stammt, in den Netzwerken des Labs erlauben.
- **Remotedesktopgateway:** Unternehmen blockieren ausgehende Remotedesktopverbindungen in der Regel an der Unternehmensfirewall. Es gibt verschiedene Optionen, um die Konnektivität mit der cloudbasierten Umgebung in den DevTest Labs zu ermöglichen, einschließlich:
  - Verwenden Sie ein [Remotedesktopgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), und fügen Sie die statische IP-Adresse des Gatewaylastenausgleichs zur Whitelist hinzu.
  - [Leiten Sie den gesamten eingehenden RDP-Verkehr](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) über die ExpressRoute-/Site-to-Site-VPN-Verbindung. Diese Funktionalität ist eine häufige Überlegung, wenn Unternehmen eine DevTest Labs-Bereitstellung planen.
- **Netzwerkdienste (virtuelle Netzwerke, Subnetze)** : Die Topologie des [Azure-Netzwerks](../networking/networking-overview.md) ist ein weiteres wichtiges Element der DevTest Labs-Architektur. Sie steuert, ob Ressourcen aus dem Lab mit lokalen Standorten und dem Internet kommunizieren können und darauf Zugriff haben. Unser Architekturdiagramm enthält die gängigsten Methoden, wie Kunden DevTest Labs nutzen: Alle Labs werden über [virtuelles Netzwerkpeering](../virtual-network/virtual-network-peering-overview.md) mithilfe eines [Hub-Spoke-Modells](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) mit der ExpressRoute-/Site-toto-Site-VPN-Verbindung mit lokalen Standorten verbunden. DevTest Labs verwendet Azure Virtual Network jedoch direkt, sodass es keine Einschränkungen bei der Einrichtung der Netzwerkinfrastruktur gibt.
- **DevTest Labs:**  DevTest Labs ist ein wichtiger Bestandteil der allgemeinen Architektur. Weitere Informationen zu diesem Dienst finden Sie unter [Informationen zu Azure DevTest Labs](devtest-lab-overview.md).
- **Virtuelle Computer und andere Ressourcen (SaaS, PaaS und IaaS):**  Virtuelle Computer sind eine wichtige Workload, die von DevTest Labs zusammen mit anderen Azure-Ressourcen unterstützt wird. Mit DevTest Labs können Unternehmen schnell und einfach Zugriff auf Azure-Ressourcen gewähren (einschließlich virtueller Computer und anderer Azure-Ressourcen). Weitere Informationen zum Zugriff auf Azure finden Sie [hier](devtest-lab-developer-lab.md) (für Entwickler) bzw. [hier](devtest-lab-test-env.md) (für Tester).

## <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit
DevTest Labs verfügt zwar nicht über integrierte Kontingente oder Grenzwerte, für andere Azure-Ressourcen, die in typischen Vorgängen eines Labs genutzt werden, gelten jedoch [Kontingente auf Abonnementebene](../azure-subscription-service-limits.md). In einer typischen Unternehmensbereitstellung sind daher mehrere Azure-Abonnements erforderlich, um eine umfangreiche Bereitstellung von DevTest Labs abzudecken. Zu den Kontingenten, die von Unternehmen am häufigsten erreicht werden, zählen folgende:

- **Ressourcengruppen**: In der Standardkonfiguration erstellt DevTest Labs jeweils eine Ressourcengruppe für jeden neuen virtuellen Computer oder der Benutzer erstellt eine Umgebung, indem er den Dienst verwendet. Abonnements können [bis zu 980 Ressourcengruppen](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) enthalten. Das sind also die Grenzwerte für virtuelle Computer und Umgebungen in einem Abonnement. Darüber hinaus sollten noch zwei weitere Konfigurationen berücksichtigt werden:
    - **[Alle virtuellen Computer in einer Ressourcengruppe:](resource-group-control.md)** Dieses Setup hilft zwar bei der Einhaltung des Ressourcengruppenlimits, wirkt sich aber auf das Ressourcentyplimit pro Ressourcengruppe aus.
    - **Verwenden gemeinsamer öffentlicher IP-Adressen:** Alle virtuellen Computer mit gleicher Größe und Region werden in derselben Ressourcengruppe platziert. Diese Konfiguration stellt einen Mittelweg zwischen Ressourcengruppenkontingenten und Ressourcentypkontingenten pro Ressourcengruppe dar, wenn virtuelle Computer öffentliche IP-Adressen besitzen dürfen.
- **Ressourcen pro Ressourcengruppe, pro Ressourcentyp**: Das Standardlimit für [Ressourcen pro Ressourcengruppe, pro Ressourcentyp](../azure-subscription-service-limits.md#resource-group-limits) liegt bei 800.  Bei Verwendung der Konfiguration *Alle virtuellen Computer in einer Ressourcengruppe* wird dieses Abonnementlimit deutlich früher erreicht – insbesondere, wenn die virtuellen Computer über zahlreiche zusätzliche Datenträger verfügen.
- **Speicherkonten**: Ein Lab in DevTest Labs wird mit einem Speicherkonto bereitgestellt. Das Azure-Kontingent für [Anzahl von Speicherkonten pro Region und Abonnement beträgt 250](../azure-subscription-service-limits.md#storage-limits). Die maximale Anzahl der DevTest Labs in derselben Region beträgt ebenfalls 250.
- **Rollenzuweisungen:** Mit einer Rollenzuweisung wird einem Benutzer oder Dienstprinzipal Zugriff auf eine Ressource gewährt (Besitzer, Ressource, Berechtigungsstufe). In Azure gilt ein [Limit von 2.000 Rollenzuweisungen pro Abonnement](../azure-subscription-service-limits.md#role-based-access-control-limits). Standardmäßig erstellt der DevTest Labs-Dienst eine Ressourcengruppe für jeden virtuellen Computer. Der Besitzer erhält die Berechtigung *Besitzer* für die DevTest Labs-VM und *Leser* für die Ressourcengruppe. Auf diese Weise verwendet jede von Ihnen neu erstellte VM zusätzlich zu den Zuweisungen, die verwendet werden, wenn Sie den Benutzern die Berechtigung für das Lab erteilen, zwei Rollenzuweisungen.
- **API-Lesevorgänge/-Schreibvorgänge:** Es gibt verschiedene Möglichkeiten, Azure und DevTest Labs zu automatisieren, einschließlich REST-APIs, PowerShell, Azure CLI und Azure SDK. Durch die Automatisierung können Sie einen anderen Grenzwert für API-Anforderungen erreichen: Pro Abonnement sind bis zu [12.000 Leseanforderungen und 1.200 Schreibanforderungen pro Stunde](../azure-resource-manager/resource-manager-request-limits.md) zulässig. Berücksichtigen Sie diesen Grenzwert bei der Automatisierung von DevTest Labs.

## <a name="manageability-considerations"></a>Überlegungen zur Verwaltbarkeit
Die administrative Benutzeroberfläche von DevTest Labs eignet sich hervorragend für die Arbeit mit einem einzelnen Lab. In einem Unternehmen sind jedoch wahrscheinlich mehrere Azure-Abonnements und zahlreiche Labs vorhanden. Konsistente Änderungen an allen Labs müssen daher mithilfe von Skripting/Automatisierung vorgenommen werden. Im Anschluss folgen einige Beispiele und bewährte Methoden für die Verwaltung einer DevTest Labs-Bereitstellung:

- **Änderungen an Labeinstellungen:** Ein gängiges Szenario ist die Aktualisierung einer bestimmten Labeinstellung in allen Labs der Bereitstellung. Dies kann beispielsweise erforderlich sein, wenn eine neue VM-Instanzgröße verfügbar wird und alle Labs aktualisiert werden müssen, um sie zuzulassen. Es empfiehlt sich, Änderungen dieser Art mithilfe von PowerShell-Skripts, über die Befehlszeilenschnittstelle oder mithilfe von REST-APIs zu automatisieren.  
- **Persönliches Zugriffstoken für das Artefaktrepository:**  Typischerweise verfallen persönliche Zugriffstoken für ein Git-Repository nach 90 Tagen, einem Jahr oder zwei Jahren. Zur Sicherstellung der Kontinuität ist es wichtig, das persönliche Zugriffstoken zu erweitern oder ein neues zu erstellen. Verwenden Sie dann die Automatisierung, um das neue persönliche Zugriffstoken auf alle Labs anzuwenden.
- **Einschränken von Änderungen an einer Labeinstellung**: Häufig muss eine bestimmte Einstellung eingeschränkt werden (z. B. die Verwendung von Marketplace-Bildern). Sie können Änderungen an einem Ressourcentyp mithilfe von Azure Policy verhindern. Oder Sie können eine benutzerdefinierte Rolle erstellen und Benutzern diese Rolle anstelle der Rolle *Besitzer* für das Lab gewähren. Diese Möglichkeit steht für nahezu alle Einstellungen im Lab (interner Support, Labankündigung, zulässige VM-Größen usw.) zur Verfügung.
- **Vorschreiben einer Namenskonvention für virtuelle Computer**: Manager möchten virtuelle Computer, die Teil der cloudbasierten Entwicklungs- und Testumgebung sind, in der Regel problemlos identifizieren können. Das können Sie mithilfe von [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns) erreichen.

Es ist wichtig zu beachten, dass DevTest Labs die zugrunde liegenden Azure-Ressourcen verwendet, die auf dieselbe Weise verwaltet werden: Netzwerke, Datenträger, Computer usw. Azure Policy gilt z. B. für virtuelle Computer, die in einem Lab erstellt werden. Informationen zur Compliance virtueller Computer finden Sie im Azure Security Center. Der Azure Backup-Dienst kann regelmäßige Sicherungen für virtuelle Computer im Lab erstellen.

## <a name="security-considerations"></a>Sicherheitshinweise
Azure DevTest Labs verwendet vorhandene Ressourcen in Azure (Compute, Netzwerke usw.). So profitiert es automatisch von den in die Plattform integrierten Sicherheitsfeatures. Wenn Sie also z. B. für alle eingehenden Remotedesktopverbindungen nur Verbindungen aus dem Unternehmensnetzwerk zulassen möchten, müssen Sie dazu lediglich dem virtuellen Netzwerk auf dem Remotedesktopgateway eine Netzwerksicherheitsgruppe hinzufügen. Die einzige zusätzliche Sicherheitsüberlegung ist die Berechtigungsebene, die Sie Teammitgliedern gewähren, die die Labs täglich nutzen. Die häufigsten Berechtigungen sind [*Besitzer* und *Benutzer*](devtest-lab-add-devtest-user.md). Weitere Informationen zu diesen Rollen finden Sie unter [Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den nächsten Artikel in dieser Reihe: [Zentrales Hochskalieren Ihrer Azure DevTest Labs-Infrastruktur](devtest-lab-guidance-scale.md)
