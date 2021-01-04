---
title: Abfragebeschleunigung für Azure Data Lake Storage
description: Die Abfragebeschleunigung ermöglicht es Anwendungen und Analyseframeworks, die Datenverarbeitung drastisch zu optimieren. Dabei werden nur die Daten abgerufen, die für einen Verarbeitungsvorgang erforderlich sind.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912755"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Abfragebeschleunigung für Azure Data Lake Storage

Die Abfragebeschleunigung ermöglicht es Anwendungen und Analyseframeworks, die Datenverarbeitung drastisch zu optimieren. Dabei werden nur die Daten abgerufen, die für die Durchführung eines bestimmten Vorgangs erforderlich sind. So werden weniger Zeit und Verarbeitungsleistung benötigt, um wichtige Einblicke in gespeicherte Daten zu erhalten.

## <a name="overview"></a>Übersicht

Die Abfragebeschleunigung unterstützt die Filterung von *Prädikaten* und *Spaltenprojektionen*. So können Anwendungen Zeilen und Spalten zu dem Zeitpunkt filtern, zu dem die Daten vom Datenträger gelesen werden. Nur die Daten, die die Bedingungen eines Prädikats erfüllen, werden über das Netzwerk an die Anwendung übertragen. Dies reduziert die Netzwerklatenz und die Computekosten.  

Sie können SQL verwenden, um die Zeilenfilterprädikate und Spaltenprojektionen in einer Abfragebeschleunigungsanforderung anzugeben. Von einer Anforderung wird jeweils nur eine Datei verarbeitet. Daher werden erweiterte relationale SQL-Funktionen, z. B. Joins und GROUP BY-Aggregate, nicht unterstützt. Die Abfragebeschleunigung unterstützt CSV- und JSON-formatierte Daten als Eingabe für die einzelnen Anforderungen.

Die Abfragebeschleunigung ist nicht auf Data Lake Storage beschränkt (Speicherkonten, für die der hierarchische Namespace aktiviert wurde). Die Abfragebeschleunigung ist vollständig kompatibel mit den Blobs in Speicherkonten, für die **kein** hierarchischer Namespace aktiviert wurde. Dies bedeutet, dass sich die Netzwerklatenz und die Computekosten genauso reduzieren lassen, wenn Sie Daten verarbeiten, die bereits als Blobs in Speicherkonten gespeichert wurden.

Ein Beispiel für die Verwendung der Abfragebeschleunigung in einer Clientanwendung finden Sie unter [Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Datenfluss

Im folgenden Diagramm wird veranschaulicht, wie eine typische Anwendung die Abfragebeschleunigung verwendet, um Daten zu verarbeiten.

> [!div class="mx-imgBorder"]
> ![Abfragebeschleunigung – Übersicht](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Die Clientanwendung fordert Dateidaten an, indem sie Prädikate und Spaltenprojektionen angibt.

2. Bei der Abfragebeschleunigung wird die angegebene SQL-Abfrage analysiert. Anschließend wird die Arbeitslast für das Analysieren und Filtern von Daten verteilt.

3. Die Daten werden von Prozessoren vom Datenträger gelesen, unter Verwendung des geeigneten Formats analysiert und dann gefiltert, indem die angegebenen Prädikate und Spaltenprojektionen angewendet werden.

4. Bei der Abfragebeschleunigung werden die Antwortshards kombiniert, die an die Clientanwendung zurückgestreamt werden.

5. Die Clientanwendung empfängt und analysiert die gestreamte Antwort. Die Anwendung muss keine zusätzlichen Daten filtern und kann die gewünschte Berechnung oder Transformation direkt anwenden.

## <a name="better-performance-at-a-lower-cost"></a>Bessere Leistung zu geringeren Kosten

Die Abfragebeschleunigung optimiert die Leistung, indem die Menge der Daten reduziert wird, die übertragen und von Ihrer Anwendung verarbeitet wird.

Um einen aggregierten Wert zu berechnen, rufen Anwendungen in der Regel **alle** Daten aus einer Datei ab und verarbeiten und filtern die Daten dann lokal. Eine Analyse der Eingabe-/Ausgabemuster für Analyseworkloads zeigt, dass Anwendungen in der Regel nur 20 % der gelesenen Daten benötigen, um die jeweilige Berechnung auszuführen. Diese Statistik gilt selbst dann, wenn Techniken wie [Partition Pruning](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning) angewendet wurden. Das bedeutet, dass 80 % dieser Daten unnötigerweise von Anwendungen über das Netzwerk übertragen, analysiert und gefiltert werden. Dieses Muster, das im Wesentlichen dafür konzipiert wurde, nicht benötigte Daten auszuschließen, verursacht erhebliche Computekosten.  

Obwohl Azure sowohl im Hinblick auf den Durchsatz als auch auf die Latenz ein branchenführendes Netzwerk bietet, wirkt sich die unnötige Übertragung von Daten über dieses Netzwerk immer noch negativ auf die Anwendungsleistung aus. Durch die Abfragebeschleunigung werden diese negativen Auswirkungen beseitigt, da unerwünschte Daten während der Speicheranforderung herausgefiltert werden.

Außerdem muss Ihre Anwendung durch die CPU-Last, die zum Analysieren und Filtern nicht benötigter Daten erforderlich ist, mehr und größere VMS bereitstellen, um die Arbeit zu bewältigen. Da diese Computelast von der Abfragebeschleunigung übernommen wird, können Anwendungen beträchtliche Kosteneinsparungen erzielen.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Anwendungen, die von der Abfragebeschleunigung profitieren können

Die Abfragebeschleunigung wurde für Frameworks für verteilte Analysen und Datenverarbeitungsanwendungen konzipiert. 

Frameworks für verteilte Analysen wie Apache Spark und Apache Hive bieten eine Speicherabstraktionsschicht innerhalb des Frameworks. Diese Engines verfügen auch über Abfrageoptimierer, die Informationen zu den Fähigkeiten des zugrunde liegenden E/A-Diensts berücksichtigen können, wenn ein optimaler Abfrageplan für Benutzerabfragen ermittelt wird. Die Abfragebeschleunigung wird bereits in die ersten Frameworks integriert. Infolgedessen profitieren Benutzer dieser Frameworks von einer verbesserten Abfragelatenz und geringeren Gesamtkosten, ohne dass Änderungen an den Abfragen vorgenommen werden müssen. 

Die Abfragebeschleunigung ist auch für Datenverarbeitungsanwendungen geeignet. Diese Anwendungstypen führen in der Regel umfangreiche Datentransformationen aus, die u. U. keine direkten analytischen Erkenntnisse liefern. Daher nutzen sie nicht immer etablierte Frameworks für verteilte Analysen. Die Anwendungen verfügen häufig über eine direktere Beziehung zu dem zugrunde liegenden Speicherdienst, sodass sie direkt von Features wie der Abfragebeschleunigung profitieren können. 

Ein Beispiel für die Integration der Abfragebeschleunigung in eine Anwendung finden Sie unter [Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Preise

Aufgrund der höheren Computelast innerhalb des Azure Data Lake Storage-Diensts unterscheidet sich das Preismodell für die Verwendung der Abfragebeschleunigung vom normalen Azure Data Lake Storage-Transaktionsmodell. Bei der Abfragebeschleunigung werden Kosten für die Menge der überprüften Daten sowie für die an den Aufrufer zurückgegebene Datenmenge berechnet. Weitere Informationen finden Sie unter [Azure Data Lake Storage Gen2: Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Trotz des geänderten Abrechnungsmodells ist das Preismodell für die Abfragebeschleunigung so angelegt, dass die Gesamtkosten für eine Workload geringer ausfallen, da die wesentlich höheren Kosten für VMs gesenkt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md)
- [Abfragebeschleunigung – SQL-Sprachreferenz](query-acceleration-sql-reference.md)