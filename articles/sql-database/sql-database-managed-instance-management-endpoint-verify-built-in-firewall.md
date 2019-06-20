---
title: Ermitteln einer Firewall, die in eine verwaltete Azure SQL-Datenbank-Instanz integriert ist | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den integrierten Firewallschutz in einer verwalteten Azure SQL-Datenbank-Instanz überprüfen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 774455a2901782ef52b213c6a13c17636e28b1a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699637"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Überprüfen der integrierten Firewall einer verwalteten Instanz

Die [obligatorischen Eingangssicherheitsregeln](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) der verwalteten Instanz schreiben vor, dass die Verwaltung der Ports 9000, 9003, 1438, 1440 und 1452 für **alle Quellen** der Netzwerksicherheitsgruppe (NSG) offen ist, die die verwaltete Instanz schützt. Zwar sind diese Ports auf NSG-Ebene offen, auf der Netzwerkebene sind sie jedoch durch die integrierte Firewall geschützt.

## <a name="verify-firewall"></a>Überprüfen der Firewall

Um diese Ports zu überprüfen, verwenden Sie ein beliebiges Überprüfungssicherheitstool zum Testen der Ports. Der folgende Screenshot zeigt die Verwendung eines solchen Tools.

![Überprüfen der integrierten Firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verwalteten Instanzen und zur Konnektivität finden Sie unter [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md) (Konnektivitätsarchitektur der verwalteten Azure SQL-Datenbank-Instanz).