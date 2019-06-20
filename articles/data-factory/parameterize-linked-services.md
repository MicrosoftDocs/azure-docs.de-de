---
title: Parametrisieren von verknüpften Diensten in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Data Factory verknüpfte Dienste parametrisieren und dynamische Werte zur Laufzeit übergeben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635543"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrisieren von verknüpften Diensten in Azure Data Factory

Sie können jetzt einen verknüpften Dienst parametrisieren und dynamische Werte zur Laufzeit übergeben. Wenn Sie beispielsweise eine Verbindung mit verschiedenen Datenbanken auf demselben Azure SQL-Datenbank-Server herstellen möchten, können Sie nun den Datenbanknamen in der verknüpften Dienstdefinition parametrisieren. Dadurch wird verhindert, dass Sie für jede Datenbank auf dem Azure SQL-Datenbank-Server einen verknüpften Dienst erstellen müssen. Sie können auch andere Eigenschaften in der Definition des verknüpften Diensts parametrisieren, z.B. den *Benutzernamen*.

Sie können mithilfe der Data Factory-Benutzeroberfläche im Azure-Portal oder einer Programmierschnittstelle verknüpfte Dienste parametrisieren.

> [!TIP]
> Es wird empfohlen, Kennwörter oder Geheimnisse nicht zu parametrisieren. Speichern Sie stattdessen alle Verbindungszeichenfolgen in Azure Key Vault, und parametrisieren Sie den *geheimen Namen*.

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Unterstützte Datenspeicher

Zurzeit wird die Parametrisierung verknüpfter Dienste in der Data Factory-Benutzeroberfläche im Azure-Portal für die folgenden Datenspeicher unterstützt. Für alle anderen Datenspeicher können Sie den verknüpften Dienst parametrisieren, indem Sie auf der Registerkarte **Verbindungen** das Symbol **Code** auswählen und den JSON-Editor verwenden.
- Azure SQL-Datenbank
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche

![Hinzufügen dynamischen Inhalts zur Definition des verknüpften Diensts](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Erstellen eines neuen Parameters](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
