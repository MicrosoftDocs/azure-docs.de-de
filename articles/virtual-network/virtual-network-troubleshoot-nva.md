---
title: Behandlung von Problemen mit virtuellen Netzwerkappliances in Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Probleme mit virtuellen Netzwerkappliances in Azure behoben werden.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 00393395745ca96ae14269ae80e4f3d25673fbfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723012"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Probleme mit virtuellen Netzwerkappliances in Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bei der Verwendung einer virtuellen Netzwerkappliance (Network Virtual Appliance, NVA) eines Drittanbieters in Microsoft Azure, treten unter Umständen Probleme und Fehler mit der VM- oder VPN-Konnektivität auf. Dieser Artikel enthält die wesentlichen Schritte, um grundlegende Anforderungen an die Azure-Plattform für NVA-Konfigurationen zu überprüfen.

Technischer Support für NVAs von Drittanbietern und deren Integration in die Azure-Plattform wird vom NVA-Anbieter bereitgestellt.

> [!NOTE]
> Bei einem Konnektivitäts- oder Routingproblem, das eine NVA betrifft, sollten Sie [sich direkt an den Anbieter der NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) wenden.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Checkliste für die Problembehandlung mit NVA-Anbietern

- Softwareupdates für NVA-VM-Software
- Einrichtung und Funktionalität von Dienstkonten
- Benutzerdefinierte Routen (User-Defined Routes, UDRs) in Subnetzen virtueller Netzwerke, die Datenverkehr an die NVA weiterleiten
- UDRs in Subnetzen virtueller Netzwerke, die Datenverkehr von einer NVA weiterleiten
- Weiterleiten von Tabellen und Regeln in der NVA (z.B. von NIC1 an NIC2)
- Ablaufverfolgung für NVA-NICs, um das Empfangen und Senden von Netzwerkdatenverkehr zu überprüfen
- Bei Verwendung einer Standard-SKU und öffentlicher IP-Adressen müssen eine NSG und eine explizite Regel erstellt werden, damit der Datenverkehr an die NVA weitergeleitet werden darf.

## <a name="basic-troubleshooting-steps"></a>Grundlegende Schritte zur Problembehandlung

- Überprüfen der grundlegenden Konfiguration
- Überprüfen der NVA-Leistung
- Erweiterte Behandlung von Netzwerkproblemen

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Überprüfen der Mindestanforderungen für die Konfiguration von NVAs in Azure

Für jede NVA müssen grundlegende Konfigurationsanforderungen erfüllt werden, damit sie ordnungsgemäß in Azure ausgeführt werden kann. Der folgende Abschnitt enthält die Schritte zum Überprüfen dieser grundlegenden Konfigurationen. Weitere Informationen [erhalten Sie beim Anbieter der NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Überprüfen, ob die IP-Weiterleitung für die NVA aktiviert ist**

Verwenden des Azure-Portals

1. Suchen Sie die NVA-Ressource im [Azure-Portal](https://portal.azure.com), klicken Sie auf „Netzwerke“, und wählen Sie dann „Netzwerkschnittstelle“ aus.
2. Wählen Sie auf der Seite „Netzwerkschnittstelle“ „IP-Konfiguration“ aus.
3. Stellen Sie sicher, dass IP-Weiterleitung aktiviert ist.

Verwenden von PowerShell

1. Öffnen Sie PowerShell, und melden Sie sich dann bei Ihrem Azure-Konto an.
2. Führen Sie den folgenden Befehl aus (ersetzen Sie die in Klammern stehenden Werte durch Ihre Informationen):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Überprüfen Sie die Eigenschaft **EnableIPForwarding**.
4. Wenn die IP-Weiterleitung nicht aktiviert ist, führen Sie die folgenden Befehle aus, um diese zu aktivieren:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Bei Verwendung einer Standard-SKU und öffentlichen IP-Adresse muss eine NSG vorhanden sein** Bei Verwendung einer Standard-SKU und öffentlicher IP-Adressen müssen eine NSG und eine explizite Regel erstellt werden, damit der Datenverkehr an die NVA weitergeleitet werden darf.

**Überprüfen, ob der Datenverkehr an die NVA weitergeleitet werden kann**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) unter **Network Watcher** die Option **Nächster Hop** aus.
2. Geben Sie eine VM an, die für das Umleiten des Datenverkehrs an die NVA konfiguriert ist, und eine IP-Zieladresse, unter der der nächste Hop angezeigt werden kann. 
3. Wenn die NVA nicht als **Nächster Hop** aufgeführt ist, überprüfen und aktualisieren Sie die Azure-Routingtabellen.

**Überprüfen, ob der Datenverkehr die NVA erreichen kann**

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) unter **Network Watcher** **IP-Flussüberprüfung** aus. 
2. Geben Sie die VM und die IP-Adresse der NVA an, und überprüfen Sie dann, ob der Datenverkehr durch eine Netzwerksicherheitsgruppe (NSG) blockiert wird.
3. Wenn eine NSG-Regel vorhanden ist, die den Datenverkehr blockiert, suchen Sie die NSG in den **effektiven Sicherheitsregeln**, und aktualisieren Sie sie dann, indem Sie die Weiterleitung von Datenverkehr zulassen. Führen Sie dann erneut **IP-Flussüberprüfung** aus, und testen Sie mit **Problembehandlung für Verbindung** die TCP-Kommunikation zwischen der VM und Ihrer internen oder externen IP-Adresse.

**Überprüfen, ob die NVA und die VMs auf erwarteten Datenverkehr lauschen**

1. Stellen Sie über RDP oder SSH eine Verbindung mit der NVA her, und führen Sie dann folgenden Befehl aus:

    Windows:

        netstat -an

    Linux:

        netstat -an | grep -i listen
2. Wenn der TCP-Port, der von der in den Ergebnissen aufgeführten NVA-Software verwendet wird, nicht angezeigt wird, müssen Sie die Anwendung in der NVA und auf der VM so konfigurieren, dass sie auf Datenverkehr, der auf diesen Ports eingeht, lauscht und antwortet. [Wenden Sie sich an den NVA-Anbieter, wenn Sie Unterstützung benötigen sollten](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Überprüfen der NVA-Leistung

### <a name="validate-vm-cpu"></a>Überprüfen der VM-CPU

Wenn die CPU-Auslastung nahezu 100 Prozent erreicht, kann es zu Problemen kommen, die sich auf die Netzwerkpaketverluste auswirken. Ihre VM meldet eine durchschnittliche CPU-Auslastung für einen bestimmten Zeitraum im Azure-Portal. Untersuchen Sie während einer CPU-Spitze, welcher Prozess auf der Gast-VM für die hohe CPU-Auslastung verantwortlich ist, und beheben Sie dies, wenn möglich. Eventuell müssen Sie auch die Größe der VM in eine größere SKU-Größe ändern oder bei VM-Skalierungsgruppen die Anzahl der Instanzen erhöhen bzw. auf die automatische Skalierung der CPU-Auslastung festlegen. [Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung zu einem dieser Probleme benötigen sollten.

### <a name="validate-vm-network-statistics"></a>Überprüfen der VM-Netzwerkstatistik

Wenn das VM-Netzwerk Spitzen oder Zeiträume mit hoher Auslastung aufweist, müssen Sie möglicherweise auch die SKU-Größe der VM erhöhen, um von einem höherem Durchsatz zu profitieren. Sie können die VM auch erneut bereitstellen, indem Sie „Beschleunigter Netzwerkbetrieb“ aktivieren. Um zu überprüfen, ob die NVA das Feature „Beschleunigter Netzwerkbetrieb“ unterstützt, [wenden Sie sich an den NVA-Anbieter, um ggf. weitere Unterstützung zu erhalten](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="advanced-network-administrator-troubleshooting"></a>Erweiterte Behandlung von Problemen mit Netzwerkadministratoren

### <a name="capture-network-trace"></a>Erfassen der Netzwerkablaufverfolgung
Erfassen Sie während der Ausführung von **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** oder **Nmap** eine gleichzeitige Netzwerkablaufverfolgung auf der Quell-VM, der NVA und der Ziel-VM, und beenden Sie dann die Ablaufverfolgung.

1. Um eine gleichzeitige Netzwerkablaufverfolgung zu erfassen, führen Sie den folgenden Befehl aus:

   **Für Windows**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **Für Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Verwenden Sie **PsPing** oder **Nmap** von der Quell-VM zur Ziel-VM (z.B. `PsPing 10.0.0.4:80` oder `Nmap -p 80 10.0.0.4`).
3. Öffnen Sie mit dem [Netzwerkmonitor](https://www.microsoft.com/download/details.aspx?id=4865) oder mit tcpdump die Netzwerkablaufverfolgung von der Ziel-VM. Wenden Sie einen Anzeigefilter für die IP-Adresse der Quell-VM an, über die Sie **PsPing** oder **Nmap** ausgeführt haben, wie z.B. `IPv4.address==10.0.0.4 (Windows netmon)` oder `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analysieren von Ablaufverfolgungen

Falls die bei der Ablaufverfolgung der Back-End-VM eingehenden Pakete nicht zu sehen sind, liegt wahrscheinlich ein Konflikt mit einer fehlerhaften NSG oder UDR oder fehlerhaften NVA-Routingtabellen vor.

Wenn Sie den Eingang der Pakete beobachten, aber keine Antwort erfolgt, müssen Sie möglicherweise ein Problem mit der VM-Anwendung oder der Firewall beheben. [Wenden Sie sich an den NVA-Anbieter](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), wenn Sie Unterstützung zu einem dieser Probleme benötigen sollten.