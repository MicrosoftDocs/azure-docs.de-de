---
title: 'Tutorial: Erste Schritte mit dem Synapse Knowledge Center'
description: In diesem Tutorial erfahren Sie, wie Sie das Synapse Knowledge Center verwenden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 611d2163e242d7851398821344c3ed595df364cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460265"
---
# <a name="explore-the-synapse-knowledge-center"></a>Überblick über Synapse Knowledge Center

In diesem Tutorial erfahren Sie, wie Sie Synapse Studio Knowledge Center verwenden.

## <a name="getting-to-the-knowledge-center"></a>Aufrufen des Knowledge Center

Es gibt zwei Möglichkeiten, das Knowledge Center in Synapse Studio aufzurufen:

  1. Klicken Sie im Start-Hub oben rechts auf **Learn**.
  2. Klicken Sie oben im Menü auf **?** und dann auf **Knowledge Center**.

Wählen Sie eine der beiden Methoden aus, und öffnen Sie das **Knowledge Center**.

## <a name="overview"></a>Übersicht

In **Knowledge Center** haben Sie drei Möglichkeiten:
* **Use samples immediately** (Beispiele sofort verwenden). Wenn Sie ein kurzes Beispiel für die Funktionsweise von Synapse sehen möchten, wählen Sie diese Option aus.
* **Katalog durchsuchen** Mit dieser Option können Sie Beispieldatasets verknüpfen und Beispielcode in Form von SQL-Skripts, Notebooks und Pipelines hinzufügen.
* **Tour Synapse studio** (Einführung in Synapse Studio). Mit dieser Option können Sie sich einen kurzen Überblick über die Hauptbereiche von Synapse Studio verschaffen. Dies ist hilfreich, wenn Sie Synapse Studio noch nie verwendet haben.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Untersuchen von Blobspeicher mit einem serverlosen SQL-Pool

1. Navigieren Sie zu **Knowledge Center**, und klicken Sie auf **Use samples immediately** (Beispiele sofort verwenden).
1. Wählen Sie **Query data with SQL** (Daten mit SQL abfragen) aus. 
1. Klicken Sie auf **Use samples immediately** (Beispiele sofort verwenden).
1. Es wird ein neues SQL-Skript erstellt.
1. Scrollen Sie zur ersten Abfrage (Zeilen 28 bis 32), und wählen Sie den Abfragetext aus.
1. Klicken Sie auf "Ausführen". Der ausgewählte Text wird ausgeführt.

## <a name="loading-more-nyc-taxi-data"></a>Laden von weiteren NYC Taxi-Daten
1. Navigieren Sie zum **Knowledge Center**, und klicken Sie auf **Katalog durchsuchen**. 
1. Wählen Sie oben die Registerkarte **SQL scripts** (SQL-Skripts) aus.
1. Wählen Sie **Load the New York Taxicab dataset** (New York Taxicab-Dataset laden) aus.
1. Wählen Sie unter **Inputs** (Eingabe) **Select an existing pool** (Vorhandenen Pool auswählen) und dann **SQLDB1** aus.
1. Klicken Sie auf **Open Script** (Skript öffnen).
1. Ein neues SQL-Skript wird angezeigt.
1. Klicken Sie auf **Run**.
1. Dadurch werden mehrere Tabellen für alle NYC Taxi-Daten erstellt und mit dem T-SQL-Befehl COPY geladen.

    > [!NOTE] 
    > Wenn Sie den Beispielkatalog für SQL-Skripts mit einem dedizierten SQL-Pool (ehemals SQL DW) verwenden, können Sie nur einen vorhandenen dedizierten SQL-Pool (ehemals SQL DW) verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](get-started.md)
* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden eines serverlosen SQL-Pools](quickstart-sql-on-demand.md)
