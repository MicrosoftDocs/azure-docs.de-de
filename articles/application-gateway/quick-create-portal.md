---
title: 'Schnellstart: Weiterleiten von Webdatenverkehr mit dem Portal'
titleSuffix: Azure Application Gateway
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie das Azure-Portal zum Erstellen einer Azure Application Gateway-Instanz verwenden, mit der Webdatenverkehr an virtuelle Computer in einem Back-End-Pool geleitet wird.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 12/08/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 42701fbcee9833fd31fff3ace55d48079015dbcd
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906402"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um ein Anwendungsgateway zu erstellen. Anschließend testen Sie es, um sicherzustellen, dass es ordnungsgemäß funktioniert. 

Das Anwendungsgateway leitet den Webdatenverkehr Ihrer Anwendungen an bestimmte Ressourcen in einem Back-End-Pool weiter. Sie weisen den Ports Listener zu, erstellen Regeln und fügen Ressourcen zu einem Back-End-Pool hinzu. Der Einfachheit halber wird in diesem Artikel ein einfaches Setup mit einer öffentlichen Front-End-IP-Adresse, einem grundlegenden Listener zum Hosten einer einzelnen Website auf diesem Anwendungsgateway, einer Routingregel für grundlegende Anforderungen und zwei virtuellen Computern im Back-End-Pool verwendet.

Diese Schnellstartanleitung steht auch für [Azure PowerShell](quick-create-powershell.md) und für die [Azure-Befehlszeilenschnittstelle](quick-create-cli.md) zur Verfügung.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Das Anwendungsgateway wird über die Registerkarten der Seite **Application Gateway erstellen** erstellt.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Das Fenster **Neu** wird angezeigt.

2. Klicken Sie auf **Netzwerk**, und wählen Sie dann in der Liste **Ausgewählte** die Option **Application Gateway** aus.

### <a name="basics-tab"></a>Registerkarte „Grundlagen“

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte für die Einstellungen des Anwendungsgateways ein:

   - **Ressourcengruppe**: Wählen Sie **myResourceGroupAG** als Ressourcengruppe aus. Falls diese Gruppe nicht vorhanden ist, wählen Sie **Neue erstellen** aus, um sie zu erstellen.
   - **Name des Anwendungsgateways**: Geben Sie *myAppGateway* als Namen des Anwendungsgateways ein.

     ![Erstellen eines neuen Anwendungsgateways: Grundlagen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Für die Kommunikation in Azure zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. Sie können ein neues virtuelles Netzwerk erstellen oder ein bereits vorhandenes virtuelles Netzwerk auswählen. In diesem Beispiel erstellen Sie gleichzeitig mit dem Anwendungsgateway ein virtuelles Netzwerk. Application Gateway-Instanzen werden in separaten Subnetzen erstellt. In diesem Beispiel erstellen Sie zwei Subnetze: eins für das Anwendungsgateway und eins für die Back-End-Server.

    > [!NOTE]
    > [Richtlinien für VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoint-policies-overview.md) werden derzeit in einem Application Gateway-Subnetz nicht unterstützt.

    Erstellen Sie unter **Virtuelles Netzwerk konfigurieren** ein neues virtuelles Netzwerk, indem Sie **Neu erstellen** auswählen. Geben Sie im Fenster **Virtuelles Netzwerk erstellen**, das geöffnet wird, die folgenden Werte ein,um das virtuelle Netzwerk und zwei Subnetze zu erstellen:

    - **Name**: Geben Sie *myVNet* als Namen des virtuellen Netzwerks ein.

    - **Subnetzname** (Application Gateway-Subnetz): Im Raster **Subnetze** wird ein Subnetz namens *Default* angezeigt. Ändern Sie den Namen dieses Subnetzes in *myAGSubnet*.<br>Das Subnetz für das Anwendungsgateway kann nur Anwendungsgateways enthalten. Andere Ressourcen sind nicht zulässig.

    - **Subnetzname** (Subnetz für Back-End-Server): Geben Sie in der zweiten Zeile des Rasters **Subnetze** in der Spalte *Subnetzname* den Namen **myBackendSubnet** ein.

    - **Adressbereich** (Subnetz für Back-End-Server): Geben Sie in der zweiten Zeile des Rasters **Subnetze** einen Adressbereich ein, der sich nicht mit dem Adressbereich des Subnetzes *myAGSubnet* überschneidet. Wenn der Adressbereich von *myAGSubnet* beispielsweise 10.0.0.0/24 lautet, geben Sie *10.0.1.0/24* als Adressbereich für *myBackendSubnet* ein.

    Wählen Sie **OK** aus, um das Fenster **Virtuelles Netzwerk erstellen** zu schließen und die Einstellungen für das virtuelle Netzwerk zu speichern.

     ![Erstellen eines neuen Anwendungsgateways: virtuelles Netzwerk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Übernehmen Sie auf der Registerkarte **Grundlagen** die Standardwerte für die übrigen Einstellungen, und wählen Sie dann **Weiter: Front-Ends** aus.

### <a name="frontends-tab"></a>Registerkarte „Front-Ends“

1. Vergewissern Sie sich auf der Registerkarte **Front-Ends**, dass der **Typ der Front-End-IP-Adresse** auf **Öffentlich** festgelegt ist. <br>Je nach Anwendungsfall können Sie konfigurieren, dass die Front-End-IP-Adresse öffentlich oder privat ist. In diesem Beispiel verwenden Sie eine öffentliche Front-End-IP-Adresse.
   > [!NOTE]
   > Für die Application Gateway v2-SKU muss eine **öffentliche** Front-End-IP-Konfiguration vorhanden sein. Sie können weiterhin eine Kombination aus öffentlicher und privater Front-End-IP-Konfiguration verwenden. Für die Version 2 der SKU kann derzeit jedoch keine rein private Front-End-IP-Konfiguration (nur ILB-Modus) verwendet werden. 

2. Wählen Sie **Neu erstellen** für die **Öffentliche IP-Adresse** aus, und geben Sie *myAGPublicIPAddress* als Namen der öffentlichen IP-Adresse ein. Wählen Sie dann **OK** aus. 

     ![Erstellen eines neuen Anwendungsgateways: Front-Ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Klicken Sie auf **Weiter: Back-Ends**.

### <a name="backends-tab"></a>Registerkarte „Back-Ends“

Der Back-End-Pool wird zum Weiterleiten von Anforderungen an die Back-End-Server verwendet, die die Anforderung verarbeiten. Back-End-Pools können Netzwerkkarten, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) und Back-Ends mit mehreren Mandanten wie Azure App Service umfassen. In diesem Beispiel erstellen Sie mit Ihrem Anwendungsgateway einen leeren Back-End-Pool und fügen dann Back-End-Ziele zum Back-End-Pool hinzu.

1. Wählen Sie auf der Registerkarte **Back-Ends** die Option **+Back-End-Pool hinzufügen** aus.

2. Geben Sie im Fenster **Back-End-Pool hinzufügen**, das geöffnet wird, die folgenden Werte ein, um einen leeren Back-End-Pool zu erstellen:

    - **Name**: Geben Sie *myBackendPool* als Name des Back-End-Pools ein.
    - **Back-End-Pool ohne Ziele hinzufügen**: Wählen Sie **Ja** aus, um einen Back-End-Pool ohne Ziele zu erstellen. Back-End-Ziele werden Sie nach dem Erstellen des Anwendungsgateways hinzufügen.

3. Wählen Sie im Fenster **Back-End-Pool hinzufügen** die Option **Hinzufügen** aus, um die Konfiguration des Back-End-Pools zu speichern und zur Registerkarte **Back-Ends** zurückzukehren.

     ![Erstellen eines neuen Anwendungsgateways: Back-Ends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Wählen Sie auf der Registerkarte **Back-Ends** die Option **Weiter: Konfiguration** aus.

### <a name="configuration-tab"></a>Registerkarte „Konfiguration“

Auf der Registerkarte **Konfiguration** verbinden Sie das Front-End und den von Ihnen erstellten Back-End-Pool mithilfe einer Routingregel.

1. Wählen Sie **Regel hinzufügen** in der Spalte **Routingregeln** aus.

2. Geben Sie im Fenster **Routingregel hinzufügen**, das geöffnet wird, *myRoutingRule* als **Regelname** ein.

3. Eine Routingregel erfordert einen Listener. Geben Sie im Fenster **Routingregel hinzufügen** auf der Registerkarte **Listener** die folgenden Werte für den Listener ein:

    - **Name des Listeners**: Geben Sie *myListener* als Name für den Listener ein.
    - **Front-End-IP**: Wählen Sie **Öffentlich** aus, um die für das Front-End erstellte öffentliche IP-Adresse auszuwählen.
  
      Übernehmen Sie auf der Registerkarte **Listener** die Standardwerte für die übrigen Einstellungen. Wählen Sie dann die Registerkarte **Back-End-Ziele** aus, um den Rest der Routingregel zu konfigurieren.

   ![Erstellen eines neuen Anwendungsgateways: Listener](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Wählen Sie auf der Registerkarte **Back-End-Ziele** den Pool **myBackendPool** als **Back-End-Ziel** aus.

5. Wählen Sie für die **HTTP-Einstellung** die Option **Neu erstellen** aus, um eine neue HTTP-Einstellung zu erstellen. Die HTTP-Einstellung bestimmt das Verhalten der Routingregel. Geben Sie im Fenster **HTTP-Einstellung hinzufügen**, das geöffnet wird, *myHTTPSetting* als **Name der HTTP-Einstellung** und *80* als **Back-End-Port** ein. Übernehmen Sie im Fenster **HTTP-Einstellung hinzufügen** die Standardwerte für die übrigen Einstellungen, und wählen Sie dann **Hinzufügen** aus, um zum Fenster **Routingregel hinzufügen** zurückzukehren. 

     ![Erstellen eines neuen Anwendungsgateways: HTTP-Einstellung](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Wählen Sie im Fenster **Routingregel hinzufügen** die Option **Hinzufügen** aus, um die Routingregel zu speichern und zur Registerkarte **Konfiguration** zurückzukehren.

     ![Erstellen eines neuen Anwendungsgateways: Routingregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Klicken Sie auf **Weiter: Tags** und dann auf **Weiter: Überprüfen + erstellen**.

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Überprüfen Sie die Einstellungen auf der Registerkarte **Überprüfen und erstellen**, und wählen Sie dann **Erstellen** aus, um das virtuelle Netzwerk, die öffentliche IP-Adresse und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways in Azure kann einige Minuten in Anspruch nehmen. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

## <a name="add-backend-targets"></a>Hinzufügen von Back-End-Zielen

In diesem Beispiel verwenden Sie virtuelle Computer als Ziel-Back-End. Sie können entweder vorhandene virtuelle Computer verwenden oder neue erstellen. Sie erstellen zwei virtuelle Computer als Back-End-Server für das Anwendungsgateway.

Gehen Sie dazu wie folgt vor:

1. Erstellen von zwei neuen virtuellen Computern (*myVM* und *myVM2*) für die Verwendung als Back-End-Server
2. Installieren von IIS auf den virtuellen Computern, um zu überprüfen, ob die Application Gateway-Instanz erfolgreich erstellt wurde
3. Hinzufügen der Back-End-Server zum Back-End-Pool

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Das Fenster **Neu** wird angezeigt.
2. Wählen Sie dann in der Liste **Beliebt** die Option **Windows Server 2016 Datacenter** aus. Die Seite **Virtuellen Computer erstellen** wird angezeigt.<br>Application Gateway kann Datenverkehr an jeden beliebigen virtuellen Computer weiterleiten, der im Back-End-Pool verwendet wird. In diesem Beispiel verwenden Sie Windows Server 2016 Datacenter.
3. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte für die VM-Einstellungen ein:

    - **Ressourcengruppe**: Wählen Sie **myResourceGroupAG** als Namen der Ressourcengruppe aus.
    - **Name des virtuellen Computers**: Geben Sie *myVM* als Namen der VM ein.
    - **Region**: Wählen Sie die Region aus, in der Sie auch das Anwendungsgateway erstellt haben.
    - **Benutzername**: Geben Sie einen Benutzernamen für den Administrator ein.
    - **Kennwort**: Geben Sie ein Kennwort ein.
    - **Öffentliche Eingangsports**: Keine.
4. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Weiter: Datenträger**.  
5. Übernehmen Sie auf der Registerkarte **Datenträger** die Standardwerte, und klicken Sie auf **Weiter: Netzwerk**.
6. Vergewissern Sie sich auf der Registerkarte **Netzwerk**, dass **myVNet** für **Virtuelles Netzwerk** ausgewählt und **Subnetz** auf **myBackendSubnet** festgelegt ist. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Weiter: Verwaltung** aus.<br>Application Gateway kann mit Instanzen außerhalb des eigenen virtuellen Netzwerks kommunizieren, es muss jedoch sichergestellt werden, dass eine IP-Verbindung besteht.
7. Legen Sie auf der Registerkarte **Verwaltung** die Option **Startdiagnose** auf **Deaktivieren** fest. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Bewerten + erstellen**.
8. Überprüfen Sie auf der Registerkarte **Bewerten + erstellen** die Einstellungen, beheben Sie alle Validierungsfehler, und wählen Sie dann **Erstellen** aus.
9. Warten Sie, bis die Erstellung des virtuellen Computers abgeschlossen ist, bevor Sie fortfahren.

### <a name="install-iis-for-testing"></a>Installieren von IIS für Testzwecke

In diesem Beispiel installieren Sie IIS auf den virtuellen Computern nur, um zu überprüfen, ob Azure das Anwendungsgateway erfolgreich erstellt hat.

1. Öffnen Sie Azure PowerShell. Wählen Sie auf der oberen Navigationsleiste des Azure-Portals **Cloud Shell** und anschließend in der Dropdownliste **PowerShell** aus. 

    ![Installieren der benutzerdefinierten Erweiterung](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren. Ändern Sie bei Bedarf den Parameter *Location*: 

    ```azurepowershell
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Erstellen Sie eine zweite VM, und installieren Sie IIS mithilfe der zuvor ausgeführten Schritte. Verwenden Sie *myVM2* für den VM-Namen und für die Einstellung **VMName** des Cmdlets **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Hinzufügen von Back-End-Servern zu Back-End-Pools

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Ressourcen** aus, oder suchen Sie nach *Alle Ressourcen*, und wählen Sie die entsprechende Option aus. Wählen Sie anschließend **myAppGateway** aus.

2. Klicken Sie im Menü auf der linken Seite auf **Back-End-Pools**.

3. Wählen Sie **myBackendPool** aus.

4. Wählen Sie unter **Back-End-Ziele** > **Zieltyp** in der Dropdownliste die Option **Virtueller Computer** aus.

5. Wählen Sie unter **Ziel** die virtuellen Computer **myVM** und **myVM2** und in den Dropdownlisten die zugehörigen Netzwerkschnittstellen aus.

   > [!div class="mx-imgBorder"]
   > ![Hinzufügen der Back-End-Server](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wählen Sie **Speichern** aus.

7. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

IIS ist für die Erstellung des Anwendungsgateways zwar nicht erforderlich, wird in dieser Schnellstartanleitung aber installiert, um die erfolgreiche Erstellung des Anwendungsgateways durch Azure zu überprüfen. Testen des Anwendungsgateways mit IIS:

1. Suchen Sie auf der Seite **Übersicht** des Anwendungsgateways nach der öffentlichen IP-Adresse für das Anwendungsgateway.![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)Sie können auch **Alle Ressourcen** auswählen, *myAGPublicIPAddress* in das Suchfeld eingeben und den Eintrag dann in den Suchergebnissen auswählen. Azure zeigt die öffentliche IP-Adresse auf der Seite **Übersicht** an.
2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste Ihres Browsers ein, um zu dieser IP-Adresse zu navigieren.
3. Überprüfen Sie die Antwort. Eine gültige Antwort bestätigt, dass das Anwendungsgateway erfolgreich erstellt wurde und eine Verbindung mit dem Back-End herstellen kann.

   ![Testen des Anwendungsgateways](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Aktualisieren Sie den Browser mehrmals. Daraufhin sollten Verbindungen mit „myVM“ und „myVM2“ angezeigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dem Anwendungsgateway erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Wenn Sie die Ressourcengruppe löschen, werden auch das Anwendungsgateway und alle zugehörigen Ressourcen entfernt.

So löschen Sie die Ressourcengruppe:

1. Wählen Sie im Menü des Azure-Portals die Option **Ressourcengruppen** aus, oder suchen Sie nach *Ressourcengruppen*, und wählen Sie die entsprechende Option aus.
2. Suchen Sie auf der Seite **Ressourcengruppen** in der Liste nach **myResourceGroupAG**, und wählen Sie den Eintrag aus.
3. Wählen Sie auf der Seite für die **Ressourcengruppe** die Option **Ressourcengruppe löschen** aus.
4. Geben Sie *myResourceGroupAG* für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren eines Anwendungsgateways mit TLS-Terminierung mithilfe des Azure-Portals](create-ssl-portal.md)
