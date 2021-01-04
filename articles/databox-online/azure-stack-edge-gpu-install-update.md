---
title: Installieren von Updates auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Updates über das Azure-Portal und die lokale Webbenutzeroberfläche für Azure Stack Edge Pro-GPU-Geräte und den Kubernetes-Cluster auf dem Gerät anwenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/30/2020
ms.author: alkohli
ms.openlocfilehash: 8d17528728c5519244210217b35d6cd6a3afe715
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449131"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Aktualisieren Ihrer Azure Stack Edge Pro-GPU 

In diesem Artikel werden die Schritte beschrieben, die ausgeführt werden müssen, um Updates über die lokale Webbenutzeroberfläche und über das Azure-Portal auf Ihrem Azure Stack Edge Pro-Gerät mit GPU zu installieren. Softwareupdates oder Hotfixes werden angewandt, um Ihr Azure Stack Edge Pro-Gerät und den zugehörigen Kubernetes-Cluster auf dem Gerät auf dem neuesten Stand zu halten. 

Das in diesem Artikel beschriebene Verfahren wurde mit einer anderen Version der Software ausgeführt, die Vorgehensweise bleibt bei der aktuellen Softwareversion aber dieselbe.

> [!IMPORTANT]
> - Das aktuelle Update ist **2011**. <!--and corresponds to **2.1.1377.2170** software version on your device.--> Informationen zu diesem Update finden Sie in den [Versionshinweisen](azure-stack-edge-gpu-2011-release-notes.md).
>
> - Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Dieses Update enthält die Gerätesoftwareupdates und die Kubernetes-Updates. Da es sich bei Azure Stack Edge Pro um ein Gerät mit einem einzelnen Knoten handelt, werden beim Aktualisieren alle ausgeführten E/A-Vorgänge unterbrochen, und es kommt zu einer Gerätedowntime von bis zu 30 Minuten.

Für die Installation von Updates auf Ihrem Gerät müssen Sie zunächst den Ort des Updateservers konfigurieren. Nach dem Konfigurieren des Updateservers können Sie die Updates über die Benutzeroberfläche des Azure-Portals oder über die lokale Webbenutzeroberfläche anwenden.

Die einzelnen Schritte werden in den folgenden Abschnitten beschrieben.

## <a name="configure-update-server"></a>Konfigurieren des Updateservers

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Konfiguration** > **Updateserver**. 
   
    ![Konfigurieren von Updates 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. Wählen Sie in der Dropdownliste unter **Typ des Updateservers auswählen** zwischen Microsoft Update-Server (Standard) und Windows Server Update Services.  
   
    Geben Sie bei Verwendung der Option „Windows Server Update Services“ den Server-URI an. Der Server unter diesem URI stellt die Updates auf allen Geräten bereit, die mit diesem Server verbunden sind.

    ![Konfigurieren von Updates 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Der WSUS-Server wird verwendet, um Updates über eine Verwaltungskonsole zu verwalten und zu verteilen. Ein WSUS-Server kann auch als Updatequelle für andere WSUS-Server in der Organisation dienen. Der als Updatequelle eingesetzte WSUS-Server wird als Upstreamserver bezeichnet. In einer WSUS-Implementierung muss mindestens ein WSUS-Server im Netzwerk eine Verbindung mit Microsoft Update herstellen können, um verfügbare Updateinformationen herunterzuladen. Als Administrator kannst du – basierend auf der Netzwerksicherheit und -konfiguration – festlegen, wie viele weitere WSUS-Server eine direkte Verbindung mit Microsoft Update herstellen.
    
    Weitere Informationen finden Sie unter [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus).

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Die Installation von Updates über das Azure-Portal wird empfohlen. Das Gerät sucht einmal täglich automatisch nach Updates. Sobald Updates verfügbar sind, wird im Portal eine Benachrichtigung angezeigt. Sie können die Updates dann herunterladen und installieren. 

> [!NOTE]
> Stellen Sie sicher, dass das Gerät fehlerfrei ist und als Status **Online** angezeigt wird, bevor Sie mit der Installation der Updates fortfahren.

1. Sobald die Updates für Ihr Gerät verfügbar sind, wird eine Benachrichtigung angezeigt. Wählen Sie die Benachrichtigung aus, oder klicken Sie auf der oberen Befehlsleiste auf **Gerät aktualisieren**. Auf diese Weise können Sie Updates der Gerätesoftware anwenden.

    ![Softwareversion nach dem Update](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Bestätigen Sie auf dem Blatt **Geräteupdates**, dass Sie die Lizenzbedingungen für neue Features in den Versionshinweisen gelesen haben.

    Sie können auswählen, ob Sie die Updates **herunterladen und installieren** oder nur **herunterladen** möchten. Sie können diese Updates dann später installieren.

    ![Softwareversion nach dem Update 2](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    Wenn Sie die Updates herunterladen und installieren möchten, aktivieren Sie die Option zum automatischen Installieren der Updates nach Abschluss des Downloads.

    ![Softwareversion nach dem Update 3](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. Der Download der Updates wird gestartet. Es wird eine Benachrichtigung angezeigt, dass der Download läuft.

    ![Softwareversion nach dem Update 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Außerdem wird im Azure-Portal ein Benachrichtigungsbanner angezeigt. Dies zeigt den Downloadstatus an. 

    ![Softwareversion nach dem Update 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Sie können diese Benachrichtigung oder **Gerät aktualisieren** auswählen, um den ausführlichen Status des Updates anzuzeigen.

    ![Softwareversion nach dem Update 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. Nachdem der Download abgeschlossen ist, wird das Benachrichtigungsbanner aktualisiert, um den Abschluss anzuzeigen. Wenn Sie ausgewählt haben, dass die Updates heruntergeladen und installiert werden sollen, wird die Installation automatisch gestartet.

    ![Softwareversion nach dem Update 7](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Wenn Sie Updates nur herunterladen möchten, wählen Sie die Benachrichtigung aus, um das Blatt **Geräteupdates** zu öffnen. Wählen Sie **Installieren** aus.
  
    ![Softwareversion nach dem Update 8](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Es wird eine Benachrichtigung angezeigt, dass die Installation läuft. 

    ![Softwareversion nach dem Update 9](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    Im Portal wird außerdem eine Informationsmeldung angezeigt, um darauf hinzuweisen, dass die Installation ausgeführt wird. Das Gerät wird offline geschaltet und in den Wartungsmodus versetzt.
   
    ![Softwareversion nach dem Update 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Da es sich um ein Gerät mit Einzelknoten handelt, wird das Gerät nach der Installation der Updates neu gestartet. Die kritische Warnung während des Neustarts weist darauf hin, dass das Gerät keinen Heartbeat mehr ausgibt.

    ![Softwareversion nach dem Update 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Wählen Sie die Warnung aus, um das entsprechende Geräteereignis anzuzeigen.
    
    ![Softwareversion nach dem Update 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Nach dem Neustart wird das Gerät erneut in den Wartungsmodus versetzt, und es wird eine Informationsmeldung mit einem entsprechenden Hinweis angezeigt.

    Wenn Sie auf der oberen Befehlsleiste die Option **Gerät aktualisieren** auswählen, wird der Fortschritt der Updates angezeigt.   

8. Nachdem die Updates installiert wurden, ändert sich der Gerätestatus in **Online**. 

    ![Softwareversion nach dem Update 13](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    Wählen Sie auf der oberen Befehlsleiste **Geräteupdates** aus. Vergewissern Sie sich, dass das Update erfolgreich installiert wurde und die entsprechende Version der Gerätesoftwareversion angezeigt wird.

    ![Softwareversion nach dem Update 14](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

<!--9. You will again see a notification that updates are available. These are the Kubernetes updates. Select the notification or select **Update device** from the top command bar.

    ![Software version after update 15](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Download the Kubernetes updates. You can see that the package size is different when compared to the previous update package.

    ![Software version after update 16](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    The process of installation is identical to that of device updates. First the updates are downloaded.

    ![Software version after update 17](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Once the updates are downloaded, you can then install the updates. 

    ![Software version after update 18](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    As the updates are installed, the device is put into maintenance mode. The device does not restart for the Kubernetes updates. -->

Nach erfolgreicher Installation der Updates für die Gerätesoftware und Kubernetes wird die Bannerbenachrichtigung ausgeblendet. Ihr Gerät verfügt nun über die aktuelle Version der Gerätesoftware sowie über die aktuelle Version von Kubernetes.


## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.


### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um das Update herunterzuladen. Sie können das Update von einem von Microsoft bereitgestellten Speicherort oder aus dem Microsoft Update-Katalog herunterladen.


Führen Sie die folgenden Schritte aus, um das Softwareupdate aus dem Microsoft Update-Katalog herunterzuladen.

1. Starten Sie einen Browser, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

    ![Katalog durchsuchen](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes oder der Nutzungsbedingungen des Updates ein, das Sie herunterladen möchten. Geben Sie beispielsweise **Azure Stack Edge Pro** ein, und klicken Sie dann auf **Suchen**.
   
    Der Updateeintrag lautet **Azure Stack Edge-Update 2011**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)-->

4. Wählen Sie **Herunterladen** aus. Es müssen zwei Dateien mit dem Suffix *SoftwareUpdatePackage.exe* (für Gerätesoftwareupdates) bzw. *Kubernetes_Package.exe* (für Kubernetes-Updates) heruntergeladen werden. Laden Sie die Dateien in einen Ordner des lokalen Systems herunter. Sie können den Ordner auch in eine Netzwerkfreigabe kopieren, auf die vom Gerät aus zugegriffen werden kann.

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Vergewissern Sie sich vor dem Installieren des Updates oder Hotfixes, dass Folgendes erfüllt ist:

 - Das Update oder der Hotfix wurde lokal auf Ihren Host heruntergeladen, oder auf das Update oder den Hotfix kann über eine Netzwerkfreigabe zugegriffen werden.
 - Der Gerätestatus ist fehlerfrei, wie auf der Seite **Übersicht** der lokalen Webbenutzeroberfläche angezeigt wird.

   ![Gerät aktualisieren](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

Dieser Vorgang dauert etwa 20 Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen. 
   
   ![Aktualisieren des Geräts 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Geben Sie den Pfad zur Updatedatei an. Sie können auch zur Installationsdatei des Updates navigieren, sofern sie auf einer Netzwerkfreigabe platziert wurde. Wählen Sie die Softwareupdatedatei mit dem Suffix *SoftwareUpdatePackage.exe* aus.

   ![Aktualisieren des Geräts 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. Wählen Sie **Übernehmen**. 

   ![Aktualisieren des Geräts 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren. Da es sich um ein Gerät mit einem Einzelknoten handelt, wird das Gerät neu gestartet, und es kommt zu einer Downtime. 
   
   ![Aktualisieren des Geräts 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
6. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Für das aktuelle Release sollte die Softwareversion **Azure Stack Edge 2011** angezeigt werden.

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)--> 

7. Als Nächstes wird die Kubernetes-Softwareversion aktualisiert. Wiederholen Sie die obigen Schritte. Geben Sie einen Pfad zur Kubernetes-Updatedatei mit dem Suffix *Kubernetes_Package.exe* an.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. Wählen Sie **Übernehmen**. 

   ![Aktualisieren des Geräts 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren. 

10. Nach erfolgreicher Installation des Kubernetes-Updates bleibt die unter **Wartung** > **Softwareupdate** angezeigte Software unverändert. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Verwaltung Ihrer Azure Stack Edge Pro-Instanz](azure-stack-edge-manage-access-power-connectivity-mode.md).