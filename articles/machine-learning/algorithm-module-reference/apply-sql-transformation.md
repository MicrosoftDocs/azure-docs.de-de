---
title: Anwenden der SQL-Transformation
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul „Apply SQL Transformation“ in Azure Machine Learning eine SQLite-Abfrage für Eingabedatasets ausführen, um die Daten zu transformieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555599"
---
# <a name="apply-sql-transformation"></a>Anwenden der SQL-Transformation

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Mit dem Modul „Apply SQL Transformation“ (Anwenden einer SQL-Transformation) können Sie folgende Vorgänge ausführen:
  
-   Erstellen von Tabellen für Ergebnisse und Speichern der Datasets in einer portierbaren Datenbank  
  
-   Ausführen von benutzerdefinierten Transformationen für Datentypen oder Erstellen von Aggregaten  
  
-   Ausführen von SQL-Abfrageanweisungen, um Daten zu filtern oder zu ändern und die Abfrageergebnisse als Datentabelle zurückzugeben  

> [!IMPORTANT]
> Die in diesem Modul verwendete SQL-Engine ist **SQLite**. Weitere Informationen zur SQLite-Syntax finden Sie unter [SQL As Understood By SQLite](https://www.sqlite.org/index.html).
> Dieses Modul leitet Daten an SQLite weiter, das sich in der Arbeitsspeicherdatenbank befindet, daher benötigt die Modulausführung viel mehr Speicher und kann einen `Out of memory`-Fehler auslösen. Stellen Sie sicher, dass Ihr Computer über genügend RAM verfügt.

## <a name="how-to-configure-apply-sql-transformation"></a>Konfigurieren von „Apply SQL Transformation“  

Das Modul kann bis zu drei Datasets als Eingaben übernehmen. Wenn Sie auf die Datasets verweisen, die mit den einzelnen Eingabeports verbunden sind, müssen Sie die Namen `t1`, `t2` und `t3` verwenden. Die Tabellennummer gibt den Index des Eingabeports an.  

Das folgende Codebeispiel zeigt, wie zwei Tabellen verknüpft werden können. t1 und t2 sind zwei Datasets, die mit dem linken und mittleren Eingabeport von **Anwenden der SQL-Transformation** verbunden sind:

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
Der verbleibende Parameter ist eine SQL-Abfrage, für die die SQLite-Syntax verwendet wird. Wenn Sie mehrere Zeilen in das Textfeld **SQL-Skript** eingeben, verwenden Sie ein Semikolon, um jede Anweisung zu beenden. Andernfalls werden Zeilenumbrüche in Leerzeichen konvertiert.  

In diesem Modul werden alle Standardanweisungen der SQLite-Syntax unterstützt. Eine Liste der nicht unterstützten-Anweisungen finden Sie im Abschnitt [Technische Hinweise](#technical-notes).

##  <a name="technical-notes"></a>Technische Hinweise  

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

-   An Port 1 ist immer eine Eingabe erforderlich.  
  
-   Enthält ein Spaltenbezeichner (Spalten-ID) ein Leerzeichen oder andere Sonderzeichen, müssen Sie den Bezeichner in eckige Klammern oder doppelte Anführungszeichen einschließen, wenn Sie in einer `SELECT`- oder `WHERE`-Klausel auf die Spalte verweisen.  
  
### <a name="unsupported-statements"></a>Nicht unterstützte Anweisungen  

SQLite unterstützt zwar einen Großteil des ANSI SQL-Standards, umfasst aber nicht viele Funktionen, die von kommerziellen relationalen Datenbanksystemen unterstützt werden. Weitere Informationen finden Sie unter [SQL as Understood by SQLite](http://www.sqlite.org/lang.html). Beachten Sie beim Erstellen von SQL-Anweisungen außerdem die folgenden Einschränkungen:  
  
- SQLite verwendet dynamische Typzuweisung für Werte, anstatt jeder Spalte einen Typ zuzuweisen, wie dies in den meisten relationalen Datenbanksystemen geschieht. SQLite ist schwach typisiert und ermöglicht implizite Typkonvertierung.  
  
- `LEFT OUTER JOIN` ist implementiert, `RIGHT OUTER JOIN` und `FULL OUTER JOIN` sind dagegen nicht implementiert.  

- Sie können `RENAME TABLE`- und `ADD COLUMN`-Anweisungen mit dem `ALTER TABLE`-Befehl verwenden, andere Klauseln werden jedoch nicht unterstützt, einschließlich `DROP COLUMN`, `ALTER COLUMN` und `ADD CONSTRAINT`.  
  
- Sie können eine SICHT in SQLite erstellen, aber danach sind Sichten schreibgeschützt. Sie können keine `DELETE`-, `INSERT`- oder `UPDATE`-Anweisung für eine Sicht ausführen. Sie können jedoch einen Trigger erstellen, der beim Ausführen einer `DELETE`-, `INSERT`- oder `UPDATE`-Anweisung für eine Sicht ausgelöst wird, und Sie können in dem Trigger weitere Vorgänge ausführen.  
  

Zusätzlich zur Liste der nicht unterstützten Funktionen, die auf der offiziellen SQLite-Website zu finden ist, wird im folgenden Wiki eine Liste weiterer nicht unterstützter Features bereitgestellt: [SQLite - Unsupported SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
