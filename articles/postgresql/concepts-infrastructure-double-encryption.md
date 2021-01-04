---
title: 'Doppelte Infrastrukturverschlüsselung: Azure Database for PostgreSQL'
description: Erfahren Sie mehr über die doppelte Infrastrukturverschlüsselung, um eine zweite Verschlüsselungsebene mit dienstseitig verwalteten Schlüsseln hinzuzufügen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 03d52611c89012d1132b4a27820f7f47e6cdd512
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242023"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Azure Database for PostgreSQL: doppelte Infrastrukturverschlüsselung

Azure Database for PostgreSQL arbeitet mit der [Verschlüsselung gespeicherter ruhender Daten](concepts-security.md#at-rest) unter Verwendung von Schlüsseln, die von Microsoft verwaltet werden. Daten, einschließlich Sicherungen, werden auf Datenträgern verschlüsselt, wobei diese Verschlüsselung stets aktiviert ist und nicht deaktiviert werden kann. Die Verschlüsselung beruht auf einem gemäß FIPS 140-2 validierten kryptografischen Modul und einem AES-256-Bit-Verschlüsselungsverfahren für die Azure-Speicherverschlüsselung.

Die doppelte Infrastrukturverschlüsselung fügt eine zweite Verschlüsselungsebene mit dienstseitig verwalteten Schlüsseln hinzu. Sie bedient sich eines gemäß FIPS 140-2 validierten kryptografischen Moduls, jedoch mit einem anderen Verschlüsselungsalgorithmus. Dies bietet eine zusätzliche Schutzebene für Ihre ruhenden Daten. Der bei der doppelten Infrastrukturverschlüsselung verwendete Schlüssel wird ebenfalls vom Azure Database for PostgreSQL-Dienst verwaltet. Die doppelte Infrastrukturverschlüsselung ist nicht standardmäßig aktiviert, da die zusätzliche Verschlüsselungsebene die Leistung beeinträchtigen kann.

> [!NOTE]
> Dieses Feature wird nur in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ in Azure Database for PostgreSQL unterstützt.

Die Verschlüsselung auf Infrastrukturebene hat den Vorteil, dass sie auf der Ebene implementiert wird, die dem Speichergerät oder den Netzwerkleitungen am nächsten ist. Azure Database for PostgreSQL implementiert die beiden Verschlüsselungsebenen mithilfe dienstseitig verwalteter Schlüssel. Obwohl technisch gesehen immer noch auf Dienstebene, ist sie sehr nahe an der Hardware, die die ruhenden Daten speichert. Optional können Sie weiterhin die Verschlüsselung ruhender Daten aktivieren, indem Sie einen [kundenseitig verwalteten Schlüssel](concepts-data-encryption-postgresql.md) für den bereitgestellten PostgreSQL-Server verwenden.  

Die Implementierung auf Infrastrukturebene unterstützt zudem eine Vielfalt von Schlüsseln. Die Infrastruktur muss die verschiedenen Cluster von Computern und Netzwerken kennen. Daher werden verschiedene Schlüssel verwendet, um den Angriffsradius von Infrastrukturangriffen und eine Vielzahl von Hardware- und Netzwerkfehlern zu minimieren. 

> [!NOTE]
> Die Verwendung der doppelten Infrastrukturverschlüsselung wirkt sich aufgrund des zusätzlichen Verschlüsselungsverfahrens auf die Leistung des Azure Database for PostgreSQL-Servers aus.

## <a name="benefits"></a>Vorteile

Die doppelte Infrastrukturverschlüsselung für Azure Database for PostgreSQL bietet die folgenden Vorteile:

1. **Mehr Vielfalt bei der Implementierung von Kryptografie** : Durch die geplante Umstellung auf hardwarebasierte Verschlüsselung werden die Implementierungen weiter diversifiziert, indem zusätzlich zur softwarebasierten Implementierung eine hardwarebasierte Implementierung bereitgestellt wird.
2. **Implementierungsfehler** : Zwei Verschlüsselungsebenen auf Infrastrukturebene schützen vor Fehlern bei der Zwischenspeicherung oder Speicherverwaltung auf höheren Ebenen, die Klartextdaten verfügbar machen. Darüber hinaus gewährleisten die beiden Ebenen auch Schutz vor Fehlern bei der Implementierung der Verschlüsselung im Allgemeinen.

Diese Kombination bietet einen wirksamen Schutz gegen häufige Bedrohungen und Schwachstellen, die für Angriffe auf die Kryptographie ausgenutzt werden.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Unterstützte Szenarien mit doppelter Infrastrukturverschlüsselung

Die Verschlüsselungsfunktionen, die von Azure Database for PostgreSQL geboten werden, können kombiniert werden. Es folgt eine Übersicht über die verschiedenen möglichen Szenarien:

|  ##   | Standardverschlüsselung | Doppelte Infrastrukturverschlüsselung | Datenverschlüsselung mithilfe kundenseitig verwalteter Schlüssel  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Ja*              | *Nein*                             | *Nein*                                         |
| 2     | *Ja*              | *Ja*                            | *Nein*                                         |
| 3     | *Ja*              | *Nein*                             | *Ja*                                        |
| 4     | *Ja*              | *Ja*                            | *Ja*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - In den Szenarien 2 und 4 wird die Leistung des Azure Database for PostgreSQL-Servers aufgrund der zusätzlichen Ebene der Infrastrukturverschlüsselung beeinträchtigt.
> - Das Konfigurieren der doppelten Infrastrukturverschlüsselung für Azure Database for PostgreSQL ist nur bei der Erstellung des Servers zulässig. Nachdem der Server bereitgestellt wurde, können Sie die Speicherverschlüsselung nicht mehr ändern. Sie können jedoch weiterhin die Datenverschlüsselung mit kundenseitig verwalteten Schlüsseln für den Server aktivieren, der mit/ohne doppelte Infrastrukturverschlüsselung erstellt wurde.

## <a name="limitations"></a>Einschränkungen

Bei Azure Database for PostgreSQL unterliegt die Unterstützung der doppelten Infrastrukturverschlüsselung mit dienstseitig verwaltetem Schlüssel den folgenden Einschränkungen:

* Die Unterstützung dieser Funktionalität ist auf die Tarife **Universell** und **Arbeitsspeicheroptimiert** beschränkt.
* Sie können ein Instanz von Azure Database for PostgreSQL mit aktivierter doppelter Infrastrukturverschlüsselung in folgenden Regionen erstellen:

   * East US
   * USA Süd Mitte
   * USA, Westen 2
   
* Diese Funktion wird nur in Regionen und auf Servern unterstützt, die eine Speicherkapazität bis zu 16 TB unterstützen. Eine Liste der Azure-Regionen, die Speicher mit bis zu 16 TB unterstützen, finden Sie in der [Speicherdokumentation](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Alle **neuen** PostgreSQL-Server, die in den oben aufgeführten Regionen erstellt wurden, unterstützen die Verschlüsselung mit kundenseitig verwalteten Schlüsseln. In diesem Fall gelten Server, die über eine Zeitpunktwiederherstellung oder Lesereplikate erstellt wurden, nicht als „neu“.
    > - Um zu überprüfen, ob Ihr bereitgestellter Server bis zu 16 TB unterstützt, können Sie im Portal zum Blatt „Tarif“ wechseln und prüfen, ob der Schieberegler für den Speicher auf bis zu 16 TB verschoben werden kann. Wenn Sie den Schieberegler nur auf 4 TB verschieben können, unterstützt Ihr Server möglicherweise keine Verschlüsselung mit kundenseitig verwalteten Schlüsseln. Die Daten sind jedoch jederzeit mit dienstseitig verwalteten Schlüsseln verschlüsselt. Wenn Sie weitere Fragen haben, wenden Sie sich an AskAzureDBforPostgreSQL@service.microsoft.com.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die [doppelte Infrastrukturverschlüsselung für Azure Database for PostgreSQL einrichten](howto-double-encryption.md).
