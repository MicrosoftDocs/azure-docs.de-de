---
title: Grundlegende Konzepte für Apache Spark
description: Einführung in grundlegende Konzepte für Apache Spark in Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 8df7a2ecd2a6732a011267eb0efe00e8eaf2bef4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458753"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Grundlegende Konzepte für Apache Spark in Azure Synapse Analytics

Apache Spark ist ein Framework für die Parallelverarbeitung, das In-Memory-Verarbeitung unterstützt, um die Leistung von Big Data-Analyseanwendungen zu steigern. Apache Spark in Azure Synapse Analytics ist eine der cloudbasierten Apache Spark-Implementierungen von Microsoft. 

Azure Synapse vereinfacht das Erstellen und Konfigurieren von Spark-Funktionen in Azure. Azure Synapse bietet eine andere Implementierung der hier dokumentierten Spark-Funktionen.

## <a name="spark-pools"></a>Spark-Pools

Ein serverloser Apache Spark-Pool wird im Azure-Portal erstellt. Bei der Instanziierung eines Spark-Pools wird die Definition des Pools verwendet, um eine Spark-Instanz für die Datenverarbeitung zu erstellen. Wenn ein Spark-Pool erstellt wird, ist er nur in Form von Metadaten vorhanden. Es werden keine Ressourcen verbraucht, ausgeführt oder in Rechnung gestellt. Ein Spark-Pool verfügt über eine Reihe von Eigenschaften zur Steuerung der Merkmale einer Spark-Instanz. Zu diesen Merkmalen zählen unter anderem der Name, die Größe, das Skalierungsverhalten und die Gültigkeitsdauer.

Da im Zusammenhang mit der Erstellung von Spark-Pools keine Gebühren oder Ressourcenkosten entstehen, kann eine beliebige Anzahl mit beliebig vielen verschiedenen Konfigurationen erstellt werden. Auf Spark-Pools können auch Berechtigungen angewendet werden, um zu steuern, auf welche Pools Benutzer zugreifen können.

Eine bewährte Methode besteht darin, kleinere Spark-Pools für die Entwicklung und das Debugging und anschließend größere Spark-Pools für die Ausführung von Produktionsworkloads zu erstellen.

Informationen zum Erstellen eines Spark-Pools sowie zu allen verfügbaren Eigenschaften finden Sie unter [Schnellstart: Erstellen eines neuen serverlosen Apache Spark-Pools über das Azure-Portal](../quickstart-create-apache-spark-pool-portal.md).

## <a name="spark-instances"></a>Spark-Instanzen

Spark-Instanzen werden erstellt, wenn Sie eine Verbindung mit einem Spark-Pool herstellen, eine Sitzung erstellen und einen Auftrag ausführen. Da mehrere Benutzer Zugriff auf einen einzelnen Spark-Pool haben können, wird für jeden Benutzer, der eine Verbindung herstellt, eine neue Spark-Instanz erstellt. 

Wenn Sie einen zweiten Auftrag übermitteln und Kapazität im Pool verfügbar ist, verfügt auch die Spark-Instanz über Kapazität. In diesem Fall wird der Auftrag von der bereits vorhandenen Instanz verarbeitet. Andernfalls wird eine neue Spark-Instanz erstellt, wenn Kapazität auf Poolebene verfügbar ist.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1

- Sie erstellen einen Spark-Pool namens „SP1“ mit einer festen Clustergröße von 20 Knoten.
- Sie übermitteln einen Notebookauftrag (J1), der zehn Knoten beansprucht. Daraufhin wird eine Spark-Instanz (SI1) erstellt, um den Auftrag zu verarbeiten.
- Anschließend übermitteln Sie einen weiteren Auftrag (J2), der zehn Knoten beansprucht, und da noch Kapazität im Pool und in der Instanz verfügbar ist, wird „J2“ von „SI1“ verarbeitet.
- Wären für „J2“ elf Knoten erforderlich gewesen, hätte die Kapazität in „SP1“ oder „SI1“ nicht ausgereicht. In diesem Fall gilt: Wenn „J2“ von einem Notebook stammt, wird der Auftrag abgelehnt. Wenn „J2“ aus einem Batchauftrag stammt, wird er in die Warteschlange eingereiht.

### <a name="example-2"></a>Beispiel 2

- Sie erstellen einen Spark-Pool namens „SP2“ mit automatischer Skalierung zwischen zehn und 20 Knoten.
- Sie übermitteln einen Notebookauftrag (J1), der zehn Knoten beansprucht. Daraufhin wird eine Spark-Instanz (SI1) erstellt, um den Auftrag zu verarbeiten.
- Anschließend übermitteln Sie einen weiteren Auftrag (J2), der zehn Knoten beansprucht, und da noch Kapazität im Pool verfügbar ist, wird die Instanz zur Verarbeitung von „J2“ automatisch auf 20 Knoten vergrößert.

### <a name="example-3"></a>Beispiel 3

- Sie erstellen einen Spark-Pool namens „SP1“ mit einer festen Clustergröße von 20 Knoten.
- Sie übermitteln einen Notebookauftrag (J1), der zehn Knoten beansprucht. Daraufhin wird eine Spark-Instanz (SI1) erstellt, um den Auftrag zu verarbeiten.
- Ein anderer Benutzer (U2) übermittelt einen Auftrag (J3), der zehn Knoten beansprucht, und es wird eine neue Spark-Instanz (SI2) erstellt, um den Auftrag zu verarbeiten.
- Anschließend übermitteln Sie einen weiteren Auftrag (J2), der zehn Knoten beansprucht, und da noch Kapazität im Pool und in der Instanz verfügbar ist, wird „J2“ von „SI1“ verarbeitet.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Kontingente und Ressourceneinschränkungen in Apache Spark für Azure Synapse

### <a name="workspace-level"></a>Arbeitsbereichsebene

Jeder Azure Synapse-Arbeitsbereich verfügt über ein Standardkontingent von virtuellen Kernen, das für Spark verwendet werden kann. Das Kontingent wird zwischen dem Benutzerkontingent und dem Datenflusskontingent aufgeteilt, sodass keines der beiden Verwendungsmuster alle virtuellen Kerne im Arbeitsbereich erschöpfend verwendet. Das Kontingent ist in Abhängigkeit vom Typ Ihres Abonnements jeweils anders, wird jedoch symmetrisch zwischen Benutzer und Datenfluss verteilt. Wenn Sie allerdings mehr virtuelle Kerne anfordern, als im Arbeitsbereich verbleiben, erhalten Sie die folgende Fehlermeldung:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

Der Link in der Meldung verweist auf diesen Artikel.

Im folgenden Artikel wird beschrieben, wie Sie eine Erhöhung des Arbeitsbereichskontingents für virtuelle Kerne anfordern.

- Wählen Sie als Diensttyp „Azure Synapse Analytics“ aus.
- Wählen Sie im Fenster „Kontingentdetails“ die Option „Apache Spark (vCore) pro Arbeitsbereich“ aus.

[Anfordern einer Kapazitätserhöhung über das Azure-Portal](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Spark-Poolebene

Wenn Sie einen Spark-Pool definieren, definieren Sie de facto ein Kontingent pro Benutzer für diesen Pool. Wenn Sie mehrere Notebooks, Aufträge oder eine Mischung aus beidem ausführen, kann das Poolkontingent erschöpft werden. Kommt es hierzu, wird eine Fehlermeldung wie die folgende generiert.

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Um dieses Problem zu beheben, müssen Sie Ihre Nutzung der Poolressourcen verringern, bevor Sie eine neue Ressourcenanforderung absenden, indem Sie ein Notebook oder einen Auftrag ausführen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark-Dokumentation](https://spark.apache.org/docs/2.4.5/)
