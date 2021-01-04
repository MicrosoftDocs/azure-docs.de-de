---
title: Anmeldung beim Azure HDInsight-Cluster nicht möglich
description: Problembehandlung, wenn das Anmelden beim Apache Hadoop-Cluster in Azure HDInsight nicht möglich ist
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289006"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Szenario: Anmeldung beim Azure HDInsight-Cluster nicht möglich

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Anmeldung beim Azure HDInsight-Cluster nicht möglich.

## <a name="cause"></a>Ursache

Die Gründe können variieren. Verwenden Sie für die Anmeldung beim Cluster oder bei den App-Dashboards Ihre „Clusteranmeldedaten“ oder HTTP-Anmeldeinformationen. Bei einer Remoteverbindung verwenden Sie Ihre Secure Shell (SSH)- oder Remotedesktop-Anmeldeinformationen.

## <a name="resolution"></a>Lösung

Probieren Sie zum Beheben allgemeiner Probleme die folgenden Schritte aus:

* Versuchen Sie, das Clusterdashboard auf einer neuen Browserregisterkarte im Datenschutzmodus zu öffnen.

* Falls Sie sich nicht mehr an Ihre SSH-Anmeldeinformationen erinnern, können Sie die [Anmeldeinformationen auf der Ambari-Benutzeroberfläche zurücksetzen](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]