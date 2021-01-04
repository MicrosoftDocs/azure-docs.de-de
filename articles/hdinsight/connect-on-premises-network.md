---
title: Verbinden von Azure HDInsight mit Ihrem lokalen Netzwerk
description: Erfahren Sie, wie Sie einen HDInsight-Cluster in einem virtuellen Azure-Netzwerk erstellen und ihn anschließend mit Ihrem lokalen Netzwerk verbinden. Sie erfahren außerdem, wie Sie unter Verwendung eines benutzerdefinierten DNS-Servers die Namensauflösung zwischen HDInsight und Ihrem lokalen Netzwerk konfigurieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 71ef902e909e552ade5174196f291630bc242ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005366"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Verbinden von HDInsight mit Ihrem lokalen Netzwerk

Erfahren Sie, wie Sie HDInsight mit Ihrem lokalen Netzwerk verbinden, indem Sie virtuelle Azure-Netzwerke und ein VPN-Gateway einsetzen. In diesem Dokument erhalten Sie Informationen zur Planung der folgenden Aufgaben:

* Verwenden von HDInsight in einem virtuellen Azure-Netzwerk mit einer Verbindung mit Ihrem lokalen Netzwerk
* Konfigurieren der DNS-Namensauflösung zwischen dem virtuellen Netzwerk und Ihrem lokalen Netzwerk
* Konfigurieren von Netzwerksicherheitsgruppen zum Einschränken des Internetzugriffs auf HDInsight
* Ports, die von HDInsight im virtuellen Netzwerk bereitgestellt werden

## <a name="overview"></a>Übersicht

Um HDInsight und den Ressourcen im angebundenen Netzwerk eine Kommunikation per Name zu ermöglichen, müssen Sie die folgenden Aufgaben ausführen:

1. Erstellen eines virtuellen Azure-Netzwerks
1. Erstellen eines benutzerdefinierten DNS-Servers im virtuellen Azure-Netzwerk
1. Konfigurieren des virtuellen Netzwerks, damit anstelle des rekursiven Azure-Resolvers der benutzerdefinierte DNS-Server verwendet wird
1. Konfigurieren der Weiterleitung zwischen dem benutzerdefinierten DNS-Server und Ihrem lokalen DNS-Server

Diese Konfigurationen ermöglichen folgendes Verhalten:

* Anforderungen für vollqualifizierte Domänennamen, die das DNS-Suffix __für das virtuelle Netzwerk__ umfassen, werden an den benutzerdefinierten DNS-Server weitergeleitet. Der benutzerdefinierte DNS-Server leitet die Anforderungen dann an den rekursiven Azure-Resolver weiter, der die IP-Adresse zurückgibt.
* Alle anderen Anforderungen werden an den lokalen DNS-Server weitergeleitet. Selbst Anforderungen für öffentliche Internetressourcen, wie z.B. microsoft.com, werden zur Namensauflösung an den lokalen DNS-Server weitergeleitet.

Im folgenden Diagramm werden Anforderungen für Ressourcen, die auf das DNS-Suffix des virtuellen Netzwerks enden, als grüne Linien dargestellt. Die blauen Linien stehen für Anforderungen von Ressourcen im lokalen Netzwerk oder dem öffentlichen Internet.

![Diagramm zur Veranschaulichung der Auflösung von DNS-Anforderungen in der Konfiguration](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Voraussetzungen

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/).
* Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Erstellen der Konfiguration des virtuellen Netzwerks

In den folgenden Dokumenten erfahren Sie, wie Sie ein virtuelles Azure-Netzwerk erstellen, das mit Ihrem lokalen Netzwerk verbunden wird:

* [Verwenden des Azure-Portals](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Verwenden von Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Verwenden der Azure-Befehlszeilenschnittstelle](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Erstellen eines benutzerdefinierten DNS-Servers

> [!IMPORTANT]  
> Sie müssen den benutzerdefinierten DNS-Server erstellen und konfigurieren, bevor Sie HDInsight im virtuellen Netzwerk installieren.

Die aufgeführten Schritte gelten für die Erstellung eines virtuellen Azure-Computers über das [Azure-Portal](https://portal.azure.com). Informationen zu anderen Möglichkeiten zum Erstellen eines virtuellen Computers finden Sie unter [Erstellen eines virtuellen Computers – Azure CLI](../virtual-machines/linux/quick-create-cli.md) und [Erstellen eines virtuellen Computers – Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Führen Sie die folgenden Schritte aus, um eine Linux-VM zu erstellen, die die DNS-Software [Bind](https://www.isc.org/downloads/bind/) verwendet:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
  
1. Klicken Sie im oberen Menü auf **+ Ressource erstellen**.

    ![Erstellen eines virtuellen Ubuntu-Computers](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Wählen Sie **Compute** > **Virtueller Computer** aus, um zur Seite **Virtuellen Computer erstellen** zu wechseln.

1. Geben Sie auf der Registerkarte __Grundeinstellungen__ die folgenden Informationen ein:  
  
    | Feld | Wert |
    | --- | --- |
    |Subscription |Wählen Sie Ihr entsprechendes Abonnement aus.|
    |Resource group |Wählen Sie die Ressourcengruppe aus, die das zuvor erstellte virtuelle Netzwerk enthält.|
    |Name des virtuellen Computers | Geben Sie einen Anzeigename ein, über den dieser virtuelle Computer identifiziert wird. In diesem Beispiel wird **DNSProxy** verwendet.|
    |Region | Wählen Sie dieselbe Region wie für das virtuelle Netzwerk aus, das zuvor erstellt wurde.  Es sind nicht alle VM-Größen in allen Regionen verfügbar.  |
    |Verfügbarkeitsoptionen |  Wählen Sie die gewünschte Verfügbarkeitsstufe aus.  Azure bietet mehrere Optionen zum Verwalten der Verfügbarkeit und Resilienz für Ihre Anwendungen.  Entwerfen Sie Ihre Projektmappe, um replizierte virtuelle Computer in Verfügbarkeitszonen und Verfügbarkeitsgruppen zu verwenden, um so Ihre Apps und Daten vor Rechenzentrumsausfällen und bei Wartungsereignissen zu schützen. Bei diesem Beispiel wird der Wert **Keine Infrastrukturredundanz erforderlich** verwendet. |
    |Image | Belassen Sie es bei **Ubuntu Server 18.04 LTS**. |
    |Authentifizierungsart | __Kennwort__ oder __öffentlicher SSH-Schlüssel__: Die Authentifizierungsmethode für das SSH-Konto. Aus Sicherheitsgründen empfehlen wir die Verwendung öffentlicher Schlüssel. In diesem Beispiel wird **Password** verwendet.  Weitere Informationen finden Sie unter [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).|
    |Benutzername |Geben Sie den Benutzernamen des Administrators für die VM ein.  In diesem Beispiel wird **sshuser** verwendet.|
    |Kennwort oder öffentlicher SSH-Schlüssel | Das verfügbare Feld wird durch Ihre Auswahl des **Authentifizierungstyps** bestimmt.  Geben Sie den entsprechenden Wert ein.|
    |Öffentliche Eingangsports|Wählen Sie **Ausgewählte Ports zulassen** aus. Wählen Sie dann **SSH (22)** aus der Dropdownliste **Eingehende Ports auswählen** aus.|

    ![Grundlegende Konfiguration des virtuellen Computers](./media/connect-on-premises-network/virtual-machine-basics.png)

    Übernehmen Sie für alle weitere Einträge die Standardwerte, und klicken Sie dann auf die Registerkarte **Netzwerk**.

4. Geben Sie auf der Registerkarte **Netzwerk** dann die folgenden Informationen ein:

    | Feld | Wert |
    | --- | --- |
    |Virtuelles Netzwerk | Wählen Sie das zuvor erstellte virtuelle Netzwerk aus.|
    |Subnet | Wählen Sie das Standardsubnetz für das zuvor erstellte virtuelle Netzwerk aus. Wählen Sie __nicht__ das Subnetz aus, das vom VPN-Gateway verwendet wird.|
    |Öffentliche IP-Adresse | Verwenden Sie den automatisch ausgefüllten Wert.  |

    ![Einstellungen für virtuelles HDInsight-Netzwerk](./media/connect-on-premises-network/virtual-network-settings.png)

    Übernehmen Sie für alle weitere Einstellungen die Standardwerte, und klicken Sie dann auf **Bewerten + erstellen**.

5. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus, um den virtuellen Computer zu erstellen.

### <a name="review-ip-addresses"></a>Überprüfen von IP-Adressen

Nachdem der virtuelle Computer erstellt wurde, erhalten Sie die Benachrichtigung **Bereitstellung erfolgreich** mit der Schaltfläche **Zu Ressource wechseln**.  Klicken Sie auf **Zu Ressource wechseln**, um zu Ihrem neuen virtuellen Computer zu wechseln.  Befolgen Sie in die Standardansicht für Ihren neuen virtuellen Computer die folgenden Schritte, um die zugeordneten IP-Adressen zu identifizieren:

1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.

2. Notieren Sie sich die Werte für **ÖFFENTLICHE IP-ADRESSE/DNS-NAMENSBEZEICHNUNG** und **PRIVATE IP-ADRESSE** für den späteren Gebrauch.

   ![Öffentliche und private IP-Adressen](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installieren und Konfigurieren von Bind (DNS-Software)

1. Verwenden Sie SSH, um eine Verbindung mit der __öffentlichen IP-Adresse__ des virtuellen Computers herzustellen. Ersetzen Sie `sshuser` durch das SSH-Benutzerkonto, der beim Erstellen des virtuellen Computers angegeben wurde. Im folgenden Beispiel wird über die IP-Adresse 40.68.254.142 eine Verbindung mit dem virtuellen Computer hergestellt:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Verwenden Sie in der SSH-Sitzung die folgenden Befehle, um Bind zu installieren:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Um Bind zur Weiterleitung von Anforderungen zur Namensauflösung an Ihren lokalen DNS-Server zu konfigurieren, verwenden Sie den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.options`:

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Ersetzen Sie die Werte im Abschnitt `goodclients` durch den IP-Adressbereich des virtuellen Netzwerks und des lokalen Netzwerks. In diesem Abschnitt werden die Adressen definiert, von denen dieser DNS-Server Anforderungen akzeptiert.
    >
    > Ersetzen Sie den Eintrag `192.168.0.1` im Abschnitt `forwarders` durch die IP-Adresse Ihres lokalen DNS-Servers. Dieser Eintrag routet DNS-Anforderungen zur Auflösung an Ihren lokalen DNS-Server.

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
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Der Text `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` ist das __DNS-Suffix__ für dieses virtuelle Netzwerk. Speichern Sie diesen Wert, da er später verwendet wird.

5. Um Bind zum Auflösen von DNS-Namen für Ressourcen innerhalb des virtuellen Netzwerks zu konfigurieren, verwenden Sie den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.local`:

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Sie müssen hierbei `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` durch das DNS-Suffix ersetzen, das Sie zuvor abgerufen haben.

    Verwenden Sie zum Bearbeiten dieser Datei den folgenden Befehl:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Zum Speichern der Datei drücken Sie __STRG+X__, __Y__ und dann die __EINGABETASTE__.

6. Verwenden Sie den folgenden Befehl, um Bind zu starten:

    ```bash
    sudo service bind9 restart
    ```

7. Um zu überprüfen, ob Bind die Namen der Ressourcen in Ihrem lokalen Netzwerk auflösen kann, verwenden Sie die folgenden Befehle:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Ersetzen Sie `dns.mynetwork.net` durch den vollqualifizierten Domänennamen (FQDN) einer Ressource in Ihrem lokalen Netzwerk.
    >
    > Ersetzen Sie `10.0.0.4` durch die __interne IP-Adresse__ Ihres benutzerdefinierten DNS-Servers im virtuellen Netzwerk.

    Die Ausgabe ähnelt dem folgenden Text:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurieren des virtuellen Netzwerks zur Verwendung des benutzerdefinierten DNS-Servers

Führen Sie über das [Azure-Portal](https://portal.azure.com) die folgenden Schritte aus, damit das virtuelle Netzwerk anstelle des rekursiven Azure-Resolvers den benutzerdefinierten DNS-Server verwendet:

1. Navigieren Sie im linken Menü zu **Alle Dienste** > **Netzwerk** > **Virtuelle Netzwerke**.

2. Wählen Sie Ihr virtuelles Netzwerk aus der Liste aus, wodurch die Standardansicht für Ihre virtuelles Netzwerk geöffnet wird.  

3. Klicken Sie in der Standardansicht unter **Einstellungen** auf **DNS-Server**.  

4. Wählen Sie __Benutzerdefiniert__ aus, und geben Sie die **PRIVATE IP-ADRESSE** des benutzerdefinierten DNS-Servers ein.

5. Wählen Sie __Speichern__ aus.  <br />  

    ![Festlegen des benutzerdefinierten DNS-Servers für das Netzwerk](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurieren des lokalen DNS-Servers

Im vorherigen Abschnitt haben Sie den benutzerdefinierten DNS-Server so konfiguriert, dass Anforderungen an den lokalen DNS-Server weitergeleitet werden. Als Nächstes müssen Sie den lokalen DNS-Server so konfigurieren, dass Anforderungen an den benutzerdefinierten DNS-Server weitergeleitet werden.

Informationen dazu, wie Sie Ihren DNS-Server konfigurieren, finden Sie in der Dokumentation zu Ihrer DNS-Serversoftware. Suchen Sie hierbei nach den Schritten zum Konfigurieren einer __bedingten Weiterleitung__.

Bei einer bedingten Weiterleitung werden nur Anforderungen für ein bestimmtes DNS-Suffix weitergeleitet. In diesem Fall müssen Sie eine Weiterleitung für das DNS-Suffix des virtuellen Netzwerks konfigurieren. Anforderungen an dieses Suffix sollen an die IP-Adresse des benutzerdefinierten DNS-Servers weitergeleitet werden. 

Der folgende Text ist eine Beispielkonfiguration für eine bedingte Weiterleitung für die DNS-Software **Bind**:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Informationen zur Verwendung von DNS in **Windows Server 2016** finden Sie in der [Add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone)-Dokumentation.

Nachdem Sie den lokalen DNS-Server konfiguriert haben, können Sie mithilfe von `nslookup` überprüfen, ob Namen im virtuellen Netzwerk aufgelöst werden können. Beispiel: 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

In diesem Beispiel wird der lokale DNS-Server unter 196.168.0.4 dazu verwendet, den Namen des benutzerdefinierten DNS-Servers aufzulösen. Ersetzen Sie die IP-Adresse durch die des lokalen DNS-Servers. Ersetzen Sie die Adresse `dnsproxy` durch den vollqualifizierten Domänennamen des benutzerdefinierten DNS-Servers.

## <a name="optional-control-network-traffic"></a>Optional: Steuern des Netzwerkdatenverkehrs

Sie können Netzwerksicherheitsgruppen (NSGs) oder benutzerdefinierte Routen dazu verwenden, den Netzwerkdatenverkehr zu steuern. NSGs ermöglichen das Filtern das eingehenden und ausgehenden Datenverkehrs, und Sie können Datenverkehr zulassen oder untersagen. Mithilfe von benutzerdefinierten Routen können Sie den Datenfluss zwischen den Ressourcen im virtuellen Netzwerk, dem Internet und dem lokalen Netzwerk steuern.

> [!WARNING]  
> HDInsight erfordert eingehenden Zugriff auf bestimmte IP-Adressen in der Azure-Cloud sowie unbeschränkten ausgehenden Zugriff. Wenn Sie NSGs oder benutzerdefinierte Routen zum Steuern des Datenverkehrs verwenden, müssen Sie die folgenden Schritte ausführen:

1. Ermitteln Sie die IP-Adressen für den Standort, der Ihr lokales Netzwerk enthält. Eine Liste der benötigten IPs nach Standort finden Sie unter [Erforderliche IP-Adressen](./hdinsight-management-ip-addresses.md).

2. Lassen Sie für die in Schritt 1 identifizierten IP-Adressen eingehenden Datenverkehr von diesen IP-Adressen zu.

   * Wenn Sie eine __Netzwerksicherheitsgruppe__ verwenden: Lassen Sie __eingehenden__ Datenverkehr an Port __443__ für die IP-Adressen zu.
   * Wenn Sie eine __benutzerdefinierte Route__ verwenden: Legen Sie den Typ des __nächsten Hops__ der Route für die IP-Adressen auf __Internet__ fest.

Ein Beispiel zur Verwendung von Azure PowerShell oder der Azure CLI zum Erstellen von NSGs finden Sie im Dokument [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-create-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Erstellen des HDInsight-Clusters

> [!WARNING]  
> Sie müssen den benutzerdefinierten DNS-Server konfigurieren, bevor Sie HDInsight im virtuellen Netzwerk installieren.

Folgen Sie den Anweisungen im Dokument [Erstellen von Linux-basierten Clustern in HDInsight mithilfe des Azure-Portals](./hdinsight-hadoop-create-linux-clusters-portal.md), um einen HDInsight-Cluster zu erstellen.

> [!WARNING]  
> * Während der Clustererstellung müssen Sie den Standort auswählen, der Ihr virtuelles Netzwerk enthält.
> * Im Abschnitt __Erweiterte Einstellungen__ der Konfiguration müssen Sie das zuvor erstellte virtuelle Netzwerk und das Subnetz auswählen.

## <a name="connecting-to-hdinsight"></a>Verbindungsherstellung mit HDInsight

In der Dokumentation zu HDInsight wird in den meisten Fällen vorausgesetzt, dass Sie über das Internet auf den Cluster zugreifen können. Sie müssen z.B. unter `https://CLUSTERNAME.azurehdinsight.net` eine Verbindung mit dem Cluster herstellen können. Diese Adresse verwendet das öffentliche Gateway, das nicht verfügbar ist, wenn Sie über NSGs oder benutzerdefinierte Routen den Zugriff über das Internet eingeschränkt haben.

Einigen Dokumente verweisen auch auf `headnodehost` beim Herstellen einer Verbindung mit dem Cluster über eine SSH-Sitzung. Diese Adresse ist über Knoten innerhalb eines Clusters verfügbar und kann nicht auf Clients verwendet werden, die über das virtuelle Netzwerk verbunden sind.

Zur direkten Verbindung mit HDInsight über das virtuelle Netzwerk führen Sie die folgenden Schritte aus:

1. Verwenden Sie eine der folgenden Methoden, um die internen FQDNs der HDInsight-Clusterknoten zu ermitteln:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Informationen dazu, wie Sie den Port ermitteln, an dem ein Dienst verfügbar ist, finden Sie unter [Ports für Apache Hadoop-Dienste in HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]  
    > Einige der auf den Hauptknoten gehosteten Dienste sind jeweils nur auf einem Knoten aktiv. Wenn Sie versuchen, auf einen Dienst auf einem der Hauptknoten zuzugreifen und ein Fehler auftritt, wechseln Sie zum anderen Hauptknoten.
    >
    > Beispielsweise ist Apache Ambari immer nur auf einem einzelnen Hauptknoten aktiv. Wenn Sie versuchen, auf dem Hauptknoten auf Ambari zuzugreifen und ein 404-Fehler zurückgegeben wird, wird Ambari auf dem anderen Hauptknoten ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Verwendung von HDInsight in einem virtuellen Netzwerk finden Sie im Dokument [Plan a virtual network for Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) (Planen eines virtuellen Netzwerks für Azure HDInsight).

* Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie in der [Übersicht zu virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-overview.md).

* Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie in der [Übersicht zu Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).

* Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung](../virtual-network/virtual-networks-udr-overview.md).