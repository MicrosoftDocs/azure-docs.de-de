---
title: Beheben von Leistungsproblemen – Azure Database for MariaDB
description: Erfahren Sie, wie Sie Probleme mit der Abfrageleistung in Azure Database for MariaDB mithilfe von EXPLAIN beheben.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 2b7491723ffcff73e4b243fe54ef18608167d636
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537236"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Verwenden von EXPLAIN zum Analysieren der Abfrageleistung in Azure Database for MariaDB
**EXPLAIN** ist ein praktisches Tool zum Optimieren von Abfragen. Mit einer EXPLAIN-Anweisung können Sie Informationen zur Ausführung von SQL-Anweisungen abrufen. Die folgende Ausgabe zeigt ein Beispiel für die Ausführung einer EXPLAIN-Anweisung.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Wie Sie in diesem Beispiel sehen können, ist der Wert von *key* NULL. Diese Ausgabe bedeutet, dass MariaDB keine für die Abfrage optimierten Indizes finden kann und einen vollständigen Tabellenscan ausführt. Wir optimieren diese Abfrage, indem wir einen Index für die Spalte **ID** hinzufügen.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Aus der neuen EXPLAIN-Anweisung geht hervor, dass MariaDB jetzt einen Index verwendet, um die Anzahl von Zeilen auf 1 zu begrenzen, wodurch sich wiederum die Suchzeit erheblich verkürzt.
 
## <a name="covering-index"></a>Abdeckender Index
Ein abdeckender Index besteht aus allen Spalten einer Abfrage im Index, sodass das Abrufen von Werten aus Datentabellen reduziert wird. Hier sehen Sie eine Abbildung in der folgenden **GROUP BY**-Anweisung.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Wie aus der Ausgabe hervorgeht, verwendet MariaDB keine Indizes, weil keine richtigen Indizes verfügbar sind. Außerdem wird *Using temporary; Using filesort* angezeigt, was bedeutet, dass MariaDB eine temporäre Tabelle erstellt, um die **GROUP BY**-Klausel zu erfüllen.
 
Das Erstellen eines Index für Spalte **c2** allein bewirkt keinen Unterschied, und MariaDB muss trotzdem eine temporäre Tabelle erstellen:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

In diesem Fall kann ein **Index mit vollständiger Abdeckung** für **c1** und **c2** erstellt werden, wobei der Wert von **c2** direkt im Index hinzugefügt wird, um eine weitere Datensuche zu vermeiden.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Wie aus der obigen EXPLAIN-Anweisung hervorgeht, verwendet MariaDB jetzt den Index mit vollständiger Abdeckung, und das Erstellen einer temporären Tabelle wird vermieden. 

## <a name="combined-index"></a>Kombinierter Index
Ein kombinierter Index besteht aus Werten aus mehreren Spalten und kann als Array von Zeilen betrachtet werden, die durch Verketten der Werte der indizierten Spalten sortiert werden.  Diese Methode kann in einer **GROUP BY**-Anweisung nützlich sein.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB führt eine *Dateisortierung* aus. Dieser Vorgang ist ziemlich langsam, insbesondere, wenn viele Zeilen sortiert werden müssen. Zur Optimierung dieser Abfrage kann ein kombinierter Index für beide Spalten erstellt werden, die sortiert werden.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

Aus der EXPLAIN-Anweisung geht jetzt hervor, dass MariaDB einen kombinierten Index verwenden kann, um eine zusätzliche Sortierung zu vermeiden, weil der Index bereits sortiert ist.
 
## <a name="conclusion"></a>Zusammenfassung
 
Durch die Verwendung von EXPLAIN und verschiedener Typen von Indizes kann die Leistung erheblich gesteigert werden. Wenn Sie über einen Index für die Tabelle verfügen, bedeutet das nicht zwangsläufig, dass MariaDB in der Lage ist, ihn für Ihre Abfragen zu verwenden. Überprüfen Sie Ihre Annahmen immer mit EXPLAIN, und optimieren Sie Ihre Abfragen mithilfe von Indizes.

## <a name="next-steps"></a>Nächste Schritte
- Um Antworten anderer Benutzer auf häufig gestellte Fragen zu erhalten oder eine neue Frage/Antwort zu veröffentlichen, besuchen Sie die [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-database-mariadb.html) oder [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).