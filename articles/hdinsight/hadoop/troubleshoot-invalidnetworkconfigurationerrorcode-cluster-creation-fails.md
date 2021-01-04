---
title: Fehler „InvalidNetworkConfigurationErrorCode“ – Azure HDInsight
description: Verschiedene Gründe für den Fehler „InvalidNetworkConfigurationErrorCode“ bei der Clustererstellung in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 0eb9afc179f1dd2559f0db7b212f6b3a1da15824
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998752"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Fehler „InvalidNetworkConfigurationErrorCode“ bei der Clustererstellung in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

Wenn der Fehlercode `InvalidNetworkConfigurationErrorCode` mit der Beschreibung „Die Konfiguration des virtuellen Netzwerks ist nicht mit der HDInsight-Anforderung kompatibel“ angezeigt wird, ist dies normalerweise ein Hinweis auf ein Problem mit der [Konfiguration des virtuellen Netzwerks](../hdinsight-plan-virtual-network-deployment.md) für Ihren Cluster. Führen Sie basierend auf der restlichen Fehlerbeschreibung die Schritte in den unten angegebenen Abschnitten aus.

## <a name="hostname-resolution-failed"></a>„Fehler beim Auflösen des Hostnamens“

### <a name="issue"></a>Problem

Fehlerbeschreibung enthält „Fehler beim Auflösen des Hostnamens“.

### <a name="cause"></a>Ursache

Dieser Fehler ist ein Hinweis auf ein Problem mit der benutzerdefinierten DNS-Konfiguration. DNS-Server innerhalb eines virtuellen Netzwerks können DNS-Abfragen an die rekursiven Resolver von Azure weiterleiten, um Hostnamen innerhalb dieses virtuellen Netzwerks aufzulösen (ausführlichere Informationen unter [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)). Der Zugriff auf die rekursiven Resolver von Azure wird über die virtuelle IP 168.63.129.16 bereitgestellt. Auf diese IP kann nur von den Azure-VMs aus zugegriffen werden. Sie funktioniert also nicht, wenn Sie einen lokalen DNS-Server nutzen oder Ihr DNS-Server eine Azure-VM ist, die nicht Teil des virtuellen Netzwerks des Clusters ist.

### <a name="resolution"></a>Lösung

1. Stellen Sie eine SSH-Verbindung mit der VM her, die Teil des Clusters ist, und führen Sie den Befehl `hostname -f` aus. Damit wird der vollqualifizierte Domänenname des Hosts (in der Anleitung unten als `<host_fqdn>` bezeichnet) zurückgegeben.

1. Führen Sie anschließend den Befehl `nslookup <host_fqdn>` aus (z. B. `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Wenn dieser Befehl den Namen in eine IP-Adresse auflöst, bedeutet dies, dass Ihr DNS-Server richtig funktioniert. Erstellen Sie in diesem Fall eine Supportanfrage für HDInsight, damit wir das Problem untersuchen können. Fügen Sie der Supportanfrage die Schritte zur Problembehandlung hinzu, die Sie ausgeführt haben. Wir können das Problem dann schneller beheben.

1. Wenn der obige Befehl keine IP-Adresse zurückgibt, führen Sie `nslookup <host_fqdn> 168.63.129.16` aus (z. B. `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Wenn dieser Befehl die IP-Adresse auflösen kann, leitet entweder Ihr DNS-Server die Abfrage nicht an das DNS von Azure weiter, oder es handelt sich nicht um eine VM im selben virtuellen Netzwerk wie der Cluster.

1. Wenn Sie nicht über eine Azure-VM verfügen, die als benutzerdefinierter DNS-Server im virtuellen Netzwerk des Clusters agieren kann, müssen Sie diese zuerst hinzufügen. Erstellen Sie eine VM im virtuellen Netzwerk, die als DNS-Weiterleitung konfiguriert wird.

1. Nachdem Sie eine VM in Ihrem virtuellen Netzwerk bereitgestellt haben, können Sie die Regeln für die DNS-Weiterleitung auf dieser VM konfigurieren. Leiten Sie alle Anforderungen für die iDNS-Namensauflösung an 168.63.129.16 und den Rest an Ihren DNS-Server weiter. [Hier](../hdinsight-plan-virtual-network-deployment.md) ist ein Beispiel für diese Einrichtung für einen benutzerdefinierten DNS-Server angegeben.

1. Fügen Sie die IP-Adresse dieser VM als ersten DNS-Eintrag für die DNS-Konfiguration des virtuellen Netzwerks hinzu.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>„Fehler beim Herstellen der Verbindung mit dem Azure Storage-Konto“

### <a name="issue"></a>Problem

Fehlerbeschreibung enthält: „Fehler beim Herstellen der Verbindung mit dem Azure Storage-Konto“ oder „Fehler beim Herstellen einer Verbindung mit Azure SQL“.

### <a name="cause"></a>Ursache

Da Azure Storage und SQL nicht über feste IP-Adressen verfügen, müssen wir ausgehende Verbindungen zu allen IP-Adressen zulassen, um den Zugriff auf diese Dienste zu ermöglichen. Die genauen Lösungsschritte sind davon abhängig, ob Sie eine Netzwerksicherheitsgruppe (NSG) oder benutzerdefinierte Regeln (UDRs) eingerichtet haben. Details zu diesen Konfigurationen finden Sie im Abschnitt zum Thema [Steuern des Netzwerkdatenverkehrs über HDInsight mit Netzwerksicherheitsgruppen und benutzerdefinierten Routen](../control-network-traffic.md).

### <a name="resolution"></a>Lösung

* Bei Verwendung einer [Netzwerksicherheitsgruppe (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) in Ihrem Cluster:

    Navigieren Sie zum Azure-Portal, und ermitteln Sie die NSG des Subnetzes, in dem der Cluster bereitgestellt wird. Lassen Sie im Abschnitt **Ausgangssicherheitsregeln** den Zugriff auf das Internet in ausgehender Richtung ohne Einschränkungen zu. (Beachten Sie, dass ein niedrigerer Wert unter **Priorität** hier für eine höhere Priorität steht.) Vergewissern Sie sich außerdem im Abschnitt **Subnetze**, ob diese NSG auf das Clustersubnetz angewendet wird.

* Bei Verwendung einer [benutzerdefinierten Route (UDR)](../../virtual-network/virtual-networks-udr-overview.md) in Ihrem Cluster:

    Navigieren Sie zum Azure-Portal, und ermitteln Sie die Routingtabelle des Subnetzes, in dem der Cluster bereitgestellt wird. Untersuchen Sie nach der Ermittlung der Routingtabelle für das Subnetz den darin enthaltenen Abschnitt **Routen**.

    Wenn Routen definiert sind, sollten Sie sicherstellen, dass Routen für IP-Adressen für die Region vorhanden sind, in der der Cluster bereitgestellt wurde, und dass **NextHopType** für jede Route auf **Internet** festgelegt ist. Für jede erforderliche IP-Adresse, die im obigen Artikel dokumentiert ist, muss eine Route definiert sein.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>„Failed to establish an outbound connection from the cluster for the communication with the HDInsight resource provider. Please ensure that outbound connectivity is allowed.“ (Es konnte für die Kommunikation keine ausgehende Verbindung vom Cluster mit dem HDInsight-Ressourcenanbieter hergestellt werden. Sorgen Sie dafür, dass ausgehende Konnektivität zulässig ist.)

### <a name="issue"></a>Problem

Die Fehlerbeschreibung enthält den folgenden Text: „Failed to establish an outbound connection from the cluster for the communication with the HDInsight resource provider. Please ensure that outbound connectivity is allowed.“ (Es konnte für die Kommunikation keine ausgehende Verbindung vom Cluster mit dem HDInsight-Ressourcenanbieter hergestellt werden. Sorgen Sie dafür, dass ausgehende Konnektivität zulässig ist.).

### <a name="cause"></a>Ursache

Wenn HDInsight-Cluster in Private Link verwendet werden, muss der ausgehende Zugriff vom Cluster so konfiguriert werden, dass es zulässig ist, Verbindungen zum HDInsight-Ressourcenanbieter herzustellen.

### <a name="resolution"></a>Lösung

* Informationen zur Lösung des Problems finden Sie in den Einrichtungsschritten für Private Link in HDInsight unter [Schützen und Isolieren von Azure HDInsight-Clustern mit Private Link (Vorschauversion)](../hdinsight-private-link.md).
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>Die Konfiguration des virtuellen Netzwerks ist nicht mit der HDInsight-Anforderung kompatibel.

### <a name="issue"></a>Problem

Die Fehlerbeschreibungen weisen Meldungen ähnlich der folgenden auf:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Ursache

Wahrscheinlich ein Problem mit dem benutzerdefinierten DNS-Setup.

### <a name="resolution"></a>Lösung

Vergewissern Sie sich, dass sich 168.63.129.16 in der benutzerdefinierten DNS-Kette befindet. DNS-Server innerhalb eines virtuellen Netzwerks können DNS-Abfragen an die rekursiven Resolver von Azure weiterleiten, um Hostnamen innerhalb dieses virtuellen Netzwerks aufzulösen. Weitere Informationen finden Sie unter [Namensauflösung in virtuellen Netzwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Der Zugriff auf die rekursiven Resolver von Azure wird über die virtuelle IP 168.63.129.16 bereitgestellt.

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Führen Sie den folgenden Befehl aus:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Befolgen Sie basierend auf dem Ergebnis einen der folgenden Schritte:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 ist nicht in dieser Liste enthalten.

**Option 1:**  
Fügen Sie 168.63.129.16 mithilfe der Schritte unter [Planen eines virtuellen Netzwerks für Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) als erstes benutzerdefiniertes DNS für das virtuelle Netzwerk hinzu. Diese Schritte gelten nur, wenn der benutzerdefinierte DNS-Server unter Linux ausgeführt wird.

**Option 2:**  
Stellen Sie eine DNS-Server-VM für das virtuelle Netzwerk bereit. Dieser Vorgang umfasst die folgenden Schritte:

* Erstellen Sie eine VM im virtuellen Netzwerk, die als DNS-Weiterleitung konfiguriert wird. (Dies kann eine Linux- oder Windows-VM sein.)
* Konfigurieren Sie DNS-Weiterleitungsregeln auf dieser VM. (Leiten Sie alle Anforderungen für die iDNS-Namensauflösung an 168.63.129.16 und alle anderen an Ihren DNS-Server weiter.)
* Fügen Sie die IP-Adresse dieser VM als ersten DNS-Eintrag für die DNS-Konfiguration des virtuellen Netzwerks hinzu.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 ist in der Liste enthalten.

Erstellen Sie in diesem Fall eine Supportanfrage für HDInsight, damit wir das Problem untersuchen können. Fügen Sie das Ergebnis der folgenden Befehle in den Supportfall ein. Auf diese Weise können wir das Problem schneller untersuchen und beheben.

Bearbeiten Sie in einer SSH-Sitzung auf dem Hauptknoten den folgenden Text, und führen Sie ihn dann aus:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Nutzen Sie [@AzureSupport](https://twitter.com/azuresupport) – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit. Hierüber hat die Azure-Community Zugriff auf die richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support**. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.
