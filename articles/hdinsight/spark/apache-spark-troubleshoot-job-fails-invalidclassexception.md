---
title: Fehler „InvalidClassException“ aus Apache Spark – Azure HDInsight
description: Apache Spark-Auftrag schlägt mit InvalidClassException, Klassenversionskonflikt in Azure HDInsight fehl
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 6220c328d05e7cd68460b7bfd0708a9d393a290f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287891"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark-Auftrag schlägt mit InvalidClassException, Klassenversionskonflikt in Azure HDInsight fehl

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Sie versuchen, einen Apache Spark-Auftrag in einem Spark 2. x-Cluster zu erstellen. Dabei tritt ein Fehler ähnlich dem folgenden auf:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Ursache

Dieser Fehler kann durch Hinzufügen einer zusätzlichen JAR-Datei zur `spark.yarn.jars`-Konfiguration verursacht werden, insbesondere einer „schattierten“ (shaded) JAR-Datei, die eine andere Version des `commons-lang3`-Pakets enthält und einen Klassenkonflikt verursacht. Standardmäßig verwendet Spark 2.1/2/3 Version 3.5 von `commons-lang3`.

> [!TIP]
> Eine Bibliothek zu schattieren, bedeutet, den Inhalt der Bibliothek in Ihrer eigenen JAR-Datei zu speichern, wodurch das Paket geändert wird. Dies unterscheidet sich vom Verpacken der Bibliothek, bei dem die Bibliothek ohne erneutes Verpacken Ihrer eigenen JAR-Datei hinzugefügt wird.

## <a name="resolution"></a>Lösung

Entfernen Sie die JAR-Datei, oder kompilieren Sie die angepasste JAR-Datei (AzureLogAppender) erneut, und verwenden Sie das [maven-shade-Plug-In](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html), um Klassen zu verschieben.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]