---
title: Erstellen eines virtuellen Netzwerks für eine verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie ein virtuelles Netzwerk erstellen, in dem Sie eine verwaltete Azure SQL-Datenbank-Instanz bereitstellen können.
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
ms.date: 01/15/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60700476"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Erstellen eines virtuellen Netzwerks für eine verwaltete Azure SQL-Datenbank-Instanz

In diesem Artikel erfahren Sie, wie Sie ein gültiges virtuelles Netzwerk und ein entsprechendes Subnetz erstellen, in dem Sie eine verwaltete Azure SQL-Datenbank-Instanz bereitstellen können.

Die verwaltete Azure SQL-Datenbank-Instanz muss in einem [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) bereitgestellt werden. Diese Bereitstellung ermöglicht die folgenden Szenarien:

- Schützen der privaten IP-Adresse
- Direktes Herstellen einer Verbindung mit einer verwalteten Instanz von einem lokalen Netzwerk
- Herstellen einer Verbindung zwischen einer verwalteten Instanz und einem Verbindungsserver oder einem anderen lokalen Datenspeicher
- Herstellen einer Verbindung zwischen einer verwalteten Instanz und Azure-Ressourcen  

> [!Note]
> Sie sollten [die Größe des Subnetzes für die verwaltete Instanz bestimmen](sql-database-managed-instance-determine-size-vnet-subnet.md), bevor Sie die erste Instanz bereitstellen. Die Größe des Subnetzes kann nach Einbinden der Ressourcen nicht mehr geändert werden.
>
> Wenn Sie die Verwendung eines vorhandenen virtuellen Netzwerks beabsichtigen, müssen Sie diese Netzwerkkonfiguration ändern, um Ihre verwaltete Instanz einzubeziehen. Weitere Informationen finden Sie unter [Ändern eines vorhandenen virtuellen Netzwerks für die verwaltete Instanz](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Die einfachste Möglichkeit zum Erstellen und Konfigurieren eines virtuellen Netzwerks ist die Verwendung einer Azure Resource Manager-Bereitstellungsvorlage.

1. Melden Sie sich beim Azure-Portal an.

2. Wählen Sie die Schaltfläche **Deploy to Azure** (In Azure bereitstellen):

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Mit dieser Schaltfläche wird ein Formular zum Konfigurieren der Netzwerkumgebung geöffnet, in der Sie die verwaltete Instanz bereitstellen können.

   > [!Note]
   > Mit dieser Azure Resource Manager-Vorlage wird ein virtuelles Netzwerk mit zwei Subnetzen bereitgestellt. Ein Subnetz namens **ManagedInstances** ist für die verwaltete Instanz reserviert und verfügt über eine vorkonfigurierte Routentabelle. Das andere Subnetz namens **Default** wird für andere Ressourcen verwendet, die auf die verwaltete Instanz zugreifen müssen (z. B. virtuelle Azure-Computer).

3. Konfigurieren Sie die Netzwerkumgebung. Im folgenden Formular können Sie Parameter für Ihre Netzwerkumgebung konfigurieren:

   ![Resource Manager-Vorlage zum Konfigurieren des Azure-Netzwerks](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Sie können die Namen des virtuellen Netzwerks und der Subnetze ändern und die Ihren Netzwerkressourcen zugeordneten IP-Adressbereiche anpassen. Sobald Sie auf die Schaltfläche **Kaufen** klicken, wird mit diesem Formular Ihre Umgebung erstellt und konfiguriert. Wenn Sie keine zwei Subnetze benötigen, können Sie das Standardsubnetz löschen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Machen Sie sich mit der [Verbindungsarchitektur der verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-connectivity-architecture.md) vertraut.
- Unter [Ändern eines vorhandenen virtuellen Netzwerks für die verwaltete Instanz](sql-database-managed-instance-configure-vnet-subnet.md) erfahren Sie, wie Sie Änderungen vornehmen können.
- Ein Tutorial zum Erstellen eines virtuellen Netzwerks, Erstellen einer verwalteten Instanz und Wiederherstellen einer Datenbank aus einer Datenbanksicherung finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started.md).
- Lesen Sie bei Problemen im Zusammenhang mit dem DNS den Artikel [Konfigurieren eines benutzerdefinierten DNS](sql-database-managed-instance-custom-dns.md).
