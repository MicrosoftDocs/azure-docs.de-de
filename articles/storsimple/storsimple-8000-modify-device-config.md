---
title: Ändern der Gerätekonfiguration bei der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt, wie der StorSimple-Geräte-Manager-Dienst dazu verwendet werden kann, ein StorSimple-Gerät neu zu konfigurieren, das bereits bereitgestellt wurde.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961174"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Ändern der StorSimple-Gerätekonfiguration

## <a name="overview"></a>Übersicht

Der Abschnitt **Geräteeinstellungen** auf dem Blatt **Einstellungen** im Azure-Portal enthält alle Geräteparameter, die Sie für ein StorSimple-Gerät, das über einen StorSimple-Geräte-Manager-Dienst verwaltet wird, neu konfigurieren können. In diesem Tutorial wird erklärt, wie Sie über das Blatt **Einstellungen** die folgenden gerätebezogenen Aufgaben ausführen:

* Ändern des Anzeigenamens des Geräts
* Ändern der Gerätezeiteinstellungen
* Zuweisen eines sekundären DNS
* Ändern von Netzwerkschnittstellen
* Austauschen oder Neuzuweisen von IP-Adressen

## <a name="modify-device-friendly-name"></a>Ändern des Anzeigenamens des Geräts

Über das Azure-Portal können Sie den Gerätenamen ändern und dem Gerät einen eindeutigen Anzeigenamen Ihrer Wahl zuweisen. Auf dem Blatt **Allgemeine Einstellungen** auf Ihrem Gerät können Sie den Anzeigename des Geräts ändern. Der Anzeigename kann beliebige Zeichen enthalten und darf maximal 64 Zeichen lang sein.

> [!NOTE] 
> Sie können den Gerätenamen im Azure-Portal nur vor Abschluss des Gerätesetups ändern. Nach Abschluss der minimalen Gerätekonfiguration kann der Gerätename nicht mehr geändert werden.

![Gerätename in „Allgemeine Einstellungen“](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Einem StorSimple-Gerät, das mit dem StorSimple-Geräte-Manager-Dienst verbunden ist, wird ein Standardname zugewiesen. Der Standardname basiert üblicherweise auf der Seriennummer des Geräts. Beispielsweise kennzeichnet ein aus 15 Zeichen bestehender Standardgerätename, etwa 8600-SHX0991003G44HT, Folgendes:

* **8600** – Kennzeichnet das Gerätemodell.
* **SHX** – Kennzeichnet den Fertigungsstandort.
* **0991003** – Kennzeichnet ein bestimmtes Produkt.
* **G44HT**– Die letzten 5 Ziffern werden inkrementiert, um eindeutige Seriennummern zu erstellen. Dies ist möglicherweise keine sequenzielle Reihe.

## <a name="modify-device-description"></a>Ändern der Gerätebeschreibung

Auf dem Blatt **Allgemeine Einstellungen** auf Ihrem Gerät können Sie die Beschreibung des Geräts ändern.

![Gerätebeschreibung in „Allgemeine Einstellungen“](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Eine Gerätebeschreibung erleichtert es üblicherweise, den Besitzer und den physischen Standort des Geräts zu identifizieren. Das Beschreibungsfeld muss weniger als 256 Zeichen enthalten.

## <a name="modify-time-settings"></a>Ändern von Zeiteinstellungen

Ihr Gerät muss die Zeit synchronisieren, damit es sich beim Cloudspeicher-Dienstanbieter authentifizieren kann. Auf dem Blatt **Allgemeine Einstellungen** auf Ihrem Gerät können Sie die Zeiteinstellungen des Geräts ändern.

![Gerätebeschreibung in „Allgemeine Einstellungen“ 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Wählen Sie in der Dropdownliste Ihre Zeitzone aus. Sie können bis zu zwei NTP-Server (Network Time Protocol, Netzwerkzeitprotokoll) angeben:

 - **Primärer NTP-Server**: Die Konfiguration ist erforderlich und wird angegeben, wenn Sie das Gerät über Windows PowerShell für StorSimple konfigurieren. Sie können den standardmäßigen Windows-Server **time.windows.com** als Ihren NTP-Server angeben. Sie können die Konfiguration des primären NTP-Servers zwar über das Azure-Portal anzeigen, wenn Sie die Konfiguration ändern möchten, müssen Sie aber die Windows PowerShell-Schnittstelle verwenden. Verwenden Sie das Cmdlet `Set-HcsNTPClientServerAddress`, um den primären NTP-Server Ihres Geräts zu ändern. Weitere Informationen zur Syntax finden Sie unter dem Cmdlet [Set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)).

- **Sekundärer NTP-Server**: Die Konfiguration ist optional. Sie können mit dem Azure-Portal einen sekundären NTP-Server konfigurieren.

Wenn Sie den NTP-Server konfigurieren, müssen Sie sich vergewissern, dass Ihr Netzwerk zulässt, dass der NTP-Datenverkehr von Ihrem Rechenzentrum in das Internet fließt. Wenn Sie einen öffentlichen NTP-Server angeben, müssen Sie sicherstellen, dass Ihre Netzwerkfirewalls und anderen Sicherheitseinrichtungen so konfiguriert sind, dass NTP-Datenverkehr in das externe und aus dem externen Netzwerk fließen kann. Wenn bidirektionaler NTP-Datenverkehr nicht zugelassen ist, müssen Sie einen internen NTP-Server verwenden (ein Windows-Domänencontroller bietet diese Funktion). Wenn Ihr Gerät die Zeit nicht synchronisieren kann, kann es möglicherweise nicht mit Ihrem Cloudspeicheranbieter kommunizieren.

Um eine Liste der öffentlichen NTP-Server anzuzeigen, wechseln Sie zu [NTP.Servers Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Was passiert, wenn das Gerät in einer anderen Zeitzone bereitgestellt wird?

Wenn das Gerät in einer anderen Zeitzone bereitgestellt wird, wird die Zeitzone des Geräts geändert. Angesichts der Tatsache, dass alle Sicherungsrichtlinien die Zeitzone des Geräts verwenden, werden die Sicherungsrichtlinien automatisch entsprechend der neuen Zeitzone angepasst. Es ist kein Benutzereingriff erforderlich.

## <a name="modify-dns-settings"></a>Ändern von DNS-Einstellungen

Ein DNS-Server wird verwendet, wenn das Gerät versucht, mit dem Cloudspeicher-Dienstanbieter zu kommunizieren. Auf dem Blatt **Netzwerkeinstellungen** auf Ihrem Gerät können Sie die konfigurierten DNS-Einstellungen anzeigen und ändern. 

![DNS-Einstellungen in „Netzwerkeinstellungen“](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Für Hochverfügbarkeit müssen Sie bei der ersten Gerätebereitstellung sowohl den primären als auch den sekundäre DNS-Server konfigurieren.

**Primärer DNS-Server**: Sie verwenden Windows PowerShell für StorSimple zur ersten Angabe des primären DNS-Servers während des Einrichtung. Sie können die Konfiguration des primären DNS-Servers nur über die Windows PowerShell-Schnittstelle ändern. Verwenden Sie das Cmdlet `Set-HcsDNSClientServerAddress`, um den primären DNS-Server Ihres Geräts zu ändern. Weitere Informationen zur Syntax finden Sie unter dem Cmdlet [Set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)).

**Sekundärer DNS-Server**: Verwenden Sie zum Ändern des sekundären DNS-Servers das Cmdlet `Set-HcsDNSClientServerAddress` in der Windows PowerShell-Schnittstelle des Geräts oder das Blatt **Netzwerkeinstellungen** Ihres StorSimple-Geräts im Azure-Portal.

Um den sekundären DNS-Server im Azure-Portal zu ändern, führen Sie die folgenden Schritte aus.

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie in der Liste der Geräte auf Ihr Gerät, und wählen Sie es aus.

2. Wechseln Sie auf dem Blatt **Einstellungen** zu **Geräteeinstellungen > Netzwerk**. Damit öffnen Sie das Blatt **Netzwerkeinstellungen**. Klicken Sie auf die Kachel **DNS-Einstellungen**. Ändern Sie die IP-Adresse des sekundäre DNS-Servers.

    ![Ändern der IP-Adresse des sekundären DNS-Servers](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Klicken Sie auf der Befehlszeile auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**.

    ![Speichern und Bestätigen von Änderungen](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ändern von Netzwerkschnittstellen

Ihr Gerät hat sechs Netzwerkschnittstellen, von denen vier 1-GbE- und zwei 10-GbE-Schnittstellen sind. Diese Schnittstellen sind mit DATA 0 bis DATA 5 gekennzeichnet. DATA 0, DATA 1, DATA 4 und DATA 5 sind 1-GbE-Netzwerkschnittstellen, DATA 2 und DATA 3 sind 10-GbE-Netzwerkschnittstellen.

Auf dem Blatt **Netzwerkeinstellungen** können Sie jede der zu verwendenden Schnittstellen konfigurieren.

![Konfigurieren von Netzwerkschnittstellen über die Netzwerkeinstellungen](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Um Hochverfügbarkeit zu gewährleisten, sollte Ihr Gerät mindestens zwei iSCSI-Schnittstellen und zwei cloudfähige Schnittstellen haben. Es ist zwar nicht erforderlich, empfiehlt sich aber, nicht verwendete Schnittstellen zu deaktivieren.

Für jede Netzwerkschnittstelle werden die folgenden Parameter angezeigt:

* **Geschwindigkeit** – Dieser Parameter ist nicht benutzerkonfigurierbar. DATA 0, DATA 1, DATA 4 und DATA 5 sind immer 1-GbE-Schnittstellen, und DATA 2 und DATA 3 sind 10-GbE-Schnittstellen.
  
  > [!NOTE]
  > Geschwindigkeit und Duplexmodus werden immer automatisch ausgehandelt. Großrahmen werden nicht unterstützt.
  
* **Schnittstellenstatus** – Eine Schnittstelle kann aktiviert oder deaktiviert sein. Wenn sie aktiviert ist, versucht das Gerät, die Schnittstelle zu verwenden. Es wird empfohlen, dass nur die Schnittstellen aktiviert werden, die mit dem Netzwerk verbunden sind und verwendet werden. Deaktivieren Sie alle Schnittstellen, die Sie nicht verwenden.
* **Schnittstellentyp** – Über diesen Parameter können Sie iSCSI-Datenverkehr und Cloudspeicher-Datenverkehr voneinander trennen. Der Parameter kann eine der folgenden Einstellungen haben:
  
  * **Cloud aktiviert** – Ist diese Einstellung aktiviert, verwendet das Gerät diese Schnittstelle, um mit der Cloud zu kommunizieren.
  * **iSCSI-aktiviert** – Ist diese Einstellung aktiviert, verwendet das Gerät diese Schnittstelle, um mit dem iSCSI-Host zu kommunizieren.
    
    Es wird empfohlen, dass Sie iSCSI-Datenverkehr und Cloudspeicher-Datenverkehr voneinander trennen. Beachten Sie auch, Sie kein Gateway zuweisen müssen, wenn sich der Host und Ihr Gerät im selben Subnetz befinden. Befindet sich der Host aber in einem anderen Subnetz als Ihr Gerät, müssen Sie ein Gateway zuweisen.
* **IP-Adresse**: Wenn Sie eine der Netzwerkschnittstellen konfigurieren, müssen Sie eine virtuelle IP-Adresse (VIP) konfigurieren. Diese kann IPv4 oder IPv6 oder beides sein. Für die Netzwerkschnittstellen eines Geräts werden sowohl die IPv4- als auch die IPv6-Adressenfamilie unterstützt. Wenn Sie IPv4 verwenden, geben Sie eine 32-Bit-IP-Adresse (*xxx.xxx.xxx.xxx*) in Dezimalschreibweise mit Punkten an. Wenn Sie IPv6 verwenden, geben Sie einfach ein Präfix aus vier Ziffern an. Ausgehend von diesem Präfix wird dann automatisch eine 128-Bit-Adresse für die Netzwerkschnittstelle Ihres Geräts generiert.
* **Subnetz** – Dieser Parameter bezieht sich auf die Subnetzmaske und wird über die Windows PowerShell-Schnittstelle konfiguriert.
* **Gateway** – Dies ist das Standardgateway, das von dieser Schnittstelle verwendet werden muss, wenn sie versucht, mit Knoten zu kommunizieren, die sich nicht im selben IP-Adressbereich (Subnetz) befinden. Das Standardgateway muss sich im selben Adressbereich (Subnetz) wie die durch die Subnetzmaske bestimmte IP-Adresse der Schnittstelle befinden.
* **Feste IP-Adresse** – Dieses Feld steht nur beim Konfigurieren der DATA 0-Schnittstelle zur Verfügung. Für Vorgänge wie etwa Updates oder Problembehandlung für das Gerät müssen Sie möglicherweise eine direkte Verbindung mit dem Gerätecontroller herstellen. Die feste IP-Adresse kann dazu verwendet werden, sowohl auf den aktiven als auch auf den passiven Controller auf Ihrem Gerät zuzugreifen.

> [!NOTE]
> * Um ordnungsgemäßen Betrieb zu gewährleisten, sollten Sie die Schnittstellengeschwindigkeit und den Duplexmodus an dem Switch überprüfen, mit dem jede Geräteschnittstelle verbunden ist. Switchschnittstellen müssen entweder Gigabit-Ethernet (1000 MBit/s) aushandeln oder für Gigabit-Ethernet konfiguriert sein und im Vollduplexmodus arbeiten. Schnittstellen, die mit niedrigeren Geschwindigkeiten oder im Halbduplexmodus arbeiten, führen zu Leistungsproblemen.
> * Um Störungen und Ausfallzeiten zu minimieren, sollten Sie „Portfast“ für jeden der Switch-Ports aktivieren, mit denen die iSCSI-Netzwerkschnittstelle Ihres Geräts verbunden werden soll. Dadurch wird sichergestellt, dass die Netzwerkverbindungen im Fall eines Failovers schnell hergestellt werden können.

### <a name="configure-data-0"></a>Konfigurieren von DATA 0

DATA 0 ist standardmäßig cloudfähig. Wenn Sie DATA 0 konfigurieren, müssen Sie auch zwei feste IP-Adressen konfigurieren, eine für jeden Controller. Diese festen IP-Adressen können verwendet werden, um direkt auf die Gerätecontroller zuzugreifen, und sind nützlich, wenn Sie Updates auf dem Gerät installieren, damit die Garbage Collection problemlos ausgeführt wird, oder wenn Sie zur Problembehandlung auf die Controller zugreifen möchten.

Sie können die festen IP-Controller über das Einstellungsblatt für DATA 0 neu konfigurieren.

![Netzwerkschnittstelle DATA 0 konfigurieren](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Die festen IP-Adressen für den Controller werden dazu verwendet, die Updates für das Gerät vorzunehmen, und sicherzustellen, dass Algorithmen zur Freigabe von Speicherplatz (Garbage Collection) ordnungsgemäß ausgeführt werden. Aus diesem Grund müssen die festen IP-Adressen routingfähig sein und eine Verbindung mit dem Internet herstellen können.

### <a name="configure-data-1---data-5"></a>Konfigurieren von DATA 1 – DATA 5

Für die Netzwerkschnittstellen DATA 1 bis DATA 5 können Sie die Netzwerkeinstellungen wie im folgenden Screenshot gezeigt konfigurieren:

![Konfigurieren der Netzwerkschnittstellen DATA 1 – DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Austauschen oder Neuzuweisen von IP-Adressen

Derzeit führt ein Controller, wenn einer Netzwerkschnittstelle auf ihm eine VIP-Adresse zugeordnet ist, die verwendet wird (durch dasselbe Gerät oder ein anderes Gerät im Netzwerk), ein Failover aus. Wenn Sie VIPs für die Netzwerkschnittstelle eines Geräts austauschen oder erneut zuweisen, müssen Sie sorgfältig vorgehen, um eine doppelte IP-Adresse zu vermeiden.

Führen Sie die folgenden Schritte aus, wenn Sie die VIPs für eine der Netzwerkschnittstellen austauschen oder neu zuweisen möchten:

#### <a name="to-reassign-ips"></a>So weisen Sie IP-Adressen zu

1. Löschen Sie die IP-Adresse für beide Schnittstellen.
2. Nachdem die IP-Adressen gelöscht sind, weisen Sie die neuen IP-Adressen den entsprechenden Schnittstellen zu.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum [Konfigurieren von MPIO für Ihr StorSimple-Gerät](storsimple-8000-configure-mpio-windows-server.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).