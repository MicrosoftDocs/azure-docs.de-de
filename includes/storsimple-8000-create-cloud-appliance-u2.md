---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562204"
---
#### <a name="to-create-a-cloud-appliance"></a>So erstellen Sie ein Cloudgerät

1. Wechseln Sie im Azure-Portal zum Dienst **StorSimple-Geräte-Manager**.
2. Navigieren Sie zum Blatt **Geräte**. Klicken Sie auf dem Blatt mit der Dienstzusammenfassung in der Befehlsleiste auf **Cloudgerät erstellen**.
    ![StorSimple: Cloudgerät erstellen](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. Geben Sie auf dem Blatt **Cloudgerät erstellen** die folgenden Details an.
   
    ![StorSimple: Erstellen eines Cloudgeräts 2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Name**: Ein eindeutiger Name für Ihr Cloudgerät.
   2. **Modell**: Wählen Sie das Modell für das Cloudgerät. Ein 8010-Gerät verfügt über 30 TB Standardspeicher, und ein 8020-Gerät verfügt über 64 TB Storage Premium-Speicher. Geben Sie 8010 an, um Abrufszenarien auf Objektebene aus Sicherungen bereitzustellen. Wählen Sie 8020 aus, um Workloads mit hoher Leistung und geringer Wartezeit bereitzustellen oder um das Gerät als sekundäres Gerät für die Notfallwiederherstellung zu verwenden.
   3. **Version**: Wählen Sie die Version für das Cloudgerät. Diese Version entspricht der Version des virtuellen Datenträgerimages, das zum Erstellen des Cloudgeräts verwendet wird. Da anhand der Version des Cloudgeräts ermittelt wird, welches physische Gerät als Quelle für ein Failover oder einen Klonvorgang verwendet wird, ist es wichtig, eine geeignete Version des Cloudgeräts zu erstellen.
   4. **Virtuelles Netzwerk**: Geben Sie ein virtuelles Netzwerk an, das für dieses Cloudgerät verwendet werden soll. Wenn Sie Storage Premium verwenden, müssen Sie ein virtuelles Netzwerk auswählen, das vom Storage Premium-Konto unterstützt wird. Die nicht unterstützten virtuellen Netzwerke werden in der Dropdownliste abgeblendet. Sie erhalten eine Warnung, wenn Sie ein nicht unterstütztes virtuelles Netzwerk auswählen.
   5. **Subnetz**: Je nach ausgewähltem virtuellem Netzwerk werden in der Dropdownliste die zugeordneten Subnetze angezeigt. Weisen Sie Ihrem Cloudgerät ein Subnetz zu.
   6. **Speicherkonto**: Wählen Sie ein Speicherkonto aus, in dem das Image des Cloudgeräts während der Bereitstellung vorhanden sein soll. Dieses Speicherkonto sollte sich in derselben Region wie das Cloudgerät und das virtuelle Netzwerk befinden. Es sollte weder vom physischen Gerät noch vom Cloudgerät zum Speichern von Daten verwendet werden. Hierfür wird standardmäßig ein neues Speicherkonto erstellt. Wenn Sie jedoch bereits über ein hierfür geeignetes Speicherkonto verfügen, können Sie dieses aus der Liste auswählen. Beim Erstellen eines Premium-Cloudgeräts werden in der Dropdownliste nur Storage Premium-Konten angezeigt.
      
      > [!NOTE]
      > Das Cloudgerät funktioniert nur mit Azure-Speicherkonten.
    
   7. Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie Folgendes verstanden haben: Die auf dem Cloudgerät gespeicherten Daten werden in einem Microsoft-Datencenter gehostet.
       * Wenn Sie lediglich ein physisches Gerät verwenden, wird der Verschlüsselungsschlüssel auf Ihrem Gerät beibehalten. Daher kann Microsoft dieses nicht entschlüsseln.

       * Wenn Sie ein Cloudgerät verwenden, werden die Ver- und Entschlüsselungsschlüssel in Microsoft Azure gespeichert. Weitere Informationen finden Sie unter den [Sicherheitsüberlegungen bei der Verwendung eines Cloudgeräts](../articles/storsimple/storsimple-8000-security.md).
   8. Klicken Sie auf **Erstellen**, um das Cloudgerät bereitzustellen. Die Bereitstellung des Geräts kann etwa 30 Minuten dauern. Sie erhalten eine Benachrichtigung, nachdem das Cloudgerät erfolgreich erstellt wurde. Navigieren Sie zum Blatt „Geräte“. Die Liste mit den Geräten wird aktualisiert, um das Cloudgerät anzuzeigen. Der Status des Geräts lautet **Zur Einrichtung bereit**.
      
      ![StorSimple Cloud Appliance – Zur Einrichtung bereit](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)