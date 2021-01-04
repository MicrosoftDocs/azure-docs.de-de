---
title: Sicherheit – Azure Database for MariaDB
description: Eine Übersicht über die Sicherheitsfeatures in Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8a46f70e3089a8610fa8ca6362bcba6f624e2b1a
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541146"
---
# <a name="security-in-azure-database-for-mariadb"></a>Sicherheit in Azure Database for MariaDB

Zum Schutz der Daten auf dem Azure Database for MariaDB-Server stehen mehrere Sicherheitsebenen zur Verfügung. In diesem Artikel werden diese Sicherheitsoptionen beschrieben.

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="in-transit"></a>Während der Übertragung
Mit Azure Database for MariaDB werden Ihre Daten durch Verschlüsselung während der Übertragung mit Transport Layer Security geschützt. Die Verschlüsselung (SSL/TLS) wird standardmäßig erzwungen.

### <a name="at-rest"></a>Ruhende Daten
Der Azure Database for MariaDB-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (mit Ausnahme von temporären Dateien, die während der Ausführung von Abfragen erstellt werden). Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.


## <a name="network-security"></a>Netzwerksicherheit
Verbindungen mit einem Azure Database for MariaDB-Server werden zunächst über ein regionales Gateway geleitet. Das Gateway verfügt über eine öffentlich zugängliche IP-Adresse, während die IP-Adressen des Servers geschützt sind. Weitere Informationen zum Gateway finden Sie im Artikel [Verbindungsarchitektur](concepts-connectivity-architecture.md).  

Ein neu erstellter Azure Database for MariaDB-Server verfügt über eine Firewall, die alle externen Verbindungen blockiert. Sie erreichen zwar das Gateway, dürfen aber keine Verbindung mit dem Server herstellen. 

### <a name="ip-firewall-rules"></a>IP-Firewallregeln
IP-Firewallregeln gewähren Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie in der [Übersicht über Firewallregeln](concepts-firewall-rules.md).

### <a name="virtual-network-firewall-rules"></a>Firewallregeln für virtuelle Netzwerke
Mit VNET-Dienstendpunkten wird die Konnektivität virtueller Netzwerke über den Azure-Backbone erweitert. Mithilfe von VNET-Regeln können Sie Ihren Azure Database for MariaDB-Server so konfigurieren, dass Verbindungen von ausgewählten Subnetzen in einem virtuellen Netzwerk zugelassen werden. Weitere Informationen finden Sie in der [Übersicht über VNET-Dienstendpunkte](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Zugriffsverwaltung

Beim Erstellen des Azure Database for MariaDB-Servers geben Sie Anmeldeinformationen für einen Administratorbenutzer ein. Mit diesem Administrator können Sie zusätzliche MariaDB-Benutzer erstellen.


## <a name="threat-protection"></a>Bedrohungsschutz

Sie können [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) verwenden. Diese Lösung erkennt anormale Aktivitäten, die darauf hindeuten, dass auf ungewöhnliche und potenziell schädliche Weise versucht wird, auf Server zuzugreifen oder diese auszunutzen.

Aktivitäten in Ihren Datenbanken können mithilfe der [Überwachungsprotokollierung](concepts-audit-logs.md) nachverfolgt werden. 


## <a name="next-steps"></a>Nächste Schritte
- Aktivieren von Firewallregeln für [IP-Adressen](concepts-firewall-rules.md) oder [virtuelle Netzwerke](concepts-data-access-security-vnet.md)