---
title: Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites – Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal ein Anwendungsgateway erstellen, mit dem mehrere Websites gehostet werden.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 85113a5007a171459b831684f584773ba4328b94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122407"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites mit dem Azure-Portal

Sie können mit dem Azure-Portal ein [Hosting mehrerer Websites](application-gateway-multi-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](application-gateway-introduction.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von VM-Skalierungsgruppen. Anschließend konfigurieren Sie Listener und Regeln basierend auf Domänen in Ihrem Besitz, um sicherzustellen, dass Webdatenverkehr von geeigneten Servern in den Pools empfangen wird. In diesem Tutorial wird vorausgesetzt, dass Sie mehrere Domänen besitzen, und es werden Beispiele zu *www\.contoso.com* und *www\.fabrikam.com* verwendet.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines Anwendungsgateways
> * Erstellen von virtuellen Computern für Back-End-Server
> * Erstellen von Back-End-Pools mit den Back-End-Servern
> * Erstellen der Listener und Routingregeln
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

![Beispiel zum Routing für mehrere Websites](./media/application-gateway-create-multisite-portal/scenario.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Für die Kommunikation zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich. In diesem Beispiel werden zwei Subnetze erstellt: eins für das Anwendungsgateway und eins für die Back-End-Server. Sie können ein virtuelles Netzwerk zum gleichen Zeitpunkt erstellen wie das Anwendungsgateway.

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf **Neu**.
2. Klicken Sie auf **Netzwerk** und dann in der Liste der ausgewählten Elemente auf **Application Gateway**.
3. Geben Sie die folgenden Werte für das Anwendungsgateway ein:

   - *myAppGateway*: Name des Anwendungsgateways
   - *myResourceGroupAG*: neue Ressourcengruppe

     ![Erstellen eines neuen Anwendungsgateways](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
5. Klicken Sie auf **Virtuelles Netzwerk auswählen** und dann auf **Neu erstellen**, und geben Sie dann die folgenden Werte für das virtuelle Netzwerk ein:

   - *myVNet*: Name des virtuellen Netzwerks
   - *10.0.0.0/16*: Adressraum des virtuellen Netzwerks
   - *myAGSubnet*: Subnetzname
   - *10.0.0.0/24*: Adressraum des Subnetzes

     ![Virtuelles Netzwerk erstellen](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Klicken Sie auf **OK**, um das virtuelle Netzwerk und das Subnetz zu erstellen.
7. Klicken Sie auf **Öffentliche IP-Adresse auswählen** und dann auf **Neu erstellen**, und geben Sie den Namen der öffentlichen IP-Adresse ein. In diesem Beispiel heißt die öffentliche IP-Adresse *myAGPublicIPAddress*. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und klicken Sie auf **OK**.
8. Übernehmen Sie die Standardwerte für die Listenerkonfiguration, lassen Sie die Web Application Firewall deaktiviert, und klicken Sie dann auf **OK**.
9. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **OK**, um die Netzwerkressourcen und das Anwendungsgateway zu erstellen. Die Erstellung des Anwendungsgateways kann einige Minuten dauern. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myVNet**.
2. Klicken Sie auf **Subnetze** und dann auf **Subnetz**.

    ![Erstellen eines Subnetzes](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Geben Sie als Name des Subnetzes *myBackendSubnet* ein, und klicken Sie auf **OK**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Beispiel erstellen Sie zwei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, dass der Datenverkehr ordnungsgemäß geroutet wird.

1. Klicken Sie auf **New**.
2. Klicken Sie auf **Compute**, und wählen Sie dann in der Liste der ausgewählten Elemente die Option **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:

    - *contosoVM*: Name des virtuellen Computers
    - *azureuser*: Name des Administratorbenutzers
    - *Azure123456!* als Kennwort
    - Wählen Sie **Vorhandene verwenden** und dann *myResourceGroupAG* aus.

4. Klicken Sie auf **OK**.
5. Wählen Sie als Größe des virtuellen Computers **DS1_V2** aus, und klicken Sie auf **Auswählen**.
6. Stellen Sie sicher, dass als virtuelles Netzwerk **myVNet** und als Subnetz **myBackendSubnet** ausgewählt ist. 
7. Klicken Sie auf **Deaktiviert**, um die Startdiagnose zu deaktivieren.
8. Klicken Sie auf **OK**, überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **Erstellen**.

### <a name="install-iis"></a>Installieren von IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Öffnen Sie die interaktive Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.

    ![Installieren der benutzerdefinierten Erweiterung](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Erstellen Sie den zweiten virtuellen Computer, und installieren Sie IIS mithilfe der soeben ausgeführten Schritte. Geben Sie die Namen von *fabrikamVM* als Namen und als Wert für „VMName“ in „Set-AzVMExtension“ ein.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Erstellen von Back-End-Pools mit den virtuellen Computern

1. Klicken Sie auf **Alle Ressourcen** und dann auf **myAppGateway**.
2. Klicken Sie auf **Back-End-Pools** und dann auf **Hinzufügen**.
3. Geben Sie als Name *contosoPool* ein, und fügen Sie mithilfe von **Ziel hinzufügen** die VM *contosoVM* hinzu.

    ![Hinzufügen von Back-End-Servern](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Klicken Sie auf **OK**.
5. Klicken Sie auf **Back-End-Pools** und dann auf **Hinzufügen**.
6. Erstellen Sie unter Verwendung der soeben ausgeführten Schritte den Pool *fabrikamPool* mit der VM *fabrikamVM*.

## <a name="create-listeners-and-routing-rules"></a>Erstellen der Listener und Routingregeln

1. Klicken Sie auf **Listener** und anschließend auf **Für mehrere Standorte**.
2. Geben Sie die folgenden Werte für den Listener ein:
    
   - *contosoListener*: Verwenden Sie diesen Name für den Listener.
   - *www\.contoso.com*: Ersetzen Sie diesen Beispielhostnamen durch Ihren Domänennamen.

3. Klicken Sie auf **OK**.
4. Erstellen Sie einen zweiten Listener mit dem Namen *fabrikamListener*, und verwenden Sie hierfür Ihren zweiten Domänennamen. In diesem Beispiel wird *www\.fabrikam.com* verwendet.

Regeln werden in der Reihenfolge verarbeitet, in der sie aufgeführt sind, wobei Datenverkehr gemäß der ersten erfüllten Regel unabhängig von der Genauigkeit weitergeleitet wird. Wenn Sie beispielsweise eine Regel mit einem einfachen Listener und eine Regel mit einem Listener für mehrere Standorte auf demselben Port aktiviert haben, muss die Regel mit dem Listener für mehrere Standorte vor der Regel mit dem einfachen Listener aufgeführt sein, damit die Regel für mehrere Standorte wie erwartet funktioniert. 

In diesem Beispiel erstellen Sie zwei neue Regeln und löschen die Standardregel, die während der Erstellung des Anwendungsgateways erstellt wurde. 

1. Klicken Sie auf **Regeln** und anschließend auf **Basis**.
2. Geben Sie als Name *contosoRule* ein.
3. Wählen Sie für den Listener *contosoListener* aus.
4. Wählen Sie für den Back-End-Pool *contosoPool* aus.

    ![Erstellen einer pfadbasierten Regel](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Klicken Sie auf **OK**.
6. Erstellen Sie eine zweite Regel unter Verwendung der Namen *fabrikamRule*, *fabrikamListener* und *fabrikamPool*.
7. Löschen Sie die Standardregel namens *rule1*, indem Sie darauf klicken und dann **Löschen** auswählen.

## <a name="create-a-cname-record-in-your-domain"></a>Erstellen eines CNAME-Eintrags in Ihrer Domäne

Nachdem das Anwendungsgateway mit der zugehörigen öffentlichen IP-Adresse erstellt wurde, können Sie die DNS-Adresse abrufen und zum Erstellen eines CNAME-Eintrags in Ihrer Domäne verwenden. Die Verwendung von A-Einträgen wird nicht empfohlen, weil sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

1. Klicken Sie auf **Alle Ressourcen** und dann auf **myAGPublicIPAddress**.

    ![Notieren der DNS-Adresse des Anwendungsgateways](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Kopieren Sie die DNS-Adresse, und verwenden Sie sie als Wert für einen neuen CNAME-Eintrag in Ihrer Domäne.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Geben Sie Ihren Domänennamen in die Adressleiste Ihres Browsers ein. Z.B. http://www.contoso.com.

    ![Testen der Contoso-Website im Anwendungsgateway](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Ändern Sie die Adresse in Ihre andere Domäne. Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Testen der Fabrikam-Website im Anwendungsgateway](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Anwendungsgateways
> * Erstellen von virtuellen Computern für Back-End-Server
> * Erstellen von Back-End-Pools mit den Back-End-Servern
> * Erstellen der Listener und Routingregeln
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, was Sie mit dem Anwendungsgateway tun können.](application-gateway-introduction.md)
