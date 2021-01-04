---
title: StorSimple Snapshot Manager und Volumes | Microsoft Docs
description: Beschreibt, wie das MMC-Snap-In StorSimple Snapshot Manager zum Anzeigen und Verwalten von Volumes sowie zum Konfigurieren von Sicherungen verwendet wird.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022937"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Verwenden des StorSimple Snapshot Managers zum Anzeigen und Verwalten von Volumes
## <a name="overview"></a>Übersicht
Mit dem Knoten **Volumes** (im Fensterbereich **Bereich**) im StorSimple Snapshot Manager können Sie Volumes auswählen und Informationen zu diesen anzeigen. Die Volumes werden als Laufwerke dargestellt, die jeweils den vom Host bereitgestellten Volumes entsprechen. Der Knoten **Volumes** zeigt die lokalen Volumes und Volumetypen an, die von StorSimple unterstützt werden. Dazu gehören auch Volumes, die mithilfe von iSCSI und einem Gerät ermittelt wurden. 

Weitere Informationen zu unterstützten Volumes finden Sie unter [Unterstützung für mehrere Volumetypen](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Volumeliste im Ergebnisbereich](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Sie können über den Knoten **Volumes** auch Volumes erneut einlesen oder löschen, nachdem StorSimple Snapshot Manager sie erkannt hat. 

In diesem Lernprogramm wird erläutert, wie Sie Volumes bereitstellen, initialisieren und formatieren und dann StorSimple Snapshot Manager für Folgendes verwenden:

* Anzeigen von Informationen zu Volumes 
* Löschen von Volumes
* Erneutes Einlesen von Volumes 
* Konfigurieren und Sichern eines Basisvolumes
* Konfigurieren und Sichern eines dynamisch gespiegelten Volumes

> [!NOTE]
> Alle über den Knoten **Volume** verfügbaren Aktionen stehen auch im Bereich **Aktionen** zur Verfügung.
> 
> 

## <a name="mount-volumes"></a>Bereitstellen von Volumes
Gehen Sie folgendermaßen vor, um StorSimple-Volumes bereitzustellen, zu initialisieren und zu formatieren. Bei diesem Verfahren wird die Datenträgerverwaltung verwendet. Dies ist ein Systemprogramm zum Verwalten von Festplatten und den entsprechenden Volumes oder Partitionen. Weitere Informationen zur Datenträgerverwaltung finden Sie unter [Disk Management](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) (in englischer Sprache) auf der Microsoft TechNet-Website.

#### <a name="to-mount-volumes"></a>So stellen Sie Volumes bereit
1. Starten Sie den Microsoft iSCSI-Initiator auf dem Hostcomputer.
2. Geben Sie die IP-Adresse einer der Schnittstellen als Zielportal oder IP-Adresse zur Ermittlung an, und stellen Sie eine Verbindung mit dem Gerät her. Nachdem das Gerät verbunden ist, kann vom Windows-System auf die Volumes zugegriffen werden. Weitere Informationen zum Verwenden des Microsoft iSCSI-Initiators finden Sie im Abschnitt zum Herstellen einer Verbindung mit einem iSCSI-Zielgerät unter [Installieren und Konfigurieren des Microsoft iSCSI-Initiators][1].
3. Starten Sie die Datenträgerverwaltung mithilfe einer der folgenden Vorgehensweisen:
   
   * Geben Sie im Feld **Ausführen** "Diskmgmt.msc" ein.
   * Starten Sie den Server-Manager, erweitern Sie den Knoten **Speicher**, und wählen Sie dann **Datenträgerverwaltung** aus.
   * Starten Sie **Verwaltung**, erweitern Sie den Knoten **Computerverwaltung**, und wählen Sie dann **Datenträgerverwaltung** aus. 
     
     > [!NOTE]
     > Sie benötigen zum Ausführen der Datenträgerverwaltung Administratorrechte.
     > 
     > 
4. Schalten Sie die Volumes online:
   
   1. Klicken Sie in der Datenträgerverwaltung mit der rechten Maustaste auf ein Volume, für das **Offline** angezeigt wird.
   2. Klicken Sie auf **Datenträger reaktivieren**. Der Datenträger sollte nach dem erneuten Aktivieren als **Online** angezeigt werden.
5. Initialisieren Sie die Volumes:
   
   1. Klicken Sie mit der rechten Maustaste auf die ermittelten Volumes.
   2. Wählen Sie im Menü die Option **Datenträger initialisieren** aus.
   3. Wählen Sie im Dialogfeld **Datenträgerinitialisierung** die Datenträger aus, die Sie initialisieren möchten, und klicken Sie dann auf **OK**.
6. Formatieren Sie einfache Volumes:
   
   1. Klicken Sie mit der rechten Maustaste auf ein zu formatierendes Volume.
   2. Wählen Sie im Menü die Option **Neues einfaches Volume** aus.
   3. Verwenden Sie den Assistenten zum Erstellen neuer einfacher Volumes, um das Volume zu formatieren:
      
      * Geben Sie die Größe des Volumes an.
      * Weisen Sie einen Laufwerkbuchstaben zu.
      * Wählen Sie das NTFS-Dateisystem aus.
      * Geben Sie 64 KB als Zuordnungseinheitsgröße an.
      * Führen Sie eine Schnellformatierung durch.
7. Formatieren Sie Volumes mit mehreren Partitionen. Anweisungen hierzu finden Sie im Abschnitt zu Partitionen und Volumes unter [Implementing Disk Management](/previous-versions/tn-archive/dd163556(v=technet.10))(in englischer Sprache).

## <a name="view-information-about-your-volumes"></a>Anzeigen von Informationen zu den Volumes
Gehen Sie folgendermaßen vor, um Informationen zu lokalen und Azure StorSimple-Volumes anzuzeigen.

#### <a name="to-view-volume-information"></a>So zeigen Sie Volumeinformationen an
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 
2. Klicken Sie im Fensterbereich **Bereich** auf den Knoten **Volumes**. Eine Liste von lokalen und bereitgestellten Volumes, einschließlich aller Azure StorSimple-Volumes, wird im **Ergebnisbereich** angezeigt. Die Spalten im **Ergebnisbereich** sind konfigurierbar. (Klicken Sie mit der rechten Maustaste auf den Knoten **Volumes**, und wählen Sie **Ansicht** und dann **Spalten hinzufügen/entfernen** aus.)
   
    ![Konfigurieren der Spalten](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Ergebnisspalte | BESCHREIBUNG |
   |:--- |:--- |
   |  Name |Die Spalte **Name** enthält den Laufwerkbuchstaben, der den jeweiligen ermittelten Volumes zugeordnet wurde. |
   |  Sicherungsmedium |Die Spalte **Gerät** enthält die IP-Adresse des mit dem Hostcomputer verbundenen Geräts. |
   |  Device Volume Name |Die Spalte **Device Volume Name** enthält den Namen des Gerätevolumes, zu dem das ausgewählte Volume gehört. Dies ist der im Azure-Portal für das Volume festgelegte Volumename. |
   |  Access Paths |Die Spalte **Access Paths** zeigt den Zugriffspfad zum Volume an. Dies ist der Laufwerkbuchstabe oder der Bereitstellungspunkt, über den auf dem Hostcomputer auf das Volume zugegriffen werden kann. |

## <a name="delete-a-volume"></a>Löschen von Volumes
Wenden Sie das folgende Verfahren zum Löschen eines Volumes aus dem StorSimple Snapshot Manager an.

> [!NOTE]
> Sie können kein Volume löschen, wenn dieses Teil einer Volumegruppe ist. (Die Löschoption ist für Volumes, die Mitglieder einer Volumegruppe sind, nicht verfügbar.) Löschen Sie die gesamte Volumegruppe, um das Volume zu löschen.

#### <a name="to-delete-a-volume"></a>So löschen Sie ein Volume
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** auf den Knoten **Volumes**. 
3. Klicken Sie im **Ergebnisbereich** mit der rechten Maustaste auf das zu löschende Volume.
4. Klicken Sie im Menü auf **Löschen**. 
   
    ![Löschen von Volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Das Dialogfeld **Volume löschen** wird angezeigt. Geben Sie in das Textfeld den Text **Bestätigen** ein, und klicken Sie dann auf **OK**.
6. Standardmäßig sichert der StorSimple Snapshot Manager ein Volume vor dem Löschen. Diese Vorsichtsmaßnahme schützt im Fall einer unbeabsichtigten Löschung vor Datenverlust. In StorSimple Snapshot Manager wird eine Statusmeldung für die **automatische Momentaufnahme** angezeigt, während das Volume gesichert wird. 
   
    ![Nachricht zur automatischen Momentaufnahme](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Erneutes Einlesen von Volumes
Gehen Sie folgendermaßen vor, um die mit dem StorSimple Snapshot Manager verbundenen Volumes erneut einzulesen.

#### <a name="to-rescan-the-volumes"></a>So lesen Sie die Volumes neu ein
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf **Volumes**, und klicken Sie dann auf **Volumes erneut überprüfen**.
   
    ![Erneutes Einlesen von Volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Mit diesem Verfahren wird die Volumeliste mit StorSimple Snapshot Manager synchronisiert. Alle Änderungen wie z. B. neue oder gelöschte Volumes werden in den Ergebnissen berücksichtigt.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurieren und Sichern eines Basisvolumes
Wenden Sie das folgende Verfahren an, um eine Sicherung eines Basisvolumes zu konfigurieren und dann eine sofortige Sicherung zu starten oder eine Richtlinie für geplante Sicherungen zu erstellen.

### <a name="prerequisites"></a>Voraussetzungen
Vorbereitungen

* Stellen Sie sicher, dass das StorSimple-Gerät und der Hostcomputer ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Bereitstellen des lokalen StorSimple-Geräts](./storsimple-8000-deployment-walkthrough-u2.md).
* Installieren und konfigurieren Sie den StorSimple Snapshot Manager. Weitere Informationen finden Sie unter [Bereitstellen des StorSimple Snapshot Managers](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>So konfigurieren Sie die Sicherung eines Basisvolumes
1. Erstellen Sie ein Basisvolume auf dem StorSimple-Gerät.
2. Führen Sie die Bereitstellung, Initialisierung und Formatierung des Volumes wie unter [Bereitstellen von Volumes](#mount-volumes)beschrieben durch. 
3. Klicken Sie auf das StorSimple Snapshot Manager-Symbol auf Ihrem Desktop. Das Fenster des StorSimple Snapshot Managers wird angezeigt. 
4. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den Knoten **Volumes**, und wählen Sie dann **Volumes erneut überprüfen** aus. Wenn die Überprüfung abgeschlossen ist, wird im **Ergebnisbereich** eine Liste der Volumes angezeigt. 
5. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf das Volume, und wählen Sie dann **Volumegruppe erstellen** aus. 
   
    ![Volumegruppe erstellen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Geben Sie im Dialogfeld **Volumegruppe erstellen** einen Namen für die Volumegruppe ein, weisen Sie Volumes zu, und klicken Sie dann auf **OK**.
7. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Volumegruppen**. Die neue Volumegruppe sollte unter dem Knoten **Volumegruppen** angezeigt werden. 
8. Klicken Sie mit der rechten Maustaste auf den Namen der Volumegruppe.
   
   * Um einen interaktiven (bedarfsgesteuerten) Sicherungsauftrag zu starten, klicken Sie auf **Sicherung anlegen**. 
   * Um eine automatische Sicherung zu planen, klicken Sie auf **Sicherungsrichtlinie erstellen**. Wählen Sie auf der Seite **Allgemein** eine Volumegruppe in der Liste aus. Geben Sie auf der Seite **Zeitplan** Details zum Zeitplan ein. Wenn Sie fertig sind, klicken Sie auf **OK**. 
9. Um den Start des Sicherungsauftrags zu überprüfen, erweitern Sie im Fensterbereich **Bereich** den Knoten **Aufträge**, und klicken Sie dann auf den Knoten **Wird ausgeführt**. Die Liste der zurzeit ausgeführten Aufträge wird im **Ergebnisbereich** angezeigt. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurieren und Sichern eines dynamisch gespiegelten Volumes
Führen Sie die folgenden Schritte aus, um die Sicherung eines dynamisch gespiegelten Volumes zu konfigurieren:

* Schritt 1: Erstellen eines dynamisch gespiegelten Volumes mithilfe der Datenträgerverwaltung 
* Schritt 2: Konfigurieren der Sicherung mit dem StorSimple Snapshot Manager

### <a name="prerequisites"></a>Voraussetzungen
Vorbereitungen

* Stellen Sie sicher, dass das StorSimple-Gerät und der Hostcomputer ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Bereitstellen des lokalen StorSimple-Geräts](storsimple-8000-deployment-walkthrough-u2.md).
* Installieren und konfigurieren Sie den StorSimple Snapshot Manager. Weitere Informationen finden Sie unter [Bereitstellen des StorSimple Snapshot Managers](storsimple-snapshot-manager-deployment.md).
* Konfigurieren Sie zwei Volumes auf dem StorSimple-Gerät. (In den Beispielen lauten die verfügbaren Volumes **Disk 1** und **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Schritt 1: Erstellen eines dynamisch gespiegelten Volumes mithilfe der Datenträgerverwaltung
Die Datenträgerverwaltung ist ein Systemprogramm zum Verwalten von Festplatten und den darauf enthaltenen Volumes oder Partitionen. Weitere Informationen zur Datenträgerverwaltung finden Sie unter [Disk Management](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) (in englischer Sprache) auf der Microsoft TechNet-Website.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>So erstellen Sie ein dynamisch gespiegeltes Volume
1. Starten Sie die Datenträgerverwaltung mithilfe einer der folgenden Vorgehensweisen: 
   
   * Öffnen Sie das Feld **Ausführen**, geben Sie **Diskmgmt.msc** ein, und drücken Sie die EINGABETASTE.
   * Starten Sie den Server-Manager, erweitern Sie den Knoten **Speicher**, und wählen Sie dann **Datenträgerverwaltung** aus. 
   * Starten Sie **Verwaltung**, erweitern Sie den Knoten **Computerverwaltung**, und wählen Sie dann **Datenträgerverwaltung** aus. 
2. Vergewissern Sie sich, dass auf dem StorSimple-Gerät zwei Volumes verfügbar sind. (Im Beispiel lauten die verfügbaren Volumes **Disk 1** und **Disk 2**.) 
3. Klicken Sie im Fenster der Datenträgerverwaltung in der rechten Spalte des unteren Bereichs mit der rechten Maustaste auf **Disk 1**, und wählen Sie **Neues gespiegeltes Volume** aus. 
   
    ![Neues gespiegeltes Volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Klicken Sie auf der Seite **Neues gespiegeltes Volum** e des Assistenten auf **Weiter**.
5. Wählen Sie auf der Seite **Datenträger auswählen** im Bereich **Ausgewählt** den Eintrag **Disk 2** aus, und klicken Sie auf **Hinzufügen** und dann auf **Weiter**. 
6. Übernehmen Sie auf der Seite **Laufwerkbuchstaben oder -pfad zuordnen** die Standardeinstellungen, und klicken Sie dann auf **Weiter**. 
7. Wählen Sie auf der Seite **Volume formatieren** im Feld **Größe der Zuordnungseinheit** den Wert **64K** aus. Aktivieren Sie das Kontrollkästchen **Schnellformatierung durchführen**, und klicken Sie dann auf **Weiter**. 
8. Überprüfen Sie auf der Seite **Fertigstellen des Assistenten** die Einstellungen, und klicken Sie dann auf **Fertig stellen**. 
9. Es wird eine Meldung angezeigt, die angibt, dass der Basisdatenträger in einen dynamischen Datenträger konvertiert wird. Klicken Sie auf **Ja**.
   
    ![Nachricht zur Konvertierung in einen dynamischen Datenträger](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Überprüfen Sie in der Datenträgerverwaltung, ob Disk 1 und Disk 2 als dynamisch gespiegelte Volumes angezeigt werden. (**Dynamisch** sollte in der Statusspalte angezeigt werden, und die Farbe des Kapazitätsbalkens sollte sich in Rot ändern, um ein gespiegeltes Volume anzuzeigen.) 
    
    ![Datenträgerverwaltung – dynamisch gespiegelte Datenträger](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Schritt 2: Konfigurieren der Sicherung mit dem StorSimple Snapshot Manager
Verwenden Sie das folgende Verfahren zum Konfigurieren eines dynamisch gespiegelten Volumes, und starten Sie dann eine sofortige Sicherung, oder erstellen Sie eine Richtlinie für geplante Sicherungen.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>So konfigurieren Sie die Sicherung eines dynamisch gespiegelten Volumes
1. Klicken Sie auf das StorSimple Snapshot Manager-Symbol auf Ihrem Desktop. Das Fenster des StorSimple Snapshot Managers wird angezeigt. 
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den Knoten **Volumes**, und wählen Sie **Volumes erneut überprüfen** aus. Wenn die Überprüfung abgeschlossen ist, wird im **Ergebnisbereich** eine Liste der Volumes angezeigt. Das dynamisch gespiegelte Volume wird als ein einzelnes Volume aufgeführt. 
3. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf das dynamisch gespiegelte Volume, und klicken Sie dann auf **Volumegruppe erstellen**. 
4. Geben Sie im Dialogfeld **Volumegruppe erstellen** einen Namen für die Volumegruppe ein, weisen Sie der Gruppe das dynamisch gespiegelte Volume zu, und klicken Sie dann auf **OK**. 
5. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Volumegruppen**. Die neue Volumegruppe sollte unter dem Knoten **Volumegruppen** angezeigt werden. 
6. Klicken Sie mit der rechten Maustaste auf den Namen der Volumegruppe. 
   
   * Um einen interaktiven (bedarfsgesteuerten) Sicherungsauftrag zu starten, klicken Sie auf **Sicherung anlegen**. 
   * Um eine automatische Sicherung zu planen, klicken Sie auf **Sicherungsrichtlinie erstellen**. Wählen Sie auf der Seite **Allgemein** die Volumegruppe aus der Liste aus. Geben Sie auf der Seite **Zeitplan** Details zum Zeitplan ein. Wenn Sie fertig sind, klicken Sie auf **OK**. 
7. Sie können den Sicherungsauftrag während der Ausführung überwachen. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Aufträge**, und klicken Sie dann auf **Wird ausgeführt**. Die Auftragsdetails werden im Bereich **Ergebnisse** angezeigt. Nach Abschluss des Sicherungsauftrags werden die Details an die Auftragsliste **Letzte 24 Stunden** übertragen. 

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* Erfahren Sie mehr über das [Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Volumegruppen](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)