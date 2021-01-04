---
title: Synchronisieren von Daten aus Azure SQL Edge mithilfe der SQL-Datensynchronisierung
description: Erfahren Sie, wie Sie Daten aus Azure SQL Edge mithilfe der Azure SQL-Datensynchronisierung synchronisieren
keywords: SQL Edge, Daten aus SQL Edge synchronisieren, SQL Edge Datensynchronisierung
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394899"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Synchronisieren von Daten zwischen SQL Edge und Azure SQL-Datenbank mithilfe der SQL-Datensynchronisierung

In diesem Tutorial erfahren Sie, wie Sie eine *Synchronisierungsgruppe* der Azure SQL-Datensynchronisierung verwenden, um eine inkrementelle Synchronisierung von Daten zwischen Azure SQL Edge und Azure SQL-Datenbank durchzuführen. Die SQL-Datensynchronisierung ist ein Dienst, der auf Azure SQL-Datenbank basiert und mit dem Sie die ausgewählten Daten bidirektional über mehrere Datenbanken in Azure SQL-Datenbank und SQL Server-Instanzen hinweg synchronisieren können. Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter [Azure SQL-Datensynchronisierung](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Da SQL Edge auf den neuesten Versionen der [SQL Server-Datenbank-Engine](/sql/sql-server/sql-server-technical-documentation/) basiert, kann jeder Mechanismus für die Datensynchronisierung, der auf eine SQL Server-Instanz angewendet werden kann, auch zum Synchronisieren von Daten einer SQL Edge-Instanz genutzt werden, die auf einem Edgegerät ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial wird ein Windows-Computer benötigt, der mit dem [Data Sync Agent für die Azure SQL-Datensynchronisierung](../azure-sql/database/sql-data-sync-agent-overview.md) konfiguriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

* Erstellen Sie eine Datenbank in Azure SQL-Datenbank. Informationen zum Erstellen einer Azure SQL-Datenbank über das Azure-Portal finden Sie unter [Erstellen einer Einzeldatenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Erstellen Sie die erforderlichen Tabellen und die weiteren benötigten Objekte in Ihrer Azure SQL-Datenbank-Bereitstellung.

* Erstellen Sie die erforderlichen Tabellen und Objekte in Ihrer Azure SQL Edge-Bereitstellung. Weitere Informationen finden Sie unter [SQL-Datenbank-DAC-Pakete in SQL Edge](deploy-dacpac.md).

* Registrieren Sie die Azure SQL Edge-Instanz mit Data Sync Agent für die Azure SQL-Datensynchronisierung. Weitere Informationen finden Sie unter [Hinzufügen einer SQL Server-Datenbank](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Synchronisieren von Daten zwischen einer Azure SQL-Datenbank und SQL Edge

Das Einrichten der Synchronisierung zwischen einer Datenbank in Azure SQL-Datenbank und einer SQL Edge-Instanz mithilfe der SQL-Datensynchronisierung umfasst drei Hauptschritte:  


1. Verwenden Sie das Azure-Portal, um eine Synchronisierungsgruppe zu erstellen. Weitere Informationen finden Sie unter [Erstellen einer Synchronisierungsgruppe](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Sie können eine einzelne *Hub* -Datenbank verwenden, um mehrere Synchronisierungsgruppen zum Synchronisieren von Daten aus verschiedenen SQL Edge-Instanzen mit einer oder mehreren Datenbanken in Azure SQL-Datenbank zu erstellen. 

2. Fügen Sie der Synchronisierungsgruppe Synchronisierungsmitglieder hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Synchronisierungsmitgliedern](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Richten Sie die Synchronisierungsgruppe ein, um die Tabellen auszuwählen, die in die Synchronisierung eingeschlossen werden. Weitere Informationen finden Sie unter [Konfigurieren einer Synchronisierungsgruppe](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Nachdem Sie die vorstehenden Schritte ausgeführt haben, verfügen Sie über eine Synchronisierungsgruppe, die eine Datenbank in Azure SQL-Datenbank und eine SQL Edge-Instanz umfasst.

Weitere Informationen zur SQL-Datensynchronisierung finden Sie in den folgenden Artikeln:

* [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Bewährte Methoden für die SQL-Datensynchronisierung](../azure-sql/database/sql-data-sync-best-practices.md) und [Behandeln von Problemen mit der SQL-Datensynchronisierung](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](../azure-sql/database/monitor-tune-overview.md)

* [Aktualisieren des Synchronisierungsschemas mit Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) oder mit [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Nächste Schritte


* [Verwenden von PowerShell zum Synchronisieren zwischen Azure SQL-Datenbank und Azure SQL Edge](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md) Ersetzen Sie in diesem Tutorial die Details zur Datenbank `OnPremiseServer` durch die Details für Azure SQL Edge.