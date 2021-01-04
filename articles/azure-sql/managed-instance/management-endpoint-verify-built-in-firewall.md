---
title: Überprüfen der Portsicherheit der integrierten Firewall
description: Hier erfahren Sie, wie Sie den integrierten Firewallschutz in Azure SQL Managed Instance überprüfen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853270"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Überprüfen der integrierten Firewall von Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Die [obligatorischen Eingangssicherheitsregeln](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) von Azure SQL Managed Instance schreiben vor, dass die Verwaltungsports 9000, 9003, 1438, 1440 und 1452 für **alle Quellen** der Netzwerksicherheitsgruppe (NSG) offen sind, die SQL Managed Instance schützt. Zwar sind diese Ports auf NSG-Ebene offen, auf der Netzwerkebene sind sie jedoch durch die integrierte Firewall geschützt.

## <a name="verify-firewall"></a>Überprüfen der Firewall

Um diese Ports zu überprüfen, verwenden Sie ein beliebiges Überprüfungssicherheitstool zum Testen der Ports. Der folgende Screenshot zeigt die Verwendung eines solchen Tools.

![Überprüfen der integrierten Firewall](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SQL Managed Instance und zur Konnektivität finden Sie unter [Konnektivitätsarchitektur für Azure SQL Managed Instance](connectivity-architecture-overview.md).
