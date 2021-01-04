---
title: Einrichten des Peerings mit Microsoft
titleSuffix: Azure
description: Übersicht über Peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586865"
---
# <a name="internet-peering-overview"></a>Übersicht über Internetpeering

Peering ist die Verbindung zwischen dem globalen Netzwerk von Microsoft (AS8075) und Ihrem Netzwerk, um ein-/ausgehenden Internetdatenverkehr für Microsoft-Onlinedienste und Microsoft Azure-Dienste auszutauschen. Netzbetreiber und Dienstanbieter können eine Verbindung mit Microsoft an einem unserer Edgestandorte anfordern. Jede Anforderung wird von Microsoft geprüft, um sicherzustellen, dass sie unserer Peeringrichtlinie entspricht. Ein Peering mit dem Microsoft-Netzwerk kann auf zwei Arten eingerichtet werden:

* **Direct Peering:**

    Das Peering wird über direkte physische Verbindungen zwischen dem Microsoft-Netzwerk an einem Microsoft-Edgestandort und Ihrem Netzwerk eingerichtet. BGP-Sitzungen werden über diese Verbindungen gemäß unserer Routingrichtlinie und unter Verwendung einer vorab ausgehandelten Vereinbarung konfiguriert. Dies wird auch als PNI bezeichnet.

* **Exchange Peering:**

    Hierbei handelt es sich um standardmäßige öffentliche Peeringverbindungen an Internetknoten (Internet Exchanges, IXs). Die physischen Verbindungen zwischen dem Microsoft-Netzwerk und Ihrem Netzwerk basieren auf einer vom IX betriebenen Switch-Struktur. BGP-Sitzungen werden mit dem vom IX bereitgestellten IP-Adressbereich konfiguriert.

## <a name="benefits-of-peering-with-microsoft"></a>Vorteile des Peerings mit Microsoft
* Senkung Ihrer Transitkosten durch Übermittlung von Microsoft-Datenverkehr mittels Peering mit Microsoft
* Verbesserung der Leistung für Ihre Kunden durch Verringerung von Netzwerkhops und Wartezeit für das Microsoft Edge-Netzwerk
* Schutz des Datenverkehrs von Kunden vor Ausfällen in Ihrem Netzwerk oder im Netzwerk des Transitanbieters durch Peering mit Microsoft an redundanten Standorten
* Leistungsmetriken für Ihre Peeringverbindungen sowie Erkenntnisse zur Behandlung von Netzwerkproblemen

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Vorteile der Peeringeinrichtung mithilfe von Azure

Das Peering mit Microsoft kann per Azure PowerShell oder über das Portal angefordert werden. Wenn das Peering auf diese Weise eingerichtet wird, wird es als Azure-Ressource verwaltet, was folgende Vorteile hat:
* Vereinfachte und automatisierbare Schritte zum Einrichten und Verwalten des Peering mit Microsoft
* Schnelle und einfache Anzeige und Verwaltung sämtlicher Peerings an einem zentralen Ort
* Nachverfolgung von Status- und Bandbreitendaten für alle Ihre Verbindungen
* Zugriff auf Azure Cloud Services mithilfe des gleichen Abonnements

Wenn Sie bereits Peerings mit Microsoft eingerichtet haben, werden diese als **Legacypeerings** bezeichnet. Solche Peerings können auf Wunsch als Azure-Ressource verwaltet werden, um von den oben genannten Vorteilen zu profitieren. Informationen zum Übermitteln einer neuen Peeringanforderung oder zum Konvertieren eines Legacypeerings in eine Azure-Ressource finden Sie weiter unten im Abschnitt **Nächste Schritte** unter dem entsprechenden Link.

## <a name="peering-policy"></a>Peeringrichtlinie
Microsoft hat eine selektive, aber grundsätzlich offene Peeringrichtlinie. Peers werden auf der Grundlage von Leistung, Potenzial und beiderseitigem Nutzen ausgewählt und unterliegen bestimmten technischen, kommerziellen und rechtlichen Einschränkungen. Ausführliche Informationen finden Sie unter [Peeringrichtlinie](policy.md).

## <a name="faq"></a>Häufig gestellte Fragen
Häufig gestellte Fragen zu Peering finden Sie unter [Internetpeering: Häufig gestellte Fragen](faqs.md).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einrichten des direkten Peerings mit Microsoft finden Sie in der [exemplarischen Vorgehensweise für direktes Peering](walkthrough-direct-all.md).
* Informationen zum Einrichten des Austauschpeerings mit Microsoft finden Sie in der [exemplarischen Vorgehensweise für Austauschpeering](walkthrough-exchange-all.md).
* Informationen zu einigen anderen zentralen Azure-Netzwerkfunktionen finden Sie [hier](../networking/networking-overview.md).