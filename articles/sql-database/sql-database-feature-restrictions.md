---
title: Einschränkungen von Azure SQL-Datenbank-Funktionen | Microsoft-Dokumentation
description: Bei Verwendung der Einschränkungen von Azure SQL-Datenbank-Funktionen verbessert sich Ihre Datenbanksicherheit. In Ihrer Datenbank werden Features eingeschränkt, über die sich Angreifer ggf. Zugang zu den darin enthaltenen Informationen verschaffen können.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259412"
---
# <a name="azure-sql-database-feature-restrictions"></a>Einschränkungen von Azure SQL-Datenbank-Funktionen

SQL Server-Angriffe erfolgen häufig über Webanwendungen, mit denen auf die Datenbank zugegriffen wird. Hierbei werden unterschiedliche Formen von Angriffen mit Einschleusung von SQL-Befehlen genutzt, um sich Informationen über die Datenbank zu verschaffen.  Im Idealfall wird Anwendungscode so entwickelt, dass keine Einschleusung von SQL-Befehlen möglich ist.  Bei einer umfangreichen Codebasis mit älterem und externem Code kann aber niemals ganz sichergestellt werden, dass alle Eventualitäten abgedeckt sind. Daher handelt es sich bei der Einschleusung von SQL-Befehlen also um eine reale Gefahr, für die ein Schutz vorhanden sein muss.  Das Ziel von Featureeinschränkungen besteht darin, bei einigen Formen der Einschleusung von SQL-Befehlen auch dann vor dem Offenlegen von Informationen über die Datenbank zu schützen, wenn die Einschleusung von SQL-Befehlen erfolgreich ist.

## <a name="enabling-feature-restrictions"></a>Aktivieren der Featureeinschränkungen

Für die Aktivierung von Featureeinschränkungen wird die gespeicherte Prozedur `sp_add_feature_restriction` wie folgt eingesetzt:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Die folgenden Features können eingeschränkt werden:

| Feature          | BESCHREIBUNG |
|------------------|-------------|
| N'ErrorMessages' | Bei einer Einschränkung werden alle Benutzerdaten in der Fehlermeldung maskiert. Weitere Informationen: [Featureeinschränkung für Fehlermeldungen](#error-messages-feature-restriction) |
| N'Waitfor'       | Bei einer Einschränkung erfolgt die Rückgabe des Befehls sofort ohne Verzögerung. Weitere Informationen: [Featureeinschränkung WAITFOR](#waitfor-feature-restriction) |

Der Wert von `object_class` kann entweder `N'User'` oder `N'Role'` lauten, um anzugeben, ob `object_name` in der Datenbank ein Benutzer- oder Rollenname ist.

Mit dem Code im folgenden Beispiel werden alle Fehlermeldungen für den Benutzer `MyUser` maskiert:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Deaktivieren der Featureeinschränkungen

Für die Deaktivierung von Featureeinschränkungen wird die gespeicherte Prozedur `sp_drop_feature_restriction` wie folgt eingesetzt:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Im folgenden Beispiel wird die Maskierung der Fehlermeldungen für den Benutzer `MyUser` deaktiviert:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Anzeigen der Featureeinschränkungen

In der Ansicht `sys.sql_feature_restrictions` werden alle Featureeinschränkungen angezeigt, die derzeit für die Datenbank definiert sind. Die folgenden Spalten sind vorhanden:

| Spaltenname | Datentyp | BESCHREIBUNG |
|-------------|-----------|-------------|
| class       | nvarchar(128) | Klasse des Objekts, für das die Einschränkung gilt |
| object      | nvarchar(256) | Name des Objekts, für das die Einschränkung gilt |
| Feature     | nvarchar(128) | Eingeschränktes Feature |

## <a name="feature-restrictions"></a>Featureeinschränkungen

### <a name="error-messages-feature-restriction"></a>Featureeinschränkung für Fehlermeldungen

Eine häufige Methode bei einem Angriff mit Einschleusung von SQL-Befehlen ist das Einschleusen von Code, der einen Fehler verursacht.  Ein Angreifer kann die Fehlermeldung untersuchen, um Informationen zum System zu erhalten und weitere Angriffe zu starten, die zielgerichteter sind.  Diese Art von Angriff kann besonders effektiv sein, wenn von einer Anwendung die Ergebnisse einer Abfrage nicht angezeigt werden, während dies für Fehlermeldungen der Fall ist.

Stellen Sie sich eine Webanwendung vor, die über eine Anforderung der folgenden Art verfügt:

```html
http://www.contoso.com/employee.php?id=1
```

Hiermit wird die folgende Datenbankabfrage durchgeführt:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Wenn der Wert, der als Parameter `id` an die Anforderung der Webanwendung übergeben wird, kopiert wird, um in der Datenbankabfrage „$EmpId“ zu ersetzen, kann ein Angreifer die folgende Anforderung senden:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Der folgende Fehler wird zurückgegeben, in dem der Angreifer den Namen der Datenbank ersehen kann:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Nachdem die Featureeinschränkung für Fehlermeldungen für den Benutzer der Anwendung in der Datenbank aktiviert wurde, wird die zurückgegebene Fehlermeldung maskiert, damit keine internen Informationen zur Datenbank abgeschöpft werden können:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Der Angreifer kann auch die folgende Anforderung senden:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Der folgende Fehler wird zurückgegeben, aus dem der Angreifer das Gehalt des Mitarbeiters entnehmen kann:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Bei Verwendung der Featureeinschränkung für Fehlermeldungen gibt die Datenbank Folgendes zurück:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Featureeinschränkung WAITFOR

Bei einem Angriff mit blinder Einschleusung von SQL-Befehlen liefert eine Anwendung einem Angreifer über den eingeschleusten SQL-Code oder per Fehlermeldung keine Ergebnisse. Der Angreifer kann aber Informationen aus der Datenbank ableiten, indem er eine bedingte Abfrage erstellt, bei der die beiden Verzweigungen der Bedingung eine unterschiedliche Ausführungsdauer aufweisen. Der Angreifer kann die Antwortzeiten vergleichen und ermitteln, welche Verzweigung ausgeführt wurde, und so Informationen zum System erhalten. Die einfachste Variante dieses Angriffs ist die Verwendung der `WAITFOR`-Anweisung zur Einfügung der Verzögerung.

Stellen Sie sich eine Webanwendung vor, die über eine Anforderung der folgenden Art verfügt:

```html
http://www.contoso.com/employee.php?id=1
```

Hiermit wird die folgende Datenbankabfrage ausgeführt:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Wenn der Wert, der als Parameter „id“ an die Anforderung der Webanwendung übergeben wird, kopiert wird, um in der Datenbankabfrage „$EmpId“ zu ersetzen, kann ein Angreifer die folgende Anforderung senden:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

Die Abfrage würde außerdem weitere fünf Sekunden länger dauern, wenn das Konto `sa` verwendet wird. Wenn die Featureeinschränkung `WAITFOR` in der Datenbank deaktiviert ist, wird die `WAITFOR`-Anweisung ignoriert, und bei diesem Angriff werden keine Informationen offengelegt.