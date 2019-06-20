---
title: Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis – Azure-Portal
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal Routingregeln auf URL-Pfadbasis für ein Anwendungsgateway und eine VM-Skalierungsgruppe erstellen.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 10bc4e4c440e5495afd820f588270b7990108b68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135338"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Erstellen eines Anwendungsgateways mit pfadbasierten Routingregeln mithilfe des Azure-Portals

Sie können mit dem Azure-Portal [Routingregeln auf URL-Pfadbasis](application-gateway-url-route-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](application-gateway-introduction.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von virtuellen Computern. Anschließend erstellen Sie Routingregeln, die sicherstellen, dass Webdatenverkehr an die richtigen Server in den Pools gesendet wird.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines Anwendungsgateways
> * Erstellen von virtuellen Computern für Back-End-Server
> * Erstellen von Back-End-Pools mit den Back-End-Servern
> * Erstellen eines Back-End-Listeners
> * Erstellen einer pfadbasierten Routingregel

![URL-Routingbeispiel](./media/application-gateway-create-url-route-portal/scenario.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. In diesem Beispiel werden zwei Subnetze erstellt: eins für das Anwendungsgateway und eins für die Back-End-Server. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway.

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf **Neu**.
2. Klicken Sie auf **Netzwerk** und dann in der Liste der ausgewählten Elemente auf **Application Gateway**.
3. Geben Sie die folgenden Werte für das Anwendungsgateway ein:

   - *myAppGateway*: Name des Anwendungsgateways
   - *myResourceGroupAG*: neue Ressourcengruppe

     ![Erstellen eines neuen Anwendungsgateways](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
5. Klicken Sie auf **Virtuelles Netzwerk auswählen** und dann auf **Neu erstellen**, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet*: Name des virtuellen Netzwerks
   - *10.0.0.0/16*: Adressraum des virtuellen Netzwerks
   - *myAGSubnet*: Subnetzname
   - *10.0.0.0/24*: Adressraum des Subnetzes

     ![Virtuelles Netzwerk erstellen](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Klicken Sie auf **OK**, um das virtuelle Netzwerk und das Subnetz zu erstellen.
7. Klicken Sie auf **Öffentliche IP-Adresse auswählen** und dann auf **Neu erstellen**, und geben Sie den Namen der öffentlichen IP-Adresse ein. In diesem Beispiel heißt die öffentliche IP-Adresse *myAGPublicIPAddress*. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
8. Übernehmen Sie die Standardwerte für die Listenerkonfiguration, lassen Sie die Web Application Firewall deaktiviert, und klicken Sie dann auf **OK**.
9. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK**, um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myVNet**.
2. Klicken Sie auf **Subnetze** und dann auf **Subnetz**.

    ![Erstellen eines Subnetzes](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Geben Sie als Name des Subnetzes *myBackendSubnet* ein, und klicken Sie auf **OK**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Beispiel erstellen Sie drei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, ob das Anwendungsgateway erfolgreich erstellt wurde.

1. Klicken Sie auf **New**.
2. Klicken Sie auf **Compute**, und wählen Sie dann in der Liste der ausgewählten Elemente die Option **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:

    - *myVM1*: Name des virtuellen Computers
    - *azureuser*: Name des Administratorbenutzers
    - *Azure123456!* als Kennwort
    - Wählen Sie **Vorhandene verwenden** und dann *myResourceGroupAG* aus.

4. Klicken Sie auf **OK**.
5. Wählen Sie als Größe des virtuellen Computers **DS1_V2** aus, und klicken Sie auf **Auswählen**.
6. Stellen Sie sicher, dass als virtuelles Netzwerk **myVNet** und als Subnetz **myBackendSubnet** ausgewählt ist. 
7. Klicken Sie auf **Deaktiviert**, um die Startdiagnose zu deaktivieren.
8. Klicken Sie auf **OK**, überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **Erstellen**.

### <a name="install-iis"></a>Installieren von IIS

1. Öffnen Sie die interaktive Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.

    ![Installieren der benutzerdefinierten Erweiterung](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

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

1. Klicken Sie auf **Alle Ressourcen** und dann auf **myAppGateway**.
2. Klicken Sie auf **Back-End-Pools**. Ein Standardpool mit dem Anwendungsgateway wurde automatisch erstellt. Klicken Sie auf **appGatewayBackendPool**.
3. Klicken Sie auf **Ziel hinzufügen**, um *myVM1* zu „appGatewayBackendPool“ hinzuzufügen.

    ![Hinzufügen von Back-End-Servern](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Klicken Sie auf **Speichern**.
5. Klicken Sie auf **Back-End-Pools** und dann auf **Hinzufügen**.
6. Geben Sie den Namen *imagesBackendPool* ein, und fügen Sie *myVM2* mithilfe von **Ziel hinzufügen** hinzu.
7. Klicken Sie auf **OK**.
8. Klicken Sie erneut auf **Hinzufügen**, um einen weiteren Back-End-Pool mit dem Namen *videoBackendPool* hinzuzufügen, und fügen Sie dem Pool *myVM3* hinzu.

## <a name="create-a-backend-listener"></a>Erstellen eines Back-End-Listeners

1. Klicken Sie auf **Listener** und dann auf **Basic**.
2. Geben Sie *myBackendListener* als Name, *myFrontendPort* als Name des Front-End-Ports und *8080* als Port für den Listener ein.
3. Klicken Sie auf **OK**.

## <a name="create-a-path-based-routing-rule"></a>Erstellen einer pfadbasierten Routingregel

1. Klicken Sie auf **Regeln** und anschließend auf **Pfadbasiert**.
2. Geben Sie als Name *rule2* ein.
3. Geben Sie als Name für den ersten Pfad *Bilder* ein. Geben Sie als Pfad */images/* \* ein. Wählen Sie als Back-End-Pool **imagesBackendPool** aus.
4. Geben Sie als Name für den ersten Pfad *Video* ein. Geben Sie als Pfad */video/* \* ein. Wählen Sie als Back-End-Pool **videoBackendPool** aus.

    ![Erstellen einer pfadbasierten Regel](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Klicken Sie auf **OK**.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Klicken Sie auf **Alle Ressourcen** und dann auf **myAGPublicIPAddress**.

    ![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Beispiel: http:\//40.121.222.19.

    ![Testen der Basis-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Ändern Sie die URL in http://&lt;ip-address&gt;:8080/images/test.htm, und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Images-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Ändern Sie die URL in http://&lt;ip-address&gt;:8080/video/test.htm, und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Video-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Anwendungsgateways
> * Erstellen von virtuellen Computern für Back-End-Server
> * Erstellen von Back-End-Pools mit den Back-End-Servern
> * Erstellen eines Back-End-Listeners
> * Erstellen einer pfadbasierten Routingregel

Weitere Informationen zu Anwendungsgateways und den zugehörigen Ressourcen finden Sie in den Artikeln mit empfohlenen Vorgehensweisen.
