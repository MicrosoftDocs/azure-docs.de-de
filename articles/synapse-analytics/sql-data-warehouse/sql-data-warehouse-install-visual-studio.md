---
title: Installieren von Visual Studio 2019
description: Installieren von Visual Studio und SQL Server Data Tools (SSDT) für Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 456d7eb0cabd7ebede6415e3ce92d7403ecef7ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368753"
---
# <a name="getting-started-with-visual-studio-2019"></a>Erste Schritte mit Visual Studio 2019

SQL Server Data Tools (SSDT) von Visual Studio **2019** ist ein einzelnes Tool, mit dem Sie folgende Aufgaben ausführen können:

- Verbinden, Abfragen und Entwickeln von Anwendungen
- Nutzen eines Objekt-Explorers, um alle Objekte im Datenmodell visuell zu untersuchen, einschließlich Tabellen, Ansichten, gespeicherten Prozeduren usw.
- Generieren von T-SQL-DDL-Skripts (Data Definition Language) für die Objekte
- Entwickeln Ihres Data Warehouse mithilfe eines zustandsbasierten Ansatzes für SSDT-Datenbankprojekte
- Integrieren Ihres Datenbankprojekts in Quellcodeverwaltungssysteme wie Git mit Azure Repos
- Einrichten von Pipelines für Continuous Integration und Continuous Deployment mit Automatisierungsservern wie Azure DevOps

## <a name="install-visual-studio-2019"></a>Installieren von Visual Studio 2019

Informationen zum Herunterladen und Installieren von Visual Studio **16.3 und höher** finden Sie unter [Visual Studio 2019 herunterladen](https://visualstudio.microsoft.com/downloads/). Wählen Sie während der Installation die Workload „Datenspeicherung und -verarbeitung“ aus. Eine eigenständige SSDT-Installation ist in Visual Studio 2019 nicht mehr erforderlich.

## <a name="unsupported-features-in-ssdt"></a>Nicht unterstützte Funktionen in SSDT

Gelegentlich bieten für Synapse SQL veröffentliche Featurereleases keine Unterstützung für SSDT. Die folgenden Funktionen werden derzeit nicht unterstützt:


- [Workloadverwaltung](sql-data-warehouse-workload-management.md) – Workloadgruppen und -klassifizierungen
- [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (einschließlich Tabellenwertfunktionen)
  - [Übermitteln Sie ein Supportticket, oder geben Sie Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security), damit die Funktion unterstützt wird.
  - [Übermitteln Sie ein Supportticket, oder geben Sie Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking), damit die Funktion unterstützt wird.
- Tabellen mit einer [Identitätsspalte](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Bestimmte T-SQL-Features wie:
   - *WITHIN GROUP*-Klausel in der [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql)-Zeichenfolgenfunktion.

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt die neueste Version von SSDT verwenden, sind Sie bereit für die [Verbindungsherstellung](sql-data-warehouse-query-visual-studio.md) mit Ihrem SQL-Pool.
