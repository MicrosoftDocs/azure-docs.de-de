---
title: VMware-Lösung von CloudSimple – öffentliche Azure-IP-Adressen
description: Erfahren Sie mehr über öffentliche IP-Adressen und deren Vorteile für die VMware-Lösung von CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f57b7397f4a2d288cd2b8b55cf23b2d635aa5f8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209567"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Öffentliche CloudSimple-IP-Adresse – Übersicht

Eine öffentliche IP-Adresse ermöglicht es Internetressourcen, eingehende Daten an Ressourcen in einer privaten Cloud mit einer privaten IP-Adresse zu senden. Die private IP-Adresse gehört entweder zu einem virtuellen Computer oder zu einem softwarebasierten Lastenausgleich (Software Load Balancer). Die private IP-Adresse befindet sich im vCenter Ihrer privaten Cloud. Über die öffentliche IP-Adresse können Sie Dienste, die in Ihrer privaten Cloud ausgeführt werden, für das Internet verfügbar machen.

Die öffentliche IP-Adresse ist solange der privaten IP-Adresse zugewiesen, bis Sie diese Zuweisung aufgehoben haben. Eine öffentliche IP-Adresse kann nur einer privaten IP-Adresse zugewiesen werden.

Für eine Ressource, die mit einer öffentlichen IP-Adresse verknüpft ist, wird für Internetzugriff immer die öffentliche IP-Adresse verwendet. Standardmäßig ist nur ausgehender Internetzugriff für eine öffentliche IP-Adresse zulässig.  Eingehender Datenverkehr über die öffentliche IP-Adresse wird abgelehnt.  Um eingehenden Datenverkehr zuzulassen, erstellen Sie eine Firewallregel für die öffentliche IP-Adresse am entsprechenden Port.

## <a name="benefits"></a>Vorteile

Das Verwenden einer öffentlichen IP-Adresse für die eingehende Kommunikation bietet:

* Schutz vor DDoS-Angriffen (verteilte Denial-of-Service-Angriffe). Dieser Schutz wird automatisch für die öffentliche IP-Adresse aktiviert.
* Ununterbrochene Datenverkehrsüberwachung sowie Echtzeit-Risikominderung von häufig vorkommenden Angriffen auf Netzwerkebene. Diese Schutzmaßnahmen sind mit denen identisch, die von Microsoft Online Services verwendet werden.
* Die gesamte Skalierung des globalen Azure-Netzwerks. Das Netzwerk kann verwendet werden, um den aus Angriffen resultierenden Datenverkehr über Regionen zu verteilen und zu minimieren.  

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Zuordnen einer öffentlichen IP-Adresse](https://docs.azure.cloudsimple.com/public-ips/).
