---
title: Erfassen von Beispieldaten im Azure-Daten-Explorer
description: Hier finden Sie Informationen zum Erfassen (Laden) von wetterbezogenen Beispieldaten im Azure-Daten-Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e80322cda671e2145cf3e65aa1457f1fa1827737
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759272"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Erfassen von Beispieldaten im Azure-Daten-Explorer

In diesem Artikel wird das Erfassen (Laden) von Beispieldaten in einer Azure-Daten-Explorer-Datenbank veranschaulicht. Es gibt [mehrere Möglichkeiten zum Erfassen von Daten](ingest-data-overview.md). Der Schwerpunkt dieses Artikel liegt auf einem allgemeinen Ansatz, der sich für Testzwecke eignet.

> [!NOTE]
> Sie verfügen bereits über diese Daten, wenn Sie Folgendes abgeschlossen haben [Schnellstart: Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md).

## <a name="prerequisites"></a>Voraussetzungen

[Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Erfassen von Daten

Das **StormEvents**-Beispieldataset enthält wetterbezogene Daten der [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an.

1. Wählen Sie in der oberen linken Ecke der Anwendung **Add cluster** (Cluster hinzufügen) aus.

1. Geben Sie im Dialogfeld **Add cluster** (Cluster hinzufügen) Ihre Cluster-URL im Format `https://<ClusterName>.<Region>.kusto.windows.net/` ein, und wählen Sie dann **Hinzufügen** aus.

1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Fügen Sie nach Abschluss der Erfassung die folgende Abfrage ein, wählen Sie im Fenster die Abfrage aus, und wählen Sie dann **Ausführen** aus.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Die Abfrage gibt die folgenden Ergebnisse aus den erfassten Beispieldaten zurück.

    ![Abfrageergebnisse](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Abfragen von Daten in Azure Data Explorer](web-query-data.md)

> [!div class="nextstepaction"]
> [Write queries](write-queries.md) (Schreiben von Abfragen)

> [!div class="nextstepaction"]
> [Azure-Daten-Explorer-Datenerfassung](ingest-data-overview.md)