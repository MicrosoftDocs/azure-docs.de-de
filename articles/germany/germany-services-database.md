---
title: Datenbankdienste von Azure Deutschland | Microsoft-Dokumentation
description: Dieser Artikel enthält einen Vergleich der SQL-Datenbank-Dienste für Azure Deutschland.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018562"
---
# <a name="azure-germany-database-services"></a>Datenbankdienste von Azure Deutschland

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>SQL-Datenbank
Azure SQL-Datenbank und Azure SQL Managed Instance V12 sind in Azure Deutschland allgemein verfügbar. Anleitungen zur Konfiguration der Metadatensichtbarkeit und bewährte Methoden für den Schutz finden Sie unter [Sicherheitscenter für SQL Server-Datenbank-Engine und Azure SQL-Datenbank](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database), in der [SQL-Datenbank-Dokumentation](../azure-sql/database/index.yml) und in der [Globalen Dokumentation zu SQL Managed Instance](../azure-sql/managed-instance/index.yml).

### <a name="variations"></a>Abweichungen
Die Adresse für SQL-Datenbank in Azure Deutschland unterscheidet sich von der Adresse in der globalen Azure-Umgebung:

| Dienstart | Globale Azure-Umgebung | Azure Deutschland |
| --- | --- | --- |
| SQL-Datenbank | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Einzelheiten zu Azure Cache for Redis und seiner Verwendung finden Sie in der [Azure Cache for Redis Documentation](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Abweichungen
Bei Azure Deutschland werden für den Zugriff auf und die Verwaltung von Azure Cache for Redis andere URLs verwendet als in der globalen Azure-Umgebung:

| Dienstart | Globale Azure-Umgebung | Azure Deutschland |
| --- | --- | --- |
| Cache-Endpunkt | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure-Portal | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> Ihre gesamten Skripts und Ihr Code müssen die richtigen Endpunkte und Umgebungen berücksichtigen. Weitere Informationen finden Sie in [Verwalten von Azure Cache for Redis mit Azure PowerShell](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md) unter „So stellen Sie eine Verbindung mit Microsoft Azure Deutschland her“.
>
>


## <a name="next-steps"></a>Nächste Schritte
Abonnieren Sie den [Azure Deutschland-Blog](/archive/blogs/azuregermany/), um weitere Informationen und Updates zu erhalten.