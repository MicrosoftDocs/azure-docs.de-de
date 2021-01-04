---
title: Verwalten von Geräten mit dem StorSimple Snapshot Manager | Microsoft Docs
description: Beschreibt, wie das MMC-Snap-In StorSimple Snapshot Manager zum Herstellen von Verbindungen mit StorSimple-Geräten und zu deren Verwaltung verwendet wird.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022971"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Verwenden des StorSimple Snapshot Managers zum Verbinden und Verwalten von StorSimple-Geräten
## <a name="overview"></a>Übersicht
Mithilfe der Knoten im **Bereichsfenster** des StorSimple Snapshot Managers können Sie die importierten Daten von StorSimple-Geräten überprüfen und verbundene Speichergeräte aktualisieren. Darüber hinaus können Sie beim Klicken auf den Knoten **Geräte** eine Liste der verbundenen Geräte und die zugehörigen Statusinformationen im Bereich **Ergebnisse** anzeigen.

![Verbundene Geräte](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Abbildung 1: Verbundenes Gerät im StorSimple Snapshot Manager** 

Je nach Ihrer Auswahl für die **Ansicht** werden im Bereich **Ergebnisse** die folgenden Informationen über jedes Gerät angezeigt. (Weitere Informationen zum Konfigurieren einer Ansicht finden Sie unter [Menü "Ansicht"](storsimple-use-snapshot-manager.md#view-menu).

| Ergebnisspalte | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Name des Geräts wie im klassischen Azure-Portal konfiguriert |
| Modell |Die Modellnummer des Geräts |
| Version |Die Version der auf dem Gerät installierten Software |
| Status |Gibt an, ob das Gerät verfügbar ist |
| Letzte Synchronisierung |Datum und Uhrzeit der letzten Synchronisierung des Geräts |
| Seriennummer |Die Seriennummer des Geräts |

Wenn Sie im Fenster **Bereich** mit der rechten Maustaste auf den Knoten **Geräte** klicken, können Sie aus den folgenden Aktionen auswählen:

* Hinzufügen oder Ersetzen eines Geräts
* Verbinden eines Geräts und Überprüfen von Importvorgängen
* Aktualisieren verbundener Geräte

Wenn Sie auf den Knoten **Geräte** klicken, und dann mit der rechten Maustaste auf einen Gerätenamen im Bereich **Ergebnisse** klicken, können Sie aus den folgenden Aktionen auswählen:

* Authentifizieren eines Geräts
* Anzeigen von Gerätedetails
* Aktualisieren eines Geräts
* Löschen einer Gerätekonfiguration
* Ändern eines Gerätekennworts

> [!NOTE]
> Alle diese Aktionen stehen auch im Bereich **Aktionen** zur Verfügung.


In diesem Lernprogramm wird erläutert, wie Sie dem StorSimple Snapshot Manager Verbindungen mit Geräten herstellen und diese verwalten. Außerdem wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Hinzufügen oder Ersetzen eines Geräts
* Verbinden eines Geräts und Überprüfen von Importvorgängen
* Aktualisieren verbundener Geräte
* Authentifizieren eines Geräts
* Anzeigen von Gerätedetails
* Aktualisieren eines einzelnen Geräts
* Löschen einer Gerätekonfiguration
* Ändern eines abgelaufenen Gerätekennworts
* Ersetzen eines ausgefallenen Geräts

> [!NOTE]
> Allgemeine Informationen zu Features der StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Hinzufügen oder Ersetzen eines Geräts
Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät hinzuzufügen oder zu ersetzen.

#### <a name="to-add-or-replace-a-device"></a>So fügen Sie ein Gerät hinzu oder ersetzen es
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den Knoten **Geräte**, und klicken Sie dann auf **Gerät konfigurieren**. Das Dialogfeld **Gerät konfigurieren** wird angezeigt.
   
    ![Konfigurieren eines StorSimple-Geräts](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Wählen Sie in der Dropdownliste **Gerät** die IP-Adresse des Geräts oder des virtuellen Geräts aus. 
4. Geben Sie im Textfeld **Kennwort** das StorSimple Snapshot Manager-Kennwort ein, das Sie im klassischen Azure-Portal für das Gerät erstellt haben. Klicken Sie auf **OK**. Der StorSimple Snapshot Manager sucht nach dem angegebenen Gerät. 
   
   * Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu.
   * Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. Klicken Sie auf **OK**, um die Fehlermeldung zu schließen, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld **Gerät konfigurieren** zu schließen.

## <a name="connect-a-device-and-verify-imports"></a>Verbinden eines Geräts und Überprüfen von Importvorgängen
Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät zu verbinden und zu überprüfen, ob alle vorhandenen Volumegruppen mit zugehörigen Sicherungen importiert werden.

#### <a name="to-connect-a-device-and-verify-imports"></a>So verbinden Sie ein Gerät und überprüfen Importvorgänge
1. Befolgen Sie zum Verbinden eines Geräts mit dem StorSimple Snapshot Manager die Anweisungen zum Hinzufügen oder Ersetzen eines Geräts. Beim Herstellen einer Verbindung mit einem Gerät reagiert der StorSimple Snapshot Manager wie folgt:
   
   * Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. 
   
   * Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu. Wenn Sie das Gerät auswählen, wird es im Bereich **Ergebnisse** angezeigt. Das Statusfeld gibt dabei an, ob das Gerät den Status **Verfügbar** aufweist. Der StorSimple Snapshot Manager importiert alle Volumegruppen, die für das Gerät konfiguriert wurden, sofern den Volumegruppen Sicherungen zugeordnet sind. Sicherungsrichtlinien werden nicht importiert. Volumegruppen ohne zugehörige Sicherungen werden nicht importiert.
2. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
3. Klicken Sie mit der rechten Maustaste auf den obersten Knoten im Fenster **Bereich**, und klicken Sie dann auf **Importanzeige umschalten**.
   
    ![Aktivieren von "Importanzeige umschalten"](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Das Dialogfeld **Importanzeige umschalten** mit dem Status der importierten Volumegruppen und Sicherungen wird angezeigt. Klicken Sie auf **OK**.

Nachdem die Volumegruppen und Sicherungen erfolgreich importiert wurden, können Sie sie im StorSimple Snapshot Manager so verwalten, wie Sie auch Volumegruppen und Sicherungen verwalten, die Sie im StorSimple Snapshot Manager erstellt und konfiguriert haben. 

## <a name="refresh-connected-devices"></a>Aktualisieren verbundener Geräte
Gehen Sie folgendermaßen vor, um die verbundenen StorSimple-Geräte mit dem StorSimple Snapshot Manager zu synchronisieren.

#### <a name="to-refresh-connected-devices"></a>So aktualisieren Sie verbundene Geräte
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fenster **Bereich** mit der rechten Maustaste auf **Geräte**, und klicken Sie dann auf **Geräte aktualisieren**. Damit werden die verbundenen Geräte mit StorSimple Snapshot Manager synchronisiert, sodass Sie die Volumegruppen und Sicherungen einschließlich aktueller kürzlichen Änderungen anzeigen können. 
   
    ![Aktualisieren der StorSimple-Geräte](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Mit der Aktion **Geräte aktualisieren** werden neue Volumegruppen und die zugehörigen Sicherungen von den verbundenen Geräten abgerufen. Im Gegensatz zur Aktion **Volumes erneut überprüfen**, die im Knoten **Volumes** verfügbar ist, wird mit **Geräte aktualisieren** nicht die Sicherungsregistrierung wiederhergestellt.

## <a name="authenticate-a-device"></a>Authentifizieren eines Geräts
Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät im StorSimple Snapshot Manager zu authentifizieren.

#### <a name="to-authenticate-a-device"></a>So authentifizieren Sie ein Gerät
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** auf **Geräte**.
3. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Authentifizieren**.
4. Das Dialogfeld **Authentifizieren** wird angezeigt. Geben Sie das Kennwort für das Gerät ein, und klicken Sie dann auf **OK**.
   
    ![Authentifizieren (Dialogfeld)](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Anzeigen von Gerätedetails
Gehen Sie folgendermaßen vor, um Details zu einem StorSimple-Gerät anzuzeigen und bei Bedarf das Gerät erneut mit dem StorSimple Snapshot Manager zu synchronisieren.

#### <a name="to-view-and-resynchronize-device-details"></a>So zeigen Sie Gerätedetails an und synchronisieren sie erneut
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** auf **Geräte**.
3. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Details**.

4. Das Dialogfeld **Gerätedetails** wird angezeigt. In diesem Feld werden Name, Modell, Version, Seriennummer, Status, qualifizierter iSCSI-Name (IQN) des Ziels und Datum und Uhrzeit der letzten Synchronisierung angezeigt.

* Klicken Sie auf **Erneut synchronisieren**, um das Gerät zu synchronisieren.
* Klicken Sie auf **OK** oder **Abbrechen**, um das Dialogfeld zu schließen.
  
  ![Gerätedetails](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aktualisieren eines einzelnen Geräts
Gehen Sie folgendermaßen vor, um ein einzelnes mit StorSimple Snapshot Manager verbundenes StorSimple-Gerät erneut zu synchronisieren.

#### <a name="to-refresh-a-device"></a>So aktualisieren Sie ein Gerät
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 
2. Klicken Sie im Fensterbereich **Bereich** auf **Geräte**. 
3. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Gerät aktualisieren**. Damit wird das Gerät mit dem StorSimple Snapshot Manager synchronisiert.

## <a name="delete-a-device-configuration"></a>Löschen einer Gerätekonfiguration
Gehen Sie folgendermaßen vor, um die Konfiguration eines einzelnen mit StorSimple Snapshot Manager verbundenen StorSimple-Geräts zu löschen.

#### <a name="to-delete-a-device-configuration"></a>So löschen Sie eine Gerätekonfiguration
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** auf **Geräte**. 
3. Klicken Sie im Bereich **Ergebnisse** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Löschen**. 
4. Die folgende Meldung wird angezeigt. Klicken Sie auf **Ja**, um die Konfiguration zu löschen, oder auf **Nein**, um den Löschvorgang abzubrechen.
   
    ![Löschen der Gerätekonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ändern eines abgelaufenen Gerätekennworts
Sie müssen ein Kennwort eingeben, um ein StorSimple-Gerät im StorSimple Snapshot Manager zu authentifizieren. Sie konfigurieren dieses Kennwort, wenn Sie das Gerät mit der Windows PowerShell-Schnittstelle einrichten. Allerdings kann das Kennwort ablaufen. In diesem Fall können Sie das Kennwort im klassischen Azure-Portal ändern. Da das Gerät im StorSimple Snapshot Manager konfiguriert wurde, bevor das Kennwort abgelaufen ist, müssen Sie das Gerät im StorSimple Snapshot Manager anschließend erneut authentifizieren.

#### <a name="to-change-the-expired-password"></a>So ändern Sie das abgelaufene Kennwort
1. Starten Sie im klassischen Azure-Portal den StorSimple Manager-Dienst.
2. Klicken Sie auf **Geräte** > **Konfigurieren** für das Gerät.
3. Scrollen Sie nach unten zum Abschnitt "StorSimple Snapshot Manager". Geben Sie ein Kennwort mit einer Länge von 14–15 Zeichen ein. Das Kennwort muss eine Kombination aus drei der vier folgenden Elemente sein: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.
4. Geben Sie das Kennwort zur Bestätigung erneut ein.
5. Klicken Sie unten auf der Seite auf **Speichern** .

#### <a name="to-re-authenticate-the-device"></a>So authentifizieren Sie das Gerät erneut
1. Starten Sie StorSimple Snapshot Manager.
2. Klicken Sie im Fensterbereich **Bereich** auf **Geräte**. Im **Ergebnisbereich** wird eine Liste der konfigurierten Geräte angezeigt.
3. Wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Authentifizieren**.
4. Geben Sie im Fenster **Authentifizieren** das neue Kennwort ein.
5. Wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Gerät aktualisieren** aus. Damit wird das Gerät mit dem StorSimple Snapshot Manager synchronisiert.

## <a name="replace-a-failed-device"></a>Ersetzen eines ausgefallenen Geräts
Wenn ein StorSimple-Gerät ausfällt und durch ein Austauschgerät ersetzt wird (Failover), gehen Sie folgendermaßen vor, um eine Verbindung mit dem neuen Gerät herzustellen und die zugehörigen Sicherungen anzuzeigen.

#### <a name="to-connect-to-a-new-device-after-failover"></a>So stellen Sie nach einem Failover eine Verbindung mit einem neuen Gerät her
1. Konfigurieren Sie die iSCSI-Verbindung mit dem neuen Gerät neu. Anweisungen hierzu finden Sie unter "Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes" in [Bereitstellen lokaler StorSimple-Geräte](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Verfügt das neue StorSimple-Gerät über dieselbe IP-Adresse wie das alte, können Sie eventuell die alte Konfiguration verwenden.


1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:
   
   1. Starten Sie den Server-Manager.
   2. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   3. Wählen Sie im Fenster **Dienste** den **Microsoft StorSimple-Verwaltungsdienst**.
   4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst beenden**.
2. Entfernen Sie die Konfigurationsinformationen für das alte Gerät:
   
   1. Navigieren Sie im Datei-Explorer zu "C:\ProgramData\Microsoft\StorSimple\BACatalog".
   2. Löschen Sie die Dateien im Ordner "BACatalog".
3. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu:
   
   1. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** das Element **Dienste** aus.
   2. Wählen Sie im Fenster **Dienste** den **Microsoft StorSimple-Verwaltungsdienst**.
   3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst neu starten**.
4. Starten Sie StorSimple Snapshot Manager.
5. Um das neue StorSimple-Gerät zu konfigurieren, führen Sie die Schritte unter „Schritt 2: Herstellen einer Verbindung mit einem StorSimple-Gerät“ in [Bereitstellen des StorSimple Snapshot Managers](storsimple-snapshot-manager-deployment.md)aus.
6. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf den Knoten der obersten Ebene (im Beispiel „StorSimple Snapshot Manager“), und klicken Sie dann auf **Importanzeige umschalten**. 
7. Es wird eine Meldung angezeigt, wenn die importierten Volumegruppen und Sicherungen im StorSimple Snapshot Manager angezeigt werden. Klicken Sie auf **OK**.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* Weitere Informationen zum [Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Volumes](storsimple-snapshot-manager-manage-volumes.md).

