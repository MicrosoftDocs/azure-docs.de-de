---
title: Inhaltsreferenz zu Schnellstartanleitungen für Einzeldatenbanken
description: Hier finden Sie eine Inhaltsreferenz zu allen Schnellstartanleitungen, die Ihnen einen schnellen Einstieg in die Verwendung von Einzeldatenbanken in Azure SQL-Datenbank ermöglichen.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184415"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Erste Schritte mit Einzeldatenbanken in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Eine [Einzeldatenbank](../index.yml) ist eine vollständig verwaltete PaaS-Datenbank als Dienst (Database-as-a-Service, DbaaS) und eine ideale Speicher-Engine für moderne cloudbasierte Anwendungen. In diesem Abschnitt erfahren Sie, wie Sie schnell eine Einzeldatenbank in Azure SQL-Datenbank konfigurieren und erstellen.

## <a name="quickstart-overview"></a>Schnellstartübersicht

Dieser Abschnitt enthält eine Übersicht über die verfügbaren Artikel für den schnellen Einstieg in die Verwendung von Einzeldatenbanken. Die folgenden Schnellstartanleitungen unterstützen Sie beim schnellen Erstellen einer Einzeldatenbank, beim Konfigurieren einer Firewallregel auf Serverebene und beim anschließenden Importieren einer Datenbank in die neue Einzeldatenbank mithilfe einer Datei vom Typ `.bacpac`:

- [Quickstart: Create a single database in Azure SQL Database using the Azure portal](single-database-create-quickstart.md) (Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal)
- Nach der Erstellung der Datenbank müssen Sie [Ihre Datenbank durch Konfigurieren von Firewallregeln schützen](firewall-create-server-level-portal-quickstart.md).
- Falls Sie über eine vorhandene Datenbank in SQL Server verfügen, die Sie zu Azure SQL-Datenbank migrieren möchten, sollten Sie den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installieren. Dieser analysiert Ihre Datenbanken in SQL Server und ermittelt sämtliche Probleme, die die Migration verhindern könnten. Wurden keine Probleme gefunden, können Sie Ihre Datenbank als Datei vom Typ `.bacpac` exportieren und [über das Azure-Portal oder mithilfe von „SqlPackage“ importieren](database-import.md).


## <a name="automating-management-operations"></a>Automatisieren von Verwaltungsvorgängen

Sie können PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Datenbank zu erstellen, zu konfigurieren und zu skalieren.

- [Erstellen und Konfigurieren einer Einzeldatenbank mithilfe von PowerShell](scripts/create-and-configure-database-powershell.md) oder der [Azure CLI](scripts/create-and-configure-database-cli.md)
- [Aktualisieren Ihrer Einzeldatenbank und Skalieren von Ressourcen mithilfe von PowerShell](scripts/monitor-and-scale-database-powershell.md) oder der [Azure CLI](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrieren zu einer Einzeldatenbank mit minimaler Downtime

In diesen Schnellstartanleitungen erfahren Sie, wie Sie Ihre Datenbank schnell erstellen oder unter Verwendung einer Datei vom Typ `.bacpac` in Azure importieren. Dateien vom Typ `.bacpac` und `.dacpac` dienen jedoch dazu, Datenbanken schnell zwischen verschiedenen Versionen von SQL Server und innerhalb von Azure SQL zu migrieren oder Continuous Integration in Ihrer DevOps-Pipeline zu implementieren. Diese Methode ist jedoch nicht für die Migration Ihrer Produktionsdatenbanken mit minimaler Downtime geeignet, da Sie vorerst keine neuen Daten hinzufügen dürfen und warten müssen, bis der Export der Quelldatenbank in eine Datei vom Typ `.bacpac` und der anschließende Import in Azure SQL-Datenbank abgeschlossen ist. Diese Wartezeit führt zu Ausfallzeiten für Ihre Anwendung, insbesondere bei großen Datenbanken. Für die Verschiebung Ihrer Produktionsdatenbank benötigen Sie eine bessere Migrationsmethode mit möglichst geringer Downtime bei der Migration. Verwenden Sie dafür [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json), um Ihre Datenbank mit minimaler Downtime zu migrieren. DMS pusht die in Ihrer Quelldatenbank vorgenommenen Änderungen inkrementell in die Einzeldatenbank, die wiederhergestellt wird. Dadurch können Sie Ihre Anwendung schnell und mit minimaler Downtime von der Quell- auf die Zieldatenbank umstellen.

## <a name="hands-on-learning-modules"></a>Praxisorientierte Lernmodule

In den folgenden Microsoft Learn-Modulen können Sie sich kostenlos über Azure SQL-Datenbank informieren.

- [Bereitstellen einer Datenbank in SQL-Datenbank zum Speichern von Anwendungsdaten](/learn/modules/provision-azure-sql-db/)
- [Entwickeln und Konfigurieren einer ASP.NET-Anwendung zum Abfragen einer Azure SQL-Datenbank](/learn/modules/develop-app-that-queries-azure-sql/)
- [Schützen Ihrer Datenbank in Azure SQL-Datenbank](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit einer [allgemeinen Liste unterstützter Features in Azure SQL-Datenbank](features-comparison.md) vertraut.
- Informieren Sie sich darüber, wie Sie [Ihre Datenbank sicherer machen](secure-database-tutorial.md).
- Weiterführende Schrittanleitungen finden Sie unter [Verwenden eines Singleton in Azure SQL-Datenbank](how-to-content-reference-guide.md).
- Sehen Sie sich weitere Beispielskripts für [PowerShell](powershell-script-content-guide.md) und die [Azure-Befehlszeilenschnittstelle](az-cli-script-samples-content-guide.md) an.
- Informieren Sie sich ausführlicher über die [Verwaltungs-API](single-database-manage.md), die Sie zum Konfigurieren Ihrer Datenbanken verwenden können.
- [Ermitteln Sie die richtige Azure SQL-Datenbank- oder Azure SQL Managed Instance-SKU für Ihre lokale Datenbank](/sql/dma/dma-sku-recommend-sql-db/).