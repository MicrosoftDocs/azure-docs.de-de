---
title: 'Tutorial: Routingregeln auf URL-Pfadbasis im Portal: Azure Application Gateway'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Portal Routingregeln auf URL-Pfadbasis für ein Anwendungsgateway und eine VM-Skalierungsgruppe erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 407bd5679c6afebf26c2e6b768e0f8513ac39123
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397585"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Erstellen eines Anwendungsgateways mit pfadbasierten Routingregeln mithilfe des Azure-Portals

Sie können mit dem Azure-Portal [Routingregeln auf URL-Pfadbasis](./url-route-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](./overview.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von virtuellen Computern. Anschließend erstellen Sie Routingregeln, die sicherstellen, dass Webdatenverkehr an die richtigen Server in den Pools gesendet wird.

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

## <a name="prerequisites"></a>Voraussetzungen

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Beispiel erstellen Sie drei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, ob das Anwendungsgateway wie erwartet funktioniert.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
2. Wählen Sie in der Liste „Beliebt“ die Option **Windows Server 2016 Datacenter** aus.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:

    - Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie *myResourceGroupAG* ein.
    - **Name des virtuellen Computers** : *myVM1*
    - **Region** : *USA, Osten*
    - **Benutzername** : *azureuser*
    - **Kennwort** : *Azure123456!*


4. Wählen Sie **Weiter: Datenträger**.
5. Wählen Sie **Weiter: Netzwerk** aus.
6. Wählen Sie unter **Virtuelles Netzwerk** die Option **Neu erstellen** aus, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet* : Name des virtuellen Netzwerks
   - *10.0.0.0/16* : Adressraum des virtuellen Netzwerks
   - *myBackendSubnet* : Erster Subnetzname
   - *10.0.1.0/24* : Adressraum des Subnetzes.
   - *myAGSubnet* : Zweiter Subnetzname
   - *10.0.0.0/24* : Adressraum des Subnetzes
7. Klicken Sie auf **OK**.

8. Stellen Sie unter **Netzwerkschnittstelle** sicher, dass **myBackendSubnet** für das Subnetz ausgewählt ist, und wählen Sie anschließend **Weiter: Verwaltung** aus.
9. Wählen Sie **Aus** aus, um die Startdiagnose zu deaktivieren.
10. Klicken Sie auf **Überprüfen + erstellen** , überprüfen Sie die Einstellungen auf der Zusammenfassungsseite, und wählen Sie anschließend **Erstellen** aus.
11. Erstellen Sie zwei weitere virtuelle Computer ( *myVM2* und *myVM3* ), und ordnen Sie sie im virtuellen Netzwerk *MyVNet* und im Subnetz *myBackendSubnet* an.

### <a name="install-iis"></a>Installieren von IIS

1. Öffnen Sie die interaktive Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.

    ![Installieren der benutzerdefinierten Erweiterung](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren: 

    ```azurepowershell
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

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressource erstellen**. Das Fenster **Neu** wird angezeigt.

2. Klicken Sie auf **Netzwerk** , und wählen Sie dann in der Liste **Ausgewählte** die Option **Application Gateway** aus.

### <a name="basics-tab"></a>Registerkarte „Grundlagen“

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte für die Einstellungen des Anwendungsgateways ein:

   - **Ressourcengruppe** : Wählen Sie **myResourceGroupAG** als Ressourcengruppe aus.
   - **Name des Anwendungsgateways** : Geben Sie *myAppGateway* als Namen des Anwendungsgateways ein.
   - **Region** : Wählen Sie **USA, Osten** aus.

        ![Erstellen eines neuen Anwendungsgateways: Grundlagen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Wählen Sie unter **Virtuelles Netzwerk konfigurieren** die Option **myVNet** als Namen für das virtuelle Netzwerk aus.
3. Wählen Sie die Option **myAGSubnet** für das Subnetz aus.
3. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und wählen Sie anschließend **Weiter: Front-Ends** aus.

### <a name="frontends-tab"></a>Registerkarte „Front-Ends“

1. Vergewissern Sie sich auf der Registerkarte **Front-Ends** , dass der **Typ der Front-End-IP-Adresse** auf **Öffentlich** festgelegt ist.

   > [!NOTE]
   > Für die Application Gateway v2-SKU können Sie nur die **öffentliche** Front-End-IP-Konfiguration wählen. Die private Front-End-IP-Konfiguration ist derzeit für diese v2-SKU nicht aktiviert.

2. Wählen Sie **Neu erstellen** für die **Öffentliche IP-Adresse** aus, und geben Sie *myAGPublicIPAddress* als Namen der öffentlichen IP-Adresse ein. Wählen Sie dann **OK** aus. 
3. Klicken Sie auf **Weiter: Back-Ends**.

### <a name="backends-tab"></a>Registerkarte „Back-Ends“

Der Back-End-Pool wird zum Weiterleiten von Anforderungen an die Back-End-Server verwendet, die die Anforderung verarbeiten. Back-End-Pools können Netzwerkkarten, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) und Back-Ends mit mehreren Mandanten wie Azure App Service umfassen.

1. Wählen Sie auf der Registerkarte **Back-Ends** die Option **+Back-End-Pool hinzufügen** aus.

2. Geben Sie im Fenster **Back-End-Pool hinzufügen** , das geöffnet wird, die folgenden Werte ein, um einen leeren Back-End-Pool zu erstellen:

    - **Name** : Geben Sie *myBackendPool* als Name des Back-End-Pools ein.
3. Wählen Sie unter **Back-End-Ziele** > **Zieltyp** in der Dropdownliste die Option **Virtueller Computer** aus.

5. Wählen Sie unter **Ziel** die Netzwerkschnittstelle für **myVM1** aus.
6. Wählen Sie **Hinzufügen**.
7. Wiederholen Sie den Vorgang, um den Back-End-Pool *Images* mit *myVM2* als Ziel und den Back-End-Pool *Video* mit *myVM3* als Ziel hinzuzufügen.
8. Wählen Sie **Hinzufügen** aus, um die Konfiguration des Back-End-Pools zu speichern, und wechseln Sie zurück zur Registerkarte **Back-Ends**.

4. Wählen Sie auf der Registerkarte **Back-Ends** die Option **Weiter: Konfiguration** aus.

### <a name="configuration-tab"></a>Registerkarte „Konfiguration“

Auf der Registerkarte **Konfiguration** verbinden Sie das Front-End und den von Ihnen erstellten Back-End-Pool mithilfe einer Routingregel.

1. Wählen Sie **Regel hinzufügen** in der Spalte **Routingregeln** aus.

2. Geben Sie im Fenster **Routingregel hinzufügen** , das geöffnet wird, *myRoutingRule* als **Regelname** ein.

3. Eine Routingregel erfordert einen Listener. Geben Sie im Fenster **Routingregel hinzufügen** auf der Registerkarte **Listener** die folgenden Werte für den Listener ein:

    - **Name des Listeners** : Geben Sie *myListener* als Name für den Listener ein.
    - **Front-End-IP** : Wählen Sie **Öffentlich** aus, um die für das Front-End erstellte öffentliche IP-Adresse auszuwählen.
    - **Port:** Geben Sie *8080* ein.
  
        Übernehmen Sie auf der Registerkarte **Listener** die Standardwerte für die übrigen Einstellungen. Wählen Sie dann die Registerkarte **Back-End-Ziele** aus, um den Rest der Routingregel zu konfigurieren.

4. Wählen Sie auf der Registerkarte **Back-End-Ziele** den Pool **myBackendPool** als **Back-End-Ziel** aus.

5. Wählen Sie für die **HTTP-Einstellung** die Option **Neu erstellen** aus, um eine neue HTTP-Einstellung zu erstellen. Die HTTP-Einstellung bestimmt das Verhalten der Routingregel. 

6. Geben Sie im Fenster **HTTP-Einstellung hinzufügen** , das geöffnet wird, *myHTTPSetting* als **Name der HTTP-Einstellung** ein. Übernehmen Sie im Fenster **HTTP-Einstellung hinzufügen** die Standardwerte für die übrigen Einstellungen, und wählen Sie dann **Hinzufügen** aus, um zum Fenster **Routingregel hinzufügen** zurückzukehren.
7. Wählen Sie unter **Pfadbasiertes Routing** die Option **Zum Erstellen einer pfadbasierten Regel mehrere Ziele hinzufügen**  aus.
8. Geben Sie unter **Pfad** den Pfad */images/* \* ein.
9. Geben Sie unter **Name der Pfadregel** den Namen *Images* ein.
10. Wählen Sie unter **HTTP-Einstellung** die Option **myHTTPSetting** aus.
11. Wählen Sie unter **Back-End-Ziel** die Option **Images** aus.
12. Wählen Sie **Hinzufügen** aus, um die Pfadregel zu speichern, und wechseln Sie zurück zur Registerkarte **Routingregel hinzufügen**.
13. Wiederholen Sie den Vorgang, um eine weitere Regel für „Video“ hinzuzufügen.
14. Wählen Sie **Hinzufügen** aus, um die Routingregel hinzuzufügen, und wechseln Sie zurück zur Registerkarte **Konfiguration**.
15. Klicken Sie auf **Weiter: Tags** und dann auf **Weiter: Überprüfen + erstellen**.

> [!NOTE]
> Sie müssen keine benutzerdefinierte */* *-Pfadregel zum Verarbeiten von Standardfällen hinzufügen. Dies wird automatisch vom Standard-Back-End-Pool durchgeführt.

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Überprüfen Sie die Einstellungen auf der Registerkarte **Überprüfen und erstellen** , und wählen Sie dann **Erstellen** aus, um das virtuelle Netzwerk, die öffentliche IP-Adresse und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways in Azure kann einige Minuten in Anspruch nehmen. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.


## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

    ![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Beispiel: http:\//52.188.72.175:8080.

    ![Testen der Basis-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Der Listener an Port 8080 leitet diese Anforderung an den Standard-Back-End-Pool weiter.

3. Ändern Sie die URL in *http://&lt;ip-address&gt;:8080/images/test.htm* , und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Images-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Der Listener an Port 8080 leitet diese Anforderung an den Back-End-Pool *Images* weiter.

4. Ändern Sie die URL in *http://&lt;ip-address&gt;:8080/video/test.htm* , und ersetzen Sie &lt;ip-address&gt; durch Ihre IP-Adresse. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Video-URL im Anwendungsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Der Listener an Port 8080 leitet diese Anforderung an den Back-End-Pool *Video* weiter.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie dazu die Ressourcengruppe und dann **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktivieren von End-to-End-TLS in Azure Application Gateway](./ssl-overview.md)