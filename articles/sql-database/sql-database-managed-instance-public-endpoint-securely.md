---
title: Schützen der öffentlichen Endpunkte verwalteter Instanzen – verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Sicheres Verwenden eines öffentlichen Endpunkts in Azure mit einer verwalteten Instanz
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470307"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Sicheres Verwenden einer verwalteten Azure SQL-Datenbank-Instanz mit öffentlichen Endpunkten

Verwaltete Azure SQL-Datenbank-Instanzen können Benutzerkonnektivität über [öffentliche Endpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) bieten. In diesem Artikel wird erläutert, wie Sie diese Konfiguration sicherer machen.

## <a name="scenarios"></a>Szenarien

Eine verwaltete Instanz von SQL-Datenbank bietet einen privaten Endpunkt, um Konnektivität aus ihrem virtuellen Netzwerk heraus zu ermöglichen. Die Standardoption ist das Bereitstellen maximaler Isolation. Es gibt jedoch Szenarien, in denen Sie eine Verbindung mit einem öffentlichen Endpunkt bereitstellen müssen:

- Die verwaltete Instanz muss in nur mehrinstanzenfähige Platform-as-a-Service-Angebote (PaaS) integriert werden.
- Sie benötigen einen höheren Durchsatz des Datenaustauschs, als mit Verwendung eines VPN möglich ist.
- Verbot von PaaS innerhalb der Unternehmensnetzwerke durch Unternehmensrichtlinien

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Bereitstellen einer verwalteten Instanz für den Zugriff über den öffentlichen Endpunkt

Auch wenn dies nicht unbedingt erforderlich ist, werden verwaltete Instanzen mit Zugriff auf einen öffentlichen Endpunkt meist in einem dedizierten isolierten virtuellen Netzwerk erstellt. In dieser Konfiguration wird das virtuelle Netzwerk nur zur Isolation des virtuellen Clusters verwendet. Es spielt keine Rolle, wenn der IP-Adressraum der verwalteten Instanz sich mit dem IP-Adressraum eines Unternehmensnetzwerks überschneidet.

## <a name="secure-data-in-motion"></a>Sichern von Daten während der Übertragung

Der Datenverkehr verwalteter Instanzen wird immer verschlüsselt, wenn der Clienttreiber Verschlüsselung unterstützt. Datenübertragungen zwischen der verwalteten Instanz und anderen virtuellen Computern in Azure oder Azure-Diensten bleibt immer auf dem Backbone von Azure. Wenn eine Verbindung zwischen der verwalteten Instanz und einem lokalen Netzwerk besteht, sollten Sie Azure ExpressRoute mit Microsoft-Peering verwenden. Mit ExpressRoute vermeiden Sie das Verschieben von Daten über das öffentliche Internet. Für die private Konnektivität der verwalteten Instanz kann nur privates Peering verwendet werden.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Sperren eingehender und ausgehender Konnektivität

Das folgende Diagramm zeigt die empfohlenen Sicherheitskonfigurationen:

![Sicherheitskonfigurationen für das Sperren eingehender und ausgehender Konnektivität](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Eine verwaltete Instanz verfügt über eine [dedizierte öffentliche Endpunktadresse](sql-database-managed-instance-find-management-endpoint-ip-address.md). Legen Sie für die clientseitige ausgehende Firewall und in den Netzwerksicherheitsgruppen-Regeln diese IP-Adresse des öffentlichen Endpunkts fest, um ausgehende Konnektivität zu beschränken.

Um sicherzustellen, dass Datenverkehr zur verwalteten Instanz aus vertrauenswürdigen Quellen stammt, sollten nur Verbindungen mit Quellen mit bekannten IP-Adressen hergestellt werden. Verwenden Sie eine Netzwerksicherheitsgruppe, um den Zugriff auf den öffentlichen Endpunkt der verwalteten Instanz auf Port 3342 zu beschränken.

Wenn Clients eine Verbindung von einem lokalen Netzwerk herstellen müssen, stellen Sie sicher, dass die ursprüngliche Adresse in eine Gruppe bekannter IP-Adressen übersetzt wird. Wenn das nicht möglich ist (eine mobile Belegschaft ist z.B. ein typisches Szenario), sollten Sie [Point-to-Site-VPN-Verbindungen und einen privaten Endpunkt](sql-database-managed-instance-configure-p2s.md) verwenden.

Wenn Verbindungen von Azure aus gestartet werden, sollte der Datenverkehr von einer gut bekannten zugewiesenen [virtuellen IP-Adresse](../virtual-network/virtual-networks-reserved-public-ip.md) (z.B. einem virtuellen Computer) kommen. Um die Verwaltung virtueller IP-Adressen (VIP) zu vereinfachen, sollten Sie [öffentliche IP-Adresspräfixe](../virtual-network/public-ip-address-prefix.md) verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Konfigurieren des öffentlichen Endpunkts für verwaltete Instanzen: [Konfigurieren des öffentlichen Endpunkts](sql-database-managed-instance-public-endpoint-configure.md)
