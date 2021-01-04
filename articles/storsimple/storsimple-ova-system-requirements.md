---
title: Systemanforderungen für Microsoft Azure StorSimple Virtual Arrays
description: Erfahren Sie mehr über die Software- und Netzwerkanforderungen für StorSimple Virtual Array.
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966172"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systemanforderungen für StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Übersicht

In diesem Artikel werden wichtige Systemanforderungen für Ihr Microsoft Azure StorSimple Virtual Array und für die Speicherclients beschrieben, die auf das Array zugreifen. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie Ihr StorSimple-System bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

* **Softwareanforderungen für Speicherclients:** Hier werden die unterstützten Virtualisierungsplattformen, Webbrowser, iSCSI-Initiatoren, SMB-Clients, Mindestanforderungen für virtuelle Geräte und alle sonstigen Anforderungen für diese Betriebssysteme beschrieben.
* **Netzwerkanforderungen für das StorSimple-Gerät:** bietet Informationen zu den Ports, die in der Firewall für Datenverkehr von iSCSI, Cloud oder Verwaltung geöffnet sein müssen.

Die in diesem Artikel veröffentlichten Informationen zu den StorSimple-Systemanforderungen gelten nur für StorSimple Virtual Arrays.

* Informationen zu Geräten der 8000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 8000-Serie](./storsimple-8000-system-requirements.md).
* Informationen zu Geräten der 7000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 5000-7000-Serie](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Softwareanforderungen
Die Softwareanforderungen umfassen Informationen zu den unterstützten Webbrowsern, SMB-Versionen, Virtualisierungsplattformen und die Mindestanforderungen für virtuelle Geräte.

### <a name="supported-virtualization-platforms"></a>Unterstützte Virtualisierungsplattformen
| **Hypervisor** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 und höher |
| VMware ESXi |5.0, 5.5, 6.0 und 6.5 |

> [!IMPORTANT]
> Installieren Sie VMware-Tools nicht im StorSimple Virtual Array. Dies führt zu einer nicht unterstützten Konfiguration.

### <a name="virtual-device-requirements"></a>Anforderungen für virtuelle Geräte
| **Komponente** | **Anforderung** |
| --- | --- |
| Mindestanzahl virtueller Prozessoren (Kerne) |4 |
| Minimaler Arbeitsspeicher (RAM) |8 GB <br> Dateiserver: 8 GB für weniger als 2 Millionen Dateien und 16 GB für 2 bis 4 Millionen Dateien|
| Festplattenspeicher<sup>1</sup> |Betriebssystemdatenträger: 80 GB <br></br>Datendatenträger: 500 GB bis 8 TB |
| Mindestanzahl von Netzwerkschnittstellen |1 |
| Internetbandbreite<sup>2</sup> |Erforderliche minimale Bandbreite: 5 MBit/s <br> Empfohlene Bandbreite: 100 MBit/s <br> Die Geschwindigkeit der Datenübertragung wird entsprechend der Internetbandbreite skaliert. Beispiel: Bei einer Geschwindigkeit von 5 Mbit/s dauert die Übertragung von 100 GB an Daten zwei Tage. Dies kann zu Sicherungsfehlern führen, da tägliche Sicherungen nicht an einem Tag abgeschlossen werden. Mit einer Bandbreite von 100 Mbit/s können 100 GB an Daten in 2,5 Stunden übertragen werden.   |

<sup>1</sup> : Bereitgestelltes Thin-Gerät

<sup>2</sup>: Die Netzwerkanforderungen können abhängig von der täglichen Datenänderungsrate variieren. Beispiel: Wenn von einem Gerät Änderungen mit einem Umfang von mindestens 10 GB pro Tag gesichert werden müssen, kann die tägliche Sicherung bei einer Verbindung mit 5 MBit/s bis zu 4,25 Stunden dauern (falls die Daten nicht komprimiert oder dedupliziert werden konnten).

### <a name="supported-web-browsers"></a>Unterstützte Webbrowser
| **Komponente** | **Version** | **Weitere Anforderungen/Hinweise** |
| --- | --- | --- |
| Microsoft Edge |Aktuelle Version | |
| Internet Explorer |Aktuelle Version |Getestet mit Internet Explorer 11 |
| Google Chrome |Aktuelle Version |Getestet mit Chrome 46 |

### <a name="supported-storage-clients"></a>Unterstützte Speicherclients
Die folgenden Softwareanforderungen gelten für die iSCSI-Initiatoren, die auf StorSimple Virtual Array zugreifen (konfiguriert als iSCSI-Server).

| **Unterstützte Betriebssysteme** | **Erforderliche Version** | **Weitere Anforderungen/Hinweise** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2 |Mit StorSimple können Sie Volumes mit schlanker Speicherzuweisung und vollständig bereitgestellte Volumes erstellen. Es können damit keine teilweise bereitgestellten Volumes erstellt werden. StorSimple iSCSI-Volumes werden nur unterstützt für: <ul><li>Einfache Volumes auf Windows-Basisdatenträgern</li><li>Windows NTFS zum Formatieren eines Volumes</li> |

Die folgenden Softwareanforderungen gelten für die SMB-Clients, die auf StorSimple Virtual Array zugreifen (konfiguriert als Dateiserver).

| **SMB-Version** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Kopieren oder speichern Sie mit dem verschlüsselndes Dateisystem von Windows (Encrypting File System, EFS) geschützte Dateien nicht auf den StorSimple Virtual Array-Dateiserver, da dies zu einer nicht unterstützten Konfiguration führt.


### <a name="supported-storage-format"></a>Unterstützte Speicherformate
Es wird nur der Azure-Blockblobspeicher unterstützt. Seitenblobs werden nicht unterstützt. Weitere Informationen zu [Blockblobs und Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Netzwerkanforderungen
In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für iSCSI-, SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das StorSimple-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z.B. ausgehende Verbindungen mit dem Internet.

| **Portnr.<sup>1</sup>** | **ein oder aus** | **Portbereich** | **Erforderlich** | **Hinweise** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |aus |WAN |Nein |Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| TCP 443 (HTTPS) |aus |WAN |Ja |Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| UDP 53 (DNS) |aus |WAN |In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden. <br></br> Beachten Sie, dass bei der Bereitstellung eines Dateiservers die Verwendung eines lokalen DNS-Servers empfohlen wird. |
| UDP 123 (NTP) |aus |WAN |In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden.<br></br> Beachten Sie, dass bei der Bereitstellung eines Dateiservers empfohlen wird, die Zeit mit Ihren Active Directory-Domänencontrollern zu synchronisieren. |
| TCP 80 (HTTP) |Geben Sie in |LAN |Ja |Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung. <br></br> Beachten Sie, dass beim Zugriff auf die lokale Benutzeroberfläche über HTTP automatisch eine Umleitung auf HTTPS erfolgt. |
| TCP 443 (HTTPS) |Geben Sie in |LAN |Ja |Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung. |
| TCP 3260 (iSCSI) |Geben Sie in |LAN |Nein |Dieser Port wird für den Datenzugriff über iSCSI verwendet. |

<sup>1</sup> Es müssen keine eingehenden Ports für das öffentliche Internet geöffnet werden.

> [!IMPORTANT]
> Achten Sie darauf, dass TLS-Datenverkehr zwischen dem StorSimple-Gerät und Azure nicht durch die Firewall geändert oder entschlüsselt wird.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-Muster für Firewallregeln
Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr virtuelles Array und der StorSimple-Geräte-Manager-Dienst sind von anderen Microsoft-Anwendungen abhängig, z.B. Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Servern. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Das bedeutet, dass der Netzwerkadministrator die Firewallregeln für Ihren StorSimple nach Bedarf überwachen und aktualisieren muss. 

Es empfiehlt sich, die Firewallregeln für den ausgehenden Verkehr basierend auf den festen StorSimple IP-Adressen in den meisten Fällen recht locker festzulegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [!NOTE]
> 
> * Die Geräte-IPs (Quell-IPs) sollten immer für alle cloudaktivierten Netzwerkschnittstellen eingerichtet sein. 
> * Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/download/confirmation.aspx?id=41653) festgelegt werden.
> 
> 

| URL-Muster | Komponente/Funktionalität |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-Geräte-Manager-Dienst<br>Zugriffssteuerungsdienst (ACS)<br>Azure-Servicebus<br>Authentifizierungsdienst|
| `http://*.backup.windowsazure.com` |Geräteregistrierung |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Zertifikatswiderruf |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-Speicherkonten und Überwachung |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-Server<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Supportpaket |
| `https://*.data.microsoft.com` |Telemetriedienst in Windows. Informationen finden Sie im [Update für Kundenzufriedenheit und Diagnosetelemetrie](https://support.microsoft.com/en-us/kb/3068708). |

## <a name="next-steps"></a>Nächste Schritte
* [Vorbereiten des Portals zum Bereitstellen von StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)