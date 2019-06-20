---
title: Verwenden einer ODBC-Verbindung (Open Database Connectivity) mit Azure Data Explorer zum Visualisieren von Daten mit Tableau
description: In diesem Artikel erfahren Sie, wie Sie eine ODBC-Verbindung (Open Database Connectivity) mit Azure Data Explorer verwenden, um Daten mit Tableau zu visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498570"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualisieren von Daten über Azure Data Explorer in Tableau

 [Tableau](https://www.tableau.com/) ist eine Plattform für visuelle Analysen für Business Intelligence. Um eine Verbindung mit Azure Data Explorer aus Tableau herzustellen und Daten aus einem Beispielcluster zu importieren, verwenden Sie den SQL Server ODBC-Treiber (Open Database Connectivity). 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* [Herstellen einer Verbindung mit Azure Data Explorer mithilfe von ODBC](connect-odbc.md) – Den Einsatz des SQL Server ODBC-Treibers, um Azure Data Explorer aus Tableau zu verbinden. 

* Tableau Desktop, Vollversion oder [Testversion](https://www.tableau.com/products/desktop/download).

* Ein Cluster, der die StormEvents-Beispieldaten enthält Weitere Informationen finden Sie unter [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualisieren von Daten in Tableau 

Nachdem Sie die Konfiguration von ODBC abgeschlossen haben, können Sie Beispieldaten in Tableau importieren.

1. Wählen Sie im linken Menü in Tableau Desk **Andere Datenbanken (ODBC)** aus.

    ![Herstellen einer Verbindung mit ODBC](media/tableau/connect-odbc.png)

1. Wählen Sie für **DSN** die Datenquelle aus, die Sie für ODBC erstellt haben, und wählen Sie dann **Anmelden** aus.

    ![Anmeldung bei ODBC](media/tableau/odbc-sign-in.png)

1. Wählen Sie für **Datenbank** die Datenbank auf Ihrem Beispielcluster aus, beispielsweise *TestDatabase*. Für **Schema** wählen Sie *dbo* aus, und für **Tabelle** wählen Sie die Beispieltabelle *StormEvents* aus.

    ![Datenbank und Tabelle auswählen](media/tableau/select-database-table.png)

1. Tableau zeigt nun das Schema für die Beispieldaten an. Wählen Sie **Jetzt aktualisieren** aus, um die Daten in Tableau zu importieren.

    ![Aktualisieren von Daten](media/tableau/update-data.png)

    Wenn die Daten importiert sind, zeigt Tableau Zeilen von Daten an, ähnlich der folgenden Abbildung.

    ![Resultset](media/tableau/result-set.png)

1. Sie können nun basierend auf den Daten, die Sie aus Azure Data Explorer eingebunden haben, in Tableau Visualisierungen erstellen. Weitere Informationen finden Sie unter [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)