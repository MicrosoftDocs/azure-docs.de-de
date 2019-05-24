---
title: Visualisieren von SQL Data Warehouse-Daten mit Power BI | Microsoft Azure
description: Visualisieren von SQL Data Warehouse-Daten mit Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 94ec38bc2cad3566fad88dc2ac56648f79aa16b2
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873376"
---
# <a name="visualize-data-with-power-bi"></a>Visualisieren von Daten mit Power BI
In diesem Lernprogramm erfahren Sie, wie Sie Power BI zum Herstellen einer Verbindung mit SQL Data Warehouse verwenden und einige einfache Visualisierungen erstellen.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Lernprogramm ist Folgendes erforderlich:

* Eine SQL Data Warehouse-Instanz, für die die AdventureWorksDW-Datenbank vorab geladen wurde. Informationen zur Bereitstellung einer Data Warehouse-Instanz finden Sie unter [Erstellen eines SQL Data Warehouse](create-data-warehouse-portal.md). Wählen Sie die Option zum Laden der Beispieldaten. Wenn Sie bereits über eine Data Warehouse-Instanz verfügen, aber noch keine Beispieldaten besitzen, können Sie [WideWorldImportersDW laden](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Verbinden mit der Datenbank
Gehen Sie wie folgt vor, um Power BI zu öffnen und eine Verbindung mit der AdventureWorksDW-Datenbank herzustellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie auf **SQL-Datenbanken** , und wählen Sie Ihre AdventureWorks SQL Data Warehouse-Datenbank aus.
   
    ![Datenbank suchen](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klicken Sie auf die Schaltfläche „Open in Power BI“.
   
    ![Power BI-Schaltfläche](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Nun sollte die Seite mit der SQL Data Warehouse-Verbindung und der Webadresse Ihrer Datenbank angezeigt werden. Klicken Sie auf "Weiter".
   
    ![Power BI-Verbindung](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Geben Sie Ihren Azure SQL Server-Benutzernamen und Ihr Kennwort ein.
   
    ![Power BI-Anmeldung](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Um die Datenbank zu öffnen, klicken Sie auf dem linken Blatt auf das Dataset AdventureWorksDW.
   
    ![Power BI – Öffnen von „AdventureWorksDW“](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Erstellen eines Berichts
Sie können Power BI jetzt zum Analysieren Ihrer AdventureWorksDW-Beispieldaten verwenden. Zum Durchführen der Analyse verfügt AdventureWorksDW über die Ansicht „AggregateSales“. Diese Ansicht enthält einige wichtige Metriken zum Analysieren des Umsatzes des Unternehmens.

1. Klicken Sie zum Erstellen einer Karte mit den Umsatzbeträgen nach Postleitzahl im rechten Bereich mit den Feldern auf die Ansicht „AggregateSales“, um sie zu erweitern. Klicken Sie dann auf die Spalten „PostalCode“ und „SalesAmount“, um sie auszuwählen.
   
    ![Power BI – Auswählen von „AggregateSales“](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI erkennt automatisch geografische Daten und fügt sie für Sie in eine Karte ein.
   
    ![Power BI-Karte](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Durch diesen Schritt wird ein Balkendiagramm erstellt, in der Umsatz pro Kundeneinkommen angezeigt wird. Wechseln Sie zur Erstellung des Balkendiagramms zur erweiterten Ansicht AggregateSales. Klicken Sie auf das Feld „SalesAmount“. Ziehen Sie das Feld „Customer Income“ auf die linke Seite, und legen Sie es auf „Axis“ ab.
   
    ![Power BI – Auswählen von „Axis“](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Das durch Ziehen nach links erstellte Balkendiagramm.
   
    ![Power BI-Balkendiagramm](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Durch diesen Schritt wird ein Liniendiagramm erstellt, das denn Umsatz pro Bestelldatum anzeigt. Wechseln Sie zur Erstellung des Liniendiagramms zur erweiterten Ansicht AggregateSales. Klicken Sie auf „SalesAmount“ und „OrderDate“. Klicken Sie in der Spalte mit den Visualisierungen auf das Symbol für Liniendiagramme. Dies ist das erste Symbol in der zweiten Zeile unter „Visualisierungen“.
   
    ![Power BI – Auswählen des Liniendiagramms](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Sie haben jetzt einen Bericht mit drei verschiedenen Visualisierungen der Daten.
   
    ![Power BI-Liniendiagramm](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Sie können Ihren Fortschritt jederzeit speichern, indem Sie auf **Datei** und auf **Speichern** klicken.

## <a name="using-direct-connect"></a>Verwendung von Direct Connect
Wie bei Azure SQL-Datenbank ermöglicht auch SQL Data Warehouse Direct Connect logische Pushdowns in Verbindung mit den Analysefunktionen von Power BI. Abfragen werden mit Direct Connect in Echtzeit zurück an Ihre Azure SQL Data Warehouse-Instanz gesendet, während Sie die Daten durchsuchen.  Dieses Feature ermöglicht Ihnen in Kombination mit der Skalierung von SQL Data Warehouse, in Minuten dynamische Berichte für Terabytes an Daten zu erstellen. Darüber hinaus ermöglicht die Einführung der Schaltfläche "Open in Power BI" Benutzern, direkt eine Verbindung zwischen Power BI und SQL Data Warehouse herzustellen, ohne dass Informationen aus anderen Teilen von Azure gesammelt werden müssen.

Beachten Sie bei der Verwendung von Direct Connect Folgendes:

* Geben Sie beim Herstellen einer Verbindung den vollqualifizierten Servernamen an.
* Stellen Sie sicher, dass die Firewallregeln für die Datenbank mit „Zugriff auf Azure-Dienste erlauben“ konfiguriert sind.
* Für jede Aktion, wie z.B. das Auswählen einer Spalte oder das Hinzufügen eines Filters, wird das Data Warehouse direkt abgefragt.
* Kacheln werden automatisch und etwa alle 15 Minuten aktualisiert.
* Fragen und Antworten sind nicht für Direct Connect-Datasets verfügbar.
* Schemaänderungen werden automatisch integriert.
* Für alle Direct Connect-Abfragen gilt ein Timeout von 2 Minuten.

Diese Einschränkungen und Hinweise können sich im Zuge fortlaufender Verbesserung der Umgebung verbessern.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Gelegenheit hatten, sich ein wenig mit den Beispieldaten vertraut zu machen, können Sie sich nun mit dem [Entwickeln](sql-data-warehouse-overview-develop.md), [Laden](design-elt-data-loading.md) oder [Migrieren](sql-data-warehouse-overview-migrate.md) befassen. Alternativ können Sie sich auch die [Power BI-Website](https://www.powerbi.com/) ansehen.
