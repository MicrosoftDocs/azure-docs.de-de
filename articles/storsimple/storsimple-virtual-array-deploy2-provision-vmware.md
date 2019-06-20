---
title: Bereitstellen eines StorSimple Virtual Array in VMware | Microsoft-Dokumentation
description: In diesem zweiten Tutorial der Reihe zur StorSimple Virtual Array-Bereitstellung geht es um das Bereitstellen eines virtuellen Geräts in VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61419160"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Bereitstellen des StorSimple Virtual Array – Bereitstellen in VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Übersicht
In diesem Tutorial wird beschrieben, wie Sie ein StorSimple Virtual Array auf einem Hostsystem mit VMware ESXi 5.0, 5.5, 6.0 oder 6.5 bereitstellen und eine Verbindung dafür herstellen. Dieser Artikel bezieht sich auf die Bereitstellung von StorSimple Virtual Arrays im Azure-Portal und in der Microsoft Azure Government-Cloud.

Sie benötigen Administratorrechte, um ein virtuelles Gerät bereitzustellen und zu verbinden. Die Bereitstellung und die anfängliche Einrichtung dauern ca. 10 Minuten.

## <a name="provisioning-prerequisites"></a>Voraussetzungen für die Bereitstellung
Für die Bereitstellung eines virtuellen Geräts auf einem Hostsystem mit VMware ESXi 5.0, 5.5, 6.0 oder 6.5 gelten die folgenden Voraussetzungen.

### <a name="for-the-storsimple-device-manager-service"></a>Voraussetzungen für den StorSimple-Geräte-Manager-Dienst
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben alle Schritte unter [Vorbereiten des Portals für StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)ausgeführt.
* Sie haben das Image des virtuellen Geräts für VMware aus dem Azure-Portal heruntergeladen. Weitere Informationen finden Sie unter **Schritt 3: Herunterladen des Image des virtuellen Arrays** in den [Anleitungen zum Vorbereiten des Portals für StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Für das virtuelle StorSimple-Gerät
Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Gerät bereitstellen:

* Sie haben Zugriff auf ein Hostsystem mit Hyper-V (2008 R2 oder höher), das zum Bereitstellen eines Geräts verwendet werden kann.
* Das Hostsystem verfügt für die Bereitstellung des virtuellen Geräts über die folgenden Ressourcen:

  * Mindestens 4 Kerne
  * Mindestens 8 GB RAM Wenn Sie planen, das virtuelle Array als Dateiserver zu konfigurieren, unterstützen 8 GB weniger als 2 Millionen Dateien. Sie benötigen 16 GB RAM, um 2–4 Millionen Dateien zu unterstützen.
  * Eine Netzwerkschnittstelle
  * Einen virtuellen Datenträger mit 500 GB für Systemdaten

### <a name="for-the-network-in-datacenter"></a>Für das Netzwerk im Rechenzentrum
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben sich die Netzwerkanforderungen zum Bereitstellen eines virtuellen StorSimple-Geräts angesehen und das Netzwerk des Rechenzentrums gemäß den Anforderungen konfiguriert. 

## <a name="step-by-step-provisioning"></a>Schritt-für-Schritt-Bereitstellung
Zum Bereitstellen und Herstellen der Verbindung mit einem virtuellen Gerät müssen Sie die folgenden Schritte ausführen:

1. Stellen Sie sicher, dass das Hostsystem über genügend Ressourcen zum Erfüllen der Mindestanforderungen des virtuellen Geräts verfügt.
2. Stellen Sie ein virtuelles Gerät in Ihrem Hypervisor bereit.
3. Starten Sie das virtuelle Gerät, und rufen Sie die IP-Adresse ab.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Geräte erfüllt
Zum Erstellen eines virtuellen Geräts benötigen Sie Folgendes:

* Zugriff auf ein Hostsystem mit VMware ESXi Server 5.0, 5.5, 6.0 oder 6.5.
* VMware vSphere-Client auf Ihrem System zum Verwalten des ESXi-Hosts

  * Mindestens 4 Kerne
  * Mindestens 8 GB RAM Wenn Sie planen, das virtuelle Array als Dateiserver zu konfigurieren, unterstützen 8 GB weniger als 2 Millionen Dateien. Sie benötigen 16 GB RAM, um 2–4 Millionen Dateien zu unterstützen.
  * Eine mit dem Netzwerk verbundene Netzwerkschnittstelle, über die Datenverkehr ins Internet weitergeleitet werden kann. Die Internetbandbreite sollte mindestens 5 MBit/s betragen, um die optimale Nutzung des Geräts zu ermöglichen.
  * Einen virtuellen Datenträger mit 500 GB

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Schritt 2: Bereitstellen eines virtuellen Geräts in VMware
Führen Sie die folgenden Schritte aus, um ein virtuelles Gerät in VMware bereitzustellen.

1. Kopieren Sie das Image mit dem virtuellen Gerät auf Ihr System. Dies ist das virtuelle Image, das Sie über das Azure-Portal heruntergeladen haben.

   1. Stellen Sie sicher, dass Sie die neueste Imagedatei heruntergeladen haben. Falls Sie das Image zuvor heruntergeladen haben, laden Sie es erneut herunter, um sicherzustellen, dass Sie das neuste Image haben. Das neueste Image hat zwei Dateien (statt einer).
   2. Notieren Sie sich den Speicherort, an den Sie das Image kopiert haben, da Sie es später noch benötigen.

2. Melden Sie sich mit dem vSphere-Client am ESXi-Server an. Sie benötigen Administratorrechte, um einen virtuellen Computer zu erstellen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Wählen Sie auf dem vSphere-Client links im Abschnitt „Inventar“ den ESXi-Server aus.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Laden Sie das VMDK auf den ESXi-Server hoch. Gehen Sie im rechten Bereich zur Registerkarte **Konfiguration** . Wählen Sie unter **Hardware** die Option **Speicher**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. Wählen Sie im rechten Bereich unter **Datenspeicher**den Datenspeicher aus, in den Sie das VMDK hochladen möchten. Der Datenspeicher muss über genügend freien Speicherplatz für das Betriebssystem und die Datenträger verfügen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klicken Sie mit der rechten Maustaste, und wählen Sie **Datenspeicher durchsuchen** aus.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Das Fenster **Datenspeicher-Browser** wird angezeigt.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Klicken Sie in der Symbolleiste auf das Symbol ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png), um einen neuen Ordner zu erstellen. Geben Sie den Ordnernamen an, und notieren Sie ihn sich. Sie verwenden diesen Ordnernamen später beim Erstellen einer virtuellen Maschine (empfohlene bewährte Methode). Klicken Sie auf **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Der neue Ordner wird im linken Bereich des **Datenspeicher-Browsers** angezeigt.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klicken Sie auf das Symbol für den Upload ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png), und wählen Sie **Datei hochladen** aus.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Rufen Sie das Verzeichnis auf, in das Sie die VMDK-Dateien heruntergeladen haben. Es enthält zwei Dateien. Wählen Sie die Datei aus, die Sie hochladen wollen.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klicken Sie auf **Öffnen**. Der Upload der VMDK-Datei in den angegebenen Datenspeicher wird gestartet. Das Hochladen der Datei kann mehrere Minuten dauern.
13. Sobald der Upload abgeschlossen ist, können Sie die Datei im Datenspeicher in dem von Ihnen erstellten Ordner sehen.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Laden Sie nun die zweite VMDK-Datei in den gleichen Datenspeicher hoch.
14. Wechseln Sie zurück zum vSphere-Clientfenster. Klicken Sie bei ausgewähltem ESXi-Server mit der rechten Maustaste, und wählen Sie **Neuer virtueller Computer**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Das Fenster **Neuen virtuellen Computer erstellen** wird angezeigt. Wählen Sie auf der Seite **Konfiguration** die Option **Benutzerdefiniert**. Klicken Sie auf **Weiter**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Geben Sie auf der Seite **Name und Speicherort** den Namen der virtuellen Maschine an. Dieser Name sollte mit dem Ordnernamen übereinstimmen, den Sie in Schritt 8 angegeben haben (empfohlene bewährte Methode).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Wählen Sie auf der Seite **Speicher** einen Datenspeicher aus, den Sie zum Bereitstellen der VM verwenden möchten.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Wählen Sie auf der Seite **Version des virtuellen Computers** die Option **Version des virtuellen Computers aus: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Wählen Sie auf der Seite **Gastbetriebssystem** unter **Gastbetriebssystem** die Option **Windows** aus. Wählen Sie unter in der Dropdownliste **Version** die Option **Microsoft Windows Server 2012 (64 Bit)** aus.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Passen Sie auf der Seite **CPUs** die Optionen **Anzahl von virtuellen Sockets** und **Anzahl von Kernen pro virtuellem Socket** so an, dass die **Gesamtzahl der Kerne** 4 (oder mehr) beträgt. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Geben Sie auf der Seite **Arbeitsspeicher** den Wert „8 GB“ (oder mehr) an. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Geben Sie auf der Seite **Netzwerk** die Anzahl der Netzwerkschnittstellen an. Als Mindestanforderung muss eine Netzwerkschnittstelle vorhanden sein.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Übernehmen Sie auf der Seite **SCSI-Controller** den standardmäßigen **LSI Logic SAS-Controller**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Wählen Sie auf der Seite **Datenträger auswählen** die Option **Vorhandenen virtuellen Datenträger verwenden**. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Klicken Sie auf der Seite **Vorhandenen Datenträger auswählen** unter **Datenträger-Dateipfad** auf **Durchsuchen**. Das Dialogfeld **Datenspeicher durchsuchen** wird geöffnet. Navigieren Sie zu dem Speicherort, an den Sie das VMDK hochgeladen haben. Sie sehen jetzt nur eine Datei im Datenspeicher, da die beiden Dateien, die Sie ursprünglich hochgeladen haben, zusammengeführt wurden. Wählen Sie die Datei aus, und klicken Sie auf **OK**. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Übernehmen Sie auf der Seite **Erweiterte Optionen** die Standardeinstellung, und klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Prüfen Sie auf der Seite **Für Fertigstellung bereit** alle Einstellungen, die der neuen virtuellen Maschine zugeordnet sind. Aktivieren Sie **Einstellungen der virtuellen Maschine vor der Fertigstellung bearbeiten**. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Suchen Sie auf der Seite **Virtual Machines-Eigenschaften** auf der Registerkarte **Hardware** nach der Gerätehardware. Wählen Sie **Neue Festplatte**. Klicken Sie auf **Hinzufügen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Sie sehen das Fenster **Hardware hinzufügen**. Wählen Sie auf der Seite **Gerätetyp** unter **Hinzuzufügenden Gerätetyp auswählen** die Option **Festplatte** aus, und klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Wählen Sie auf der Seite **Datenträger auswählen** die Option **Neuen virtuellen Datenträger erstellen**. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Ändern Sie auf der Seite **Datenträger erstellen** die **Datenträgergröße** in 500 GB (oder mehr). 500 GB sind die Mindestanforderung, aber Sie können auch einen größeren Datenträger bereitstellen. Beachten Sie hierbei, dass Sie den Datenträger nach der Bereitstellung nicht mehr erweitern oder verkleinern können. Weitere Informationen zur Größe des bereitzustellenden Datenträgers finden Sie im Abschnitt zum Festlegen der Größe im Dokument mit [Best Practices](storsimple-ova-best-practices.md). Wählen Sie unter **Datenträgerbereitstellung** die Option **Thin-Bereitstellung**. Klicken Sie auf **Weiter**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Übernehmen Sie auf der Seite **Erweiterte Optionen** die Standardeinstellung.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Prüfen Sie auf der Seite **Für Fertigstellung bereit** die Datenträgeroptionen. Klicken Sie auf **Fertig stellen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Kehren Sie auf die Seite „Eigenschaften des virtuellen Computers“ zurück. Ihrer virtuellen Maschine wird eine neue Festplatte hinzugefügt. Klicken Sie auf **Fertig stellen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Navigieren Sie zur Registerkarte **Zusammenfassung** , während Ihre virtuelle Maschine im rechten Bereich ausgewählt ist. Überprüfen Sie die Einstellungen für die virtuelle Maschine.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Ihre virtuelle Maschine wird nun bereitgestellt. Der nächste Schritt umfasst das Inbetriebnehmen der Maschine und das Abrufen der IP-Adresse.

> [!NOTE]
> Es wird empfohlen, VMware-Tools nicht auf Ihrem virtuellen Array zu installieren (wie oben beschrieben wird). Die Installation von VMware-Tools führt zu einer nicht unterstützten Konfiguration.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Schritt 3: Starten des virtuellen Geräts und Abrufen der IP-Adresse
Führen Sie die folgenden Schritte aus, um Ihr virtuelles Gerät zu starten und eine Verbindung dafür herzustellen.

#### <a name="to-start-the-virtual-device"></a>So starten Sie das virtuelle Gerät
1. Starten Sie das virtuelle Gerät. Wählen Sie im vSphere Configuration Manager links Ihr Gerät aus, und klicken Sie mit der rechten Maustaste, um das Kontextmenü anzuzeigen. Wählen Sie **Ein/Aus** und dann **Einschalten**. Die virtuelle Maschine sollte nun eingeschaltet werden. Sie können den Status im unteren Bereich **Letzte Tasks** des vSphere-Clients anzeigen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Es dauert einige Minuten, bis die Einrichtungsaufgaben abgeschlossen sind. Wenn das Gerät ausgeführt wird, navigieren Sie zur Registerkarte **Konsole** . Verwenden Sie STRG+ALT+ENTF, um sich am Gerät anzumelden. Alternativ dazu können Sie auch mit dem Cursor auf das Konsolenfenster zeigen und STRG+ALT+EINFG drücken. Der Standardbenutzer lautet *StorSimpleAdmin*, und das Standardkennwort lautet *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Aus Sicherheitsgründen gilt das Kennwort des Geräteadministrators nur für die erste Anmeldung. Sie werden aufgefordert, das Kennwort zu ändern.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Geben Sie ein Kennwort ein, das mindestens acht Zeichen lang ist. Beim Kennwort muss es sich um eine Kombination von drei der vier folgenden Elemente handeln: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. Geben Sie das Kennwort erneut ein, um es zu bestätigen. Sie werden benachrichtigt, dass das Kennwort geändert wurde.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Nachdem das Kennwort erfolgreich geändert wurde, wird das virtuelle Gerät unter Umständen neu gestartet. Warten Sie, bis der Neustart abgeschlossen ist. Die Windows PowerShell-Konsole des Geräts wird unter Umständen zusammen mit der Statusanzeige eingeblendet.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Die Schritte 6 bis 8 gelten nur beim Starten in anderen Umgebungen als einer DHCP-Umgebung. Falls Sie sich in einer DHCP-Umgebung befinden, sollten Sie diese Schritte überspringen und mit Schritt 9 fortfahren. Wenn Sie Ihr Gerät in einer anderen Umgebung als einer DHCP-Umgebung gestartet haben, wird der folgende Bildschirm angezeigt.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Konfigurieren Sie als Nächstes das Netzwerk.
7. Verwenden Sie den Befehl `Get-HcsIpAddress` , um die Netzwerkschnittstellen aufzulisten, die auf Ihrem virtuellen Gerät aktiviert sind. Wenn für das Gerät eine einzelne Netzwerkschnittstelle aktiviert ist, wird dieser Schnittstelle der Standardname `Ethernet`zugewiesen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Verwenden Sie das `Set-HcsIpAddress` -Cmdlet, um das Netzwerk zu konfigurieren. Unten ist ein Beispiel angegeben:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Nachdem die anfängliche Einrichtung abgeschlossen und das Gerät gestartet wurde, wird der Bannertext für das Gerät angezeigt. Notieren Sie sich die IP-Adresse und die URL, die im Bannertext für die Verwaltung des Geräts angezeigt wird. Sie verwenden diese IP-Adresse zum Herstellen der Verbindung mit der Web-UI Ihres virtuellen Geräts und zum Durchführen des lokalen Setups und der Registrierung.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Optional) Führen Sie diesen Schritt nur aus, wenn Sie Ihr Gerät in der Government-Cloud bereitstellen. Aktivieren Sie jetzt den FIPS-Modus (Federal Information Processing Standard) auf Ihrem Gerät. Der FIPS 140-Standard definiert kryptographische Algorithmen, die zum Schutz vertraulicher Daten in Computersystemen der US-Regierung zugelassen sind.

    1. Führen Sie das folgende Cmdlet aus, um den FIPS-Modus zu aktivieren:

        `Enable-HcsFIPSMode`
    2. Starten Sie das Gerät neu, nachdem Sie den FIPS-Modus aktiviert haben, damit die kryptografischen Überprüfungen greifen.

       > [!NOTE]
       > Sie können auf Ihrem Gerät den FIPS-Modus entweder aktivieren oder deaktivieren. Ein ständiger Wechsel des Geräts vom FIPS-Modus in den Nicht-FIPS-Modus und umgekehrt wird nicht unterstützt.
       >
       >

Wenn Ihr Gerät die Mindestanforderungen für die Konfiguration nicht erfüllt, wird im Bannertext ein Fehler angezeigt (siehe unten). Sie müssen die Gerätekonfiguration ändern, damit sie über ausreichende Ressourcen zum Erfüllen der Mindestanforderungen verfügt. Sie können das Gerät dann neu starten und die Verbindung dafür herstellen. Die Mindestanforderungen für die Konfiguration finden Sie in [Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Geräte erfüllt](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Falls bei der anfänglichen Konfiguration über die lokale Webbenutzeroberfläche andere Fehler auftreten, finden Sie weitere Informationen in den folgenden Workflows:

* Ausführen von Diagnosetests zum [Beheben von Setup-Fehlern für die Web-UI](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)
* [Generieren des Protokollpakets und Anzeigen von Protokolldateien](storsimple-ova-web-ui-admin.md#generate-a-log-package)

## <a name="next-steps"></a>Nächste Schritte
* [Einrichten des StorSimple Virtual Array als Dateiserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Einrichten des StorSimple Virtual Array als iSCSI-Server](storsimple-virtual-array-deploy3-iscsi-setup.md)
