---
title: Datenbankdienste von Azure Deutschland | Microsoft-Dokumentation
description: Vergleich der Datenbankdienste für Azure Deutschland
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 45e058d887ae79164196ab310f93270accbfb389
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60684840"
---
# <a name="azure-germany-database-services"></a>Datenbankdienste von Azure Deutschland
## <a name="sql-database"></a>SQL-Datenbank
Azure SQL-Datenbank V12 ist allgemein in Azure Deutschland verfügbar. Anleitungen zur Konfiguration der Metadatensichtbarkeit und bewährte Methoden für den Schutz finden Sie unter [Sicherheitscenter für SQL Server-Datenbank-Engine und Azure SQL-Datenbank](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database) und in der [SQL-Datenbank-Dokumentation](../sql-database/index.yml).

### <a name="variations"></a>Abweichungen
Die Adresse für SQL-Datenbank in Azure Deutschland unterscheidet sich von der Adresse in der globalen Azure-Umgebung:

| Dienstart | Globale Azure-Umgebung | Azure Deutschland |
| --- | --- | --- |
| SQL-Datenbank | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Einzelheiten zu Azure Cache for Redis und seiner Verwendung finden Sie in der [Azure Cache for Redis Documentation](../azure-cache-for-redis/index.md).

### <a name="variations"></a>Abweichungen
Bei Azure Deutschland werden für den Zugriff auf und die Verwaltung von Azure Cache for Redis andere URLs verwendet als in der globalen Azure-Umgebung:

| Dienstart | Globale Azure-Umgebung | Azure Deutschland |
| --- | --- | --- |
| Cache-Endpunkt | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure-Portal | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> Ihre gesamten Skripts und Ihr Code müssen die richtigen Endpunkte und Umgebungen berücksichtigen. Weitere Informationen finden Sie in [Verwalten von Azure Cache for Redis mit Azure PowerShell](../azure-cache-for-redis/cache-howto-manage-redis-cache-powershell.md) unter „So stellen Sie eine Verbindung mit Microsoft Azure Deutschland her“.
>
>


## <a name="next-steps"></a>Nächste Schritte
Abonnieren Sie den [Azure Deutschland-Blog](https://blogs.msdn.microsoft.com/azuregermany/), um weitere Informationen und Updates zu erhalten.
