---
title: 'Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen: Team Data Science-Prozess'
description: Downsampling von in Azure HDInsight Hive-Tabellen gespeicherten Daten mithilfe von Hive-Abfragen, um Daten auf eine für die Analyse besser verwaltbare Größe zu reduzieren.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321877"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen
In diesem Artikel wird das Downsampling von in Azure HDInsight Hive-Tabellen gespeicherten Daten mithilfe von Hive-Abfragen beschrieben, um Daten auf eine für die Analyse besser verwaltbare Größe zu reduzieren. Dabei werden drei häufig verwendete Samplingmethoden behandelt:

* Einheitliche Stichproben
* Zufällige Stichproben nach Gruppen
* Geschichtete Stichproben

**Warum eine Datenstichprobe entnehmen?**
Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Downsampling erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Team Data Science-Prozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Machine Learning-Modelle zu erstellen.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Gewusst wie: Übermitteln von Hive-Abfragen
Hive-Abfragen können in der Hadoop-Befehlszeilenkonsole auf dem Hauptknoten des Hadoop-Clusters übermittelt werden.  Melden Sie sich beim Hauptknoten des Hadoop-Clusters an, öffnen Sie die Hadoop-Befehlszeilenkonsole, und übermitteln Sie die Hive-Abfragen von dort aus. Informationen zum Übermitteln von Hive-Abfragen in der Hadoop-Befehlszeilenkonsole finden Sie unter [Übermitteln von Hive-Abfragen](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Einheitliche Stichproben
Bei einheitlichen Stichproben hat jede Zeile im Dataset die gleiche Wahrscheinlichkeit, als Stichprobe verwendet zu werden. Dies kann durch das Hinzufügen des zusätzlichen Felds „rand()“ zum Dataset in der inneren select-Abfrage und in der äußeren select-Abfrage, die von diesem Zufallsfeld abhängt, implementiert werden.

Dies ist eine Beispielabfrage:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

Dabei gibt `<sample rate, 0-1>` den Anteil von Datensätzen an, den die Benutzer erfassen möchten.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Zufällige Stichproben nach Gruppen
Bei der Entnahme von Stichproben aus kategorischen Daten sollten Sie entweder alle Instanzen eines bestimmten Werts der kategorischen Variable ein- oder ausschließen. Diese Art von Sampling wird als „Stichprobe nach Gruppe“ bezeichnet. Wenn Sie beispielsweise die kategorische Variable „ *State* “ haben, die als Werte „NY“, „MA“, „CA“, „NJ“, „PA“ usw. hat, möchten Sie natürlich, dass Datensätze der einzelnen Bundesstaaten immer zusammenbleiben, ob als Stichprobe oder auch sonst.

Dies ist eine Beispielabfrage für das Erstellen einer Stichprobe nach Gruppe:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Geschichtete Stichproben
Zufällige Stichproben sind in Bezug auf eine kategorische Variable geschichtet, wenn die abgerufenen Beispieldaten kategorische Werte enthalten, die im gleichen Verhältnis wie in der übergeordneten Datenmenge vorliegen. Bei dem oben genannten Beispiel besitzen Ihre Daten beispielsweise die folgenden Vorkommen nach Bundesstaat: „NJ“ hat 100 Vorkommen, „NY“ 60 und „WA“ 300. Wenn Sie die Rate für die geschichtete Stichprobe auf 0,5 festlegen, sollte die abgerufene Stichprobe ungefähr 50, 30 und 150 Vorkommen für "NJ", "NY" und "WA" aufweisen.

Dies ist eine Beispielabfrage:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Informationen zu komplexeren Methoden für das Extrahieren von Stichproben, die in Hive verfügbar sind, finden Sie unter [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)(in englischer Sprache).