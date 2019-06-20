---
title: Wichtige Unterschiede bei den Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)
description: Dieses Thema beschreibt die wichtigsten Unterschiede zwischen Machine Learning Services von Azure SQL-Datenbank (mit R) und SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725058"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Wichtige Unterschiede zwischen Machine Learning Services von Azure SQL-Datenbank (Vorschauversion) und SQL Server

Die Machine Learning Services von Azure SQL-Datenbank (mit R) (Vorschauversion) ähneln in ihrer Funktionalität den [Machine Learning Services von SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nachfolgend finden Sie einige wesentliche Unterschiede.

> [!IMPORTANT]
> Die Machine Learning Services von Azure SQL-Datenbank werden derzeit in einer öffentlichen Vorschauversion bereitgestellt.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Sprachunterstützung

SQL Server bietet Unterstützung für R und Python über das [Erweiterbarkeitsframework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL-Datenbank unterstützt nicht beide Sprachen. Folgende wichtige Unterschiede bestehen:

- R ist die einzige unterstützte Sprache in SQL-Datenbank. Momentan ist keine Unterstützung für Python vorhanden.
- Die Version von R ist 3.4.4.
- Es ist nicht erforderlich, `external scripts enabled` über `sp_configure` zu konfigurieren. Sobald Sie [registriert](sql-database-machine-learning-services-overview.md#signup) sind, wird Machine Learning für Ihre SQL-Datenbank aktiviert.

## <a name="package-management"></a>Paketverwaltung

Die R-Paketverwaltung und -Installation funktionieren für SQL-Datenbank und SQL Server unterschiedlich. Die folgenden Unterschiede bestehen:

- R-Pakete werden über [sqlmlutils](https://github.com/Microsoft/sqlmlutils) oder [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) installiert.
- Pakete können keine ausgehenden Netzwerkaufrufe ausführen. Diese Einschränkung ähnelt den [Standardfirewallregeln für Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, kann jedoch in SQL-Datenbank nicht geändert werden.
- Es gibt keine Unterstützung für Pakete, die von externen Runtimes (z.B. Java) abhängig sind oder Zugriff auf Betriebssystem-APIs für die Installation oder Verwendung benötigen.

## <a name="resource-governance"></a>Ressourcenkontrolle

Es ist nicht möglich, R-Ressourcen durch [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) und externe Ressourcenpools zu beschränken.

In der öffentlichen Vorschauversion sind R-Ressourcen auf ein Maximum von 20 % der SQL-Datenbankressourcen festgelegt. Das richtet sich nach der ausgewählten Dienstebene. Weitere Informationen finden Sie unter [Kaufmodelle für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Fehler bei nicht ausreichendem Arbeitsspeicher

Wenn für R nicht genügend Arbeitsspeicher verfügbar ist, erhalten Sie eine Fehlermeldung. Häufige Fehlermeldungen:

- Fehler beim Kommunizieren mit der Runtime für das Skript "R" für die Anforderungs-ID: "*******". Überprüfen Sie die Anforderungen der Runtime
- Unerwarteter "R"-Skriptfehler beim Ausführen von "sp_execute_external_script" mit HRESULT 0x80004004. Externer Skriptfehler: "In C-Funktion 'R_AllocStringBuffer' konnte kein Speicher (0 MB) zugewiesen werden"
- Externer Skriptfehler: Fehler: Vektor dieser Größe kann nicht zugewiesen werden.

Die Arbeitsspeichernutzung hängt von der in R-Skripts verwendeten Anzahl und von der Anzahl parallel ausgeführter Abfragen ab. Wenn die obigen Fehlermeldungen angezeigt werden, können Sie Ihre Datenbank zur Lösung dieses Problems auf eine höhere Dienstebene hochstufen.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die Übersicht, [Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-overview.md).
- Wenn Sie erfahren möchten, wie Sie R zum Abfragen von Machine Learning Services in Azure SQL-Datenbank (Vorschauversion) verwenden, lesen Sie die [Schnellstartanleitung](sql-database-connect-query-r.md).
- Für die ersten Schritte mit einfachen R-Skripts lesen Sie [Erstellen und Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)](sql-database-quickstart-r-create-script.md).
