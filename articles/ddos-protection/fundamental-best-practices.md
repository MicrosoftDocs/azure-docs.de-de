---
title: Grundlegende bewährte Methoden für Azure DDoS Protection
description: Erfahren Sie, welche bewährten Sicherheitsmethoden Sie bei Verwendung von DDoS Protection verwenden sollten.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989472"
---
# <a name="fundamental-best-practices"></a>Grundlegende bewährte Methoden

Die folgenden Abschnitte enthalten eine ausführliche Anleitung zum Erstellen DDoS-resilienter Dienste in Azure.

## <a name="design-for-security"></a>Sicherheitsorientiertes Design

Stellen Sie sicher, dass die Sicherheit im gesamten Lebenszyklus einer Anwendung Priorität hat – von Entwurf und Implementierung bis hin zu Bereitstellung und Betrieb. Anwendungen können Fehler enthalten, die einer relativ geringen Anzahl Anforderungen die übermäßige Verwendung von Ressourcen erlauben. Dies führt zu einem Dienstausfall. 

Um einen in Microsoft Azure ausgeführten Dienst zu schützen, sollten Sie Ihre Anwendungsarchitektur verstehen und sich auf die [five pillars of software quality (Fünf Säulen der Softwarequalität)](/azure/architecture/guide/pillars) konzentrieren.
Sie sollten Folgendes kennen: das typische Datenverkehrsvolumen, das Modell der Konnektivität zwischen der Anwendung und anderen Anwendungen, sowie die Dienstendpunkte, die dem öffentlichen Internet verfügbar gemacht werden.

Es ist von größter Wichtigkeit sicherzustellen, dass eine Anwendung stabil genug ist, um mit einem Denial-of-Service-Angriff fertig zu werden. Sicherheit und Datenschutz sind direkt in der Azure-Plattform integriert, beginnend mit [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Die SDL spricht Sicherheit in jeder Entwicklungsphase an und sorgt dafür, dass Azure kontinuierlich aktualisiert wird, um noch sicherer zu sein.

## <a name="design-for-scalability"></a>Skalierbarkeitsorientiertes Design

Die Skalierbarkeit zeigt, wie gut ein System eine höhere Last verarbeiten kann. Entwerfen der Anwendungen für eine [horizontale Skalierung](/azure/architecture/guide/design-principles/scale-out), um die Anforderungen einer verstärkten Auslastung zu erfüllen – insbesondere im Falle eines DDoS-Angriffs. Wenn Ihre Anwendung von einer einzelnen Instanz eines Diensts abhängig ist, entsteht dadurch ein Single Point of Failure. Durch Bereitstellen mehrerer Instanzen wird Ihr System stabiler und besser skalierbar.

Wählen Sie für [Azure App Service](../app-service/overview.md) einen [App Service-Plan](../app-service/overview-hosting-plans.md) aus, der mehrere Instanzen bietet. Konfigurieren Sie für Azure Cloud Services alle Rollen so, dass sie [mehrere Instanzen](../cloud-services/cloud-services-choose-me.md) verwenden. Stellen Sie für [Azure Virtual Machines](../virtual-machines/index.yml) (VMs) sicher, dass die VM-Architektur mehr als eine VM enthält und dass jede VM zu einer [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) gehört. Sie sollten [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) für Funktionen zur automatischen Skalierung verwenden.

## <a name="defense-in-depth"></a>Tiefgehende Verteidigung

Hinter der tiefgehenden Verteidigung steht die Idee, dem Risiko mit verschiedenen Verteidigungsstrategien zu begegnen. Abwehrmaßnahmen in die Schichten einer Anwendung zu integrieren reduziert die Wahrscheinlichkeit eines erfolgreichen Angriffs. Sie sollten sichere Entwürfe für Ihre Anwendungen mithilfe der integrierten Funktionen der Azure-Plattform implementieren.

Das Risiko von Angriffen steigt z.B. mit der Größe (*Oberflächenbereich*) der Anwendung. Sie können den Oberflächenbereich durch Verwendung einer Genehmigungsliste verringern, um den verfügbar gemachten IP-Adressraum und die Überwachungsports zu schließen, die im Lastenausgleichsmodul ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) und [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)) nicht mehr benötigt werden. Durch [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md) kann die angreifbare Oberfläche ebenfalls reduziert werden.
Sie können mit [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) und [Anwendungssicherheitsgruppen](../virtual-network/network-security-groups-overview.md#application-security-groups) das Erstellen von Sicherheitsregeln weniger komplex machen und Netzwerksicherheit als natürliche Erweiterung der Struktur einer Anwendung konfigurieren.

Sie sollten Azure-Dienste nach Möglichkeit in einem [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) bereitstellen. Mit dieser Methode können Dienstressourcen über private IP-Adressen kommunizieren. Standardmäßig werden für Datenverkehr von Azure-Diensten aus einem virtuellen Netzwerk öffentliche IP-Adressen als Quell-IP-Adressen verwendet. Bei Verwendung von [Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) wechselt der Dienstdatenverkehr zu privaten Adressen im virtuellen Netzwerk als Quell-IP-Adressen, wenn aus einem virtuellen Netzwerk auf den Azure-Dienst zugegriffen wird.

Häufig werden lokale Ressourcen von Kunden zusammen mit ihren Azure-Ressourcen angegriffen. Wenn Sie die Verbindung einer lokalen Umgebung mit Azure herstellen, sollten Sie dem öffentlichen Internet lokale Ressourcen so wenig wie möglich verfügbar machen. Sie können die Skalierungsfunktionen und erweiterten Funktionen zum DDoS-Schutz von Azure durch Bereitstellung Ihrer gut bekannten öffentlichen Entitäten in Azure verwenden. Da diese öffentlich zugänglichen Entitäten häufig ein Ziel von DDoS-Angriffen sind, reduziert ihre Unterbringung in Azure die Auswirkungen auf Ihre lokalen Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen DDoS-Schutzplan erstellen](manage-ddos-protection.md).