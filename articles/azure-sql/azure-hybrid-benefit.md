---
title: Azure-Hybridvorteil
titleSuffix: Azure SQL Database & SQL Managed Instance
description: In diesem Artikel erfahren Sie, wie Sie vorhandene SQL Server-Lizenzen für Rabatte bei Azure SQL-Datenbank und verwalteten SQL-Instanzen nutzen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4, devx-track-azurecli
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 872b36eae9142473885885d5bf5ad3fa10ae5ccb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841596"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure-Hybridvorteil: Azure SQL-Datenbank und verwaltete SQL-Instanzen
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Bei der bereitgestellten Computeebene des vCore-basierten Kaufmodells können Sie Ihre vorhandenen Lizenzen gegen Rabattpreise für Azure SQL-Datenbank und verwaltete SQL-Instanzen tauschen, indem Sie den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen. Mit diesem Azure-Vorteil können Sie bei Azure SQL-Datenbank und verwalteten SQL-Instanzen mindestens 30 Prozent sparen, indem Sie Ihre SQL Server-Lizenzen mit Software Assurance verwenden. Mit dem Kostenrechner auf der Seite [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) können Sie Ihre möglichen Einsparungen ausrechnen.  Beachten Sie, dass der Azure-Hybridvorteil nicht für „Azure SQL-Datenbank – Serverlos“ gilt.

> [!NOTE]
> Um zu Azure-Hybridvorteil zu wechseln, ist keine Downtime erforderlich.

![Preisstruktur für virtuelle Kerne](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Auswählen eines Lizenzmodells

Mit dem Azure-Hybridvorteil können Sie wahlweise nur die zugrunde liegende Azure-Infrastruktur bezahlen und Ihre vorhandene SQL Server-Lizenz für die eigentliche SQL Server-Datenbank-Engine verwenden (Grundpreis für Computeleistung), oder Sie bezahlen für die zugrunde liegende Infrastruktur sowie für die SQL Server-Lizenz (Preismodell mit enthaltener Lizenz).

Sie können Ihr Lizenzierungsmodell im Azure-Portal wie folgt auswählen oder ändern: 
- Wählen Sie bei neuen Datenbanken während der Erstellung auf der Registerkarte **Grundlagen** die Option **Datenbank konfigurieren** aus, und wählen Sie dann die Option aus, mit der Sie Geld sparen.
- Wählen Sie bei vorhandenen Datenbanken im Menü **Einstellungen** die Option **Konfigurieren** aus, und wählen Sie dann die Option aus, mit der Sie Geld sparen.

Sie können auch eine neue oder vorhandene Datenbank mithilfe einer der folgenden APIs konfigurieren:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Festlegen oder Aktualisieren des Lizenztyps mithilfe von PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Festlegen oder Aktualisieren des Lizenztyps mithilfe der Azure-Befehlszeilenschnittstelle:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST-API](#tab/rest)

Festlegen oder Aktualisieren des Lizenztyps mithilfe der REST-API:

- [Datenbanken – Erstellen oder Aktualisieren](/rest/api/sql/databases/createorupdate)
- [Datenbanken – Aktualisieren](/rest/api/sql/databases/update)
- [Verwaltete Instanzen – Erstellen oder Aktualisieren](/rest/api/sql/managedinstances/createorupdate)
- [Verwaltete Instanzen – Aktualisieren](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Fragen zum Azure-Hybridvorteil

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Gibt es für den Azure-Hybridvorteil für SQL Server Dualnutzungsrechte?

Sie verfügen für die Lizenz 180 Tage lang über Dualnutzungsrechte, um sicherzustellen, dass die Migrationen reibungslos durchgeführt werden können. Nach diesem Zeitraum von 180 Tagen können Sie die SQL Server-Lizenz nur in der Cloud in Azure SQL-Datenbank verwenden. Sie verfügen nicht mehr über duale Nutzungsrechte für die lokale und die Cloudumgebung.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Wie unterscheidet sich der Azure-Hybridvorteil für SQL Server von der License Mobility?

Wir bieten SQL Server-Kunden License Mobility-Leistungen im Rahmen von Software Assurance. Dies ermöglicht die erneute Zuweisung Ihrer Lizenzen zu mit einem Partner gemeinsam genutzten Servern. Sie können diese Leistung für Azure IaaS und AWS EC2 in Anspruch nehmen.

Der Azure-Hybridvorteil für SQL Server unterscheidet sich in zwei wichtigen Bereichen von License Mobility:

- Er ist mit wirtschaftlichen Vorteilen in Bezug auf das Verlagern von hochgradig virtualisierten Workloads nach Azure verbunden. Kunden mit SQL Server Enterprise Edition können in Azure für die SKU „Universell“ für jeden Kern, der lokal vorhanden ist, vier Kerne für hochgradig virtualisierte Anwendungen erhalten. Bei License Mobility ergeben sich bei der Verlagerung virtualisierter Workloads in die Cloud keine besonderen Kostenvorteile.
- Es wird ein PaaS-Ziel in Azure (SQL Managed Instance) bereitgestellt, das mit SQL Server in hohem Maß kompatibel ist.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Welche spezifischen Rechte gelten für den Azure-Hybridvorteil für SQL Server?

Azure SQL-Datenbank-Kunden bietet Azure-Hybridvorteil für SQL Server die folgenden Rechte:

|Lizenzspeicherbedarf|Was erhalten Sie beim Azure-Hybridvorteil für SQL Server?|
|---|---|
|SQL Server Enterprise Edition – Hauptkunden mit SA|<li>Zahlung des Basistarifs für SKU „Universell“ oder „Unternehmenskritisch“</li><br><li>1 Kern lokal = 4 Kerne in SKU „Universell“</li><br><li>1 Kern lokal = 1 Kern in SKU „Unternehmenskritisch“</li>|
|SQL Server Standard Edition – Hauptkunden mit SA|<li>Zahlung des Basistarifs nur für SKU „Universell“</li><br><li>1 Kern lokal = 1 Kern in SKU „Universell“</li>|
|||


## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Auswahl einer Azure SQL-Bereitstellungsoption finden Sie unter [Auswählen der richtigen Bereitstellungsoption in Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Einen Vergleich der Funktionen von Azure SQL-Datenbank und verwalteten SQL-Instanzen finden Sie unter [Funktionen von Azure SQL-Datenbank und verwalteten SQL-Instanzen](database/features-comparison.md).
