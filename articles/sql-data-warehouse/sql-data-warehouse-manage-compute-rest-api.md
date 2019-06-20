---
title: Anhalten, Fortsetzen und Skalieren mit REST in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Verwalten der Rechenleistung in SQL Data Warehouse über REST-APIs.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5b8652a0b08b426e708a909ff988e51eee9c0821
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476060"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST-APIs für Azure SQL Data Warehouse
REST-APIs für die Verwaltung von Computeressourcen in Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Skalieren von Computeressourcen
Um die DWUs (Data Warehouse Units) zu ändern, verwenden Sie die REST-API zum [Erstellen oder Aktualisieren einer Datenbank](/rest/api/sql/databases/createorupdate). Im folgenden Beispiel werden die DWUs für die Datenbank „MySQLDW“, die auf dem Server „MyServer“ gehostet wird, auf „DW1000“ festgelegt. Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Anhalten von Computeressourcen

Zum Anhalten einer Datenbank verwenden Sie die REST-API zum [Anhalten der Datenbank](/rest/api/sql/databases/pause) . Im folgenden Beispiel wird die auf dem Server „Server01“ gehostete Datenbank mit der Bezeichnung „Database02“ angehalten. Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Fortsetzen von Computeressourcen

Verwenden Sie die REST-API zum [Fortsetzen der Datenbank](/rest/api/sql/databases/resume) , um eine Datenbank zu starten. Im folgenden Beispiel wird die auf dem Server „Server01“ gehostete Datenbank mit der Bezeichnung „Database02“ gestartet. Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Überprüfen des Datenbankzustands

> [!NOTE]
> Derzeit kann beim Überprüfen des Datenbankzustands ONLINE zurückgegeben werden, während die Datenbank den Online-Workflow ausführt. Dies führt zu Verbindungsfehlern. Sie müssen möglicherweise eine Verzögerung von 2 bis 3 Minuten in Ihrem Anwendungscode hinzufügen, wenn Sie diesen API-Aufruf zum Auslösen von Verbindungsversuchen verwenden.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Abrufen des Wartungszeitplans
Überprüfen Sie den Wartungsplan, der für ein Data Warehouse festgelegt wurde. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Festlegen des Wartungszeitplans
Einrichten und Aktualisieren eines Wartungsplans für ein vorhandenes Data Warehouse.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Verwalten von Computeressourcen in Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md).

