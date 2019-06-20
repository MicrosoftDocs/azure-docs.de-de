---
title: Konfigurieren einer verwalteten Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine verwaltete Azure SQL-Datenbank-Instanz konfigurieren und verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 886f06e8640891ac09d1e4624335a7bfebcd3def
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60340790"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Verwenden einer verwalteten Instanz in Azure SQL-Datenbank

In diesem Artikel finden Sie verschiedene Anleitungen, Skripts und Erläuterungen, die Sie beim Verwalten und Konfigurieren Ihrer verwalteten Instanz unterstützen.

## <a name="migration"></a>Migration

- [Migrieren zur verwalteten Instanz](sql-database-managed-instance-migrate.md): Hier finden Sie Informationen zum empfohlenen Migrationsprozess und zu den Tools für die Migration zur verwalteten Instanz.

- [Migrieren eines TDE-Zertifikats zur verwalteten Instanz](sql-database-managed-instance-migrate-tde-certificate.md): Wenn Ihre SQL Server-Datenbank mit transparenter Datenverschlüsselung (TDE) geschützt ist, müssen Sie ein Zertifikat migrieren, das die verwaltete Instanz zum Entschlüsseln der Sicherung verwenden kann, die Sie in Azure wiederherstellen möchten.

## <a name="network-configuration"></a>Netzwerkkonfiguration

- [Bestimmen der Größe des Subnetzes für verwaltete Instanzen](sql-database-managed-instance-determine-size-vnet-subnet.md): Die verwaltete Instanz wird in einem dedizierten Subnetz platziert, dessen Größe nach dem Hinzufügen der Ressource nicht geändert werden kann. Deshalb müssen Sie berechnen, welcher IP-Adressbereich für das Subnetz erforderlich ist, je nach Anzahl und Arten von Instanzen, die im Subnetz bereitgestellt werden sollen.
- [Erstellen eines neuen VNET und Subnetzes für die verwaltete Instanz](sql-database-managed-instance-create-vnet-subnet.md): Das Azure-VNET und das Subnetz, in denen Ihre verwalteten Instanzen bereitgestellt werden sollen, müssen entsprechend den [hier beschriebenen Netzwerkanforderungen](sql-database-managed-instance-connectivity-architecture.md#network-requirements) konfiguriert werden. In dieser Anleitung ist die einfachste Möglichkeit zum Erstellen des neuen VNET und Subnetzes angegeben, sodass sie für verwaltete Instanzen ordnungsgemäß konfiguriert sind.
- [Konfigurieren eines vorhandenen VNET und Subnetzes für die verwaltete Instanz](sql-database-managed-instance-configure-vnet-subnet.md): Wenn Sie das vorhandene VNET und Subnetz so konfigurieren möchten, dass darin verwaltete Instanzen bereitgestellt werden, finden Sie hier ein Skript, mit dem die [Netzwerkanforderungen](sql-database-managed-instance-connectivity-architecture.md#network-requirements) überprüft werden und das Subnetz entsprechend den Anforderungen konfiguriert wird.
- [Konfigurieren von benutzerdefiniertem DNS](sql-database-managed-instance-custom-dns.md): Sie müssen benutzerdefiniertes DNS konfigurieren, wenn Sie von der verwalteten Instanz über einen Verbindungsserver mit Datenbank-E-Mail-Profilen auf externe Ressourcen in den benutzerdefinierten Domänen zugreifen möchten.
- [Synchronisieren der Netzwerkkonfiguration](sql-database-managed-instance-sync-network-configuration.md): Möglicherweise können Sie auch nach dem [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md) keine Verbindung mit der verwalteten Instanz herstellen. Sie können dann versuchen, die Netzwerkkonfiguration für Ihren Dienstplan zu aktualisieren.
- [Ermitteln der IP-Adresse des Verwaltungsendpunkts:](sql-database-managed-instance-find-management-endpoint-ip-address.md) Die verwaltete Instanz verwendet den öffentlichen Endpunkt zu Verwaltungszwecken. Mit dem hier beschriebenen Skript können Sie die IP-Adresse des Verwaltungsendpunkts ermitteln.
- [Überprüfen des integrierten Firewallschutzes](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md): Die verwaltete Instanz ist durch eine integrierte Firewall geschützt, die den Datenverkehr nur an erforderlichen Ports zulässt. Mit dem in dieser Anleitung beschriebenen Skript können Sie die Regeln für die integrierte Firewall überprüfen.
- [Herstellen einer Verbindung mit Anwendungen](sql-database-managed-instance-connect-app.md): Die verwaltete Instanz wird in Ihrem eigenen privaten Azure-VNET mit privater IP-Adresse platziert. Lernen Sie verschiedene Möglichkeiten zum Verbinden von Anwendungen mit Ihrer verwalteten Instanz kennen.

## <a name="feature-configuration"></a>Featurekonfiguration

- [Transaktionsreplikation](replication-with-sql-database-managed-instance.md): Dies ermöglicht Ihnen das Replizieren Ihrer Daten zwischen verwalteten Instanzen oder von einem lokalen SQL Server zur verwalteten Instanz und umgekehrt. In dieser Anleitung finden Sie weitere Informationen zum Verwenden und Konfigurieren der Transaktionsreplikation.
- [Konfigurieren der Bedrohungserkennung](sql-database-managed-instance-threat-detection.md): Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) ist ein integriertes Azure SQL-Datenbank-Feature, mit dem verschiedene potenzielle Angriffe, z.B. die Einschleusung von SQL-Befehlen oder ein Zugriff von verdächtigen Standorten, erkannt werden. In dieser Anleitung erfahren Sie, wie Sie die [Bedrohungserkennung](sql-database-threat-detection-overview.md) für die verwaltete Instanz aktivieren und konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Anleitungen für Einzeldatenbanken](sql-database-howto-single-database.md).