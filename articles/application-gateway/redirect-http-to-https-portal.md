---
title: HTTP-zu-HTTPS-Umleitung im Portal – Azure Application Gateway
description: Erfahren Sie, wie Sie mithilfe des Azure-Portals ein Anwendungsgateway mit Datenverkehr erstellen, der von HTTP zu HTTPS umgeleitet wird.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397279"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über das Azure-Portal

Sie können das Azure-Portal verwenden, um ein [Anwendungsgateway](overview.md) mit einem Zertifikat für die TLS-Terminierung zu erstellen. Zum Umleiten des HTTP-Datenverkehrs an den HTTPS-Port in Ihrem Anwendungsgateway wird eine Routingregel verwendet. In diesem Beispiel erstellen Sie auch eine [VM-Skalierungsgruppe](../virtual-machine-scale-sets/overview.md) für den Back-End-Pool des Anwendungsgateways, die zwei virtuelle Computerinstanzen enthält.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines selbstsignierten Zertifikats
* Einrichten eines Netzwerks
* Erstellen eines Anwendungsgateways mit dem Zertifikat
* Hinzufügen eines Listeners und einer Umleitungsregel
* Erstellen einer VM-Skalierungsgruppe mit dem standardmäßigen Back-End-Pool

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Für dieses Tutorial ist die Version 1.0.0 oder höher des Azure PowerShell-Moduls erforderlich, um ein Zertifikat zu erstellen und IIS zu installieren. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Um die Befehle in diesem Tutorial auszuführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Für den Einsatz in einer Produktionsumgebung sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat importieren. Für dieses Tutorial erstellen Sie mit [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) ein selbstsigniertes Zertifikat. Sie können [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) mit dem zurückgegebenen Fingerabdruck verwenden, um eine PFX-Datei aus dem Zertifikat zu exportieren.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Die Ausgabe sollte in etwa wie folgendes Ergebnis aussehen:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Verwenden Sie den Fingerabdruck, um die PFX-Datei zu erstellen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. In diesem Beispiel werden zwei Subnetze erstellt: eins für das Anwendungsgateway und eins für die Back-End-Server. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie in der linken oberen Ecke des Azure-Portals auf **Ressource erstellen**.
3. Klicken Sie auf **Netzwerk** und dann in der Liste der ausgewählten Elemente auf **Application Gateway**.
4. Geben Sie die folgenden Werte für das Anwendungsgateway ein:

   - *myAppGateway* : Name des Anwendungsgateways
   - *myResourceGroupAG* : neue Ressourcengruppe

     ![Erstellen eines neuen Anwendungsgateways](./media/create-url-route-portal/application-gateway-create.png)

5. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
6. Klicken Sie auf **Virtuelles Netzwerk auswählen** und dann auf **Neu erstellen** , und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet* : Name des virtuellen Netzwerks
   - *10.0.0.0/16* : Adressraum des virtuellen Netzwerks
   - *myAGSubnet* : Subnetzname
   - *10.0.0.0/24* : Adressraum des Subnetzes

     ![Virtuelles Netzwerk erstellen](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klicken Sie auf **OK** , um das virtuelle Netzwerk und das Subnetz zu erstellen.
8. Stellen Sie unter **Front-End-IP-Konfiguration** sicher, dass **IP-Adresstyp** auf **Öffentlich** festgelegt und **Neu erstellen** ausgewählt ist. Geben Sie als Namen *myAGPublicIPAddress* ein. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
9. Wählen Sie unter **Listenerkonfiguration** die Option **HTTPS** aus, wählen Sie **Datei auswählen** aus, und navigieren Sie dann zu der Datei *c:\appgwcert.pfx* , und wählen Sie **Öffnen** aus.
10. Geben Sie als Zertifikatnamen *appgwcert* und als Kennwort *Azure123456!* ein. als Kennwort
11. Lassen Sie die Web Application Firewall deaktiviert, und wählen Sie dann **OK** aus.
12. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK** , um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myVNet**.
2. Klicken Sie auf **Subnetze** und anschließend auf **Subnetz**.

    ![Erstellen eines Subnetzes](./media/create-url-route-portal/application-gateway-subnet.png)

3. Geben Sie *myBackendSubnet* als Namen des Subnetzes ein.
4. Geben Sie *10.0.2.0/24* als Adressbereich ein, und wählen Sie dann **OK** aus.

## <a name="add-a-listener-and-redirection-rule"></a>Hinzufügen eines Listeners und einer Umleitungsregel

### <a name="add-the-listener"></a>Hinzufügen des Listeners

Fügen Sie zunächst den Listener namens *myListener* für Port 80 hinzu.

1. Öffnen Sie die Ressourcengruppe **myResourceGroupAG** , und wählen Sie **myAppGateway** aus.
2. Wählen Sie **Listener** und dann **+ Basic** aus.
3. Geben Sie als Namen *MyListener* ein.
4. Geben Sie *httpPort* als neuen Fron-End-Port-Namen und *80* als Port ein.
5. Stellen Sie sicher, dass das Protokoll auf **HTTP** festgelegt ist, und wählen Sie dann **OK** aus.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Hinzufügen einer Routingregel mit einer Umleitungskonfiguration

1. Wählen Sie für **myAppGateway** die Option **Regeln** und dann **+Routingregel anfordern** aus.
2. Geben Sie als **Regelnamen** den Text *Rule2* ein.
3. Stellen Sie sicher, dass für den Listener **MyListener** aktiviert ist.
4. Klicken Sie auf die Registerkarte **Back-End-Ziele** , und wählen Sie **Zieltyp** als *Umleitung* aus.
5. Wählen Sie für **Umleitungstyp** die Option **Permanent** aus.
6. Wählen Sie für **Umleitungsziel** die Option **Listener** aus.
7. Stellen Sie sicher, dass **Ziellistener** auf **appGatewayHttpListener** festgelegt ist.
8. Wählen Sie für **Abfragezeichenfolge einbeziehen** und **Pfad einbeziehen** die Option *Ja* aus.
9. Wählen Sie **Hinzufügen**.

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, um Server für den Back-End-Pool im Anwendungsgateway bereitzustellen.

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** aus.
3. Geben Sie in das Suchfeld *Skalierungsgruppe* ein, und drücken Sie die EINGABETASTE.
4. Wählen Sie **VM-Skalierungsgruppe** und dann **Erstellen** aus.
5. Geben Sie für **Name der VM-Skalierungsgruppe** den Wert *myvmss* ein.
6. Stellen Sie bei „Betriebssystem-Datenträgerimage“ sicher, dass **Windows Server 2016 Datacenter** ausgewählt ist.
7. Wählen Sie für **Ressourcengruppe** den Wert **myResourceGroupAG** aus.
8. Geben Sie für *Benutzername* den Wert **azureuser** ein.
9. Geben Sie für **Kennwort** den Wert *Azure123456!* ein, und bestätigen Sie das Kennwort.
10. Stellen Sie sicher, dass der Wert für **Instanzenanzahl** **2** lautet.
11. Wählen Sie für **Instanzgröße** den Wert **D2s_v3** aus.
12. Stellen Sie sicher, dass unter **Netzwerk** die Option **Optionen für den Lastenausgleich auswählen** auf **Application Gateway** festgelegt ist.
13. Stellen Sie sicher, dass **Application Gateway** auf **myAppGateway** festgelegt ist.
14. Stellen Sie sicher, dass **Subnetz** auf **myBackendSubnet** festgelegt ist.
15. Klicken Sie auf **Erstellen**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Zuordnen der Skalierungsgruppe zum entsprechenden Back-End-Pool

Die Benutzeroberfläche des Portals für VM-Skalierungsgruppen erstellt einen neuen Back-End-Pool für die Skalierungsgruppe, aber Sie müssen diese Ihrem vorhandenen appGatewayBackendPool zuordnen.

1. Öffnen Sie die Ressourcengruppe **myResourceGroupAg**.
2. Wählen Sie **myAppGateway** aus.
3. Wählen Sie **Back-End-Pools** aus.
4. Wählen Sie **myAppGatewaymyvmss** aus.
5. Wählen Sie **Alle Ziele aus Back-End-Pool entfernen** aus.
6. Wählen Sie **Speichern** aus.
7. Nach Abschluss dieses Vorgangs wählen Sie den Back-End-Pool **myAppGatewaymyvmss** aus, und wählen Sie dann **Löschen** sowie anschließend **OK** aus.
8. Wählen Sie **appGatewayBackendPool** aus.
9. Wählen Sie unter **Ziele** die Option **VMSS** aus.
10. Wählen Sie unter **VMSS** die Option **myvmss** aus.
11. Wählen Sie unter **Netzwerkschnittstellenkonfigurationen** die Option **myvmssNic** aus.
12. Wählen Sie **Speichern** aus.

### <a name="upgrade-the-scale-set"></a>Upgrade der Skalierungsgruppe

Abschließend müssen Sie ein Upgrade der Skalierungsgruppe mit diesen Änderungen vornehmen.

1. Wählen Sie die Skalierungsgruppe **myvmss** aus.
2. Wählen Sie unter **Einstellungen** die Option **Instanzen** aus.
3. Wählen Sie beide Instanzen und dann **Upgrade** aus.
4. Klicken Sie auf **Ja** , um zu bestätigen.
5. Nachdem dieser Vorgang abgeschlossen ist, wechseln Sie zurück zu **myAppGateway** , und wählen Sie **Back-End-Pools** aus. Es sollte jetzt angezeigt werden, dass der **appGatewayBackendPool** zwei Ziele und **myAppGatewaymyvmss** keine Ziele besitzt.
6. Wählen Sie **myAppGatewaymyvmss** und dann **Löschen** aus.
7. Wählen Sie zum Fortzufahren **OK** aus.

### <a name="install-iis"></a>Installieren von IIS

Eine einfache Möglichkeit zum Installieren von IIS in der Skalierungsgruppe besteht in der Verwendung der PowerShell. Klicken Sie im Portal auf das Cloud Shell-Symbol, und stellen Sie sicher, dass **PowerShell** ausgewählt ist.

Fügen Sie den folgenden Code in das PowerShell-Fenster ein, und drücken Sie die EINGABETASTE.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Upgrade der Skalierungsgruppe

Nach dem Ändern der Instanzen mit IIS müssen Sie erneut ein Upgrade der Skalierungsgruppe mit dieser Änderung vornehmen.

1. Wählen Sie die Skalierungsgruppe **myvmss** aus.
2. Wählen Sie unter **Einstellungen** die Option **Instanzen** aus.
3. Wählen Sie beide Instanzen und dann **Upgrade** aus.
4. Klicken Sie auf **Ja** , um zu bestätigen.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Sie finden die öffentliche IP-Adresse der Anwendung auf der Seite „Übersicht“ des Application Gateway.

1. Wählen Sie **myAppGateway** aus.
2. Notieren Sie sich auf der Seite **Übersicht** die IP-Adresse unter **Öffentliche Front-End-IP-Adresse**.

3. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Zum Beispiel, http://52.170.203.149

   ![Sicherheitswarnung](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Wenn Sie ein selbstsigniertes Zertifikat verwendet haben und die Sicherheitswarnung akzeptieren möchten, klicken Sie auf **Details** und anschließend auf **Webseite trotzdem laden**. Die gesicherte IIS-Website wird dann wie im folgenden Beispiel angezeigt:

   ![Testen der Basis-URL im Anwendungsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum [Erstellen eines Application Gateways mit interner Umleitung](redirect-internal-site-powershell.md).