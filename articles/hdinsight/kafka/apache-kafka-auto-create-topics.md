---
title: Aktivieren der automatischen Themenerstellung in Apache Kafka – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Kafka in HDInsight für die automatische Erstellung von Themen konfigurieren. Sie können Kafka konfigurieren, indem Sie „auto.create.topics.enable“ über Ambari oder während der Clustererstellung über PowerShell oder Resource Manager-Vorlagen auf „true“ festlegen.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: af26bcee08ded8eb66d640f954113be3e7672e1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709133"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Konfigurieren von Apache Kafka in HDInsight für die automatische Erstellung von Themen

Standardmäßig aktiviert [Apache Kafka](https://kafka.apache.org/) in HDInsight die automatische Themenerstellung nicht. Sie können die automatische Themenerstellung für vorhandene Cluster mithilfe von [Apache Ambari](https://ambari.apache.org/) aktivieren. Sie können die automatische Themenerstellung auch aktivieren, wenn Sie mithilfe einer Azure Resource Manager-Vorlage einen neuen Kafka-Cluster erstellen.

## <a name="apache-ambari-web-ui"></a>Apache Ambari-Webbenutzeroberfläche

Um die automatische Themenerstellung in einem vorhandenen Cluster über die Ambari-Web-UI zu aktivieren, führen Sie folgende Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) den Kafka-Cluster aus.

2. Klicken Sie in der __Clusterübersicht__ auf __Clusterdashboard__. 

    ![Abbildung: Portal mit ausgewähltem Clusterdashboard](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Wählen Sie dann __HDInsight-Clusterdashboard__. Bei Aufforderung authentifizieren Sie sich mit den Anmeldeinformationen (Admin) für den Cluster.

    ![Abbildung: Eintrag des HDInsight-Clusterdashboards](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Wählen Sie auf der linken Seite aus der Liste den Kafka-Dienst aus.

    ![Liste der Dienste](./media/apache-kafka-auto-create-topics/service-list.png)

4. Klicken Sie in der Mitte der Seite auf „Konfigurationen“.

    ![Registerkarte zur Dienstkonfiguration](./media/apache-kafka-auto-create-topics/service-config.png)

5. Geben Sie in das Feld „Filter“ den Wert `auto.create` ein. 

    ![Abbildung: Filter-Dialogfeld](./media/apache-kafka-auto-create-topics/filter.png)

    Dies filtert die Liste der Eigenschaften und zeigt die Einstellung `auto.create.topics.enable`.

6. Ändern Sie den Wert von `auto.create.topics.enable` zu `true`, und klicken Sie dann auf „Speichern“. Fügen Sie einen Hinweis hinzu, und klicken Sie dann erneut auf „Speichern“.

    ![Abbildung: Eintrag „auto.create.topics.enable“](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Wählen Sie nacheinander den Kafka-Dienst, die Option __Neu starten__ und die Option __Neustart aller betroffenen__ aus. Klicken Sie bei entsprechender Aufforderung auf __Neustart aller Dienste bestätigen__.

    ![Abbildung: Auswahl des Neustarts](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Sie können Ambari-Werte auch über die Ambari-REST-API festlegen. Dies ist im Allgemeinen schwieriger, da Sie mehrere REST-Aufrufe ausführen müssen, um die aktuelle Konfiguration abzurufen, sie bearbeiten usw. Weitere Informationen finden Sie im Dokument [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

Wenn Sie einen Kafka-Cluster mithilfe einer Azure Resource Manager-Vorlage erstellen, können Sie `auto.create.topics.enable` durch Hinzufügen in einem `kafka-broker` direkt festlegen. Der folgende JSON-Codeausschnitt zeigt, wie dieser Wert auf `true` festgelegt wird:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie die automatische Themenerstellung für Apache Kafka in HDInsight aktivieren. Weitere Informationen zur Arbeit mit Kafka finden Sie unter den folgenden Links:

* [Analysieren von Apache Kafka-Protokollen](apache-kafka-log-analytics-operations-management.md)
* [Replizieren von Daten zwischen Apache Kafka-Clustern](apache-kafka-mirroring.md)
