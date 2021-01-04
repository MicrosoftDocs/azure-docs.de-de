---
title: Serverloser SQL-Pool
description: Hier erfahren Sie mehr über den serverlosen SQL-Pool in Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 34ce36f0ff348f896b7c2ea680c113b5e9e4ea09
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463164"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Serverloser SQL-Pool in Azure Synapse Analytics 

Jeder Azure Synapse Analytics-Arbeitsbereich enthält Endpunkte für serverlose SQL-Pools, die Sie zum Abfragen von Daten im Data Lake verwenden können.

Ein serverloser SQL-Pool ist ein Abfragedienst für die Daten in Ihrem Data Lake. Er ermöglicht den Zugriff auf Ihre Daten über folgende Funktionen:
 
- Eine vertraute T-SQL-Syntax zum direkten Abfragen von Daten, ohne dass diese in einen speziellen Speicher kopiert oder geladen werden müssen 
- Integrierte Konnektivität über die T-SQL-Schnittstelle, die ein breites Spektrum an Business Intelligence und Ad-hoc-Abfragetools bietet – einschließlich der beliebtesten Treiber 

Ein serverloser SQL-Pool ist ein verteiltes Datenverarbeitungssystem, das für umfangreiche Datenmengen und Berechnungsfunktionen konzipiert ist. Mit einem serverlosen SQL-Pool können Sie Big Data abhängig von der Workload in wenigen Sekunden oder Minuten analysieren. Dank der integrierten Fehlertoleranz bei der Abfrageausführung bietet das System auch bei Abfragen mit langer Ausführungszeit und umfangreichen Datasets eine hohe Zuverlässigkeit und Erfolgsquote.

Da es sich bei einem serverlosen SQL-Pool um eine serverlose Lösung handelt, müssen Sie weder eine Infrastruktur einrichten noch Cluster verwalten. In jedem Azure Synapse-Arbeitsbereich wird ein Standardendpunkt für diesen Dienst bereitgestellt, sodass Sie direkt nach der Erstellung des Arbeitsbereichs mit dem Abfragen von Daten beginnen können. 

Es fallen keine Gebühren für reservierte Ressourcen an. Ihnen werden lediglich die Daten in Rechnung gestellt, die im Rahmen Ihrer Abfragen verarbeitet werden. Somit handelt es sich hierbei um ein Modell mit echter nutzungsbasierter Bezahlung.  

Wenn Sie Apache Spark für Azure Synapse in Ihrer Datenpipeline verwenden, können Sie zur Datenvorbereitung, -bereinigung oder -anreicherung im Rahmen des Prozesses erstellte [externe Spark-Tabellen](develop-storage-files-spark-tables.md) direkt über einen serverlosen SQL-Pool abfragen. Verwenden Sie [Private Link](../security/how-to-connect-to-workspace-with-private-links.md), um Ihren Endpunkt für den serverlosen SQL-Pool in das [verwaltete VNET Ihres Arbeitsbereichs](../security/synapse-workspace-managed-vnet.md) zu integrieren.  

## <a name="serverless-sql-pool-benefits"></a>Vorteile eines serverlosen SQL-Pools

Wenn Sie Daten im Data Lake untersuchen, Erkenntnisse aus diesen Daten gewinnen oder Ihre vorhandene Datentransformationspipeline optimieren möchten, können Sie von einem serverlosen SQL-Pool profitieren. SQL On-Demand eignet sich für folgende Szenarien:

- Grundlegende Ermittlung und Untersuchung: Unterschiedliche Datenformate (Parquet, CSV, JSON) in Ihrem Data Lake können zur Planung einer geeigneten Vorgehensweise für die Gewinnung von Erkenntnissen schnell und einfach analysiert werden.
- Logisches Data Warehouse: Eine relationale Abstraktion auf der Grundlage unformatierter oder unterschiedlicher Daten ohne Datenverschiebung oder -transformation ermöglicht einen stets aktuellen Blick auf Ihre Daten.
- Datentransformation: Eine einfache, skalierbare und leistungsfähige Möglichkeit zum Transformieren von Daten im Data Lake mithilfe von T-SQL, um die Daten für BI und andere Tools bereitzustellen oder in einen relationalen Datenspeicher (beispielsweise Synapse SQL-Datenbanken oder Azure SQL-Datenbank) zu laden.

Verschiedene professionelle Rollen können von einem serverlosen SQL-Pool profitieren:

- Dateningenieure können den Data Lake erkunden, Daten mithilfe dieses Diensts transformieren und vorbereiten und ihre Datentransformationspipelines vereinfachen. Weitere Informationen finden Sie in [diesem Tutorial](tutorial-data-analyst.md).
- Dank Features wie OPENROWSET und automatischem Schemarückschluss können Data Scientists schnell den Inhalt und die Struktur der Daten im Data Lake analysieren.
- Datenanalysten können mithilfe der vertrauten T-SQL-Sprache oder mit den von ihnen bevorzugten Tools, die eine Verbindung mit einem serverlosen SQL-Pool herstellen können, [Daten und externe Spark-Tabellen erkunden](develop-storage-files-spark-tables.md), die von wissenschaftlichen und technischen Fachkräften für Daten erstellt wurden.
- BI-Experten können schnell [Power BI-Berichte auf der Grundlage von Daten im Data Lake](tutorial-connect-power-bi-desktop.md) sowie Spark-Tabellen erstellen.

## <a name="how-to-start-using-serverless-sql-pool"></a>Erste Schritte bei der Verwendung eines serverlosen SQL-Pools

In jedem Azure Synapse-Arbeitsbereich steht ein Endpunkt für einen serverlosen SQL-Pool zur Verfügung. Sie können einen Arbeitsbereich erstellen und sofort damit beginnen, Daten mithilfe Ihrer vertrauten Tools abzufragen.

## <a name="client-tools"></a>Clienttools

Ein serverloser SQL-Pool ermöglicht die Nutzung des Data Lake durch bereits vorhandene Tools für SQL-Ad-hoc-Abfragen und Business Intelligence. Dank der vertrauten T-SQL-Syntax kann jedes Tool, das für SQL-Angebote mit TDS-Verbindung geeignet ist, bei Bedarf [eine Verbindung mit Synapse SQL herstellen und entsprechende Abfragen ausführen](connect-overview.md). Sie können eine Verbindung mit Azure Data Studio herstellen und Ad-hoc-Abfragen ausführen oder eine Verbindung mit Power BI herstellen, um innerhalb weniger Minuten Erkenntnisse zu gewinnen.

## <a name="t-sql-support"></a>T-SQL-Unterstützung

Ein serverloser SQL-Pool bietet eine Oberfläche für T-SQL-Abfragen, die in einigen Bereichen geringfügig verbessert bzw. erweitert wurde, um Abfragen für teilweise strukturierte und unstrukturierte Daten zu ermöglichen. Darüber hinaus werden einige Aspekte der T-SQL-Sprache aufgrund des Designs von serverlosen SQL-Pools nicht unterstützt (beispielsweise die DML-Funktion).

- Die Workload kann mithilfe vertrauter Konzepte strukturiert werden:
- Datenbanken: Der Endpunkt für einen serverlosen SQL-Pool kann über mehrere Datenbanken verfügen.
- Schemas: Eine Datenbank kann einzelne oder mehrere Objektbesitzgruppen (Schemas) enthalten.
- Sichten
- Externe Ressourcen: Datenquellen, Dateiformate und Tabellen

Verfügbare Sicherheitsmaßnahmen:

- Anmeldungen und Benutzer
- Anmeldeinformationen zum Steuern des Zugriffs auf Speicherkonten
- Erteilung, Verweigerung und Widerruf von Berechtigungen auf der Objektebene
- Azure Active Directory-Integration

T-SQL-Unterstützung:

- Vollständige Unterstützung der Oberfläche [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), einschließlich der meisten SQL-Funktionen
- CETAS (CREATE EXTERNAL TABLE AS SELECT)
- Sicht- und sicherheitsbezogene DDL-Anweisungen

Ein serverloser SQL-Pool verfügt über keinen lokalen Speicher, und es werden nur Metadatenobjekte in Datenbanken gespeichert. Daher wird T-SQL im Zusammenhang mit folgenden Konzepten nicht unterstützt:

- Tabellen
- Trigger
- Materialisierte Sichten
- Sicht- und sicherheitsfremde DDL-Anweisungen
- DML-Anweisungen

### <a name="extensions"></a>Erweiterungen

Ein serverloser SQL-Pool erweitert die vorhandene Funktion [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) mit folgenden Funktionen, um reibungslose Abfragen für Daten zu ermöglichen, die sich in Dateien im Data Lake befinden:

[Abfragen mehrerer Dateien oder Ordner](query-data-storage.md#query-multiple-files-or-folders)

[Parquet-Dateiformat](query-data-storage.md#query-parquet-files)

[Zusätzliche Optionen für die Verwendung von durch Trennzeichen getrenntem Text (Feldabschlusszeichen, Zeilenabschlusszeichen, Escapezeichen)](query-data-storage.md#query-csv-files)

[Lesen einer ausgewählten Teilmenge von Spalten](query-data-storage.md#read-a-chosen-subset-of-columns)

[Schemarückschluss](query-data-storage.md#schema-inference)

[filename-Funktion](query-data-storage.md#filename-function)

[filepath-Funktion](query-data-storage.md#filepath-function)

[Verwenden von komplexen Typen und geschachtelten oder wiederholten Datenstrukturen](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Sicherheit

Ein serverloser SQL-Pool bietet Mechanismen, um den Zugriff auf Ihre Daten zu schützen.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-Integration und Multi-Factor Authentication

Ein serverloser SQL-Pool ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten über die [Azure Active Directory-Integration](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Diese Funktion vereinfacht die Berechtigungsverwaltung und erhöht die Sicherheit. Azure Active Directory (Azure AD) unterstützt die [mehrstufige Authentifizierung](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Multi-Factor Authentication, MFA), um die Daten- und Anwendungssicherheit zu erhöhen, sowie einmaliges Anmelden.

#### <a name="authentication"></a>Authentifizierung

Die Authentifizierung mit einem serverlosen SQL-Pool bezieht sich darauf, auf welche Weise Benutzer ihre Identität beim Herstellen der Verbindung mit dem Endpunkt nachweisen. Zwei Authentifizierungstypen werden unterstützt:

- **SQL-Authentifizierung**

  Diese Authentifizierungsmethode verwendet einen Benutzernamen und ein Kennwort.

- **Azure Active Directory-Authentifizierung**:

  Bei dieser Authentifizierungsmethode werden von Azure Active Directory verwaltete Identitäten verwendet. Für Azure AD-Benutzer kann die mehrstufige Authentifizierung aktiviert werden. Verwenden Sie immer die Active Directory-Authentifizierung (integrierte Sicherheit), [sofern dies möglich ist](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Authorization

Autorisierung bezieht sich darauf, welche Aufgaben ein Benutzer innerhalb einer Datenbank im serverlosen SQL-Pool ausführen kann. Dies wird durch datenbankbezogene Rollenmitgliedschaften und Objektebenenberechtigungen Ihres Benutzerkontos gesteuert.

Bei Verwendung der SQL-Authentifizierung ist der SQL-Benutzer nur in einem serverlosen SQL-Pool vorhanden, und Berechtigungen gelten für die Objekte im serverlosen SQL-Pool. Zugriff auf sicherungsfähige Objekte in anderen Diensten (etwa in Azure Storage) kann einem SQL-Benutzer nicht direkt gewährt werden, da er nur im serverlosen SQL-Pool vorhanden ist. Der SQL-Benutzer muss einen der [unterstützten Autorisierungstypen](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) verwenden, um auf die Dateien zugreifen zu können.

Bei Verwendung der Azure AD-Authentifizierung kann sich ein Benutzer beim serverlosen SQL-Pool und bei anderen Diensten (etwa Azure Storage) anmelden und dem Azure AD-Benutzer Berechtigungen erteilen.

### <a name="access-to-storage-accounts"></a>Zugriff auf Speicherkonten

Ein beim serverlosen SQL-Pool angemeldeter Benutzer benötigt eine Autorisierung, um auf die Dateien in Azure Storage zugreifen und Abfragen für die Dateien ausführen zu können. Von einem serverlosen SQL-Pool werden folgende Autorisierungstypen unterstützt:

- **Shared Access Signature (SAS)** für delegierten Zugriff auf Ressourcen im Speicherkonto. Mit einer SAS können Sie Clients Zugriff auf Ressourcen im Speicherkonto gewähren, ohne dafür Kontoschlüssel weitergeben zu müssen. Mit einer SAS können Sie präzise steuern, welche Art von Zugriff Sie Clients gewähren möchten, die über die SAS verfügen. Dazu stehen Parameter wie Gültigkeitsintervall, erteilte Berechtigungen, zulässiger IP-Adressbereich und zulässiges Protokoll (HTTPS/HTTP) zur Verfügung.

- **Benutzeridentität** (auch „Passthrough“ genannt) ist ein Autorisierungstyp, bei dem die Identität des beim serverlosen SQL-Pool angemeldeten Azure AD-Benutzers verwendet wird, um den Zugriff auf die Daten zu autorisieren. Vor dem Zugriff auf die Daten muss der Azure Storage-Administrator dem Azure AD-Benutzer Zugriffsberechtigungen für die Daten erteilen. Da bei diesem Autorisierungstyp der beim serverlosen SQL-Pool angemeldete Azure AD-Benutzer verwendet wird, wird er für SQL-Benutzertypen nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Endpunktverbindung und zum Abfragen von Dateien finden Sie in den folgenden Artikeln: 
- [Herstellen einer Verbindung mit Synapse SQL](connect-overview.md)
- [Abfragen von Speicherdateien mithilfe von SQL On-Demand-Ressourcen (Vorschauversion) in Synapse SQL](develop-storage-files-overview.md)
