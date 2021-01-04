---
title: Verwenden der Azure Firewall zum Überprüfen des Datenverkehrs für einen privaten Endpunkt
titleSuffix: Azure Private Link
description: Erfahren Sie, wie Sie Datenverkehr, der für einen privaten Endpunkt bestimmt ist, mit Azure Firewall untersuchen können.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 5cbfd90ca65a1fb75c9cbe5602ac2a69741e378f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96017235"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Verwenden der Azure Firewall zum Überprüfen des Datenverkehrs für einen privaten Endpunkt

Der private Azure-Endpunkt ist der grundlegende Baustein für Azure Private Link. Private Endpunkte ermöglichen die Bereitstellung von Azure-Ressourcen in einem virtuellen Netzwerk, um privat mit Private Link-Ressourcen zu kommunizieren.

Private Endpunkte ermöglichen Ressourcenzugriff auf den Private Link-Dienst, der in einem virtuellen Netzwerk bereitgestellt wird. Der Zugriff auf den privaten Endpunkt durch Peering virtueller Netzwerke und lokale Netzwerkverbindungen erweitern die Konnektivität.

Möglicherweise müssen Sie den Datenverkehr von Clients zu den über private Endpunkte verfügbar gemachten Diensten untersuchen oder blockieren. Schließen Sie diese Untersuchung ab, indem Sie [Azure Firewall](../firewall/overview.md) oder ein virtuelles Netzwerkgerät eines Drittanbieters verwenden.

Es gelten die folgenden Einschränkungen:

* Netzwerksicherheitsgruppen (NSGs) gelten nicht für private Endpunkte
* Benutzerdefinierte Routen (UDR) gelten nicht für private Endpunkte
* Eine einzelne Routingtabelle kann an ein Subnetz angefügt werden
* Eine Routingtabelle unterstützt bis zu 400 Routen

Azure Firewall filtert den Datenverkehr mit einer der folgenden Optionen:

* [FQDN in Netzwerkregeln](../firewall/fqdn-filtering-network-rules.md) für TCP- und UDP-Protokolle
* [FQDN in Anwendungsregeln](../firewall/features.md#application-fqdn-filtering-rules) für HTTP, HTTPS und MSSQL. 

Die meisten Dienste, die über private Endpunkte verfügbar gemacht werden, verwenden HTTPS. Bei Verwendung von Azure SQL wird die Anwendung von Anwendungsregeln über Netzwerkregeln empfohlen.

> [!NOTE]
> Die SQL-FQDN-Filterung wird nur im [Proxymodus](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt (Port 1433). Im Modus **Proxy** kommt es unter Umständen zu längeren Wartezeiten als im Modus *Umleiten*. Wenn Sie weiterhin den Umleitungsmodus (Standardmodus für Clients, die eine Verbindung innerhalb von Azure herstellen) verwenden möchten, können Sie den Zugriff mit dem FQDN in Netzwerkregeln der Firewall filtern.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Szenario 1: Hub-and-Spoke-Architektur – Dediziertes virtuelles Netzwerk für private Endpunkte

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Dediziertes virtuelles Netzwerk für private Endpunkte" border="true":::

Dieses Szenario ist die erweiterbarste Architektur, um über private Endpunkte eine private Verbindung mit mehreren Azure-Diensten herzustellen. Es wird eine Route erstellt, die auf den Netzwerkadressraum verweist, in dem die privaten Endpunkte bereitgestellt werden. Diese Konfiguration reduziert den Verwaltungsaufwand und verhindert, dass die Grenze von 400 Routen erreicht wird.

Verbindungen von einem virtuellen Clientnetzwerk zur Azure Firewall in einem virtuellen Hub-Netzwerk sind kostenpflichtig, wenn die virtuellen Netzwerke mit Peering miteinander verbunden sind.

Weitere Informationen zu den Gebühren im Zusammenhang mit Verbindungen mit virtuellen Netzwerken finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Preise](https://azure.microsoft.com/pricing/details/private-link/).

>[!NOTE]
> Dieses Szenario kann unter Verwendung beliebiger NVA- oder Azure Firewall-Netzwerkregeln von Drittanbietern anstelle von Anwendungsregeln implementiert werden.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Szenario 2: Hub-and-Spoke-Architektur – Freigegebenes virtuelles Netzwerk für private Endpunkte und virtuelle Computer

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Private Endpunkte und virtuelle Computer im selben virtuellen Netzwerk" border="true":::

Dieses Szenario wird in folgenden Situationen implementiert:

* Es ist nicht möglich, ein dediziertes virtuelles Netzwerk für die privaten Endpunkte zu verwenden

* Wenn nur einige wenige Dienste im virtuellen Netzwerk über private Endpunkte verfügbar sind

Die virtuellen Computer werden über /32 Systemrouten verfügen, die auf jeden privaten Endpunkt zeigen. Eine Route pro privatem Endpunkt ist so konfiguriert, dass der Datenverkehr durch Azure Firewall geleitet wird. 

Der Verwaltungsaufwand für die Wartung der Routingtabelle nimmt zu, da die Dienste im virtuellen Netzwerk verfügbar gemacht werden. Auch die Möglichkeit, den Routengrenzwert zu erreichen, nimmt zu.

Abhängig von Ihrer Gesamtarchitektur ist es möglich, den Grenzwert von 400 Routen zu erreichen. Es wird empfohlen, wann immer möglich Szenario 1 zu verwenden.

Verbindungen von einem virtuellen Clientnetzwerk zur Azure Firewall in einem virtuellen Hub-Netzwerk sind kostenpflichtig, wenn die virtuellen Netzwerke mit Peering miteinander verbunden sind.

Weitere Informationen zu den Gebühren im Zusammenhang mit Verbindungen mit virtuellen Netzwerken finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Preise](https://azure.microsoft.com/pricing/details/private-link/).

>[!NOTE]
> Dieses Szenario kann unter Verwendung beliebiger NVA- oder Azure Firewall-Netzwerkregeln von Drittanbietern anstelle von Anwendungsregeln implementiert werden.

## <a name="scenario-3-single-virtual-network"></a>Szenario 3: Einzelnes virtuelles Netzwerk

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Einzelnes virtuelles Netzwerk" border="true":::

Es gibt einige Einschränkungen bei der Implementierung: Eine Migration zu einer Hub-and-Spoke-Architektur ist nicht möglich. Es gelten dieselben Überlegungen wie in Szenario 2. In diesem Szenario fallen keine Gebühren für das Peering virtueller Netzwerke an.

>[!NOTE]
> Wenn Sie dieses Szenario mit einer NVA- oder Azure Firewall eines Drittanbieters implementieren möchten, sind Netzwerkregeln anstelle von Anwendungsregeln für den SNAT-Datenverkehr erforderlich, der für die privaten Endpunkte bestimmt ist. Andernfalls tritt ein Fehler bei der Kommunikation zwischen den virtuellen Computern und privaten Endpunkten auf.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Szenario 4: Lokaler Datenverkehr zu privaten Endpunkten

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Lokaler Datenverkehr zu privaten Endpunkten" border="true":::

Diese Architektur kann implementiert werden, wenn Sie die Konnektivität mit Ihrem lokalen Netzwerk über eine der beiden folgenden Optionen konfiguriert haben: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [VPN zwischen Standorten](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md) 

Wenn Ihre Sicherheitsanforderungen erfordern, dass der Datenverkehr von Clients zu Diensten, die über private Endpunkte verfügbar gemacht werden, über eine Sicherheitsappliance geleitet wird, stellen Sie dieses Szenario bereit.

Es gelten dieselben Überlegungen wie oben in Szenario 2. In diesem Szenario fallen keine Gebühren für das Peering virtueller Netzwerke an. Weitere Informationen zum Konfigurieren Ihrer DNS-Server, damit lokale Workloads auf private Endpunkte zugreifen können, finden Sie unter [Lokale Workloads mit DNS-Weiterleitung](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> Wenn Sie dieses Szenario mit einer NVA- oder Azure Firewall eines Drittanbieters implementieren möchten, sind Netzwerkregeln anstelle von Anwendungsregeln für den SNAT-Datenverkehr erforderlich, der für die privaten Endpunkte bestimmt ist. Andernfalls tritt ein Fehler bei der Kommunikation zwischen den virtuellen Computern und privaten Endpunkten auf.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement.
* Einen Log Analytics-Arbeitsbereich  

Weitere Informationen zum Erstellen eines Arbeitsbereichs, wenn Sie keinen Arbeitsbereich in Ihrem Abonnement besitzen, finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-vm"></a>Erstellen einer VM

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz zum Hosten des virtuellen Computers, der für den Zugriff auf Ihre Private Link-Ressource verwendet wird. Als Beispieldienst wird eine Azure SQL-Datenbank verwendet.

## <a name="virtual-networks-and-parameters"></a>Virtuelle Netzwerke und Parameter

Erstellen Sie drei virtuelle Netzwerke und ihre entsprechenden Subnetze für Folgendes:

* Sie enthalten die Azure Firewall, die zur Einschränkung der Kommunikation zwischen dem virtuellen Computer und dem privaten Endpunkt verwendet wird.
* Sie hosten den virtuellen Computer, der für den Zugriff auf Ihre Private Link-Ressource verwendet wird.
* Sie hosten den privaten Endpunkt.

Ersetzen Sie die folgenden Parameter in den Schritten durch die folgenden Informationen:

### <a name="azure-firewall-network"></a>Azure Firewall-Netzwerk
| Parameter                   | Wert                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | USA Süd Mitte      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Netzwerk für virtuelle Computer
| Parameter                   | Wert                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | USA Süd Mitte      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Netzwerk des privaten Endpunkts
| Parameter                   | Wert                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | USA Süd Mitte      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Wiederholen Sie die Schritte 1 bis 9, um die virtuellen Netzwerke für das Hosting der virtuellen Computer und der privaten Endpunktressourcen zu erstellen.

### <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Wählen Sie oben links auf dem Bildschirm im Azure-Portal die Option **Ressource erstellen** > **Compute** > **Virtueller Computer** aus.

2. Geben Sie in **Virtuellen Computer erstellen – Grundlagen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese Ressourcengruppe wurde im vorherigen Abschnitt erstellt.  |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Süden-Mitte** aus. |
    | Verfügbarkeitsoptionen | Übernehmen Sie den Standardwert **Keine Infrastrukturredundanz erforderlich**. |
    | Image | Wählen Sie **Ubuntu Server 18.04 LTS – Gen1** aus. |
    | Size | Wählen Sie **Standard_B2s** aus. |
    | **Administratorkonto** |  |
    | Authentifizierungsart | Wählen Sie **Kennwort** aus. |
    | Username | Geben Sie einen Benutzernamen Ihrer Wahl ein. |
    | Kennwort | Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |  |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |
    |||

3. Klicken Sie auf **Weiter: Datenträger**.

4. Übernehmen Sie unter **Virtuellen Computer erstellen – Datenträger** die Standardwerte, und wählen Sie **Weiter: Netzwerk**.

5. Wählen Sie in **Virtuellen Computer erstellen – Netzwerk** diese Informationen aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Virtuelles Netzwerk | Wählen Sie **myVMVNet** aus.  |
    | Subnet | Wählen Sie **VMSubnet (10.1.0.0/24)** aus.|
    | Öffentliche IP-Adresse | Übernehmen Sie den Standardwert **(neu) myVm-ip**. |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **SSH** aus.|
    ||

6. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

7. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

## <a name="deploy-the-firewall"></a>Bereitstellen der Firewall

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

2. Geben Sie **Firewall** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.

3. Wählen Sie **Firewall** aus, und klicken Sie anschließend auf **Erstellen**.

4. Konfigurieren Sie die Firewall auf der Seite **Firewall erstellen** anhand der folgenden Tabelle:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus.  |
    | **Instanzendetails** |  |
    | Name | Geben Sie **myAzureFirewall** ein. |
    | Region | Wählen Sie **USA, Süden-Mitte** aus. |
    | Verfügbarkeitszone | Übernehmen Sie den Standardwert **Keine**. |
    | Virtuelles Netzwerk auswählen    |    Wählen Sie **Vorhandene verwenden** aus.    |
    | Virtuelles Netzwerk    |    Wählen Sie **myAzFwVNet** aus.    |
    | Öffentliche IP-Adresse    |    Wählen Sie **Neue hinzufügen** aus, und geben Sie unter Name **myFirewall-ip** ein.    |
    | Tunnelerzwingung    | Belassen Sie die Standardeinstellung **Deaktiviert**.    |
    |||
5. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

6. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

## <a name="enable-firewall-logs"></a>Aktivieren von Firewallprotokollen

In diesem Abschnitt aktivieren Sie die Protokolle für die Firewall.

1. Wählen Sie im Azure-Portal **Alle Ressourcen** im linken Menü aus.

2. Wählen Sie die Firewall **myAzureFirewall** in der Liste der Ressourcen aus.

3. Wählen Sie unter **Überwachung** in den Firewalleinstellungen **Diagnoseeinstellungen** aus.

4. Wählen Sie **+ Diagnoseeinstellung hinzufügen** in den Diagnoseeinstellungen aus.

5. Geben Sie die folgenden Informationen in **Diagnoseeinstellung** ein oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name der Diagnoseeinstellung | Geben Sie **myDiagSetting** ein. |
    | Kategoriedetails | |
    | log | Wählen Sie **AzureFirewallApplicationRule** und **AzureFirewallNetworkRule** aus. |
    | Zieldetails | Wählen Sie **An Log Analytics senden** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Log Analytics-Arbeitsbereich | Wählen Sie Ihren Log Analytics-Arbeitsbereich aus. |

6. Wählen Sie **Speichern** aus.

## <a name="create-azure-sql-database"></a>Erstellen einer Azure SQL-Datenbank

In diesem Abschnitt erstellen Sie eine private SQL-Datenbank.

1. Wählen Sie oben links auf dem Bildschirm im Azure-Portal die Option **Ressource erstellen** > **Datenbanken** > **SQL-Datenbank** aus.

2. Geben Sie unter **SQL-Datenbank erstellen – Grundlagen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. Diese Ressourcengruppe wurde im vorherigen Abschnitt erstellt.|
    | **Datenbankdetails** |  |
    | Datenbankname  | Geben Sie **mydatabase** ein.  |
    | Server | Wählen Sie **Neu erstellen** aus, und geben Sie die nachfolgenden Informationen ein.    |
    | Servername | Geben Sie **mydbserver** ein. Wenn dieser Name vergeben ist, geben Sie einen eindeutigen Namen ein.   |
    | Serveradministratoranmeldung | Geben Sie einen Namen Ihrer Wahl ein. |
    | Kennwort    |    Geben Sie das gewünschte Kennwort ein.    |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein.    |
    | Standort    | Wählen Sie **USA, Süden-Mitte** aus.    |
    | Möchten Sie einen Pool für elastische SQL-Datenbanken verwenden?    | Übernehmen Sie den Standardwert **Nein**. |
    | Compute und Speicher | Belassen Sie die Standardeinstellung **Universell Gen5, 2 virtuelle Kerne, 32 GB Speicher**. |
    |||

3. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

4. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie einen privaten Endpunkt für die Azure SQL-Datenbank aus dem vorigen Abschnitt.

1. Wählen Sie im Azure-Portal **Alle Ressourcen** im linken Menü aus.

2. Wählen Sie den Azure SQL-Server **mydbserver** in der Liste der Dienste aus.  Wenn Sie einen anderen Servernamen verwendet haben, wählen Sie diesen Namen aus.

3. Wählen Sie in den Servereinstellungen **Private Endpunktverbindungen** unter **Sicherheit** aus.

4. Wählen Sie **+ Privaten Endpunkt hinzufügen** aus.

5. Geben Sie unter **Privaten Endpunkt erstellen** die folgenden Informationen auf der Registerkarte **Grundlagen** ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** | |
    | Name | Geben Sie **SQLPrivateEndpunkt** ein. |
    | Region | Wählen Sie **USA, Süden-Mitte** aus. |

6. Wählen Sie die Registerkarte **Ressource** oder die Option **Weiter: Ressource** unten auf der Seite aus.

7. Geben Sie auf der Registerkarte **Ressource** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsmethode | Wählen Sie **Hiermit wird eine Verbindung mit einer Azure-Ressource im eigenen Verzeichnis hergestellt** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.Sql/servers** aus. |
    | Resource | Wählen Sie **mydbserver** oder den Namen des Servers aus, den Sie im vorherigen Schritt erstellt haben.
    | Zielunterressource | Wählen Sie **sqlServer** aus. |

8. Wählen Sie die Registerkarte **Konfiguration** oder die Option **Weiter: Konfiguration** unten auf der Seite aus.

9. Geben Sie diese Informationen auf der Registerkarte **Konfiguration** ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerk** | |
    | Virtuelles Netzwerk | Wählen Sie **myPEVnet** aus. |
    | Subnet | Wählen Sie **PrivateEndpointSubnet** aus. |
    | **Private DNS-Integration** | |
    | Integration in eine private DNS-Zone | Wählen Sie **Ja** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Private DNS-Zonen | Belassen Sie die Standardeinstellung **privatelink.database.windows.net**. |

10. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Option **Überprüfen + erstellen** unten auf der Seite aus.

11. Klicken Sie auf **Erstellen**.

12. Nachdem der Endpunkt erstellt wurde, wählen Sie **Firewalls und virtuelle Netzwerke** unter **Sicherheit** aus.

13. In **Firewalls und virtuelle Netzwerke** wählen Sie **Ja** neben **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aus.

14. Wählen Sie **Speichern** aus.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Verbinden der virtuellen Netzwerke mittels Peering virtueller Netzwerke

In diesem Abschnitt verbinden wir die virtuellen Netzwerke **myVMVNet** und **myPEVNet** mit **myAzFwVNet** mittels Peering. Es wird keine direkte Konnektivität zwischen **myVMVNet** und **myPEVNet** geben.

1. Geben Sie in der Suchleiste des Portals **myAzFwVNet** ein.

2. Wählen Sie **Peerings** unter **Einstellungen** und dann **+ Hinzufügen** aus.

3. Geben Sie in **Peering hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name für das Peering von myAzFwVNet zum virtuellen Remotenetzwerk | Geben Sie **myAzFwVNet-to-myVMVNet** ein. |
    | **Peerdetails** |  |
    | Bereitstellungsmodell für das virtuelle Netzwerk  | Belassen Sie die Standardeinstellung **Resource Manager**.  |
    | Ich kenne meine Ressourcen-ID | Lassen Sie die Option deaktiviert.    |
    | Subscription | Wählen Sie Ihr Abonnement aus.    |
    | Virtuelles Netzwerk | Wählen Sie **myVMVNet** aus. |
    | Name für das Peering vom virtuellen Remotenetzwerk zu myAzFwVNet    |    Geben Sie **myVMVNet-to-myAzFwVNet** ein.    |
    | **Konfiguration** | |
    | **Einstellungen für VNET-Zugriff konfigurieren** | |
    | Gestatten des virtuellen Netzwerkzugriff von myAzFwVNet auf ein virtuelles Remotenetzwerk | Behalten Sie den Standardwert von **Aktiviert** bei.    |
    | Gestatten des virtuellen Netzwerkzugriff vom virtuellen Remotenetzwerk zu myAzFwVNet    | Behalten Sie den Standardwert von **Aktiviert** bei.    |
    | **Einstellungen für weitergeleiteten Datenverkehr konfigurieren** | |
    | Gestatten des weitergeleiteten Datenverkehrs vom virtuellen Remotenetzwerk zu myAzFwVNet    | Wählen Sie **Aktiviert**. |
    | Gestatten des weitergeleiteten Datenverkehrs von myAzFwVNet zum virtuellen Remotenetzwerk | Wählen Sie **Aktiviert**. |
    | **Einstellungen für Gatewaytransit konfigurieren** | |
    | Gatewaytransit zulassen | Lassen Sie die Option deaktiviert. |
    |||

4. Klicken Sie auf **OK**.

5. Wählen Sie **+ Hinzufügen**.

6. Geben Sie in **Peering hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name für das Peering von myAzFwVNet zum virtuellen Remotenetzwerk | Geben Sie **myAzFwVNet-to-myPEVNet** ein. |
    | **Peerdetails** |  |
    | Bereitstellungsmodell für das virtuelle Netzwerk  | Belassen Sie die Standardeinstellung **Resource Manager**.  |
    | Ich kenne meine Ressourcen-ID | Lassen Sie die Option deaktiviert.    |
    | Subscription | Wählen Sie Ihr Abonnement aus.    |
    | Virtuelles Netzwerk | Wählen Sie **myPEVNet** aus. |
    | Name für das Peering vom virtuellen Remotenetzwerk zu myAzFwVNet    |    Geben Sie **myPEVNet-to-myAzFwVNet** ein.    |
    | **Konfiguration** | |
    | **Einstellungen für VNET-Zugriff konfigurieren** | |
    | Gestatten des virtuellen Netzwerkzugriff von myAzFwVNet auf ein virtuelles Remotenetzwerk | Behalten Sie den Standardwert von **Aktiviert** bei.    |
    | Gestatten des virtuellen Netzwerkzugriff vom virtuellen Remotenetzwerk zu myAzFwVNet    | Behalten Sie den Standardwert von **Aktiviert** bei.    |
    | **Einstellungen für weitergeleiteten Datenverkehr konfigurieren** | |
    | Gestatten des weitergeleiteten Datenverkehrs vom virtuellen Remotenetzwerk zu myAzFwVNet    | Wählen Sie **Aktiviert**. |
    | Gestatten des weitergeleiteten Datenverkehrs von myAzFwVNet zum virtuellen Remotenetzwerk | Wählen Sie **Aktiviert**. |
    | **Einstellungen für Gatewaytransit konfigurieren** | |
    | Gatewaytransit zulassen | Lassen Sie die Option deaktiviert. |

7. Klicken Sie auf **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Verknüpfen der virtuellen Netzwerke mit der privaten DNS-Zone

In diesem Abschnitt werden wir die virtuellen Netzwerke **myVMVNet** und **myAzFwVNet** mit der privaten DNS-Zone **privatelink.database.windows.net** verknüpfen. Diese Zone wurde erstellt, als wir den privaten Endpunkt erzeugt haben. 

Der Link ist erforderlich, damit der virtuelle Computer und die Firewall den FQDN der Datenbank in seine private Endpunktadresse auflösen können. Das virtuelle Netzwerk **myPEVNet** wurde beim Erstellen des privaten Endpunkts automatisch verknüpft.

>[!NOTE]
>Wenn Sie die VM und die virtuellen Firewallnetzwerke nicht mit der privaten DNS-Zone verknüpfen, können sowohl die VM als auch die Firewall den SQL Server-FQDN trotzdem auflösen. Sie werden zu seiner öffentlichen IP-Adresse aufgelöst.

1. Geben Sie in der Suchleiste des Portals **privatelink.database** ein.

2. Wählen Sie **privatelink.database.windows.net** in den Suchergebnissen aus.

3. Wählen Sie **Verknüpfungen virtueller Netzwerke** unter **Einstellungen** aus.

4. Wählen Sie **+ Hinzufügen** aus.

5. Geben Sie in **VNET-Verknüpfung hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Linkname | Geben Sie **Link-to-myVMVNet** ein. |
    | **Details zum virtuellen Netzwerk** |  |
    | Ich kenne die Ressourcen-ID des virtuellen Netzwerks.  | Lassen Sie die Option deaktiviert.  |
    | Subscription | Wählen Sie Ihr Abonnement aus.    |
    | Virtuelles Netzwerk | Wählen Sie **myVMVNet** aus. |
    | **CONFIGURATION** | |
    | Automatische Registrierung aktivieren | Lassen Sie die Option deaktiviert.    |


6. Klicken Sie auf **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Konfigurieren einer Anwendungsregel mit SQL-FQDN in Azure Firewall

In diesem Abschnitt konfigurieren Sie eine Anwendungsregel, um die Kommunikation zwischen **myVM** und dem privaten Endpunkt für SQL Server **mydbserver.database.windows.net** zuzulassen. 

Diese Regel ermöglicht die Kommunikation über die Firewall, die wir in den vorherigen Schritten erstellt haben.

1. Geben Sie in der Suchleiste des Portals **myAzureFirewall** ein.

2. Wählen Sie in den Suchergebnissen **myAzureFirewall** aus.

3. Wählen Sie **Regeln** unter **Einstellungen** in der Übersicht **myAzureFirewall** aus.

4. Klicken Sie auf die Registerkarte **Anwendungsregelsammlung**.

5. Wählen Sie **+ Anwendungsregelsammlung hinzufügen** aus.

6. Geben Sie in **Anwendungsregelsammlung hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **SQLPrivateEndpunkt** ein. |
    | Priorität | Geben Sie **100** ein. |
    | Aktion | Geben Sie **Zulassen** ein. |
    | **Regeln** |  |
    | **FQDN-Tags** | |
    | Name  | Lassen Sie dieses Feld leer.  |
    | Quelltyp | Belassen Sie die standardmäßige **IP-Adresse**.    |
    | `Source` | Lassen Sie dieses Feld leer. |
    | FQDN-Tags | Belassen Sie die Standardeinstellung **0 ausgewählt**. |
    | **Ziel-FQDNs** | |
    | Name | Geben Sie **SQLPrivateEndpunkt** ein.    |
    | Quelltyp | Belassen Sie die standardmäßige **IP-Adresse**. |
    | `Source` | Geben Sie **10.1.0.0/16** ein. |
    | Protokoll: Port | Geben Sie **mssql:1433** ein. |
    | Ziel-FQDNs | Geben Sie **mydbserver.database.windows.net** ein. |
    |||

7. Wählen Sie **Hinzufügen**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Weiterleiten des Datenverkehrs zwischen dem virtuellen Computer und dem privaten Endpunkt über die Azure Firewall

Wir haben kein Peering virtueller Netzwerke direkt zwischen den virtuellen Netzwerken **myVMVNet** und **myPEVNet** erstellt. Der virtuelle Computer **myVM** besitzt keine Route zu dem von uns erstellten privaten Endpunkt. 

In diesem Abschnitt erstellen wir eine Routingtabelle mit einer benutzerdefinierten Route. 

Die Route sendet Datenverkehr aus dem Subnetz **myVM** durch die Azure Firewall in den Adressraum des virtuellen Netzwerks **myPEVNet**.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

2. Geben Sie **Routingtabelle** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.

3. Wählen Sie **Routingtabelle** und dann **Erstellen** aus.

4. Verwenden Sie auf der Seite **Routingtabelle erstellen** die folgende Tabelle aus, um die Routingtabelle zu konfigurieren:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus.  |
    | **Instanzendetails** |  |
    | Region | Wählen Sie **USA, Süden-Mitte** aus. |
    | Name | Geben Sie **VMsubnet-to-AzureFirewall** ein. |
    | Gatewayrouten verteilen | Wählen Sie **Nein** aus. |

5. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Seite **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

6. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

7. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressource wechseln** aus.

8. Wählen Sie unter **Einstellungen** die Option **Routen** aus.

9. Wählen Sie **+ Hinzufügen**.

10. Geben Sie auf der Seite **Route hinzufügen** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Routenname | Geben Sie **myVMsubnet-to-privateendpoint** ein. |
    | Adresspräfix | Geben Sie **10.2.0.0/16** ein.  |
    | Typ des nächsten Hops | Wählen Sie **Virtuelles Gerät** aus. |
    | Adresse des nächsten Hops | Geben Sie **10.0.0.4** ein. |

11. Klicken Sie auf **OK**.

12. Wählen Sie unter **Einstellungen** die Option **Subnetze** aus.

13. Wählen Sie **+ Zuordnen** aus.

14. Geben Sie auf der Seite **Subnetz zuordnen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Virtuelles Netzwerk | Wählen Sie **myVMVNet** aus. |
    | Subnet | Wählen Sie **VMSubnet** aus.  |

15. Klicken Sie auf **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Herstellen einer Verbindung mit dem virtuellen Computer über den Clientcomputer

Stellen Sie wie folgt eine Verbindung mit dem virtuellen Computer **myVm** aus dem Internet her:

1. Geben Sie in der Suchleiste des Portals **myVm-ip** ein.

2. Wählen Sie **myVM-ip** in den Suchergebnissen aus.

3. Kopieren oder notieren Sie den Wert unter **IP-Adresse**.

4. Wenn Sie Windows 10 verwenden, führen Sie den folgenden Befehl mit PowerShell aus. Für andere Windows-Clientversionen verwenden Sie einen SSH-Client wie [Putty](https://www.putty.org/):

* Ersetzen Sie den **Benutzernamen** durch den Administratorbenutzernamen, den Sie bei der Erstellung des virtuellen Computers eingegeben haben.

* Ersetzen Sie **IP-Adresse** durch die IP-Adresse aus dem vorherigen Schritt.

    ```bash
    ssh username@IPaddress
    ```

5. Geben Sie das Kennwort ein, das Sie bei der Erstellung von **myVm** definiert haben.

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Privater Zugriff auf SQL Server über den virtuellen Computer

In diesem Abschnitt stellen Sie über den privaten Endpunkt eine private Verbindung mit der SQL-Datenbank her.

1. Geben Sie `nslookup mydbserver.database.windows.net` ein.
    
    Sie erhalten eine Meldung wie die folgende:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Installieren Sie [SQL Server-Befehlszeilentools](/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools).

3. Führen Sie den folgenden Befehl aus, um eine Verbindung mit dem SQL Server herzustellen. Verwenden Sie den Serveradministrator und das zugehörige Kennwort, das Sie beim Erstellen des SQL-Servers in den vorherigen Schritten definiert haben.

* Ersetzen Sie **\<ServerAdmin>** durch den Administratorbenutzernamen, den Sie bei der Erstellung des SQL-Servers eingegeben haben.

* Ersetzen Sie **\<YourPassword>** durch das Administratorkennwort, das Sie bei der Erstellung des SQL-Servers eingegeben haben.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Bei erfolgreicher Anmeldung wird eine SQL-Eingabeaufforderung angezeigt. Geben Sie **exit** ein, um das Tool **sqlcmd** zu beenden.

5. Schließen Sie die Verbindung mit **myVM** durch Eingabe von **exit**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Überprüfen des Datenverkehrs in Azure Firewall-Protokollen

1. Wählen Sie im Azure-Portal **Alle Ressourcen** und dann Ihren Log Analytics-Arbeitsbereich aus.

2. Wählen Sie **Protokolle** unter **Allgemein** auf der Log Analytics-Arbeitsbereichsseite aus.

3. Wählen Sie die Schaltfläche **Los geht's** aus.

4. Wählen Sie im Fenster **Beispielabfragen** unter **Alle Abfragen** die Option **Firewalls** aus.

5. Wählen Sie die Schaltfläche **Ausführen** unter **Anwendungsregelprotokolldaten** aus.

6. Überprüfen Sie in der Ausgabe der Protokollabfrage, ob **mydbserver.database.windows.net** unter **FQDN** und **SQLPrivateEndpoint** unter **RuleCollection** aufgeführt ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit den Ressourcen fertig sind, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen:

1. Geben Sie oben im Portal im Feld **Suchen** die Zeichenfolge **myResourceGroup** ein, und wählen Sie in den Suchergebnissen den Eintrag **myResourceGroup** aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie verschiedene Szenarien untersucht, mit denen Sie den Datenverkehr zwischen einem virtuellen Computer und einem privaten Endpunkt mit Azure Firewall einschränken können. 

Sie haben eine Verbindung mit dem virtuellen Computer hergestellt und über die Azure Firewall mit der Datenbank über einen privaten Link sicher kommuniziert.

Weitere Informationen zu privaten Endpunkten finden Sie unter [Was ist ein privater Endpunkt in Azure?](private-endpoint-overview.md).