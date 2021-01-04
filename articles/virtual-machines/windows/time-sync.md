---
title: Zeitsynchronisierung für Windows-VMs in Azure
description: Zeitsynchronisierung für virtuelle Windows-Computer.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 830bdd45be4b0365ac45bc3ea366b99a34882a4c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010621"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Zeitsynchronisierung für Windows-VMs in Azure

Die Zeitsynchronisierung ist für die Sicherheit und die Ereigniskorrelation wichtig. Manchmal wird sie für die Implementierung von verteilten Transaktionen verwendet. Die Genauigkeit der Uhrzeit zwischen mehreren Computersystemen wird durch die Synchronisierung erreicht. Die Synchronisierung kann durch mehrere Faktoren beeinträchtigt werden, einschließlich Neustarts und Netzwerkdatenverkehr zwischen der Zeitquelle und dem Computer, der die Uhrzeit abruft. 

Azure wird jetzt von einer Infrastruktur unter Windows Server 2016 unterstützt. Windows Server 2016 verfügt über verbesserte Algorithmen zur Korrektur der Uhrzeit und zur Konditionierung der lokalen Uhr zum Synchronisieren mit UTC.  Windows Server 2016 verbessert auch den VMICTimeSync-Dienst, der steuert, wie VMs in Bezug auf die genaue Uhrzeit mit dem Host synchronisiert werden. Die Verbesserungen umfassen eine genauere Anfangszeit beim VM-Start oder bei der VM-Wiederherstellung sowie eine Unterbrechungskorrektur der Latenzzeit für Beispiele, die für den Windows-Zeitdienst (W32time) bereitgestellt werden. 


>[!NOTE]
>Eine kurze Übersicht über den Windows-Zeitdienst bietet dieses [allgemeine Übersichtsvideo](https://aka.ms/WS2016TimeVideo).
>
> Weitere Informationen finden Sie unter [Genaue Uhrzeit für Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Übersicht

Die Genauigkeit einer Computeruhr wird daran gemessen, wie nahe die Computeruhr dem Zeitstandard Koordinierte Weltzeit (Coordinated Universal Time, UTC) ist. UTC wird durch ein multinationales Muster von präzisen Atomuhren definiert, die in 300 Jahren nur um eine Sekunde abweichen. Um UTC jedoch direkt lesen zu können, ist spezielle Hardware erforderlich. Stattdessen werden Zeitserver mit UTC synchronisiert, und der Zugriff darauf erfolgt über andere Computer, um Skalierbarkeit und Stabilität zu ermöglichen. Auf jedem Computer wird ein Zeitsynchronisierungsdienst ausgeführt, der weiß, welche Zeitserver zu verwenden sind, und regelmäßig überprüft, ob die Computeruhr korrigiert werden muss, und die Uhrzeit bei Bedarf anpasst. 

Azure-Hosts werden mit internen Microsoft-Zeitservern synchronisiert, die die Uhrzeit von Microsoft-eigenen Stratum 1-Geräten mit GPS-Antennen abfragen. Virtuelle Computer in Azure können zum Übergeben der genauen Uhrzeit an den virtuellen Computer von ihrem Host abhängen (*Hostzeit*), oder der virtuelle Computer kann die Uhrzeit direkt von einem Zeitserver abrufen, oder eine Kombination aus beidem. 

VM-Interaktionen mit dem Host können sich auch auf die Uhr auswirken. Während der [Wartung mit Speicherbeibehaltung](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) werden VMs bis zu 30 Sekunden angehalten. Beispiel: Vor Beginn der Wartung zeigt die VM-Uhr 10:00:00 Uhr an und wird 28 Sekunden angehalten. Nach Wiederaufnahme würde die Uhr des virtuellen Computers auch noch 10:00:00 Uhr anzeigen, was einen Zeitverlust von 28 Sekunden bedeuten würde. Um dies zu korrigieren, überwacht der VMICTimeSync-Dienst die Vorgänge auf dem Host und fordert zwecks Kompensierung zum Durchführen von Änderungen auf dem virtuellen Computer auf.

Der VMICTimeSync-Dienst wird entweder im Sampling- oder Synchronisierungsmodus ausgeführt und wirkt sich nur auf das Vorstellen der Uhr aus. Im Sampling-Modus, der die Ausführung von W32time erfordert, fragt der VMICTimeSync-Dienst den Host alle 5 Sekunden ab und Zeit-Samples an W32time bereit. Etwa alle 30 Sekunden verwendet der W32time-Dienst das letzte Zeit-Sample, um damit die Uhr des Gasts zu beeinflussen. Der Synchronisierungsmodus wird aktiviert, wenn der Gast fortgesetzt wurde oder die Uhr des Gasts mehr als 5 Sekunden hinter der Uhr des Hosts liegt. Bei einer ordnungsgemäßen Ausführung des W32time-Diensts sollte der letztere Fall nie eintreten.

Ohne Zeitsynchronisierung würden sich auf dem virtuellen Computer Zeitfehler ansammeln. Wenn nur ein virtueller Computer vorhanden ist, sind die Auswirkungen möglicherweise nicht unbedingt erheblich, sofern die Workload keine genaue Zeitmessung erfordert. Aber in den meisten Fällen verfügen wir über mehrere, miteinander verbundene VMs, die Transaktionen anhand der Uhrzeit nachverfolgen, und die Uhrzeit muss in der gesamten Bereitstellung einheitlich sein. Bei unterschiedlichen Uhrzeiten der VMs kann es zu den folgenden Auswirkungen kommen:

- Die Authentifizierung schlägt fehl. Sicherheitsprotokolle wie Kerberos oder von Zertifikaten abhängige Technologien erfordern eine einheitliche Uhrzeit auf allen Systemen. 
- Wenn Protokolle (oder andere Daten) zeitlich nicht genau übereinstimmen, lassen sich die Vorgänge in einem System nur sehr schwer nachvollziehen. Das gleiche Ereignis würde so aussehen, als wäre es zu verschiedenen Zeiten aufgetreten, was die Korrelation erschwert.
- Wenn die Uhr ausgeschaltet ist, könnte die Abrechnung falsch erfolgen.

Bei Windows-Bereitstellungen werden die besten Ergebnisse erzielt, wenn Sie Windows Server 2016 als Gastbetriebssystem verwenden. Dadurch wird sichergestellt, dass Sie die neuesten Verbesserungen in der Zeitsynchronisierung verwenden können.

## <a name="configuration-options"></a>Konfigurationsoptionen

Zum Konfigurieren der Zeitsynchronisierung für Ihre in Azure gehosteten Windows-VMs stehen drei Optionen zur Verfügung:

- Hostzeit und „time.windows.com“. Das ist die in Azure Marketplace-Images verwendete Standardkonfiguration.
- Nur Host
- Verwenden eines anderen, externen Zeitservers mit oder ohne Verwendung der Hostzeit


### <a name="use-the-default"></a>Verwenden der Standardeinstellung

Standardmäßig werden Windows-Betriebssystem-VM-Images für w32time für die Synchronisierung aus zwei Quellen konfiguriert: 

- Der Zeitanbieter „NtpClient“, der Informationen von „time.windows.com“ abruft.
- Der VMICTimeSync-Dienst, der verwendet wird, um die Hostzeit an die VMs zu übermitteln und Korrekturen vorzunehmen, nachdem der virtuelle Computer zu Wartungszwecken angehalten wird. Azure-Hosts verwenden Microsoft-eigene Stratum 1-Geräte, um die genaue Uhrzeit beizubehalten.

W32time bevorzugt den Zeitanbieter in der folgenden Prioritätenfolge: Stratum-Ebene, Stammverzögerung, Stammabweichung, Zeitoffset. In den meisten Fällen bevorzugt w32time auf einer Azure-VM die Hostzeit aufgrund der Auswertung für den Vergleich der beiden Zeitquellen. 

Bei in die Domäne eingebundenen Computern legt die Domäne die Zeitsynchronisierungshierarchie fest, aber der Gesamtstrukturstamm muss die Uhrzeit noch aus einer Quelle abrufen, und die folgenden Aspekte würden weiterhin gelten.


### <a name="host-only"></a>Nur Host 

Da „time.windows.com“ ein öffentlicher NTP-Server ist, muss zum Synchronisieren der Uhrzeit mit ihm Datenverkehr über das Internet gesendet werden, und unterschiedliche Paketverzögerungen können sich negativ auf die Qualität der Zeitsynchronisierung auswirken. Wenn Sie „time.windows.com“ durch den Wechsel zu einer Nur-Host-Synchronisierung entfernen, können Sie Ihre Zeitsynchronisierungsergebnisse mitunter verbessern.

Der Wechsel zu einer Nur-Host-Zeitsynchronisierung ist sinnvoll, wenn in der Standardkonfiguration Probleme bei der Zeitsynchronisierung auftreten. Testen Sie die Nur-Host-Synchronisierung, um zu prüfen, ob dies die Zeitsynchronisierung auf dem virtuellen Computer verbessern würde. 

### <a name="external-time-server"></a>Externer Zeitserver

Bei bestimmten Zeitsynchronisierungsanforderungen haben Sie auch die Möglichkeit, externe Zeitserver zu verwenden. Externe Zeitserver können eine bestimmte Uhrzeit angeben. Dies kann in Testszenarien hilfreich sein, um die Zeiteinheitlichkeit bei in Microsoft-fremden Rechenzentren gehosteten Computern sicherzustellen oder Schaltsekunden auf besondere Weise zu behandeln.

Sie können externe Server mit dem VMICTimeSync-Dienst und VMICTimeProvider kombinieren, um mit der Standardkonfiguration vergleichbare Ergebnisse zu liefern. 

## <a name="check-your-configuration"></a>Überprüfen Ihrer Konfiguration


Überprüfen Sie, ob der Zeitanbieter „NtpClient“ für die Verwendung von expliziten NTP-Servern (NTP) oder der Domänenzeitsynchronisierung (NT5DS) konfiguriert ist.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Wenn der virtuelle Computer NTP verwendet, wird die folgende Ausgabe angezeigt:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Wenn Sie anzeigen möchten, welchen Zeitserver der Zeitanbieter „NtpClient“ verwendet, geben Sie an einer Eingabeaufforderung mit erhöhten Rechten Folgendes ein:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Wenn der virtuelle Computer die Standardeinstellung verwendet, sieht die Ausgabe wie folgt aus:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


So können Sie anzeigen, welcher Zeitanbieter aktuell verwendet wird.

```
w32tm /query /source
```


Nachfolgend sehen Sie die Ausgabe, die angezeigt werden könnte und was dies bedeuten würde:
    
- **time.windows.com**: In der Standardkonfiguration würde w32time die Uhrzeit von „time.windows.com“ abrufen. Die Qualität der Zeitsynchronisierung hängt von der vorhandenen Internetverbindung ab und wird von Paketverzögerungen beeinflusst. Dies ist die normale Ausgabe, die Sie auf einem physischen Computer erhalten.
- **VM IC Time Synchronization Provider**: Der virtuelle Computer synchronisiert die Uhrzeit mit dem Host. Dies ist die normale Ausgabe, die Sie auf einem virtuellen Computer unter Azure erhalten. 
- *Ihr Domänenserver*: Der aktuelle Computer befindet sich in einer Domäne, und die Domäne definiert die Zeitsynchronisierungshierarchie.
- *Ein anderer Server*: w32time wurde explizit für das Abrufen der Uhrzeit von einem anderen bestimmten Server konfiguriert. Die Qualität der Zeitsynchronisierung hängt von der Qualität dieses Zeitservers ab.
- **Local CMOS Clock**: Die Uhr wird nicht synchronisiert. Sie erhalten diese Ausgabe, wenn w32time nach einem Neustart nicht genügend Zeit zum Starten hatte oder alle konfigurierten Zeitquellen nicht verfügbar sind.


## <a name="opt-in-for-host-only-time-sync"></a>Aktivieren der Nur-Host-Zeitsynchronisierung

Azure arbeitet kontinuierlich an der Verbesserung der Zeitsynchronisierung auf Hosts und kann sicherstellen, dass die gesamte Zeitsynchronisierungsinfrastruktur in Microsoft-eigenen Rechenzentren angeordnet wird. Wenn in der Standardeinrichtung Probleme bei der Zeitsynchronisierung auftreten, bei der vorzugsweise „time.windows.com“ als primäre Zeitquelle verwendet wird, können Sie die folgenden Befehle verwenden, um die Nur-Host-Zeitsynchronisierung zu aktivieren.

Markieren Sie den Anbieter „VMIC“ als aktiviert. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markieren Sie den Anbieter „NtpClient“ als deaktiviert.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Starten Sie den w32time-Dienst neu.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>VMs unter Windows Server 2012 und Windows Server 2012 R2 

Windows Server 2012 und Windows Server 2012 R2 weisen unterschiedliche Standardeinstellungen für die Zeitsynchronisierung auf. Der w32time-Dienst ist standardmäßig so konfiguriert, dass ein geringerer Overhead des Diensts einer genauen Uhrzeit vorzuziehen ist. 

Wenn Sie in Ihren Windows Server 2012- und Windows Server 2012 R2-Bereitstellungen die neueren Standardeinstellungen verwenden möchten, die eine genaue Uhrzeit bevorzugen, können Sie die folgenden Einstellungen anwenden.

Aktualisieren Sie den w32time-Abruf und die Intervalle gemäß den Einstellungen von Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Damit w32time die neuen Abrufintervalle verwenden kann, müssen die NTP-Server zu ihrer Verwendung gekennzeichnet sein. Wenn die Server mit einer Bitkennzeichenmaske „0x1“ kommentiert werden, würde das diesen Mechanismus außer Kraft setzen, und w32time würde stattdessen „SpecialPollInterval“ verwenden. Stellen Sie sicher, dass die angegebenen NTP-Server entweder „0x8“ oder gar kein Flag verwenden:

Überprüfen Sie, welche Flags für die verwendeten NTP-Server verwendet werden.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Nächste Schritte

Im Folgenden finden Sie Links zu weiteren Details zur Zeitsynchronisierung:

- [Windows-Zeitdienst: Tools und Einstellungen](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Verbesserungen in Windows Server 2016](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Genaue Uhrzeit für Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Unterstützungsgrenzen zum Konfigurieren des Windows-Zeitdiensts für Umgebungen mit hoher Genauigkeit](/windows-server/networking/windows-time-service/support-boundary)
