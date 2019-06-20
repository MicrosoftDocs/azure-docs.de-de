---
title: Sicherheit auf Spaltenebene für Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Mit der Sicherheit auf Spaltenebene (CLS) können Kunden den Zugriff auf Datentabellenspalten basierend auf dem Ausführungskontext oder der Gruppenmitgliedschaft des Benutzers steuern. CLS vereinfacht das Entwerfen und Programmieren der Sicherheit in Ihrer Anwendung. Mit CLS können Sie den Zugriff auf Spalten einschränken.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61082795"
---
# <a name="column-level-security"></a>Sicherheit auf Spaltenebene
Mit der Sicherheit auf Spaltenebene (CLS) können Kunden den Zugriff auf Datentabellenspalten basierend auf dem Ausführungskontext oder der Gruppenmitgliedschaft des Benutzers steuern.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS vereinfacht das Entwerfen und Programmieren der Sicherheit in Ihrer Anwendung. Mit CLS können Sie den Zugriff auf Spalten zum Schutz vertraulicher Daten einschränken. Zum Beispiel können Sie so sicherstellen, dass bestimmte Benutzer nur auf bestimmte Spalten einer Tabelle zugreifen können, die für ihre Abteilung relevant sind. Die Zugriffsbeschränkungslogik befindet sich auf der Datenbankebene und nicht auf einer anderen, von den Daten getrennten Anwendungsebene. Die Datenbank wendet die Zugriffsbeschränkungen jedes Mal an, wenn ein Datenzugriff von einer beliebigen Ebene aus versucht wird. Dadurch bietet Ihr gesamtes Sicherheitssystem eine geringere Angriffsfläche und ist zuverlässiger und robuster. Darüber hinaus entfällt dank CLS die Notwendigkeit, Ansichten zum Herausfiltern von Spalten einzuführen, um den Benutzern Zugriffsbeschränkungen aufzuerlegen.

Sie können CLS mit der T-SQL-Anweisung [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) implementieren. Mit diesem Mechanismus wird sowohl die SQL- als auch die Azure Active Directory (AAD)-Authentifizierung unterstützt.

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```sql
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
Im folgenden Beispiel wird erläutert, wie der Zugriff von „TestUser“ auf die Spalte „SSN“ in der Tabelle „Mitgliedschaft“ eingeschränkt wird:

Erstellen Sie die Tabelle „Mitgliedschaft“ mit der Spalte „SSN“, um Sozialversicherungsnummern zu speichern:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Gestatten Sie „TestUser“ den Zugriff auf alle Spalten mit Ausnahme der Spalte „SSN“ mit den vertraulichen Daten:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Bei als „TestUser“ ausgeführten Abfragen mit der Spalte „SSN“ tritt dann ein Fehler auf:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Anwendungsfälle
Einige Beispiele, wie CLS derzeit verwendet wird:
- Ein Finanzdienstleistungsunternehmen erlaubt Kundenbetreuern nur den Zugriff auf Sozialversicherungsnummern (SSN), Telefonnummern und andere personenbezogene Daten (PII).
- Ein Gesundheitsdienstleister erlaubt nur Ärzten und Krankenschwestern den Zugriff auf vertrauliche Krankenakten, Mitglieder der Abrechnungsabteilung können diese Daten jedoch nicht einsehen.
