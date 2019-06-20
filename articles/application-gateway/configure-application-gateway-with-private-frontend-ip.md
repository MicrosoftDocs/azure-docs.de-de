---
title: Konfigurieren von Azure Application Gateway mit einer privaten Front-End-IP-Adresse
description: In diesem Artikel erfahren Sie, wie Sie Application Gateway mit einer privaten Front-End-IP-Adresse konfigurieren.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66134617"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurieren einer Application Gateway-Instanz mit einem Endpunkt für den internen Lastenausgleich (Internal Load Balancer, ILB)

Azure Application Gateway kann mit einer VIP mit Internetzugriff oder mit einem internen Endpunkt konfiguriert werden, der nicht über das Internet erreichbar ist. (Hierzu wird für die Front-End-IP-Adresse eine private IP-Adresse verwendet.) Dieser Endpunkt wird auch als Endpunkt für den internen Lastenausgleich (Internal Load Balancer, ILB) bezeichnet. Das Konfigurieren des Gateways mit einer privaten Front-End-IP-Adresse ist für interne Branchenanwendungen nützlich, die nicht für das Internet verfügbar gemacht werden. Es ist auch hilfreich für die Dienste und Ebenen in einer Anwendung mit mehreren Ebenen, die sich innerhalb einer Sicherheitsgrenze befinden und nicht für das Internet verfügbar gemacht werden, aber dennoch eine Round-Robin-Lastverteilung, Sitzungsbindungen oder SSL-Beendigung erfordern.

Dieser Artikel führt Sie durch die Schritte zum Konfigurieren einer Application Gateway-Instanz mit einer privaten Front-End-IP-Adresse über das Azure-Portal.

In diesem Artikel wird Folgendes behandelt:

- Erstellen einer privaten Front-End-IP-Konfiguration für eine Application Gateway-Instanz
- Erstellen einer Application Gateway-Instanz mit privater Front-End-IP-Konfiguration


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter <https://portal.azure.com> beim Azure-Portal an.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation in Azure zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. Sie können ein neues virtuelles Netzwerk erstellen oder ein bereits vorhandenes virtuelles Netzwerk auswählen. In diesem Beispiel wird ein neues virtuelles Netzwerk erstellt. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway. Application Gateway-Instanzen werden in separaten Subnetzen erstellt. In diesem Beispiel erstellen Sie zwei Subnetze: eins für das Anwendungsgateway und eins für die Back-End-Server.

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf **Neu**.
2. Klicken Sie auf **Netzwerk** und dann in der Liste „Ausgewählte“ auf **Application Gateway**.
3. Geben Sie für das Anwendungsgateway den Namen *myAppGateway* und für die neue Ressourcengruppe den Namen *myResourceGroupAG* ein.
4. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
5. Klicken Sie auf **Virtuelles Netzwerk auswählen** und dann auf **Neu erstellen**, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:
   - myVNet*: Name des virtuellen Netzwerks
   - 10.0.0.0/16*: Adressraum des virtuellen Netzwerks
   - *myAGSubnet*: Subnetzname
   - *10.0.0.0/24*: Adressraum des Subnetzes  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Klicken Sie auf **OK**, um das virtuelle Netzwerk und das Subnetz zu erstellen.
7. Konfigurieren Sie die Front-End-IP-Konfiguration als „Privat“. Standardmäßig wird eine dynamische IP-Adresszuweisung verwendet. Als Front-End-IP-Adresse wird die erste verfügbare Adresse des gewählten Subnetzes zugewiesen.
8. Falls Sie eine private IP-Adresse aus dem Subnetzadressbereich auswählen möchten (statische Zuordnung), klicken Sie auf das Feld **Bestimmte private IP-Adresse auswählen**, und geben Sie die IP-Adresse an.
   > [!NOTE]
   > Die Art der IP-Adresse (statisch oder dynamisch) kann nach der Zuordnung nicht mehr geändert werden.
9. Wählen Sie Ihre Listenerkonfiguration für das Protokoll und den Port sowie ggf. die WAF-Konfiguration aus, und klicken Sie auf „OK“.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK**, um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

## <a name="add-backend-pool"></a>Hinzufügen eines Back-End-Pools

Der Back-End-Pool wird zum Weiterleiten von Anforderungen an die Back-End-Server verwendet, die die Anforderung verarbeiten. Das Back-End kann Netzwerkadapter, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) und Back-Ends mit mehreren Mandanten (beispielsweise Azure App Service) umfassen. In diesem Beispiel werden virtuelle Computer als Ziel-Back-End verwendet. Wir können entweder vorhandene virtuelle Computer verwenden oder neue erstellen. In diesem Beispiel erstellen wir zwei virtuelle Computer, die von Azure als Back-End-Server für die Application Gateway-Instanz verwendet werden. Hierzu müssen die folgenden Schritte ausgeführt werden:

1. Erstellen von zwei neuen virtuellen Computern (*myVM* und *myVM2*) für die Verwendung als Back-End-Server
2. Installieren von IIS auf den virtuellen Computern, um zu überprüfen, ob die Application Gateway-Instanz erfolgreich erstellt wurde
3. Hinzufügen der Back-End-Server zum Back-End-Pool

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie auf **New**.
2. Klicken Sie auf **Compute**, und wählen Sie dann in der Liste der ausgewählten Elemente die Option **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:
   - *myVM*: Name des virtuellen Computers
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
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
