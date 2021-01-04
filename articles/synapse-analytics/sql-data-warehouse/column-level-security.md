---
title: Sicherheit auf Spaltenebene für dedizierte SQL-Pools
description: Mithilfe der Sicherheit auf Spaltenebene können Kunden den Zugriff auf Spalten in Datenbanktabellen basierend auf dem Ausführungskontext oder der Gruppenmitgliedschaft des Benutzers steuern und somit den Entwurf und die Programmierung der Sicherheit in Ihrer Anwendung vereinfachen. Dies ermöglicht das Implementieren von Einschränkungen beim Spaltenzugriff.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 817a912dabfc5365eabe8e0dabd7e0b40e40c525
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462503"
---
# <a name="column-level-security"></a>Sicherheit auf Spaltenebene

Mit Sicherheit auf Spaltenebene können Kunden den Zugriff auf Tabellenspalten basierend auf dem Ausführungskontext oder der Gruppenmitgliedschaft des Benutzers steuern.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Nach der Bereitstellung dieses Videos wurde auch die [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für dedizierte SQL-Pools in Azure Synapse eingeführt.

Sicherheit auf Spaltenebene vereinfacht das Entwerfen und Programmieren der Sicherheit in Ihrer Anwendung, indem Sie den Spaltenzugriff einschränken können, um vertrauliche Daten zu schützen. Zum Beispiel können Sie so sicherstellen, dass bestimmte Benutzer nur auf bestimmte Spalten einer Tabelle zugreifen können, die für ihre Abteilung relevant sind. Die Datenbeschränkungszugriffslogik befindet sich auf der Datenbankebene, statt fern der Daten auf einer anderen Anwendungsebene. Die Datenbank wendet die Zugriffsbeschränkungen jedes Mal an, wenn ein Datenzugriff von einer beliebigen Ebene aus versucht wird. Dadurch bietet Ihr gesamtes Sicherheitssystem eine geringere Angriffsfläche und ist zuverlässiger und robuster. Darüber hinaus entfällt dank Sicherheit auf Spaltenebene die Notwendigkeit, Sichten zum Herausfiltern von Spalten einzuführen, um den Benutzern Zugriffsbeschränkungen aufzuerlegen.

Sie können die Sicherheit auf Spaltenebene mit der T-SQL-Anweisung [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) implementieren. Mit diesem Mechanismus wird sowohl die SQL- als auch die Azure AD-Authentifizierung (Azure Active Directory) unterstützt.

![Diagramm einer schematische Tabelle, bei der die erste Spalte ein geschlossenes Vorhängeschloss und orangefarbene Zellen aufweist, während die Zellen in den anderen Spalten weiß sind](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird erläutert, wie der Zugriff von `TestUser` auf die Spalte `SSN` in der Tabelle `Membership` eingeschränkt wird:

Erstellen Sie die Tabelle `Membership` mit einer Spalte „SSN“, um Sozialversicherungsnummern zu speichern:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Gestatten Sie `TestUser` den Zugriff auf alle Spalten mit Ausnahme der Spalte „SSN“ mit den vertraulichen Daten:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Bei Abfragen mit der Spalte „SSN“, die als `TestUser` ausgeführt werden, tritt dann ein Fehler auf:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Anwendungsfälle

Im Folgenden finden Sie einige Beispiele für aktuelle Anwendungsfälle der Sicherheit auf Spaltenebene:

- Ein Finanzdienstleistungsunternehmen erlaubt nur Kundenbetreuern den Zugriff auf Sozialversicherungsnummern (SSN), Telefonnummern und andere personenbezogene Daten (PII).
- Ein Gesundheitsdienstleister erlaubt nur Ärzten und Krankenschwestern den Zugriff auf vertrauliche Krankenakten, Mitglieder der Abrechnungsabteilung können diese Daten jedoch nicht einsehen.
