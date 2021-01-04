---
title: Beheben von Problemen bei der Umleitung zur App Service-URL
titleSuffix: Azure Application Gateway
description: Dieser Artikel enthält Informationen dazu, wie sich das Umleitungsproblem beheben lässt, wenn Azure Application Gateway mit Azure App Service verwendet wird.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182885"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Behandeln von App Service-Problemen in Application Gateway

Es wird beschrieben, wie Sie Probleme diagnostizieren und beheben, die ggf. auftreten, wenn Azure App Service als Back-End-Ziel mit Azure Application Gateway verwendet wird.

## <a name="overview"></a>Übersicht

In diesem Artikel erfahren Sie, wie Sie die folgenden Probleme beheben:

* Die App-Dienst-URL wird im Browser verfügbar gemacht, wenn eine Umleitung durchgeführt wird.
* Die ARRAffinity-Cookiedomäne des App-Diensts ist auf den App-Dienst-Hostnamen „example.azurewebsites.net“ festgelegt, und nicht auf den ursprünglichen Host.

Wenn eine Back-End-Anwendung eine Umleitungsantwort sendet, empfiehlt es sich u.U., den Client an eine andere als die von der Back-End-Anwendung angegebene URL umzuleiten. Dies kann ratsam sein, wenn ein App-Dienst hinter einem Anwendungsgateway gehostet wird und erfordert, dass der Client eine Umleitung zu seinem relativen Pfad durchführt. Ein Beispiel ist eine Umleitung von „contoso.azurewebsites.net/path1“ zu „contoso.azurewebsites.net/path2“. 

Wenn der App-Dienst eine Umleitungsantwort sendet, verwendet er im Adressheader seiner Antwort den gleichen Hostnamen wie in der Anforderung, die er vom Anwendungsgateway empfängt. Der Client sendet die Anforderung beispielsweise direkt an „contoso.azurewebsites.net/path2“ und durchläuft nicht das Anwendungsgateway „contoso.com/path2“. Es kann ratsam sein, das Anwendungsgateway nicht zu umgehen.

Dieses Problem kann folgende Hauptursachen haben:

- Sie haben für Ihren App-Dienst eine Umleitung konfiguriert. Diese kann im einfachsten Fall auf einen nachgestellten Schrägstrich zurückzuführen sein, der der Anforderung hinzugefügt wurde.
- Die Umleitung wird durch die Nutzung der Azure Active Directory-Authentifizierung ausgelöst.

Wenn Sie App-Dienste hinter einem Anwendungsgateway verwenden, unterscheidet sich der Domänenname, der dem Anwendungsgateway (example.com) zugeordnet ist, zudem vom Domänennamen des App-Diensts (z. B. „example.azurewebsites.net“). Der Domänenwert für das vom App-Dienst festgelegte ARRAffinity-Cookie enthält den Domänennamen „example.azurewebsites.net“. Dies ist nicht erwünscht. Der ursprüngliche Hostname „example.com“ sollte dem Domänennamenwert im Cookie entsprechen.

## <a name="sample-configuration"></a>Beispielkonfiguration

- HTTP-Listener: Basislistener oder Listener für mehrere Standorte
- Back-End-Adresspool: App Service
- HTTP-Einstellungen: **Pick Hostname from Backend Address** (Hostnamen aus Back-End-Adresse auswählen) aktiviert
- Test: **Pick Hostname from Backend HTTP Settings** (Hostnamen aus Back-End-HTTP-Einstellungen auswählen) aktiviert

## <a name="cause"></a>Ursache

Da App Service ein mehrinstanzenfähiger Dienst ist, nutzt er den Hostheader in der Anforderung zur Weiterleitung der Anforderung an den richtigen Endpunkt. Der Standarddomänenname von App Service („*.azurewebsites.net“, z. B. „contoso.azurewebsites.net“) unterscheidet sich vom Domänennamen des Anwendungsgateways (z. B. „contoso.com“). 

Die ursprüngliche Anforderung vom Client weist als Hostname den Domänennamen des Anwendungsgateways („contoso.com“) auf. Sie müssen das Anwendungsgateway so konfigurieren, dass der Hostname in der ursprünglichen Anforderung in den Hostnamen des App-Diensts geändert wird, wenn die Anforderung an das Back-End des App-Diensts geleitet wird. Verwenden Sie den Switch **Hostnamen aus Back-End-Adresse auswählen** in der HTTP-Einstellungskonfiguration des Anwendungsgateways. Verwenden Sie den Switch **Hostnamen aus Back-End-HTTP-Einstellungen auswählen** in der Integritätstestkonfiguration.



![Änderung des Hostnamens durch das Anwendungsgateway](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Wenn der App-Dienst eine Umleitung durchführt, wird anstelle des ursprünglichen Hostnamens „contoso.com“ der außer Kraft gesetzte Hostname „contoso.azurewebsites.net“ im Adressheader verwendet, falls keine andere Konfiguration vorgenommen wurde. Sehen Sie sich die folgende Beispielanforderung und die Antwortheader an.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Beachten Sie im vorherigen Beispiel, dass der Antwortheader über den Statuscode 301 für die Umleitung verfügt. Der Adressheader enthält den Hostnamen des App-Diensts und nicht den ursprünglichen Hostnamen `www.contoso.com`.

## <a name="solution-rewrite-the-location-header"></a>Lösung: Erneutes Generieren des Adressheaders

Legen Sie den Hostnamen im Adressheader auf den Domänennamen des Anwendungsgateways fest. Erstellen Sie hierzu eine [Regel zum erneuten Generieren](./rewrite-http-headers.md) mit einer Bedingung, die prüft, ob der Adressheader in der Antwort „azurewebsites.net“ enthält. Außerdem muss eine Aktion zum erneuten Generieren des Adressheaders durchgeführt werden, damit der Hostname des Anwendungsgateways vorhanden ist. Weitere Informationen finden Sie in der Anleitung unter [Ändern einer Umleitungs-URL](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Die Unterstützung für das erneute Generieren von HTTP-Headern ist nur für die [Standard_v2- und WAF_v2-SKU](./application-gateway-autoscaling-zone-redundant.md) von Application Gateway verfügbar. Bei Verwendung der v1-SKU empfehlen wir Ihnen die [Migration von v1 zu v2](./migrate-v1-v2.md). Es ist ratsam, das erneute Generieren und andere [erweiterte Funktionen](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) zu nutzen, die für die v2-SKU verfügbar sind.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativlösung: Verwenden eines benutzerdefinierten Domänennamens

Bei Verwendung der v1-SKU können Sie den Adressheader nicht erneut generieren. Diese Funktion ist nur für die v2-SKU verfügbar. Übergeben Sie zum Beheben des Umleitungsproblems den gleichen Hostnamen, der vom Anwendungsgateway empfangen wird, auch an den App-Dienst, anstatt eine Außerkraftsetzung des Hosts durchzuführen.

Der App-Dienst führt die Umleitung (falls zutreffend) dann nicht mit dem eigenen Header, sondern mit dem ursprünglichen Hostheader durch, der auf das Anwendungsgateway verweist.

Sie müssen über eine benutzerdefinierte Domäne verfügen und diesen Prozess verwenden:

- Tragen Sie die Domäne in die Liste mit den benutzerdefinierten Domänen des App-Diensts ein. Sie benötigen in Ihrer benutzerdefinierten Domäne einen CNAME-Eintrag, der auf den FQDN des App-Diensts verweist. Weitere Informationen finden Sie unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

    ![Liste mit benutzerdefinierten Domänen für App-Dienst](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Ihr App-Dienst kann nun den Hostnamen `www.contoso.com` akzeptieren. Ändern Sie nun den CNAME-Eintrag im DNS so, dass er wieder auf den FQDN von Application Gateway verweist, z. B. `appgw.eastus.cloudapp.azure.com`.

- Stellen Sie sicher, dass die Domäne `www.contoso.com` in den FQDN des Anwendungsgateways aufgelöst wird, wenn Sie eine DNS-Abfrage durchführen.

- Legen Sie für Ihren benutzerdefinierten Test fest, dass die Option **Pick Hostname from Backend HTTP Settings** (Hostnamen aus Back-End-HTTP-Einstellungen auswählen) deaktiviert wird. Deaktivieren Sie im Azure-Portal das Kontrollkästchen in den Einstellungen für den Test. Verwenden Sie in PowerShell nicht den Switch **-PickHostNameFromBackendHttpSettings** im Befehl **Set-AzApplicationGatewayProbeConfig**. Geben Sie im Hostnamenfeld des Tests den FQDN Ihres App-Diensts ein, z. B. „example.azurewebsites.net“. Die vom Anwendungsgateway gesendeten Testanforderungen enthalten diesen FQDN im Hostheader.

  > [!NOTE]
  > Stellen Sie im nächsten Schritt sicher, dass Ihr benutzerdefinierter Test nicht den HTTP-Einstellungen Ihres Back-Ends zugeordnet ist. Für Ihre HTTP-Einstellungen ist an diesem Punkt noch der Switch **Hostnamen aus Back-End-Adresse auswählen** aktiviert.

- Passen Sie die HTTP-Einstellungen des Anwendungsgateways so an, dass **Hostnamen aus Back-End-Adresse auswählen** deaktiviert wird. Deaktivieren Sie das Kontrollkästchen im Azure-Portal. Verwenden Sie in PowerShell nicht den Switch **-PickHostNameFromBackendAddress** im Befehl **Set-AzApplicationGatewayBackendHttpSettings**.

- Ordnen Sie den benutzerdefinierten Test wieder den Back-End-HTTP-Einstellungen zu, und vergewissern Sie sich, dass das Back-End fehlerfrei ist.

- Das Anwendungsgateway sollte jetzt den gleichen Hostnamen `www.contoso.com` an den App-Dienst weiterleiten. Die Umleitung erfolgt unter demselben Hostnamen. Sehen Sie sich die folgende Beispielanforderung und die Antwortheader an.

Verwenden Sie das folgende PowerShell-Beispielskript, um die obigen Schritte für ein vorhandenes Setup mit PowerShell zu implementieren. Beachten Sie, dass die **-PickHostname**-Switches in der Konfiguration für die Test- und HTTP-Einstellungen nicht verwendet wurden.

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

Wenn sich das Problem mit den obigen Schritten nicht beheben lässt, sollten Sie ein [Supportticket](https://azure.microsoft.com/support/options/) erstellen.