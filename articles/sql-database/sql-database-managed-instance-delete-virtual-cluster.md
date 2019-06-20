---
title: Löschen von VNET nach Löschen der verwalteten Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie VNET nach Löschen der verwalteten Azure SQL-Datenbank-Instanz löschen.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791374"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Löschen des Subnetzes nach Löschen der verwalteten Azure SQL-Datenbank-Instanz

Dieser Artikel enthält Richtlinien zum manuellen Löschen des Subnetzes nach Löschen der letzten darin enthaltenen verwalteten Azure SQL-Datenbank-Instanz.

Der [virtuelle Cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture), der die gelöschte verwaltete Instanz enthalten hat, wird 12 Stunden lang vor der Instanzlöschung bewahrt. Der virtuelle Cluster wird mit Absicht aktiv gehalten, um eine schnellere Erstellung verwalteter Instanzen im selben Subnetz zu ermöglichen. Das Beibehalten eines leeren virtuellen Clusters ist kostenlos. Während dieses Zeitraums kann der mit dem Subnetz verbundene virtuelle Cluster nicht gelöscht werden.

Die sofortige Freigabe des von einem leeren virtuellen Cluster benutzten Subnetzes ist über das manuelle Löschen des virtuellen Clusters möglich. Das Löschen des virtuellen Clusters kann über das Azure-Portal oder die API für virtuelle Cluster durchgeführt werden.

> [!NOTE]
> Damit der Löschvorgang erfolgreich ist, sollte der virtuelle Cluster keine verwalteten Instanzen enthalten.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Löschen virtueller Cluster im Azure-Portal

Um einen virtuellen Cluster mit dem Azure-Portal zu löschen, suchen Sie mit der integrierten Suche nach den Ressourcen des virtuellen Clusters.

![Suchen Sie nach dem virtuellen Cluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Nachdem Sie den virtuellen Cluster gefunden haben, den Sie löschen möchten, wählen Sie diese Ressource und dann die Option „Löschen“ aus. Sie werden aufgefordert, das Löschen des virtuellen Clusters zu bestätigen.

![Löschen Sie den virtuellen Cluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Die Bestätigung, dass der virtuelle Cluster gelöscht wurde, erfolgt über die Azure-Portalbenachrichtigungen. Bei erfolgreichem Löschen des virtuellen Clusters wird das Subnetz sofort zur Wiederverwendung freigegeben.

## <a name="delete-virtual-cluster-using-api"></a>Löschen virtueller Cluster mit API

Um einen virtuellen Cluster mithilfe der API zu löschen, verwenden Sie die in der [Methode zum Löschen virtueller Cluster](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete) angegebenen URI-Parameter.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Machen Sie sich mit der [Verbindungsarchitektur der verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-connectivity-architecture.md) vertraut.
- Unter [Ändern eines vorhandenen virtuellen Netzwerks für die verwaltete Instanz](sql-database-managed-instance-configure-vnet-subnet.md) erfahren Sie, wie Sie Änderungen vornehmen können.
- Ein Tutorial zum Erstellen eines virtuellen Netzwerks, Erstellen einer verwalteten Instanz und Wiederherstellen einer Datenbank aus einer Datenbanksicherung finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started.md).
- Lesen Sie bei Problemen im Zusammenhang mit dem DNS den Artikel [Konfigurieren eines benutzerdefinierten DNS](sql-database-managed-instance-custom-dns.md).
