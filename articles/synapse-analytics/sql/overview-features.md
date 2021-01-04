---
title: T-SQL-Funktionsunterschiede in Synapse SQL
description: Liste der Transact-SQL-Funktionen, die in Synapse SQL verfügbar sind.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 6bb54cf6a5545a49b3c84df59a9ee1294b788846
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462682"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>In Azure Synapse SQL unterstützte Transact-SQL-Funktionen

Azure Synapse SQL ist ein Big Data-Analysedienst, mit dem Sie Ihre Daten per T-SQL-Sprache abfragen und analysieren können. Sie können einen ANSI-kompatiblen Standarddialekt der SQL-Sprache verwenden, die in SQL Server und Azure SQL-Datenbank für die Datenanalyse verwendet wird. 

Die Transact-SQL-Sprache wird im serverlosen SQL-Pool verwendet. Das dedizierte Modell kann auf verschiedene Objekte verweisen, und bei den unterstützten Funktionen gibt es gewisse Unterschiede. Auf dieser Seite finden Sie allgemeine Transact-SQL-Sprachunterschiede zwischen den Verbrauchsmodellen von Synapse SQL.

## <a name="database-objects"></a>Datenbankobjekte

Verbrauchsmodelle in Synapse SQL ermöglichen die Verwendung verschiedener Datenbankobjekte. Die folgende Tabelle gibt Aufschluss über die unterstützten Objekttypen:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Tabellen** | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nein. Im Rahmen des serverlosen Modells können nur externe Daten in [Azure Storage](#storage-options) abgefragt werden. |
| **Ansichten** | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Von Sichten können [Abfragesprachelemente](#query-language) verwendet werden, die im dedizierten Modell verfügbar sind. | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Von Sichten können [Abfragesprachelemente](#query-language) verwendet werden, die im serverlosen Modell verfügbar sind. |
| **Schemas** | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Temporäre Tabellen** | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nein |
| **Vorgehensweisen** | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ja |
| **Funktionen** | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Ja, nur Inline-Tabellenwertfunktionen |
| **Trigger** | Nein | Nein |
| **Externe Tabellen** | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Weitere Informationen finden Sie in den unterstützten [Datenformaten](#data-formats). | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Weitere Informationen finden Sie in den unterstützten [Datenformaten](#data-formats). |
| **Zwischenspeichern von Abfragen** | Ja, mehrere Varianten (SSD-basierte Zwischenspeicherung, In-Memory, Resultset-Zwischenspeicherung). Außerdem wird die materialisierte Sicht unterstützt. | Nein |
| **Tabellenvariablen** | [Nein](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Verwenden Sie temporäre Tabellen. | Nein |
| **[Tabellenverteilung](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Ja | Nein |
| **[Tabellenindizes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Ja | Nein |
| **[Tabellenpartitionen](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Ja | Nein |
| **[Statistik](develop-tables-statistics.md)**            | Ja | Ja |
| **[Workloadverwaltung, Ressourcenklassen und Gleichzeitigkeitssteuerung](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ja    | Nein |
| **Kostenkontrolle** | Ja, mithilfe von Aktionen zum Hochskalieren und Herunterskalieren | Ja, mithilfe des [Azure-Portals oder der T-SQL-Prozedur](https://docs.microsoft.com/azure/synapse-analytics/sql/data-processed#cost-control) |

## <a name="query-language"></a>Abfragesprache

Die unterstützten Funktionen der in Synapse SQL verwendeten Abfragesprachen können sich abhängig vom Verbrauchsmodell unterscheiden. In der folgenden Tabelle sind die wichtigsten Unterschiede bei der Abfragesprache in Transact-SQL-Dialekten aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **SELECT-Anweisung** | Ja. Die Transact-SQL-Abfrageklauseln [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) werden nicht unterstützt. | Ja. Die Transact-SQL-Abfrageklauseln [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und Abfragehinweise werden nicht unterstützt. |
| **INSERT-Anweisung** | Ja | Nein |
| **UPDATE-Anweisung** | Ja | Nein |
| **DELETE-Anweisung** | Ja | Nein |
| **MERGE-Anweisung** | Nein  | Nein |
| **[Transaktionen](develop-transactions.md)** | Ja | Ja, gültig für Metadatenobjekte |
| **[Bezeichnungen](develop-label.md)** | Ja | Nein |
| **Ladevorgänge für Daten** | Ja. Das bevorzugte Hilfsprogramm ist zwar die [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-Anweisung, vom System werden jedoch auch Massenladen (BCP) sowie [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) zum Laden von Daten unterstützt. | Nein |
| **Datenexport** | Ja. Unter Verwendung von [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Unter Verwendung von [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Typen** | Ja. Alle Transact-SQL-Typen außer [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text und image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [räumliche Typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Alle Transact-SQL-Typen außer [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text und image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [räumliche Typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und Tabellentyp. |
| **Datenbankübergreifende Abfragen** | Nein | Ja, einschließlich [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-Anweisung. |
| **Integrierte Funktionen (Analyse)** | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Analyse](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), Konvertierung, [Datum und Uhrzeit](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), Logik und [Mathematik](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Analyse](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), Konvertierung, [Datum und Uhrzeit](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), Logik und [Mathematik](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Integrierte Funktionen (Text)** | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Zeichenfolgen](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und Sortierung mit Ausnahme von [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Alle Transact-SQL-Funktionen im Zusammenhang mit [Zeichenfolgen](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und Sortierung. |
| **Integrierte Tabellenwertfunktionen** | Ja. [Transact-SQL-Rowsetfunktionen](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions) mit Ausnahme von [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. [Transact-SQL-Rowsetfunktionen](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions) mit Ausnahme von [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  |
| **Aggregate** |  Integrierte Transact-SQL-Aggregate mit Ausnahme von [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Integrierte Transact-SQL-Aggregate |
| **Operatoren** | Ja. Alle [Transact-SQL-Operatoren](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Alle [Transact-SQL-Operatoren](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  |
| **Ablaufsteuerung** | Ja. Alle [Transact-SQL-Ablaufsteuerungsanweisungen](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) mit Ausnahme von [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja. Alle [Transact-SQL-Ablaufsteuerungsanweisungen](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) und SELECT-Abfrage in der Bedingung `WHILE (...)` |
| **DDL-Anweisungen (CREATE, ALTER, DROP)** | Ja. Alle auf die unterstützten Objekttypen anwendbaren Transact-SQL-DDL-Anweisungen. | Ja. Alle auf die unterstützten Objekttypen anwendbaren Transact-SQL-DDL-Anweisungen. |

## <a name="security"></a>Sicherheit

Synapse SQL ermöglicht die Verwendung integrierter Sicherheitsfeatures, um Ihre Daten zu schützen und den Zugriff zu steuern. In der folgenden Tabelle werden allgemeine Unterschiede zwischen den Synapse-SQL-Verbrauchsmodellen gegenübergestellt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Anmeldungen** | Nicht zutreffend (In Datenbanken werden nur enthaltene Benutzer unterstützt.) | Ja |
| **Benutzer** |  Nicht zutreffend (In Datenbanken werden nur enthaltene Benutzer unterstützt.) | Ja |
| **[Eigenständige Benutzer](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Ja. **Hinweis:** Es kann immer nur ein einzelner Azure AD-Benutzer uneingeschränkter Administrator sein. | Nein  |
| **Authentifizierung mit SQL-Benutzername/Kennwort**| Ja | Ja |
| **Azure Active Directory-Authentifizierung (Azure AD)**| Ja, Azure AD-Benutzer | Ja, Azure AD-Anmeldungen und -Benutzer |
| **Passthrough-Authentifizierung für Azure Active Directory-Speicher (Azure AD)** | Ja | Ja |
| **SAS-Tokenauthentifizierung für den Speicher** | Nein | Ja, unter Verwendung von [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oder [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (auf Instanzebene). |
| **Authentifizierung per Speicherzugriffsschlüssel** | Ja, unter Verwendung von [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Nein |
| **Authentifizierung für den Speicher mittels [verwalteter Identität](../security/synapse-workspace-managed-identity.md)** | Ja, unter Verwendung von [Anmeldeinformationen für eine verwaltete Dienstidentität](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Ja, unter Verwendung von Anmeldeinformationen vom Typ `Managed Identity`. |
| **Authentifizierung für den Speicher mittels Anwendungsidentität** | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nein |
| **Berechtigungen – Objektebene** | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer. | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für die unterstützten Systemobjekte. |
| **Berechtigungen – Schemaebene** | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für das Schema. | Ja, einschließlich der Möglichkeit zum Gewähren (GRANT), Verweigern (DENY) und Widerrufen (REVOKE) von Berechtigungen für Benutzer/Anmeldungen für das Schema. |
| **Berechtigungen – [Datenbankebene](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Ja | Ja |
| **Berechtigungen – [Serverebene](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nein | Ja. Systemadministrator und andere Serverrollen werden unterstützt. |
| **Berechtigungen – [Sicherheit auf Spaltenebene](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ja | Ja |
| **Rollen/Gruppen** | Ja, auf Datenbankebene. | Ja, auf Server- und Datenbankebene. |
| **Sicherheit&amp; Identitätsfunktionen** | Einige Transact-SQL-Sicherheitsfunktionen und -operatoren: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Einige Transact-SQL-Sicherheitsfunktionen und -operatoren: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SESSION_CONTEXT`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` und `REVERT`. Sicherheitsfunktionen können nicht zum Abfragen externer Daten verwendet werden. (Speichern Sie das Ergebnis in einer Variablen, die in der Abfrage verwendet werden kann.)  |
| **DATABASE SCOPED CREDENTIAL** | Ja | Ja |
| **SERVER SCOPED CREDENTIAL** | Nein | Ja |
| **Sicherheit auf Zeilenebene** | [Ja](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Nein |
| **Transparente Datenverschlüsselung (TDE)** | [Ja](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Nein | 
| **Datenermittlung und -klassifizierung** | [Ja](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nein |
| **Sicherheitsrisikobewertung** | [Ja](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nein |
| **Advanced Threat Protection für Azure SQL-Datenbank** | [Ja](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Überwachung** | [Ja](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nein |
| **[Firewallregeln](../security/synapse-workspace-ip-firewall.md)**| Ja | Ja |
| **[Privater Endpunkt](../security/synapse-workspace-managed-private-endpoints.md)**| Ja | Ja |

Ein dedizierter SQL-Pool und ein serverloser SQL-Pool verwenden die Transact-SQL-Standardsprache, um Daten abzufragen. Ausführliche Informationen zu den Unterschieden finden Sie in der [Transact-SQL-Sprachreferenz](/sql/t-sql/language-reference).

## <a name="tools"></a>Tools

Sie können verschiedene Tools verwenden, um eine Verbindung mit Synapse SQL herzustellen und Daten abzufragen.

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Synapse Studio** | Ja, SQL-Skripts. | Ja, SQL-Skripts. |
| **Power BI** | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Ja | Ja |
| **Azure Data Studio** | Ja | Ja, ab Version 1.18.0. SQL-Skripts und -Notebooks werden unterstützt. |
| **SQL Server Management Studio** | Ja | Ja, ab Version 18.5 |

> [!NOTE]
> Sie können mithilfe von SSMS eine Verbindung mit einem serverlosen SQL-Pool herstellen und Abfragen durchführen. Es wird ab Version 18.5 teilweise unterstützt, kann aber nur zum Herstellen einer Verbindung und für Abfragen verwendet werden.

Die meisten Anwendungen verwenden die Transact-SQL-Standardsprache und können sowohl dedizierte als auch serverlose Verbrauchsmodelle von Synapse SQL abfragen.

## <a name="storage-options"></a>Speicheroptionen

Die zu analysierenden Daten können in verschiedenen Arten von Speicher gespeichert sein. In der folgenden Tabelle sind alle verfügbaren Speicheroptionen aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Interner Speicher** | Ja | Nein |
| **Azure Data Lake v2** | Ja | Ja |
| **Azure Blob Storage** | Ja | Ja |
| **Azure SQL (Remote)** | Nein  | Nein  |
| **Transaktionaler Azure CosmosDB-Speicher** | Nein  | Nein  |
| **Analytischer Speicher für Azure CosmosDB** | Nein | Ja, mithilfe von [Synapse Link (Vorschau)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ([Public Preview](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |
| **Apache Spark-Tabellen (im Arbeitsbereich)** | Nein  | PARQUET-Tabellen, die nur [Metadatensynchronisierung](develop-storage-files-spark-tables.md) verwenden |
| **Apache Spark-Tabellen (Remote)** | Nein  | Nein  |
| **Databricks-Tabellen (Remote)** | Nein | Nein |

## <a name="data-formats"></a>Datenformate

Zu analysierende Daten können in verschiedenen Speicherformaten gespeichert sein. In der folgenden Tabelle sind alle für die Analyse verfügbaren Datenformate aufgeführt:

|   | Dediziert | Serverlos |
| --- | --- | --- |
| **Mit Trennzeichen** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ja](query-single-csv-file.md) |
| **CSV** | Ja. (Trennzeichen mit mehreren Zeichen werden nicht unterstützt.) | [Ja](query-single-csv-file.md) |
| **Parquet** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Ja](query-parquet-files.md), einschließlich Dateien mit [geschachtelten Typen](query-parquet-nested-types.md). |
| **Hive ORC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nein |
| **Hive RC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nein |
| **JSON** | Ja | [Ja](query-json-files.md) |
| **Avro** | Nein | Nein |
| **[Delta-Lake](https://delta.io/)** | Nein | Nein |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Nein | Nein |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu bewährten Methoden für dedizierte SQL-Pools und serverlose SQL-Pools finden Sie in den folgenden Artikeln:

- [Best Practices für dedizierte SQL-Pools](best-practices-sql-pool.md)
- [Best Practices für serverlose SQL-Pools](best-practices-sql-on-demand.md)
