---
title: Eine Apache Spark-Streaminganwendung wird nach 24 Tagen in Azure HDInsight beendet
description: Eine Apache Spark-Streaminganwendung wird beendet, nachdem sie 24 Tage lang ausgeführt wurde. In den Protokolldateien werden keine Fehler angezeigt.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288021"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Szenario: Eine Apache Spark-Streaminganwendung wird beendet, nachdem sie 24 Tage lang in Azure HDInsight ausgeführt wurde.

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Eine Apache Spark-Streaminganwendung wird beendet, nachdem sie 24 Tage lang ausgeführt wurde. In den Protokolldateien werden keine Fehler angezeigt.

## <a name="cause"></a>Ursache

Der Wert von `livy.server.session.timeout` steuert, wie lange Apache Livy warten soll, bis eine Sitzung abgeschlossen wird. Wenn die Sitzungslänge den `session.timeout`-Wert erreicht hat, werden die Livy-Sitzung und die Anwendung automatisch beendet.

## <a name="resolution"></a>Lösung

Erhöhen Sie für zeitintensive Aufträge den Wert `livy.server.session.timeout` auf der Ambari-Benutzeroberfläche. Mithilfe der URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` können Sie über die Ambari-Benutzeroberfläche auf die Livy-Konfiguration zugreifen.

Ersetzen Sie `<yourclustername>` durch den Namen Ihres HDInsight-Clusters, wie er im Portal angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]