---
title: 'Problembehandlung für Azure Application Gateway mit App Service: Umleitung zur App Service-URL'
description: Dieser Artikel enthält Informationen dazu, wie sich das Umleitungsproblem beheben lässt, wenn Azure Application Gateway mit Azure App Service verwendet wird.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715196"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>Problembehandlung für Application Gateway mit App Service

Erfahren Sie, wie Sie aufgetretene Probleme bei Application Gateway und App Service als Back-End-Server diagnostizieren und beheben können.

## <a name="overview"></a>Übersicht

In diesem Artikel erfahren Sie, wie Sie die folgenden Probleme beheben können:

> [!div class="checklist"]
> * App Service-URL wird bei einer Umleitung im Browser angezeigt
> * ARRAffinity-Cookiedomäne von App Service ist auf App Service-Hostname (example.azurewebsites.net) und nicht auf ursprünglichen Host festgelegt

Wenn Sie eine öffentliche App Service-Instanz im Back-End-Pool von Application Gateway konfigurieren und im Anwendungscode eine Umleitung eingerichtet ist, werden Sie beim Zugriff auf Application Gateway möglicherweise vom Browser direkt zur App Service-URL umgeleitet.

Dieses Problem kann folgende Hauptursachen haben:

- Sie haben für App Service eine Umleitung konfiguriert. Diese kann im einfachsten Fall auf einen nachgestellten Schrägstrich zurückzuführen sein, der der Anforderung hinzugefügt wurde.
- Die Umleitung wird durch die Nutzung der Azure AD-Authentifizierung ausgelöst.
- Sie haben in den HTTP-Einstellungen von Application Gateway die Option „Hostnamen aus Back-End-Adresse auswählen“ aktiviert.
- Sie haben Ihre benutzerdefinierte Domäne nicht bei App Service registriert.

Auch wenn Sie App Services hinter Application Gateway und für den Zugriff auf Application Gateway eine benutzerdefinierte Domäne verwenden, entspricht der Domänenwert für das von App Service festgelegte ARRAffinity-Cookie möglicherweise dem Domänennamen „example.azurewebsites.net“. Wenn der ursprüngliche Hostname auch für die Cookiedomäne verwendet werden soll, befolgen Sie die Anweisungen in der Lösung zur Problembehandlung in diesem Artikel.

## <a name="sample-configuration"></a>Beispielkonfiguration

- HTTP-Listener: Basislistener oder Listener für mehrere Standorte
- Back-End-Adresspool: App Service
- HTTP-Einstellungen: „Hostnamen aus Back-End-Adresse auswählen“ aktiviert
- Test: „Pick Hostname from Backend HTTP Settings“ (Hostnamen aus Back-End-HTTP-Einstellungen auswählen)

## <a name="cause"></a>Ursache

Auf eine App Service-Instanz kann nur über die konfigurierten Hostnamen zugegriffen werden, die sich in den Einstellungen der benutzerdefinierten Domäne befinden. Standardmäßig lautet der Name „example.azurewebsites.net“. Wenn Sie mithilfe von Application Gateway und einem Hostnamen, der nicht in App Service registriert ist, oder mit dem FQDN von Application Gateway auf Ihre App Service-Instanz zugreifen möchten, müssen Sie den Hostnamen in der ursprünglichen Anforderung an den Hostnamen von App Service überschreiben.

Hierzu verwenden Sie für Application Gateway die Option „Hostnamen aus Back-End-Adresse auswählen“ in den HTTP-Einstellungen. Für die Testkonfiguration nutzen Sie „Pick Hostname from Backend HTTP Settings“ (Hostnamen aus Back-End-HTTP-Einstellungen auswählen).

![App Service (1)](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Wenn die App Service-Instanz nun eine Umleitung durchführt, wird anstelle des ursprünglichen Hostnamens „example.azurewebsites.net“ im Adressheader verwendet, falls keine andere Konfiguration vorgenommen wurde. Im Folgenden sehen Sie ein Beispiel für Anforderungs- und Antwortheader.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
Im obigen Beispiel verfügt der Antwortheader über den Statuscode 301 für die Umleitung. Außerdem entspricht der Adressheader nicht dem ursprünglichen Hostnamen „www.contoso.com“, sondern dem von App Service.

## <a name="solution"></a>Lösung

Sie können dieses Problem beheben, indem Sie auf eine Umleitung auf Anwendungsseite verzichten. Wenn dies nicht möglich ist, müssen Sie App Service denselben Hostheader übergeben, der von Application Gateway empfangen wird, anstatt den Hostnamen zu überschreiben.

Dadurch führt App Service Umleitungen nicht für den eigenen Header, sondern für denselben ursprünglichen Hostheader durch, der auf Application Gateway verweist.

Hierzu müssen Sie über eine benutzerdefinierte Domäne verfügen und die im Folgenden genannten Schritte ausführen:

- Tragen Sie die Domäne in die Liste der benutzerdefinierten Domänen von App Service ein. Dazu benötigen Sie in Ihrer benutzerdefinierten Domäne einen CNAME-Eintrag, der auf den FQDN von App Service verweist. Weitere Informationen finden Sie unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![App Service (2)](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Anschließend kann Ihrer App Service-Instanz der Hostname „www.contoso.com“ übergeben werden. Ändern Sie nun den CNAME-Eintrag im DNS so, dass er wieder auf den FQDN von Application Gateway verweist. Beispiel: „appgw.eastus.cloudapp.azure.com“.

- Stellen Sie sicher, dass die Domäne „www.contoso.com“ in den FQDN von Application Gateway aufgelöst wird, wenn Sie eine DNS-Abfrage ausführen.

- Deaktivieren Sie für Ihren benutzerdefinierten Test „Pick Hostname from Backend HTTP Settings“ (Hostnamen aus Back-End-HTTP-Einstellungen auswählen). Dazu können Sie im Azure-Portal in den Testeinstellungen das entsprechende Kontrollkästchen deaktivieren oder in PowerShell bei Nutzung des Befehls „Set-AzApplicationGatewayProbeConfig“ auf die Option „-PickHostNameFromBackendHttpSettings“ verzichten. Geben Sie im Feld für den Hostnamen des Tests den FQDN „example.azurewebsites.net“ von App Service ein, da die von Application Gateway gesendeten Testanforderungen diesen Namen im Hostheader enthalten.

  > [!NOTE]
  > Stellen Sie vor dem nächsten Schritt sicher, dass der benutzerdefinierte Test nicht Ihren Back-End-HTTP-Einstellungen zugeordnet ist. Für Ihre HTTP-Einstellungen ist zu diesem Zeitpunkt nach wie vor die Option „Hostnamen aus Back-End-Adresse auswählen“ aktiviert.

- Passen Sie die HTTP-Einstellungen von Application Gateway so an, dass „Hostnamen aus Back-End-Adresse auswählen“ deaktiviert wird. Dazu können Sie im Azure-Portal das entsprechende Kontrollkästchen deaktivieren oder in PowerShell bei Nutzung des Befehls „Set-AzApplicationGatewayBackendHttpSettings“ auf die Option „-PickHostNameFromBackendAddress“ verzichten.

- Ordnen Sie den benutzerdefinierten Test wieder den Back-End-HTTP-Einstellungen zu, und überprüfen Sie die Back-End-Integrität.

- Der Hostname „www.contoso.com“ sollte nun von Application Gateway an die App Service-Instanz weitergeleitet und außerdem für die Umleitung verwendet werden. Im Folgenden sehen Sie ein Beispiel für Anforderungs- und Antwortheader.

Um die oben beschriebenen Schritte unter Verwendung von PowerShell für ein vorhandenes Setup zu implementieren, führen Sie das nachfolgende PowerShell-Beispielskript aus. Beachten Sie, dass die „-PickHostname“-Switches in der Konfiguration für die Test- und HTTP-Einstellungen nicht verwendet wurden.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Nächste Schritte

Sollte sich das Problem mit den oben genannten Schritten nicht beheben lassen, erstellen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).
