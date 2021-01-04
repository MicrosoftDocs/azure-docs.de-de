---
title: Konfigurieren von MPIO für Ihr StorSimple-Gerät | Microsoft Docs
description: Beschreibt, wie Sie Multipfad-E/A für Ihr StorSimple-Gerät konfigurieren, das mit einem Host unter Windows Server 2012 R2 verbunden ist.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968807"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurieren von Multipfad-E/A für Ihr StorSimple-Gerät

In diesem Tutorial werden die Schritte zum Installieren und Verwenden von MPIO (Multipath I/O, Multipfad-E/A) auf einem Host unter Windows Server 2012 R2 beschrieben, der mit einem physischen StorSimple-Gerät verbunden ist. Die Anleitung in diesem Artikel gilt nur für physische Geräte der StorSimple 8000-Serie. MPIO wird von StorSimple Cloud Appliances derzeit nicht unterstützt.

Windows Server unterstützt das MPIO-Feature (Multipath I/O, Multipfad-E/A), um Sie bei der Erstellung hochverfügbarer, fehlertoleranter iSCSI-Netzwerkkonfigurationen zu unterstützen. MPIO verwendet redundante physische Pfadkomponenten (Adapter, Kabel und Switches), um logische Pfade zwischen dem Server und dem Speichergerät zu erstellen. Wenn bei einer Komponente ein Fehler auftritt, durch den ein logischer Pfad fehlschlägt, verwendet die Multipfad-Logik einen anderen Pfad für E/A, sodass Anwendungen weiterhin auf ihre Daten zugreifen können. Darüber hinaus kann MPIO abhängig von Ihrer Konfiguration auch die Leistung durch ein Umverteilen der Lasten auf alle Pfade verbessern. Weitere Informationen finden Sie in der [MPIO-Übersicht](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Übersicht und Features von MPIO").

Für eine hohe Verfügbarkeit Ihrer StorSimple-Lösung sollte MPIO auf dem StorSimple-Gerät konfiguriert werden. Wenn MPIO auf den Hostservern unter Windows Server 2012 R2 installiert ist, können die Server den Ausfall einer Verknüpfung, des Netzwerks oder einer Schnittstelle tolerieren.

## <a name="mpio-configuration-summary"></a>Zusammenfassung der MPIO-Konfiguration

MPIO ist ein optionales Feature und wird unter Windows Server nicht standardmäßig installiert. Diese Funktion sollte als Feature über den Server-Manager installiert werden.

Führen Sie die folgenden Schritte aus, um MPIO auf Ihrem StorSimple-Gerät zu konfigurieren:

* Schritt 1: Installieren von MPIO auf dem Windows Server-Host
* Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes
* Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host
* Schritt 4: Konfigurieren von MPIO für Hochverfügbarkeit und Lastenausgleich

Jeder der vorherigen Schritte wird in den folgenden Abschnitten erläutert.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Schritt 1: Installieren von MPIO auf dem Windows Server-Host

Gehen Sie folgendermaßen vor, um dieses Feature auf Ihrem Windows Server-Host zu installieren.

#### <a name="to-install-mpio-on-the-host"></a>So installieren Sie MPIO auf dem Host

1. Öffnen Sie den Server-Manager auf Ihrem Windows Server-Host. Der Server-Manager wird standardmäßig gestartet, wenn sich ein Mitglied der Gruppe „Administratoren“ an einem Computer unter Windows Server 2012 R2 oder Windows Server 2012 anmeldet. Wenn der Server-Manager nicht bereits geöffnet ist, klicken Sie auf **Start > Server-Manager**.
   
   ![Server-Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klicken Sie auf **Server-Manager > Dashboard > Rollen und Features hinzufügen**. Dadurch wird der Assistent **Rollen und Features hinzufügen** gestartet.
   
   ![Hinzufügen von Rollen und Features – Assistent 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Gehen Sie im Assistenten **Rollen und Features hinzufügen** folgendermaßen vor:
   
   1. Klicken Sie auf der Seite **Voraussetzungen** auf **Weiter**.
   2. Akzeptieren Sie auf der Seite **Auswählen des Installationstyps** die Standardeinstellung **Rollenbasierte oder featurebasierte Installation**. Klicken Sie auf **Weiter**.
   
       ![Hinzufügen von Rollen und Features – Assistent 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Wählen Sie auf der Seite **Zielserver auswählen** die Option **Einen Server aus Serverpool auswählen** aus. Ihr Hostserver sollte automatisch ermittelt werden. Klicken Sie auf **Weiter**.
   4. Klicken Sie auf der Seite **Serverrollen auswählen** auf **Weiter**.
   5. Wählen Sie auf der Seite **Auswählen von Features** die Option **Multipfad-E/A** aus, und klicken Sie dann auf **Weiter**.
   
       ![Hinzufügen von Rollen und Features – Assistent 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Bestätigen Sie auf der Seite **Installationsauswahl bestätigen** Ihre Auswahl, und wählen Sie dann wie unten gezeigt **Zielserver bei Bedarf automatisch neu starten** aus. Klicken Sie auf **Installieren**.
   
       ![Hinzufügen von Rollen und Features – Assistent 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Nachdem die Installation abgeschlossen wurde, erhalten Sie eine Benachrichtigung. Klicken Sie auf **Schließen** , um den Assistenten zu schließen.
   
       ![Hinzufügen von Rollen und Features – Assistent 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes

MPIO muss konfiguriert werden, damit StorSimple-Volumes erkannt werden. Führen Sie zum Konfigurieren von MPIO für die Erkennung von StorSimple-Geräten die folgenden Schritte aus.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>So konfigurieren Sie MPIO für StorSimple-Volumes

1. Öffnen Sie die **MPIO-Konfiguration**. Klicken Sie auf **Server-Manager > Dashboard > Tools > MPIO**.
2. Wählen Sie im Dialogfeld **MPIO-Eigenschaften** die Registerkarte **Multipfade suchen** aus.
3. Wählen Sie **Unterstützung für iSCSI-Geräte hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**.  
   ![MPIO-Eigenschaften – Multipfade ermitteln](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Starten Sie den Server neu, wenn Sie dazu aufgefordert werden.
5. Klicken Sie im Dialogfeld **MPIO-Eigenschaften** auf die Registerkarte **MPIO-Geräte**. Klicken Sie auf **Hinzufügen**.
    </br>![MPIO-Eigenschaften – MPIO-Geräte](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Geben Sie im Dialogfeld **MPIO-Unterstützung hinzufügen** unter **Gerätehardware-ID** die Seriennummer des Geräts ein. Greifen Sie auf den StorSimple-Geräte-Manager-Dienst zu, um die Seriennummer des Geräts zu erhalten. Navigieren Sie zu **Geräte > Dashboard**. Die Seriennummer des Geräts wird im rechten Bereich **Auf einen Blick** des Gerätedashboards angezeigt.
    </br>
    ![Hinzufügen von MPIO-Unterstützung](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starten Sie den Server neu, wenn Sie dazu aufgefordert werden.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host

Nachdem MPIO unter Windows Server konfiguriert wurde, können auf dem StorSimple-Gerät erstellte Volumes bereitgestellt werden und anschließend MPIO für Redundanz nutzen. Führen Sie die folgenden Schritte aus, um ein Volume bereitzustellen.

#### <a name="to-mount-volumes-on-the-host"></a>So stellen Sie Volumes auf dem Host bereit

1. Öffnen Sie das Fenster **Eigenschaften des iSCSI-Initiators** auf dem Windows Server-Host. Klicken Sie auf **Server-Manager > Dashboard > Tools > iSCSI-Initiator**.
2. Klicken Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** auf die Registerkarte „Erkennung“, und klicken Sie dann auf **Zielportal ermitteln**.
3. Gehen Sie im Dialogfeld **Zielportal ermitteln** folgendermaßen vor:
   
   1. Geben Sie die IP-Adresse des DATA-Ports Ihres StorSimple-Geräts ein (Beispiel: DATA 0).
   2. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.
     
      > [!IMPORTANT]
      > **Wenn Sie ein privates Netzwerk für iSCSI-Verbindungen verwenden, geben Sie die IP-Adresse des DATA-Ports ein, der mit dem privaten Netzwerk verbunden ist.**
    
4. Wiederholen Sie die Schritte 2 bis 3 für eine zweite Netzwerkschnittstelle (z. B. DATA 1) auf Ihrem Gerät. Denken Sie daran, dass diese Schnittstellen für iSCSI aktiviert sein sollten. Weitere Informationen finden Sie unter [Ändern von Netzwerkschnittstellen](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Wählen Sie die Registerkarte **Ziele** im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus. Der Ziel-IQN des StorSimple-Geräts sollte unter **Ermittelte Ziele** angezeigt werden.

   ![Eigenschaften des iSCSI-Initiators – Registerkarte "Ziele"](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klicken Sie auf **Verbinden** , um eine iSCSI-Sitzung mit Ihrem StorSimple-Gerät einzurichten. Das Dialogfeld **Mit Ziel verbinden** wird angezeigt.
7. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.
8. Gehen Sie im Dialogfeld **Erweiterte Eigenschaften** folgendermaßen vor:
   
   1. Wählen Sie in der Dropdownliste **Lokaler Adapter** die Option **Microsoft iSCSI-Initiator** aus.
   2. Wählen Sie die IP-Adresse des Hosts in der Dropdownliste **Initiator-IP** aus.
   3. Wählen Sie die IP-Adresse der Geräteschnittstelle in der Dropdownliste **Zielportal-IP** aus.
   4. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.
9. Klicken Sie auf **Eigenschaften**. Klicken Sie im Dialogfeld **Eigenschaften** auf **Sitzung hinzufügen**.
10. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.
11. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:

    1. Wählen Sie "Microsoft iSCSI-Initiator" in der Dropdownliste **Lokaler Adapter** aus.
    2. Wählen Sie die IP-Adresse, die dem Host entspricht, in der Dropdownliste **Initiator-IP** aus. In diesem Fall werden zwei Netzwerkschnittstellen auf dem Gerät mit einer einzelnen Netzwerkschnittstelle auf dem Host verbunden. Aus diesem Grund handelt es sich bei dieser Schnittstelle um die gleiche Schnittstelle, die für die erste Sitzung bereitgestellt wurde.
    3. Wählen Sie die IP-Adresse für die zweite Datenschnittstelle, die auf dem Gerät aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
    4. Klicken Sie auf **OK** , um zum Dialogfeld "Eigenschaften des iSCSI-Initiators" zurückzukehren. Sie haben dem Ziel eine zweite Sitzung hinzugefügt.
12. Öffnen Sie die **Computerverwaltung**, indem Sie zu **Server-Manager > Dashboard > Computerverwaltung** navigieren. Klicken Sie im linken Bereich auf **Speicher > Datenträgerverwaltung**. Die auf dem StorSimple-Gerät erstellten und für diesen Host sichtbaren Volumes werden unter **Datenträgerverwaltung** als neue Datenträger angezeigt.
13. Initialisieren Sie den Datenträger, und erstellen Sie dann ein neues Volume. Wählen Sie während des Formatierungsvorgangs eine Blockgröße von 64 KB aus.
    
    ![Datenträgerverwaltung](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Klicken Sie unter **Datenträgerverwaltung** mit der rechten Maustaste auf den **Datenträger**, und wählen Sie dann **Eigenschaften** aus.
15. Klicken Sie im Dialogfeld **Eigenschaften von Multipfad-Datenträgergerät** der StorSimple-Modellnummer auf die Registerkarte **MPIO**.
    
    ![StorSimple 8100 – Eigenschaften von Multipfad-Datenträgergerät](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Klicken Sie im Abschnitt **DSM-Name** auf **Details**, und vergewissern Sie sich, dass die Parameter auf die Standardparameter festgelegt sind. Die Standardparameter lauten:
    
    * Pfadüberprüfungszeitraum = 30
    * Anzahl der Wiederholungsversuche = 3
    * Zeitraum für das Entfernen von PDO = 20
    * Wiederholungsintervall = 1
    * Pfadüberprüfung aktiviert = nicht aktiviert.

> [!NOTE]
> **Ändern Sie die Standardparameter nicht.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Schritt 4: Konfigurieren von MPIO für Hochverfügbarkeit und Lastenausgleich

Für auf Multipfad basierende Hochverfügbarkeit und den Lastenausgleich müssen mehrere Sitzungen manuell hinzugefügt werden, um die verschiedenen verfügbaren Pfade zu deklarieren. Wenn beispielsweise der Host und das Gerät jeweils zwei Schnittstellen haben, die mit dem iSCSI-Netzwerk verbunden sind, benötigen Sie vier Sitzungen, die mit den richtigen Pfadpermutationen konfiguriert sein müssen. (Wenn sich jede DATA- und Host-Schnittstelle in einem anderen IP-Subnetz befindet und nicht routingfähig ist, sind nur zwei Sitzungen erforderlich.)

**Es wird empfohlen, dass Sie über mindestens acht aktive parallele Sitzungen zwischen dem Gerät und dem Anwendungshost verfügen.** Dies kann erreicht werden, indem vier Netzwerkschnittstellen auf dem Windows Server-System aktiviert werden. Verwenden Sie physische Netzwerkschnittstellen oder virtuelle Schnittstellen über Netzwerkvirtualisierungstechnologien auf der Hardware- oder Betriebssystemebene auf Ihrem Windows Server-Host. Bei zwei Netzwerkschnittstellen auf dem Gerät würde diese Konfiguration acht aktive Sitzungen ergeben. Diese Konfiguration trägt zur Optimierung des Geräte- und Clouddurchsatzes bei.

> [!IMPORTANT]
> **Es wird nicht empfohlen, 1-GbE- und 10-GbE-Netzwerkschnittstellen zu mischen. Bei Verwendung von zwei Netzwerkschnittstellen müssen beide vom gleichen Typ sein.**

Das folgende Verfahren beschreibt, wie Sitzungen hinzugefügt werden, wenn ein StorSimple-Gerät mit zwei Netzwerkschnittstellen mit einem Host mit zwei Netzwerkschnittstellen verbunden ist. Damit verfügen Sie lediglich über vier aktive Sitzungen. Führen Sie die gleichen Schritte für ein StorSimple-Gerät mit zwei Netzwerkschnittstellen aus, das mit einem Host mit vier Netzwerkschnittstellen verbunden ist. Sie müssen dann anstelle der hier beschriebenen vier Sitzungen acht Sitzungen konfigurieren.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>So konfigurieren Sie MPIO für Hochverfügbarkeit und Lastenausgleich

1. Führen Sie eine Ermittlung des Ziels aus: Klicken Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Erkennung** auf **Portal ermitteln**.
2. Geben Sie im Dialogfeld **Mit Ziel verbinden** die IP-Adresse einer Netzwerkschnittstelle des Geräts ein.
3. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.
4. Wählen Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** die Registerkarte **Ziele** aus, markieren Sie das erkannte Ziel, und klicken Sie dann auf **Verbinden**. Das Dialogfeld **Mit Ziel verbinden** wird angezeigt.
5. Gehen Sie im Dialogfeld **Mit Ziel verbinden** folgendermaßen vor:
   
   1. Lassen Sie die Standardeinstellung für das ausgewählte Ziel für **Diese Verbindung der Liste der bevorzugten Ziele hinzufügen** unverändert. Dies veranlasst das Gerät, bei jedem Neustart dieses Computers automatisch einen Neustart der Verbindung zu versuchen.
   2. Aktivieren Sie das Kontrollkästchen **Multipfad aktivieren** .
   3. Klicken Sie auf **Erweitert**.
6. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
   
   1. Wählen Sie in der Dropdownliste **Lokaler Adapter** die Option **Microsoft iSCSI-Initiator** aus.
   2. Wählen Sie in der Dropdownliste **Initiator-IP** die IP-Adresse aus, die der ersten Schnittstelle auf dem Host entspricht (iSCSI-Schnittstelle).
   3. Wählen Sie in der Dropdownliste **Zielportal-IP** die IP-Adresse für die erste auf dem Gerät aktivierte Datenschnittstelle aus.
   4. Klicken Sie auf **OK** , um zum Dialogfeld "Eigenschaften des iSCSI-Initiators" zurückzukehren.
7. Klicken Sie auf **Eigenschaften** und dann im Dialogfeld **Eigenschaften** auf **Sitzung hinzufügen**.
8. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**, und klicken Sie dann auf **Erweitert**.
9. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
   
   1. Wählen Sie in der Dropdownliste **Lokaler Adapter** die Option **Microsoft iSCSI-Initiator** aus.
   2. Wählen Sie in der Dropdownliste **Initiator-IP** die IP-Adresse aus, die der zweiten iSCSI-Schnittstelle auf dem Host entspricht.
   3. Wählen Sie die IP-Adresse für die zweite Datenschnittstelle, die auf dem Gerät aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
   4. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren. Sie haben dem Ziel damit eine zweite Sitzung hinzugefügt.
10. Wiederholen Sie die Schritte 8 bis 10, um dem Ziel weitere Sitzungen (Pfade) hinzuzufügen. Mit zwei Schnittstellen auf dem Host und zwei Schnittstellen auf dem Gerät können Sie insgesamt vier Sitzungen hinzufügen.
11. Nachdem Sie die gewünschten Sitzungen (Pfade) hinzugefügt haben, wählen Sie das Ziel im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus, und klicken dann auf **Eigenschaften**. Beachten Sie auf der Registerkarte "Sitzungen" des Dialogfelds **Eigenschaften** die vier Sitzungsbezeichner, die den möglichen Pfadpermutationen entsprechen. Wenn Sie eine Sitzung abbrechen möchten, aktivieren Sie das Kontrollkästchen neben einem Sitzungsbezeichner, und klicken Sie dann auf **Trennen**.
12. Wenn Sie Geräte in den Sitzungen anzeigen möchten, wählen Sie die Registerkarte **Geräte** aus. Klicken Sie zum Konfigurieren der MPIO-Richtlinie für ein ausgewähltes Gerät auf **MPIO**. Das Dialogfeld **Gerätedetails** wird angezeigt. Auf der Registerkarte **MPIO** können Sie die entsprechenden Einstellungen für die **Lastenausgleichsrichtlinie** auswählen. Sie können auch den Pfadtyp **Aktiv** oder **Standby** anzeigen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum Thema [Verwenden des StorSimple-GeräteManager-Diensts, um eine StorSimple-Gerätekonfiguration zu ändern](storsimple-8000-modify-device-config.md).