---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552586"
---
#### <a name="to-create-a-volume"></a>So erstellen Sie ein Volume
1. Wählen Sie auf dem Blatt **Geräte** in der tabellarischen Geräteauflistung Ihr Gerät aus. Klicken Sie auf **+ Volume hinzufügen**.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Hinzufügen eines neuen Volumes](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. Auf dem Blatt **Volume hinzufügen**:
   
   1. Das Feld **Gerät auswählen** wird automatisch mit den Informationen zum aktuellen Gerät ausgefüllt.

   2. Wählen Sie in der Dropdownliste den Volumecontainer aus, dem Sie ein Volume hinzufügen müssen. 

   3. den **Namen** für das Volume ein. Sie können ein Volume nicht mehr umbenennen, nachdem es erstellt wurde.

   4. Wählen Sie in der Dropdownliste den **Typ** für das Volume aus. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie ein **lokales** Volume aus. Wählen Sie für alle anderen Daten ein **mehrstufiges** Volume aus. Wenn Sie dieses Volume für archivierte Daten verwenden, aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**.
      
       Ein mehrstufiges Volume wird mit schlanker Speicherzuweisung bereitgestellt und kann schnell erstellt werden. Bei Verwendung des mehrstufigen Volumes für archivierte Daten wird durch Auswahl der Option **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** die Deduplizierungblockgröße für das Volume in 512 KB geändert. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.
       
       Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume lokal auf dem Gerät verbleiben und nicht in die Cloud übergehen.  Wenn Sie ein lokales Volume erstellen, überprüft das Gerät den verfügbaren Speicherplatz auf den lokalen Ebenen, um das Volume in der angeforderten Größe bereitzustellen. Bei der Erstellung eines lokalen Volumes werden vorhandene Daten eventuell vom Gerät in die Cloud übertragen, und das Erstellen des Volumes kann längere Zeit in Anspruch nehmen. Die gesamte Zeit hängt von der Größe des bereitgestellten Volumes, von der verfügbaren Netzwerkbandbreite und von den Daten auf Ihrem Gerät ab.

   5. Geben Sie die **Bereitgestellte Kapazität** für das Volume an. Notieren Sie die Kapazität, die basierend auf dem ausgewählten Volumetyp verfügbar ist. Die angegebene Volumegröße darf den verfügbaren Speicherplatz nicht überschreiten.
      
       Sie können lokale Volumes von bis zu 8,5 TB oder mehrstufige Volumes von bis zu 200 TB auf dem 8100-Gerät bereitstellen. Auf dem größeren 8600-Gerät können Sie lokale Volumes von bis zu 22,5 TB oder mehrstufige Volumes von bis zu 500 TB bereitstellen. Da für das Hosten des Arbeitssatzes mehrstufiger Volumes lokaler Speicher auf dem Gerät erforderlich ist, wirkt sich die Erstellung lokaler Volumes auf den Speicherplatz aus, der für die Bereitstellung mehrstufiger Volumes zur Verfügung steht. Wenn Sie daher ein lokales Volume erstellen, verringert sich dadurch der verfügbare Speicherplatz für die Erstellung mehrstufiger Volumes. Wenn Sie ein mehrstufiges Volume erstellen, reduziert sich in gleicher Weise der verfügbare Speicherplatz für die Erstellung lokaler Volumes.
      
       Wenn Sie ein lokales Volume von 8,5 TB (maximal zulässige Größe) auf dem 8100-Gerät bereitstellen, haben Sie damit den gesamten lokalen Speicherplatz ausgeschöpft, der auf dem Gerät verfügbar ist. Ab diesem Zeitpunkt können Sie keine mehrstufigen Volumes mehr erstellen, da auf dem Gerät kein lokaler Speicherplatz zum Hosten des Arbeitssatzes mehrstufiger Volumes mehr vorhanden ist. Vorhandene mehrstufige Volumes wirken sich ebenfalls auf den verfügbaren Speicherplatz aus. Wenn Sie beispielsweise ein 8100-Gerät verwenden, auf dem bereits mehrstufige Volumes von ca. 106 TB vorhanden sind, stehen nur noch 4 TB Speicherplatz für lokale Volumes zur Verfügung.
 
      1. Klicken Sie im Feld **Verbundene Hosts** auf den Pfeil. 

         ![Verbundene Hosts](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. Wählen Sie auf dem Blatt **Verbundene Hosts** einen vorhandenen SCR aus, oder erstellen Sie mit den folgenden Schritten einen neuen ACR:

         1. Geben Sie einen **Namen** für den ACR ein.
         2. Geben Sie unter **iSCSI-Initiatorname** den qualifizierten iSCSI-Namen (IQN) des Windows-Hosts an. Wenn Sie den IQN nicht kennen, fahren Sie mit [Abrufen des IQNs eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host)fort.

      1. Klicken Sie auf **Erstellen**. Ein Volume wird mit den angegebenen Einstellungen erstellt.

         ![Klicken Sie auf „Erstellen“.](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Beachten Sie, dass das hier erstellte Volume nicht geschützt ist. Erstellen Sie Sicherungsrichtlinien, und ordnen Sie diese dem Volume zu, damit geplante Sicherungen durchgeführt werden. 

