---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419958"
---
Der letzte Schritt ist die Vorbereitung des Geräts für den Versands. In diesem Schritt werden alle Gerätefreigaben offline geschaltet. Sobald Sie mit diesem Prozess begonnen haben, können Sie nicht mehr auf die Freigaben zugreifen.

> [!IMPORTANT]
> Im Rahmen der erforderlichen Versandvorbereitung werden Daten markiert, die nicht den Namenskonventionen von Azure entsprechen. Wenn Sie diesen Schritt überspringen, treten beim Hochladen möglicherweise Fehler aufgrund von nicht konformen Daten auf.

1. Wechseln Sie zu **Versandvorbereitung**, und klicken Sie auf **Vorbereitung starten**. Prüfsummen werden standardmäßig beim Kopieren von Daten berechnet. Bei der Versandvorbereitung wird die Prüfsummenberechnung abgeschlossen und die Liste der Dateien erstellt (auch als *BOM-Dateien* oder Manifest bezeichnet). Die Berechnung der Prüfsumme kann je nach Größe Ihrer Daten mehrere Stunden oder Tage dauern.
   
    ![Versandvorbereitung 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Die Gerätevorbereitung kann bei Bedarf durch Klicken auf **Vorbereitung beenden** beendet werden. Sie können die Versandvorbereitung später fortsetzen.
        
    ![Versandvorbereitung 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. Die Versandvorbereitung beginnt, und die Gerätefreigaben werden offline geschaltet. Sobald das Gerät bereit ist, werden Sie daran erinnert, das Adressetikett herunterzuladen.

    ![Erinnerung an das Herunterladen des Adressetiketts](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Nach Abschluss der Gerätevorbereitung ändert sich der Gerätestatus in *Bereit für den Versand*, und das Gerät wird gesperrt.
        
    ![Versandvorbereitung 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Falls Sie weitere Daten auf das Gerät kopieren möchten, können Sie das Gerät entsperren, weitere Daten kopieren und die Versandvorbereitung erneut ausführen.

    Sollten bei diesem Schritt Fehler auftreten, laden Sie das Fehlerprotokoll herunter, und beheben Sie sie. Führen Sie nach Abschluss der Fehlerbehebung **Für Versand vorbereiten** aus.

4. Laden Sie im Anschluss an die (fehlerfreie) Versandvorbereitung die Liste der Dateien herunter, die im Rahmen dieses Prozesses kopiert wurden (auch als *BOM-Dateien* oder Manifest bezeichnet). 

    ![Herunterladen der Liste von Dateien oder BOM-Dateien](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Sie können diese Liste später verwenden, um die in Azure hochgeladenen Dateien zu überprüfen. Weitere Informationen finden Sie unter [Inspect BOM files during Prepare to ship](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship) (Überprüfen von BOM-Dateien während der Versandvorbereitung).
        
    ![Beispiel für eine BOM-Datei](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Fahren Sie das Gerät herunter. Wechseln Sie zur Seite **Herunterfahren oder neu starten**, und klicken Sie auf **Herunterfahren**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**, um fortzufahren.

    ![Herunterfahren des ersten Geräteknotens](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Wiederholen Sie alle oben genannten Schritte für den anderen Geräteknoten.
7. Sobald das Gerät vollständig heruntergefahren wurde, sind alle LEDs auf der Rückseite des Geräts ausgeschaltet. Der nächste Schritt besteht darin, alle Kabel zu entfernen und das Gerät an Microsoft zu senden.
