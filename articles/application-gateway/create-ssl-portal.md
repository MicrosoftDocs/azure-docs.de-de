---
title: 'Tutorial: Konfigurieren der TLS-Terminierung im Portal: Azure Application Gateway'
description: In diesem Tutorial erfahren Sie, wie Sie im Azure-Portal ein Anwendungsgateway konfigurieren und ein Zertifikat für die TLS-Terminierung hinzufügen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: c976ea236ae1d37cc0a543b10a9de55609035632
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986751"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Tutorial: Konfigurieren eines Anwendungsgateways mit TLS-Terminierung über das Azure-Portal

Sie können im Azure-Portal ein [Anwendungsgateway](overview.md) mit einem Zertifikat für die TLS-Terminierung konfigurieren, das virtuelle Computer als Back-End-Server verwendet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines selbstsignierten Zertifikats
> * Erstellen eines Anwendungsgateways mit dem Zertifikat
> * Erstellen der virtuellen Computer, die als Back-End-Server verwendet werden
> * Testen des Anwendungsgateways

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

In diesem Abschnitt erstellen Sie mit [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) ein selbstsigniertes Zertifikat. Sie laden das Zertifikat ins Azure-Portal hoch, wenn Sie den Listener für das Anwendungsgateway erstellen.

Öffnen Sie auf dem lokalen Computer ein Windows PowerShell-Fenster als Administrator. Führen Sie den folgenden Befehl aus, um das Zertifikat zu erstellen:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Die Ausgabe sollte in etwa wie die folgende Antwort aussehen:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Verwenden Sie [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) mit dem zurückgegebenen Fingerabdruck, um eine PFX-Datei aus dem Zertifikat zu exportieren. Stellen Sie sicher, dass Ihr Kennwort vier bis zwölf Zeichen lang ist:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressource erstellen**. Das Fenster **Neu** wird angezeigt.

2. Klicken Sie auf **Netzwerk**, und wählen Sie dann in der Liste **Ausgewählte** die Option **Application Gateway** aus.

### <a name="basics-tab"></a>Registerkarte „Grundlagen“

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte für die Einstellungen des Anwendungsgateways ein:

   - **Ressourcengruppe**: Wählen Sie **myResourceGroupAG** als Ressourcengruppe aus. Falls diese Gruppe nicht vorhanden ist, wählen Sie **Neue erstellen** aus, um sie zu erstellen.
   - **Name des Anwendungsgateways**: Geben Sie *myAppGateway* als Namen des Anwendungsgateways ein.

        ![Erstellen eines neuen Anwendungsgateways: Grundlagen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Für die Kommunikation in Azure zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. Sie können ein neues virtuelles Netzwerk erstellen oder ein bereits vorhandenes virtuelles Netzwerk auswählen. In diesem Beispiel erstellen Sie gleichzeitig mit dem Anwendungsgateway ein virtuelles Netzwerk. Application Gateway-Instanzen werden in separaten Subnetzen erstellt. In diesem Beispiel erstellen Sie zwei Subnetze: eins für das Anwendungsgateway und eins für die Back-End-Server.

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
   > Für die Application Gateway v2-SKU können Sie nur die **öffentliche** Front-End-IP-Konfiguration wählen. Die private Front-End-IP-Konfiguration ist derzeit für diese v2-SKU nicht aktiviert.

2. Wählen Sie **Neu hinzufügen** für **Öffentliche IP-Adresse** aus, und geben Sie *myAGPublicIPAddress* als Namen der öffentlichen IP-Adresse ein. Wählen Sie dann **OK** aus. 

   ![Erstellen eines neuen Anwendungsgateways: Front-Ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Klicken Sie auf **Weiter: Back-Ends**.

### <a name="backends-tab"></a>Registerkarte „Back-Ends“

Der Back-End-Pool wird zum Weiterleiten von Anforderungen an die Back-End-Server verwendet, die die Anforderung verarbeiten. Back-End-Pools können Netzwerkkarten, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) und Back-Ends mit mehreren Mandanten wie Azure App Service umfassen. In diesem Beispiel erstellen Sie mit Ihrem Anwendungsgateway einen leeren Back-End-Pool und fügen dann Back-End-Ziele zum Back-End-Pool hinzu.

1. Wählen Sie auf der Registerkarte **Back-Ends** die Option **Back-End-Pool hinzufügen** aus.

2. Geben Sie im Fenster **Back-End-Pool hinzufügen**, das geöffnet wird, die folgenden Werte ein, um einen leeren Back-End-Pool zu erstellen:

    - **Name**: Geben Sie *myBackendPool* als Name des Back-End-Pools ein.
    - **Back-End-Pool ohne Ziele hinzufügen**: Wählen Sie **Ja** aus, um einen Back-End-Pool ohne Ziele zu erstellen. Back-End-Ziele werden Sie nach dem Erstellen des Anwendungsgateways hinzufügen.

3. Wählen Sie im Fenster **Back-End-Pool hinzufügen** die Option **Hinzufügen** aus, um die Konfiguration des Back-End-Pools zu speichern und zur Registerkarte **Back-Ends** zurückzukehren.

   ![Erstellen eines neuen Anwendungsgateways: Back-Ends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Wählen Sie auf der Registerkarte **Back-Ends** die Option **Weiter: Konfiguration** aus.

### <a name="configuration-tab"></a>Registerkarte „Konfiguration“

Auf der Registerkarte **Konfiguration** verbinden Sie das Front-End und den von Ihnen erstellten Back-End-Pool mithilfe einer Routingregel.

1. Wählen Sie in der Spalte **Routingregeln** die Option **Routingregel hinzufügen** aus.

2. Geben Sie im Fenster **Routingregel hinzufügen**, das geöffnet wird, *myRoutingRule* als **Regelname** ein.

3. Eine Routingregel erfordert einen Listener. Geben Sie im Fenster **Routingregel hinzufügen** auf der Registerkarte **Listener** die folgenden Werte für den Listener ein:

    - **Name des Listeners**: Geben Sie *myListener* als Name für den Listener ein.
    - **Front-End-IP**: Wählen Sie **Öffentlich** aus, um die für das Front-End erstellte öffentliche IP-Adresse auszuwählen.
    - **Protokoll:** Wählen Sie **HTTPS** aus.
    - **Port:** Vergewissern Sie sich, dass 443 für den Port eingegeben wurde.

   Unter **HTTPS-Einstellungen**:

   - **Zertifikat auswählen**: Wählen Sie **Zertifikat hochladen** aus.
   - **PFX-Zertifikatsdatei**: Navigieren Sie zur zuvor erstellten Datei „c:\appgwcert.pfx“.
   - **Zertifikatname**: Geben Sie als Name für das Zertifikat *mycert1* ein.
   - **Kennwort**: Geben Sie das Kennwort ein, das Sie zum Erstellen des Zertifikats verwendet haben.
  
        Übernehmen Sie auf der Registerkarte **Listener** die Standardwerte für die übrigen Einstellungen. Wählen Sie dann die Registerkarte **Back-End-Ziele** aus, um den Rest der Routingregel zu konfigurieren.

   ![Erstellen eines neuen Anwendungsgateways: Listener](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Wählen Sie auf der Registerkarte **Back-End-Ziele** den Pool **myBackendPool** als **Back-End-Ziel** aus.

5. Wählen Sie für die **HTTP-Einstellung** die Option **Neu hinzufügen** aus, um eine neue HTTP-Einstellung zu erstellen. Die HTTP-Einstellung bestimmt das Verhalten der Routingregel. Geben Sie im daraufhin geöffneten Fenster **HTTP-Einstellung hinzufügen** unter **Name der HTTP-Einstellung** den Namen *myHTTPSetting* ein. Übernehmen Sie im Fenster **HTTP-Einstellung hinzufügen** die Standardwerte für die übrigen Einstellungen, und wählen Sie dann **Hinzufügen** aus, um zum Fenster **Routingregel hinzufügen** zurückzukehren. 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Erstellen eines neuen Anwendungsgateways: HTTP-Einstellung":::

6. Wählen Sie im Fenster **Routingregel hinzufügen** die Option **Hinzufügen** aus, um die Routingregel zu speichern und zur Registerkarte **Konfiguration** zurückzukehren.

   ![Erstellen eines neuen Anwendungsgateways: Routingregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Klicken Sie auf **Weiter: Tags** und dann auf **Weiter: Überprüfen + erstellen**.

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Überprüfen Sie die Einstellungen auf der Registerkarte **Überprüfen und erstellen**, und wählen Sie dann **Erstellen** aus, um das virtuelle Netzwerk, die öffentliche IP-Adresse und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways in Azure kann einige Minuten in Anspruch nehmen. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

## <a name="add-backend-targets"></a>Hinzufügen von Back-End-Zielen

In diesem Beispiel verwenden Sie virtuelle Computer als Ziel-Back-End. Sie können entweder vorhandene virtuelle Computer verwenden oder neue erstellen. Sie erstellen zwei virtuelle Computer, die von Azure als Back-End-Server für das Anwendungsgateway verwendet werden.

Gehen Sie dazu wie folgt vor:

1. Erstellen von zwei neuen virtuellen Computern (*myVM* und *myVM2*) für die Verwendung als Back-End-Server
2. Installieren von IIS auf den virtuellen Computern, um zu überprüfen, ob die Application Gateway-Instanz erfolgreich erstellt wurde
3. Hinzufügen der Back-End-Server zum Back-End-Pool

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**. Das Fenster **Neu** wird angezeigt.
2. Wählen Sie dann in der Liste **Beliebt** die Option **Windows Server 2016 Datacenter** aus. Die Seite **Virtuellen Computer erstellen** wird angezeigt.

   Application Gateway kann Datenverkehr an jeden beliebigen virtuellen Computer weiterleiten, der im Back-End-Pool verwendet wird. In diesem Beispiel verwenden Sie Windows Server 2016 Datacenter.

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte für die VM-Einstellungen ein:

    - **Ressourcengruppe**: Wählen Sie **myResourceGroupAG** als Namen der Ressourcengruppe aus.
    - **Name des virtuellen Computers**: Geben Sie *myVM* als Namen der VM ein.
    - **Benutzername**: Geben Sie einen Benutzernamen für den Administrator ein.
    - **Kennwort**: Geben Sie ein Kennwort für das Administratorkonto ein.
1. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Weiter: Datenträger**.  
2. Übernehmen Sie auf der Registerkarte **Datenträger** die Standardwerte, und klicken Sie auf **Weiter: Netzwerk**.
3. Vergewissern Sie sich auf der Registerkarte **Netzwerk**, dass **myVNet** für **Virtuelles Netzwerk** ausgewählt und **Subnetz** auf **myBackendSubnet** festgelegt ist. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Weiter: Verwaltung** aus.

   Application Gateway kann mit Instanzen außerhalb des eigenen virtuellen Netzwerks kommunizieren, es muss jedoch sichergestellt werden, dass eine IP-Verbindung besteht.
1. Legen Sie auf der Registerkarte **Verwaltung** die Option **Startdiagnose** auf **Deaktivieren** fest. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Bewerten + erstellen**.
2. Überprüfen Sie auf der Registerkarte **Bewerten + erstellen** die Einstellungen, beheben Sie alle Validierungsfehler, und wählen Sie dann **Erstellen** aus.
3. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie den Vorgang fortsetzen.

### <a name="install-iis-for-testing"></a>Installieren von IIS für Testzwecke

In diesem Beispiel installieren Sie IIS auf den virtuellen Computern nur, um zu überprüfen, ob Azure das Anwendungsgateway erfolgreich erstellt hat.

1. Öffnen Sie [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Wählen Sie dazu in der oberen Navigationsleiste des Azure-Portals **Cloud Shell** und dann in der Dropdownliste **PowerShell** aus. 

    ![Installieren der benutzerdefinierten Erweiterung](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Ändern Sie die Einstellung des Speicherorts für Ihre Umgebung, und führen Sie anschließend den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Erstellen Sie eine zweite VM, und installieren Sie IIS mithilfe der zuvor ausgeführten Schritte. Verwenden Sie *myVM2* für den VM-Namen und für die Einstellung **VMName** des Cmdlets **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Hinzufügen von Back-End-Servern zu Back-End-Pools

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.

2. Klicken Sie im Menü auf der linken Seite auf **Back-End-Pools**.

3. Wählen Sie **myBackendPool** aus.

4. Wählen Sie unter **Zieltyp** in der Dropdownliste die Option **Virtueller Computer** aus.

5. Wählen Sie unter **Ziel** in der Dropdownliste die Netzwerkschnittstelle unter **myVM** aus.

6. Wiederholen Sie den Vorgang, um die Netzwerkschnittstelle für **myVM2** hinzuzufügen.

    ![Hinzufügen von Back-End-Servern](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wählen Sie **Speichern** aus.

7. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Wählen Sie die Option **Alle Ressourcen** und dann **myAGPublicIPAddress** aus.

    ![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Geben Sie in die Adressleiste Ihres Browsers *https://\<your application gateway ip address\>* ein.

   Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf **Details** (bzw. in Chrome auf **Erweitert**), und anschließend auf „Webseite trotzdem laden“:

    ![Sicherheitswarnung](./media/create-ssl-portal/application-gateway-secure.png)

    Die gesicherte IIS-Website wird dann wie im folgenden Beispiel angezeigt:

    ![Testen der Basis-URL im Anwendungsgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie dazu die Ressourcengruppe und dann **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Erfahren Sie mehr über die [TLS-Unterstützung in Application Gateway](ssl-overview.md)