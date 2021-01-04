---
title: Die Apache Ambari-Tez-Ansicht wird in Azure HDInsight langsam geladen
description: Die Apache Ambari-Tez-Ansicht wird möglicherweise langsam oder gar nicht in Azure HDInsight geladen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288849"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Szenario: Die Apache Ambari-Tez-Ansicht wird in Azure HDInsight langsam geladen

Dieser Artikel beschreibt Schritte zur Problembehandlung sowie mögliche Lösungen für Probleme bei der Verwendung von Interactive Query-Komponenten in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

Die Apache Ambari-Tez-Ansicht wird möglicherweise langsam oder gar nicht geladen. Beim Laden der Ambari-Tez-Ansicht sehen Sie möglicherweise, dass Prozesse auf Hauptknoten nicht mehr reagieren.

## <a name="cause"></a>Ursache

Der Zugriff auf Yarn ATS-APIs kann manchmal zu einer schlechten Leistung bei vor dem Oktober 2017 erstellten Clustern führen, wenn auf dem Cluster eine große Anzahl von Hive-Aufträgen ausgeführt wird.

## <a name="resolution"></a>Lösung

Dieses Problem wurde im Oktober 2017 behoben. Wenn Sie Ihren Cluster neu erstellen, wird dieses Problem nicht mehr auftreten.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]