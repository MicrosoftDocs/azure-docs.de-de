---
title: Tutorial zum Konfigurieren von Netzwerkeinstellungen für ein Azure Stack Edge Pro-Gerät mit GPU im Azure-Portal | Microsoft-Dokumentation
description: Im Tutorial zur Bereitstellung eines Azure Stack Edge Pro-Geräts mit GPU erfahren Sie, wie Sie Netzwerk-, Computenetzwerk- und Webproxyeinstellungen für Ihr physisches Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 07a4c06b840d41455beea9be4ed0343b4946ddb3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594602"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>Tutorial: Konfigurieren des Netzwerks für Azure Stack Edge Pro mit GPU

In diesem Tutorial wird erläutert, wie Sie das Netzwerk für Ihr Azure Stack Edge Pro-Gerät mit eingebauter GPU über die lokale Webbenutzeroberfläche konfigurieren.

Der Verbindungsvorgang dauert ca. 20 Minuten.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie Folgendes, bevor Sie ein Azure Stack Edge Pro-Gerät mit GPU konfigurieren und einrichten:

* Sie haben Ihr physisches Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) installiert.
* Sie haben, wie unter [Herstellen einer Verbindung mit Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md), beschrieben, eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts hergestellt.


## <a name="configure-network"></a>Netzwerk konfigurieren

Auf der Seite **Erste Schritte** werden die verschiedenen Einstellungen angezeigt, die zum Konfigurieren und Registrieren des physischen Geräts beim Azure Stack Edge-Dienst erforderlich sind. 

Führen Sie diese Schritte aus, um das Netzwerk für Ihr Gerät zu konfigurieren.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Erste Schritte**. 

2. Wählen Sie auf der Kachel **Netzwerk** die Option **Konfigurieren** aus.  
    
    ![Kachel „Netzwerkeinstellungen“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Auf dem physischen Gerät gibt es sechs Netzwerkschnittstellen. PORT 1 und PORT 2 sind 1-Gbit/s-Netzwerkschnittstellen. PORT 3, PORT 4, PORT 5 und PORT 6 sind alle 25 Gbit/s-Netzwerkschnittstellen, die auch als 10-Gbit/s-Netzwerkschnittstellen verwendet werden können. PORT 1 wird automatisch als reiner Verwaltungsport konfiguriert, während es sich bei PORT 2 bis PORT 6 um Datenports handelt. Für ein neues Gerät wird, wie unten dargestellt, die Seite **Netzwerkeinstellungen** gezeigt.
    
    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)

3. Um die Netzwerkeinstellungen zu ändern, wählen Sie einen Port aus. Ändern Sie im eingeblendeten rechten Bereich die IP-Adresse, das Subnetz, das Gateway, das primäre DNS und das sekundäre DNS. 

    - Wenn Sie Port 1 auswählen, sehen Sie, dass er als statisch vorkonfiguriert ist. 

        ![Seite „Netzwerkeinstellungen für Port 1“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Wenn Sie Port 2, Port 3, Port 4 oder Port 5 auswählen, werden alle diese Ports standardmäßig mit DHCP konfiguriert.

        ![Seite „Netzwerkeinstellungen für Port 3“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Beachten Sie Folgendes, wenn Sie Netzwerkeinstellungen konfigurieren:

   * Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert. IP-Adresse, Subnetz, Gateway und DNS werden automatisch zugewiesen.
   * Wenn DHCP nicht aktiviert ist, können Sie bei Bedarf statische IP-Adressen zuweisen.
   * Sie können die Netzwerkschnittstelle als IPv4 konfigurieren.
   * Für die 25-GBit/s-Schnittstellen können Sie den RDMA-Modus (Remote Direct Access Memory) auf iWarp oder RoCE (RDMA over Converged Ethernet) festlegen. Wenn niedrige Latenz die Hauptanforderung ist und Skalierbarkeit keine Rolle spielt, wählen Sie RoCE. Wenn Latenz eine Hauptanforderung ist, aber auch Benutzerfreundlichkeit und Skalierbarkeit hohe Priorität haben, ist iWARP die beste Wahl.
   * Der NIC-Teamvorgang (Network Interface Card, Netzwerkadapter) oder Linkaggregation wird bei Azure Stack Edge nicht unterstützt. 
   * Die Seriennummer eines beliebigen Ports entspricht der Seriennummer des Knotens.

    Sobald das Gerätenetz konfiguriert ist, wird die Seite wie unten dargestellt aktualisiert.

    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     > [!NOTE]
     > Wir empfehlen, die lokale IP-Adresse der Netzwerkschnittstelle nicht von statisch auf DHCP umzustellen, es sei denn, Sie haben eine andere IP-Adresse für die Verbindung zum Gerät. Wenn Sie eine Netzwerkschnittstelle verwenden und zu DHCP wechseln, gibt es keine Möglichkeit, die DHCP-Adresse zu bestimmen. Wenn Sie zu einer DHCP-Adresse wechseln möchten, warten Sie, bis sich das Gerät im Dienst aktiviert hat, und nehmen Sie dann die Änderung vor. Sie können dann die IPs aller Adapter im Azure-Portal in den **Geräteeigenschaften** für Ihren Dienst anzeigen.


    Nachdem Sie die Netzwerkeinstellungen konfiguriert und angewendet haben, können Sie **Weiter: Compute** auswählen, um das Computenetzwerk zu konfigurieren.

## <a name="enable-compute-network"></a>Computenetzwerk aktivieren

Befolgen Sie diese Schritte, um Compute zu aktivieren und das Computenetzwerk zu konfigurieren. 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. Wählen Sie auf der Seite **Compute** eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. 

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Wählen Sie im Dialogfeld **Netzwerkeinstellungen** die Option **Aktivieren** aus. Wenn Sie Compute aktivieren, wird auf Ihrem Gerät an dieser Netzwerkschnittstelle ein virtueller Switch erstellt. Der virtuelle Switch wird für die Compute-Infrastruktur auf dem Gerät verwendet. 
    
1. Weisen Sie **IP-Adressen für Kubernetes-Knoten** zu. Diese statischen IP-Adressen gelten für die Compute-VM.  

    Für ein Gerät mit *n*-Knoten wird ein zusammenhängender Bereich von mindestens *n+1* IPv4-Adressen für die Compute-VM unter Verwendung der Start- und End-IP-Adresse bereitgestellt. Da es sich bei Azure Stack Edge um ein Gerät mit einem Knoten handelt, werden mindestens zwei zusammenhängende IPv4-Adressen bereitgestellt.

    > [!IMPORTANT]
    > Kubernetes in Azure Stack Edge verwendet das Subnetz 172.27.0.0/16 für den Pod und das Subnetz 172.28.0.0/16 für den Dienst. Stellen Sie sicher, dass diese in Ihrem Netzwerk nicht verwendet werden. Werden diese Subnetze bereits in Ihrem Netzwerk verwendet, können Sie sie ändern, indem Sie das Cmdlet `Set-HcsKubeClusterNetworkInfo` über die PowerShell-Schnittstelle des Geräts ausführen. Weitere Informationen finden Sie unter [Ändern des Pod- und Dienstsubnetzes in Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Weisen Sie **externe Dienst-IP-Adressen für Kubernetes** zu. Dies sind auch die IP-Adressen für den Lastenausgleich. Diese zusammenhängenden IP-Adressen sind für Dienste, die Sie außerhalb des Kubernetes-Clusters verfügbar machen möchten. Sie legen den statischen IP-Adressenbereich abhängig von der Anzahl der verfügbar gemachten Dienste fest. 
    
    > [!IMPORTANT]
    > Es wird dringend empfohlen, mindestens eine IP-Adresse für den Azure Stack Edge Pro-Hubdienst anzugeben, um auf Computemodule zugreifen zu können. Sie können dann optional zusätzliche IP-Adressen für andere Dienste/IoT Edge-Module (eine pro Dienst/Modul) angeben, auf die von außerhalb des Clusters zugegriffen werden muss. Die IP-Adressen des Diensts können später aktualisiert werden. 
    
1. Wählen Sie **Übernehmen**.

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Es dauert einige Minuten, bis die Konfiguration übernommen wurde. Möglicherweise müssen Sie den Browser aktualisieren. Wie Sie sehen, ist der angegebene Port für Compute aktiviert. 
 
    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Klicken Sie auf **Weiter: Webproxy**, um den Webproxy zu konfigurieren.  

  
## <a name="configure-web-proxy"></a>Konfigurieren des Webproxys

Dies ist eine optionale Konfiguration.

> [!IMPORTANT]
> * Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Pro-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. NTLM wird nicht unterstützt.
> * Dateien für die automatische Proxykonfiguration (Proxy Auto Config, PAC) werden nicht unterstützt. Eine PAC-Datei definiert, wie Webbrowser und andere Benutzer-Agents automatisch den entsprechenden Proxyserver (Zugriffsmethode) zum Abrufen einer bestimmten URL auswählen können. 
> * Transparente Proxys funktionieren gut mit Azure Stack Edge Pro. Für nicht transparente Proxys, die den gesamten Datenverkehr (über ihre eigenen auf dem Proxyserver installierten Zertifikate) abfangen und lesen, laden Sie den öffentlichen Schlüssel des Proxyzertifikats als Signaturkette auf Ihrem Azure Stack Edge Pro-Gerät hoch. Anschließend können Sie die Proxyservereinstellungen auf Ihrem Azure Stack Edge-Gerät konfigurieren. Weitere Informationen finden Sie unter [Bereitstellen eigener Zertifikate](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).  

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. Führen Sie auf der Seite **Webproxyeinstellungen** die folgenden Schritte aus:

    1. Geben Sie im Feld **Webproxy-URL** die URL im folgenden Format ein: `http://host-IP address or FQDN:Port number`. HTTPS-URLs werden nicht unterstützt.

    2. Klicken Sie unter **Authentifizierung** auf **Keine** oder **NTLM**. Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Pro-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. **NTLM** wird nicht unterstützt.

    3. Falls Sie eine Authentifizierung verwenden, geben Sie einen Benutzernamen und ein Kennwort ein.

    4. Klicken Sie auf **Anwenden**, um die konfigurierten Webproxyeinstellungen zu überprüfen und anzuwenden.
    
   ![Seite „Webproxyeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Nachdem die Einstellungen angewendet wurden, wählen Sie **Weiter: Gerät** ein.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


Informationen zum Einrichten Ihres Azure Stack Edge Pro-Geräts finden Sie unter:

> [!div class="nextstepaction"]
> [Konfigurieren von Geräteeinstellungen](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
