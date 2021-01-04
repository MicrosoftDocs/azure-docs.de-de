---
title: Systemanforderungen der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die Anforderungen und bewährten Methoden für Software, Netzwerke und Hochverfügbarkeit für eine Microsoft Azure StorSimple-Lösung.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 6dcaa83980210a1f5449e8a2e0982cb8e39ff03d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966189"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software für StorSimple 8000-Serie, Hochverfügbarkeit und Netzwerkanforderungen

## <a name="overview"></a>Übersicht

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Willkommen bei Microsoft Azure StorSimple In diesem Artikel werden wichtige Systemanforderungen sowie bewährte Methoden für Ihr StorSimple-Gerät und für die Speicherclients beschrieben, die auf das Gerät zugreifen. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie Ihr StorSimple-System bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

* **Softwareanforderungen für Speicherclients:** beschreibt die unterstützten Betriebssysteme und zusätzlichen Anforderungen für diese Betriebssysteme.
* **Netzwerkanforderungen für das StorSimple-Gerät:** bietet Informationen zu den Ports, die in der Firewall für Datenverkehr von iSCSI, Cloud oder Verwaltung geöffnet sein müssen.
* **Anforderungen an Hochverfügbarkeit für StorSimple:** beschreibt die Anforderungen an Hochverfügbarkeit und bewährte Methoden für das StorSimple-Gerät und den Hostcomputer.

## <a name="software-requirements-for-storage-clients"></a>Softwareanforderungen für Speicherclients:

Die folgenden Softwareanforderungen gelten für Speicherclients, die auf das StorSimple-Gerät zugreifen.

| Unterstützte Betriebssysteme | Erforderliche Version | Weitere Anforderungen/Hinweise |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-Volumes werden nur für die Verwendung auf den folgenden Windows-Datenträgertypen unterstützt:<ul><li>Einfaches Volume auf Basisfestplatte</li><li>Einfaches und gespiegeltes Volume auf dynamischem Datenträger</li></ul>Es werden nur die nativ im Betriebssystem vorhandenen Software-iSCSI-Initiatoren unterstützt. Hardware-iSCSI-Initiatoren werden nicht unterstützt.<br></br>Die schlanke Speicherzuweisung von Windows Server 2012 und 2016 sowie ODX-Features werden unterstützt, wenn Sie ein StorSimple-iSCSI-Volume verwenden.<br><br>Mit StorSimple können Sie Volumes mit schlanker Speicherzuweisung und vollständig bereitgestellte Volumes erstellen. Es können damit keine teilweise bereitgestellten Volumes erstellt werden.<br><br>Das erneute Formatieren eines mit schlanker Speicherzuweisung erstellten Volumes kann lange dauern. Es wird empfohlen, das Volume zu löschen und dann ein neues Volume zu erstellen, anstatt neu zu formatieren. Falls Sie trotzdem das Neuformatieren eines Volumes vorziehen:<ul><li>Führen Sie vor der Neuformatierung den folgenden Befehl aus, um Verzögerungen bei der Freigabe von Speicherplatz zu vermeiden: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Verwenden Sie nach Abschluss der Neuformatierung den folgenden Befehl, um die Freigabe von Speicherplatz erneut zu aktivieren:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Wenden Sie das Windows Server 2012-Hotfix wie in [KB 2878635](https://support.microsoft.com/kb/2870270) beschrieben auf Ihren Windows Server-Computer an.</li></ul></li></ul></ul> Greifen Sie auf [Softwareanforderungen für optionale Komponenten](#software-requirements-for-optional-components)zu, wenn Sie StorSimple Snapshot Manager oder den StorSimple-Adapter für SharePoint konfigurieren. |
| VMware ESX |5.5 und 6.0 |Wird für VMware vSphere als iSCSI-Client unterstützt. Die VAAI-Blockfunktion wird für VMware vSphere auf StorSimple-Geräten unterstützt. |
| Linux RHEL/CentOS |5, 6 und 7 |Unterstützung für Linux-iSCSI-Clients mit Open-iSCSI-Initiator, Versionen 5, 6 und 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX wird für StorSimple derzeit nicht unterstützt.


## <a name="software-requirements-for-optional-components"></a>Softwareanforderungen für optionale Komponenten

Die folgenden Sofwareanforderungen gelten für die optionalen StorSimple-Komponenten (StorSimple Snapshot Manager und StorSimple-Adapter für SharePoint).

| Komponente | Hostplattform | Weitere Anforderungen/Hinweise |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Die Verwendung für StorSimple Snapshot Manager unter Windows Server ist für die Sicherung/Wiederherstellung von gespiegelten dynamischen Datenträgern und für alle anwendungskonsistenten Sicherungen erforderlich.<br> Der StorSimple Snapshot Manager wird nur unter Windows Server 2008 R2 SP1 (64 Bit), Windows Server 2012 R2 und Windows Server 2012 unterstützt.<ul><li>Wenn Sie Windows Server 2012 verwenden, müssen Sie .NET 3.5 bis 4.5 installieren, bevor Sie den StorSimple Snapshot Manager installieren.</li><li>Bei Verwendung von Windows Server 2008 R2 SP1 müssen Sie Windows Management Framework 3.0 installieren, bevor Sie den StorSimple Snapshot Manager installieren.</li></ul> |
| StorSimple-Adapter für SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Der StorSimple-Adapter für SharePoint wird nur in SharePoint 2010 und SharePoint 2013 unterstützt.</li><li>RBS erfordert SQL Server Enterprise Edition, Version 2008 R2 oder 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Netzwerkanforderungen für Ihr StorSimple-Gerät

Das StorSimple-Gerät ist ein gesperrtes Gerät. Allerdings müssen Ports in der Firewall für iSCSI-, Cloud- und Verwaltungsdatenverkehr geöffnet werden. In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das StorSimple-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z.B. ausgehende Verbindungen mit dem Internet.

| Portnr.<sup>1,2</sup> | ein oder aus | Portbereich | Erforderlich | Notizen |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |aus |WAN |Nein |<ul><li>Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet.</li><li>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden.</li><li>Um Systemupdates zuzulassen, muss dieser Port auch für die festen Controller-IPs geöffnet sein.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |aus |WAN |Ja |<ul><li>Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet.</li><li>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden.</li><li>Um Systemupdates zuzulassen, muss dieser Port auch für die festen Controller-IPs geöffnet sein.</li><li>Dieser Port wird außerdem auf beiden Controllern für die Garbage Collection verwendet.</li></ul> |
| UDP 53 (DNS) |aus |WAN |In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden. |
| UDP 123 (NTP) |aus |WAN |In einigen Fällen; siehe Hinweise. |Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden. |
| TCP 9354 |aus |WAN |Ja |Der ausgehende Port wird vom StorSimple-Gerät für die Kommunikation mit dem StorSimple-Geräte-Manager-Dienst verwendet. |
| 3260 (iSCSI) |Geben Sie in |LAN |Nein |Dieser Port wird für den Datenzugriff über iSCSI verwendet. |
| 5985 |Geben Sie in |LAN |Nein |Der eingehende Port wird vom StorSimple-Momentaufnahme-Manager für die Kommunikation mit dem StorSimple-Gerät verwendet.<br>Dieser Port wird auch verwendet, wenn Sie eine Remoteverbindung mit Windows PowerShell für StorSimple über HTTP herstellen. |
| 5986 |Geben Sie in |LAN |Nein |Dieser Port wird verwendet, wenn Sie eine Remoteverbindung mit Windows PowerShell für StorSimple über HTTPS herstellen. |

<sup>1</sup> Es müssen keine eingehenden Ports für das öffentliche Internet geöffnet werden.

<sup>2</sup> Wenn mehrere Ports eine Gatewaykonfiguration besitzen, wird die Reihenfolge des ausgehenden weitergeleiteten Datenverkehrs basierend auf der unten unter [Portweiterleitung](#routing-metric) beschriebenen Reihenfolge der Portweiterleitungen ermittelt.

<sup>3</sup> Die festen IP-Adressen der Controller im StorSimple-Gerät müssen routingfähig sein und direkt oder über den konfigurierten Proxy eine Verbindung mit dem Internet herstellen können. Die festen IP-Adressen werden für die Bereitstellung der Updates für das Gerät sowie für die Garbage Collection verwendet. Wenn die Gerätecontroller über die festen IP-Adressen keine Verbindung mit dem Internet herstellen können, können Sie das StorSimple-Gerät nicht aktualisieren, und die Garbage Collection wird nicht ordnungsgemäß ausgeführt.

> [!IMPORTANT]
> Achten Sie darauf, dass TLS-Datenverkehr zwischen dem StorSimple-Gerät und Azure nicht durch die Firewall geändert oder entschlüsselt wird.


### <a name="url-patterns-for-firewall-rules"></a>URL-Muster für Firewallregeln

Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr StorSimple-Gerät und StorSimple-Geräte-Manager-Dienst hängen von anderen Microsoft-Anwendungen wie Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Servern ab. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Das bedeutet, dass der Netzwerkadministrator die Firewallregeln für Ihren StorSimple nach Bedarf überwachen und aktualisieren muss.

Es empfiehlt sich, die Firewallregeln für den ausgehenden Verkehr basierend auf den festen StorSimple IP-Adressen in den meisten Fällen recht locker festzulegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [!NOTE]
> Die Geräte-(Quellen-)IPs sollten immer für alle aktivierten Netzwerkschnittstellen eingerichtet sein. Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653) festgelegt werden.


#### <a name="url-patterns-for-azure-portal"></a>URL-Muster für Azure-Portal

| URL-Muster | Komponente/Funktionalität | Geräte-IPs |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-Geräte-Manager-Dienst<br>Zugriffssteuerungsdienst (ACS)<br>Azure-Servicebus<br>Authentifizierungsdienst |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.backup.windowsazure.com` |Geräteregistrierung |Nur DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Zertifikatswiderruf |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-Speicherkonten und Überwachung |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-Server<br> |Feste IPs des Controllers |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Feste IPs des Controllers |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Cloudaktivierte Netzwerkschnittstellen |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-Muster für Azure Government-Portal

| URL-Muster | Komponente/Funktionalität | Geräte-IPs |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-Geräte-Manager-Dienst<br>Zugriffssteuerungsdienst (ACS)<br>Azure-Servicebus<br>Authentifizierungsdienst |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.backup.windowsazure.us` |Geräteregistrierung |Nur DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Zertifikatswiderruf |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-Speicherkonten und Überwachung |Cloudaktivierte Netzwerkschnittstellen |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-Server<br> |Feste IPs des Controllers |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Feste IPs des Controllers |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Cloudaktivierte Netzwerkschnittstellen |

### <a name="routing-metric"></a>Routingmetrik

Eine Routingmetrik ist den Schnittstellen und dem Gateway zugeordnet, die die Daten an die angegebenen Netzwerke weiterleiten. Das Routingprotokoll errechnet anhand der Routingmetrik die beste Route für ein bestimmtes Ziel, wenn festgestellt wird, dass mehrere Pfade zum gleichen Ziel vorhanden sind. Je niedriger die Routingmetrik, desto höher ist die Voreinstellung.

Im Kontext von StorSimple gilt: Wenn mehrere Netzwerkschnittstellen und Gateways für das Weiterleiten von Datenverkehr konfiguriert sind, wird die Routingmetrik eingesetzt, um die relative Reihenfolge für die Verwendung der Schnittstellen festzulegen. Die Routingmetrik kann vom Benutzer nicht geändert werden. Mit dem `Get-HcsRoutingTable` -Cmdlet können Sie jedoch die Routingtabelle (und die Metrik) auf Ihrem StorSimple-Gerät ausgeben. Weitere Informationen zum Get-HcsRoutingTable-Cmdlet finden Sie unter [Beheben von Problemen mit der Bereitstellung von StorSimple-Geräten](./storsimple-8000-troubleshoot-deployment.md).

Der für Update 2 und höhere Versionen verwendete Routingmetrikalgorithmus lässt sich wie folgt beschreiben.

* Ein Satz vordefinierter Werte wurde Netzwerkschnittstellen zugewiesen.
* Betrachten Sie die unten aufgeführte Beispieltabelle mit Werten, die den verschiedenen Netzwerkschnittstellen zugewiesen sind, wenn sie für die Cloud aktiviert oder mit konfiguriertem Gateway für die Cloud deaktiviert sind. Beachten Sie, dass die hier zugewiesenen Werte nur Beispielwerte sind.

    | Netzwerkschnittstelle | Cloudfähig | Nicht cloudfähig mit Gateway |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Der Clouddatenverkehr wird in folgender Reihenfolge über die Netzwerkschnittstellen weitergeleitet:
  
    *Data 0 &gt; Data 1 &gt; Date 2 &gt; Data 3 &gt; Data 4 &gt; Data 5*
  
    Dies lässt sich anhand des folgenden Beispiels erläutern:
  
    Stellen Sie sich ein StorSimple-Gerät mit zwei cloudfähigen Netzwerkschnittstellen vor, Data 0 und Data 5. Data 1 bis Data 4 sind nicht cloudfähig, weisen jedoch ein konfiguriertes Gateway auf. Der Datenverkehr für dieses Gerät wird in folgender Reihenfolge weitergeleitet:
  
    *Data 0 (1) &gt; Data 5 (6) &gt; Data 1 (20) &gt; Data 2 (30) &gt; Data 3 (40) &gt; Data 4 (50)*
  
    *Die Zahlen in Klammern geben die jeweilige Routingmetrik an.*
  
    Wenn Data 0 einen Fehler verursacht, wird der Clouddatenverkehr über Data 5 weitergeleitet. Falls ein Gateway auf dem gesamten übrigen Netzwerk konfiguriert ist, verläuft der Clouddatenverkehr bei einem Ausfall von Data 0 und Data 5 über Data 1.
* Wenn auf einer cloudfähigen Netzwerkschnittstelle ein Fehler auftritt, werden mit 30 Sekunden Verzögerung drei weitere Versuche zum Herstellen einer Verbindung mit der Schnittstelle unternommen. Wenn kein Versuch zum Erfolg führt, wird der Datenverkehr anhand der Routingtabelle an die nächste verfügbare cloudfähige Schnittstelle geroutet. Wenn sämtliche cloudfähigen Netzwerkschnittstellen einen Fehler verursachen, führt das Gerät ein Failover zum anderen Controller durch (in diesem Fall findet kein Neustart statt).
* Wenn ein VIP-Fehler für eine iSCSI-aktivierte Netzwerkschnittstelle vorliegt, werden drei Wiederholungsversuche mit einer Verzögerung von 2 Sekunden unternommen. Dieses Verhalten ist seit früheren Versionen gleich geblieben. Wenn alle iSCSI-Netzwerkschnittstellen einen Fehler verursachen, wird ein Controllerfailover (zusammen mit einem Neustart) durchgeführt.
* Außerdem wird eine Warnung auf dem StorSimple-Gerät ausgelöst, wenn ein VIP-Fehler vorliegt. Weitere Informationen finden Sie unter [Kurzübersicht zu Warnungen](storsimple-8000-manage-alerts.md).
* Im Hinblick auf Wiederholungsversuche erhält iSCSI Vorrang vor der Cloud.
  
    Betrachten Sie das folgenden Beispiel: Für ein StorSimple-Gerät sind zwei Netzwerkschnittstellen aktiviert, Data 0 und Data 1. Data 0 ist cloudfähig, Data 1 ist sowohl cloudfähig als auch iSCSI-aktiviert. Keine anderen Netzwerkschnittstellen auf diesem Gerät sind für die Cloud oder iSCSI aktiviert.
  
    Wenn bei Data 1 ein Fehler auftritt und es sich dabei um die letzte iSCSI-Netzwerkschnittstelle handelt, führt dies zu einem Controllerfailover auf Data 1 auf dem anderen Controller.

### <a name="networking-best-practices"></a>Bewährte Methoden für Netzwerke

Befolgen Sie zusätzlich zu den oben genannten Netzwerkanforderungen die folgenden bewährten Methoden, um eine optimale Leistung für Ihre StorSimple-Lösung zu gewährleisten:

* Stellen Sie sicher, dass für das StorSimple-Gerät jederzeit eine dedizierte Bandbreite von 40 MBit/s (oder mehr) zur Verfügung steht. Diese Bandbreite sollte nicht mit anderen Anwendungen gemeinsam verwendet werden (oder die Zuweisungen sollten durch die Verwendung von QoS-Richtlinien garantiert werden).
* Stellen Sie sicher, dass jederzeit eine Netzwerkverbindung mit dem Internet verfügbar ist. Zeitweise unterbrochene oder unzuverlässige Internetverbindungen mit den Geräten, also auch eine Unterbrechung der Internetverbindung, führt zu einer nicht unterstützten Konfiguration.
* Isolieren Sie den Datenverkehr für iSCSI und Cloud durch Verwendung jeweils eigener Netzwerkschnittstellen auf dem Gerät für den iSCSI- und Cloudzugriff. Weitere Informationen finden Sie unter [Ändern von Netzwerkschnittstellen](storsimple-8000-modify-device-config.md#modify-network-interfaces) für das StorSimple-Gerät.
* Verwenden Sie keine Link Aggregation Control Protocol-Konfiguration (LACP) für die Netzwerkschnittstellen. Dies ist eine nicht unterstützte Konfiguration.

## <a name="high-availability-requirements-for-storsimple"></a>Anforderungen an Hochverfügbarkeit für StorSimple:

Die Hardwareplattform der StorSimple-Lösung bietet Features für Verfügbarkeit und Zuverlässigkeit, die als Grundlage für eine hoch verfügbare, fehlertolerante Speicherinfrastruktur in Ihrem Datencenter dienen. Es gibt jedoch Anforderungen und bewährte Methoden, die Sie einhalten sollten, um eine hohe Verfügbarkeit der StorSimple-Lösung sicherzustellen. Überprüfen Sie vor dem Bereitstellen von StorSimple sorgfältig die folgenden Anforderungen und bewährten Methoden für das StorSimple-Gerät und die verbundenen Hostcomputer.

Weitere Informationen zur Überwachung und Wartung der Hardwarekomponenten Ihres StorSimple-Geräts finden Sie unter [Überwachen von Hardwarekomponenten und Status mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-monitor-hardware-status.md) und [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Anforderungen und bewährte Methoden für die Hochverfügbarkeit des StorSimple-Geräts

Lesen Sie die folgenden Informationen sorgfältig durch, um die Hochverfügbarkeit des StorSimple-Geräts sicherzustellen.

#### <a name="pcms"></a>Stromversorgungs- und Kühleinheiten

StorSimple-Geräte enthalten redundante Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs), die per Hot-Swap ausgetauscht werden können. Jedes PCM verfügt über genügend Kapazität, um das gesamte Gehäuse zu versorgen. Um Hochverfügbarkeit zu gewährleisten, müssen beide Stromversorgungs- und Kühleinheiten installiert sein.

* Verbinden Sie Ihre Stromversorgungs- und Kühleinheiten mit unterschiedlichen Stromquellen, um auch beim Ausfall einer Stromquelle die Verfügbarkeit zu gewährleisten.
* Beim Ausfall einer Stromversorgungs- und Kühleinheit fordern Sie sofort einen Austausch an.
* Entfernen Sie eine fehlerhafte Stromversorgungs- und Kühleinheit nur, wenn Sie das Ersatzteil zur Hand haben und bereit zur Installation sind.
* Entfernen Sie nicht beide Stromversorgungs- und Kühleinheiten gleichzeitig. Das PCM enthält den Notfallakku. Wenn Sie beide PCMs entfernen, wird das Gerät ohne Akkuschutz heruntergefahren, und der Gerätestatus wird nicht gespeichert. Weitere Informationen zum Akku finden Sie unter [Warten des Notfallakkus](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Controllermodule

StorSimple-Geräte enthalten redundante Controllermodule, die per Hot-Swap ausgetauscht werden können. Die Controllermodule werden aktiv/passiv ausgeführt. Zu jedem Zeitpunkt ist ein Controllermodul aktiv und dienstbereit, während das anderen Controllermodul passiv ist. Das passive Controllermodul wird eingeschaltet und in Betrieb versetzt, wenn das aktive Controllermodul ausfällt oder entfernt wird. Jedes Controllermodul weist genügend Kapazität auf, um das gesamte Gehäuse zu versorgen. Beide Controllermodule müssen installiert sein, um Hochverfügbarkeit sicherzustellen.

* Stellen Sie sicher, dass jederzeit beide Controllermodule installiert sind.
* Beim Ausfall eines Controllermoduls fordern Sie sofort einen Austausch an.
* Entfernen Sie eine fehlerhaftes Controllermodul nur, wenn Sie das Ersatzteil zur Hand haben und bereit für die Installation sind. Das Entfernen eines Moduls über einen längeren Zeitraum beeinträchtigt die Luftströmung und somit die Kühlung des Systems.
* Stellen Sie sicher, dass die Netzwerkverbindungen auf beiden Controllermodulen identisch sind und dass die verbundenen Netzwerkschnittstellen eine identische Netzwerkkonfigurationen aufweisen.
* Wenn ein Controllermodul ausfällt oder ausgetauscht werden muss, stellen Sie sicher, dass das andere Controllermodul aktiv ist, bevor Sie das fehlerhafte Controllermodul austauschen. Informationen zum Sicherstellen, dass ein Controller aktiv ist, finden Sie unter [Identifizieren des aktiven Controllers im Gerät](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Entfernen Sie nicht beide Controllermodule zur gleichen Zeit. Wenn ein Failover für die Controller durchgeführt wird, dürfen Sie das Standby-Controllermodul nicht herunterfahren oder aus dem Gehäuse entfernen.
* Warten Sie nach einem Failover für die Controller mindestens fünf Minuten, bevor Sie eines der beiden Controllermodule entfernen.

#### <a name="network-interfaces"></a>Netzwerkschnittstellen

Die Controllermodule von StorSimple-Geräten verfügen jeweils über vier 1-Gigabit-und zwei 10-Gigabit-Ethernet-Netzwerkschnittstellen.

* Stellen Sie sicher, dass die Netzwerkverbindungen auf beiden Controllermodulen identisch sind und dass die Netzwerkschnittstellen, mit denen die Controllermodul-Schnittstellen verbunden sind, eine identische Netzwerkkonfigurationen aufweisen.
* Wenn möglich, stellen Sie die Netzwerkverbindungen über unterschiedliche Switches bereit, um die Dienstverfügbarkeit beim Ausfall eines Netzwerkgerätes sicherzustellen.
* Wenn die einzige oder die letzte verbleibende iSCSI-aktivierte Schnittstelle (mit zugewiesenen IP-Adressen) entfernt wird, deaktivieren Sie zunächst die Schnittstelle, und trennen Sie dann die Kabel. Wenn die Schnittstelle zuerst getrennt wird, erfolgt ein Failover vom aktiven Controller zum passiven Controller. Wenn beim passiven Controller auch die entsprechenden Schnittstellen getrennt wurden, führen beide Controller mehrere Neustarts durch, bevor ein Controller aktiviert wird.
* Verbinden Sie auf jedem Controllermodul mindestens zwei DATA-Schnittstellen mit dem Netzwerk.
* Wenn Sie die beiden 10-GbE-Schnittstellen aktiviert haben, stellen Sie diese über unterschiedliche Switches bereit.
* Verwenden Sie nach Möglichkeit auf Servern MPIO, damit die Server Fehler bei Links, dem Netzwerk oder einzelnen Schnittstellen tolerieren können.

Informationen zur Einbindung Ihres Geräts in das Netzwerk, um Hochverfügbarkeit und Leistung zu erzielen, finden Sie unter [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) bzw. [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs und HDDs

StorSimple-Geräte enthalten Solid-State-Datenträger (SSDs) und Festplattenlaufwerke (HDDs), die mit gespiegelten Speicherbereichen geschützt werden. Durch die Verwendung von gespiegelten Speicherbereichen wird sichergestellt, dass das Gerät Fehler von einzelnen oder mehreren SSDs oder HDDs tolerieren kann.

* Stellen Sie sicher, dass alle SSD- und HDD-Module installiert sind.
* Beim Ausfall einer SSD oder HDD fordern Sie sofort einen Austausch an.
* Fällt eine SSD oder HDD aus oder erfordert einen Austausch, dürfen Sie nur die SSD oder HDD entfernen, die ersetzt werden muss.
* Entfernen Sie zu keinem Zeitpunkt mehrere SSDs oder HDDs aus dem System.
  Ein Ausfall von zwei oder mehr Datenträgern eines bestimmten Typs (HDD, SSD) oder aufeinander folgende Fehler innerhalb eines kurzen Zeitraums können zu einer Fehlfunktion im System und zu potenziellen Datenverlusten führen. Wenden Sie sich in einem solchen Fall an den [Microsoft Support](storsimple-8000-contact-microsoft-support.md) .
* Überwachen Sie während des Austauschs **Gemeinsam genutzte Komponenten** auf dem Blatt **Hardwareintegrität** für die Laufwerke auf den SSDs und HDDs. Ein grünes Häkchen beim Status zeigt an, dass die Datenträger fehlerfrei funktionieren, während ein rotes Ausrufezeichen einen Fehler bei einer SSD oder HDD angibt.
* Es wird empfohlen, Cloudmomentaufnahmen für alle Volumes zu konfigurieren, die Sie im Falle eines Systemfehlers schützen müssen.

#### <a name="ebod-enclosure"></a>EBOD-Gehäuse

StorSimple-Geräte des Modells 8600 bieten zusätzlich zum normalen Gehäuse ein EBOD (Extended Bunch of Disks)-Gehäuse. Ein EBOD enthält EBOD-Controller und Festplattenlaufwerke (HDDs), die mit gespiegelten Speicherbereichen geschützt werden. Durch die Verwendung von gespiegelten Speicherbereichen wird sichergestellt, dass das Gerät Fehler von einzelnen oder mehreren HDDs tolerieren kann. Das EBOD-Gehäuse ist mit dem primären Gehäuse über redundante SAS-Kabel verbunden.

* Stellen Sie sicher, dass beide Controllermodule der EBOD-Gehäuse, beide SAS-Kabel und alle Festplatten jederzeit installiert sind.
* Fordern Sie sofort Ersatz an, wenn ein Controllermodul des EBOD-Gehäuses ausfällt.
* Fällt ein Controllermodul des EBOD-Gehäuses aus, vergewissern Sie sich vor dem Austausch, dass das andere Controllermodul aktiv ist. Informationen zum Sicherstellen, dass ein Controller aktiv ist, finden Sie unter [Identifizieren des aktiven Controllers im Gerät](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Überwachen Sie während des Austauschs eines EBOD-Controllermoduls ständig den Status der Komponente im StorSimple-Geräte-Manager-Dienst unter **Überwachen** > **Hardwareintegrität**.
* Wenn ein SAS-Kabel ausfällt oder ersetzt werden muss (der Microsoft Support sollte in eine solche Entscheidung einbezogen werden), tauschen Sie nur das SAS-Kabel aus, das auch ersetzt werden muss.
* Entfernen Sie niemals gleichzeitig beide SAS-Kabel aus dem System.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Empfehlungen zur Hochverfügbarkeit für Ihre Hostcomputer

Lesen Sie sich diese bewährten Methoden sorgfältig durch, um die Hochverfügbarkeit der Hosts sicherzustellen, die mit dem StorSimple-Gerät verbunden sind.

* Konfigurieren von StorSimple mit [Dateiserverclustern mit zwei Knoten][1]. Wenn Sie einzelne Fehlerquellen eliminieren und auf Hostseite Redundanz erzeugen, wird die gesamte Lösung höher verfügbar.
* Verwenden Sie die in Windows Server 2012 (SMB 3.0) verfügbaren permanent verfügbaren Freigaben für Hochverfügbarkeit während des Failovers von Speichercontrollern. Weitere Informationen zum Konfigurieren von Dateiserverclustern und permanent verfügbaren Freigaben mit Windows Server 2012 erhalten Sie in diesem [Vorführvideo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)(in englischer Sprache).

## <a name="next-steps"></a>Nächste Schritte

* [Informationen über StorSimple-Systemeinschränkungen](storsimple-8000-limits.md).
* [Informationen zum Bereitstellen der StorSimple-Lösung](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)