---
title: HBase-Clusterreplikation in virtuellen Netzwerken – Azure HDInsight
description: Erfahren Sie, wie Sie die HBase-Replikation zwischen HDInsight-Versionen für Lastenausgleich, Hochverfügbarkeit, Migration und Updates ohne Ausfallzeit und Notfallwiederherstellung einrichten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 8fc5ba2280b5ad68a40f4992adc170408e80e5a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021791"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Einrichten der Apache HBase-Clusterreplikation in virtuellen Azure-Netzwerken

Hier erfahren Sie, wie Sie die [Apache HBase](https://hbase.apache.org/)-Replikation innerhalb eines virtuellen Netzwerks oder zwischen zwei virtuellen Netzwerken in Azure einrichten.

Clusterreplikation verwendet eine Source-Push-Methodologie. Ein HBase-Cluster kann Quelle oder Ziel sein oder beide Rollen gleichzeitig erfüllen. Die Replikation ist asynchron. Das Ziel der Replikation ist die endgültige Konsistenz. Die Bearbeitung einer Spaltenfamilie in der Quelle bei aktivierter Replikation wird an alle Zielcluster weitergegeben. Wenn Daten von einem Cluster auf einen anderen repliziert werden, werden der Quellcluster und alle Cluster, die bereits Daten genutzt haben, nachverfolgt, um Replikationsschleifen zu verhindern.

In diesem Artikel richten Sie eine Quell-/Zielreplikation ein. Andere Clustertopologien finden Sie im [Apache HBase-Referenzhandbuch](https://hbase.apache.org/book.html#_cluster_replication).

Anwendungsfälle für die HBase-Replikation für ein einzelnes virtuelles Netzwerk sind:

* Lastenausgleich. Sie können z.B. Scans oder MapReduce-Aufträge im Zielcluster ausführen und Daten im Quellcluster erfassen.
* Hinzufügen von Hochverfügbarkeit.
* Migrieren von Daten zwischen HBase-Clustern.
* Aktualisieren eines Azure HDInsight-Clusters von einer Version auf eine andere.

Anwendungsfälle für die HBase-Replikation für zwei virtuelle Netzwerks sind:

* Einrichten der Notfallwiederherstellung.
* Lastenausgleich und Partitionierung der Anwendung.
* Hinzufügen von Hochverfügbarkeit.

Sie können Cluster mit [Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md)-Skripts aus [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) replizieren.

## <a name="prerequisites"></a>Voraussetzungen
Für diesen Artikel benötigen Sie ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (So erhalten Sie eine kostenlose Azure-Testversion zum Testen von Hadoop in HDInsight).

## <a name="set-up-the-environments"></a>Einrichten der Umgebungen

Es gibt drei Konfigurationsoptionen:

- Zwei Apache HBase-Cluster in einem virtuellen Azure-Netzwerk.
- Zwei Apache HBase-Cluster in zwei verschiedenen virtuellen Netzwerken in der gleichen Region.
- Zwei Apache HBase-Cluster in zwei verschiedenen virtuellen Netzwerken in zwei verschiedenen Regionen (Georeplikation).

In diesem Artikel wir das Szenario der Georeplikation behandelt.

Um Ihnen die Einrichtung der Umgebungen zu erleichtern, haben wir einige [Azure Resource Manager-Vorlagen](../../azure-resource-manager/management/overview.md) erstellt. Wenn Sie die Umgebungen mit anderen Methoden einrichten möchten, finden Sie hier weitere Informationen:

- [Erstellen von Apache Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Erstellen von Apache HBase-Clustern in Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Einrichten von zwei virtuellen Netzwerken in zwei verschiedenen Regionen

Um eine Vorlage zu verwenden, die zwei virtuelle Netzwerke in zwei verschiedenen Regionen und die VPN-Verbindung zwischen den VNets zu erstellen, wählen Sie die folgende Schaltfläche **In Azure bereitstellen** aus. Die Vorlagendefinition befindet sich in einem [öffentlichen Blob-Speicher](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Im Folgenden werden einige der hartcodierten Werte in der Vorlage aufgeführt:

**VNet 1**

| Eigenschaft | value |
|----------|-------|
| Location | USA (Westen) |
| VNet-Name | &lt;ClusterNamePrevix>-vnet1 |
| Adressraumpräfix | 10.1.0.0/16 |
| Subnetzname | Subnetz 1 |
| Subnetzpräfix | 10.1.0.0/24 |
| Subnetzname (Gateway) | GatewaySubnet (kann nicht geändert werden) |
| Subnetzpräfix (Gateway) | 10.1.255.0/27 |
| Gatewayname | vnet1gw |
| Gatewaytyp | VPN |
| Gateway-VPN-Typ | RouteBased |
| Gateway-SKU | Basic |
| Gateway-IP | vnet1gwip |

**VNet 2**

| Eigenschaft | value |
|----------|-------|
| Location | East US |
| VNet-Name | &lt;ClusterNamePrevix>-vnet2 |
| Adressraumpräfix | 10.2.0.0/16 |
| Subnetzname | Subnetz 1 |
| Subnetzpräfix | 10.2.0.0/24 |
| Subnetzname (Gateway) | GatewaySubnet (kann nicht geändert werden) |
| Subnetzpräfix (Gateway) | 10.2.255.0/27 |
| Gatewayname | vnet2gw |
| Gatewaytyp | VPN |
| Gateway-VPN-Typ | RouteBased |
| Gateway-SKU | Basic |
| Gateway-IP | vnet1gwip |

## <a name="setup-dns"></a>Einrichten von DNS

Im letzten Abschnitt wird anhand der Vorlage ein virtueller Ubuntu-Computer in jedem der beiden virtuellen Netzwerke erstellt.  In diesem Abschnitt installieren Sie Bind auf den beiden virtuellen DNS-Computern und konfigurieren dann die DNS-Weiterleitung auf den beiden virtuellen Computern.

Um Bind zu installieren, müssen Sie die öffentliche IP-Adresse der beiden virtuellen DNS-Computer finden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Öffnen Sie den virtuellen DNS-Computer, indem Sie **Ressourcengruppen > [Ressourcengruppenname] > [vnet1DNS]** wählen.  Der Ressourcengruppenname ist derjenige, den Sie in der vorherigen Prozedur erstellt haben. Die Standardnamen der DNS-VMs lauten *vnet1DNS* und *vnet2DNS*.
3. Wählen Sie **Eigenschaften** aus, um die Eigenschaftenseite des virtuellen Netzwerks zu öffnen.
4. Notieren Sie sich die **öffentliche IP-Adresse**, und überprüfen Sie außerdem die **private IP-Adresse**.  Die private IP-Adresse soll **10.1.0.4** für vnet1DNS und **10.2.0.4** für vnet2DNS sein.  
5. Ändern Sie die DNS-Server für beide virtuelle Netzwerke in die Standard-DNS-Server (von Azure bereitgestellt), um eingehenden und ausgehenden Zugriff auf Downloadpakete zum Installieren von Bind in den folgenden Schritten zuzulassen.

Gehen Sie zur Installation von Bind wie folgt vor:

1. Verwenden Sie SSH, um eine Verbindung mit der __öffentlichen IP-Adresse__ des virtuellen DNS-Computers herzustellen. Im folgenden Beispiel wird über die IP-Adresse 40.68.254.142 eine Verbindung mit dem virtuellen Computer hergestellt:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersetzen Sie `sshuser` durch das SSH-Benutzerkonto, das beim Erstellen des virtuellen DNS-Computers angegeben wurde.

    > [!NOTE]  
    > Das Hilfsprogramm `ssh` kann auf verschiedene Weise erworben werden. Unter Linux, Unix und macOS gehört es zum Lieferumfang des Betriebssystems. Wenn Sie Windows verwenden, erwägen Sie eine der folgenden Optionen:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash auf Ubuntu unter Windows 10](/windows/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Verwenden Sie in der SSH-Sitzung die folgenden Befehle, um Bind zu installieren:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurieren Sie Bind, um Namensauflösungsanforderungen an Ihren lokalen DNS-Server weiterzuleiten. Verwenden Sie hierfür als Inhalt der Datei `/etc/bind/named.conf.options` den folgenden Text:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Ersetzen Sie die Werte im Abschnitt `goodclients` durch den IP-Adressbereich der zwei virtuellen Netzwerke. In diesem Abschnitt werden die Adressen definiert, von denen dieser DNS-Server Anforderungen akzeptiert.

    Verwenden Sie zum Bearbeiten dieser Datei den folgenden Befehl:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Zum Speichern der Datei drücken Sie __STRG+X__, __Y__ und dann die __EINGABETASTE__.

4. Verwenden Sie aus der SSH-Sitzung den folgenden Befehl:

    ```bash
    hostname -f
    ```

    Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht:

    ```output
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Der Text `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` ist das __DNS-Suffix__ für dieses virtuelle Netzwerk. Speichern Sie diesen Wert, da er später wieder verwendet wird.

    Zudem müssen Sie das DNS-Suffix von dem anderen DNS-Server ermitteln. Sie benötigen sie im nächsten Schritt.

5. Um Bind zum Auflösen von DNS-Namen für Ressourcen innerhalb des virtuellen Netzwerks zu konfigurieren, verwenden Sie den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.local`:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Sie müssen den Wert `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` durch das DNS-Suffix des anderen virtuellen Netzwerks ersetzen. Und die Weiterleitungs-IP-Adresse ist die private IP-Adresse des DNS-Servers im anderen virtuellen Netzwerk.

    Verwenden Sie zum Bearbeiten dieser Datei den folgenden Befehl:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Zum Speichern der Datei drücken Sie __STRG+X__, __Y__ und dann die __EINGABETASTE__.

6. Verwenden Sie den folgenden Befehl, um Bind zu starten:

    ```bash
    sudo service bind9 restart
    ```

7. Um zu überprüfen, ob Bind die Namen der Ressourcen im anderen virtuellen Netzwerk auflösen kann, verwenden Sie die folgenden Befehle:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Ersetzen Sie `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` mit dem vollqualifizierten Domänennamen (FQDN) des virtuellen DNS-Computers im anderen Netzwerk.
    >
    > Ersetzen Sie `10.2.0.4` durch die __interne IP-Adresse__ Ihres benutzerdefinierten DNS-Servers im anderen virtuellen Netzwerk.

    Die Ausgabe ähnelt dem folgenden Text:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Bisher können Sie die IP-Adresse des anderen Netzwerks nicht ohne die angegebene DNS-Server-IP-Adresse abfragen.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurieren des virtuellen Netzwerks zur Verwendung des benutzerdefinierten DNS-Servers

Führen Sie die folgenden Schritte aus, damit das virtuelle Netzwerk anstelle des rekursiven Azure-Resolvers den benutzerdefinierten DNS-Server verwendet:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) das virtuelle Netzwerk aus, und klicken Sie dann auf __DNS-Server__.

2. Wählen Sie __Benutzerdefiniert__ aus, und geben Sie die __interne IP-Adresse__ des benutzerdefinierten DNS-Servers ein. Klicken Sie abschließend auf __Speichern__.

6. Öffnen Sie den virtuellen DNS-Servercomputer in vnet1, und klicken Sie auf **Neustart**.  Sie müssen alle virtuellen Computer im virtuellen Netzwerk neu starten, damit die DNS-Konfiguration übernommen wird.
7. Wiederholen Sie die Schritte zur Konfiguration des benutzerdefinierten DNS-Servers für vnet2.

Um die DNS-Konfiguration zu testen, können Sie eine Verbindung zu den beiden virtuellen DNS-Computern über SSH herstellen und den DNS-Server des anderen virtuellen Netzwerks über dessen Hostnamen pingen. Wenn es nicht funktioniert, verwenden Sie den folgenden Befehl, um den DNS-Status zu überprüfen:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Erstellen von Apache HBase-Clustern

Erstellen Sie in jedem der beiden virtuellen Netzwerke einen [Apache HBase](https://hbase.apache.org/)-Cluster mit folgender Konfiguration:

- **Name der Ressourcengruppe**: Verwenden Sie den gleichen Ressourcengruppennamen, wie beim Erstellen der virtuellen Netzwerke.
- **Clustertyp**: HBase
- **Version**: HBase 1.1.2 (HDI 3.6)
- **Standort**: Verwenden Sie denselben Standort wie für das virtuelle Netzwerk.  Standardmäßig ist vnet1 *USA, Westen* und vnet2 ist *USA, Osten*.
- **Speicher**: Erstellen Sie ein neues Speicherkonto für den Cluster.
- **Virtuelles Netzwerk** (aus den erweiterten Einstellungen im Portal): Wählen Sie vnet1, das Sie in der vorherigen Prozedur erstellt haben.
- **Subnetz**: Der in der Vorlage verwendete Standardname lautet **subnet1**.

Um sicherzustellen, dass die Umgebung korrekt konfiguriert ist, müssen Sie den FQDN des Hauptknotens zwischen den beiden Clustern pingen können.

## <a name="load-test-data"></a>Laden von Testdaten

Wenn Sie einen Cluster replizieren, müssen Sie die Tabellen angeben, die Sie replizieren möchten. In diesem Abschnitt laden Sie einige Daten in den Quellcluster. Im nächsten Abschnitt aktivieren Sie die Replikation zwischen den beiden Clustern.

Wenn Sie eine Tabelle namens [Kontakte](apache-hbase-tutorial-get-started-linux.md) erstellen und einige Daten in die Tabelle einfügen möchten, befolgen Sie die Anweisungen unter **Apache HBase-Tutorial: Erste Schritte mit Apache HBase in HDInsight**.

> [!NOTE]
> Wenn Sie Tabellen aus einem benutzerdefinierten Namespace replizieren möchten, müssen Sie sicherstellen, dass auch die entsprechenden benutzerdefinierten Namespaces auch auf dem Zielcluster definiert sind.
>

## <a name="enable-replication"></a>Aktivieren der Replikation

Die folgenden Schritte zeigen, wie Sie das Skript mit Skriptaktionen aus dem Azure-Portal aufrufen. Informationen zum Ausführen einer Skriptaktion mithilfe von Azure PowerShell und der klassischen Azure CLI finden Sie unter [Anpassen von Linux-basierten HDInsight-Clustern mithilfe von Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md).

**So aktivieren Sie die HBase-Replikation über das Azure-Portal**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Öffnen Sie den HBase-Quellcluster.
3. Wählen Sie im Clustermenü **Skriptaktionen**.
4. Wählen Sie am oberen Rand der Seite **Neue übermitteln** aus.
5. Wählen Sie folgende Informationen aus, oder geben Sie sie ein:

   1. **Name**: Geben Sie **Replikation aktivieren** ein.
   2. **Bashskript-URL**: Geben Sie **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** ein.
   3. **Hauptknoten**: Stellen Sie sicher, dass diese Option aktiviert ist. Deaktivieren Sie die anderen Knotentypen.
   4. **Parameter**: Die folgenden Beispielparameter aktivieren die Replikation für alle vorhandenen Tabellen und kopieren dann alle Daten aus dem Quellcluster in den Zielcluster:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Verwenden Sie für den DNS-Namen des Quell- und des Zielclusters jeweils den Hostnamen anstelle des FQDN.
      >
      > In dieser exemplarischen Vorgehensweise wird hn1 als aktiver Hauptknoten angenommen. Überprüfen Sie den Cluster, um den aktiven Hauptknoten zu identifizieren.

6. Klicken Sie auf **Erstellen**. Die Ausführung des Skripts kann einige Zeit in Anspruch nehmen, insbesondere dann, wenn Sie das Argument **-copydata** verwenden.

Erforderliche Argumente:

|Name|BESCHREIBUNG|
|----|-----------|
|-s, --src-cluster | Gibt den DNS-Namen des HBase-Quellclusters an. Beispiel: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Gibt den DNS-Namen des HBase-Zielclusters (Replikats) an. Beispiel: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Gibt das Administratorkennwort für Ambari im HBase-Quellcluster an. |
|-dp, --dst-ambari-password | Gibt das Administratorkennwort für Ambari im HBase-Zielcluster an.|

Optionale Argumente:

|Name|BESCHREIBUNG|
|----|-----------|
|-su, --src-ambari-user | Gibt den Administratorbenutzernamen für Ambari im HBase-Quellcluster an. Der Standardwert lautet **admin**. |
|-du, --dst-ambari-user | Gibt den Administratorbenutzernamen für Ambari im HBase-Zielcluster an. Der Standardwert lautet **admin**. |
|-t, --table-list | Gibt die zu replizierenden Tabellen an. Beispiel: --table-list="table1;table2;table3". Wenn Sie keine Tabellen angeben, werden alle vorhandenen HBase-Tabellen repliziert.|
|-m, --machine | Gibt den Hauptknoten an, auf dem die Skriptaktion ausgeführt werden soll. Der Wert muss basierend auf dem aktiven Hauptknoten ausgewählt werden. Verwenden Sie diese Option, wenn Sie das $0-Skript als Skriptaktion im HDInsight-Portal oder in Azure PowerShell ausführen.|
|-cp, -copydata | Aktiviert die Migration vorhandener Daten in den Tabellen, in denen die Replikation aktiviert ist. |
|-rpm, -replicate-phoenix-meta | Aktiviert die Replikation in Phoenix-Systemtabellen. <br><br>*Verwenden Sie diese Option mit Vorsicht.* Es wird empfohlen, die Phoenix-Tabellen in Replikatclustern neu zu erstellen, bevor Sie dieses Skript verwenden. |
|-h, --help | Zeigt Informationen zur Nutzung an. |

Der Abschnitt `print_usage()` des [Skripts](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) bietet eine detaillierte Erläuterung der Parameter.

Nachdem die Skriptaktion erfolgreich bereitgestellt wurde, können Sie über SSH eine Verbindung mit dem HBase-Zielcluster herstellen und dann überprüfen, ob die Daten repliziert wurden.

### <a name="replication-scenarios"></a>Replikationsszenarien

Die folgende Liste zeigt einige allgemeine Anwendungsfälle und die zugehörigen Parametereinstellungen:

- **Aktivieren der Replikation in allen Tabellen zwischen den beiden Clustern**. Dieses Szenario erfordert kein Kopieren oder Migrieren der in den Tabellen vorhandenen Daten und verwendet keine Phoenix-Tabellen. Verwenden Sie die folgenden Parameter:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Aktivieren der Replikation in bestimmten Tabellen**. Verwenden Sie die folgenden Parameter, um die Replikation in „table1“, „table2“ und „table3“ zu aktivieren:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Aktivieren der Replikation in bestimmten Tabellen und Kopieren der vorhandenen Daten**. Verwenden Sie die folgenden Parameter, um die Replikation in „table1“, „table2“ und „table3“ zu aktivieren:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Aktivieren der Replikation in allen Tabellen und Replikation der Phoenix-Metadaten von der Quelle zum Ziel**. Die Replikation der Phoenix-Metadaten ist nicht perfekt. Verwenden Sie sie mit Vorsicht. Verwenden Sie die folgenden Parameter:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Kopieren und Migrieren von Daten

Es gibt zwei separate Skripte mit Skriptaktionen für das Kopieren oder Migrieren von Daten, nachdem die Replikation aktiviert wurde:

- [Skript für kleine Tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (Tabellen, die wenige Gigabytes groß sind, sodass der gesamte Kopiervorgang in weniger als einer Stunde abgeschlossen sein sollte)

- [Skript für große Tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (Tabellen, deren Kopiervorgang voraussichtlich länger als eine Stunde dauert)

Sie können das gleiche Verfahren wie unter [Aktivieren der Replikation](#enable-replication) befolgen, um die Skriptaktion aufzurufen. Verwenden Sie die folgenden Parameter:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

Der Abschnitt `print_usage()` des [Skripts](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) bietet eine detaillierte Erläuterung der Parameter.

### <a name="scenarios"></a>Szenarien

- **Kopieren bestimmter Tabellen („test1“, „test2“ und „test3“) für alle bis jetzt (aktueller Zeitstempel) bearbeiteten Zeilen**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Oder:

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Kopieren bestimmter Tabellen innerhalb eines angegebenen Zeitraums**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Deaktivieren der Replikation

Um die Replikation zu deaktivieren, verwenden Sie ein weiteres Skript mit einer Skriptaction, das Sie auf [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) finden. Sie können das gleiche Verfahren wie unter [Aktivieren der Replikation](#enable-replication) befolgen, um die Skriptaktion aufzurufen. Verwenden Sie die folgenden Parameter:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

Der Abschnitt `print_usage()` des [Skripts](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) bietet eine detaillierte Erläuterung der Parameter.

### <a name="scenarios"></a>Szenarien

- **Deaktivieren der Replikation in allen Tabellen**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  oder

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Deaktivieren der Replikation in angegebenen Tabellen („table1“, „table2“ und „table3“)** :

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Wenn Sie beabsichtigen, den Zielcluster zu löschen, stellen Sie sicher, dass Sie ihn aus der Peerliste des Quellclusters entfernen. Dies können Sie erreichen, indem Sie den Befehl remove_peer '1' in der hBase-Shell auf dem Quellcluster ausführen. Wenn sie nicht so vorgehen, funktioniert der Quellcluster möglicherweise nicht ordnungsgemäß.
>

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Apache HBase-Replikation innerhalb eines virtuellen Netzwerks oder zwischen zwei virtuellen Netzwerken einrichten. Weitere Informationen zu HDInsight und Apache HBase finden Sie in den folgenden Artikeln:

* [Get started with Apache HBase in HDInsight (Erste Schritte mit Apache HBase in HDInsight)](./apache-hbase-tutorial-get-started-linux.md)
* [Überblick über Apache HBase in HDInsight: Eine NoSQL-Datenbank, die BigTable-ähnliche Funktionen für Hadoop bereitstellt](./apache-hbase-overview.md)
* [Erstellen von Apache HBase-Clustern in Azure Virtual Network](./apache-hbase-provision-vnet.md)