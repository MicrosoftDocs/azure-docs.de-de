---
title: Benutzerdefinierter DNS
titleSuffix: Azure SQL Managed Instance
description: In diesem Thema werden die Konfigurationsoptionen für ein benutzerdefiniertes DNS mit einer Azure SQL Managed Instance beschrieben.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 017698975706aa8501cd059351cf9a9d88594f77
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779899"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurieren eines benutzerdefinierten DNS für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance muss in einem [virtuellen Azure-Netzwerk (VNET)](../../virtual-network/virtual-networks-overview.md) bereitgestellt werden. Es gibt einige Szenarien (z. B. Datenbank-E-Mail, Verbindungsserver für andere SQL Server-Instanzen in Ihrer Cloud- oder Hybridumgebung), bei denen private Hostnamen von SQL Managed Instance aufgelöst werden müssen. In diesem Fall müssen Sie einen benutzerdefinierten DNS in Azure konfigurieren. 

Da SQL Managed Instance intern den gleichen DNS verwendet, muss der benutzerdefinierte DNS-Server so konfiguriert werden, dass er öffentliche Domänennamen auflösen kann.

> [!IMPORTANT]
> Verwenden Sie immer die vollqualifizierten Domänennamen (Fully-Qualified Domain Name, FQDN) für E-Mail-Server, die SQL-Server-Instanz und andere Dienste – auch wenn sie sich in Ihrer privaten DNS-Zone befinden. Verwenden Sie beispielsweise `smtp.contoso.com` für den E-Mail-Server, da `smtp` nicht ordnungsgemäß aufgelöst wird. Zum Erstellen eines Verbindungsservers oder einer Replikation mit Verweis auf virtuelle SQL Server-Computer im selben virtuellen Netzwerk sind ebenfalls ein vollqualifizierter Domänenname und ein Standard-DNS-Suffix erforderlich. Beispiel: `SQLVM.internal.cloudapp.net`. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Die Aktualisierung von VNet-DNS-Servern wirkt sich nicht sofort auf SQL Managed Instance aus. Die DNS-Konfiguration von SQL Managed Instance wird nach Ablauf der DHCP-Lease oder nach dem Plattformupgrade (je nachdem, was zuerst eintritt) aktualisiert. **Benutzer sollten ihre VNET-DNS-Konfiguration festlegen, bevor sie ihre erste verwaltete Instanz erstellen.**

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Ein Tutorial, in dem die Erstellung einer neuen verwalteten Instanz gezeigt wird, finden Sie unter [Schnellstart: Erstellen einer verwalteten Azure SQL-Instanz](instance-create-quickstart.md).
- Informationen zum Konfigurieren eines VNET für eine verwaltete Instanz finden Sie unter [Konfigurieren eines VNET für eine verwaltete Azure SQL-Datenbank-Instanz](connectivity-architecture-overview.md).