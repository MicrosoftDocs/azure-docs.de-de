---
title: Tutorial zum Konfigurieren der Netzwerkeinstellungen für ein Azure Stack Edge Pro R-Gerät im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Pro R erfahren Sie, wie Sie Netzwerk-, Computenetzwerk- und Webproxyeinstellungen für Ihr physisches Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ad0010a1885c1e9bac8fa2341771cedc12115adc
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464016"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Tutorial: Konfigurieren des Netzwerks für Azure Stack Edge Pro R

In diesem Tutorial erfahren Sie, wie Sie das Netzwerk für Ihr Azure Stack Edge Pro R-Gerät über die lokale Webbenutzeroberfläche konfigurieren.

Der Verbindungsvorgang dauert ca. 20 Minuten.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Pro R-Gerät konfigurieren und einrichten:

* Sie haben das physische Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-install.md) installiert.
* Sie haben eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts hergestellt, wie unter [Tutorial: Herstellen einer Verbindung mit Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-connect.md) beschrieben.


## <a name="configure-network"></a>Netzwerk konfigurieren

Auf der Seite **Erste Schritte** werden die verschiedenen Einstellungen angezeigt, die zum Konfigurieren und Registrieren des physischen Geräts beim Azure Stack Edge-Dienst erforderlich sind. 

Führen Sie diese Schritte aus, um das Netzwerk für Ihr Gerät zu konfigurieren.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Erste Schritte**. 

2. Wählen Sie auf der Kachel **Netzwerk** die Option **Konfigurieren** aus, um zur Seite **Netzwerk** zu gelangen. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Auf Ihrem physischen Gerät stehen vier Netzwerkschnittstellen zur Verfügung. PORT 1 und PORT 2 sind 1-Gbit/s-Netzwerkschnittstellen. PORT 3 und PORT 4 sind 10/25-Gbit/s-Netzwerkschnittstellen. PORT 1 wird automatisch als reiner Verwaltungsport konfiguriert, während es sich bei PORT 2 bis PORT 4 jeweils um Datenports handelt. Die Seite **Netzwerk** sieht wie folgt aus:
    
    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Um die Netzwerkeinstellungen zu ändern, wählen Sie einen Port aus. Ändern Sie im eingeblendeten rechten Bereich die IP-Adresse, das Subnetz, das Gateway, das primäre DNS und das sekundäre DNS. 

    - Wenn Sie Port 1 auswählen, sehen Sie, dass er als statisch vorkonfiguriert ist. 

        ![Seite „Netzwerkeinstellungen für Port 1“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Die Ports 2, 3 und Port 4 werden standardmäßig mit DHCP konfiguriert.

        ![Seite „Netzwerkeinstellungen für Port 3“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Beachten Sie Folgendes, wenn Sie Netzwerkeinstellungen konfigurieren:

   * Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert. IP-Adresse, Subnetz, Gateway und DNS werden automatisch zugewiesen.
   * Wenn DHCP nicht aktiviert ist, können Sie bei Bedarf statische IP-Adressen zuweisen.
   * Sie können die Netzwerkschnittstelle als IPv4 konfigurieren.
   * Die Seriennummer eines beliebigen Ports entspricht der Seriennummer des Knotens.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Sobald das Gerätenetz konfiguriert ist, wird die Seite wie unten dargestellt aktualisiert.

    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Wir empfehlen, die lokale IP-Adresse der Netzwerkschnittstelle nicht von statisch auf DHCP umzustellen, es sei denn, Sie haben eine andere IP-Adresse für die Verbindung zum Gerät. Wenn Sie eine Netzwerkschnittstelle verwenden und zu DHCP wechseln, gibt es keine Möglichkeit, die DHCP-Adresse zu bestimmen. Wenn Sie zu einer DHCP-Adresse wechseln möchten, warten Sie, bis sich das Gerät im Dienst aktiviert hat, und nehmen Sie dann die Änderung vor. Sie können dann die IPs aller Adapter im Azure-Portal in den **Geräteeigenschaften** für Ihren Dienst anzeigen.


    Nachdem Sie die Netzwerkeinstellungen konfiguriert und angewendet haben, können Sie **Weiter: Compute** auswählen, um das Computenetzwerk zu konfigurieren.

## <a name="enable-compute-network"></a>Computenetzwerk aktivieren

Befolgen Sie diese Schritte, um Compute zu aktivieren und das Computenetzwerk zu konfigurieren. 

1. Wählen Sie auf der Seite **Compute** eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. 

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Wählen Sie im Dialogfeld **Netzwerkeinstellungen** die Option **Aktivieren** aus. Wenn Sie Compute aktivieren, wird auf Ihrem Gerät an dieser Netzwerkschnittstelle ein virtueller Switch erstellt. Der virtuelle Switch wird für die Compute-Infrastruktur auf dem Gerät verwendet. 
    
1. Weisen Sie **IP-Adressen für Kubernetes-Knoten** zu. Diese statischen IP-Adressen gelten für die Compute-VM.  

    Für ein Gerät mit *n*-Knoten wird ein zusammenhängender Bereich von mindestens *n+1* IPv4-Adressen für die Compute-VM unter Verwendung der Start- und End-IP-Adresse bereitgestellt. Da es sich bei Azure Stack Edge um ein Gerät mit einem Knoten handelt, werden mindestens zwei zusammenhängende IPv4-Adressen bereitgestellt. Diese IP-Adressen müssen sich in dem Netzwerk befinden, in dem Sie Compute aktiviert haben und in dem der virtuelle Switch erstellt wurde.

    > [!IMPORTANT]
    > Kubernetes in Azure Stack Edge verwendet das Subnetz 172.27.0.0/16 für den Pod und das Subnetz 172.28.0.0/16 für den Dienst. Stellen Sie sicher, dass diese in Ihrem Netzwerk nicht verwendet werden. Werden diese Subnetze bereits in Ihrem Netzwerk verwendet, können Sie sie ändern, indem Sie das Cmdlet `Set-HcsKubeClusterNetworkInfo` über die PowerShell-Schnittstelle des Geräts ausführen. Weitere Informationen finden Sie unter [Ändern des Pod- und Dienstsubnetzes in Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Weisen Sie **externe Dienst-IP-Adressen für Kubernetes** zu. Dies sind auch die IP-Adressen für den Lastenausgleich. Diese zusammenhängenden IP-Adressen sind für Dienste, die Sie außerhalb des Kubernetes-Clusters verfügbar machen möchten. Sie legen den statischen IP-Adressenbereich abhängig von der Anzahl der verfügbar gemachten Dienste fest. 
    
    > [!IMPORTANT]
    > Es wird dringend empfohlen, mindestens eine IP-Adresse für den Azure Stack Edge Pro R-Hubdienst anzugeben, um auf Computemodule zugreifen zu können. Sie können dann optional zusätzliche IP-Adressen für andere Dienste/IoT Edge-Module (eine pro Dienst/Modul) angeben, auf die von außerhalb des Clusters zugegriffen werden muss. Die IP-Adressen des Diensts können später aktualisiert werden. 
    
1. Wählen Sie **Übernehmen**.

    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Es dauert einige Minuten, bis die Konfiguration übernommen wurde. Möglicherweise müssen Sie den Browser aktualisieren. Wie Sie sehen, ist der angegebene Port für Compute aktiviert. 
 
    ![Seite „Compute“ auf der lokalen Benutzeroberfläche 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Klicken Sie auf **Weiter: Webproxy**, um den Webproxy zu konfigurieren.  

  
## <a name="configure-web-proxy"></a>Konfigurieren des Webproxys

Dies ist eine optionale Konfiguration.

> [!IMPORTANT]
> * Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Pro R-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. NTLM wird nicht unterstützt.
>* Dateien für die automatische Proxykonfiguration (Proxy Auto Config, PAC) werden nicht unterstützt. Eine PAC-Datei definiert, wie Webbrowser und andere Benutzer-Agents automatisch den entsprechenden Proxyserver (Zugriffsmethode) zum Abrufen einer bestimmten URL auswählen können. Proxys, die versuchen, den gesamten Datenverkehr abzufangen und zu lesen (und anschließend alles mit eigener Zertifizierung neu zu signieren), sind nicht kompatibel, da das Zertifikat des Proxys nicht vertrauenswürdig ist. In der Regel funktionieren transparente Proxys gut mit Azure Stack Edge Pro R. Nicht transparente Webproxys werden nicht unterstützt.


1. Führen Sie auf der Seite **Webproxyeinstellungen** die folgenden Schritte aus:

    1. Geben Sie im Feld **Webproxy-URL** die URL im folgenden Format ein: `http://host-IP address or FQDN:Port number`. HTTPS-URLs werden nicht unterstützt.

    2. Klicken Sie unter **Authentifizierung** auf **Keine** oder **NTLM**. Wenn Sie Compute aktivieren und ein IoT Edge-Modul auf Ihrem Azure Stack Edge Pro R-Gerät verwenden, sollten Sie für „Webproxyauthentifizierung“ die Option **Keine** festlegen. **NTLM** wird nicht unterstützt.

    3. Falls Sie eine Authentifizierung verwenden, geben Sie einen Benutzernamen und ein Kennwort ein.

    4. Klicken Sie auf **Anwenden**, um die konfigurierten Webproxyeinstellungen zu überprüfen und anzuwenden.
    
   ![Seite „Webproxyeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Nachdem die Einstellungen angewendet wurden, wählen Sie **Weiter: Gerät** ein.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Voraussetzungen
> * Netzwerk konfigurieren
> * Computenetzwerk aktivieren
> * Konfigurieren des Webproxys


Informationen zum Einrichten Ihres Azure Stack Edge Pro R-Geräts finden Sie hier:

> [!div class="nextstepaction"]
> [Konfigurieren von Geräteeinstellungen](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
