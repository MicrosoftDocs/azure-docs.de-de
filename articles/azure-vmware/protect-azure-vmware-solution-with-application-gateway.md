---
title: Verwenden von Azure Application Gateway zum Schützen Ihrer Web-Apps in Azure VMware Solution
description: Konfigurieren Sie Azure Application Gateway, um Ihre in Azure VMware Solution ausgeführten Web-Apps sicher verfügbar zu machen.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: fdef37bd76b08a8778db8401a1e8a0406c2ed652
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988638"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Verwenden von Azure Application Gateway zum Schützen Ihrer Web-Apps in Azure VMware Solution

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) ist ein Lastenausgleich für Webdatenverkehr auf Schicht 7, mit dem Sie in Ihre Webanwendungen eingehenden Datenverkehr verwalten können. Er wird sowohl in Azure VMware Solution v1.0 als auch v2.0 angeboten. Beide Versionen wurden mit Web-Apps getestet, die in Azure VMware Solution ausgeführt werden.

Die Funktionen sind: 
- Cookiebasierte Sitzungsaffinität
- URL-basiertes Routing
- Web Application Firewall (WAF)

Eine umfassende Liste der Features finden Sie unter [Azure Application Gateway-Funktionen](../application-gateway/features.md). 

In diesem Artikel erfahren Sie, wie Sie Application Gateway vor einer Webserverfarm verwenden, um eine Web-App zu schützen, die in Azure VMware Solution ausgeführt wird. 

## <a name="topology"></a>Topologie
Das Diagramm zeigt, wie Application Gateway verwendet wird, um virtuelle Azure-IaaS-Computer (VMs), Azure-VM-Skalierungsgruppen oder lokale Server zu schützen. Application Gateway behandelt Azure VMware Solution-VMs als lokale Server. 

![Diagramm, das zeigt, wie Application Gateway virtuelle Azure-IaaS-Computer (VMs), Azure-VM-Skalierungsgruppen oder lokale Server schützt.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway ist derzeit die einzige unterstützte Methode, um Web-Apps verfügbar zu machen, die auf Azure VMware Solution-VMs ausgeführt werden.

Das Diagramm zeigt das Testszenario zum Überprüfen von Application Gateway mit Azure VMware Solution-Webanwendungen.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagramm mit dem Testszenario zum Überprüfen von Application Gateway mit Azure VMware Solution-Webanwendungen." border="false":::

Die Application Gateway-Instanz wird auf dem Hub in einem dedizierten Subnetz bereitgestellt. Sie hat eine öffentliche Azure-IP-Adresse. Es wird empfohlen, den DDoS-Standardschutz für das virtuelle Netzwerk zu aktivieren. Der Webserver wird in einer privaten Azure VMware Solution-Cloud hinter NSX T0- und T1-Routern gehostet. Azure VMware Solution aktiviert die Kommunikation mit dem Hub und den lokalen Systemen mit [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement.
- Eine bereitgestellte und ausgeführte private Azure VMware Solution-Cloud.

## <a name="deployment-and-configuration"></a>Bereitstellung und Konfiguration

1. Suchen Sie im Azure-Portal nach **Application Gateway**, und wählen Sie **Anwendungsgateway erstellen** aus.

2. Geben Sie die grundlegenden Details wie in der folgenden Abbildung an, und wählen Sie dann **Weiter: Front-Ends>** . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Screenshot der Seite „Application Gateway erstellen“ im Azure-Portal.":::

3. Wählen Sie den IP-Adresstyp für das Front-End aus. Wählen Sie für den Typ „Öffentlich“ eine vorhandene öffentliche IP-Adresse aus, oder erstellen Sie eine neue. Klicken Sie auf **Weiter: Back-Ends>** .

    > [!NOTE]
    > Für private Front-Ends werden nur die SKUs „Standard“ und „Web Application Firewall (WAF)“ unterstützt.

4. Fügen Sie einen Back-End-Pool der virtuellen Computer hinzu, die in der Azure VMware Solution-Infrastruktur ausgeführt werden. Geben Sie die Details der in der privaten Azure VMware Solution-Cloud ausgeführten Webserver an, und wählen Sie **Hinzufügen** aus.  Wählen Sie anschließend **Next: Konfiguration>** .

1. Wählen Sie auf der Registerkarte **Konfiguration** die Option **Routingregel hinzufügen** aus.

6. Geben Sie auf der Registerkarte **Listener** die Details für den Listener an. Wenn HTTPS ausgewählt wurde, müssen Sie entweder ein Zertifikat aus einer PFX-Datei oder ein vorhandenes Zertifikat von Azure Key Vault bereitstellen. 

7. Wählen Sie die Registerkarte **Back-End-Ziele** und dann das zuvor erstellte Back-End aus. Wählen Sie für das Feld **HTTP-Einstellungen** die Option **Neu hinzufügen** aus.

8. Konfigurieren Sie die Parameter für die HTTP-Einstellungen. Wählen Sie **Hinzufügen**.

9. Wenn Sie mehrere pfadbasierte Regeln konfigurieren möchten, wählen Sie **Fügen Sie mehrere Ziele zum Erstellen einer pfadbasierten Regel hinzu** aus. 

10. Fügen Sie eine pfadbasierte Regel hinzu, und wählen Sie **Hinzufügen** aus. Wiederholen Sie diese Schritte zum Hinzufügen weiterer pfadbasierter Regeln. 

11. Wenn Sie mit dem Hinzufügen von pfadbasierten Regeln fertig sind, wählen Sie erneut **Hinzufügen** und dann **Weiter: Tags>** aus. 

12. Fügen Sie Tags hinzu, und wählen Sie **Weiter: Überprüfen und erstellen>** aus.

13. Auf der Application Gateway-Instanz wird eine Überprüfung ausgeführt. Wenn sie erfolgreich ist, wählen Sie **Erstellen** zum Bereitstellen aus.

## <a name="configuration-examples"></a>Konfigurationsbeispiele

In diesem Abschnitt erfahren Sie, wie Sie Application Gateway mit Azure VMware Solution-VMs als Back-End-Pools für diese Anwendungsfälle konfigurieren: 

- [Hosten mehrerer Websites](#hosting-multiple-sites)
- [Routing per URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hosten mehrerer Websites

Hier lernen Sie die Vorgehensweise zum Definieren von Back-End-Adresspools mithilfe von VMs kennen, die in einer privaten Azure VMware Solution-Cloud auf einem vorhandenen Anwendungsgateway ausgeführt werden. 

>[!NOTE]
>Diese Vorgehensweise setzt voraus, dass Sie mehrere Domänen besitzen, und als Beispiele werden „www.contoso.com“ und „www.fabrikam.com“ verwendet.


1. Erstellen Sie in Ihrer privaten Cloud zwei unterschiedliche VM-Pools. Einer stellt Contoso und der zweite Fabrikam dar. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Screenshot, der die Zusammenfassung der Details eines Webservers im vSphere-Client anzeigt.":::

    Wir verwenden in diesem Tutorial Windows Server 2016 mit installierter IIS-Rolle (Internetinformationsdienste) zur Veranschaulichung. Nachdem die virtuellen Computer installiert wurden, führen Sie die folgenden PowerShell-Befehle aus, um IIS auf den einzelnen VMs zu konfigurieren. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Wählen Sie in einer vorhandenen Application Gateway-Instanz im Menü links **Back-End-Pools** aus, wählen Sie **Hinzufügen** aus, und geben Sie die Details der neuen Pools ein. Wählen Sie im rechten Bereich **Hinzufügen** aus.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Screenshot der Seite „Back-End-Pools“ zum Hinzufügen von Back-End-Pools." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. Erstellen Sie im Abschnitt **Listener** einen neuen Listener für jede Website. Geben Sie die Details für jeden Listener ein, und wählen Sie **Hinzufügen** aus.

4. Wählen Sie links **HTTP-Einstellungen** aus, und wählen Sie im linken Bereich **Hinzufügen** aus. Geben Sie die Details ein, um eine neue HTTP-Einstellung zu erstellen, und wählen Sie **Speichern** aus.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Screenshot der Seite „HTTP-Einstellungen“ zum Erstellen einer neuen HTTP-Einstellung." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Erstellen Sie die Regeln im linken Menü im Abschnitt **Regeln**. Ordnen Sie jede Regel dem entsprechenden Listener zu. Wählen Sie **Hinzufügen**.

6. Konfigurieren Sie den entsprechenden Back-End-Pool und die HTTP-Einstellungen. Wählen Sie **Hinzufügen**.

7. Testen Sie die Verbindung. Öffnen Sie Ihren bevorzugten Browser, und browsen Sie zu den verschiedenen Websites, die in Ihrer Azure VMware Solution-Umgebung gehostet werden, z. B. http://www.fabrikam.com.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Screenshot der Browserseite, auf der der erfolgreiche Test der Verbindung angezeigt wird.":::

### <a name="routing-by-url"></a>Routing per URL

Hier lernen Sie die Vorgehensweise zum Definieren von Back-End-Adresspools mithilfe von VMs kennen, die in einer privaten Azure VMware Solution-Cloud auf einem vorhandenen Anwendungsgateway ausgeführt werden. Anschließend erstellen Sie Routingregeln, die sicherstellen, dass Webdatenverkehr an die richtigen Server in den Pools gesendet wird.

1. Erstellen Sie in Ihrer privaten Cloud einen VM-Pool, um die Webfarm darzustellen. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Screenshot der Seite im vSphere-Client, die die Zusammenfassung eines anderen virtuellen Computers anzeigt.":::

    Zur Veranschaulichung wurde in diesem Tutorial Windows Server 2016 mit installierter IIS-Rolle verwendet. Nachdem die virtuellen Computer installiert wurden, führen Sie die folgenden PowerShell-Befehle aus, um IIS für jedes VM-Tutorial zu konfigurieren. 

    Auf dem ersten virtuellen Computer (contoso-web-01) wird die Hauptwebsite gehostet.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    Auf dem zweiten virtuellen Computer (contoso-web-02) wird die Website für die Bilder gehostet.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    Auf dem dritten virtuellen Computer (contoso-web-03) wird die Website für die Videos gehostet.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Fügen Sie drei neue Back-End-Pools in einer vorhandenen Application Gateway-Instanz hinzu. 

   1. Klicken Sie im Menü auf der linken Seite auf **Back-End-Pools**. 
   1. Wählen Sie **Hinzufügen** aus, und geben Sie die Details des ersten Pools (**contoso-web**) ein. 
   1. Fügen Sie eine VM als Ziel hinzu. 
   1. Wählen Sie **Hinzufügen**. 
   1. Wiederholen Sie diesen Vorgang für **contoso-images** und **contoso-video**, wobei Sie jeweils eine andere VM als Ziel hinzufügen. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Screenshot der Seite „Back-End-Pools“ mit der Addition von drei neuen Back-End-Pools." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. Erstellen Sie im Abschnitt **Listener** einen neuen Listener vom Typ Basic an Port 8080.

4. Wählen Sie im linken Navigationsbereich **HTTP-Einstellungen** aus, und wählen Sie im linken Bereich **Hinzufügen** aus. Geben Sie die Details ein, um eine neue HTTP-Einstellung zu erstellen, und wählen Sie **Speichern** aus.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Screenshot der Seite „HTTP-Einstellung hinzufügen“ mit der Konfiguration der HTTP-Einstellungen.":::

5. Erstellen Sie die Regeln im linken Menü im Abschnitt **Regeln**. Ordnen Sie jede Regel dem zuvor erstellten Listener zu. Konfigurieren Sie dann den Haupt-Back-End-Pool und die HTTP-Einstellungen. Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Screenshot der Seite „Hinzufügen einer Routingregel“ zum Konfigurieren von Routingregeln für ein Back-End-Ziel.":::

6. Testen Sie die Konfiguration. Greifen Sie im Azure-Portal auf das Anwendungsgateway zu, und kopieren Sie die öffentliche IP-Adresse im Abschnitt **Übersicht**. 

   1. Öffnen Sie ein neues Browserfenster, und geben Sie die URL `http://<app-gw-ip-address>:8080` ein. 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Screenshot der Browserseite, auf der der erfolgreiche Test der Konfiguration angezeigt wird.":::

   1. Ändern Sie die URL in `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Screenshot eines anderen erfolgreichen Tests mit der neuen URL.":::

   1. Ändern Sie die URL erneut in `http://<app-gw-ip-address>:8080/video/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Screenshot eines erfolgreichen Tests mit der endgültigen URL.":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit Application Gateway zum Schutz einer in Azure VMware Solution ausgeführten Web-App beschäftigt haben, möchten Sie möglicherweise mehr über Folgendes erfahren:

- [Konfigurieren von Azure Application Gateway für verschiedene Szenarien](../application-gateway/configuration-overview.md)
- [Bereitstellen von Traffic Manager zum Ausgleichen von Azure VMware Solution-Workloads](deploy-traffic-manager-balance-workloads.md)
- [Integrieren von Azure NetApp Files und auf Azure VMware Solution basierenden Workloads](netapp-files-with-azure-vmware-solution.md)
