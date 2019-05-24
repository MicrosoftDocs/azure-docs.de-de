---
title: Includedatei
description: Includedatei
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111566"
---
| | Standard-SKU | Basic-SKU |
| --- | --- | --- |
| Größe des Back-End-Pools | Unterstützt bis zu 1.000 Instanzen | Unterstützt bis zu 100 Instanzen |
| Back-End-Pool-Endpunkte | Jeder virtuelle Computer in einem einzelnen virtuellen Netzwerk, einschließlich Kombinationen aus virtuellen Computern, Verfügbarkeitsgruppen, VM-Skalierungsgruppen | Virtuelle Computer in einer einzelnen Verfügbarkeitsgruppe oder VM-Skalierungsgruppe |
| [Integritätstests](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Verhalten bei Ausfall während Integritätstest](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-Verbindungen bleiben bei Ausfällen während Instanztests __und__ bei Ausfällen aller Tests bestehen. | TCP-Verbindungen bleiben bei Ausfällen während Instanztests bestehen. Alle TCP-Verbindungen werden bei Ausfällen aller Tests beendet. |
| Verfügbarkeitszonen | In Standard-SKU, zonenredundante und zonale Front-Ends für eingehende und ausgehende Datenflüsse, Zuordnungen für ausgehende Datenflüsse bleiben bei Zonenausfällen erhalten, zonenübergreifender Lastenausgleich. | Nicht verfügbar. |
| Diagnose | Azure Monitor, mehrdimensionale Metriken einschließlich Byte- und Paketleistungsindikatoren, Integritätsteststatus, Verbindungsversuche (TCP SYN), Integrität ausgehender Verbindungen (erfolgreiche und fehlgeschlagene SNAT-Datenströme), Messungen für die aktive Datenebene | Azure Log Analytics nur für öffentlichen Lastenausgleich, SNAT-Überlastungswarnung, Integritätsanzahl für Back-End-Pool |
| HA-Ports | Interner Lastenausgleich | Nicht verfügbar. |
| Standardmäßig sicher | Öffentliche IP, öffentliche Load Balancer-Endpunkte und interne Load Balancer-Endpunkte sind für eingehende Datenflüsse geschlossen, es sei denn, sie werden von einer Netzwerksicherheitsgruppe in die Whitelist aufgenommen. | Standardmäßig geöffnet, Netzwerksicherheitsgruppe ist optional. |
| [Ausgehende Verbindungen](../articles/load-balancer/load-balancer-outbound-connections.md) | Sie können die poolbasierte ausgehende Netzwerkadressenübersetzung (NAT) explizit mit [Regeln für ausgehenden Datenverkehr](../articles/load-balancer/load-balancer-outbound-rules-overview.md) definieren. Sie können mehrere Front-Ends mit Deaktivierung über Lastenausgleichsregel verwenden. Es _muss_ explizit ein Szenario für ausgehenden Datenverkehr erstellt werden, damit der virtuelle Computer, die Verfügbarkeitsgruppe und die VM-Skalierungsgruppe ausgehende Verbindungen verwenden können.  VNET-Dienstendpunkte können erreicht werden, ohne dass ausgehende Verbindungen definiert werden müssen, und werden nicht für verarbeitete Daten angerechnet.  Alle öffentlichen IP-Adressen, einschließlich der Azure-PaaS-Dienste, die nicht als VNet-Dienstendpunkte verfügbar sind, müssen über ausgehende Verbindungen erreicht werden und werden als verarbeitete Daten angerechnet. Wenn nur ein interner Load Balancer einen virtuellen Computer, eine Verfügbarkeitsgruppe oder eine VM-Skalierungsgruppe versorgt, sind ausgehende Verbindungen nicht über Standard-SNAT verfügbar. Verwenden Sie stattdessen [Ausgangsregeln](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Ausgehende SNAT-Programmierung ist transportprotokollspezifisch entsprechend dem Protokoll der Regel für eingehenden Lastenausgleich. | Einzelnes Front-End, ausgewählt nach dem Zufallsprinzip, wenn mehrere Front-Ends vorhanden sind.  Wenn nur ein interner Load Balancer einen virtuellen Computer, eine Verfügbarkeitsgruppe oder eine VM-Skalierungsgruppe versorgt, wird Standard-SNAT verwendet. |
| [Regeln für ausgehenden Datenverkehr](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarative NAT-Konfiguration für ausgehenden Datenverkehr, Verwendung öffentlicher IP-Adressen oder der Präfixe öffentlicher IP-Adressen oder von beidem, konfigurierbares Leerlauftimeout (zwischen vier und 120 Minuten) für ausgehenden Datenverkehr, benutzerdefinierte SNAT-Portzuordnung. | Nicht verfügbar. |
|  [TCP-Zurücksetzung bei Leerlauf](../articles/load-balancer/load-balancer-tcp-reset.md) | Aktivieren von TCP-Zurücksetzung (TCP RST) bei Leerlauftimeout bei einer beliebigen Regel | Nicht verfügbar |
| [Mehrere Front-Ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Eingehend und [ausgehend](../articles/load-balancer/load-balancer-outbound-connections.md) | Nur eingehend |
| Verwaltungsvorgänge | Die meisten Vorgänge < 30 Sekunden | Meist 60 bis 90 (oder mehr) Sekunden |
| SLA | 99,99 % für Datenpfad mit zwei fehlerfreien virtuellen Computern | Nicht zutreffend | 
| Preise | Berechnung anhand der Anzahl der Regeln, der Daten, die eingehend und ausgehend verarbeitet werden, die der Ressource zugeordnet sind.  | Kostenlos |
|  |  |  |
