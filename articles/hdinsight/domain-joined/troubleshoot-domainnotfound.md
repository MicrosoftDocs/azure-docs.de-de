---
title: Fehler „DomainNotFound“ bei der Clustererstellung in Azure HDInsight
description: Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005944"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Szenario: Fehler „DomainNotFound“ bei der Clustererstellung in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Fehlermeldung `DomainNotFound` bei der Clustererstellung für HDI Secure (Enterprise-Sicherheitspaket).

## <a name="cause"></a>Ursache

Falsche DNS-Einstellungen.

## <a name="resolution"></a>Lösung

Wenn die in eine Domäne eingebundenen Cluster bereitgestellt werden, erstellt HDI einen internen Benutzernamen und ein Kennwort in AAD DS (für jeden Cluster) und bindet alle Clusterknoten in diese Domäne ein. Der Domänenbeitritt erfolgt mithilfe von Samba-Tools. Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt werden:

* Der Domänenname sollte über DNS aufgelöst werden.
* Die IP-Adresse der Domänencontroller sollte in den DNS-Einstellungen für das virtuelle Netzwerk festgelegt werden, in dem der Cluster bereitgestellt wird.
* Wenn das virtuelle Netzwerk per Peering mit dem virtuellen Netzwerk von AAD DS verknüpft ist, muss dies manuell ausgeführt werden.
* Wenn Sie DNS-Weiterleitungen verwenden, muss der Domänenname innerhalb des virtuellen Netzwerks korrekt aufgelöst werden.
* Der Domänenbeitritt sollte nicht durch Sicherheitsrichtlinien (NSGs) blockiert werden.

### <a name="additional-debugging-steps"></a>Weitere Debuggingschritte

* Bereitstellen einer Windows-VM im gleichen Subnetz und Einbinden des Computers mit einem Benutzernamen und einem Kennwort in die Domäne (dies kann über die Benutzeroberfläche der Systemsteuerung erfolgen) oder

* Bereitstellen einer Ubuntu-VM im gleichen Subnetz und Einbinden des Computers in die Domäne
  * SSH-Verbindung mit dem Computer
  * sudo su
  * Ausführen des Skripts mit Benutzername und Kennwort
  * Das Skript führt einen Ping aus, erstellt die erforderlichen Konfigurationsdateien und dann die Domäne. Wenn dies erfolgreich ausgeführt wird, sind Ihre DNS-Einstellungen in Ordnung.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]