---
title: Bestimmen der VNET-/Subnetzgröße für eine verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die Größe des Subnetzes berechnen, in dem verwaltete Azure SQL-Datenbank-Instanzen bereitgestellt werden.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: 2a10876bc3c9558de29caf9fee2ae0b06ee87f28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60405340"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Bestimmen der VNET-/Subnetzgröße für eine verwaltete Azure SQL-Datenbank-Instanz

In einem [virtuellen Azure-Netzwerk (VNET)](../virtual-network/virtual-networks-overview.md) muss eine verwaltete Azure SQL-Datenbank-Instanz bereitgestellt werden.

Die Anzahl der verwalteten Instanzen, die im Subnetz des VNETs bereitgestellt werden können, hängt von der Größe des Subnetzes (dem Subnetzbereich) ab.

Bei der Erstellung einer verwalteten Instanz ordnet Azure abhängig vom Tarif, den Sie bei der Bereitstellung ausgewählt haben, eine Reihe von virtuellen Computern zu. Da diese virtuellen Computer Ihrem Subnetz zugeordnet sind, benötigen sie IP-Adressen. Um bei regulären Vorgängen und bei der Wartung von Diensten Hochverfügbarkeit sicherzustellen, kann Azure zusätzliche virtuelle Computer zuordnen. Daher ist die Anzahl der erforderlichen IP-Adressen in einem Subnetz größer als die Anzahl der verwalteten Instanzen in diesem Subnetz.

Prinzipiell benötigt eine verwaltete Instanz zwischen 16 und 256 IP-Adressen in einem Subnetz. Daher können Sie für die Definition Ihrer Subnetz-IP-Adressbereiche 28- bis 24-Bit-Masken verwenden. Eine 28-Bit-Maske (14 Hosts pro Netzwerk) ist eine gute Größe für eine Bereitstellung für einen universellen Zweck oder für geschäftskritische Bereitstellungen. Eine 27-Bit-Maske (30 Hosts pro Netzwerk) ist ideal für Bereitstellungen mit mehreren verwalteten Instanzen innerhalb des gleichen VNets. Eine 26- (62 Hosts) und 24-Bit-Maske (254 Hosts) ermöglichen die weitere Skalierung über das VNet hinaus, um zusätzliche verwaltete Instanzen zu unterstützen.

> [!IMPORTANT]
> Als Größe für das Subnetz stellen 16 IP-Adressen das absolute Minimum dar, das nur beschränkte Möglichkeiten für eine weitere Hochskalierung der verwalteten Instanz bietet. Die Auswahl eines Subnetzes mit dem Präfix /27 oder darunter wird dringend empfohlen.

## <a name="determine-subnet-size"></a>Bestimmen der Subnetzgröße

Wenn Sie mehrere verwaltete Instanzen innerhalb des Subnetzes bereitstellen möchten und die Subnetzgröße optimieren müssen, führen Sie mithilfe der folgenden Parameter eine Berechnung durch:

- Azure verwendet fünf IP-Adressen im Subnetz für den eigenen Bedarf.
- Jede allgemeine Instanz benötigt zwei Adressen.
- Jede unternehmenskritisch Instanz benötigt vier Adressen.

**Beispiel**: Sie planen, mit drei universellen und zwei unternehmenskritischen verwalteten Instanzen zu arbeiten. Dies bedeutet, dass Sie 5 + 3 * 2 + 2 * 4 = 19 IP-Adressen benötigen. Da IP-Adressbereiche in Zweierpotenzen definiert sind, benötigen Sie den IP-Adressbereich von 32 (2^5) IP-Adressen. Aus diesem Grund müssen Sie das Subnetz mit der Subnetzmaske /27 reservieren.

> [!IMPORTANT]
> Die oben gezeigte Berechnung wird mit weiteren Verbesserungen demnächst veralten.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Machen Sie sich mit der [Verbindungsarchitektur der verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-connectivity-architecture.md) vertraut.
- Erfahren Sie, wie Sie [ein VNET erstellen, in dem Sie verwaltete Instanzen bereitstellen können](sql-database-managed-instance-create-vnet-subnet.md).
- Bei Problemen im Zusammenhang mit dem DNS lesen Sie [Konfigurieren eines benutzerdefinierten DNS](sql-database-managed-instance-custom-dns.md).
