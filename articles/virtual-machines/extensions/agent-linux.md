---
title: Azure-Linux-VM-Agent – Übersicht
description: Erfahren Sie, wie Sie den Linux-Agent (waagent) zum Verwalten der Interaktion Ihres virtuellen Computers mit Azure Fabric Controller installieren und konfigurieren.
author: axayjo
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4a391e52f80ec171d48718a18764f0f88f34133
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016487"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Grundlegendes zum Azure Linux-Agent und seiner Verwendung

Der Microsoft Azure Linux-Agent (waagent) verwaltet die Bereitstellung von Linux und FreeBSD sowie die Interaktion virtueller Computer mit dem Azure Fabric Controller. Der Linux-Agent bietet nicht nur Bereitstellungsfunktionen, sondern auch die Möglichkeit zur Verwendung von cloud-init für einige Linux-Betriebssysteme. Der Linux-Agent stellt die folgenden Funktionen für Linux- und FreeBSD-basierte IaaS-Bereitstellungen zur Verfügung:

> [!NOTE]
> Weitere Informationen finden Sie in der [Infodatei](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Image-Bereitstellung**
  
  * Erstellen eines Benutzerkontos
  * Konfigurieren von SSH-Authentifizierungstypen
  * Bereitstellen von öffentlichen SSH-Schlüsseln und -Schlüsselpaaren
  * Festlegen des Hostnamens
  * Veröffentlichen des Hostnamens beim Plattform-DNS
  * Mitteilen des SSH-Hostschlüssel-Fingerabdrucks an die Plattform
  * Verwalten von Ressourcendatenträgern
  * Formatieren und Bereitstellen des Ressourcendatenträgers
  * Konfigurieren von Auslagerungsbereich
* **Netzwerk**
  
  * Verwaltet von Routen zur Verbesserung der Kompatibilität mit Plattform-DHCP-Servern
  * Stellt die Stabilität des Netzwerkschnittstellennamens sicher
* **Kernel**
  
  * Konfiguriert einen virtuellen NUMA (muss für Kernel vor Version `2.6.37` deaktiviert werden)
  * Verbraucht Hyper-V-Entropie für /dev/random
  * Konfiguriert SCSI-Zeitüberschreitungen für das Stammgerät (das auch remote sein kann)
* **Diagnose**
  
  * Konsolenumleitung an den seriellen Port
* **SCVMM-Bereitstellungen**
  
  * Erkennt und startet den VMM-Agenten für Linux bei Ausführung in einer Umgebung mit System Center Virtual Machine Manager 2012 R2
* **VM-Erweiterung**
  
  * Fügt von Microsoft und Partnern erstellte Komponenten in einen virtuellen Linux-Computer ein, um die Automatisierung der Software und der Konfiguration zu ermöglichen
  * Referenzimplementierung der VM-Erweiterung unter [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikation
Der Informationsfluss von der Plattform zum Agenten erfolgt über zwei Kanäle:

* Über eine beim Start angeschlossene DVD für IaaS-Bereitstellungen. Diese DVD enthält eine OVF-kompatible Konfigurationsdatei mit allen Bereitstellungsinformationen außer den tatsächlichen SSH-Schlüsselpaaren.
* Über einen TCP-Endpunkt, der eine REST-API verfügbar macht, um die Bereitstellungs- und Topologiekonfiguration abzurufen.

## <a name="requirements"></a>Requirements (Anforderungen)
Folgende Systeme können nachweislich mit dem Azure Linux-Agent verwendet werden:

> [!NOTE]
> Diese Liste kann von der offiziellen Liste der [unterstützten Distributionen](../linux/endorsed-distros.md) abweichen.
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Andere unterstützte Systeme:

* FreeBSD 10+ (Azure Linux-Agent v2.0.10+)

Der Linux-Agent erfordert zur ordnungsgemäßen Funktion bestimmte Systempakete:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Dienstprogramme für das Dateisystem: sfdisk, fdisk, mkfs, parted
* Kennworttools: chpasswd, sudo
* Textverarbeitungstools: sed, grep
* Netzwerktools: ip-route
* Kernel-Unterstützung für die Einbindung von UDF-Dateisystemen.

Stellen Sie sicher, dass Ihr virtueller Computer Zugriff auf die IP-Adresse 168.63.129.16 hat. Unter [Was ist die IP-Adresse 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md) finden Sie weitere Informationen dazu.


## <a name="installation"></a>Installation
Für die Installation und Upgrades des Azure Linux-Agents sollte nach Möglichkeit ein RPM- oder DEB-Paket aus dem Paketrepository Ihrer Verteilung installiert werden. Das Azure Linux-Agent-Paket wird von allen [unterstützten Distributionsanbietern](../linux/endorsed-distros.md) in ihre jeweiligen Images und Repositorys integriert.

Informationen zu erweiterten Installationsoptionen (beispielsweise zu einer quellbasierten Installation, zum Installieren an benutzerdefinierten Orten oder zur Verwendung von Präfixen) finden Sie in der Dokumentation im [Azure Linux-Agent-Repository auf GitHub](https://github.com/Azure/WALinuxAgent).

## <a name="command-line-options"></a>Befehlszeilenoptionen
### <a name="flags"></a>Flags
* verbose: Erhöht die Ausführlichkeit des angegebenen Befehls.
* force: Überspringt die interaktive Bestätigung für einige Befehle.

### <a name="commands"></a>Befehle
* help: Listet die unterstützten Befehle und Flags auf.
* deprovision: Versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Bei diesem Vorgang wird Folgendes gelöscht:
  
  * Alle SSH-Hostschlüssel (sofern Provisioning.RegenerateSshHostKeyPair in der Konfigurationsdatei auf "y" festgelegt ist)
  * Namenserverkonfiguration in "/etc/resolv.conf"
  * Stammkennwort aus "/etc/shadow" (sofern Provisioning.DeleteRootPassword in der Konfigurationsdatei auf "y" festgelegt ist)
  * Zwischengespeicherte DHCP-Clientleases
  * Setzt den Hostnamen auf "localhost.localdomain" zurück

> [!WARNING]
> Die Aufhebung der Bereitstellung garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden und das Image für eine weitere Verteilung geeignet ist.
> 
> 

* deprovision+user: Führt alle oben für „-deprovision“ aufgeführten Aktionen aus und löscht darüber hinaus das zuletzt bereitgestellte Benutzerkonto (aus „/var/lib/waagent“ abgerufen) sowie die zugehörigen Daten. Dieser Parameter sollte verwendet werden, um die Bereitstellung eines Image aufzuheben, das zuvor für Bereitstellungen auf Azure verwendet wurde, sodass es erfasst und erneut verwendet werden kann.
* version: Zeigt die Version von waagent an.
* serialconsole: Konfiguriert GRUB so, dass ttyS0 (der erste serielle Port) als Startkonsole markiert wird. Hierdurch wird sichergestellt, dass Kernelstartprotokolle an den seriellen Port gesendet und für das Debuggen verfügbar gemacht werden.
* daemon: Führt waagent als Daemon aus, um die Interaktion mit der Plattform zu verwalten. Dieses Argument wird waagent im waagent-Initialisierungsskript mitgeteilt.
* start: Führt waagent als Hintergrundprozess aus.

## <a name="configuration"></a>Konfiguration
Eine Konfigurationsdatei (/etc/waagent.conf) steuert die Aktionen von waagent. Das folgende Beispiel zeigt eine Beispielkonfigurationsdatei:

```config
Provisioning.Enabled=y
Provisioning.DeleteRootPassword=n
Provisioning.RegenerateSshHostKeyPair=y
Provisioning.SshHostKeyPairType=rsa
Provisioning.MonitorHostName=y
Provisioning.DecodeCustomData=n
Provisioning.ExecuteCustomData=n
Provisioning.AllowResetSysUser=n
Provisioning.PasswordCryptId=6
Provisioning.PasswordCryptSaltLength=10
ResourceDisk.Format=y
ResourceDisk.Filesystem=ext4
ResourceDisk.MountPoint=/mnt/resource
ResourceDisk.MountOptions=None
ResourceDisk.EnableSwap=n
ResourceDisk.SwapSizeMB=0
LBProbeResponder=y
Logs.Verbose=n
OS.RootDeviceScsiTimeout=300
OS.OpensslPath=None
HttpProxy.Host=None
HttpProxy.Port=None
AutoUpdate.Enabled=y
```

Die verschiedenen Konfigurationsoptionen werden nachfolgend erläutert. Konfigurationsoptionen weisen einen von drei Datentypen auf: Boolean, String oder Integer. Die Konfigurationsoptionen vom Typ "Boolesch" können als "j" oder "n" angegeben werden. Für einige Konfigurationseinträge des Typs „String“ kann das spezielle Schlüsselwort „None“ verwendet werden, wie nachfolgend beschrieben:

**Provisioning.Enabled:**  
```txt
Type: Boolean  
Default: y
```
Hiermit kann der Benutzer die Bereitstellungsfunktion im Agenten aktivieren oder deaktivieren. Gültige Werte sind "y" und "n". Bei deaktivierter Bereitstellung werden die SSH-Host- und -Benutzerschlüssel im Image beibehalten, und alle in der Azure-Bereitstellungs-API angegebenen Konfigurationen werden ignoriert.

> [!NOTE]
> Der Parameter `Provisioning.Enabled` ist für Ubuntu-Cloud-Images, die „cloud-init“ für die Bereitstellung verwenden, standardmäßig auf „n“ festgelegt.
> 
> 

**Provisioning.DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Bei aktivierter Option wird das Stammkennwort in der Datei "/etc/shadow" bei der Bereitstellung gelöscht.

**Provisioning.RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Bei aktivierter Option werden alle SSH-Hostschlüsselpaare (ecdsa, dsa und rsa) bei der Bereitstellung aus „/etc/ssh/“ gelöscht. Außerdem wird ein einzelnes neues Schlüsselpaar generiert.

Der Verschlüsselungstyp für das neue Schlüsselpaar kann mit dem Provisioning.SshHostKeyPairType-Eintrag konfiguriert werden. Einige Distributionen erstellen SSH-Schlüsselpaare für alle fehlenden Verschlüsselungstypen neu, wenn der SSH-Daemon neu gestartet wird (z.B. bei einem Systemneustart).

**Provisioning.SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Diese Option kann auf einen Verschlüsselungsalgorithmustyp festgelegt werden, der vom SSH-Daemon auf dem virtuellen Computer unterstützt wird. Typischerweise werden die Werte "rsa", "dsa" und "ecdsa" unterstützt. „putty.exe“ unter Windows bietet keine Unterstützung für „ecdsa“. Wenn Sie also beabsichtigen, mithilfe von „putty.exe“ unter Windows eine Verbindung mit einer Linux-Bereitstellung herzustellen, verwenden Sie „rsa“ oder „dsa“.

**Provisioning.MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Bei aktivierter Option überwacht waagent den virtuellen Linux-Computer auf Änderungen des Hostnamens (wie vom Befehl „hostname“ zurückgegeben) und aktualisiert die Netzwerkkonfiguration im Image automatisch entsprechend der Änderung. Um die Namensänderung mithilfe von Push an die DNS-Server zu übertragen, wird die Netzwerkfunktion auf dem virtuellen Computer neu gestartet. Dadurch geht die Internetkonnektivität für einen kurzen Augenblick verloren.

**Provisioning.DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Ist diese Option festgelegt, decodiert waagent CustomData aus Base64.

**Provisioning.ExecuteCustomData**  
```txt
Type: Boolean  
Default: n
```
Ist diese Option festgelegt, führt waagent CustomData nach der Bereitstellung aus.

**Provisioning.AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Mit dieser Option kann das Kennwort für den Benutzer SYS zurückgesetzt werden. Die Option ist standardmäßig deaktiviert.

**Provisioning.PasswordCryptId**  
```txt
Type: String  
Default: 6
```
Algorithmus, der von Crypt zum Generieren des Kennworthashs verwendet wird.  
 1: MD5  
 2: Blowfish  
 5: SHA-256  
 6: SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Länge des Zufallssalts, das beim Generieren des Kennworthashs verwendet wird.

**ResourceDisk.Format:**  
```txt
Type: Boolean  
Default: y
```
Bei aktivierter Option wird der von der Plattform bereitgestellte Ressourcendatenträger von waagent formatiert und bereitgestellt, falls der Benutzer in „ResourceDisk.Filesystem“ einen anderen Dateisystemtyp als „ntfs“ anfordert. Auf dem Datenträger wird eine einzelne Linux-Partition (83) verfügbar gemacht. Diese Partition wird nicht formatiert, falls sie erfolgreich bereitgestellt werden kann.

**ResourceDisk.Filesystem:**  
```txt
Type: String  
Default: ext4
```
Diese Option gibt den Dateisystemtyp für den Ressourcendatenträger an. Die unterstützten Werte sind je nach Linux-Distribution verschieden. Bei der Zeichenfolge X sollte mkfs.X im Linux-Image vorhanden sein. Für SLES 11-Images sollte normalerweise "ext3" verwendet werden. Für FreeBSD-Images sollte hier "ufs2" verwendet werden.

**ResourceDisk.MountPoint:**  
```txt
Type: String  
Default: /mnt/resource 
```
Diese Option gibt den Pfad an, in dem der Ressourcendatenträger bereitgestellt wird. Der Ressourcendatenträger ist ein *temporärer* Datenträger und kann geleert werden, wenn die Bereitstellung der VM aufgehoben wird.

**ResourceDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Gibt Optionen für die Datenträgereinbindung an, die an den Befehl „mount -o“ übergeben werden. Hierbei handelt es sich um eine durch Trennzeichen getrennte Liste. Beispiel: 'nodev,nosuid'. Weitere Informationen finden Sie unter „mount(8)“.

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Bei aktivierter Option wird eine Auslagerungsdatei (/swapfile) auf dem Ressourcendatenträger erstellt und dem Systemauslagerungsbereich hinzugefügt.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
Die Größe der Auslagerungsdatei in Megabyte.

**Logs.Verbose:**  
```txt
Type: Boolean  
Default: n
```
Bei aktivierter Option wird ein ausführlicheres Protokoll erstellt. Waagent schreibt das Protokoll in „/var/log/waagent.log“ und nutzt die logrotate-Funktion des Systems zum Rotieren von Protokollen.

**OS.EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Ist diese Option festgelegt, versucht der Agent, einen passenden RDMA-Kerneltreiber für die Firmwareversion der zugrunde liegenden Hardware zu installieren und anschließend zu laden.

**OS.RootDeviceScsiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Mit dieser Einstellung wird die SCSI-Zeitüberschreitung auf dem Betriebssystemdatenträger und Datenlaufwerken in Sekunden konfiguriert. Wird kein Wert festgelegt, gelten die Systemstandardwerte.

**OS.OpensslPath:**  
```txt
Type: String  
Default: None
```
Mit dieser Einstellung kann ein alternativer Pfad für die openssl-Binärdatei zur Verwendung für kryptografische Vorgänge angegeben werden.

**HttpProxy.Host, HttpProxy.Port**  
```txt
Type: String  
Default: None
```
Ist diese Option festgelegt, greift der Agent über den angegebenen Proxyserver auf das Internet zu. 

**AutoUpdate.Enabled**
```txt
Type: Boolean
Default: y
```
Aktivieren oder deaktivieren Sie die automatische Aktualisierung für die Verarbeitung des Zielzustands. Sie ist standardmäßig aktiviert.



## <a name="ubuntu-cloud-images"></a>Ubuntu-Cloud-Images
Ubuntu Cloud Images verwenden [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) für zahlreiche Konfigurationsaufgaben, die andernfalls vom Azure Linux-Agent verwaltet würden. Es liegen folgende Unterschiede vor:

* **Provisioning.Enabled** ist für Ubuntu-Cloud-Images, die für Bereitstellungsaufgaben Cloud-Init verwenden, standardmäßig auf "n" gesetzt.
* Die folgenden Konfigurationsparameter wirken sich nicht auf Ubuntu-Cloud-Images aus, die Ressourcendatenträger und Auslagerungsbereiche mit Cloud-Init verwalten:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Berücksichtigen Sie zum Konfigurieren des Bereitstellungspunkts und des Auslagerungsbereichs für den Ressourcendatenträger von Ubuntu Cloud Images die folgenden Ressourcen:
  
  * [Ubuntu Wiki: Configure Swap Partitions](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409) (Konfigurieren von Swappartitionen)
  * [Einfügen benutzerdefinierter Daten in einen virtuellen Azure-Computer](../windows/tutorial-automate-vm-deployment.md)