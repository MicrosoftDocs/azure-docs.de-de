---
title: Tutorial zum Konfigurieren von Netzwerkeinstellungen für ein Azure Stack Edge Mini R-Gerät im Azure-Portal
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Mini R erfahren Sie, wie Sie Netzwerk-, Computenetzwerk- und Webproxyeinstellungen für Ihr physisches Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: 915aca5f7400496aacb3c3cf248120dff39d747c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464262"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Tutorial: Konfigurieren des Netzwerks für Azure Stack Edge Mini R

In diesem Tutorial erfahren Sie, wie Sie das Netzwerk für Ihr Azure Stack Edge Mini R-Gerät mit eingebauter GPU über die lokale Webbenutzeroberfläche konfigurieren.

Der Verbindungsvorgang dauert ca. 20 Minuten.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Mini R-Gerät konfigurieren und einrichten:

* Sie haben das physische Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-install.md) installiert.
* Sie haben eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts hergestellt, wie unter [Tutorial: Herstellen einer Verbindung mit Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md) beschrieben.


## <a name="configure-network"></a>Netzwerk konfigurieren

Auf der Seite **Erste Schritte** werden die verschiedenen Einstellungen angezeigt, die zum Konfigurieren und Registrieren des physischen Geräts beim Azure Stack Edge-Dienst erforderlich sind. 

Führen Sie diese Schritte aus, um das Netzwerk für Ihr Gerät zu konfigurieren.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Erste Schritte**. 

2. Sollte ein Zero-Day-Update erforderlich sein, können Sie es hier durchführen, indem Sie einen Datenport mit einer Kabelverbindung konfigurieren. Weitere Informationen zur Einrichtung einer Kabelverbindung für dieses Gerät finden Sie unter [Verkabeln Ihres Geräts](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Nach Abschluss des Updates können Sie die Kabelverbindung entfernen.

3. Erstellen Sie Zertifikate für WLAN und Signaturkette. Sowohl das Signaturkettenzertifikat als auch das WLAN-Zertifikat muss im DER-Format vorliegen und über die Dateierweiterung *.cer* verfügen. Eine entsprechende Anleitung finden Sie unter [Verwenden von Zertifikaten mit einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-j-series-manage-certificates.md).

4. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Erste Schritte**. Wählen Sie auf der Kachel **Sicherheit** die Option **Zertifikate** und anschließend **Konfigurieren** aus. 

    [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Wählen Sie **+ Zertifikat hinzufügen** aus. 
    
        [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Laden Sie die Signaturkette hoch, und wählen Sie **Anwenden** aus.

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Wiederholen Sie die Prozedur mit dem WLAN-Zertifikat. 

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Die neuen Zertifikate sollten auf der Seite **Zertifikate** angezeigt werden. 
    
        [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Kehren Sie zu **Erste Schritte** zurück.

3. Wählen Sie auf der Kachel **Netzwerk** die Option **Konfigurieren** aus.  
    
    Auf Ihrem physischen Gerät stehen fünf Netzwerkschnittstellen zur Verfügung. PORT 1 und PORT 2 sind 1-Gbit/s-Netzwerkschnittstellen. PORT 3 und PORT 4 sind 10-Gbit/s-Netzwerkschnittstellen. Der fünfte Port ist der WLAN-Port. 

    [![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Wählen Sie den WLAN-Port aus, und konfigurieren Sie die Porteinstellungen. 
    
    > [!IMPORTANT]
    > Es wird dringend empfohlen, eine statische IP-Adresse für den WLAN-Port zu konfigurieren.  

    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Nachdem Sie die Einstellungen für den WLAN-Port angewendet haben, wird die Seite **Netzwerk** aktualisiert.

    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Wählen Sie **WLAN-Profil hinzufügen** aus, und laden Sie Ihr WLAN-Profil hoch. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Ein Funknetzwerkprofil enthält die SSID (Netzwerkname), den Kennwortschlüssel sowie Sicherheitsinformationen für die Verbindungsherstellung mit einem Funknetzwerk. Das WLAN-Profil für Ihre Umgebung erhalten Sie von Ihrem Netzwerkadministrator.

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Nach dem Hinzufügen des Profils wird die Liste mit den WLAN-Profilen aktualisiert, und das neue Profil wird angezeigt. Der **Verbindungsstatus** des Profils sollte **Getrennt** lauten. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Nachdem das Funknetzwerkprofil erfolgreich geladen wurde, können Sie eine Verbindung mit diesem Profil herstellen. Wählen Sie **Connect to Wi-Fi profile** (Verbindung mit WLAN-Profil herstellen) aus. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Wählen Sie das im vorherigen Schritt hinzugefügte WLAN-Profil und anschließend **Anwenden** aus. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    Der **Verbindungsstatus** sollte in **Verbunden** aktualisiert werden. Die Signalstärke wird aktualisiert, um die Qualität des Signals anzugeben. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Für die Übertragung großer Datenmengen empfiehlt es sich, anstelle des Funknetzwerks eine Kabelverbindung zu verwenden. 

6. Trennen Sie PORT 1 des Geräts vom Laptop. 

7. Beachten Sie Folgendes, wenn Sie Netzwerkeinstellungen konfigurieren:

   - Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert. IP-Adresse, Subnetz, Gateway und DNS werden automatisch zugewiesen.
   - Wenn DHCP nicht aktiviert ist, können Sie bei Bedarf statische IP-Adressen zuweisen.
   - Sie können die Netzwerkschnittstelle als IPv4 konfigurieren.
   - Die Seriennummer eines beliebigen Ports entspricht der Seriennummer des Knotens. Bei einem Gerät der K-Serie wird nur eine einzelne Seriennummer angezeigt.

     >[!NOTE] 
     > Wir empfehlen, die lokale IP-Adresse der Netzwerkschnittstelle nicht von statisch auf DHCP umzustellen, es sei denn, Sie haben eine andere IP-Adresse für die Verbindung zum Gerät. Wenn Sie eine Netzwerkschnittstelle verwenden und zu DHCP wechseln, gibt es keine Möglichkeit, die DHCP-Adresse zu bestimmen. Wenn Sie zu einer DHCP-Adresse wechseln möchten, warten Sie, bis sich das Gerät beim Dienst registriert hat, und ändern Sie dann. Sie können dann die IPs aller Adapter im Azure-Portal in den **Geräteeigenschaften** für Ihren Dienst anzeigen.

Nachdem Sie die Netzwerkeinstellungen konfiguriert und angewendet haben, können Sie **Weiter: Compute** auswählen, um das Computenetzwerk zu konfigurieren.

## <a name="enable-compute-network"></a>Computenetzwerk aktivieren

Befolgen Sie diese Schritte, um Compute zu aktivieren und das Computenetzwerk zu konfigurieren. 


1. Wählen Sie auf der Seite **Compute** eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. 

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. Wählen Sie im Dialogfeld **Netzwerkeinstellungen** die Option **Aktivieren** aus. Wenn Sie Compute aktivieren, wird auf Ihrem Gerät an dieser Netzwerkschnittstelle ein virtueller Switch erstellt. Der virtuelle Switch wird für die Compute-Infrastruktur auf dem Gerät verwendet. 
    
1. Weisen Sie **IP-Adressen für Kubernetes-Knoten** zu. Diese statischen IP-Adressen gelten für die Compute-VM.  

    Für ein Gerät mit *n*-Knoten wird ein zusammenhängender Bereich von mindestens *n+1* IPv4-Adressen für die Compute-VM unter Verwendung der Start- und End-IP-Adresse bereitgestellt. Da es sich bei Azure Stack Edge um ein Gerät mit einem Knoten handelt, werden mindestens zwei zusammenhängende IPv4-Adressen bereitgestellt.

    > [!IMPORTANT]
    > Kubernetes in Azure Stack Edge verwendet das Subnetz 172.27.0.0/16 für den Pod und das Subnetz 172.28.0.0/16 für den Dienst. Stellen Sie sicher, dass diese in Ihrem Netzwerk nicht verwendet werden. Werden diese Subnetze bereits in Ihrem Netzwerk verwendet, können Sie sie ändern, indem Sie das Cmdlet `Set-HcsKubeClusterNetworkInfo` über die PowerShell-Schnittstelle des Geräts ausführen. Weitere Informationen finden Sie unter [Ändern des Pod- und Dienstsubnetzes in Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Weisen Sie **externe Dienst-IP-Adressen für Kubernetes** zu. Dies sind auch die IP-Adressen für den Lastenausgleich. Diese zusammenhängenden IP-Adressen sind für Dienste, die Sie außerhalb des Kubernetes-Clusters verfügbar machen möchten. Sie legen den statischen IP-Adressenbereich abhängig von der Anzahl der verfügbar gemachten Dienste fest. 
    
    > [!IMPORTANT]
    > Es wird dringend empfohlen, mindestens eine IP-Adresse für den Azure Stack Edge Mini R-Hubdienst anzugeben, um auf Computemodule zugreifen zu können. Sie können dann optional zusätzliche IP-Adressen für andere Dienste/IoT Edge-Module (eine pro Dienst/Modul) angeben, auf die von außerhalb des Clusters zugegriffen werden muss. Die IP-Adressen des Diensts können später aktualisiert werden. 
    
1. Wählen Sie **Übernehmen**.

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Es dauert einige Minuten, bis die Konfiguration übernommen wurde. Möglicherweise müssen Sie den Browser aktualisieren. Wie Sie sehen, ist der angegebene Port für Compute aktiviert. 
 
    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Klicken Sie auf **Weiter: Webproxy**, um den Webproxy zu konfigurieren.  

  
## <a name="configure-web-proxy"></a>Konfigurieren des Webproxys

Dies ist eine optionale Konfiguration.

> [!IMPORTANT]
> * Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Mini R-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. NTLM wird nicht unterstützt.
>* Dateien für die automatische Proxykonfiguration (Proxy Auto Config, PAC) werden nicht unterstützt. Eine PAC-Datei definiert, wie Webbrowser und andere Benutzer-Agents automatisch den entsprechenden Proxyserver (Zugriffsmethode) zum Abrufen einer bestimmten URL auswählen können. Proxys, die versuchen, den gesamten Datenverkehr abzufangen und zu lesen (und anschließend alles mit eigener Zertifizierung neu zu signieren), sind nicht kompatibel, da das Zertifikat des Proxys nicht vertrauenswürdig ist. In der Regel funktionieren transparente Proxys gut mit Azure Stack Edge Mini R. Nicht transparente Webproxys werden nicht unterstützt.


1. Führen Sie auf der Seite **Webproxyeinstellungen** die folgenden Schritte aus:

    1. Geben Sie im Feld **Webproxy-URL** die URL im folgenden Format ein: `http://host-IP address or FQDN:Port number`. HTTPS-URLs werden nicht unterstützt.

    2. Klicken Sie unter **Authentifizierung** auf **Keine** oder **NTLM**. Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Mini R-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. **NTLM** wird nicht unterstützt.

    3. Falls Sie eine Authentifizierung verwenden, geben Sie einen Benutzernamen und ein Kennwort ein.

    4. Klicken Sie auf **Anwenden**, um die konfigurierten Webproxyeinstellungen zu überprüfen und anzuwenden.
    
   ![Seite „Webproxyeinstellungen“ der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Nachdem die Einstellungen angewendet wurden, wählen Sie **Weiter: Gerät** ein.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


Informationen zum Einrichten Ihres Azure Stack Edge Mini R-Geräts finden Sie hier:

> [!div class="nextstepaction"]
> [Konfigurieren von Geräteeinstellungen](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
