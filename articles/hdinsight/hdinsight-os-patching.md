---
title: Konfigurieren des Zeitplans für das Patchen des Betriebssystems für Linux-basierte HDInsight-Cluster – Azure
description: Erfahren Sie, wie der Zeitplan für das Patchen des Betriebssystems für Linux-basierte HDInsight-Cluster konfiguriert wird.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244406"
---
# <a name="os-patching-for-hdinsight"></a>Patchen des Betriebssystems für HDInsight 

> [!IMPORTANT]
> Ubuntu-Images stehen für die Erstellung neuer HDInsight-Cluster innerhalb von 3 Monaten nach der Veröffentlichung zur Verfügung. Seit Januar 2019 werden laufende Cluster **nicht** automatisch gepatcht. Kunden müssen Skriptaktionen oder andere Mechanismen verwenden, um einen laufenden Cluster zu patchen. Neu erstellte Cluster verfügen immer über die aktuellen verfügbaren Updates, einschließlich der neuesten Sicherheitspatches.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurieren des Zeitplans für das Patchen des Betriebssystems für Linux-basierte HDInsight-Cluster
Die virtuellen Computer in einem HDInsight-Cluster müssen gelegentlich neu gestartet werden, damit wichtige Sicherheitspatches installiert werden können. 

Mithilfe der in diesem Artikel beschriebenen Skriptaktion können Sie den Zeitplan für Patches des Betriebssystems in folgender Weise ändern:
1. Installieren vollständiger Betriebssystemupdates oder ausschließliches Installieren von Sicherheitsupdates
2. Neustarten des virtuellen Computers

> [!NOTE]  
> Diese Skriptaktion funktioniert nur mit Linux-basierten HDInsight-Clustern, die nach dem 1. August 2016 erstellt wurden. Patches werden nur wirksam, wenn die virtuellen Computer neu gestartet werden. Dieses Skript wendet nicht automatisch Updates für alle zukünftigen Updatezyklen an. Führen Sie das Skript immer dann aus, wenn neue Updates angewendet werden müssen, damit die Updates installiert werden und der virtuelle Computer neu gestartet wird.

## <a name="how-to-use-the-script"></a>Verwendung des Skripts 

Zur Verwendung des Skripts benötigen Sie die folgenden Informationen:
1. Skriptspeicherort: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh  HDInsight verwendet diesen URI zum Suchen und Ausführen des Skripts auf allen virtuellen Computern im Cluster.
  
2. Clusterknotentypen, auf die das Skript angewendet wird: Hauptknoten, Arbeitsknoten, Zookeeper. Dieses Skript muss auf alle Knotentypen im Cluster angewendet werden. Wenn es auf einen Knotentyp nicht angewendet wird, werden die virtuellen Computer für den betreffenden Knotentyp nicht aktualisiert.


3.  Parameter: Dieses Skript akzeptiert einen numerischen Parameter:

    | Parameter | Definition |
    | --- | --- |
    | Installieren vollständiger Betriebssystemupdates/Ausschließliches Installieren von Sicherheitsupdates |0 oder 1. Beim Wert 0 werden nur Sicherheitsupdates installiert, während beim Wert 1 vollständige Betriebssystemupdates installiert werden. Wenn kein Parameter angegeben wird, ist der Standardwert 0. |

> [!NOTE]  
> Sie müssen dieses Skript als permanent kennzeichnen, wenn Sie es auf einen vorhandenen Cluster anwenden. Andernfalls verwenden durch Skalierungsvorgänge erstellte neue Knoten den Standardpatchzeitplan.  Wenn Sie das Skript im Rahmen der Clustererstellung anwenden, ist es automatisch permanent.


## <a name="next-steps"></a>Nächste Schritte

Spezielle Schritte zur Verwendung der Skriptaktion finden Sie in den folgenden Abschnitten in [Anpassen von Linux-basierten HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md):

* [Verwenden einer Skriptaktion während der Clustererstellung](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Anwenden einer Skriptaktion auf einen ausgeführten Cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
