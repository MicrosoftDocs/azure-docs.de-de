---
title: Sicherheitsattribute für Azure SQL-Datenbank
description: Eine Prüfliste der Sicherheitsattribute für die Auswertung von Azure SQL-Datenbank
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001037"
---
# <a name="security-attributes-for-azure-sql-database"></a>Sicherheitsattribute für Azure SQL-Datenbank

In diesem Artikel werden die allgemeinen in Azure SQL-Datenbank integrierten Sicherheitsattribute beschrieben.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL-Datenbank umfasst Konzepte für [Einzeldatenbanken](sql-database-single-index.yml) und für [verwaltete Instanzen](sql-database-managed-instance.md). Die nachstehenden Einträge gelten für beide Angebote, sofern nichts anderes angegeben ist.

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Wird als „Verschlüsselung während der Verwendung“ bezeichnet, wie im Artikel [Always Encrypted](sql-database-always-encrypted.md) beschrieben. Dienstseitige Verschlüsselung verwendet die [transparente Datenverschlüsselung](transparent-data-encryption-azure-sql.md) (Transparent Data Encryption, TDE).|
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Mithilfe von HTTPS |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Es wird sowohl die Verarbeitung von vom Dienst verwalteten Schlüsseln als auch die Verarbeitung von vom Kunden verwalteten Schlüsseln angeboten (die letztgenannte Option durch [Azure Key Vault](../key-vault/index.yml). |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| Ja | Durch [Always Encrypted](sql-database-always-encrypted.md). |
| Verschlüsselte API-Aufrufe| Ja | Mithilfe von HTTPS/SSL. |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Gilt nur für eine [Einzeldatenbank](sql-database-single-index.yml). |
| Unterstützung der VNET-Einschleusung| Ja | Gilt nur für eine [verwaltete Instanz](sql-database-managed-instance.md). |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Firewall auf Datenbank- und Serverebene; Netzwerkisolation nur für eine [verwaltete Instanz](sql-database-managed-instance.md) |
| Unterstützung der Tunnelerzwingung| Ja | [Verwaltete Instanz](sql-database-managed-instance.md) über [Azure ExpressRoute](../expressroute/index.yml)-VPN |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Die SIEM-Drittanbieterlösung von Imperva (SecureSphere) wird auch unterstützt, und zwar mittels [Azure Event Hubs](../event-hubs/index.yml)-Integration über [SQL-Überwachung](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Azure Active Directory |
| Autorisierung| Ja |  |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene: Protokollierung und Überwachung| Ja | Ja, für einige Ereignisse. |
| Datenebene: Protokollierung und Überwachung | Ja | Über [SQL-Überwachung](sql-database-auditing.md). |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Nein  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Zusätzliche Sicherheitsattribute für SQL-Datenbank

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Prävention: Sicherheitsrisikobewertung | Ja | Weitere Informationen finden Sie unter [Mit dem Dienst zur SQL-Sicherheitsrisikobewertung können Sie Datenbankschwachstellen erkennen](sql-vulnerability-assessment.md). |
| Prävention: Datenermittlung und -klassifizierung  | Ja | Weitere Informationen finden Sie unter [Azure SQL-Datenbank und SQL Data Warehouse: Datenermittlung und -klassifizierung](sql-database-data-discovery-and-classification.md). |
| Erkennung: Bedrohungserkennung | Ja | Weitere Informationen finden Sie unter [Advanced Threat Protection für Azure SQL-Datenbank](sql-database-threat-detection-overview.md). |
