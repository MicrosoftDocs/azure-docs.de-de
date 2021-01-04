---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e3bff61cfbf89aee3566d677ccf593b102cff36d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376093"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>So fügen Sie eine StorSimple-Sicherungsrichtlinie hinzu

1. Wechseln Sie zu Ihrem StorSimple-Gerät, und klicken Sie auf **Sicherungsrichtlinie**.

2. Klicken Sie auf dem Blatt **Sicherungsrichtlinie** auf der Befehlsleiste auf **+ Richtlinie hinzufügen**.
   
    ![Sicherungsrichtlinie hinzufügen](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Führen Sie auf dem Blatt **Sicherungsrichtlinie erstellen** die folgenden Schritte aus:
   
   1. **Gerät auswählen** wird automatisch mit den Informationen Ihres ausgewählten Geräts ausgefüllt.
   
   2. Geben Sie für Ihre **Sicherungsrichtlinie einen Namen** an, der zwischen 3 und 150 Zeichen lang ist. Der Name der Richtlinie kann nach der Erstellung nicht mehr geändert werden.
       
   3. Zum Hinzufügen von Volumes zu dieser Sicherungsrichtlinie wählen Sie **Volumes hinzufügen**, und klicken dann in der tabellarischen Volumeauflistung auf das bzw. die Kontrollkästchen, um dieser Sicherungsrichtlinie ein oder mehrere Volumes zuzuweisen.

       ![Hinzufügen einer Sicherungsrichtlinie 2](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Zum Definieren eines Zeitplans für diese Sicherungsrichtlinie klicken Sie auf **Erster Zeitplan** und ändern dann die folgenden Parameter:

       ![Hinzufügen einer Sicherungsrichtlinie 3](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Wählen Sie als **Momentaufnahmetyp** die Option **Cloud** oder **Lokal** aus.

       2. Geben Sie die Häufigkeit der Sicherungen an. (Geben Sie eine Zahl an, und wählen Sie dann in der Dropdownliste **Tage** oder **Wochen** aus.)

       3. Geben Sie einen Aufbewahrungszeitplan ein.

       4. Geben Sie Startzeit und -datum für die Sicherungsrichtlinie ein.

       5. Klicken Sie auf **OK**, um den Zeitplan zu definieren.

   5. Klicken Sie auf **Erstellen**, um eine Sicherungsrichtlinie zu erstellen.

       ![Hinzufügen einer Sicherungsrichtlinie 4](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Sie werden benachrichtigt, wenn die Sicherungsrichtlinie erstellt wurde. Die neu hinzugefügte Richtlinie wird in der Tabellenansicht auf dem Blatt **Sicherungsrichtlinie** angezeigt.

       ![Hinzufügen einer Sicherungsrichtlinie 5](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

