---
title: 'Azure Cache for Redis: Netzwerkisolationsoptionen'
description: In diesem Artikel erfahren Sie, wie Sie die beste Lösung für die Netzwerkisolation gemäß Ihren Anforderungen ermitteln. Wir behandeln die Grundlagen von Azure Private Link, Azure Virtual Network-Einfügung (VNet) und Azure Firewall-Regeln mit ihren Vorteilen und Einschränkungen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621517"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure Cache for Redis: Netzwerkisolationsoptionen 
In diesem Artikel erfahren Sie, wie Sie die beste Lösung für die Netzwerkisolation gemäß Ihren Anforderungen ermitteln. Wir behandeln die Grundlagen von Azure Private Link, Azure Virtual Network-Einfügung (VNet) und Azure Firewall-Regeln mit ihren Vorteilen und Einschränkungen.  

## <a name="azure-private-link-public-preview"></a>Azure Private Link (öffentliche Vorschau) 
Azure Private Link stellt eine private Verbindung zwischen einem virtuellen Netzwerk und Azure-PaaS-Diensten (Platform-as-a-Service) her. Dadurch wird die Netzwerkarchitektur vereinfacht und die Verbindung zwischen Endpunkten in Azure wird geschützt, indem die Offenlegung von Daten im öffentlichen Internet verhindert wird. 

### <a name="advantages"></a>Vorteile
* Unterstützt auf Basic-, Standard- und Premium-Instanzen von Azure Cache for Redis. 
* Mithilfe von [Azure Private Link](../private-link/private-link-overview.md) können Sie aus Ihrem virtuellen Netzwerk über einen privaten Endpunkt, dem eine private IP-Adresse in einem Subnezt innerhalb des virtuellen Netzwerks zugewiesen ist, eine Verbindung mit einer Azure Cache-Instanz herstellen. Dadurch sind Cache-Instanzen sowohl innerhalb des VNet als auch öffentlich verfügbar.  
* Nachdem ein privater Endpunkt erstellt wurde, kann der Zugriff auf das öffentliche Netzwerk über das `publicNetworkAccess`-Flag eingeschränkt werden. Dieses Flag wird standardmäßig auf `Disabled` festgelegt, wodurch nur Zugriff über private Verbindungen zulässig ist. Sie können den Wert mit einer PATCH-Anforderung auf `Enabled` oder `Disabled` festlegen. Weitere Informationen finden Sie unter [Azure Cache for Redis mit Azure Private Link (Vorschau)](cache-private-link.md). 
* Keine der externen Cacheabhängigkeiten haben Auswirkungen auf die Netzwerksicherheitsgruppen-Regeln des VNet.

### <a name="limitations"></a>Einschränkungen 
* Netzwerksicherheitsgruppen (NSGs) sind für private Endpunkte deaktiviert. Wenn jedoch andere Ressourcen im Subnetz vorhanden sind, gilt die Netzwerksicherheitsgruppen-Erzwingung für diese Ressourcen.
* Georeplikation, Firewallregeln, Unterstützung der Portalkonsole, mehrere Endpunkte pro gruppiertem Cache, Persistenz in der Firewall und in VNet eingefügte Caches werden noch nicht unterstützt. 
* Zum Herstellen einer Verbindung mit einem gruppierten Cache muss `publicNetworkAccess` auf `Disabled` festgelegt werden, und es kann nur eine private Endpunktverbindung geben.

> [!NOTE]
> Beim Hinzufügen eines privaten Endpunkts zu einer Cache-Instanz wird der gesamte Redis-Datenverkehr wegen des DNS auf den privaten Endpunkt verschoben.
> Stellen Sie sicher, dass vorherige Firewallregeln vorher angepasst werden.  
>
>

## <a name="azure-virtual-network-injection"></a>Einfügen in Azure Virtual Network 
Ein VNet ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. VNet ermöglicht zahlreichen Azure-Ressourcen die sichere Kommunikation untereinander sowie mit dem Internet und mit lokalen Netzwerken. VNet ist wie ein herkömmliches Netzwerk, das Sie in Ihrem Rechenzentrum betreiben würden, bietet jedoch die Vorteile der Infrastruktur von Azure, also Skalierbarkeit, Verfügbarkeit und Isolation. 

### <a name="advantages"></a>Vorteile
* Wenn eine Azure Cache for Redis-Instanz mit einem VNet konfiguriert wird, ist dieses nicht öffentlich adressierbar, und auf das VNet kann nur über virtuelle Computer und Anwendungen innerhalb des VNet zugegriffen werden.  
* Durch die Kombination von VNet und eingeschränkten NSG-Richtlinien kann das Risiko der Datenexfiltration verringert werden. 
* Die Bereitstellung über VNet bietet ein höheres Maß an Sicherheit und Isolation sowohl für Ihre Azure Cache for Redis-Instanz als auch für Ihre Subnetze. Sie profitieren außerdem von Richtlinien für die Zugriffssteuerung sowie von weiteren Features zur Begrenzung des Zugriffs. 
* Geografische Replikation wird unterstützt. 

### <a name="limitations"></a>Einschränkungen
* In VNet eingefügte Caches sind nur für Azure Cache for Redis Premium verfügbar. 
* Wenn Sie einen in VNet eingefügten Cache verwenden, müssen Sie Ihr VNet öffnen, um Abhängigkeiten wie CRLs/PKI, AKV, Azure Storage, Azure Monitor und mehr zwischenzuspeichern.  


## <a name="azure-firewall-rules"></a>Azure Firewall-Regeln
[Azure Firewall](../firewall/overview.md) ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst zum Schutz Ihrer Azure VNet-Ressourcen. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Sie können Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren.  

### <a name="advantages"></a>Vorteile
* Wenn Firewallregeln konfiguriert werden, können nur Clientverbindungen aus dem angegebenen IP-Adressbereich eine Verbindung mit dem Cache herstellen. Verbindungen von Azure Cache for Redis-Überwachungssystemen werden immer zugelassen, auch wenn Firewallregeln konfiguriert sind. Netzwerksicherheitsgruppen-Regeln, die Sie definieren, sind ebenfalls zulässig.  

### <a name="limitations"></a>Einschränkungen
* Firewallregeln können in Verbindung mit in VNet eingefügten Caches verwendet werden, aber derzeit nicht mit privaten Endpunkten. 


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie einen [In VNet eingefügten Cache für eine Premium-Instanz von Azure Cache for Redis](cache-how-to-premium-vnet.md) konfigurieren.  
* Erfahren Sie, wie Sie [Firewallregeln für alle Azure Cache for Redis-Tarife](cache-configure.md#firewall) konfigurieren. 
* Erfahren Sie, wie Sie [private Endpunkte für alle Azure Cache for Redis-Tarife](cache-private-link.md) konfigurieren.