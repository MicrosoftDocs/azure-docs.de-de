---
title: 'Verschieben von Daten in eine Azure SQL-Datenbank: Team Data Science-Prozess'
description: Verschieben von Daten aus Flatfiles (CSV- oder TSV-Formate) oder von in einer lokalen SQL Server-Instanz gespeicherten Daten in eine Azure SQL-Datenbank.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/04/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d1634552522a3d1056f9af29386b6ae32754cae0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61429296"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning

In diesem Artikel werden die Optionen zum Verschieben von Daten aus Flatfiles (CSV- oder TSV-Format) oder von in einer lokalen SQL Server-Instanz gespeicherten Daten in eine Azure SQL-Datenbank beschrieben. Diese Tasks zum Verschieben von Daten in die Cloud sind Teil des Team Data Science-Prozesses.

Ein Thema, in dem die Optionen für das Verschieben von Daten in eine lokale SQL Server-Instanz für Machine Learning beschrieben werden, finden Sie unter [Verschieben von Daten in SQL Server auf einem virtuellen Computer in Azure](move-sql-server-virtual-machine.md).

In der folgende Tabelle sind die Optionen zum Verschieben von Daten in eine Azure SQL-Datenbank zusammengefasst.

| <b>QUELLE</b> | <b>ZIEL: Azure SQL-Datenbank</b> |
| --- | --- |
| <b>Flatfile (CSV- oder TSV-Format)</b> |[SQL-Abfrage zum Masseneinfügen](#bulk-insert-sql-query) |
| <b>Lokale SQL Server-Instanz</b> |1. [Exportieren in eine Flatfile](#export-flat-file)<br> 2. [SQL-Datenbankmigrations-Assistent](#insert-tables-bcp)<br> 3. [Datenbanksicherung und -wiederherstellung](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Voraussetzungen
Für diese hier beschriebenen Verfahren benötigen Sie:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* Ein **Azure-Speicherkonto**. Sie nutzen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-quickstart-create-account.md) . Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Weitere Informationen finden Sie unter [Verwalten von Speicherzugriffsschlüsseln](../../storage/common/storage-account-manage.md#access-keys).
* Zugriff auf eine **Azure SQL-Datenbank**. Wenn Sie eine Azure SQL-Datenbank einrichten müssen, finden Sie in [Erste Schritte mit Microsoft Azure SQL-Datenbank](../../sql-database/sql-database-get-started.md) Informationen dazu, wie Sie eine neue Instanz einer Azure SQL-Datenbank bereitstellen.
* Lokal installierte und konfigurierte **Azure PowerShell** . Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

**Daten**: Die Migrationsprozesse werden anhand des [NYC Taxi-Datasets](https://chriswhong.com/open-data/foil_nyc_taxi/)demonstriert. Das NYC Taxi-Dataset enthält Informationen zu Daten und Preisen von Fahrten und ist in Azure Blob Storage verfügbar: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Ein Beispiel und eine Beschreibung dieser Dateien finden Sie unter [Beschreibung des NYC Taxi Trips-Datasets](sql-walkthrough.md#dataset).

Sie können die hier beschriebenen Verfahren entweder auf einen Satz Ihrer eigenen Daten anpassen oder die Schritte wie beschrieben unter Verwendung des NYC Taxi-Datasets durchführen. Um das NYC Taxi-Dataset in Ihre lokale SQL Server-Datenbank hochzuladen, befolgen Sie das unter [Massenimport von Daten in eine SQL Server-Datenbank](sql-walkthrough.md#dbload) beschriebene Verfahren. Diese Anleitungen gelten für eine SQL Server-Instanz auf einem virtuellen Azure-Computer, aber das Verfahren zum Hochladen auf die lokale SQL Server-Instanz ist identisch.

## <a name="file-to-azure-sql-database"></a> Verschieben von Daten aus einer Flatfilequelle in eine Azure SQL-Datenbank
Daten in Flatfiles (CSV- oder TSV-Format) können mithilfe einer SQL-Abfrage zum Masseneinfügen in eine Azure SQL-Datenbank verschoben werden.

### <a name="bulk-insert-sql-query"></a> SQL-Abfrage zum Masseneinfügen
Die Schritte des Verfahrens unter Verwendung der SQL-Abfrage zum Masseneinfügen sind mit denen vergleichbar, die in den Abschnitten zum Verschieben von Daten aus einer Flatfilequelle in SQL Server auf einem virtuellen Azure-Computer behandelt werden. Ausführliche Informationen finden Sie unter [SQL-Abfrage zum Masseneinfügen](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Verschieben von Daten aus einer lokalen SQL Server-Instanz in eine Azure SQL-Datenbank
Wenn die Quelldaten auf einer lokalen SQL Server-Instanz gespeichert sind, stehen verschiedene Optionen zum Verschieben der Daten in eine Azure SQL-Datenbank zur Verfügung:

1. [Exportieren in eine Flatfile](#export-flat-file)
2. [SQL-Datenbankmigrations-Assistent](#insert-tables-bcp)
3. [Datenbanksicherung und -wiederherstellung](#db-migration)
4. [Azure Data Factory](#adf)

Die Schritte für die ersten drei Möglichkeiten sind den Abschnitten unter [Verschieben von Daten in eine SQL Server-Instanz auf einem virtuellen Computer in Azure](move-sql-server-virtual-machine.md) sehr ähnlich, in denen diese Verfahren behandelt werden. Links zu den entsprechenden Abschnitten in diesem Thema finden Sie in den folgenden Anleitungen.

### <a name="export-flat-file"></a>Exportieren in eine Flatfile
Die Schritte für diesen Export in eine Flatfile ähneln den unter [Exportieren in eine Flatfile](move-sql-server-virtual-machine.md#export-flat-file)behandelten.

### <a name="insert-tables-bcp"></a>SQL-Datenbankmigrations-Assistent
Die Schritte für die Verwendung des SQL-Datenbankmigrations-Assistenten ähneln den unter [SQL-Datenbankmigrations-Assistent](move-sql-server-virtual-machine.md#sql-migration)behandelten.

### <a name="db-migration"></a>Datenbanksicherung und -wiederherstellung
Die Schritte für die Verwendung der Datenbanksicherung und -wiederherstellung ähneln den unter [Datenbanksicherung und -wiederherstellung](move-sql-server-virtual-machine.md#sql-backup)behandelten.

### <a name="adf"></a>Azure Data Factory
Das Verfahren zum Verschieben von Daten in eine Azure SQL-Datenbank mit Azure Data Factory (ADF) finden Sie im Thema [Verschieben von Daten von einer lokalen SQL Server-Instanz zu SQL Azure mithilfe von Azure Data Factory](move-sql-azure-adf.md). In diesem Thema wird gezeigt, wie Sie mithilfe von ADF Daten aus einer lokalen SQL Server-Datenbank über Azure Blob Storage in eine Azure SQL-Datenbank verschieben.

Sie sollten die Verwendung von ADF in Betracht ziehen, wenn Daten in einem Hybridszenario kontinuierlich migriert werden müssen, das sowohl auf lokale als auch Cloudressourcen zugreift, und wenn die Daten Transaktionen unterworfen werden oder geändert werden müssen, oder wenn ihnen bei der Migration Geschäftslogik hinzugefügt wird. ADF ermöglicht die Planung und Überwachung von Aufträgen mithilfe einfacher JSON-Skripts, die das Verschieben von Daten in regelmäßigen Abständen verwalten. ADF verfügt außerdem über weitere Funktionen wie Unterstützung für komplexe Vorgänge.
