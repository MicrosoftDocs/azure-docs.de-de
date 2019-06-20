---
title: Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Power BI
description: 'In diesem Artikel erfahren Sie, wie Sie eine der drei Optionen zum Visualisieren von Daten in Power BI verwenden: den Power BI-Connector für Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 0bfacc968a04f8ef9e39a31cff5e81cf4e04c6fb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494619"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Power BI

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Power BI ist eine Business Analytics-Lösung, mit der Sie Ihre Daten visualisieren und die Ergebnisse in Ihrer Organisation teilen können.

Azure Data Explorer bietet drei Optionen für die Verbindungsherstellung mit Daten in Power BI: Verwenden des integrierten Connectors, Importieren einer Abfrage aus Azure Data Explorer oder Verwenden einer SQL-Abfrage. In diesem Artikel wird der integrierte Connector verwendet, um Daten abzurufen und in einem Power BI-Bericht zu visualisieren.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* Ein Organisations-E-Mail-Konto, das Azure Active Directory angehört, um eine Verbindung mit dem [Azure Data Explorer-Hilfecluster](https://dataexplorer.azure.com/clusters/help/databases/samples) herstellen zu können.

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wählen Sie **KOSTENLOS HERUNTERLADEN** aus)

## <a name="get-data-from-azure-data-explorer"></a>Abrufen von Daten aus Azure Data Explorer

Sie stellen zunächst eine Verbindung mit dem Azure Data Explorer-Hilfecluster her und fügen dann eine Teilmenge der Daten aus der Tabelle *StormEvents* ein. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Wählen Sie in Power BI Desktop auf der Registerkarte **Start** die Option **Daten abrufen** und anschließend **Mehr** aus.

    ![Datensammlung](media/power-bi-connector/get-data-more.png)

1. Suchen Sie nach *Azure Data Explorer*, wählen Sie **Azure Data Explorer** und dann **Verbinden** aus.

    ![Suchen und Abrufen von Daten](media/power-bi-connector/search-get-data.png)

1. Füllen Sie im Bildschirm **Azure Data Explorer (Kusto)** das Formular mit den folgenden Informationen aus.

    ![Optionen für Cluster, Datenbank und Tabelle](media/power-bi-connector/cluster-database-table.png)

    **Einstellung** | **Wert** | **Feldbeschreibung**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | Die URL für den Hilfecluster. Für andere Cluster hat die URL das Format *https://\<Clustername\>.\<Region\>.kusto.windows.net*. |
    | Datenbank | Nicht ausfüllen | Eine Datenbank, die in dem Cluster gehostet wird, mit dem Sie eine Verbindung herstellen. Wird in einem späteren Schritt ausgewählt. |
    | Tabellenname | Nicht ausfüllen | Eine der Tabellen in der Datenbank oder eine Abfrage wie <code>StormEvents \| take 1000</code> Wird in einem späteren Schritt ausgewählt. |
    | Erweiterte Optionen | Nicht ausfüllen | Optionen für Ihre Abfragen (etwa die Größe des Resultsets). |
    | Datenkonnektivitätsmodus | *DirectQuery* | Bestimmt, ob Power BI die Daten importiert oder direkt eine Verbindung mit der Datenquelle herstellt. Für diesen Connector sind beide Optionen geeignet. |
    | | | |

1. Falls Sie noch nicht über eine Verbindung mit dem Hilfecluster verfügen, melden Sie sich an. Melden Sie sich mit einem Organisationskonto an, und wählen Sie dann **Verbinden** aus.

    ![Anmelden](media/power-bi-connector/sign-in.png)

1. Erweitern Sie im Bildschirm **Navigator** die Datenbank **Samples**, und wählen Sie **StormEvents** > **Bearbeiten** aus.

    ![Tabelle auswählen](media/power-bi-connector/select-table.png)

    Die Tabelle wird im Power Query-Editor geöffnet. Dort können Sie die Zeilen und Spalten bearbeiten, bevor die Daten importiert werden.

1. Wählen Sie im Power Query-Editor den Pfeil neben der Spalte **DamageCrops** und anschließend **Absteigend sortieren** aus.

    ![Absteigendes Sortieren von „DamageCrops“](media/power-bi-connector/sort-descending.png)

1. Wählen Sie auf der Registerkarte **Start** die Option **Zeilen beibehalten** und anschließend **Erste Zeilen beibehalten** aus. Geben Sie den Wert *1.000* ein, um die ersten 1.000 Zeilen der sortierten Tabelle zu importieren.

    ![Beibehalten der ersten Zeilen](media/power-bi-connector/keep-top-rows.png)

1. Klicken Sie auf der Registerkarte **Start** auf **Schließen und übernehmen**.

    ![Schließen und übernehmen](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualisieren von Daten in einem Bericht

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Bericht, den Sie für diesen Artikel erstellt haben, nicht mehr benötigen, löschen Sie die Power BI Desktop-Datei (PBIX-Datei).

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe einer importierten Abfrage in Power BI](power-bi-imported-query.md)
