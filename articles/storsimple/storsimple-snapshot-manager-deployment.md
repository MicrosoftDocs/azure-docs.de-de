---
title: Bereitstellen des StorSimple Snapshot Managers | Microsoft Docs
description: Erfahren Sie, wie Sie StorSimple Snapshot Manager herunterladen und installieren. Mit diesem MMC-Snap-In lassen sich die StorSimple-Features für Datensicherheit und Sicherungen verwalten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003870"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Bereitstellen des StorSimple Snapshot Manager-MMC-Snap-Ins

## <a name="overview"></a>Übersicht
Der StorSimple Snapshot Manager ist ein MMC-Snap-In (Microsoft Management Console), das den Datenschutz und die Sicherungsverwaltung in einer Microsoft Azure StorSimple-Umgebung vereinfacht. Mit dem StorSimple Snapshot Manager können Sie lokalen und cloudbasierten Microsoft Azure StorSimple-Speicher so verwalten, als handele es sich um ein vollständig integriertes Speichersystem, wodurch die Sicherungs- und Wiederherstellungsprozesse vereinfacht und die Kosten reduziert werden. 

Dieses Tutorial beschreibt die Konfigurationsanforderungen sowie Verfahren zum Installieren, Entfernen und Aktualisieren des StorSimple Snapshot Managers.

> [!NOTE]
> * StorSimple Snapshot Manager eignet sich nicht zur Verwaltung von Microsoft Azure StorSimple Virtual Arrays (auch bekannt als lokale virtuelle StorSimple-Geräte).
> * Wenn Sie StorSimple Update 2 auf Ihrem StorSimple-Gerät installieren möchten, laden Sie unbedingt die aktuelle Version von StorSimple Snapshot Manager herunter, und installieren Sie sie **vor der Installation von StorSimple Update 2**. Die neueste Version von StorSimple Snapshot Manager ist abwärtskompatibel und kann mit allen veröffentlichten Versionen von Microsoft Azure StorSimple verwendet werden. Wenn Sie die vorherige Version von StorSimple Snapshot Manager verwenden, müssen Sie sie aktualisieren (die vorherige Version muss vor der Installation der neuen Version nicht deinstalliert werden).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager-Installation
Der StorSimple Snapshot Manager kann auf Computern mit dem Betriebssystem Windows Server 2008 R2 SP1, Windows Server 2012 oder Windows Server 2012 R2 installiert werden. Auf Servern unter Windows 2008 R2 müssen Sie außerdem Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren.

Stellen Sie vor der Installation oder Aktualisierung des StorSimple Snapshot Manager-Snap-Ins für die Microsoft Management Console (MMC) sicher, dass das Microsoft Azure StorSimple-Gerät und der -Hostserver ordnungsgemäß konfiguriert sind.

## <a name="configure-prerequisites"></a>Konfigurieren der Voraussetzungen
Die folgenden Schritte bieten einen allgemeinen Überblick über die Konfigurationsaufgaben, die Sie ausführen müssen, bevor Sie den StorSimple Snapshot Manager installieren. Vollständige Microsoft Azure StorSimple-Konfigurations- und Setupinformationen, einschließlich Systemanforderungen und detaillierten Anweisungen, finden Sie unter [Bereitstellen des lokalen StorSimple-Geräts](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Bevor Sie loslegen, gehen Sie die [Konfigurationsprüfliste für die Bereitstellung](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) und die [Bereitstellungsvoraussetzungen](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) in [Bereitstellen lokaler StorSimple-Geräte](storsimple-8000-deployment-walkthrough-u2.md) durch.
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Vor der Installation des StorSimple Snapshot Managers
1. Entpacken, Bereitstellen und Verbinden des Microsoft Azure StorSimple-Geräts, wie in [Installieren des StorSimple-8100-Geräts](storsimple-8100-hardware-installation.md) oder in [Installieren des StorSimple-8600-Geräts](storsimple-8600-hardware-installation.md) beschrieben.
2. Stellen Sie sicher, dass auf Ihrem Computer eines der folgenden Betriebssysteme ausgeführt wird:
   
   * Windows Server 2008 R2 (auf Servern unter Windows 2008 R2 müssen Sie außerdem Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Für ein virtuelles StorSimple-Gerät muss der Host ein virtueller Microsoft Azure-Computer sein.
3. Stellen Sie sicher, dass Sie die Microsoft Azure StorSimple-Konfigurationsanforderungen erfüllen. Weitere Informationen finden Sie unter [Voraussetzungen für die Bereitstellung](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Verbinden Sie das Gerät mit dem Host, und führen Sie die Erstkonfiguration durch. Weitere Informationen finden Sie unter [Bereitstellungsschritte](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Erstellen Sie Volumes auf dem Gerät, weisen Sie sie dem Host zu, und stellen Sie sicher, dass der Host die Volumes bereitstellen und verwenden kann. Der StorSimple Snapshot Manager unterstützt die folgenden Typen von Volumes:
   
   * Basisvolumes
   * Einfache Volumes
   * Dynamische Volumes
   * Gespiegelte dynamische Volumes (RAID 1)
   * Freigegebene Clustervolumes
     
     Informationen zum Erstellen von Volumes auf dem StorSimple-Gerät oder dem virtuellen StorSimple-Gerät finden Sie unter [Schritt 6: Erstellen eines Volumes](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) in [Bereitstellen lokaler StorSimple-Geräte](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installieren eines neuen StorSimple Snapshot Managers
Stellen Sie vor der Installation von StorSimple Snapshot Manager sicher, dass die auf dem StorSimple-Gerät oder dem virtuellen StorSimple-Gerät erstellten Volumes wie in [Konfigurieren der Voraussetzungen](#configure-prerequisites)beschrieben bereitgestellt, initialisiert und formatiert sind.

> [!IMPORTANT]
> * Für ein virtuelles StorSimple-Gerät muss der Host ein virtueller Microsoft Azure-Computer sein. 
> * Der Host muss unter Windows 2008 R2, Windows Server 2012 oder Windows Server 2012 R2 ausgeführt werden. Wenn Ihr Server unter Windows Server 2008 R2 ausgeführt wird, müssen Sie auch Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren.
> * Sie müssen eine iSCSI-Verbindung vom Host zum StorSimple-Gerät konfigurieren, bevor Sie das Gerät mit dem StorSimple Snapshot Manager verbinden können.

Gehen Sie folgendermaßen vor, um eine Neuinstallation des StorSimple Snapshot Managers durchzuführen. Wenn Sie ein Upgrade installieren, wechseln Sie zu [Upgrade oder Neuinstallation des StorSimple Snapshot Managers](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Schritt 1: Installieren des StorSimple Snapshot Managers 
* Schritt 2: Verbinden eines Geräts mit dem StorSimple Snapshot Manager 
* Schritt 3: Überprüfen der Verbindung mit dem Gerät 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Schritt 1: Installieren des StorSimple Snapshot Managers
Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager zu installieren.

#### <a name="to-install-storsimple-snapshot-manager"></a>So installieren Sie den StorSimple Snapshot Manager
1. Laden Sie die StorSimple Snapshot Manager-Software herunter (wechseln Sie zu [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) im Microsoft Download Center), und speichern Sie sie lokal auf dem Host.
2. Klicken Sie im Datei-Explorer mit der rechten Maustaste auf den komprimierten Ordner, und klicken Sie dann auf **Alle extrahieren**.
3. Geben Sie im Fenster **Komprimierte (gezippte) Ordner extrahieren** im Feld **Ziel auswählen und Dateien extrahieren** den Pfad ein, wo die Datei extrahiert werden soll, oder navigieren Sie dort hin.
   
    > [!IMPORTANT]
    > Sie müssen auf dem Laufwerk C: StorSimple Snapshot Manager installieren.
    
4. Aktivieren Sie das Kontrollkästchen **Dateien nach Extrahierung anzeigen**, und klicken Sie dann auf **Extrahieren**.
   
    ![Dateien extrahieren (Dialogfeld)](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Wenn die Extrahierung beendet ist, wird der Zielordner geöffnet. Doppelklicken Sie auf das Anwendungssetupsymbol, das im Zielordner angezeigt wird.
6. Wenn die Meldung **Setup wurde erfolgreich abgeschlossen** erscheint, klicken Sie auf **Schließen**. Das Symbol für den StorSimple Snapshot Manager sollte auf dem Desktop angezeigt werden.
   
    ![Desktopsymbol](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Schritt 2: Verbinden eines Geräts mit dem StorSimple Snapshot Manager
Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager mit einem StorSimple-Gerät zu verbinden.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>So verbinden Sie den StorSimple Snapshot Manager mit einem Gerät
1. Klicken Sie auf das StorSimple Snapshot Manager-Symbol auf Ihrem Desktop. Das Fenster des StorSimple Snapshot Managers wird angezeigt. Das Fenster enthält die Bereiche **Bereich**, **Ergebnisse** und **Aktionen**. 
   
    ![Benutzeroberfläche des StorSimple Snapshot Managers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Der Fensterbereich **Bereich** (der linke Bereich) enthält eine Liste der Knoten, die in einer Baumstruktur angeordnet sind. Sie können einige Knoten zum Auswählen einer Ansicht oder bestimmter Daten in Bezug auf diesen Knoten erweitern. Klicken Sie zum Erweitern oder Reduzieren eines Knotens auf das Pfeilsymbol. Klicken Sie mit der rechten Maustaste auf ein Element im Fensterbereich **Bereich**, um eine Liste der verfügbaren Aktionen für das Element anzuzeigen.
   * Der Bereich **Ergebnisse** (der mittlere Bereich) enthält ausführliche Statusinformationen über den Knoten, die Ansicht oder die Daten, die Sie im Fensterbereich **Bereich** ausgewählt haben.
   * Im Bereich **Aktionen** werden die Vorgänge aufgelistet, die Sie für den Knoten, die Ansicht oder die Daten ausführen können, die Sie im Fensterbereich **Bereich** ausgewählt haben.
     
     Eine vollständige Beschreibung der StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den Knoten **Geräte**, und klicken Sie dann auf **Gerät konfigurieren**. Das Dialogfeld **Gerät konfigurieren** wird angezeigt.
   
    ![Gerät konfigurieren](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Wählen Sie im Listenfeld **Gerät** die IP-Adresse des Microsoft Azure StorSimple-Geräts oder des virtuellen Geräts aus. Geben Sie im Textfeld **Kennwort** das StorSimple Snapshot Manager-Kennwort ein, das Sie im Azure-Portal für das Gerät erstellt haben. Klicken Sie auf **OK**.
4. Der StorSimple Snapshot Manager sucht nach dem angegebenen Gerät. Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu. Sie können [die Verbindung mit dem Gerät überprüfen](#to-verify-the-connection) , um zu bestätigen, dass die Verbindung erfolgreich hinzugefügt wurde.
   
    Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. Klicken Sie auf **OK**, um die Fehlermeldung zu schließen, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld **Gerät konfigurieren** zu schließen.
5. Beim Herstellen einer Verbindung mit einem Gerät importiert der StorSimple Snapshot Manager jede Volumegruppe, die für das Gerät konfiguriert wurde, vorausgesetzt, dass die Volumegruppe über zugeordnete Sicherungen verfügt. Volumegruppen ohne zugehörige Sicherungen werden nicht importiert. Darüber hinaus werden Sicherungsrichtlinien, die für eine Volumegruppe erstellt wurden, nicht importiert. Um die importierten Gruppen anzuzeigen, klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den obersten Knoten **Volumegruppen**, und klicken Sie dann auf **Importierte Gruppen umschalten**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Schritt 3: Überprüfen der Verbindung mit dem Gerät
Gehen Sie folgendermaßen vor, um sicherzustellen, dass der StorSimple Snapshot Manager mit dem StorSimple-Gerät verbunden ist.

#### <a name="to-verify-the-connection"></a>So überprüfen Sie die Verbindung
1. Klicken Sie im Fensterbereich **Bereich** auf den Knoten **Geräte**.
   
    ![Gerätestatus des StorSimple Snapshot Managers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Überprüfen Sie den **Ergebnisse** -Bereich: 
   
   * Wenn ein grüner Indikator auf dem Gerätesymbol und der Wert **Verfügbar** in der Spalte **Status** angezeigt wird, ist das Gerät verbunden. 
   * Wenn ein roter Indikator auf dem Gerätesymbol und „Nicht verfügbar“ in der Spalte **Status** angezeigt wird, ist das Gerät nicht verbunden. 
   * Wenn der Wert **Wird aktualisiert** in der Spalte **Status** angezeigt wird, ruft der StorSimple Snapshot Manager Volumegruppen und zugehörige Sicherungen für ein verbundenes Gerät ab.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Upgrade oder Neuinstallation des StorSimple Snapshot Managers
Sie sollten den StorSimple Snapshot Manager vollständig deinstallieren, bevor Sie die Software aktualisieren oder neu installieren. 

Sichern Sie vor der Neuinstallation des StorSimple Snapshot Managers die vorhandene StorSimple Snapshot Manager-Datenbank auf dem Hostcomputer. Dies speichert die Sicherungsrichtlinien und Konfigurationsinformationen, sodass Sie diese Daten problemlos aus einer Sicherung wiederherstellen können.

Gehen Sie folgendermaßen vor, wenn Sie den StorSimple Snapshot Manager aktualisieren oder erneut installieren:

* Schritt 1: Deinstallieren von StorSimple Snapshot Manager 
* Schritt 2: Sichern der StorSimple Snapshot Manager-Datenbank 
* Schritt 3: Erneutes Installieren von StorSimple Snapshot Manager und Wiederherstellen der Datenbank 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Schritt 1: Deinstallieren von StorSimple Snapshot Manager
Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager zu deinstallieren.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>So deinstallieren Sie StorSimple Snapshot Manager
1. Öffnen Sie auf dem Hostcomputer die **Systemsteuerung**, klicken Sie auf **Programme**, und klicken Sie dann auf **Programme und Funktionen**.
2. Klicken Sie im linken Bereich auf **Deinstallieren oder Ändern eines Programms**.
3. Klicken Sie mit der rechten Maustaste auf **StorSimple Snapshot Manager** und dann auf **Deinstallieren**.
4. Dadurch wird das Setup-Programm für den StorSimple Snapshot Manager gestartet. Klicken Sie auf **Setup ändern** und dann auf **Deinstallieren**.
   
   > [!NOTE]
   > Wenn im Hintergrund MMC-Prozesse laufen, z. B. der StorSimple Snapshot Manager oder die Datenträgerverwaltung, dann schlägt die Deinstallation fehl, und Sie erhalten eine Nachricht, dass vor der Deinstallation des Programms alle Instanzen von MMC geschlossen werden müssen. Wählen Sie **Anwendungen automatisch schließen und einen Neustart nach Abschluss der Installation versuchen** aus, und klicken Sie dann auf **OK**.
   > 
   > 
5. Nach Abschluss der Deinstallation erscheint die Meldung **Setup wurde erfolgreich abgeschlossen** . Klicken Sie auf **Schließen**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Schritt 2: Sichern der StorSimple Snapshot Manager-Datenbank
Führen Sie die folgenden Schritte aus, um eine Kopie der StorSimple Snapshot Manager-Datenbank zu erstellen und zu speichern.

#### <a name="to-back-up-the-database"></a>So sichern Sie die Datenbank
1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:
   
   1. Starten Sie den Server-Manager.
   2. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   3. Wählen Sie auf der Seite **Dienste** den Eintrag **Microsoft StorSimple-Verwaltungsdienst** aus.
   4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst beenden**.
      
        ![Beenden des StorSimple-Geräte-Manager-Diensts](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Navigieren Sie zu „C:\ProgramData\Microsoft\StorSimple\BACatalog“. 
   
   > [!NOTE]
   > „ProgramData“ ist ein versteckter Ordner.
  
3. Suchen Sie die XML-Katalogdatei, kopieren Sie die Datei, und speichern Sie die Kopie an einem sicheren Ort oder in der Cloud.
   
    ![StorSimple-Sicherungskatalogdatei](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu: 
   
   1. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   2. Wählen Sie auf der Seite **Dienste** den **Microsoft StorSimple-Verwaltungsdienst** aus.
   3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst neu starten**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Schritt 3: Erneutes Installieren von StorSimple Snapshot Manager und Wiederherstellen der Datenbank
Um den StorSimple Snapshot Manager erneut zu installieren, führen Sie die Schritte in [Installieren eines neuen StorSimple Snapshot Managers](#install-a-new-storsimple-snapshot-manager)aus. Verwenden Sie dann das folgende Verfahren zum Wiederherstellen der StorSimple Snapshot Manager-Datenbank.

#### <a name="to-restore-the-database"></a>So stellen Sie die Datenbank wieder her
1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:
   
   1. Starten Sie den Server-Manager.
   2. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   3. Wählen Sie auf der Seite **Dienste** den Eintrag **Microsoft StorSimple-Verwaltungsdienst** aus.
   4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst beenden**.
2. Navigieren Sie zu „C:\ProgramData\Microsoft\StorSimple\BACatalog“.
   
   > [!NOTE]
   > „ProgramData“ ist ein versteckter Ordner.
   > 
   > 
3. Löschen Sie die XML-Katalogdatei, und ersetzen Sie sie durch die Version, die Sie zuvor gespeichert haben.
4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu: 
   
   1. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   2. Wählen Sie auf der Seite **Dienste** den Eintrag **Microsoft StorSimple-Verwaltungsdienst** aus.
   3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst neu starten**.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum StorSimple Snapshot Manager finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Weitere Informationen zur StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).
* Weitere Informationen zum Verwenden von StorSimple Snapshot Manager finden Sie unter [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).

