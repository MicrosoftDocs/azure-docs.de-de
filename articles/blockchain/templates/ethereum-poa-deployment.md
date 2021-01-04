---
title: Bereitstellen der Lösungsvorlage „Ethereum-Proof-of-Authority-Konsortium“ auf Azure
description: Verwenden der Lösung „Ethereum-Proof-of-Authority-Konsortium“, um ein Netzwerk mit vielen Elementen für das Ethereum-Konsortium auf Azure bereitzustellen und zu konfigurieren
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: e680bc601b7f230314c1063523a003e95a849c0a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024397"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Bereitstellen der Lösungsvorlage „Ethereum-Proof-of-Authority-Konsortium“ auf Azure

Sie können die Azure-Lösungsvorlage [Ethereum-Proof-of-Authority-Konsortium](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) verwenden, um das Bereitstellen, Konfigurieren und Steuern eines Multi-Member-Netzwerks für das Ethereum-Proof-of-Authority-Konsortium zu erleichtern, ohne dass ausführliche Azure- und Ethereum-Kenntnisse vorhanden sein müssen.

Die Lösungsvorlage kann von jedem Konsortiummitglied verwendet werden, um mithilfe Microsoft Azure-Dienste für Compute-, Netzwerk- und Speicherfunktionen einen Blockchainnetzwerkspeicherbedarf anzugeben. Der Netzwerkfußabdruck jedes Konsortiummitglieds umfasst einige Validierungsknoten mit Lastenausgleich, über die eine Anwendung oder ein Benutzer interagieren kann, um Ethereum-Transaktionen zu übermitteln.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Auswählen einer Azure Blockchain-Lösung

Bevor Sie sich für die Lösungsvorlage „Ethereum-Proof-of-Authority-Konsortium“ entscheiden, sollten Sie Ihr Szenario mit den gängigen Anwendungsfällen der verfügbaren Azure Blockchain-Optionen vergleichen.

> [!IMPORTANT]
> Erwägen Sie die Verwendung des [Azure Blockchain Service](../service/overview.md) anstelle der Lösungsvorlage für Ethereum unter Azure. Azure Blockchain Service ist ein unterstützter verwalteter Azure-Dienst. Parity Ethereum ist zu einer von der Community gesteuerten Entwicklung und Wartung übergegangen. Weitere Informationen finden Sie unter [Wechsel von Parity Ethereum zu OpenEthereum DAO](https://www.parity.io/parity-ethereum-openethereum-dao/).

Option | Dienstmodell | Gängiger Anwendungsfall
-------|---------------|-----------------
Lösungsvorlagen | IaaS | Lösungsvorlagen sind Azure Resource Manager-Vorlagen, die Sie verwenden können, um eine vollständig konfigurierte Blockchainnetzwerktopologie bereitzustellen. Die Vorlagen stellen Microsoft Azure-Dienste für Compute-, Netzwerk- und Speicherfunktionen für einen bestimmten Blockchainnetzwerktyp bereit und konfigurieren diese. Lösungsvorlagen werden ohne Vereinbarung zum Servicelevel bereitgestellt. Nutzen Sie die [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-blockchain-workbench.html) zur Unterstützung.
[Azure Blockchain Service](../service/overview.md) | PaaS | Mit der Vorschauversion von Azure Blockchain Service wird die Einrichtung, Verwaltung und Governance von Konsortiumblockchainnetzwerken vereinfacht. Setzen Sie Azure Blockchain Service für Lösungen ein, bei denen PaaS, Konsortiumverwaltung oder Datenschutz für Vertrags- und Transaktionsaktivitäten erforderlich ist.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS und PaaS | Azure Blockchain Workbench (Vorschauversion) ist eine Sammlung mit Azure-Diensten und -Funktionen zum Erstellen und Bereitstellen von Blockchain-Anwendungen, mit denen Geschäftsprozesse und Daten mit anderen Organisationen gemeinsam genutzt werden können. Verwenden Sie Azure Blockchain Workbench, um einen Prototyp für eine Blockchainlösung oder einen Proof of Concept für eine Blockchainanwendung zu erstellen. Azure Blockchain Workbench wird ohne Vereinbarung zum Servicelevel bereitgestellt. Nutzen Sie die [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-blockchain-workbench.html) zur Unterstützung.

## <a name="solution-architecture"></a>Lösungsarchitektur

Mit der Ethereum-Lösungsvorlage können Sie Multi-Member-Netzwerke für Ethereum-Proof-of-Authority-Konsortien für eine oder mehrere Regionen bereitgestellt werden.

![Bereitstellungsarchitektur](./media/ethereum-poa-deployment/deployment-architecture.png)

Jede Bereitstellung von Konsortiumsmitgliedern umfasst Folgendes:

* Virtuelle Computer für die Ausführung der PoA-Validierungssteuerelemente
* Azure Load Balancer für die Verteilung von RPC-, Peering- und Governance-DApp-Anforderungen
* Azure Key Vault zum Schützen der Validierungselementidentitäten
* Azure Storage zum Hosten von persistenten Netzwerkinformationen und Koordinieren des Leasens
* Azure Monitor zum Aggregieren von Protokollen und Leistungsstatistiken
* VNET-Gateway (optional) zum Zulassen von VPN-Verbindungen über private VNETs

Standardmäßig sind die RPC- und Peeringendpunkte über die öffentliche IP-Adresse zugänglich, um eine vereinfachte Konnektivität über

Abonnements und Clouds zu ermöglichen. Für die Zugriffssteuerung auf Anwendungsebene können Sie [Parity-Verträge zur Berechtigungsvergabe](https://openethereum.github.io/Permissioning.html) verwenden. Es werden Netzwerke unterstützt, die hinter VPNs mit VNET-Gateways für abonnementübergreifende Konnektivität bereitgestellt werden. Da VPN- und VNET-Bereitstellungen komplexer sind sollten Sie beim Erstellen eines Prototyps für eine Lösung mit einem öffentlichen IP-Modell beginnen.

Docker-Container sorgen für Zuverlässigkeit und Modularität. Azure Container Registry wird verwendet, um im Rahmen jeder Bereitstellung Images mit Versionsangabe zu hosten und bereitzustellen. Die Containerimages bestehen aus:

* Orchestrator: generiert Identitäten und Governanceverträge, speichert Identitäten in einem Identitätsspeicher
* Parity-Client: least Identitäten aus dem Identitätsspeicher, ermittelt Peers und stellt die Verbindung damit her
* EthStats-Agent: erfasst lokale Protokolle und Statistiken per RPC und überträgt Informationen mithilfe von Push an Azure Monitor
* Governance-DApp: Webschnittstelle für die Interaktion mit Governanceverträgen

### <a name="validator-nodes"></a>Validierungsknoten

Beim Proof-of-Authority-Protokoll übernehmen Validierungsknoten die Rolle der herkömmlichen Miningknoten. Jedes Validierungssteuerelement verfügt über eine eindeutige Ethereum-Identität, die die Teilnahme am Blockerstellungsprozess ermöglicht. Jedes Konsortiumsmitglied kann zwei oder mehr Validierungsknoten für fünf Regionen bereitstellen, um Georedundanz zu erzielen. Validierungsknoten kommunizieren mit anderen Validierungsknoten, um sich über den Zustand des zugrunde liegenden Distributed Ledger abzustimmen. Damit im Netzwerk eine faire Beteiligung sichergestellt wird, ist es allen Konsortiumsmitgliedern untersagt, mehr Validierungssteuerelemente als das erste Mitglied des Netzwerks zu verwenden. Wenn das erste Mitglied beispielsweise drei Validierungssteuerelemente bereitstellt, kann jedes Mitglied nur drei Validierungssteuerelemente nutzen.

### <a name="identity-store"></a>Identitätsspeicher

Ein Identitätsspeicher wird im Abonnement jedes Mitglieds bereitgestellt, in dem die generierten Ethereum-Identitäten enthalten sind und geschützt werden. Für jedes Validierungssteuerelement generiert der Orchestrierungscontainer einen privaten Ethereum-Schlüssel für und speichert ihn in Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Bereitstellen des Ethereum-Konsortiumnetzwerks

In dieser exemplarischen Vorgehensweise gehen wir davon aus, dass Sie ein Multi-Party-Netzwerk für das Ethereum-Konsortium erstellen. Hier sehen Sie ein Beispiel für den Ablauf einer Multi-Party-Bereitstellung:

1. Drei Mitglieder generieren mit MetaMask jeweils ein Ethereum-Konto.
1. *Mitglied A* stellt einen Ethereum-PoA bereit und gibt seine öffentliche Ethereum-Adresse an.
1. *Mitglied A* stellt die Konsortiums-URL für *Mitglied B* und *Mitglied C* bereit.
1. *Mitglied B* und *Mitglied C* stellen Ethereum-PoA bereit und geben ihre öffentliche Ethereum-Adresse und die Konsortiums-URL von *Mitglied A* an.
1. *Mitglied A* wählt per Abstimmung *Mitglied B* als Administrator aus.
1. *Mitglied A* und *Mitglied B* wählen beide *Mitglied C* als Administrator aus.

In den nächsten Abschnitten wird veranschaulicht, wie Sie den Speicherbedarf des ersten Mitglieds im Netzwerk konfigurieren.

### <a name="create-resource"></a>Erstellen von Ressourcen

Klicken Sie im [Azure-Portal](https://portal.azure.com) in der linken oberen Ecke auf **Ressource erstellen**.

Wählen Sie **Blockchain** > **Ethereum Proof-of-Authority Consortium (Preview)** (Blockchain > Ethereum-Proof-of-Authority-Konsortium (Vorschauversion)) aus.

### <a name="basics"></a>Grundlagen

Geben Sie unter **Grundlagen** Werte für die Standardparameter für jede Bereitstellung an.

![Grundlagen](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | BESCHREIBUNG | Beispielwert
----------|-------------|--------------
Neues Netzwerk erstellen oder vorhandenem Netzwerk beitreten? | Sie können ein neues Netzwerk erstellen oder einem bereits vorhandenen Konsortiumnetzwerk beitreten. Für den Beitritt zu einem vorhandenen Netzwerk sind zusätzliche Parameter erforderlich. | Neu erstellen
E-Mail-Adresse | Sie erhalten eine E-Mail-Benachrichtigung mit Informationen zu Ihrer Bereitstellung, wenn die Bereitstellung abgeschlossen ist. | eine gültige E-Mail-Adresse
VM-Benutzername | Benutzername des Administrators der einzelnen bereitgestellten virtuellen Computer | die alphanumerischen Zeichen 1 bis 64
Authentifizierungsart | Die Methode zur Authentifizierung des virtuellen Computers. | Kennwort
Kennwort | Das Kennwort für das Administratorkonto jedes bereitgestellten, virtuellen Computers. Alle VMs verfügen anfänglich über das gleiche Kennwort. Sie können das Kennwort nach der Bereitstellung ändern. | 12 bis 72 Zeichen 
Subscription | Das Abonnement, für das das Konsortiumsnetzwerk bereitgestellt wird. |
Ressourcengruppe| Die Ressourcengruppe, für die das Konsortiumsnetzwerk bereitgestellt wird. | myResourceGroup
Standort | Die Azure-Region für die Ressourcengruppe. | USA, Westen 2

Klicken Sie auf **OK**.

### <a name="deployment-regions"></a>Bereitstellungsregionen

Geben Sie unter *Bereitstellungsregionen* die Anzahl der Regionen und Standorte für die einzelnen Standorte an. Sie können in maximal fünf Regionen Bereitstellungen durchführen. Die erste Region sollte mit dem Ressourcengruppenstandort aus dem Abschnitt *Grundlagen* übereinstimmt. Für Entwicklungs- oder Testnetzwerke können Sie eine einzelne Region pro Mitglied verwenden. Für die Produktion sollten Sie mindestens zwei Regionen bereitstellen, um Hochverfügbarkeit sicherzustellen.

![Bereitstellungsregionen](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | BESCHREIBUNG | Beispielwert
----------|-------------|--------------
Anzahl der Regionen|Die Anzahl der Regionen, in denen das Konsortiumsnetzwerk bereitgestellt wird.| 2
Erste Region | Die erste Region, in der das Konsortiumsnetzwerk bereitgestellt wird. | USA, Westen 2
Zweite Region | Die zweite Region, in der das Konsortiumsnetzwerk bereitgestellt wird – weitere Regionen werden angezeigt, wenn mindestens zwei Regionen vorhanden sind. | USA (Ost) 2

Klicken Sie auf **OK**.

### <a name="network-size-and-performance"></a>Netzwerkgröße und -leistung

Geben Sie unter *Netzwerkgröße und -leistung* Werte für die Größe des Konsortiumnetzwerks ein. Die Speichergröße des Validierungsknotens gibt die potenzielle Größe der Blockchain vor. Die Größe kann nach der Bereitstellung geändert werden.

![Netzwerkgröße und -leistung](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | BESCHREIBUNG | Beispielwert
----------|-------------|--------------
Anzahl von Validierungsknoten mit Lastenausgleich | Anzahl von Validierungsknoten, die als Teil des Netzwerks bereitgestellt werden | 2
Speicherleistung von Validierungsknoten | Die Art des verwalteten Datenträgers für die einzelnen bereitgestellten Validierungsknoten – Informationen zu den Preisen finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/). | SSD Standard
VM-Größe für Validierungsknoten | Die VM-Größe, die für Validierungsknoten verwendet wird. Informationen zu den Preisen finden Sie unter [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). | Standard D2 v3

Virtuelle Computer und die Speicherebene haben Auswirkungen auf die Netzwerkleistung.  Mithilfe der folgenden Tabelle können Sie eine kosteneffiziente Option auswählen:

SKU des virtuellen Computers|Speicherebene|Preis|Throughput|Latency
---|---|---|---|---
F1|SSD Standard|niedrig|niedrig|high
D2_v3|SSD Standard|mittel|mittel|mittel
F16s|SSD Premium|high|high|niedrig

Klicken Sie auf **OK**.

### <a name="ethereum-settings"></a>Ethereum-Einstellungen

Geben Sie unter *Ethereum-Einstellungen* die für Ethereum gültigen Konfigurationseinstellungen an.

![Ethereum-Einstellungen](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | BESCHREIBUNG | Beispielwert
----------|-------------|--------------
ID des Konsortiumsmitglieds | Die ID, die jedem Mitglied des Konsortiumnetzwerks zugeordnet ist. Sie wird verwendet, um IP-Adressräume zu konfigurieren und Kollisionen zu vermeiden. Bei einem privaten Netzwerk sollte die Mitglieds-ID für verschiedene Organisationen in demselben Netzwerk eindeutig sein.  Eine eindeutige Mitglieds-ID ist erforderlich, auch dieselbe Organisation Bereitstellungen in mehreren Regionen vornimmt. Notieren Sie sich den Wert dieses Parameters, da Sie ihn anderen teilnehmenden Mitgliedern mitteilen müssen, um Konflikte auszuschließen. Der gültige Bereich liegt zwischen 0 und 255. | 0
Netzwerk-ID | Die Netzwerk-ID für das bereitgestellte Ethereum-Konsortiumsnetzwerk. Jedes Ethereum-Netzwerk verfügt über eine eigene Netzwerk-ID, wobei 1 die ID für das öffentliche Netzwerk ist. Der gültige Bereich liegt zwischen 5 und 999.999.999. | 10101010
Ethereum-Administratoradresse | Die Adresse des Ethereum-Kontos, das für die Teilnahme am PoA-Governance-Prozess verwendet wird – Sie können MetaMask verwenden, um eine Ethereum-Adresse zu generieren. |
Erweiterte Optionen | Erweiterte Optionen für Ethereum-Einstellungen | Aktivieren
Deploy using Public IP (Mit öffentlicher IP-Adresse bereitstellen) | Wenn ein privates VNET ausgewählt ist, wird das Netzwerk hinter einer VNet Gateway-Instanz bereitgestellt, und der Peeringzugriff wird aufgehoben. In einem privaten VNET müssen alle Mitglieder eine VNet Gateway-Instanz nutzen, damit die Verbindung kompatibel ist. | Öffentliche IP-Adresse
Block Gas Limit (gas-Limit für Blocks) | Das anfängliche gas-Limit für Blocks im Netzwerk | 50000000
Zeitraum für erneute Blockversiegelung (Sek.) | Die Häufigkeit, mit der leere Blöcke erstellt werden, wenn im Netzwerk keine Transaktionen vorhanden sind. Eine höhere Häufigkeit führt zu einer schnelleren Finalität, jedoch auch zu erhöhten Speicherkosten. | 15
Transaction Permission Contract (Vertrag zu Transaktionsberechtigungen) | Bytecode für den Vertrag zur Berechtigungsvergabe für Transaktionen. Beschränkt die Smart Contract-Bereitstellung und -Ausführung auf eine berechtigungsbasierte Liste mit Ethereum-Konten. |

Klicken Sie auf **OK**.

### <a name="monitoring"></a>Überwachung

Mithilfe der Überwachung können Sie eine Protokollressource für Ihr Netzwerk konfigurieren. Der Überwachungs-Agent sammelt nützliche Metriken und Protokolle aus Ihrem Netzwerk und zeigt diese an. So lassen sich die Netzwerkintegrität oder Debugprobleme schnell überprüfen.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | BESCHREIBUNG | Beispielwert
----------|-------------|--------------
Überwachung | Option zum Aktivieren der Überwachung | Aktivieren
Connect to existing Azure Monitor logs (Mit vorhandenen Azure Monitor-Protokollen verbinden) | Option zum Erstellen einer neuen Azure Monitor-Protokollinstanz oder zum Beitritt zu einer vorhandenen Instanz | Neu erstellen
Standort | Die Region, in der die neue Instanz bereitgestellt wird | East US
Existing log analytics workspace ID (Connect to existing Azure Monitor logs = Join Existing) (Vorhandene Log Analytics-Arbeitsbereichs-ID (Mit vorhandener Azure Monitor-Protokollinstanz verbinden = Vorhandener beitreten))|Die Arbeitsbereichs-ID der vorhandenen Azure Monitor-Protokollinstanz||Nicht verfügbar
Existing log analytics primary key (Connect to existing Azure Monitor logs = Join Existing) (Vorhandener Log Analytics-Primärschlüssel (Mit vorhandener Azure Monitor-Protokollinstanz verbinden = Vorhandener beitreten))|Der Primärschlüssel, der zum Herstellen einer Verbindung mit der vorhandenen Azure Monitor-Protokollinstanz verwendet wird.||Nicht verfügbar

Klicken Sie auf **OK**.

### <a name="summary"></a>Zusammenfassung

Klicken Sie sich durch die Zusammenfassung, um die Eingaben zu überprüfen und eine grundlegende Überprüfung vor der Bereitstellung durchzuführen. Vor der Bereitstellung können Sie die Vorlage und die Parameter herunterladen.

Klicken Sie auf **Erstellen**, um die Bereitstellung durchzuführen.

Wenn die Bereitstellung VNet Gateway-Instanzen umfasst, kann diese zwischen 45 und 50 Minuten dauern.

## <a name="deployment-output"></a>Ausgabe der Bereitstellung

Nachdem die Bereitstellung abgeschlossen ist, können Sie über das Azure-Portal auf die erforderlichen Parameter zugreifen.

### <a name="confirmation-email"></a>Bestätigungs-E-Mail

Wenn Sie eine E-Mail-Adresse ([Grundlagen](#basics)) angeben, wird eine E-Mail gesendet, die die Bereitstellungsinformationen und Links zu dieser Dokumentation enthält.

![E-Mail zur Bereitstellung](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Nachdem die Bereitstellung erfolgreich abgeschlossen wurde und alle Ressourcen bereitgestellt wurden, können Sie sich die Ausgabeparameter in Ihrer Ressourcengruppe ansehen.

1. Navigieren Sie im Portal zu Ihrer Ressourcengruppe.
1. Klicken Sie auf **Übersicht > Bereitstellungen**.

    ![Übersicht über Ressourcengruppen](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Wählen Sie die Bereitstellung **microsoft-azure-blockchain.azure-blockchain-ether-...** aus.
1. Klicken Sie auf den Abschnitt **Ausgaben**.

    ![Bereitstellungsausgaben](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Vergrößern des Konsortiums

Um Ihr Konsortium zu erweitern, müssen Sie zuerst eine Verbindung mit dem physischen Netzwerk herstellen. Bei einer Bereitstellung hinter einem VPN helfen Ihnen die Informationen im Abschnitt [Herstellen einer Verbindung mit VNet Gateway-Instanzen](#connecting-vnet-gateways) weiter, um die Netzwerkverbindung im Rahmen der Bereitstellung neuer Mitglieder zu konfigurieren. Verwenden Sie nach Abschluss Ihrer Bereitstellung die [Governance-DApp](#governance-dapp), um zum Netzwerkadministrator zu werden.

### <a name="new-member-deployment"></a>Bereitstellung eines neuen Mitglieds

Stellen Sie für das beitretende Mitglied die folgenden Informationen bereit. Diese Informationen finden Sie in der E-Mail, die Sie nach der Bereitstellung erhalten, oder in der Ausgabe zur Bereitstellung im Portal.

* URL der Konsortiumsdaten
* Anzahl von bereitgestellten Knoten
* VNET-Gateway-Ressourcen-ID (bei Verwendung von VPN)

Das bereitstellende Mitglied sollte die gleiche Lösungsvorlage für das Ethereum-Proof-of-Authority-Konsortium verwenden, wenn die Netzwerkverfügbarkeit nach folgender Anleitung bereitgestellt wird:

* Wählen Sie **Vorhandener beitreten**.
* Wählen Sie die gleiche Anzahl von Validierungsknoten wie die anderen Mitglieder des Netzwerks aus, um eine faire Verteilung sicherzustellen.
* Verwenden Sie dieselbe Ethereum-Administratoradresse.
* Nutzen Sie die auf der Registerkarte mit den *Ethereum-Einstellungen* angegebene *URL der Konsortiumsdaten*.
* Wählen Sie unter „Erweitert“ die Option **Private VNet** (Privates VNET) aus, wenn sich der restliche Teil des Netzwerks hinter einem VPN befindet.

### <a name="connecting-vnet-gateways"></a>Herstellen einer Verbindung für VNET-Gateways

Dieser Abschnitt ist nur erforderlich, wenn Sie die Bereitstellung mit einem privaten VNET durchgeführt haben. Sie können diesen Abschnitt überspringen, wenn Sie öffentliche IP-Adressen verwenden.

Bei einem privaten Netzwerk werden die unterschiedlichen Mitglieder über VNET-Gatewayverbindungen vernetzt. Bevor ein Mitglied dem Netzwerk beitreten und Transaktionsdatenverkehr anzeigen lassen kann, muss ein vorhandenes Mitglied die Konfiguration auf seinem VPN-Gateway abschließen, um die Verbindung zu akzeptieren. Die Ethereum-Knoten des beitretenden Mitglieds werden erst ausgeführt, wenn die Verbindung hergestellt wurde. Sie können die Wahrscheinlichkeit eines Single Point of Failure zu reduzieren, indem Sie redundante Netzwerkverbindungen im Konsortium erstellen.

Nachdem das neue Mitglied bereitgestellt wurde, muss das vorhandene Mitglied die bidirektionale Verbindung herstellen, indem es eine VNET-Gatewayverbindung mit dem neuen Mitglied einrichtet. Das vorhandene Mitglied benötigt Folgendes:

* die VNET-Gateway-ResourceID des Mitglieds, das die Verbindung herstellt ([Ausgabe der Bereitstellung](#deployment-output))
* den Schlüssel für die gemeinsame Verbindung

Das vorhandene Mitglied muss das folgende PowerShell-Skript ausführen, um die Verbindung herzustellen. Sie können Azure Cloud Shell verwenden. Die entsprechende Option befindet sich in der rechten oberen Navigationsleiste des Portals.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Dienstüberwachung

Sie können auf Ihr Azure Monitor-Portal zugreifen, indem Sie entweder dem Link in der E-Mail zur Bereitstellung folgen oder in der Ausgabe der Bereitstellung nach dem Parameter [OMS_PORTAL_URL] suchen.

Im Portal werden zunächst eine allgemeine Netzwerkstatistik und eine Knotenübersicht angezeigt.

![Überwachungskategorien](./media/ethereum-poa-deployment/monitor-categories.png)

In der **Knotenübersicht** wird die Infrastrukturstatistik pro Knoten angezeigt.

![Knotenstatistik](./media/ethereum-poa-deployment/node-stats.png)

In der **Netzwerkstatistik** wird die Ethereum-Netzwerkstatistik angezeigt.

![Netzwerkstatistik](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

Sie können die Überwachungsprotokolle abfragen, um Fehler oder Warnungen für Setupschwellenwerte zu untersuchen. Die folgenden Abfragen sind Beispiele, die Sie in der *Protokollsuche* ausführen können:

Listenblöcke, die von mehr als einer Validierungssteuerelement-Abfrage gemeldet wurden, können hilfreich sein, um Kettenverzweigungen zu suchen.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Rufen Sie die durchschnittliche Peeranzahl für einen angegebenen Validierungsknoten ab (Mittelwert für Fünf-Minuten-Buckets).

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH-Zugriff

Aus Sicherheitsgründen wird der SSH-Portzugriff durch eine Netzwerksicherheitsgruppen-Regel standardmäßig verweigert. Sie müssen die folgende Sicherheitsregel in *Zulassen* ändern, um auf die Instanzen des virtuellen Computers im PoA-Netzwerk zugreifen zu können.

1. Wechseln Sie im Azure-Portal zum Abschnitt **Übersicht** der bereitgestellten Ressourcengruppe.

    ![SSH-Übersicht](./media/ethereum-poa-deployment/ssh-overview.png)

1. Wählen Sie die **Netzwerksicherheitsgruppe** für die Region der VM aus, auf die Sie zugreifen möchten.

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Wählen Sie die Regel **allow-ssh** aus.

    ![Bildschirmaufnahme, die ein Übersichtsfenster mit ausgewähltem „ssh-allow“ zeigt.](./media/ethereum-poa-deployment/ssh-allow.png)

1. Ändern Sie **Aktion** in **Zulassen**.

    ![SSH – Aktivieren von „Zulassen“](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Wählen Sie **Speichern** aus. Es kann einige Minuten dauern, bis die Änderungen wirksam werden.

Für die Validierungsknoten können Sie eine Remoteverbindung mit virtuellen Computern herstellen. Dazu verwenden Sie SSH mit dem angegebenen Administratorbenutzernamen und Kennwort/SSH-Schlüssel. Der SSH-Befehl für den Zugriff auf den ersten Validierungsknoten wird in der Vorlage für Bereitstellungsausgaben aufgeführt. Beispiel:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Erhöhen Sie die Portnummer um 1, um zusätzliche Transaktionsknoten zu erhalten.

Wenn Sie die Bereitstellung in mehr als einer Region durchgeführt haben, sollten Sie den Befehl in den DNS-Namen oder die IP-Adresse des Lastenausgleichs dieser Region ändern. Sie können den DNS-Namen oder die IP-Adresse der anderen Regionen ermitteln, indem Sie auf die Ressource mit der Namenskonvention **\*\*\*\*\*-lbpip-reg\#** zugreifen und die zugehörigen Eigenschaften für den DNS-Namen und die IP-Adresse anzeigen.

## <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager – Lastenausgleich

Mit Azure Traffic Manager lassen sich durch die Weiterleitung des eingehenden Datenverkehrs über mehrere Bereitstellungen in unterschiedlichen Regionen Ausfallzeiten reduzieren und die Reaktionsfähigkeit des PoA-Netzwerks verbessern. Durch integrierte Integritätsprüfungen und das automatische Neuberechnen von Routen wird die Hochverfügbarkeit für RPC-Endpunkte und die Governance-DApp sichergestellt. Dieses Feature ist nützlich, wenn Sie die Bereitstellung in mehreren Regionen durchgeführt haben und für die Produktion bereit sind.

Verwenden Sie Traffic Manager, um die Verfügbarkeit von PoA-Netzwerken mit automatischem Failover zu verbessern. Sie können mit Traffic Manager auch die Reaktionsfähigkeit verbessern, indem Endbenutzer an den Azure-Standort mit der geringsten Netzwerklatenz geleitet werden.

Wenn Sie sich dafür entscheiden, ein Traffic Manager-Profil zu erstellen, können Sie den DNS-Namen des Profils verwenden, um auf Ihr Netzwerk zuzugreifen. Nachdem dem Netzwerk weitere Konsortiumsmitglieder hinzugefügt wurden, kann Traffic Manager auch genutzt werden, um den Lastenausgleich für die bereitgestellten Validierungselemente zu laden.

### <a name="creating-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) in der linken oberen Ecke auf **Ressource erstellen**.
1. Suchen Sie nach **Traffic Manager-Profil**.

    ![Suche nach Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Geben Sie dem Profil einen eindeutigen Namen, und wählen Sie die Ressourcengruppe aus, die für die PoA-Bereitstellung verwendet wurde.

1. Klicken Sie auf **Erstellen**, um die Bereitstellung durchzuführen.

    ![Erstellen in Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Wählen Sie nach der Bereitstellung die Instanz in der Ressourcengruppe aus. Der DNS-Name für den Zugriff auf Traffic Manager befindet sich auf der Registerkarte „Übersicht“.

    ![Ermitteln des DNS-Namens für Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Klicken Sie auf die Registerkarte **Endpunkte** und dann auf **Hinzufügen**.
1. Geben Sie dem Endpunkt einen eindeutigen Namen.
1. Wählen Sie **Öffentliche IP-Adresse** als **Zielressourcentyp** aus.
1. Wählen Sie die öffentliche IP-Adresse für den Lastenausgleich der ersten Region aus.

    ![Routing für Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Wiederholen Sie dies für jede Region im bereitgestellten Netzwerk. Wenn sich die Endpunkte im Status **Aktiviert** befinden, werden dafür von Traffic Manager automatisch der Lastenausgleich und der Ausgleich nach Region für den DNS-Namen durchgeführt. Sie können diesen DNS-Namen jetzt anstelle des Parameters [CONSORTIUM_DATA_URL] in den anderen Schritten des Artikels verwenden.

## <a name="data-api"></a>Daten-API

Jedes Konsortiumsmitglied hostet die erforderlichen Informationen, über die andere Personen eine Verbindung mit dem Netzwerk herstellen können. Die Verbindungsherstellung kann vereinfacht werden, indem jedes Mitglied auf dem API-Datenendpunkt Verbindungsinformationen hostet.

Das vorhandene Mitglied gibt den Parameter [CONSORTIUM_DATA_URL] vor der Bereitstellung des Mitglieds an. Bei der Bereitstellung ruft ein beitretendes Mitglied Informationen über die JSON-Schnittstelle am folgenden Endpunkt ab:

`<CONSORTIUM_DATA_URL>/networkinfo`

Die Antwort enthält Informationen, die für den Beitritt von Mitgliedern hilfreich sind (Genesis-Block, Vertrags-ABI für die Validierungssteuerelemente, Startknoten), sowie Informationen, die für das vorhandene Mitglied hilfreich sind (Adressen der Validierungssteuerelemente). Sie können diese Standardisierung verwenden, um das Konsortium auf mehrere Cloudanbieter zu erweitern. Diese API gibt eine Antwort im JSON-Format mit der folgenden Struktur zurück:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Governance-DApp

Das Herzstück von Proof-of-Authority ist die dezentralisierte Governance-Komponente. Da Proof-of-Authority auf einer berechtigungsbasierten Liste mit Netzwerkautoritäten basiert, um das Netzwerk frei von Fehlern zu halten, ist Folgendes wichtig: Es muss ein fairer Mechanismus vorhanden sein, der zum Vornehmen von Änderungen an dieser Berechtigungsliste verwendet werden kann. Jede Bereitstellung verfügt über eine Gruppe von Smart Contracts und ein Portal für On-Chain-Governance-Maßnahmen für diese berechtigungsbasierte Liste. Nachdem eine vorgeschlagene Änderung bei den Konsortiumsmitgliedern die Stimmenmehrheit erzielt hat, wird die Änderung durchgeführt. Durch die Abstimmung können neue Konsensteilnehmer hinzugefügt oder kompromittierte Teilnehmer transparent zu entfernen, um für ein vertrauenswürdiges Netzwerk zu sorgen.

Die Governance-DApp umfasst vorab bereitgestellte [Smart Contracts](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) und eine Webanwendung, die zum Steuern der Autoritäten im Netzwerk verwendet werden. Die Autoritäten sind in Administratoridentitäten und Validierungsknoten unterteilt.
Administratoren können die Teilnahme am Konsens an einige Validierungsknoten delegieren. Außerdem können Administratoren andere Administratoren für das Netzwerk auswählen oder abwählen.

![Governance-DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Dezentralisierte Governance:** Änderungen der Netzwerkautoritäten werden von ausgewählten Administratoren per On-Chain-Abstimmung verwaltet.
* **Delegation von Validierungssteuerelementen:** Autoritäten können ihre Validierungsknoten verwalten, die für die einzelnen PoA-Bereitstellungen eingerichtet werden.
* **Überwachbarer Änderungsverlauf:** Jede Änderung wird für die Blockchain aufgezeichnet, um für Transparenz und Überwachbarkeit zu sorgen.

### <a name="getting-started-with-governance"></a>Erste Schritte mit Governance

Wenn Sie verschiedene Transaktionen über die Governance-DApp durchführen möchten, müssen Sie ein Ethereum-Wallet nutzen. Die Verwendung eines Wallets wie [MetaMask](https://metamask.io) im Browser stellt den einfachsten Ansatz dar. Da es sich hierbei jedoch um im Netzwerk bereitgestellte Smart Contracts handelt, müssen Sie Ihre Interaktionen mit dem Governancevertrag möglicherweise auch automatisieren.

Navigieren Sie nach der Installation von MetaMask zu der Governance-DApp im Browser.  Sie können die URL im Azure-Portal in der Bereitstellungsausgabe suchen.  Wenn Sie kein Wallet im Browser installiert haben, können Sie keine Aktionen ausführen. Sie können jedoch weiterhin den Administratorstatus anzeigen.  

### <a name="becoming-an-admin"></a>Administrator werden

Wenn Sie das erste Mitglied sind, das im Netzwerk eine Bereitstellung durchgeführt hat, werden Sie automatisch zum Administrator, und Ihre Paritätsknoten werden als Validierungssteuerelemente aufgeführt. Wenn Sie dem Netzwerk beitreten, müssen Sie von einer Mehrheit der vorhandenen Administratoren (mehr als 50 %) zum Administrator gewählt werden. Wenn Sie sich dagegen entscheiden, Administrator zu werden, synchronisieren und überprüfen Ihre Knoten weiterhin die Blockchain. Sie sind jedoch nicht an der Blockerstellung beteiligt. Klicken Sie zum Starten des Abstimmungsprozesses, um Administrator zu werden, auf **Nominierung**, und geben Sie Ihre Ethereum-Adresse und den Alias ein.

![Nominierung](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandidaten

Auf der Registerkarte **Kandidaten** werden Ihnen die aktuellen Bewerber für Administratoren angezeigt.  Sobald ein Kandidat eine Stimmenmehrheit durch die aktuellen Administratoren erreicht, wird er zum Administrator ernannt.  Wenn Sie für einen Kandidaten abstimmen möchten, wählen Sie die Zeile aus, und klicken Sie auf **Vote in** (Abstimmen). Wenn Sie Ihre Meinung zu einer abgegebenen Stimme ändern, können Sie den Kandidaten auswählen und auf **Rescind vote** (Abstimmung widerrufen) klicken.

![Kandidaten](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratoren

Auf der Registerkarte **Administratoren** wird die aktuelle Gruppe der Administratoren angezeigt, für die Sie über diese Registerkarte abstimmen können.  Sobald ein Administrator die Unterstützung von mehr als 50 % verliert, wird er als Administrator aus dem Netzwerk entfernt. Alle Validierungsknoten dieses Administrators verlieren ihren Status und werden im Netzwerk zu Transaktionsknoten. Für das Entfernen eines Administrators gibt es verschiedene Gründe. Das Konsortium muss sich jedoch vorab auf eine Richtlinie einigen.

![Administratoren](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validierungssteuerelemente

Durch die Auswahl der Registerkarte **Validierungssteuerelemente** werden die aktuell bereitgestellten Paritätsknoten dieser Instanz mit dem zugehörigen Status (Knotentyp) angezeigt. Jedem Mitglied des Konsortiums in dieser Liste werden andere Validierungssteuerelemente angezeigt, da in dieser Ansicht die aktuell bereitgestellten Mitglieder des Konsortiums dargestellt werden. Handelt es sich um eine neu bereitgestellte Instanz, zu der Sie Ihre Validierungssteuerelemente noch nicht hinzugefügt haben, wird Ihnen die Option **Add Validators** (Validierungssteuerelemente hinzufügen) angezeigt. Durch das Hinzufügen von Validierungssteuerelementen werden automatisch regional ausgewogene Paritätsknoten ausgewählt, die Ihren Validierungssteuerelementen zugewiesen werden. Wenn Sie mit Ihren bereitgestellten Knoten die zulässige Kapazität überschreiten, werden die verbleibenden Knoten zu Transaktionsknoten im Netzwerk.

Die Adresse der einzelnen Validierungssteuerelemente wird automatisch über den [Identitätsspeicher](#identity-store) in Azure zugewiesen.  Wenn ein Knoten ausfällt, wird seine Identität aufgegeben, wodurch ein anderer Knoten in Ihrer Bereitstellung den Platz dieses Knotens übernehmen kann. Dadurch wird sichergestellt, dass Ihre Teilnahme am Konsens hochverfügbar ist.

![Validierungssteuerelemente](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Konsortiumsname

Jeder Administrator kann den Namen des Konsortiums aktualisieren.  Klicken Sie links oben auf das Zahnradsymbol, um den Konsortiumsnamen zu aktualisieren.

### <a name="account-menu"></a>Kontomenü

Rechts oben befinden sich Ihr Ethereum-Kontoalias und -Identicon.  Wenn Sie Administrator sind, können Sie Ihren Alias aktualisieren.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum-Entwicklung<a id="tutorials"></a>

Es gibt einige Optionen für das Kompilieren, Bereitstellen und Testen von Smart Contracts, die Sie für die Ethereum-Entwicklung in Betracht ziehen können:
* [Truffle Suite:](https://www.trufflesuite.com/docs/truffle/overview) eine clientbasierte Entwicklungsumgebung für Ethereum
* [Ethereum Remix:](https://remix-ide.readthedocs.io/en/latest/index.html ) eine browserbasierte lokale Entwicklungsumgebung für Ethereum

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilieren, Bereitstellen und Ausführen von Smart Contracts

Im folgenden Beispiel erstellen Sie einen einfachen Smart Contract. Sie verwenden Truffle zum Kompilieren und Bereitstellen des Smart Contract in Ihrem Blockchainnetzwerk. Nach der Bereitstellung rufen Sie eine Smart Contract-Funktion über eine Transaktion auf.

#### <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python ist für Truffle und Web3 erforderlich. Wählen Sie die entsprechende Installationsoption aus, um Python in Ihren Pfad einzubeziehen.
* Installieren Sie Truffle 5.0.5 über `npm install -g truffle@v5.0.5`. Für Truffle ist die Installation mehrerer Tools erforderlich, dazu gehören [Node.js](https://nodejs.org) und [Git](https://git-scm.com/). Weitere Informationen finden Sie in der [Dokumentation zu Truffle](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Erstellen eines Truffle-Projekts

Bevor Sie einen Smart Contract kompilieren und bereitstellen können, müssen Sie ein Truffle-Projekt erstellen.

1. Öffnen Sie eine Eingabeaufforderung oder Shell.
1. Erstellen Sie einen Ordner mit dem Namen `HelloWorld`.
1. Ändern Sie in das Verzeichnis in neuen Ordner `HelloWorld`.
1. Initialisieren Sie mit dem Befehl `truffle init` ein neues Truffle-Projekt.

    ![Neues Truffle-Projekt erstellen](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Hinzufügen eines Smart Contract

Erstellen Sie Ihre Smart Contract im Unterverzeichnis **contracts** Ihres Truffle-Projekts.

1. Erstellen Sie eine Datei namens `postBox.sol` im Unterverzeichnis **contracts** Ihres Truffle-Projekts.
1. Fügen Sie folgenden Solidity-Code zu **postBox.sol** hinzu.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Bereitstellen eines Smart Contract mit Truffle

Truffle-Projekte enthalten eine Konfigurationsdatei für die Verbindungsdetails von Blockchainnetzwerken. Ändern Sie die Konfigurationsdatei, damit die Verbindungsinformationen für Ihr Netzwerk einbezogen werden.

> [!WARNING]
> Senden Sie niemals Ihren privaten Ethereum-Schlüssel über das Netzwerk. Stellen Sie sicher, dass jede Transaktion zuerst lokal signiert wird und die signierte Transaktion über das Netzwerk gesendet wird.

1. Sie benötigen den mnemonischen Satz für das [Ethereum-Administratorkonto, das für die Bereitstellung Ihres Blockchainnetzwerks verwendet wurde](#ethereum-settings). Wenn Sie das Konto mit MetaMask erstellt haben, können Sie den mnemonischen Satz über MetaMask abrufen. Klicken Sie in der MetaMask-Erweiterung oben rechts auf das Symbol für das Administratorkonto, und wählen Sie **Settings > Security & Privacy > Reveal Seed Words** (Einstellungen > Sicherheit und Datenschutz > Seedwörter anzeigen) aus.
1. Ersetzen Sie den Inhalt von `truffle-config.js` in Ihrem Truffle-Projekt durch Folgendes: Ersetzen Sie den Platzhalterendpunkt und den mnemonischen Wert.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Da Sie truffle-hdwallet-provider verwenden, müssen Sie das Modul mit dem Befehl `npm install truffle-hdwallet-provider --save` in Ihrem Projekt installieren.

Für Truffle werden Migrationsskripts verwendet, um Smart Contract in einem Blockchainnetzwerk bereitzustellen. Sie benötigen ein Migrationsskript, um den neuen Smart Contract bereitzustellen.

1. Fügen Sie eine neue Migration hinzu, um den neuen Smart Contract bereitzustellen. Erstellen Sie die Datei `2_deploy_contracts.js` im Unterverzeichnis **migrations** des Truffle-Projekts.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Führen Sie die Bereitstellung im PoA-Netzwerk mit dem Befehl „truffle migrate“ durch. Führen Sie über die Eingabeaufforderung im Truffle-Projektverzeichnis Folgendes aus:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Aufrufen einer Smart-Contract-Funktion

Nachdem Ihr Smart Contract bereitgestellt wurde, können Sie eine Transaktion senden, um eine Funktion aufzurufen.

1. Erstellen Sie im Truffle-Projektverzeichnis eine neue Datei namens `sendtransaction.js`.
1. Fügen Sie **sendtransaction. js-** den folgenden Inhalt hinzu.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Führen Sie das Skript mit dem Befehl „truffle exec“ aus.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Skriptausführung für einen Funktionsaufruf über eine Transaktion](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Unterstützung von WebAssembly (WASM)

Die WebAssembly-Unterstützung ist für neu bereitgestellte PoA-Netzwerke bereits für Sie aktiviert. Sie ermöglicht die Smart Contract-Entwicklung in jeder Sprache, mit der die Transpilierung für Web-Assembly (Rust, C, C++) möglich ist. Weitere Informationen finden Sie unter [Parity-Übersicht für WebAssembly](https://openethereum.github.io/WebAssembly-Home.html) und [Tutorial von Parity Technologies](https://github.com/paritytech/pwasm-tutorial).

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Mir sind im Netzwerk viele Transaktionen aufgefallen, die ich nicht gesendet habe. Woher kommen diese Transaktionen?

Es ist unsicher, die [persönliche API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) zu entsperren. Bots lauschen auf nicht gesperrte Ethereum-Konten und versuchen, das Guthaben abzuschöpfen. Der Bot nimmt an, dass diese Konten echtes Ether-Guthaben enthalten, und versucht, als Erster an das Guthaben zu gelangen. Sie sollten die persönliche API im Netzwerk nicht aktivieren. Signieren Sie die Transaktionen stattdessen entweder programmgesteuert oder manuell mithilfe eines Wallets wie MetaMask.

### <a name="how-to-ssh-onto-a-vm"></a>Wie stelle ich eine SSH-Verbindung mit einem virtuellen Computer her?

Der SSH-Port wird aus Sicherheitsgründen nicht verfügbar gemacht. Befolgen Sie [diese Anleitung zum Aktivieren des SSH-Ports](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Wie richte ich ein Überwachungsmitglied oder einen Transaktionsknoten ein?

Bei Transaktionsknoten handelt es sich um eine Reihe von Parity-Clients, die als Peers im Netzwerk vorhanden sind, aber nicht am Konsens teilnehmen. Diese Knoten können weiterhin verwendet werden, um Ethereum-Transaktionen zu übermitteln und den Smart Contract-Zustand zu lesen. Dieser Mechanismus funktioniert für die Bereitstellung der Überwachbarkeit für Konsortiumsmitglieder im Netzwerk, die keiner Autorität angehören. Befolgen Sie hierfür die Schritte unter [Vergrößern des Konsortiums](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Warum dauern MetaMask-Transaktionen so lange?

Um sicherzustellen, dass Transaktionen in der richtigen Reihenfolge empfangen werden, verfügt jede Ethereum-Transaktion über einen Nonce für die Inkrementierung. Wenn Sie ein Konto in MetaMask in einem anderen Netzwerk verwendet haben, müssen Sie den Nonce-Wert zurücksetzen. Klicken Sie auf das Symbol für die Einstellungen (drei Balken) und dann auf „Einstellungen > Konto zurücksetzen“. Der Transaktionsverlauf wird gelöscht, und Sie können die Transaktion erneut übermitteln.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Muss ich in MetaMask eine gas-Gebühr angeben?

Ether erfüllt im Proof-of-Authority-Konsortium keinen Zweck. Daher müssen Sie bei der Übermittlung von Transaktionen in MetaMask keine gas-Gebühr angeben.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Was kann ich tun, wenn meine Bereitstellung aufgrund einer fehlgeschlagenen Bereitstellung der Azure OMS fehlschlägt?

Die Überwachung ist ein optionales Feature. Wenn der seltene Fall eintritt, dass Ihre Bereitstellung fehlschlägt, weil die Azure Monitor-Ressource nicht bereitgestellt werden kann, können Sie die Bereitstellung ohne Azure Monitor noch mal durchführen.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Sind Bereitstellungen mit öffentlicher IP-Adresse mit Bereitstellungen privater Netzwerke kompatibel?

Nein. Für das Peering ist eine bidirektionale Kommunikation erforderlich, sodass das gesamte Netzwerk entweder öffentlich oder privat sein muss.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Welcher Transaktionsdurchsatz kann bei Proof-of-Authority erwartet werden?

Der Transaktionsdurchsatz hängt stark von den Transaktionstypen und der Netzwerktopologie ab. Bei der Verwendung einfacher Transaktionen haben wir durchschnittlich 400 Transaktionen pro Sekunde mit einem regionsübergreifenden Netzwerk gemessen.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Wie kann ich ein Abonnement für Smart Contract-Ereignisse abschließen?

Ethereum Proof-of Authority unterstützt jetzt auch Web-Sockets.  Suchen Sie in der Bereitstellungsausgabe nach der URL und dem Port des Websocket.

## <a name="support-and-feedback"></a>Support und Feedback

Neues zu Azure Blockchain: Mit dem [Azure Blockchain-Blog](https://azure.microsoft.com/blog/topics/blockchain/) bleiben Sie in Bezug auf Blockchain-Dienstangebote und Informationen vom Azure Blockchain-Technikteam immer auf dem Laufenden.

Über das [Azure-Feedbackforum für Blockchain](https://aka.ms/blockchainuservoice) können Sie Produktfeedback senden, neue Features anfordern oder über Ideen abstimmen.

### <a name="community-support"></a>Communityunterstützung

Diskutieren Sie mit Microsoft-Technikern und Azure Blockchain-Communityexperten.

* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-blockchain-workbench.html). Der technische Support für Blockchain-Vorlagen ist auf Bereitstellungsprobleme beschränkt.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Nächste Schritte

Weitere Azure Blockchain-Lösungen finden Sie in der [Dokumentation zu Azure Blockchain](../index.yml).
