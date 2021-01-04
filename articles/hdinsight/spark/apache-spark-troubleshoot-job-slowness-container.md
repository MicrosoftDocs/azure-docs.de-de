---
title: Langsame Ausführung in Apache Spark, wenn der Azure HDInsight-Speicher viele Dateien enthält
description: Langsame Ausführung eines Apache Spark-Auftrags, wenn der Azure-Speichercontainer viele Dateien in Azure HDInsight enthält
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: f14e9bfad959d7f40b2d364c702983c31e0e3cef
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289761"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Langsame Ausführung eines Apache Spark-Auftrags, wenn der Azure-Speichercontainer viele Dateien in Azure HDInsight enthält

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Wenn Sie einen HDInsight-Cluster ausführen, wird der Apache Spark-Auftrag, der in den Azure-Speichercontainer schreibt, langsam, wenn eine große Anzahl von Dateien/Unterordnern vorhanden ist. Schreibvorgänge in einem neuen Container dauern beispielsweise 20 Sekunden, in einem Container mit 200.000 Dateien dagegen etwa zwei Minuten.

## <a name="cause"></a>Ursache

Dies ist ein bekanntes Spark-Problem. Die Verlangsamung hängt mit den Vorgängen `ListBlob` und `GetBlobProperties` im Rahmen der Ausführung von Spark-Aufträgen zusammen.

Zur Nachverfolgung von Partitionen benötigt Spark einen Dateistatuscache (`FileStatusCache`) mit Informationen zur Verzeichnisstruktur. Anhand dieses Caches kann Spark die Pfade analysieren und die verfügbaren Partitionen berücksichtigen. Der Vorteil der Partitionsnachverfolgung besteht darin, dass Spark beim Lesen von Daten nur auf die erforderlichen Dateien zugreift. Um diese Informationen auf dem neuesten Stand zu halten, muss Spark beim Schreiben neuer Daten alle Dateien im Verzeichnis auflisten und diesen Cache aktualisieren.

In Spark 2.1 muss der Cache zwar nicht nach jedem Schreibvorgang aktualisiert werden, Spark überprüft jedoch, ob eine bereits vorhandene Partitionsspalte mit der in der aktuellen Schreibanforderung vorgeschlagenen Spalte übereinstimmt, was ebenfalls Auflistungsvorgänge zu Beginn jedes Schreibvorgangs zur Folge hat.

In Spark 2.2 wird dieses Leistungsproblem behoben, indem Daten im Anfügemodus geschrieben werden.

## <a name="resolution"></a>Lösung

Wenn Sie ein partitioniertes Dataset erstellen, ist es wichtig, ein Partitionierungsschema zu verwenden, das die Anzahl von Dateien einschränkt, die von Spark aufgelistet werden müssen, um den Dateistatuscache (`FileStatusCache`) zu aktualisieren.

Verschieben Sie bereits vorhandene Daten für jeden n-ten Microbatch mit „N % 100 == 0“ („100“ ist hier nur ein Beispiel) in ein anderes Verzeichnis, das von Spark geladen werden kann.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]