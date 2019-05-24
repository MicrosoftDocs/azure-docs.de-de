---
title: 'Tutorial: Konfigurieren eines Anwendungsgateways mit SSL-Beendigung – Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie im Azure-Portal ein Anwendungsgateway konfigurieren und ein Zertifikat für die SSL-Beendigung hinzufügen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134531"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Tutorial: Konfigurieren eines Anwendungsgateways mit SSL-Beendigung mithilfe des Azure-Portals

Sie können im Azure-Portal ein [Anwendungsgateway](overview.md) mit einem Zertifikat für die SSL-Beendigung konfigurieren, das virtuelle Computer als Back-End-Server verwendet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines selbstsignierten Zertifikats
> * Erstellen eines Anwendungsgateways mit dem Zertifikat
> * Erstellen der virtuellen Computer, die als Back-End-Server verwendet werden
> * Testen des Anwendungsgateways

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

In diesem Abschnitt erstellen Sie mit [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) ein selbstsigniertes Zertifikat. Sie laden das Zertifikat ins Azure-Portal hoch, wenn Sie den Listener für das Anwendungsgateway erstellen.

Öffnen Sie auf dem lokalen Computer ein Windows PowerShell-Fenster als Administrator. Führen Sie den folgenden Befehl aus, um das Zertifikat zu erstellen:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Die Ausgabe sollte in etwa wie die folgende Antwort aussehen:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. In diesem Beispiel werden zwei Subnetze erstellt: eins für das Anwendungsgateway und eins für die Back-End-Server. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway.

1. Wählen Sie in der linken oberen Ecke des Azure-Portals die Option **Neu** aus.
2. Klicken Sie auf **Netzwerk** und dann in der Liste der ausgewählten Elemente auf **Application Gateway**.
3. Geben Sie für das Anwendungsgateway den Namen *myAppGateway* und für die neue Ressourcengruppe den Namen *myResourceGroupAG* ein.
4. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie dann auf **OK**.
5. Wählen Sie **Virtuelles Netzwerk auswählen** und dann **Neu erstellen** aus, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet*: Name des virtuellen Netzwerks
   - *10.0.0.0/16*: Adressraum des virtuellen Netzwerks
   - *myAGSubnet*: Subnetzname
   - *10.0.0.0/24*: Adressraum des Subnetzes

     ![Virtuelles Netzwerk erstellen](./media/create-ssl-portal/application-gateway-vnet.png)

6. Wählen Sie **OK** aus, um das virtuelle Netzwerk und das Subnetz zu erstellen.
7. Wählen Sie **Öffentliche IP-Adresse auswählen** und dann **Neu erstellen** aus, und geben Sie den Namen der öffentlichen IP-Adresse ein. In diesem Beispiel heißt die öffentliche IP-Adresse *myAGPublicIPAddress*. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie dann auf **OK**.
8. Wählen Sie für das Protokoll des Listeners **HTTPS** aus, und stellen Sie sicher, dass als Port **443** festgelegt ist.
9. Wählen Sie das Ordnersymbol aus, und navigieren Sie zum zuvor erstellten Zertifikat *appgwcert.pfx*, um es hochzuladen.
10. Geben Sie als Name des Zertifikats *mycert1* und als Kennwort *Azure123456!* ein, und wählen Sie dann **OK** aus.

    ![Erstellen eines neuen Anwendungsgateways](./media/create-ssl-portal/application-gateway-create.png)

11. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK**, um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myVNet**.
2. Wählen Sie **Subnetze** und dann **Subnetz** aus.

    ![Erstellen eines Subnetzes](./media/create-ssl-portal/application-gateway-subnet.png)

3. Geben Sie als Name des Subnetzes *myBackendSubnet* ein, und wählen Sie **OK** aus.

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Beispiel erstellen Sie zwei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, ob das Anwendungsgateway erfolgreich erstellt wurde.

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Wählen Sie **Neu**aus.
2. Wählen Sie **Compute**, und wählen Sie dann in der Liste der ausgewählten Elemente die Option **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:

    - *myVM*: Name des virtuellen Computers
    - *azureuser*: Name des Administratorbenutzers
    - *Azure123456!* als Kennwort
    - Wählen Sie **Vorhandene verwenden** und dann *myResourceGroupAG* aus.

4. Klicken Sie auf **OK**.
5. Wählen Sie als Größe des virtuellen Computers **DS1_V2** und dann die Option **Auswählen** aus.
6. Stellen Sie sicher, dass als virtuelles Netzwerk **myVNet** und als Subnetz **myBackendSubnet** ausgewählt ist. 
7. Wählen Sie **Deaktiviert**, um die Startdiagnose zu deaktivieren.
8. Wählen Sie **OK**, überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und wählen Sie anschließend **Erstellen**.

### <a name="install-iis"></a>Installieren von IIS

1. Öffnen Sie die interaktive Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.

    ![Installieren der benutzerdefinierten Erweiterung](./media/create-ssl-portal/application-gateway-extension.png)

2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren: 

    ```azurepowershell-interactive
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

3. Erstellen Sie einen zweiten virtuellen Computer, und installieren Sie IIS mithilfe der soeben ausgeführten Schritte. Geben Sie *myVM2* als Name und für „VMName“ in „Set-AzVMExtension“ ein.

### <a name="add-backend-servers"></a>Hinzufügen von Back-End-Servern

1. Wählen Sie **Alle Ressourcen** und dann **myAppGateway** aus.
1. Wählen Sie **Back-End-Pools** aus. Ein Standardpool mit dem Anwendungsgateway wurde automatisch erstellt. Wählen Sie **appGatewayBackendPool** aus.
1. Wählen Sie **Ziel hinzufügen** aus, um alle erstellten virtuellen Computer dem Back-End-Pool hinzuzufügen.

    ![Hinzufügen von Back-End-Servern](./media/create-ssl-portal/application-gateway-backend.png)

1. Wählen Sie **Speichern** aus.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Wählen Sie die Option **Alle Ressourcen** und dann **myAGPublicIPAddress** aus.

    ![Notieren der öffentlichen IP-Adresse des Anwendungsgateways](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf „Details“ und anschließend auf „Webseite trotzdem laden“:

    ![Sicherheitswarnung](./media/create-ssl-portal/application-gateway-secure.png)

    Die gesicherte IIS-Website wird dann wie im folgenden Beispiel angezeigt:

    ![Testen der Basis-URL im Anwendungsgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, welche Möglichkeiten Azure Application Gateway bietet.](application-gateway-introduction.md)
