---
title: Erstellen eines benutzerdefinierten Tests mithilfe des klassischen Bereitstellungsmodells – Azure Application Gateway
description: Erfahren Sie, wie Sie mithilfe von PowerShell einen benutzerdefinierten Test für ein Application Gateway im klassischen Bereitstellungsmodell erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397857"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Erstellen eines benutzerdefinierten Tests für ein Azure Application Gateway (klassisch) mithilfe von PowerShell

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klassische Azure PowerShell](application-gateway-create-probe-classic-ps.md)

In diesem Artikel fügen Sie einen benutzerdefinierten Test zu einem vorhandenen Anwendungsgateway mit PowerShell hinzu. Benutzerdefinierte Tests sind für Anwendungen, die über eine bestimmte Seite für die Integritätsprüfung verfügen, oder für Anwendungen hilfreich, die keine erfolgreiche Antwort für die Standardwebanwendung bereitstellen.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../azure-resource-manager/management/deployment-models.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Erfahren Sie, wie Sie [diese Schritte mit dem Resource Manager-Modell ausführen](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

So erstellen Sie ein Application Gateway

1. Erstellen einer Application Gateway-Ressource
2. Erstellen Sie eine XML-Konfigurationsdatei oder ein Konfigurationsobjekt.
3. Ordnen Sie die Konfiguration der neu erstellten Anwendungsgatewayressource zu.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Erstellen einer Anwendungsgatewayressource mit benutzerdefiniertem Test

Erstellen Sie das Gateway mithilfe des Cmdlets `New-AzureApplicationGateway`. Ersetzen Sie dabei die Werte durch eigene Werte. Die Abrechnung für das Gateway beginnt jetzt noch nicht. Die Abrechnung beginnt in einem späteren Schritt, wenn das Gateway erfolgreich gestartet wurde.

Das folgende Beispiel erstellt ein Anwendungsgateway mithilfe eines virtuellen Netzwerks mit dem Namen „testvnet1“ und eines Subnetzes mit dem Namen „subnet-1“.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Mithilfe des Cmdlets `Get-AzureApplicationGateway` können Sie überprüfen, ob das Gateway erstellt wurde.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Small“, „Medium“ und „Large“ wählen.
> 
> 

*VirtualIPs* und *DnsName* werden leer angezeigt, da das Gateway noch nicht gestartet wurde. Diese Werte werden erstellt, sobald das Gateway ausgeführt wird.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurieren des Application Gateways per XML

Im folgenden Beispiel verwenden Sie eine XML-Datei, um alle Einstellungen des Anwendungsgateways zu konfigurieren und auf die Anwendungsgatewayressource zu übertragen.  

Kopieren Sie den folgenden Text in Editor.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Bearbeiten Sie die Werte zwischen den Klammern für die Konfigurationselemente. Speichern Sie die Datei mit der Erweiterung XML.

Das folgende Beispiel zeigt, wie Sie mithilfe einer Konfigurationsdatei das Application Gateway für den Lastenausgleich von HTTP-Datenverkehr am öffentlichen Port 80 und zum Senden des Netzwerkdatenverkehrs an den Back-End-Port 80 zwischen zwei IP-Adressen einrichten, indem Sie einen benutzerdefinierten Test verwenden.

> [!IMPORTANT]
> Für die Protokollelemente Http oder Https muss die Groß-/Kleinschreibung beachtet werden.

Es wird ein neues Konfigurationselement vom Typ \<Probe\> hinzugefügt, um benutzerdefinierte Tests zu konfigurieren.

Die Konfigurationsparameter sind:

|Parameter|BESCHREIBUNG|
|---|---|
|**Name** |Referenzname für den benutzerdefinierten Test. |
| **Protokoll** | Verwendetes Protokoll (mögliche Werte: „HTTP“ und „HTTPS“).|
| **Host** und **Pfad** | Vollständiger URL-Pfad, der vom Anwendungsgateway aufgerufen wird, um die Integrität der Instanz zu ermitteln. Beispiel: Für die Website „http:\//contoso.com/“ können Sie den benutzerdefinierten Test für „http:\//contoso.com/path/custompath.htm“ konfigurieren, damit die HTTP-Antwort bei den Überprüfungen des Tests erfolgreich ist.|
| **Intervall** | Konfiguriert die Intervalle der Testausführungen (in Sekunden).|
| **Timeout** | Definiert das Timeout des Tests für eine HTTP-Antwortprüfung.|
| **UnhealthyThreshold** | Die Anzahl von HTTP-Antworten mit Fehlern, ab der die Back-End-Instanz als *fehlerhaft* gekennzeichnet wird.|

Auf den Namen des Tests wird in der Konfiguration \<BackendHttpSettings\> verwiesen, um festzulegen, welcher Back-End-Pool die Einstellungen für den benutzerdefinierten Test verwenden soll.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Hinzufügen eines benutzerdefinierten Tests zu einem vorhandenen Anwendungsgateway

Zum Ändern der aktuellen Konfiguration eines Anwendungsgateways sind drei Schritte nötig: das Abrufen der aktuellen XML-Konfigurationsdatei, das Ändern für einen benutzerdefinierten Test und das Konfigurieren des Anwendungsgateways mit den neuen XML-Einstellungen.

1. Rufen Sie die XML-Datei mit `Get-AzureApplicationGatewayConfig` ab. Dieses Cmdlet exportiert die XML-Konfigurationsdatei, damit ihr die Einstellungen für die Überprüfung hinzugefügt werden können.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Öffnen Sie die XML-Datei in einem Texteditor. Fügen Sie nach `<frontendport>` den Abschnitt `<probe>` hinzu.

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   Fügen Sie den Namen des Tests im Abschnitt „backendHttpSettings“ der XML-Datei wie im folgenden Beispiel hinzu:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Speichern Sie die XML-Datei.

1. Aktualisieren Sie die Konfiguration des Anwendungsgateways mit der neuen XML-Datei, und verwenden Sie dazu `Set-AzureApplicationGatewayConfig`. Dieses Cmdlet aktualisiert das Anwendungsgateway mit der neuen Konfiguration.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Transport Layer Security (TLS) konfigurieren möchten, früher als SSL-Auslagerung (Secure Sockets Layer) bekannt, lesen Sie den Abschnitt [Konfigurieren eines Anwendungsgateways für die TLS-Auslagerung](./tutorial-ssl-powershell.md).

Wenn Sie ein Anwendungsgateway für die Verwendung mit einem internen Lastenausgleich konfigurieren möchten, lesen Sie den Abschnitt [Erstellen eines Anwendungsgateways mit einem internen Lastenausgleich (ILB)](./application-gateway-ilb-arm.md).