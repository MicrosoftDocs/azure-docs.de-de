---
title: 'Tutorial: Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis – Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Portal Routingregeln auf URL-Pfadbasis für ein Anwendungsgateway und eine VM-Skalierungsgruppe erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134897"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Erstellen eines Anwendungsgateways mit pfadbasierten Routingregeln mithilfe des Azure-Portals

Sie können mit dem Azure-Portal [Routingregeln auf URL-Pfadbasis](url-route-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](overview.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von virtuellen Computern. Anschließend erstellen Sie Routingregeln, die sicherstellen, dass Webdatenverkehr an die richtigen Server in den Pools gesendet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Anwendungsgateways
> * Erstellen von virtuellen Computern für Back-End-Server
> * Erstellen von Back-End-Pools mit den Back-End-Servern
> * Erstellen eines Back-End-Listeners
> * Erstellen einer pfadbasierten Routingregel

![URL-Routingbeispiel](./media/create-url-route-portal/scenario.png)

Dieser Artikel kann auch mit der [Azure-Befehlszeilenschnittstelle](tutorial-url-route-cli.md) oder mit [Azure PowerShell](tutorial-url-route-powershell.md) durchgearbeitet werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. In diesem Beispiel werden zwei Subnetze erstellt: eins für das Anwendungsgateway und eins für die Back-End-Server. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway.

1. Wählen Sie in der linken oberen Ecke des Azure-Portals die Option **Neu** aus.
2. Klicken Sie auf **Netzwerk** und dann in der Liste der ausgewählten Elemente auf **Application Gateway**.
3. Geben Sie die folgenden Werte für das Anwendungsgateway ein:

   - *myAppGateway*: Name des Anwendungsgateways
   - *myResourceGroupAG*: neue Ressourcengruppe

     ![Erstellen eines neuen Anwendungsgateways](./media/create-url-route-portal/application-gateway-create.png)

4. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie dann auf **OK**.
5. Wählen Sie **Virtuelles Netzwerk auswählen** und dann **Neu erstellen** aus, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet*: Name des virtuellen Netzwerks
   - *10.0.0.0/16*: Adressraum des virtuellen Netzwerks
   - *myAGSubnet*: Subnetzname
   - *10.0.0.0/24*: Adressraum des Subnetzes

     ![Virtuelles Netzwerk erstellen](./media/create-url-route-portal/application-gateway-vnet.png)

6. Wählen Sie **OK** aus, um das virtuelle Netzwerk und das Subnetz zu erstellen.
7. Wählen Sie **Öffentliche IP-Adresse auswählen** und dann **Neu erstellen** aus, und geben Sie den Namen der öffentlichen IP-Adresse ein. In diesem Beispiel heißt die öffentliche IP-Adresse *myAGPublicIPAddress*. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie dann auf **OK**.
8. Übernehmen Sie die Standardwerte für die Listenerkonfiguration, lassen Sie die Web Application Firewall deaktiviert, und wählen Sie dann **OK** aus.
9. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK**, um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myVNet**.
2. Wählen Sie **Subnetze** und dann **Subnetz** aus.

    ![Erstellen eines Subnetzes](./media/create-url-route-portal/application-gateway-subnet.png)

3. Geben Sie als Name des Subnetzes *myBackendSubnet* ein, und wählen Sie **OK** aus.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Beispiel erstellen Sie drei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, ob das Anwendungsgateway erfolgreich erstellt wurde.

1. Wählen Sie **Neu**aus.
2. Wählen Sie **Compute**, und wählen Sie dann in der Liste der ausgewählten Elemente die Option **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:

    - *myVM1*: Name des virtuellen Computers
    - *azureuser*: Name des Administratorbenutzers
    - *Azure123456!* als Kennwort
    - Wählen Sie **Vorhandene verwenden** und dann *myResourceGroupAG* aus.

4. Klicken Sie auf **OK**.
5. Wählen Sie als Größe des virtuellen Computers **DS1_V2** und dann die Option **Auswählen** aus.
6. Stellen Sie sicher, dass als virtuelles Netzwerk **myVNet** und als Subnetz **myBackendSubnet** ausgewählt ist. 
7. Wählen Sie **Deaktiviert**, um die Startdiagnose zu deaktivieren.
8. Wählen Sie **OK**, überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und wählen Sie anschließend **Erstellen**.

### <a name="install-iis"></a>Installieren von IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Öffnen Sie die interaktive Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.

    ![Installieren der benutzerdefinierten Erweiterung](./media/create-url-route-portal/application-gateway-extension.png)

2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Erstellen Sie zwei weitere virtuelle Computer, und installieren Sie IIS mithilfe der soeben ausgeführten Schritte. Geben Sie als Namen *myVM2* und *myVM3* und als Werte für „VMName“ in „Set-AzVMExtension“ ein.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Erstellen von Back-End-Pools mit den virtuellen Computern

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.
2. Wählen Sie **Back-End-Pools** aus. Ein Standardpool mit dem Anwendungsgateway wurde automatisch erstellt. Wählen Sie **appGatewayBackendPool** aus.
3. Wählen Sie **Ziel hinzufügen** aus, um *myVM1* zu appGatewayBackendPool hinzuzufügen.

    ![Hinzufügen von Back-End-Servern](./media/create-url-route-portal/application-gateway-backend.png)

4. Wählen Sie **Speichern** aus.
5. Wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
6. Geben Sie den Namen *imagesBackendPool* ein, und fügen Sie *myVM2* mithilfe von **Ziel hinzufügen** hinzu.
7. Klicken Sie auf **OK**.
8. Wählen Sie **Hinzufügen** erneut aus, um einen weiteren Back-End-Pool mit dem Namen *videoBackendPool* hinzuzufügen, und fügen Sie dem Pool *myVM3* hinzu.

## <a name="create-a-backend-listener"></a>Erstellen eines Back-End-Listeners

1. Wählen Sie **Listener** und dann **Basic** aus.
2. Geben Sie *myBackendListener* als Name, *myFrontendPort* als Name des Front-End-Ports und *8080* als Port für den Listener ein.
3. Klicken Sie auf **OK**.

## <a name="create-a-path-based-routing-rule"></a>Erstellen einer pfadbasierten Routingregel

1. Wählen Sie **Regeln** und dann **Pfadbasiert** aus.
2. Geben Sie als Name *rule2* ein.
3. Geben Sie als Name für den ersten Pfad *Bilder* ein. Geben Sie als Pfad */images/*\* ein. Wählen Sie als Back-End-Pool **imagesBackendPool** aus.
4. Geben Sie als Name für den ersten Pfad *Video* ein. Geben Sie als Pfad */video/*\* ein. Wählen Sie als Back-End-Pool **videoBackendPool** aus.

    ![Erstellen einer pfadbasierten Regel](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Klicken Sie auf **OK**.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Wählen Sie die Option **Alle Ressourcen** und dann **myAGPublicIPAddress** aus.

    ![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Z.B. http://40.121.222.19.

    ![Testen der Basis-URL im Anwendungsgateway](./media/create-url-route-portal/application-gateway-iistest.png)

3. Ändern Sie die URL in http://&lt;ip-address&gt;:8080/images/test.htm, und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Images-URL im Anwendungsgateway](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Ändern Sie die URL in http://&lt;ip-address&gt;:8080/video/test.htm, und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Video-URL im Anwendungsgateway](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dem Anwendungsgateway erstellten Ressourcen nicht mehr benötigen, entfernen Sie die Ressourcengruppe. Beim Entfernen der Ressourcengruppe werden auch das Anwendungsgateway und alle zugehörigen Ressourcen entfernt. 

So entfernen Sie die Ressourcengruppe:

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressourcengruppen** aus.
2. Suchen Sie auf der Seite **Ressourcengruppen** in der Liste nach **myResourceGroupAG**, und wählen Sie den Eintrag aus.
3. Wählen Sie auf der Seite für die **Ressourcengruppe** die Option **Ressourcengruppe löschen** aus.
4. Geben Sie *myResourceGroupAG* für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, welche Möglichkeiten Azure Application Gateway bietet.](application-gateway-introduction.md)
