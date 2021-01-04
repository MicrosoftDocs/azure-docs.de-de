---
title: Erstellen eines Verbindungsmonitors – PowerShell
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell einen Verbindungsmonitor erstellen.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 1d5f879ead35ef6d47b993ff833dc0b0595e3c6c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861917"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Erstellen eines Verbindungsmonitors mithilfe von PowerShell

Erfahren Sie, wie Sie mit dem Feature Verbindungsmonitor von Azure Network Watcher die Kommunikation zwischen Ihren Ressourcen überwachen.


## <a name="before-you-begin"></a>Voraussetzungen

In Verbindungsmonitoren, die Sie mit Verbindungsmonitor erstellen, können Sie sowohl lokale Computer als auch virtuelle Azure-Computer (Virtual Machines, VMs) als Quellen hinzufügen. Mit diesen Verbindungsmonitoren kann auch die Konnektivität mit Endpunkten überwacht werden. Die Endpunkte können sich in Azure oder einer beliebigen anderen URL oder IP-Adresse befinden.

Ein Verbindungsmonitor umfasst die folgenden Entitäten:

* **Verbindungsmonitorressource**: Eine regionsspezifische Azure-Ressource. Alle folgenden Entitäten sind Eigenschaften der Verbindungsmonitorressource.
* **Endpunkt**: Eine Quelle oder ein Ziel, die bzw. das an Konnektivitätsprüfungen beteiligt ist. Beispiele für Endpunkte umfassen Azure-VMs, lokale Agents, URLs und IP-Adressen.
* **Testkonfiguration**: Eine protokollspezifische Konfiguration für einen Test. Je nach ausgewähltem Protokoll können Sie den Port, Schwellenwerte, die Testhäufigkeit und weitere Parameter definieren.
* **Testgruppe**: Die Gruppe, die Quellendpunkte, Zielendpunkte und Testkonfigurationen enthält. Ein Verbindungsmonitor kann mehrere Testgruppen enthalten.
* **Test**: Die Kombination aus einem Quellendpunkt, einem Zielendpunkt und einer Testkonfiguration. Ein Test ist die differenzierteste Ebene, auf der Überwachungsdaten verfügbar sind. Die Überwachungsdaten umfassen den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit.

    ![Diagramm eines Verbindungsmonitors, in dem die Beziehung zwischen Testgruppen und Tests definiert ist.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Schritte zum Erstellen eines Verbindungsmonitors

Verwenden Sie die folgenden Befehle, um über PowerShell einen Verbindungsmonitor zu erstellen.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Beschreibung der Eigenschaften

* **ConnectionMonitorName**: Der Name der Verbindungsmonitorressource.

* **SUB**: Die Abonnement-ID des Abonnements, in dem Sie einen Verbindungsmonitor erstellen möchten.

* **NW**: Die ID der Network Watcher-Ressource, in der ein Verbindungsmonitor erstellt wird.

* **Standort**: Die Region, in der ein Verbindungsmonitor erstellt wird.

* **Endpunkte**
    * **Name**: Der eindeutige Name für jeden Endpunkt.
    * **Ressourcen-ID:** Bei Azure-Endpunkten bezieht sich die Ressourcen-ID auf die ID der Azure Resource Manager-Ressource für VMs. Bei Nicht-Azure-Endpunkten bezieht sich die Ressourcen-ID auf die ID der Azure Resource Manager-Ressource für den Log Analytics-Arbeitsbereich, der mit Nicht-Azure-Agents verknüpft ist.
    * **Adresse**: Nur zutreffend, wenn keine Ressourcen-ID angegeben wurde oder sich die Ressourcen-ID im Log Analytics-Arbeitsbereich befindet. Bei Verwendung ohne Ressourcen-ID kann dies die URL oder IP-Adresse eines beliebigen öffentlichen Endpunkts sein. Bei Verwendung mit einer Log Analytics-Ressourcen-ID bezieht sich dies auf den FQDN des Überwachungs-Agents.
    * **Filter**: Bei Nicht-Azure-Endpunkten verwenden Sie Filter, um Überwachungs-Agents aus dem Log Analytics-Arbeitsbereich in der Verbindungsmonitorressource auszuwählen. Wenn keine Filter festgelegt sind, können alle Agents, die zum Log Analytics-Arbeitsbereich gehören, für die Überwachung verwendet werden.
        * **Typ:** Legen Sie den Typ als **Agent-Adresse** fest.
        * **Adresse**: Legen Sie die Adresse als den FQDN Ihres lokalen Agents fest.

* **Testgruppen**
    * **Name**: Geben Sie Ihrer Testgruppe einen Namen.
    * **Quellen**: Treffen Sie eine Auswahl aus den zuvor erstellten Endpunkten. Auf Azure-basierten Quellendpunkten muss die Azure Network Watcher-Erweiterung installiert sein, und bei nicht auf Azure basierenden Quellendpunkten muss ein Azure Log Analytics-Agent installiert sein. Informationen zum Installieren eines Agents für Ihre Quelle finden Sie unter [Installieren von Überwachungs-Agents](./connection-monitor-overview.md#install-monitoring-agents).
    * **Ziele:**  Treffen Sie eine Auswahl aus den zuvor erstellten Endpunkten. Sie können die Konnektivität mit Azure-VMs oder anderen Endpunkten (öffentliche IP-Adresse, URL oder FQDN) überwachen, indem Sie diese als Ziele festlegen. In einer einzelnen Testgruppe können Sie Azure-VMs, Office 365-URLs, Dynamics 365-URLs und benutzerdefinierte Endpunkte hinzufügen.
    * **Deaktivieren**: Deaktivieren Sie die Überwachung für alle in der Testgruppe angegebenen Quellen und Ziele.

* **Testkonfigurationen**
    * **Name**: Geben Sie der Testkonfiguration einen Namen.
    * **TestFrequencySec**: Geben Sie an, wie oft die Quellen die Ziele über das angegebene Protokoll und den angegebenen Port pingen. Sie können Intervalle von 30 Sekunden, 1 Minute, 5 Minuten, 15 Minuten oder 30 Minuten auswählen. Die Quellen testen die Konnektivität mit den Zielen basierend auf dem von Ihnen ausgewählten Wert. Wenn Sie z. B. 30 Sekunden auswählen, überprüfen die Quellen die Konnektivität mit dem Ziel mindestens einmal alle 30 Sekunden.
    * **Protokoll:** Wählen Sie TCP, ICMP, HTTP oder HTTPS aus. Je nach Protokoll können Sie auch die folgenden protokollspezifischen Konfigurationen auswählen:
        * **preferHTTPS**: Geben Sie an, ob vorzugsweise HTTPS statt HTTP verwendet werden soll.
        * **port**: Geben Sie den Zielport Ihrer Wahl an.
        * **disableTraceRoute**: Verhindern Sie, dass Quellen weder die Topologie noch die Hop-by-Hop-Roundtripzeit ermitteln. Dies gilt für Testgruppen mit TCP oder ICMP.
        * **method**: Wählen Sie die HTTP-Anforderungsmethode (GET oder POST) aus. Dies gilt für Testkonfigurationen mit HTTP.
        * **path**: Geben Sie Pfadparameter an, die an die URL angefügt werden sollen.
        * **validStatusCodes**: Wählen Sie die anwendbaren Statuscodes aus. Wenn der Antwortcode nicht damit übereinstimmt, wird eine Diagnosemeldung angezeigt.
        * **requestHeaders**: Geben Sie benutzerdefinierte Anforderungsheader-Zeichenfolgen an, die an das Ziel übergeben werden.
    * **Schwellenwert für erfolgreichen Vorgang**: Legen Sie Schwellenwerte für die folgenden Netzwerkparameter fest:
        * **checksFailedPercent**: Legen Sie den Prozentsatz der Überprüfungen fest, die Fehler aufweisen können, wenn Quellen die Konnektivität mit Zielen anhand der von Ihnen angegebenen Kriterien überprüfen. Beim TCP- oder ICMP-Protokoll kann der Prozentsatz der fehlerhaften Überprüfungen dem Prozentsatz der Paketverluste entsprechen. Beim HTTP-Protokoll stellt dieses Feld den Prozentsatz von HTTP-Anforderungen dar, die keine Antwort erhielen.
        * **roundTripTimeMs**: Legen Sie die Zeit in Millisekunden fest, die von Quellen zum Herstellen einer Verbindung mit dem Ziel über die Testkonfiguration benötigt werden darf.

## <a name="scale-limits"></a>Skalierungslimits

Für Verbindungsmonitore gelten die folgenden Skalierungslimits:

* Maximale Anzahl von Verbindungsmonitoren pro Abonnement und Region: 100
* Maximale Anzahl von Testgruppen pro Verbindungsmonitor: 20
* Maximale Anzahl von Quellen und Zielen pro Verbindungsmonitor: 100
* Maximale Anzahl von Testkonfigurationen pro Verbindungsmonitor:

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Überwachungsdaten analysieren und Warnungen festlegen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Erfahren Sie, wie Sie [Probleme in Ihrem Netzwerk diagnostizieren](./connection-monitor-overview.md#diagnose-issues-in-your-network).