---
title: Herstellen einer Verbindung mit Azure Synapse Link (Vorschau) für Azure Cosmos DB
description: Hier erfahren Sie, wie Sie mithilfe von Azure Synapse Link eine Azure Cosmos DB-Datenbank mit einem Azure Synapse-Arbeitsbereich verbinden.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 0e4bcc13a2eaddbf394d8468b29eeff475119e2d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459023"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Herstellen einer Verbindung mit Azure Synapse Link für Azure Cosmos DB

In diesem Artikel wird beschrieben, wie Sie über Azure Synapse Analytics Studio mit Azure Synapse Link auf eine Azure Cosmos DB-Datenbank zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie eine Azure Cosmos DB-Datenbank mit Ihrem Arbeitsbereich verbinden können, benötigen Sie Folgendes:

* Eine vorhandene Azure Cosmos DB-Datenbank. Erstellen Sie alternativ ein neues Konto anhand der Schritte in [Schnellstart: Verwalten eines Azure Cosmos DB-Kontos](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account).
* Einen vorhandenen Azure Synapse-Arbeitsbereich, oder Sie erstellen einen neuen Arbeitsbereich anhand der Schritte in [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace).

> [!IMPORTANT]
> Azure Synapse Link für Azure Cosmos DB wird derzeit für Arbeitsbereiche unterstützt, in denen kein verwaltetes virtuelles Netzwerk aktiviert ist.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Aktivieren von Synapse Link in einem Azure Cosmos DB-Datenbankkonto

Es empfiehlt sich, Synapse Link für Azure Cosmos DB zu aktivieren, wenn Sie umfangreiche Analysen in Azure Cosmos DB ohne Beeinträchtigung der betrieblichen Leistung ausführen möchten. Synapse Link bietet HTAP-Funktionen für Container sowie integrierte Unterstützung in Azure Synapse.

## <a name="go-to-synapse-studio"></a>Wechseln zu Synapse Studio

Wählen Sie in Ihrem Azure Synapse-Arbeitsbereich die Option **Synapse Studio starten** aus. Wählen Sie auf der Synapse Studio-Homepage die Option **Daten** aus, um zum Datenobjekt-Explorer zu wechseln.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Herstellen einer Verbindung zwischen einer Azure Cosmos DB-Datenbank und einem Azure Synapse-Arbeitsbereich

Die Verbindung mit einer Azure Cosmos DB-Datenbank wird über einen verknüpften Dienst hergestellt. Mit einem verknüpften Azure Cosmos DB-Dienst könnten Sie Daten sowie Lese- und Schreibvorgänge von Apache Spark für Azure Synapse Analytics oder SQL in Azure Cosmos DB durchsuchen und untersuchen.

Über den Datenobjekt-Explorer können Sie mithilfe der folgenden Schritte direkt eine Verbindung mit einer Azure Cosmos DB-Datenbank herstellen:

1. Wählen Sie neben **Daten** das Symbol **+** aus.
1. Wählen Sie **Verbindung mit externen Daten herstellen** aus.
1. Wählen Sie die API aus, mit der Sie eine Verbindung herstellen möchten, z. B. **SQL-API** oder **API für MongoDB**.
1. Wählen Sie **Weiter**.
1. Verwenden Sie einen aussagekräftigen Anzeigenamen für den verknüpften Dienst. Der Name wird im Datenobjekt-Explorer angezeigt und von Azure Synapse-Laufzeiten verwendet, um eine Verbindung mit der Datenbank und Containern herzustellen.
1. Wählen Sie den **Azure Cosmos DB-Kontonamen** und den **Datenbanknamen** aus.
1. (Optional) Wird keine Region angegeben, werden Azure Synapse-Laufzeitvorgänge an die nächstgelegene Region weitergeleitet, in der der Analysespeicher aktiviert ist. Sie können die Region, die Ihre Benutzer für den Zugriff auf den Azure Cosmos DB-Analysespeicher verwenden sollen, auch manuell festlegen. Wählen Sie **Zusätzliche Verbindungseigenschaften** und dann **Neu** aus. Geben Sie unter **Eigenschaftsname** den Wert **PreferredRegions** ein. Legen Sie den **Wert** auf die gewünschte Region fest, z. B. **WestUS2**. (Zwischen den Wörtern und der Zahl dürfen sich keine Leerzeichen befinden.)
1. Klicken Sie auf **Erstellen**.

Azure Cosmos DB-Datenbanken werden im Abschnitt **Azure Cosmos DB** auf der Registerkarte **Verknüpft** angezeigt. Mit Azure Cosmos DB können Sie anhand der folgenden Symbole einen HTAP-fähigen Container von einem reinen OLTP-Container unterscheiden:

**Reiner OLTP-Container**:

![Visualisierung, die das Symbol für den OLTP-Container zeigt.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP-fähiger Container**:

![Visualisierung, die das Symbol für den HTAP-Container zeigt.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Schnelles Interagieren mit durch Code generierte Aktionen

Wenn Sie mit der rechten Maustaste in einen Container klicken, wird eine Liste mit Gesten angezeigt, die eine Spark- oder SQL-Laufzeit auslösen. Das Schreiben in einen Container erfolgt über den Transaktionsspeicher von Azure Cosmos DB und verbraucht Anforderungseinheiten.  

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu den unterstützten Features zwischen Azure Synapse und Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Informationen zum Abfragen des Analysespeichers mit Spark](./how-to-query-analytical-store-spark.md)
