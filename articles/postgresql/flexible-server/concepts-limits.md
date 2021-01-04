---
title: Beschränkungen für Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel werden die Beschränkungen in Azure Database for PostgreSQL – Flexible Server beschrieben, z. B. die Anzahl der Verbindungen und Optionen für die Speicher-Engine.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f270ac736bb5dbc429dc8659cc88e63d0b51a523
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366637"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Beschränkungen in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

In den folgenden Abschnitten werden die Kapazitäts- und funktionalen Beschränkungen im Datenbankdienst beschrieben. Informationen zu den Tarifen für Ressourcen (Compute, Arbeitsspeicher, Speicher) finden Sie im Artikel [Compute und Speicher](concepts-compute-storage.md).

## <a name="maximum-connections"></a>Maximale Anzahl der Verbindungen

Die folgende Tabelle enthält die maximale Anzahl von Verbindungen nach Tarif und virtuellen Kernen, wie unten gezeigt. Das Azure-System benötigt drei Verbindungen, um Azure Database for PostgreSQL – Flexible Server zu überwachen.

| SKU-Name             | V-Kerne | Arbeitsspeichergröße | Max. Anzahl von Verbindungen | Max. Benutzerverbindungen |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burstfähig**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Allgemeiner Zweck**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5.000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5.000            | 4997                 |
| **Arbeitsspeicheroptimiert** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64GiB      | 5.000            | 4997                 |
| E16s_v3              | 16     | 128 GB     | 5.000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5.000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5.000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5.000            | 4997                 |

Wenn Verbindungen den Grenzwert übersteigen, erhalten Sie möglicherweise den folgenden Fehler:
> SCHWERWIEGEND: Es sind bereits zu viele Clients vorhanden.

> [!IMPORTANT]
> Für eine optimale Erfahrung empfehlen wir, dass Sie einen Verbindungspooler wie PgBouncer einsetzen, um Verbindungen effizient zu verwalten.

Eine PostgreSQL-Verbindung kann selbst im Leerlauf ca. 10 MB Arbeitsspeicher belegen. Außerdem nimmt das Erstellen neuer Verbindungen Zeit in Anspruch. Die meisten Anwendungen fordern viele kurzlebige Verbindungen an, was diese Situation erschwert. Das Ergebnis sind weniger Ressourcen, die für ihre tatsächliche Workload verfügbar sind, was zu verringerter Leistung führt. Mithilfe des Verbindungspoolings können Verbindungen im Leerlauf verringert und vorhandene Verbindungen wiederverwendet werden. Weitere Informationen finden Sie in unserem [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funktionale Beschränkungen

### <a name="scale-operations"></a>Skalierungsvorgänge

- Zum Skalieren des Serverspeichers ist ein Neustart des Servers erforderlich.
- Serverspeicher kann nur in Verdopplungsschritten skaliert werden. Weitere Informationen finden Sie unter [Compute und Speicher](concepts-compute-storage.md).
- Die Verringerung der Größe des Serverspeichers wird zurzeit nicht unterstützt.

### <a name="server-version-upgrades"></a>Upgrades von Serverversionen

- Die automatisierte Migration zwischen Hauptversionen von Datenbank-Engines wird derzeit nicht unterstützt. Wenn Sie auf die nächste Hauptversion upgraden möchten, führen Sie eine [Sicherung und Wiederherstellung](../howto-migrate-using-dump-and-restore.md) auf einem Server aus, der mit der neuen Engine-Version erstellt wurde.

### <a name="networking"></a>Netzwerk

- Das Verschieben in und aus einem VNET wird derzeit nicht unterstützt.
- Das Kombinieren des öffentlichen Zugriffs mit der Bereitstellung in einem VNET wird derzeit nicht unterstützt.
- Firewallregeln werden im VNET nicht unterstützt. Stattdessen können Netzwerksicherheitsgruppen verwendet werden.
- Datenbankserver mit öffentlichem Zugriff können z. B. über `postgres_fdw` eine Verbindung mit dem öffentlichen Internet herstellen. Dieser Zugriff kann nicht eingeschränkt werden. Für VNET-basierte Server kann der ausgehende Zugriff mithilfe von Netzwerksicherheitsgruppen eingeschränkt werden.

### <a name="high-availability-ha"></a>Hochverfügbarkeit

- Zonenredundante Hochverfügbarkeit wird derzeit für Server im Tarif „Burstfähig“ nicht unterstützt.
- Die IP-Adresse des Datenbankservers ändert sich, wenn für Ihren Server ein Failover auf den Standbyserver mit Hochverfügbarkeit erfolgt. Stellen Sie sicher, dass Sie den DNS-Eintrag anstelle der IP-Adresse des Servers verwenden.
- Wenn die logische Replikation mit einem flexiblen Server mit Hochverfügbarkeit konfiguriert ist, werden die logischen Replikationsslots bei einem Failover auf den Standbyserver nicht auf den Standbyserver kopiert. 
- Weitere Informationen zur zonenredundanten Hochverfügbarkeit und den damit verbundenen Einschränkungen finden Sie auf der Dokumentationsseite [Konzepte – Hochverfügbarkeit](concepts-high-availability.md).

### <a name="availability-zones"></a>Verfügbarkeitszonen

- Manuelles Verschieben von Servern in eine andere Verfügbarkeitszone wird derzeit nicht unterstützt.
- Die Verfügbarkeitszone des Standbyservers mit Hochverfügbarkeit kann nicht manuell konfiguriert werden.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres-Engine, Erweiterungen und PgBouncer

- Postgres 10 und niedriger werden nicht unterstützt. Die Option [Einzelserver](../overview-single-server.md) wird empfohlen, wenn Sie ältere Postgres-Versionen benötigen.
- Die Erweiterungsunterstützung ist derzeit auf die Postgres-Erweiterungen des Typs `contrib` beschränkt.
- Der integrierte Verbindungspooler PgBouncer ist derzeit für Datenbankserver in einem VNET oder für Server im Tarif „Burstfähig“ nicht verfügbar.

### <a name="stopstart-operation"></a>Vorgang „Anhalten/Starten“

- Der Server kann nicht länger als sieben Tage angehalten werden.

### <a name="scheduled-maintenance"></a>Geplante Wartung

- Die Änderung des Wartungsfensters in weniger als fünf Tage vor einem bereits geplanten Upgrade hat keinen Einfluss auf dieses Upgrade. Änderungen werden erst bei der nächsten geplanten Wartung wirksam.

### <a name="backing-up-a-server"></a>Sichern eines Servers

- Sicherungen werden vom System verwaltet. Es gibt derzeit keine Möglichkeit, diese Sicherungen manuell auszuführen. Stattdessen wird die Verwendung von `pg_dump` empfohlen.
- Sicherungen sind stets auf Momentaufnahmen basierende vollständige Sicherungen (keine differenziellen Sicherungen), was möglicherweise zu einer höheren Belegung des Sicherungsspeichers führt. Beachten Sie, dass Transaktionsprotokolle (Write-Ahead-Protokolle, WAL) von den vollständigen/differentiellen Sicherungen getrennt sind und kontinuierlich archiviert werden.

### <a name="restoring-a-server"></a>Wiederherstellen eines Servers

- Bei Verwenden des Features „Zeitpunktwiederherstellung“ wird der neue Server mit der dem zugrunde liegende Server entsprechenden Compute- und Speicherkonfiguration erstellt.
- VNET-basierte Datenbankserver werden im selben VNET wiederhergestellt, wenn Sie sie aus einer Sicherung wiederherstellen.
- Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die Firewallregeln auf, die auf dem ursprünglichen Server vorhanden waren. Firewallregeln müssen für den neuen Server separat erstellt werden.
- Die Wiederherstellung eines gelöschten Servers wird nicht unterstützt.
- Eine regionsübergreifende Wiederherstellung wird nicht unterstützt.

### <a name="other-features"></a>Andere Funktionen

* Azure AD-Authentifizierung wird noch nicht unterstützt. Wenn Sie die Azure AD-Authentifizierung benötigen, wird die Option [Einzelserver](../overview-single-server.md) empfohlen.
* Lesereplikate werden noch nicht unterstützt. Wenn Sie die Lesereplikate benötigen, wird die Option [Einzelserver](../overview-single-server.md) empfohlen.


## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zu [verfügbaren Optionen für Compute- und Speicheroptionen](concepts-compute-storage.md)
- Informationen zu [unterstützten PostgreSQL-Datenbankversionen](concepts-supported-versions.md)
- Informationen zum [Sichern und Wiederherstellen eines Servers in Azure Database for PostgreSQL mithilfe des Azure-Portals](how-to-restore-server-portal.md)
