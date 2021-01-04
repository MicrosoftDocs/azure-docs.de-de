---
title: Einführung in die Problembehandlung für Ressourcen
titleSuffix: Azure Network Watcher
description: Diese Seite enthält eine Übersicht über die Network Watcher-Funktionen zur Problembehandlung bei Ressourcen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: f27cb217b60c23f3cf89f48effb933837269000e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960324"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Einführung in die Problembehandlung bei Ressourcen in Azure Network Watcher

Virtual Network-Gateways stellen die Konnektivität zwischen lokalen Ressourcen und anderen virtuellen Netzwerken in Azure bereit. Die Überwachung der Gateways und ihrer Verbindungen ist wichtig, damit die Kommunikation nicht unterbrochen wird. Network Watcher bietet Funktionen zur Problembehandlung bei Gateways und Verbindungen. Diese können über Portal, PowerShell, Azure CLI oder REST-API aufgerufen werden. Bei Aufruf untersucht Network Watcher die Integrität des Gateways oder der Verbindung und gibt die Ergebnisse zurück. Die Anforderung ist eine lange andauernde Transaktion. Die Ergebnisse werden zurückgegeben, sobald die Diagnose abgeschlossen ist.

![Screenshot zeigt Network Watcher VPN-Diagnose.][2]

## <a name="results"></a>Ergebnisse

Die vorab zurückgegebenen Ergebnisse bieten eine allgemeine Übersicht über die Integrität der Ressource. Es können auch ausführlichere Informationen für Ressourcen bereitgestellt werden, wie im folgenden Abschnitt gezeigt:

Die folgende Liste enthält die Werte, die von der Problembehandlungs-API zurückgegeben wurden:

* **startTime:** Dieser Wert ist die Startzeit des Aufrufs der Problembehandlungs-API.
* **endTime:** Dieser Wert ist der Zeitpunkt, zu dem die Problembehandlung beendet wurde.
* **code:** Dieser Wert ist UnHealthy, wenn ein einziger Diagnosefehler auftritt.
* **results:** Dies ist eine Sammlung von Ergebnissen, die für die Verbindung oder das Virtual Network-Gateway zurückgegeben wurden.
    * **id:** Dieser Wert ist der Fehlertyp.
    * **summary:** Dieser Wert ist eine Zusammenfassung des Fehlers.
    * **detailed:** Dieser Wert enthält eine ausführliche Beschreibung des Fehlers.
    * **recommendedActions:** Diese Eigenschaft ist eine Sammlung von empfohlenen Maßnahmen.
      * **actionText:** Dieser Wert enthält die Beschreibung der erforderlichen Maßnahme.
      * **actionUri:** Dieser Wert enthält den URI zur Dokumentation der Maßnahme.
      * **actionUriText:** Dieser Wert gibt eine Kurzbeschreibung des Aktionstextes an.

Die folgenden Tabellen enthalten die verschiedenen verfügbaren Fehlertypen (ID unter den Ergebnissen der vorherigen Liste) und Informationen dazu, ob der Fehler Protokolle erstellt.

### <a name="gateway"></a>Gateway

| Fehlertyp | `Reason` | Log|
|---|---|---|
| NoFault | Es wurde kein Fehler erkannt. |Ja|
| GatewayNotFound | Das Gateway wurde nicht gefunden oder nicht bereitgestellt. |Nein|
| PlannedMaintenance |  Die Gatewayinstanz wird zurzeit gewartet.  |Nein|
| UserDrivenUpdate | Dieser Fehler tritt auf, wenn ein Benutzerupdate des Geräts durchgeführt wird. Das Update könnte z.B. eine Größenänderung sein. | Nein |
| VipUnResponsive | Dieser Fehler tritt auf, wenn die primäre Instanz des Gateways aufgrund eines Integritätstestfehlers nicht erreicht werden kann. | Nein |
| PlatformInActive | Es ist ein Problem mit der Plattform aufgetreten. | Nein|
| ServiceNotRunning | Der zugrunde liegende Dienst wird nicht ausgeführt. | Nein|
| NoConnectionsFoundForGateway | Es gibt keine Verbindungen auf dem Gateway. Dieser Fehler ist nur eine Warnung.| Nein|
| ConnectionsNotConnected | Es sind keine Verbindungen vorhanden. Dieser Fehler ist nur eine Warnung.| Ja|
| GatewayCPUUsageExceeded | Die aktuelle CPU-Auslastung auf dem Gateway liegt über 95 %. | Ja |

### <a name="connection"></a>Verbindung

| Fehlertyp | `Reason` | Log|
|---|---|---|
| NoFault | Es wurde kein Fehler erkannt. |Ja|
| GatewayNotFound | Das Gateway wurde nicht gefunden oder nicht bereitgestellt. |Nein|
| PlannedMaintenance | Die Gatewayinstanz wird zurzeit gewartet.  |Nein|
| UserDrivenUpdate | Dieser Fehler tritt auf, wenn ein Benutzerupdate des Geräts durchgeführt wird. Das Update könnte z.B. eine Größenänderung sein.  | Nein |
| VipUnResponsive | Dieser Fehler tritt auf, wenn die primäre Instanz des Gateways aufgrund eines Integritätstestfehlers nicht erreicht werden kann. | Nein |
| ConnectionEntityNotFound | Die Verbindungskonfiguration fehlt. | Nein |
| ConnectionIsMarkedDisconnected | Die Verbindung ist als „getrennt“ gekennzeichnet. |Nein|
| ConnectionNotConfiguredOnGateway | Für den zugrunde liegenden Dienst wurde die Verbindung nicht konfiguriert. | Ja |
| ConnectionMarkedStandby | Der zugrunde liegende Dienst ist als im Ruhezustand gekennzeichnet.| Ja|
| Authentifizierung | Der vorinstallierte Schlüssel stimmt nicht überein. | Ja|
| PeerReachability | Das Peergateway ist nicht erreichbar. | Ja|
| IkePolicyMismatch | Das Peergateway verfügt über IKE-Richtlinien, die von Azure nicht unterstützt werden. | Ja|
| WfpParse Error | Beim Analysieren des WFP-Protokolls ist ein Fehler aufgetreten. |Ja|

## <a name="supported-gateway-types"></a>Unterstützte Gatewaytypen

In der folgenden Tabelle sind die Gateways und Verbindungen aufgelistet, die von der Network Watcher-Problembehandlung unterstützt werden:

| Gateway oder Verbindung | Unterstützt  |
|---------|---------|
|**Gatewaytypen**   |         |
|VPN      | Unterstützt        |
|ExpressRoute | Nicht unterstützt |
|**VPN-Typen** | |
|Routenbasiert | Unterstützt|
|Richtlinienbasiert | Nicht unterstützt|
|**Verbindungstypen**||
|IPsec| Unterstützt|
|Vnet2Vnet| Unterstützt|
|ExpressRoute| Nicht unterstützt|
|VPNClient| Nicht unterstützt|

## <a name="log-files"></a>Protokolldateien

Nach Abschluss der Problembehandlung werden die Protokolldateien zur Ressourcenproblembehandlung in einem Speicherkonto gespeichert. Die folgende Abbildung zeigt Beispielinhalte für einen Aufruf, der zu einem Fehler geführt hat.

![ZIP-Datei][1]

> [!NOTE]
> In einigen Fällen wird nur eine Teilmenge der Protokolldateien in den Speicher geschrieben.

Anweisungen zum Herunterladen von Dateien von Azure-Speicherkonten finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ein weiteres Tool, das verwendet werden kann, ist der Storage-Explorer. Weitere Informationen zum Storage-Explorer finden Sie unter dem folgenden Link: [Storage-Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Die Datei **ConnectionStats.txt** enthält die gesamte Statistik der Verbindung, einschließlich der ein- und ausgehenden Bytes, des Verbindungsstatus und der Zeit, zu der die Verbindung hergestellt wurde.

> [!NOTE]
> Wenn der Aufruf der Problembehandlungs-API als Ergebnis die Integrität zurückgibt, ist der einzige Inhalt der zurückgegebenen ZIP-Datei die Datei **ConnectionStats.txt**.

Der Inhalt dieser Datei ähnelt folgendem Beispiel:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Die Datei **CPUStats.txt** enthält die CPU-Auslastung und den verfügbaren Arbeitsspeicher zum Zeitpunkt des Tests.  Der Inhalt dieser Datei ähnelt folgendem Beispiel:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Die Datei **IKEErrors.txt** enthält alle IKE-Fehler, die während der Überwachung gefunden wurden.

Das folgende Beispiel zeigt den Inhalt der Datei „IKEErrors.txt“. Die Fehler können je nach Problem variieren.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Die Protokolldatei **Scrubbed-wfpdiag.txt** enthält das WFP-Protokoll. Dieses Protokoll enthält die verloren gegangenen Pakete und die IKE-/AuthIP-Fehler.

Das folgende Beispiel zeigt den Inhalt der Datei „Scrubbed-wfpdiag.txt“. In diesem Beispiel war der gemeinsam verwendete Schlüssel einer Verbindung falsch. Dies ist in der drittletzten Zeile zu erkennen. Das folgende Beispiel ist ein Ausschnitt aus dem vollständigen Protokoll, da das Protokoll je nach Problem sehr lang werden kann.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

Die Datei **wfpdiag.txt.sum** ist ein Protokoll mit den verarbeiteten Puffern und Ereignissen.

Das folgende Beispiel zeigt den Inhalt der Datei „wfpdiag.txt.sum“.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Diagnostizieren eines Problems mit einem Gateway oder einer Gatewayverbindung finden Sie unter [Tutorial: Diagnostizieren eines Problems mit der Kommunikation zwischen Netzwerken mit dem Azure-Portal](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png