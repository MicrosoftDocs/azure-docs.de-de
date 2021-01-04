---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375891"
---
Der letzte Schritt ist die Vorbereitung des Geräts für den Versands. In diesem Schritt werden alle Gerätefreigaben offline geschaltet. Sobald Sie mit diesem Prozess begonnen haben, können Sie nicht mehr auf die Freigaben zugreifen.

> [!IMPORTANT]
> Im Rahmen der erforderlichen Versandvorbereitung werden Daten markiert, die nicht den Namenskonventionen von Azure entsprechen. Durch diesen Schritt wird verhindert, dass aufgrund von nicht konformen Daten potenzielle Fehler beim Hochladen von Daten auftreten.

1. Wechseln Sie zu **Versandvorbereitung** , und klicken Sie auf **Vorbereitung starten**. Prüfsummen werden standardmäßig beim Kopieren von Daten berechnet. Bei der Versandvorbereitung wird die Prüfsummenberechnung abgeschlossen und die Liste der Dateien ( *-BOM-Dateien* ) erstellt. Die Berechnung der Prüfsumme kann je nach Größe Ihrer Daten mehrere Stunden oder Tage dauern. 
   
    ![Versandvorbereitung 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Die Gerätevorbereitung kann bei Bedarf durch Klicken auf **Vorbereitung beenden** beendet werden. Sie können die Versandvorbereitung später fortsetzen.
        
    ![Versandvorbereitung 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Die Versandvorbereitung beginnt, und die Gerätefreigaben werden offline geschaltet. <!--You see a reminder to download the shipping label once the device is ready.--> Nach Abschluss der Gerätevorbereitung ändert sich der Gerätestatus in *Bereit für den Versand* , und das Gerät wird gesperrt.
        
    ![Versandvorbereitung 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Falls Sie weitere Daten auf das Gerät kopieren möchten, können Sie das Gerät entsperren, weitere Daten kopieren und die Versandvorbereitung erneut ausführen.

    Falls bei diesem Schritt Fehler auftreten, wird der Status auf *Überprüfung wurde mit Fehlern abgeschlossen* aktualisiert. Entsperren Sie das Gerät, und navigieren Sie zur Seite **Verbindung herstellen und Daten kopieren**. Laden Sie anschließend die Liste mit den Problemen herunter, und beheben Sie die Fehler.

    ![Versandvorbereitung 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Führen Sie nach Abschluss der Fehlerbehebung **Für Versand vorbereiten** aus.

4. Führen Sie die folgenden Schritte aus, nachdem die Versandvorbereitung erfolgreich abgeschlossen wurde (ohne Fehler):

    1. Notieren Sie sich die Referenznummer für den Abschluss. Je nach Land, in dem Sie sich befinden, wird diese Nummer ggf. für einige Vorgänge benötigt.
    2. Laden Sie die Liste mit den Dateien (auch als Manifest bezeichnet) herunter, die bei diesem Prozess kopiert wurden. Sie können diese Liste später verwenden, um die in Azure hochgeladenen Dateien zu überprüfen. Weitere Informationen finden Sie unter [Inspect BOM files during Prepare to ship](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship) (Überprüfen von BOM-Dateien während der Versandvorbereitung).
        
        ![Versandvorbereitung 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Wählen Sie die Versandanweisungen für das Gerät aus, und laden Sie sie herunter. Die Versandanweisungen unterscheiden sich je nach dem Land, in dem Sie sich befinden.
    4. Falls in der E-Ink-Anzeige das Adressetikett nicht angezeigt wird, können Sie das Etikett für die Rücksendung hier herunterladen. 

5. Fahren Sie das Gerät herunter. Wechseln Sie zur Seite **Herunterfahren oder neu starten** , und klicken Sie auf **Herunterfahren**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK** , um fortzufahren.

6. Entfernen Sie die Kabel. Im nächsten Schritt versenden Sie das Gerät an Microsoft.
