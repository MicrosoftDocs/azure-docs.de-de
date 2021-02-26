---
title: Parametrisieren von verknüpften Diensten in Azure Data Factory
description: Erfahren Sie, wie Sie in Azure Data Factory verknüpfte Dienste parametrisieren und dynamische Werte zur Laufzeit übergeben.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
ms.openlocfilehash: fbb7cb6fd0041ab77e649b14712907d916eaa449
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372925"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrisieren von verknüpften Diensten in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können jetzt einen verknüpften Dienst parametrisieren und dynamische Werte zur Laufzeit übergeben. Wenn Sie beispielsweise Verbindungen mit verschiedenen Datenbanken auf demselben logischen SQL-Server herstellen möchten, können Sie nun den Datenbanknamen in der verknüpften Dienstdefinition parametrisieren. Dadurch wird vermieden, dass Sie für jede Datenbank auf dem logischen SQL-Server einen verknüpften Dienst erstellen müssen. Sie können auch andere Eigenschaften in der Definition des verknüpften Diensts parametrisieren, z.B. den *Benutzernamen*.

Sie können mithilfe der Data Factory-Benutzeroberfläche im Azure-Portal oder einer Programmierschnittstelle verknüpfte Dienste parametrisieren.

> [!TIP]
> Es wird empfohlen, Kennwörter oder Geheimnisse nicht zu parametrisieren. Speichern Sie stattdessen alle Verbindungszeichenfolgen in Azure Key Vault, und parametrisieren Sie den *geheimen Namen*.

> [!Note]
> Es gibt derzeit noch einen ungelösten Fehler bei Verwendung von „-“ in Parameternamen. Es wird empfohlen, Namen ohne „-“ zu verwenden, bis der Fehler behoben wurde.

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Unterstützte Arten verknüpfter Dienste

Sie können jede Art von verknüpftem Dienst parametrisieren.
Für die Erstellung verknüpfter Dienste auf der Benutzeroberfläche bietet Data Factory eine integrierte Parametrisierungsfunktion für die folgenden Arten verknüpfter Dienste. Im Blatt zur Erstellung/Bearbeitung von verknüpften Diensten finden Sie Optionen für neue Parameter und können dynamische Inhalte hinzufügen.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL-API)
- Azure Database for MySQL
- Azure Databricks
- Azure-Schlüsseltresor
- Azure SQL-Datenbank
- Verwaltete Azure SQL-Instanz
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- Generisches HTTP
- Generisches REST

Für andere Arten verknüpfter Dienste, die nicht in der obigen Liste enthalten sind, können Sie den verknüpften Dienst parametrisieren, indem Sie die JSON über die Benutzeroberfläche bearbeiten:

- Im Blatt zur Erstellung/Bearbeitung von verknüpften Diensten erweitern Sie die Option „Erweitert“ am unteren Rand. Aktivieren Sie dann das Kontrollkästchen „Dynamische Inhalte im JSON-Format angeben“, und geben Sie die JSON-Nutzlast für den verknüpften Dienst an. 
- Oder, nachdem Sie einen verknüpften Dienst ohne Parametrisierung erstellt haben, suchen Sie in [Verwaltungshub](author-visually.md#management-hub) -> Verknüpfte Dienste den bestimmten verknüpften Dienst, und klicken Sie auf „Code“ (Schaltfläche „{}“), um den JSON-Code zu bearbeiten. 

Informationen zum Definieren von Parametern und Verweisen auf den Parameter mithilfe von ` @{linkedService().paraName} ` finden Sie im Abschnitt mit dem [JSON-Beispiel](#json) zum Hinzufügen von ` parameters`.

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
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
