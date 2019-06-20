---
title: Erstellen eines Azure Application Gateways – Vorlagen
description: Dieser Artikel enthält Anweisungen zum Erstellen eines Azure-Anwendungsgateways mit einer Azure Resource Manager-Vorlage.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 29b607f10c15ba35a515b55b059b7b55ff594fe6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135760"
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Erstellen eines Anwendungsgateways mit der Azure-Ressourcen-Manager-Vorlage

Azure Application Gateway verwendet einen Load Balancer auf der Schicht 7 (Anwendungsschicht). Es ermöglicht Failover sowie schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung. Application Gateway bietet zahlreiche ADC-Features (Application Delivery Controller) wie HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität, SSL-Auslagerung (Secure Sockets Layer), benutzerdefinierte Integritätstests und Unterstützung mehrerer Standorte. Eine Liste mit allen unterstützten Features finden Sie unter [Übersicht über Application Gateway](application-gateway-introduction.md).

In diesem Artikel erfahren Sie, wie Sie eine vorhandene [Azure Resource Manager-Vorlage](../azure-resource-manager/resource-group-authoring-templates.md) von GitHub herunterladen und ändern und wie Sie die Vorlage über GitHub, PowerShell und die Azure-Befehlszeilenschnittstelle bereitstellen.

Wenn Sie die Vorlage ohne Änderungen direkt aus GitHub bereitstellen, fahren Sie mit „Bereitstellen einer Vorlage aus GitHub“ fort.

## <a name="scenario"></a>Szenario

In diesem Szenario führen Sie Folgendes durch:

* Erstellen eines Anwendungsgateways mit der Web Application Firewall.
* Sie erstellen ein virtuelles Netzwerk mit dem Namen VirtualNetwork1 und dem reservierten CIDR-Block 10.0.0.0/16.
* Sie erstellen ein Subnetz mit dem Namen Appgatewaysubnet, für das 10.0.0.0/28 als CIDR-Block verwendet wird.
* Sie richten zwei zuvor konfigurierte Back-End-IP-Adressen für die Webserver ein, die zum Durchführen des Lastenausgleichs verwendet werden sollen. In diesem Vorlagenbeispiel werden die Back-End-IP-Adressen 10.0.1.10 und 10.0.1.11 verwendet.

> [!NOTE]
> Diese Einstellungen sind die Parameter für diese Vorlage. Zum Anpassen der Vorlage können Sie Regeln, den Listener, SSL und andere Optionen in der Datei „azuredeploy.json“ ändern.

![Szenario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Herunterladen und Verstehen der Azure-Ressourcen-Manager-Vorlage

Sie können die vorhandene Azure-Ressourcen-Manager-Vorlage herunterladen, um ein virtuelles Netzwerk und zwei Subnetze aus GitHub zu erstellen. Anschließend können Sie die gewünschten Änderungen vornehmen und die Vorlage bei Bedarf wiederverwenden. Führen Sie dazu die folgenden Schritte aus:

1. Navigieren Sie zu [Erstellen eines Anwendungsgateways mit aktiver Web Application Firewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Klicken Sie auf **azuredeploy.json**, und klicken Sie dann auf **RAW**.
1. Speichern Sie die Datei in einem lokalen Ordner auf Ihrem Computer.
1. Fahren Sie mit Schritt 7 fort, wenn Sie mit Azure-Ressourcen-Manager-Vorlagen bereits vertraut sind.
1. Öffnen Sie die Datei, die Sie gespeichert haben, und betrachten Sie den Inhalt unter **parameters** in Zeile
1. Die Parameter der Azure-Ressourcen-Manager-Vorlage stellen Platzhalter für Werte dar, die während der Bereitstellung ausgefüllt werden können.

   | Parameter | BESCHREIBUNG |
   | --- | --- |
   | **subnetPrefix** |CIDR-Block für das Anwendungsgateway-Subnetz |
   | **applicationGatewaySize** | Größe des Anwendungsgateways.  WAF lässt nur mittelgroß und groß zu. |
   | **backendIpaddress1** |IP-Adresse des ersten Webservers. |
   | **backendIpaddress2** |IP-Adresse des zweiten Webservers. |
   | **wafEnabled** | Festlegung, ob WAF aktiviert ist.|
   | **wafMode** | Modus der Web Application Firewall.  Verfügbare Optionen sind **Prävention** und **Erkennung**.|
   | **wafRuleSetType** | Regelsatztyp für WAF.  Derzeit die OWASP die einzige unterstützte Option. |
   | **wafRuleSetVersion** |Regelsatzversion. Die unterstützten Optionen sind derzeit OWASP CRS 2.2.9 und 3.0. |

1. Überprüfen Sie den Inhalt unter **resources**, und beachten Sie folgende Eigenschaften:

   * **type**. Typ der Ressource, die von der Vorlage erstellt wird. In diesem Fall lautet der Typ `Microsoft.Network/applicationGateways` (ein Anwendungsgateway).
   * **name**. Name der Ressource. Beachten Sie die Verwendung von `[parameters('applicationGatewayName')]`. Das bedeutet, dass der Name während der Bereitstellung durch Sie oder eine Parameterdatei eingegeben wird.
   * **properties**. Liste der Eigenschaften für die Ressource. Diese Vorlage verwendet während der Erstellung des Anwendungsgateways das virtuelle Netzwerk und die öffentliche IP-Adresse.

1. Navigieren Sie zurück zu [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Klicken Sie auf **azuredeploy-parameters.json** und anschließend auf **RAW**.
1. Speichern Sie die Datei in einem lokalen Ordner auf Ihrem Computer.
1. Öffnen Sie die Datei, die Sie gespeichert haben, und bearbeiten Sie die Parameterwerte. Verwenden Sie die folgenden Werte, um das Anwendungsgateway wie in unserem Szenario beschrieben bereitzustellen.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "addressPrefix": {
             "value": "10.0.0.0/16"
             },
             "subnetPrefix": {
             "value": "10.0.0.0/28"
             },
             "applicationGatewaySize": {
             "value": "WAF_Medium"
             },
             "capacity": {
             "value": 2
             },
             "backendIpAddress1": {
             "value": "10.0.1.10"
             },
             "backendIpAddress2": {
             "value": "10.0.1.11"
             },
             "wafEnabled": {
             "value": true
             },
             "wafMode": {
             "value": "Detection"
             },
             "wafRuleSetType": {
             "value": "OWASP"
             },
             "wafRuleSetVersion": {
             "value": "3.0"
             }
         }
     }
     ```

1. Speichern Sie die Datei . Sie können die JSON-Vorlage und die Parametervorlage mithilfe von online verfügbaren JSON-Validierungstools wie [JSlint.com](https://www.jslint.com/)testen.

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Bereitstellen der Azure-Ressourcen-Manager-Vorlage mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Sie Azure PowerShell zuvor noch nicht verwendet haben, besuchen Sie: [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview), und befolgen Sie die Anweisungen, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.

1. Anmeldung bei PowerShell

    ```powershell
    Connect-AzAccount
    ```

1. Überprüfen Sie die Abonnements für das Konto.

    ```powershell
    Get-AzSubscription
    ```

    Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.

1. Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    ```powershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. Erstellen Sie bei Bedarf eine Ressourcengruppe mit dem Cmdlet **New-AzureResourceGroup** . Im folgenden Beispiel erstellen Sie eine Ressourcengruppe mit dem Namen AppgatewayRG in der Region „USA, Osten“.

    ```powershell
    New-AzResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Führen Sie das Cmdlet **New-AzResourceGroupDeployment** aus, um das neue virtuelle Netzwerk mit der zuvor heruntergeladenen und geänderten Vorlage und den Parameterdateien bereitzustellen.
    
    ```powershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Bereitstellen der Azure-Ressourcen-Manager-Vorlage mit der Azure-Befehlszeilenschnittstelle

Führen Sie die folgenden Schritte aus, um die heruntergeladene Azure Resource Manager-Vorlage mithilfe der Azure-Befehlszeilenschnittstelle bereitzustellen:

1. Wenn Sie die Azure-CLI noch nie verwendet haben, lesen Sie den Artikel [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) , und befolgen Sie die Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

1. Führen Sie bei Bedarf den Befehl `az group create` aus, um eine neue Ressourcengruppe zu erstellen, wie im folgenden Codeausschnitt zu sehen. Beachten Sie die Ausgabe des Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (oder --name)** . Name für die neue Ressourcengruppe. In unserem Fall ist dies *appgatewayRG*.
    
    **-l (oder --location)** . Azure-Region, in der die neue Ressourcengruppe erstellt wird. In unserem Szenario ist dies *westus*.

1. Führen Sie das Cmdlet `az group deployment create` aus, um das neue virtuelle Netzwerk mithilfe der im vorherigen Schritt heruntergeladenen und geänderten Vorlage und Parameterdateien bereitzustellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Bereitstellen der Azure-Ressourcen-Manager-Vorlage per Click-to-Deploy

Click-to-Deploy ist eine weitere Option zum Verwenden von Azure-Ressourcen-Manager-Vorlagen. Diese Art der Bereitstellung bietet eine einfache Möglichkeit, Vorlagen im Azure-Portal zu verwenden.

1. Wechseln Sie zu [Erstellen eines Anwendungsgateways mit der Web Application Firewall](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.

    ![Bereitstellen in Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Füllen Sie im Portal die Parameter für die Bereitstellungsvorlage aus, und klicken Sie auf **OK**.

    ![Parameter](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** aus, und klicken Sie auf **Kaufen**.

1. Klicken Sie auf dem Blatt „Benutzerdefinierte Bereitstellung“ auf **Erstellen**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Bereitstellen von Zertifikatdaten für Resource Manager-Vorlagen

Wenn Sie SSL mit einer Vorlage verwenden, muss das Zertifikat nicht hochgeladen, sondern in einer base64-Zeichenfolge bereitgestellt werden. Führen Sie zum Konvertieren einer PFX- oder CER-Datei in eine base64-Zeichenfolge einen der folgenden Befehle aus. Mit den folgenden Befehlen wird das Zertifikat in eine base64-Zeichenfolge konvertiert, die der Vorlage bereitgestellt werden kann. Die erwartete Ausgabe ist eine Zeichenfolge, die in einer Variablen gespeichert und in die Vorlage eingefügt werden kann.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Löschen aller Ressourcen

Führen Sie einen der folgenden Schritte aus, um alle Ressourcen zu löschen, die in diesem Artikel erstellt wurden:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, besuchen Sie: [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Anwendungsgateway für die Verwendung mit einem internen Lastenausgleich konfigurieren möchten, besuchen Sie: [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md).

Weitere grundsätzliche Informationen zu Lastenausgleichsoptionen finden Sie unter:

* [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)