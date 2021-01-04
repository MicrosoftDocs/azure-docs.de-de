---
title: Verbinden mit und Konfigurieren und Aktivieren von Azure Data Box Gateway im Azure-Portal
description: Im dritten Tutorial zur Bereitstellung von Data Box Gateway erfahren Sie, wie Sie Ihr virtuelles Gerät verbinden, einrichten und aktivieren können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96580322"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Verbinden, Einrichten und Aktivieren von Azure Data Box Gateway

## <a name="introduction"></a>Einführung

In diesem Tutorial wird beschrieben, wie Sie sich mit Ihrem Data Box Gateway-Gerät über die lokale Webbenutzeroberfläche verbinden und wie Sie es einrichten und aktivieren. 

Der Einrichtungs- und Aktivierungsvorgang kann ca. 10 Minuten dauern. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit einem virtuellen Gerät
> * Einrichten und Aktivieren des virtuellen Geräts

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie Folgendes, bevor Sie Data Box Gateway konfigurieren und einrichten:

* Sie haben ein virtuelles Gerät bereitgestellt und eine damit verbundene URL abgerufen, wie unter [Bereitstellen eines Data Box Gateways in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) bzw. [Bereitstellen eines Data Box Gateways in VMware](data-box-gateway-deploy-provision-vmware.md) beschrieben.
* Sie haben den Aktivierungsschlüssel vom Data Box Gateway-Dienst erhalten, den Sie zur Verwaltung von Data Box Gateway-Geräten erstellt haben. Weitere Informationen finden Sie unter [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Herstellen einer Verbindung mit der lokalen Webbenutzeroberfläche 

1. Öffnen Sie ein Browserfenster, und greifen Sie auf die lokale Webbenutzeroberfläche des Geräts zu unter:
   
   https:\//ip-address-of-network-interface
   
   Verwenden Sie die Verbindungs-URL aus dem vorherigen Tutorial. Es wird eine Fehlermeldung oder eine Warnung mit dem Hinweis angezeigt, dass ein Problem mit dem Sicherheitszertifikat der Website vorliegt.

2. Klicken Sie auf **Mit dieser Webseite fortfahren**. Diese Schritte können sich je nach verwendetem Browser unterscheiden.
   
    ![Meldung zum Sicherheitszertifikatfehler auf der Website](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Melden Sie sich bei der Webbenutzeroberfläche des virtuellen Geräts an. Das Standardkennwort lautet *Password1*. 
   
    ![Anmelden bei der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Ändern Sie das Gerätekennwort in der Aufforderung. Das neue Kennwort muss zwischen acht und 16 Zeichen lang sein. Das Kennwort muss drei der folgenden Elemente enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.

    ![Ändern des Gerätekennworts](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Sie befinden sich jetzt im **Dashboard** Ihres Geräts.

## <a name="set-up-and-activate-the-virtual-device"></a>Einrichten und Aktivieren des virtuellen Geräts
 
Das Dashboard zeigt die verschiedenen Einstellungen an, die zum Konfigurieren und Registrieren des virtuellen Geräts beim Data Box Gateway-Dienst erforderlich sind. Der **Gerätename**, die **Netzwerkeinstellungen**, die **Webproxyeinstellungen** und die **Uhrzeiteinstellungen** sind optional. Die einzigen erforderlichen Einstellungen sind **Cloudeinstellungen**.
   
![Seite „Dashboard“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Klicken Sie im linken Bereich auf **Gerätename**, und geben Sie anschließend einen Anzeigenamen für Ihr Gerät ein. Der Anzeigename muss aus 1 bis 15 Zeichen bestehen und Buchstaben, Zahlen und Bindestriche enthalten. 

    ![Seite „Gerätename“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Optional) Klicken Sie im linken Bereich auf **Netzwerkeinstellungen**, und konfigurieren Sie die Einstellungen anschließend. Auf Ihrem virtuellen Gerät wird mindestens eine Netzwerkschnittstelle angezeigt, je nachdem, wie viele Sie in dem zugrunde liegenden virtuellen Computer konfiguriert haben. Die Seite **Netzwerkeinstellungen** für ein virtuelles Gerät mit aktivierter Netzwerkschnittstelle ist nachstehend abgebildet.
    
    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Beachten Sie Folgendes, wenn Sie Netzwerkeinstellungen konfigurieren:

    - Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert. IP-Adresse, Subnetz, Gateway und DNS werden automatisch zugewiesen.
    - Wenn DHCP nicht aktiviert ist, können Sie bei Bedarf statische IP-Adressen zuweisen.
    - Sie können die Netzwerkschnittstelle als IPv4 konfigurieren.

     >[!NOTE] 
     > Wir empfehlen, die lokale IP-Adresse der Netzwerkschnittstelle nicht von statisch auf DHCP umzustellen, es sei denn, Sie haben eine andere IP-Adresse für die Verbindung zum Gerät. Wenn Sie eine Netzwerkschnittstelle verwenden und zu DHCP wechseln, gibt es keine Möglichkeit, die DHCP-Adresse zu bestimmen. Wenn Sie zu einer DHCP-Adresse wechseln möchten, warten Sie, bis sich das Gerät beim Dienst registriert hat, und ändern Sie dann. Sie können dann die IPs aller Adapter im Azure-Portal in den **Geräteeigenschaften** für Ihren Dienst anzeigen.

3. (Optional) Konfigurieren Sie Ihren Webproxyserver. Die Webproxykonfiguration ist optional. Bei Verwendung eines Webproxys kann dieser jedoch nur auf dieser Seite konfiguriert werden.
   
   ![Seite „Webproxyeinstellungen“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Gehen Sie auf der Seite **Webproxy** folgendermaßen vor:
   
   1. Geben Sie im Feld **Webproxy-URL** die URL im folgenden Format ein: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS-URLs werden nicht unterstützt.
   2. Klicken Sie unter **Authentifizierung** auf **Keine** oder **NTLM**.
   3. Falls Sie eine Authentifizierung verwenden, geben Sie einen **Benutzernamen** und ein **Kennwort** ein.
   4. Klicken Sie auf **Anwenden**, um die konfigurierten Webproxyeinstellungen zu überprüfen und anzuwenden.

   > [!NOTE]
   > Dateien für die automatische Proxykonfiguration (Proxy Auto Config, PAC) werden nicht unterstützt. Eine PAC-Datei definiert, wie Webbrowser und andere Benutzer-Agents automatisch den entsprechenden Proxyserver (Zugriffsmethode) zum Abrufen einer bestimmten URL auswählen können.
   > Proxys, die versuchen, den gesamten Datenverkehr abzufangen und zu lesen (und anschließend alles mit eigener Zertifizierung neu zu signieren), sind nicht kompatibel, da das Zertifikat des Proxys nicht vertrauenswürdig ist.
   > In der Regel funktionieren transparente Proxys gut mit Azure Data Box Gateway.

4. (Optional) Klicken Sie im linken Bereich auf **Zeiteinstellungen**, und konfigurieren Sie die Zeitzone und die primären und sekundären NTP-Server für das Gerät. 

    NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät bei den Clouddienstanbietern authentifiziert werden kann.
    
    ![Seite „Zeiteinstellungen“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Gehen Sie auf der Seite **Zeiteinstellungen** folgendermaßen vor:
    
    1. Wählen Sie aus der Dropdownliste die **Zeitzone** aus, die dem geografischen Standort entspricht, an dem das Gerät bereitgestellt wird.
        Die Standardzeitzone für Ihr Gerät ist „PST“. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.

    2. Geben Sie einen **primären NTP-Server** für das Gerät an, oder übernehmen Sie den Standardwert `time.windows.com`.   
        Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt.

    3. Geben Sie optional im Feld **Sekundärer NTP-Server** einen sekundären Server für Ihr Gerät ein.

    4. Klicken Sie auf **Übernehmen**, um die konfigurierten Uhrzeiteinstellungen zu überprüfen und anzuwenden.

6. Klicken Sie im linken Bereich auf **Cloudeinstellungen**, und aktivieren Sie Ihr Gerät im Azure-Portal für den Data Box Gateway-Dienst.
    
    1. Geben Sie im Feld **Aktivierungsschlüssel** den **Aktivierungsschlüssel** ein, den Sie unter [Aktivierungsschlüssel abrufen](data-box-gateway-deploy-prep.md#get-the-activation-key) für Data Box Gateway abgerufen haben.

    2. Wählen Sie **Aktivieren** aus.
       
         ![Seite „Cloudeinstellungen“ der lokalen Webbenutzeroberfläche](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Das Gerät wird aktiviert und wichtige Updates, falls verfügbar, werden automatisch angewendet. Es wird eine entsprechende Benachrichtigung angezeigt. Den Fortschritt des Updates können Sie über das Azure-Portal überwachen.

        ![Seite „Cloudeinstellungen“ der lokalen Webbenutzeroberfläche 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Das Dialogfeld enthält außerdem einen Wiederherstellungsschlüssel, den Sie kopieren und an einem sicheren Ort speichern sollten. Dieser Schlüssel wird verwendet, um Ihre Daten wiederherzustellen, falls das Gerät nicht starten kann.**


    4. Möglicherweise müssen Sie einige Minuten warten, bis das Update erfolgreich abgeschlossen ist. Nachdem das Update abgeschlossen ist, melden Sie sich am Gerät an. Die Seite **Cloudeinstellungen** wird mit der Angabe aktualisiert, dass das Gerät erfolgreich aktiviert wurde.

        ![Seite „Cloudeinstellungen“ der lokalen Webbenutzeroberfläche aktualisiert](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Die Einrichtung des Geräts ist abgeschlossen. Sie können nun Freigaben auf Ihrem Gerät hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit einem virtuellen Gerät
> * Einrichten und Aktivieren des virtuellen Geräts

Weitere Informationen zum Übertragen von Daten mit Data Box Gateway finden Sie unter:

> [!div class="nextstepaction"]
> [Übertragen von Daten mit Data Box Gateway](./data-box-gateway-deploy-add-shares.md)
