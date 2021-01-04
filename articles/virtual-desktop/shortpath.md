---
title: Windows Virtual Desktop – RDP Shortpath (Vorschau)
titleSuffix: Azure
description: Einrichten von RDP Shortpath (Vorschau) für Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 6ffe631dc237e7efaf1d6bfd9ac79ab7431c7371
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023138"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows Virtual Desktop – RDP Shortpath (Vorschau)

> [!IMPORTANT]
> RDP Shortpath befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP Shortpath ist ein Feature von Windows Virtual Desktop und ermöglicht für einen direkten UDP-basierten Transport zwischen Remotedesktopclient und Sitzungshost. RDP stellt mit diesem Transport Remotedesktop und RemoteApp bereit und sorgt gleichzeitig für mehr Zuverlässigkeit und konsistente Wartezeiten.

## <a name="key-benefits"></a>Hauptvorteile

* Der Transport von RDP Shortpath basiert auf dem äußerst effizienten [Universal Rate Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP ergänzt UDP um die aktive Überwachung der Netzwerkbedingungen und gewährleistet eine angemessene und vollständige Verbindungsnutzung. URCP arbeitet mit den für Remotedesktop erforderlichen niedrigen Verzögerungs- und Ausfallraten. Durch dynamisches Erlernen von Netzwerkparametern und Bereitstellen eines Protokolls mit Ratensteuerungsmechanismus kann URCP eine optimale Leistung erzielen.
* RDP Shortpath stellt die direkte Konnektivität zwischen Remotedesktopclient und Sitzungshost her. Eine direkte Konnektivität reduziert die Abhängigkeit von den Windows Virtual Desktop-Gateways, erhöht die Zuverlässigkeit der Verbindung und sorgt bei jeder Benutzersitzung für mehr verfügbare Bandbreite.
* Der Verzicht auf ein zusätzliches Relay reduziert die Roundtripzeit und verbessert dadurch die Benutzererfahrung mit latenzempfindlichen Anwendungen und Eingabemethoden.
* RDP Shortpath unterstützt durch die Markierung mit DCSP (Differentiated Services Code Point) die [Konfiguration der Quality of Service (QoS)-Priorität](./rdp-quality-of-service-qos.md) für RDP-Verbindungen.
* Beim RDP Shortpath-Transport wird durch Angabe einer Drosselungsrate für jede Sitzung die [Beschränkung des ausgehenden Netzwerkdatenverkehrs](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) ermöglicht.

## <a name="connection-security"></a>Verbindungssicherheit

RDP Shortpath ergänzt die RDP-Mehrfachtransportfunktionen. Es soll den Reverse Connection-Transport nicht ersetzen, sondern ergänzen. Die Verwaltung des gesamten anfänglichen Sitzungsbrokerings übernimmt die Windows Virtual Desktop-Infrastruktur.

UDP-Port 3390 wird nur für den eingehenden Shortpath-Datenverkehr verwendet, der über den Reverse Connection-Transport authentifiziert wird. Der RDP Shortpath-Listener ignoriert alle Verbindungsversuche mit dem Listener, solange sie nicht zu der Reverse Connection-Sitzung passen.

RDP Shortpath verwendet eine TLS-Verbindung zwischen Client und Sitzungshost und nutzt dafür die Zertifikate des Sitzungshosts. Standardmäßig wird das Zertifikat, das für die RDP-Verschlüsselung verwendet wird, vom Betriebssystem bei der Bereitstellung selbst generiert. Bei Bedarf können Kunden zentral verwaltete Zertifikate bereitstellen, die von der Unternehmenszertifizierungsstelle ausgestellt werden. Weitere Informationen zur Konfiguration von Zertifikaten finden Sie in der [Windows Server-Dokumentation](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath-Verbindungssequenz

Nach der Installation des [Reverse Connection-Transports](./network-connectivity.md) stellen Client und Sitzungshost die RDP-Verbindung her und handeln Mehrfachtransportfunktionen aus. Nachfolgend werden weitere Schritte beschrieben:

1. Der Sitzungshost sendet die Liste der privaten und öffentlichen IPv4- und IPv6-Adressen an den Client.
2. Der Client startet den Hintergrundthread, um einen parallelen UDP-basierten Transport direkt zu einer der IP-Adressen des Hosts einzurichten.
3. Während der Client die angegebenen IP-Adressen prüft, setzt er die anfängliche Verbindungsherstellung über den Reverse Connection-Transport fort, um jegliche Verzögerung in der Benutzerverbindung zu vermeiden.
4. Wenn der Client eine direkte Sichtverbindung hat und die Konfiguration der Firewall korrekt ist, stellt der Client eine sichere TLS-Verbindung mit dem Sitzungshost her.
5. Nach dem Einrichten des Shortpath-Transports verschiebt RDP alle dynamischen virtuellen Kanäle (Dynamic Virtual Channels, DVCs), einschließlich Remotegrafik, Eingabe und Geräteumleitung, zum neuen Transport.
6. Wenn eine Firewall oder die Netzwerktopologie den Client daran hindert, eine direkte UDP-Verbindung herzustellen, setzt RDP den Vorgang mit einem Reverse Connection-Transport fort.

Das folgende Diagramm bietet eine allgemeine Übersicht über die RDP Shortpath-Netzwerkverbindung.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagramm der RDP Shortpath-Netzwerkverbindungen" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Requirements (Anforderungen)

Zur Unterstützung von RDP Shortpath muss der virtuelle Windows Virtual Desktop-Client über eine direkte Sichtverbindung zum Sitzungshost verfügen. Eine direkte Sichtverbindung können Sie mit einer der folgenden Technologien herstellen:

* [Privates ExpressRoute-Peering](../expressroute/expressroute-circuit-peerings.md)
* [Site-to-Site-VPN (IPsec-basiert)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Point-to-Site-VPN (IPsec-basiert)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Zuweisung der öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md)

Wenn Sie für eine Verbindung mit dem virtuellen Azure-Netzwerk andere VPN-Arten verwenden, empfiehlt es sich, ein UDP-basiertes VPN zu verwenden, um optimale Ergebnisse zu erzielen. Der überwiegende Teil der TCP-basierten VPN-Lösungen kapselt zwar alle IP-Pakete (einschließlich UDP), fügt aber als geerbten Mehraufwand die TCP-Überlastungssteuerung hinzu, welche die RDP-Leistung verlangsamt.

Direkte Sichtverbindung bedeutet, dass Firewalls den UDP-Port 3390 nicht blockieren und der Client eine direkte Verbindung mit dem Sitzungshost herstellen kann.

## <a name="enabling-rdp-shortpath-preview"></a>Aktivieren der RDP Shortpath-Vorschauversion

Wenn Sie die Vorschauversion von RDP Shortpath nutzen möchten, müssen Sie den RDP Shortpath-Listener auf dem Sitzungshost aktivieren. Sie können RDP Shortpath für eine beliebige Anzahl von Sitzungshosts aktivieren, die in Ihrer Umgebung verwendet werden. Es ist nicht erforderlich, RDP Shortpath auf allen Hosts im Pool zu aktivieren.
Zum Aktivieren des Shortpath-Listeners müssen Sie die folgenden Registrierungswerte konfigurieren:

> [!WARNING]
> Wenn Sie die Registrierung mithilfe des Registrierungs-Editors oder einer anderen Methode ändern und dabei Fehler machen, kann dies zu schwerwiegenden Problemen führen. Unter Umständen ist in einem solchen Fall sogar die Neuinstallation des Betriebssystems erforderlich. Microsoft garantiert nicht, dass diese Probleme behoben werden können. Das Bearbeiten der Registrierung erfolgt auf eigenes Risiko.

1. Starten Sie „regedit.exe“ auf dem Sitzungshost, und navigieren Sie dann zu folgendem Speicherort:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Erstellen Sie einen neuen **DWORD**-Wert namens **fUseUdpPortRedirector**, und legen Sie ihn auf **1** (dezimal) fest.
3. Erstellen Sie einen neuen **DWORD**-Wert namens **UdpPortNumber**, und legen Sie ihn auf **3390** (dezimal) fest.
4. Beenden Sie den Registrierungs-Editor.
5. Erneutes Starten des Sitzungshosts

Sie können auch die folgenden Cmdlets in einem PowerShell-Fenster mit erhöhten Rechten ausführen, um die folgenden Registrierungswerte festzulegen:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Sie können auch mit PowerShell eine Gruppenrichtlinie konfigurieren:

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Konfigurieren von Windows Defender Firewall mit erweiterter Sicherheit

Um eingehenden Netzwerkdatenverkehr für RDP Shortpath zuzulassen, erstellen Sie mithilfe von Windows Defender Firewall und dem Knoten „Erweiterte Sicherheit“ im MMC-Snap-In der Gruppenrichtlinienverwaltung die Firewallregeln.

1. Öffnen Sie die Gruppenrichtlinien-Verwaltungskonsole für [Windows Defender Firewall mit erweiterter Sicherheit](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. Wählen Sie im Navigationsbereich **Regeln für eingehenden Datenverkehr** aus.
3. Wählen Sie **Aktion** und dann **Neue Regel** aus.
4. Wählen Sie im Assistenten für neue Regeln für eingehenden Datenverkehr auf der Seite **Regeltyp** die Option **Benutzerdefiniert** aus, und wählen Sie dann **Weiter** aus.
5. Wählen Sie auf der Seite **Programm** die Option **Dieser Programmpfad** aus, geben Sie „%SystemRoot%\system32\svchost.exe“ ein, und wählen Sie dann **Weiter** aus.
6. Wählen Sie auf der Seite **Protokoll und Ports** „UDP“ als Protokolltyp aus. Wählen Sie unter **Lokaler Port** die Option „Bestimmte Ports“ aus, und geben Sie „3390“ ein.
7. Auf der Seite **Bereich** können Sie angeben, dass die Regel nur für Netzwerkdatenverkehr zu und von den auf dieser Seite eingegebenen IP-Adressen gelten soll. Nehmen Sie die Konfiguration entsprechend Ihrem Design vor, und wählen Sie dann **Weiter** aus.
8. Wählen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** aus, und wählen Sie dann **Weiter** aus.
9. Wählen Sie auf der Seite **Profil** die Netzwerkadresstypen aus, für die diese Regel gilt, und wählen Sie dann **Weiter** aus.
10. Geben Sie auf der Seite **Name** einen Namen und eine Beschreibung für die Regel ein, und wählen Sie dann **Fertig stellen** aus.

Sie können überprüfen, ob die neue Regel den folgenden Screenshots entspricht: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="Screenshot der Registerkarte „Allgemein“ für die Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Screenshot der Registerkarte „Programme und Dienste“ für die Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Screenshot der Registerkarte „Protokolle und Ports“ für die Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Sie können die Windows-Firewall auch mit PowerShell konfigurieren:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Verwenden von PowerShell zum Konfigurieren von Windows Defender Firewall

Sie können auch mit PowerShell eine Gruppenrichtlinie konfigurieren:

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Konfigurieren einer Azure-Netzwerksicherheitsgruppe

Wenn Sie den Zugriff auf den RDP Shortpath-Listener über Netzwerksicherheitsgrenzen hinweg zulassen möchten, müssen Sie die Azure-Netzwerksicherheitsgruppe konfigurieren und UDP-Port 3390 für den eingehenden Datenverkehr zulassen.
Beachten Sie die [Dokumentation zu Netzwerksicherheitsgruppen](../virtual-machines/windows/nsg-quickstart-portal.md), um mit den folgenden Parametern eine Sicherheitsregel für den eingehenden Datenverkehr zu erstellen, die den Datenverkehr erlaubt:

* **Quelle** - **Beliebig** oder der IP-Adressbereich, in dem sich die Clients befinden
* **Quellportbereiche** – * *\** _ _ **Ziel** - **Beliebig**
* **Zielportbereiche** - **3390**
* **Protokoll** - **UDP**
* **Aktion** - **Zulassen**
* Optional können Sie die **Priorität** ändern. Die Priorität wirkt sich auf die Reihenfolge aus, in der Regeln angewendet werden: je niedriger der numerische Wert, desto früher wird die Regel angewendet.
* **Name** – **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Deaktivieren von RDP Shortpath für ein bestimmtes Subnetz

Wenn bestimmte Subnetze für die Verwendung des RDP Shortpath-Transports gesperrt werden müssen, können Sie weitere Netzwerksicherheitsgruppen konfigurieren und die Quell-IP-Adressbereiche angeben.

## <a name="verifying-the-connectivity"></a>Überprüfen der Konnektivität

### <a name="using-connection-information-dialog"></a>Im Dialogfeld „Verbindungsinformationen“

Wenn Sie überprüfen möchten, ob die Verbindungen RDP Shortpath verwenden, öffnen Sie das Dialogfeld „Verbindungsinformationen“, indem Sie auf der Verbindungssymbolleiste auf das Antennensymbol klicken.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Abbildung der Remotedesktop-Verbindungssymbolleiste":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Abbildung des Remotedesktop-Dialogfelds „Verbindungsinformationen“":::

### <a name="using-event-logs"></a>Mit Ereignisprotokollen

So überprüfen Sie, ob die Sitzung RDP Shortpath-Transport verwendet:

1. Stellen Sie eine Verbindung mit dem Desktop der VM mithilfe des Windows Virtual Desktop-Clients her.
2. Starten Sie die Ereignisanzeige, und navigieren Sie zum folgenden Knoten: **Anwendungs- und Dienstprotokolle > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Um festzustellen, ob RDP Shortpath-Transport verwendet wird, suchen Sie nach der Ereignis-ID 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Überprüfen der Shortpath-Konnektivität mit Log Analytics

Wenn Sie [Azure Log Analytics](./diagnostics-log-analytics.md) verwenden, können Sie durch Abfragen der [WVDConnections-Tabelle](/azure/azure-monitor/reference/tables/wvdconnections) Verbindungen überwachen. Die Spalte „UdpUse“ gibt an, ob der Windows Virtual Desktop-RDP-Stapel das UDP-Protokoll für die aktuelle Benutzerverbindung verwendet.
Mögliche Werte sind:

* **0**: Benutzerverbindung verwendet RDP Shortpath nicht
* **1**: Benutzerverbindung verwendet RDP Shortpath
  
Mit der folgenden Abfrageliste können Sie Verbindungsinformationen überprüfen. Sie können diese Abfrage im [Log Analytics-Abfrage-Editor](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query) ausführen. Ersetzen Sie für jede Abfrage `userupn` durch den UPN des Benutzers, den Sie suchen möchten.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="verify-shortpath-listener"></a>Überprüfen des Shortpath-Listeners

Mit dem folgenden PowerShell-Befehl auf dem Sitzungshost können Sie überprüfen, ob der UDP-Listener aktiviert ist:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Wenn er aktiviert ist, sollte eine Ausgabe wie die folgende angezeigt werden:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Wenn ein Konflikt besteht, können Sie mit dem folgenden Befehl den Prozess identifizieren, der den Port belegt:

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Deaktivieren von RDP Shortpath

In einigen Fällen müssen Sie möglicherweise den RDP Shortpath-Transport deaktivieren. RDP Shortpath wird mithilfe der Gruppenrichtlinie deaktiviert.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Deaktivieren von RDP Shortpath auf dem Client

Um RDP Shortpath für einen bestimmten Client zu deaktivieren, können Sie die folgende Gruppenrichtlinie verwenden und die UDP-Unterstützung deaktivieren:

1. Führen Sie auf dem Client **gpedit.msc** aus.
2. Navigieren Sie zu **Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Remotedesktopdienste > Remotedesktop-Verbindungsclient**.
3. Legen Sie die Einstellung **UDP auf Client deaktivieren** auf **Aktiviert** fest.

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Deaktivieren von RDP Shortpath auf dem Sitzungshost

Um RDP Shortpath für einen bestimmten Sitzungshost zu deaktivieren, können Sie die folgende Gruppenrichtlinie verwenden und die UDP-Unterstützung deaktivieren:

1. Führen Sie auf dem Sitzungshost **gpedit.msc** aus.
2. Navigieren Sie zu **Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Remotedesktopdienste > Remotedesktop-Verbindungshost > Verbindungen**.
3. Legen Sie die Einstellung **RDP-Transportprotokolle auswählen** auf **Nur TCP** fest.

## <a name="public-preview-feedback"></a>Feedback zur öffentlichen Vorschauversion

Bitte teilen Sie uns Ihre Erfahrungen mit dieser öffentlichen Vorschauversion mit!
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Netzwerkkonnektivität von Windows Virtual Desktop finden Sie unter [Grundlegendes zur Windows Virtual Desktop-Netzwerkkonnektivität](network-connectivity.md).
* Informationen zu den ersten Schritten mit Quality of Service (QoS) für Windows Virtual Desktop finden Sie unter [Implementieren von Quality of Service (QoS) für Windows Virtual Desktop](rdp-quality-of-service-qos.md).