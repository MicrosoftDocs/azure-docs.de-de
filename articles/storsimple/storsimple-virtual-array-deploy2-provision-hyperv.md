---
title: Bereitstellen eines StorSimple Virtual Array in Hyper-V | Microsoft-Dokumentation
description: In diesem zweiten Tutorial zum Thema StorSimple Virtual Array-Bereitstellungen geht es um das Bereitstellen eines virtuellen Arrays in Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61416112"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Bereitstellen des StorSimple Virtual Array – Bereitstellen in Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Übersicht
In diesem Tutorial erfahren Sie, wie Sie ein StorSimple Virtual Array auf einem Hostsystem mit Hyper-V unter Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2 bereitstellen. Dieser Artikel bezieht sich auf die Bereitstellung von StorSimple Virtual Arrays im Azure-Portal und in der Microsoft Azure Government-Cloud.

Sie benötigen Administratorrechte, um ein virtuelles Array bereitzustellen und zu konfigurieren. Die Bereitstellung und die anfängliche Einrichtung dauern ca. 10 Minuten.

## <a name="provisioning-prerequisites"></a>Voraussetzungen für die Bereitstellung
Hier finden Sie die Voraussetzungen, die erfüllt sein müssen, damit Sie ein virtuelles Array auf einem Hostsystem mit Hyper-V unter Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2 bereitstellen können.

### <a name="for-the-storsimple-device-manager-service"></a>Voraussetzungen für den StorSimple-Geräte-Manager-Dienst
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben alle Schritte unter [Vorbereiten des Portals für StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)ausgeführt.
* Sie haben das Image des virtuellen Arrays für Hyper-V aus dem Azure-Portal heruntergeladen. Weitere Informationen finden Sie unter **Schritt 3: Herunterladen des Image des virtuellen Arrays** in den [Anleitungen zum Vorbereiten des Portals für StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Die Software, die auf dem StorSimple Virtual Array ausgeführt wird, kann nur in Verbindung mit dem StorSimple-Geräte-Manager-Dienst verwendet werden.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Voraussetzungen für das StorSimple Virtual Array
Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Array bereitstellen:

* Sie haben Zugriff auf ein Hostsystem mit Hyper-V unter Windows Server 2008 R2 oder höher, das zum Bereitstellen eines Geräts verwendet werden kann.
* Das Hostsystem verfügt für die Bereitstellung des virtuellen Arrays über die folgenden Ressourcen:

  * Mindestens 4 Kerne
  * Mindestens 8 GB RAM Wenn Sie planen, das virtuelle Array als Dateiserver zu konfigurieren, unterstützen 8 GB weniger als 2 Millionen Dateien. Sie benötigen 16 GB RAM, um 2–4 Millionen Dateien zu unterstützen.
  * Eine Netzwerkschnittstelle
  * Einen virtuellen Datenträger mit 500 GB

### <a name="for-the-network-in-the-datacenter"></a>Für das Netzwerk im Datencenter
Bevor Sie beginnen, überprüfen Sie die Netzwerkanforderungen zum Bereitstellen eines StorSimple Virtual Array, und konfigurieren Sie das Netzwerk des Rechenzentrums entsprechend. Weitere Informationen finden Sie unter [StorSimple Virtual Array – Netzwerkanforderungen](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Schritt-für-Schritt-Bereitstellung
Zum Bereitstellen und Herstellen der Verbindung mit einem virtuellen Array müssen Sie die folgenden Schritte ausführen:

1. Stellen Sie sicher, dass das Hostsystem über genügend Ressourcen verfügt, um die Mindestanforderungen des virtuellen Arrays zu erfüllen.
2. Stellen Sie ein virtuelles Array in Ihrem Hypervisor bereit.
3. Starten Sie das virtuelle Array, und rufen Sie die IP-Adresse ab.

Jeder dieser Schritte wird in den folgenden Abschnitten erläutert.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Arrays erfüllt
Um ein virtuelles Array zu erstellen, benötigen Sie Folgendes:

* Die unter Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2 mit SP1 installierte Hyper-V-Rolle.
* Microsoft Hyper-V Manager auf einem Microsoft Windows-Client mit einer Verbindung mit dem Host

Sie müssen gewährleisten, dass die zugrunde liegende Hardware (Hostsystem), auf der Sie das virtuelle Array erstellen, die folgenden Ressourcen für Ihr virtuelles Array zur Verfügung stellen kann:

* Mindestens 4 Kerne
* Mindestens 8 GB RAM Wenn Sie planen, das virtuelle Array als Dateiserver zu konfigurieren, unterstützen 8 GB weniger als 2 Millionen Dateien. Sie benötigen 16 GB RAM, um 2–4 Millionen Dateien zu unterstützen.
* Eine Netzwerkschnittstelle
* Einen virtuellen Datenträger mit 500 GB für Systemdaten

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Schritt 2: Bereitstellen eines virtuellen Arrays in Hypervisor
Führen Sie die folgenden Schritte aus, um ein Gerät im Hypervisor bereitzustellen.

#### <a name="to-provision-a-virtual-array"></a>So stellen Sie ein virtuelles Array bereit
1. Kopieren Sie das Image des virtuellen Arrays auf den lokalen Datenträger Ihres Windows Server-Hosts. Dies ist das Image (VHD oder VHDX), das Sie über das Azure-Portal heruntergeladen haben. Notieren Sie sich den Speicherort, an den Sie das Image kopiert haben, da Sie es später noch benötigen.
2. Öffnen Sie den **Server-Manager**. Klicken Sie in der oberen rechten Ecke auf **Extras**, und wählen Sie **Hyper-V-Manager**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Wenn Sie Windows Server 2008 R2 ausführen, öffnen Sie den Hyper-V-Manager. Klicken Sie im Server-Manager auf **Rollen > Hyper-V > Hyper-V-Manager**.
3. Klicken Sie im **Hyper-V-Manager** unter „Bereich“ mit der rechten Maustaste auf Ihren Systemknoten, um das Kontextmenü zu öffnen. Klicken Sie dann auf **Neu** > **Virtueller Computer**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Klicken Sie auf der Seite **Vorbereitung** des Assistenten für neue virtuelle Computer auf **Weiter**.
5. Geben Sie auf der Seite **Namen und Speicherort angeben** einen **Namen** für Ihr virtuelles Array an. Klicken Sie auf **Weiter**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Wählen Sie auf der Seite **Generation angeben** den Imagetyp für das Gerät aus, und klicken Sie anschließend auf **Weiter**. Diese Seite wird nicht angezeigt, wenn Sie Windows Server 2008 R2 verwenden.

   * Wählen Sie **Generation 2** , wenn Sie ein VHDX-Image für Windows Server 2012 oder höher heruntergeladen haben.
   * Wählen Sie **Generation 1** , wenn Sie ein VHD-Image für Windows Server 2008 R2 oder höher heruntergeladen haben.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Geben Sie auf der Seite **Speicher zuweisen** einen **Startspeicher** von mindestens **8192 MB** an, aktivieren Sie nicht den dynamischen Arbeitsspeicher, und klicken Sie auf **Weiter**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Geben Sie auf der Seite **Netzwerk konfigurieren** den virtuellen Switch an, der mit dem Internet verbunden ist, und klicken Sie auf **Weiter**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Vorhandene virtuelle Festplatte verwenden**, geben Sie den Speicherort des virtuellen Arrays an (VHDX oder VHD), und klicken Sie anschließend auf **Weiter**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Überprüfen Sie die **Zusammenfassung**, und klicken Sie dann auf **Fertig stellen**, um den virtuellen Computer zu erstellen.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Sie benötigen vier Kerne, um die Mindestanforderungen zu erfüllen. Um vier virtuelle Prozessoren hinzuzufügen, wählen Sie im Fenster **Hyper-V-Manager** Ihr Hostsystem aus. Suchen Sie im rechten Bereich unter der Liste **Virtuelle Computer** den virtuellen Computer, den Sie gerade erstellt haben. Klicken Sie mit der rechten Maustaste auf den ausgewählten Namen der Maschine, und wählen Sie **Einstellungen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Klicken Sie auf der Seite **Einstellungen** im linken Bereich auf **Prozessor**. Legen Sie im rechten Bereich die **Anzahl virtueller Prozessoren** auf 4 (oder mehr) fest. Klicken Sie auf **Anwenden**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Um die Mindestanforderungen zu erfüllen, müssen Sie auch einen virtuellen Datenträger mit 500 GB hinzufügen. Auf der Seite **Einstellungen** :

    1. Wählen Sie im linken Bereich die Option **SCSI-Controller**.
    2. Wählen Sie im rechten Bereich die Option **Festplatte**, und klicken Sie auf **Hinzufügen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Wählen Sie auf der Seite **Festplatte** die Option **Virtuelle Festplatte**, und klicken Sie auf **Neu**. Der **Assistent für neue virtuelle Festplatten** wird gestartet.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Klicken Sie auf der Seite **Vorbereitung** des Assistenten für neue virtuelle Festplatten auf **Weiter**.
16. Übernehmen Sie auf der Seite **Datenträgerformat auswählen** die Standardoption **VHDX** für das Format. Klicken Sie auf **Weiter**. Dieser Bildschirm wird nicht angezeigt, wenn Sie Windows Server 2008 R2 ausführen.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Legen Sie auf der Seite **Datenträgertyp auswählen** den Datenträgertyp für die virtuelle Festplatte auf **Dynamisch erweiterbar** fest (empfohlen). Sie können auch die Option **Feste Größe** für den Datenträger auswählen, aber dies ist unter Umständen mit einer langen Wartezeit verbunden. Die Verwendung der Option **Differenzierend** ist nicht zu empfehlen. Klicken Sie auf **Weiter**. In Windows Server 2012 R2 und Windows Server 2012 ist **Dynamisch erweiterbar** die Standardoption, während in Windows Server 2008 R2 die Standardeinstellung **Feste Größe** lautet.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Geben Sie auf der Seite **Namen und Speicherort angeben** einen **Namen** und einen **Speicherort** (z.B. per Durchsuchen) für den Datenträger an. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Wählen Sie auf der Seite **Datenträger konfigurieren** die Option **Neue virtuelle Festplatte ohne Inhalt erstellen**, und geben Sie als Größe **500 GB** (oder mehr) an. 500 GB sind die Mindestanforderung, aber Sie können auch einen größeren Datenträger bereitstellen. Beachten Sie hierbei, dass Sie den Datenträger nach der Bereitstellung nicht mehr erweitern oder verkleinern können. Weitere Informationen zur Größe des bereitzustellenden Datenträgers finden Sie im Abschnitt zum Festlegen der Größe im Dokument mit [Best Practices](storsimple-ova-best-practices.md). Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Überprüfen Sie auf der Seite **Zusammenfassung** die Details Ihres virtuellen Datenträgers, und klicken Sie zum Erstellen des Datenträgers auf **Fertig stellen**, wenn alles korrekt ist. Der Assistent wird geschlossen, und Ihrem Computer wird eine virtuelle Festplatte hinzugefügt.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Wechseln Sie zurück zur Seite **Einstellungen**. Klicken Sie auf **OK**, um die Seite **Einstellungen** zu schließen und zum Hyper-V-Manager-Fenster zurückzukehren.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Schritt 3: Starten des virtuelles Arrays und Abrufen der IP-Adresse
Führen Sie die folgenden Schritte aus, um Ihr virtuelles Array zu starten und eine Verbindung damit herzustellen.

#### <a name="to-start-the-virtual-array"></a>So starten Sie das virtuelle Array
1. Starten Sie das virtuelle Array.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Wenn das Gerät ausgeführt wird, wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste, und wählen Sie **Verbinden**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Unter Umständen müssen Sie 5 bis 10 Minuten warten, bis das Gerät bereit ist. In der Konsole wird eine Statusmeldung angezeigt, die den Fortschritt angibt. Wenn das Gerät bereit ist, wählen Sie **Aktion**. Drücken Sie `Ctrl + Alt + Delete`, um sich am virtuellen Array anzumelden. Der Standardbenutzer lautet *StorSimpleAdmin*, und das Standardkennwort lautet *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Aus Sicherheitsgründen gilt das Kennwort des Geräteadministrators nur für die erste Anmeldung. Sie werden aufgefordert, das Kennwort zu ändern.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Geben Sie ein Kennwort ein, das mindestens acht Zeichen lang ist. Das Kennwort muss mindestens drei der folgenden vier Anforderungen erfüllen: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. Geben Sie das Kennwort erneut ein, um es zu bestätigen. Sie werden benachrichtigt, dass das Kennwort geändert wurde.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Nachdem das Kennwort erfolgreich geändert wurde, wird das virtuelle Array unter Umständen neu gestartet. Warten Sie, bis das Gerät gestartet wurde.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Die Windows PowerShell-Konsole des Geräts wird zusammen mit der Statusanzeige angezeigt.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Die Schritte 6 bis 8 gelten nur beim Starten in anderen Umgebungen als einer DHCP-Umgebung. Falls Sie sich in einer DHCP-Umgebung befinden, sollten Sie diese Schritte überspringen und mit Schritt 9 fortfahren. Wenn Sie Ihr Gerät in einer anderen Umgebung als einer DHCP-Umgebung gestartet haben, wird der folgende Bildschirm angezeigt.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Konfigurieren Sie als Nächstes das Netzwerk.
7. Verwenden Sie den Befehl `Get-HcsIpAddress`, um die Netzwerkschnittstellen aufzulisten, die auf Ihrem virtuellen Array aktiviert sind. Wenn für das Gerät eine einzelne Netzwerkschnittstelle aktiviert ist, wird dieser Schnittstelle der Standardname `Ethernet`zugewiesen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Verwenden Sie das `Set-HcsIpAddress` -Cmdlet, um das Netzwerk zu konfigurieren. Siehe folgendes Beispiel:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Nachdem die anfängliche Einrichtung abgeschlossen und das Gerät gestartet wurde, wird der Bannertext für das Gerät angezeigt. Notieren Sie sich die IP-Adresse und die URL, die im Bannertext für die Verwaltung des Geräts angezeigt wird. Verwenden Sie diese IP-Adresse, um eine Verbindung mit der Webbenutzeroberfläche Ihres virtuellen Arrays herzustellen und um die lokale Einrichtung und die Registrierung abzuschließen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Optional) Führen Sie diesen Schritt nur aus, wenn Sie Ihr Gerät in der Government-Cloud bereitstellen. Aktivieren Sie jetzt den FIPS-Modus (Federal Information Processing Standard) auf Ihrem Gerät. Der FIPS 140-Standard definiert kryptographische Algorithmen, die zum Schutz vertraulicher Daten in Computersystemen der US-Regierung zugelassen sind.

    1. Führen Sie das folgende Cmdlet aus, um den FIPS-Modus zu aktivieren:

        `Enable-HcsFIPSMode`
    2. Starten Sie das Gerät neu, nachdem Sie den FIPS-Modus aktiviert haben, damit die kryptografischen Überprüfungen greifen.

       > [!NOTE]
       > Sie können auf Ihrem Gerät den FIPS-Modus entweder aktivieren oder deaktivieren. Ein ständiger Wechsel des Geräts vom FIPS-Modus in den Nicht-FIPS-Modus und umgekehrt wird nicht unterstützt.
       >
       >

Wenn Ihr Gerät die Mindestanforderungen für die Konfiguration nicht erfüllt, wird im Bannertext ein Fehler angezeigt (siehe unten). Sie müssen die Gerätekonfiguration ändern, damit der Computer über ausreichende Ressourcen verfügt und die Mindestanforderungen erfüllen kann. Sie können das Gerät dann neu starten und die Verbindung dafür herstellen. Die Mindestanforderungen für die Konfiguration finden Sie in Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Arrays erfüllt.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Falls bei der anfänglichen Konfiguration über die lokale Webbenutzeroberfläche andere Fehler auftreten, finden Sie weitere Informationen in den folgenden Workflows:

* Ausführen von Diagnosetests zum [Beheben von Setup-Fehlern für die Web-UI](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)
* [Generieren des Protokollpakets und Anzeigen von Protokolldateien](storsimple-ova-web-ui-admin.md#generate-a-log-package)

## <a name="next-steps"></a>Nächste Schritte
* [Einrichten des StorSimple Virtual Array als Dateiserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Einrichten des StorSimple Virtual Array als iSCSI-Server](storsimple-virtual-array-deploy3-iscsi-setup.md)
