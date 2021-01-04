---
title: Bestimmen der erforderlichen Subnetzgröße und des Bereichs
titleSuffix: Azure SQL Managed Instance
description: In diesem Artikel erfahren Sie, wie Sie die Größe des Subnetzes berechnen, in dem Azure SQL Managed Instance bereitgestellt wird.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012448"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Bestimmen von Subnetzgröße und -bereich für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance muss in einem [virtuellen Azure-Netzwerk (VNET)](../../virtual-network/virtual-networks-overview.md) bereitgestellt werden.

Die Anzahl der verwalteten Instanzen, die im Subnetz eines VNETs bereitgestellt werden können, hängt von der Größe des Subnetzes (dem Subnetzbereich) ab.

Bei der Erstellung einer verwalteten Instanz ordnet Azure abhängig vom Tarif, den Sie bei der Bereitstellung ausgewählt haben, eine Reihe von virtuellen Computern zu. Da diese virtuellen Computer Ihrem Subnetz zugeordnet sind, benötigen sie IP-Adressen. Um bei regulären Vorgängen und bei der Wartung von Diensten Hochverfügbarkeit sicherzustellen, kann Azure zusätzliche virtuelle Computer zuordnen. Daher ist die Anzahl der erforderlichen IP-Adressen in einem Subnetz größer als die Anzahl der verwalteten Instanzen in diesem Subnetz.

Prinzipiell benötigt eine verwaltete Instanz mindestens 32 IP-Adressen in einem Subnetz. Daher können Sie bei der Definition der IP-Adressbereiche für das Subnetz die Subnetzmaske /27 verwenden. Es wird eine sorgfältige Planung der Subnetzgröße für die Bereitstellungen verwalteter Instanzen empfohlen. Folgende Eingaben sollten bei der Planung berücksichtigt werden:

- Anzahl verwalteter Instanzen mit den folgenden Instanzparametern:
  - Dienstebene
  - Hardwaregeneration
  - Anzahl der virtuellen Kerne
- Pläne zum Hoch- oder Herunterskalieren oder Ändern der Dienstebene

> [!IMPORTANT]
> Ein Subnetz, das 16 IP-Adressen (Subnetzmaske /28) umfasst, ermöglicht die Bereitstellung verwalteter Instanzen in diesem Subnetz. Es sollte allerdings nur für die Bereitstellung einzelner Instanzen verwendet werden, die für die Evaluierung oder in Dev/Test-Szenarios genutzt werden, in denen keine Skalierungsvorgänge ausgeführt werden.

## <a name="determine-subnet-size"></a>Bestimmen der Subnetzgröße

Wählen Sie die Größe Ihres Subnetzes in Abhängigkeit von den Anforderungen hinsichtlich der Bereitstellung und Skalierung künftiger Instanzen. Die folgenden Parameter unterstützen Sie bei der Berechnung:

- Azure verwendet fünf IP-Adressen im Subnetz für den eigenen Bedarf.
- Von jedem virtuellen Cluster wird eine zusätzliche Anzahl von Adressen zugeordnet. 
- Jede verwaltete Instanz verwendet die Anzahl der Adressen, die vom Tarif und der Hardwaregeneration abhängig ist.

> [!IMPORTANT]
> Es ist nicht möglich, den Subnetzadressbereich zu ändern, wenn eine Ressource im Subnetz vorhanden ist. Außerdem ist es nicht möglich, verwaltete Instanzen von einem Subnetz in ein anderes zu verschieben. Verwenden Sie nach Möglichkeit immer größere Subnetze, um in Zukunft Probleme zu vermeiden.

GP = universell; BC = unternehmenskritisch; VC = virtueller Cluster

| **Hardwaregeneration** | **Preisstufe** | **Azure-Nutzung** | **Nutzung virtueller Cluster** | **Instanznutzung** | **Gesamt** _ |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \_ Die Spalte „Gesamt“ gibt die Anzahl der Adressen an, die verwendet werden, wenn eine Instanz im Subnetz bereitgestellt wird. Jede zusätzliche Instanz im Subnetz fügt eine Anzahl von Adressen hinzu, die mit der Spalte „Instanznutzung“ angegeben werden. Adressen, die mit der Spalte „Azure-Nutzung“ dargestellt werden, werden für mehrere virtuelle Cluster freigegeben, während Adressen, die mit der Spalte „Nutzung virtueller Cluster“ dargestellt werden, für alle Instanzen in diesem virtuellen Cluster freigegeben werden.

Beim Aktualisierungsvorgang ist in der Regel eine Größenänderung der virtuellen Cluster erforderlich. Unter bestimmten Umständen ist für den Aktualisierungsvorgang die Erstellung virtueller Cluster erforderlich (weitere Informationen finden Sie im Artikel [Verwaltungsvorgänge](sql-managed-instance-paas-overview.md#management-operations)). Bei der Erstellung virtueller Cluster ist die Anzahl erforderlicher zusätzlicher Adressen gleich der Anzahl von Adressen, die durch die Spalte „Nutzung virtueller Cluster“ dargestellt werden, summiert mit den Adressen, die für in diesem virtuellen Cluster platzierte Instanzen erforderlich sind (Spalte „Instanznutzung“).

**Beispiel 1:** Sie planen, eine universelle verwaltete Instanz (Gen4-Hardware) und eine unternehmenskritische verwaltete Instanz (Gen5-Hardware) zu verwenden. Dies bedeutet, dass Sie für die Bereitstellung mindestens 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP-Adressen benötigen. Da IP-Bereiche in Zweierpotenzen definiert sind, erfordert Ihr Subnetz für diese Bereitstellung einen minimalen IP-Adressbereich von 32 (2 ^ 5).<br><br>
Wie bereits erwähnt, ist für den Aktualisierungsvorgang unter bestimmten Umständen die Erstellung eines virtuellen Clusters erforderlich. Dies bedeutet beispielsweise, dass bei einem Update der verwalteten Gen5-Instanz, für die ein virtueller Cluster erstellt werden muss, zusätzlich 6 + 5 = 11 IP-Adressen verfügbar sein müssen. Da Sie bereits 22 der 32 Adressen verwenden, sind für diesen Vorgang keine Adressen verfügbar. Aus diesem Grund müssen Sie das Subnetz mit der Subnetzmaske /26 (64 Adressen) reservieren.

**Beispiel 2:** Sie planen, drei universelle (Gen5-Hardware) und zwei unternehmenskritische verwaltete Instanzen (Gen5-Hardware) im gleichen Subnetz zu platzieren. Dies bedeutet, dass Sie 5 + 6 + 3 * 3 + 2 * 5 = 30 IP-Adressen benötigen. Aus diesem Grund müssen Sie das Subnetz mit der Subnetzmaske /26 reservieren. Die Auswahl der Subnetzmaske /27 würde dazu führen, dass die verbleibende Anzahl von Adressen 2 ist (32 – 30). Dadurch werden Aktualisierungsvorgänge für alle Instanzen verhindert, da zum Durchführen der Instanzskalierung zusätzliche Adressen im Subnetz erforderlich sind.

**Beispiel 3:** Sie planen, eine universelle verwaltete Instanz (Gen5-Hardware) zu verwenden und den Aktualisierungsvorgang für virtuelle Kerne von Zeit zu Zeit durchzuführen. Dies bedeutet, dass Sie 5 + 6 + 1 * 3 + 3 = 17 IP-Adressen benötigen. Da IP-Adressbereiche in Zweierpotenzen definiert sind, benötigen Sie den IP-Adressbereich von 32 (2^5) IP-Adressen. Aus diesem Grund müssen Sie das Subnetz mit der Subnetzmaske /27 reservieren.

### <a name="address-requirements-for-update-scenarios"></a>Adressanforderungen für Aktualisierungsszenarios

Bei Skalierungsvorgängen benötigen Instanzen vorübergehend zusätzliche IP-Kapazität, die vom Tarif und der Hardwaregeneration abhängig ist.

| **Hardwaregeneration** | **Preisstufe** | **Szenario** | **Zusätzliche Adressen** _ |
| --- | --- | --- | --- |
| Gen4 | universell oder unternehmenskritisch | Skalieren von virtuellen Kernen | 5 |
| Gen4 | universell oder unternehmenskritisch | Skalieren des Speichers | 5 |
| Gen4 | universell oder unternehmenskritisch | Wechseln von universell zu unternehmenskritisch oder von unternehmenskritisch zu universell | 5 |
| Gen4 | GP | Wechseln zu Gen5_ | 9 |
| Gen4 | BC | Wechseln zu Gen5* | 11 |
| Gen5 | GP | Skalieren von virtuellen Kernen | 3 |
| Gen5 | GP | Skalieren des Speichers | 0 |
| Gen5 | GP | Wechseln zu unternehmenskritisch | 5 |
| Gen5 | BC | Skalieren von virtuellen Kernen | 5 |
| Gen5 | BC | Skalieren des Speichers | 5 |
| Gen5 | BC | Wechseln zu universell | 3 |

  \* Die Gen4-Hardware wird eingestellt und steht für neue Bereitstellungen nicht mehr zur Verfügung. Aktualisieren Sie die Hardware von Gen4 auf Gen5, um die Funktionen zu nutzen, die für die Gen5-Hardwaregeneration spezifisch sind.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Machen Sie sich mit der [Verbindungsarchitektur für SQL Managed Instance](connectivity-architecture-overview.md) vertraut.
- Informieren Sie sich, wie Sie [ein VNET erstellen, in dem Sie SQL Managed Instance bereitstellen können](virtual-network-subnet-create-arm-template.md).
- Informationen zu Problemen im Zusammenhang mit dem DNS finden Sie unter [Konfigurieren eines benutzerdefinierten DNS für Azure SQL Managed Instance](custom-dns-configure.md).
