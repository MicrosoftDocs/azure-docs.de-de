---
title: Anfordern einer Erhöhung des Kontingents für CPU-Kerne – Azure HDInsight
description: Erfahren Sie, wie Sie eine Erhöhung für die Ihrem Abonnement zugewiesenen CPU-Kerne anfordern können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 93c07b47e03bde20d494feb1c86179cd9a24ba26
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539410"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Anfordern einer Erhöhung von Kontingenten für Azure HDInsight

Das Kontingent für CPU-Kerne trägt dazu bei, dass die Ressourcenauslastung gerecht zwischen allen Kunden in einer bestimmten Azure-Region verteilt wird. In bestimmten Fällen kann es jedoch sein, dass Ihre Geschäftsanforderungen mehr Clusterressourcen erfordern, als Ihr aktuelles Kontingent zulässt. In solchen Fällen können Sie eine Erhöhung des Kontingents für CPU-Kerne anfordern, sodass Sie Cluster bereitstellen können, die Ihren Anforderungen an die Datenverarbeitung entsprechen.

Wenn Sie eine Kontingentgrenze erreichen, können Sie möglicherweise nicht neue Cluster bereitstellen oder vorhandene Cluster durch Hinzufügen weiterer Workerknoten aufskalieren. Die einzige Kontingentgrenze ist das Kontingent für CPU-Kerne, das auf Regionsebene für jedes Abonnement vorhanden ist. Beispielsweise kann Ihr Abonnement in der Region „USA, Osten“ ein Limit von 30 CPU-Kernen aufweisen, während in der Region „USA, Osten“ weitere 30 CPU-Kerne zulässig sind.

## <a name="gather-required-information"></a>Sammeln erforderlicher Informationen

Wenn Sie einen Fehler erhalten haben, der anzeigt, dass Sie eine Kontingentsgrenze erreicht haben, verwenden Sie das in diesem Abschnitt beschriebene Verfahren, um wichtige Informationen zu sammeln und eine Anforderung zur Kontingenterhöhung zu übermitteln.

1. Bestimmen Sie die gewünschte Größe, Skalierung und den Typ für Ihren virtuellen Clustercomputer.
1. Überprüfen Sie die aktuellen Kontingentkapazitätsgrenzen Ihres Abonnements. Führen Sie die folgenden Schritte aus, um die verfügbaren Kerne zu überprüfen:

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
    1. Navigieren Sie zur Seite **Übersicht** für den HDInsight-Cluster.
    1. Wählen Sie im linken Menü **Kontingentgrenzen** aus. Auf der Seite werden die Anzahl der verwendeten Kerne, die Anzahl der verfügbaren Kerne und die Gesamtanzahl der Kerne angezeigt.

Gehen Sie wie folgt vor, um eine Kontingenterhöhung anzufordern:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie unten links auf der Seite **Hilfe und Support** aus.

    ![Schaltfläche „Hilfe und Support“](./media/quota-increase-request/help-support-button.png)

1. Wählen Sie **Neue Supportanfrage** aus.
1. Wählen Sie auf der Seite **Neue Supportanfrage** unter der Registerkarte **Grundlagen** die folgenden Optionen aus:

   - **Problemtyp** : **Grenzwerte für Dienste und Abonnements (Kontingente)**
   - **Abonnement** : Das zu ändernde Abonnement.
   - **Kontingenttyp** : **HDInsight**

     ![Erstellen einer Supportanfrage zur Erhöhung des HDInsight-Kernkontingents](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Klicken Sie auf **Weiter: Lösungen >>** .
1. Geben Sie auf der Seite **Details** eine Beschreibung des Problems ein, wählen Sie den Schweregrad des Problems, Ihre bevorzugte Kontaktmethode und weitere Pflichtfelder aus. Verwenden Sie die unten aufgeführte Vorlage, um sicherzustellen, dass Sie die erforderlichen Informationen bereitstellen. Anforderungen zur Kontingenterhöhung werden durch das Azure-Kapazitätsteam und nicht durch das HDInsight-Produktteam ausgewertet. Je vollständiger Ihre Angaben sind, desto wahrscheinlicher ist es, dass Ihre Anforderung genehmigt wird.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![Problemdetails](./media/quota-increase-request/problem-details.png)

1. Klicken Sie auf **Weiter: Bewerten + erstellen >>** .
1. Wählen Sie auf der Registerkarte **Überprüfen + erstellen** die Option **Erstellen** aus.

> [!NOTE]  
> Wenn Sie das HDInsight-Kernkontingent in einer privaten Region erhöhen müssen, [senden Sie eine Anforderung für eine genehmigte Liste](https://aka.ms/canaryintwhitelist).

Sie können [sich an den Support wenden, um eine Erhöhung von Kernkontingenten anzufordern](../azure-portal/supportability/resource-manager-core-quotas-request.md).

Es gibt einige feste Kontingentgrenzen. Ein einzelnes Azure-Abonnement kann z. B. höchstens 10.000 Kerne aufweisen. Informationen über diese Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von Clustern in HDInsight mit Apache Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md): Erfahren Sie, wie Cluster in HDInsight eingerichtet und konfiguriert werden können.
* [Überwachen der Clusterleistung](hdinsight-key-scenarios-to-monitor.md): Lernen Sie wichtige Szenarien zur Überwachung des HDInsight-Clusters kennen, die sich auf die Kapazität des Clusters auswirken können.