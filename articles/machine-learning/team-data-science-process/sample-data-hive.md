---
title: 'Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen: Team Data Science-Prozess'
description: Downsampling von in Azure HDInsight Hive-Tabellen gespeicherten Daten mithilfe von Hive-Abfragen, um Daten auf eine für die Analyse besser verwaltbare Größe zu reduzieren.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61042882"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen
In diesem Artikel wird das Downsampling von in Azure HDInsight Hive-Tabellen gespeicherten Daten mithilfe von Hive-Abfragen beschrieben, um Daten auf eine für die Analyse besser verwaltbare Größe zu reduzieren. Dabei werden drei häufig verwendete Samplingmethoden behandelt:

* Einheitliche Stichproben
* Zufällige Stichproben nach Gruppen
* Geschichtete Stichproben

**Warum eine Datenstichprobe entnehmen?**
Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Downsampling erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Team Data Science-Prozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Machine Learning-Modelle zu erstellen.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Gewusst wie: Übermitteln von Hive-Abfragen
Hive-Abfragen können in der Hadoop-Befehlszeilenkonsole auf dem Hauptknoten des Hadoop-Clusters übermittelt werden. Dazu melden Sie sich beim Hauptknoten des Hadoop-Clusters an, öffnen die Hadoop-Befehlszeilenkonsole und übermitteln die Hive-Abfragen von dort aus. Informationen zum Übermitteln von Hive-Abfragen in der Hadoop-Befehlszeilenkonsole finden Sie unter [Übermitteln von Hive-Abfragen](move-hive-tables.md#submit).

## <a name="uniform"></a> Einheitliche Stichproben
Bei einheitlichen Stichproben hat jede Zeile im Dataset die gleiche Wahrscheinlichkeit, als Stichprobe verwendet zu werden. Dies kann durch das Hinzufügen des zusätzlichen Felds „rand()“ zum Dataset in der inneren select-Abfrage und in der äußeren select-Abfrage, die von diesem Zufallsfeld abhängt, implementiert werden.

Dies ist eine Beispielabfrage:

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

Dabei gibt `<sample rate, 0-1>` den Anteil von Datensätzen an, den die Benutzer erfassen möchten.

## <a name="group"></a> Zufällige Stichproben nach Gruppen
Bei der Entnahme von Stichproben aus kategorischen Daten sollten Sie entweder alle Instanzen eines bestimmten Werts der kategorischen Variable ein- oder ausschließen. Diese Art von Sampling wird als „Stichprobe nach Gruppe“ bezeichnet. Wenn Sie beispielsweise die kategorische Variable „*State*“ haben, die als Werte „NY“, „MA“, „CA“, „NJ“, „PA“ usw. hat, möchten Sie natürlich, dass Datensätze der einzelnen Bundesstaaten immer zusammenbleiben, ob als Stichprobe oder auch sonst.

Dies ist eine Beispielabfrage für das Erstellen einer Stichprobe nach Gruppe:

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

## <a name="stratified"></a>Geschichtete Stichproben
Zufällige Stichproben sind in Bezug auf eine kategorische Variable geschichtet, wenn die abgerufenen Beispieldaten kategorische Werte enthalten, die im gleichen Verhältnis wie in der übergeordneten Datenmenge vorliegen. Verwenden Sie das gleiche Beispiel wie oben beschrieben. Angenommen, Ihre Daten weisen die folgenden Beobachtungen nach Zuständen auf: NJ hat 100 Beobachtungen, NY hat 60 Beobachtungen, und WA hat 300 Beobachtungen. Wenn Sie die Rate für die geschichtete Stichprobe auf 0,5 festlegen, sollte die abgerufene Stichprobe ungefähr 50, 30 und 150 Vorkommen für "NJ", "NY" und "WA" aufweisen.

Dies ist eine Beispielabfrage:

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


Informationen zu komplexeren Methoden für das Extrahieren von Stichproben, die in Hive verfügbar sind, finden Sie unter [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)(in englischer Sprache).

