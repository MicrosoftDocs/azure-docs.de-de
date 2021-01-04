---
title: 'Azure DDoS Protection Standard: häufig gestellte Fragen'
description: Häufig gestellte Fragen über den Azure DDoS Protection Standard, der beim Schutz vor DDoS-Angriffen hilft.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: c09f8c5ae4a742e6caa489ee29043f500617bb24
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746487"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection Standard: häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zu Azure DDoS Protection Standard beantwortet. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Was ist ein Distributed Denial-of-Service-Angriff (DDoS)?
Distributed Denial-of-Service oder DDoS ist eine Art von Angriff, bei dem ein Angreifer mehr Anforderungen an eine Anwendung sendet, als von der Anwendung bewältigt werden kann. Dies führt zu einer Verknappung der Ressourcen, was sich auf die Verfügbarkeit der Anwendung und die Fähigkeit zum Erfüllen der Anforderungen ihrer Benutzer auswirkt. In den letzten Jahren hat die Branche eine deutliche Zunahme derartiger Angriffe erlebt, die immer ausgefeilter und umfangreicher geworden sind. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Was ist der Dienst Azure DDoS Protection Standard?
Azure DDoS Protection Standard, kombiniert mit bewährten Methoden des Anwendungsentwurfs, bietet erweiterte Features zur DDoS-Risikominderung, um vor DDoS-Angriffen zu schützen. Er wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen in einem virtuellen Netzwerk zu schützen. Der Schutz kann einfach in jedem neuen oder vorhandenen virtuellen Netzwerk aktiviert werden und erfordert keine Änderung von Anwendung oder Ressource. Er hat mehrere Vorteile gegenüber dem Basisdienst, z.B. Protokollierung, Warnungen und Telemetrie. Weitere Informationen finden Sie unter [Übersicht über Azure DDoS Protection Standard](ddos-protection-overview.md). 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Was gilt für Schutz auf Dienstebene (Ebene 7)?
Kunden können den Dienst Azure DDoS Protection in Kombination mit einer Web Application Firewall (WAF) zum Schutz sowohl auf Netzwerkebene (Ebene 3 und 4, angeboten von Azure DDoS Protection Standard) als auch auf Anwendungsebene (Ebene 7, angeboten von einer WAF) verwenden. WAF-Angebote umfassen [Azure Application Gateway-WAF-SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sowie Web Application Firewall-Angebote von Drittanbietern, die in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) verfügbar sind.

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Sind Dienste in Azure ohne den Dienst unsicher?
In Azure ausgeführte Dienste sind grundsätzlich durch den Dienst Azure DDoS Protection Basic geschützt, der zum Schutz der Infrastruktur von Azure eingesetzt wird. Der Schutz, der die Infrastruktur absichert, hat jedoch eine viel höhere Schwelle, als die meisten Anwendungen bewältigen können, und bietet keine Telemetrie oder Warnfunktionen. Während also ein Datenverkehrsaufkommen von der Plattform als harmlos eingestuft werden kann, kann es für die Anwendung, die es empfängt, verheerend sein. 

Durch die Integration in den Dienst Azure DDoS Protection Standard wird die Anwendung dediziert zur Erkennung von Angriffen und anwendungsspezifischen Schwellenwerten überwacht. Ein Dienst wird mit einem Profil geschützt, das auf das zu erwartende Datenverkehrsaufkommen abgestimmt ist, was eine wesentlich konsequentere Abwehr von DDoS-Angriffen ermöglicht.

## <a name="what-are-the-supported-protected-resource-types"></a>Welche geschützten Ressourcentypen werden unterstützt?
Öffentliche IP-Adressen in auf Azure Resource Manager (ARM) basierenden virtuellen Netzwerken (VNETs) sind derzeit der einzige geschützte Ressourcentyp. PaaS-Dienste (mit mehreren Mandanten) werden gegenwärtig nicht unterstützt. Weitere Informationen finden Sie unter [Azure DDoS Protection Standard – Referenzarchitekturen](ddos-protection-reference-architectures.md).

## <a name="are-classicrdfe-protected-resources-supported"></a>Werden klassische bzw. durch RDFE geschützte Ressourcen unterstützt?
In der Vorschauphase werden nur ARM-basierte geschützte Ressourcen unterstützt. VMs in klassischen/RDFE-Bereitstellungen werden nicht unterstützt. Unterstützung für klassische/RDFE-Ressourcen ist zurzeit nicht geplant. Weitere Informationen finden Sie unter [Azure DDoS Protection Standard – Referenzarchitekturen](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>Kann ich meine PaaS-Ressourcen mit DDoS Protection schützen?
Öffentliche IP-Adressen, die mehrinstanzenfähigen Einzel-VIP-PaaS-Diensten zugeordnet sind, werden derzeit nicht unterstützt. Beispiele für nicht unterstützte Ressourcen sind Speicher-VIPs, Event Hub-VIPs und App Services-/Cloud Services-Anwendungen. Weitere Informationen finden Sie unter [Azure DDoS Protection Standard – Referenzarchitekturen](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Kann ich meine lokalen Ressourcen mithilfe von DDoS Protection schützen?
Die öffentlichen Endpunkte Ihres Diensts müssen mit einem VNET in Azure verbunden sein, damit sie für DDoS-Schutz aktiviert werden können. Beispiele hierfür sind:
- Websites (IaaS) in Azure und Back-End-Datenbanken im lokalen Rechenzentrum. 
- Application Gateway in Azure (DDoS-Schutz in App Gateway/WAF aktiviert) und Websites in lokalen Rechenzentren.

Weitere Informationen finden Sie unter [Azure DDoS Protection Standard – Referenzarchitekturen](ddos-protection-reference-architectures.md).

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Kann ich eine Domäne außerhalb von Azure registrieren und einer geschützten Ressource wie VM oder ELB zuordnen?
Für die Szenarien mit öffentlichen IP-Adressen unterstützt der Dienst DDoS Protection sämtliche Anwendungen unabhängig davon, wo die zugehörige Domäne registriert ist oder gehostet wird, solange die zugehörige öffentliche IP-Adresse in Azure gehostet wird. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Kann ich die für die VNETs und öffentlichen IP-Adressen geltende DDoS-Richtlinie manuell konfigurieren?
Nein, eine Richtlinienanpassung ist derzeit leider nicht möglich.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Kann ich bestimmte IP-Adressen einer Positivliste/Sperrliste hinzufügen?
Nein, eine manuelle Konfiguration ist derzeit leider nicht möglich.

## <a name="how-can-i-test-ddos-protection"></a>Wie kann ich DDoS Protection testen?
Weitere Informationen finden Sie unter [Durchführen von Simulationstests](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Wie lange dauert das Laden der Metriken in das Portal?
Die Metriken sollten binnen 5 Minuten im Portal sichtbar sein. Wenn Ihre Ressource angegriffen wird, werden andere Metriken innerhalb von 5-7 Minuten im Portal angezeigt. 
    