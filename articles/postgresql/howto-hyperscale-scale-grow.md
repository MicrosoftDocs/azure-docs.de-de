---
title: Skalieren von Servergruppen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Anpassen von Servergruppenspeicher-, Datenträger- und CPU-Ressourcen, um höhere Auslastungen zu bewältigen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026419"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalieren einer Hyperscale (Citus)-Servergruppe

Azure Database for PostgreSQL – Hyperscale (Citus) unterstützt Self-Service-Skalierung, um höhere Auslastungen zu bewältigen. Das Azure-Portal vereinfacht das Hinzufügen neuer Workerknoten und das Erhöhen der Anzahl der virtuellen Kerne vorhandener Knoten. Das Hinzufügen von Knoten verursacht keine Ausfallzeiten, und auch das Verschieben von Shards auf die neuen Knoten (das so genannte [Ausgleichen von Shards](howto-hyperscale-scale-rebalance.md)) erfolgt ohne Unterbrechung der Abfragen.

## <a name="add-worker-nodes"></a>Hinzufügen von Workerknoten

Wenn Sie Knoten hinzufügen möchten, navigieren Sie in Ihrer Servergruppe „Hyperscale (Citus)“ zur Registerkarte **Compute + Speicher**.  Ziehen Sie den Schieberegler für **Anzahl von Workerknoten**, um den Wert zu ändern.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Schieberegler für Ressourcen":::

Klicken Sie auf die Schaltfläche **Speichern**, damit der geänderte Wert wirksam wird.

> [!NOTE]
> Nachdem Sie die Anzahl der Workerknoten erhöht und gespeichert haben, kann sie mit dem Schieberegler nicht mehr verringert werden.

> [!NOTE]
> Um neu hinzugefügte Knoten optimal zu nutzen, müssen Sie die verteilte [Shardtabelle ausgleichen](howto-hyperscale-scale-rebalance.md). Dabei werden einige [Shards](concepts-hyperscale-distributed-data.md#shards) von vorhandenen Knoten auf neue Knoten verschoben.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Erhöhen und Verringern der Anzahl von virtuellen Kernen auf Knoten

Zusätzlich zum Hinzufügen neuer Knoten können Sie die Funktionen vorhandener Knoten erweitern. Das Anpassen der Computekapazität nach oben und unten kann für Leistungsexperimente und kurz- oder langfristige Änderungen der Datenverkehrsanforderungen nützlich sein.

Um die virtuellen Kerne für alle Workerknoten zu ändern, passen Sie den Schieberegler **Virtuelle Kerne** unter **Konfiguration (pro Workerknoten)** an. Die virtuellen Kerne des Koordinatorknotens können unabhängig voneinander angepasst werden. Schieben Sie den Schieberegler für **virtuelle Kerne** und **Konfiguration (Koordinatorknoten)** an die gewünschte Position.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Leistungsoptionen](concepts-hyperscale-configuration-options.md) für Servergruppen.
- [Ausgleichen verteilter Tabellenshards](howto-hyperscale-scale-rebalance.md), damit alle Workerknoten an parallelen Abfragen teilnehmen können
