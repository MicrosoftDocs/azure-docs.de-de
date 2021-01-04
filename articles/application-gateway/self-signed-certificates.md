---
title: Generieren eines selbstsignierten Zertifikats mit einer benutzerdefinierten Stammzertifizierungsstelle
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie ein selbstsigniertes Azure Application Gateway-Zertifikats mit einer benutzerdefinierten Stammzertifizierungsstelle generieren.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396939"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generieren eines selbstsignierten Azure Application Gateway-Zertifikats mit einer benutzerdefinierten Stammzertifizierungsstelle

Seit der Application Gateway v2-SKU können vertrauenswürdige Stammzertifikate verwendet werden, damit Back-End-Server zugelassen werden können. Hierdurch werden die Authentifizierungszertifikate ersetzt, die in der v1-SKU erforderlich sind. Das *Stammzertifikat* ist ein Base64-kodiertes Stammzertifikat im X.509-Format (.CER) vom Back-End-Zertifikatserver. Es kennzeichnet die Stammzertifizierungsstelle, die das Serverzertifikat ausgestellt hat. Das Serverzertifikat wird dann für die TLS/SSL-Kommunikation verwendet.

Application Gateway vertraut standardmäßig dem Zertifikat Ihrer Website, wenn es von einer bekannten Zertifizierungsstelle (etwa GoDaddy oder DigiCert) signiert ist. In diesem Fall müssen Sie das Stammzertifikat nicht explizit hochladen. Weitere Informationen finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](ssl-overview.md). Wenn Sie jedoch eine Entwicklungs- oder Testumgebung haben und kein verifiziertes, von einer Zertifizierungsstelle signiertes Zertifikat kaufen möchten, können Sie Ihre eigene benutzerdefinierte Zertifizierungsstelle und dann ein selbstsigniertes Zertifikat erstellen. 

> [!NOTE]
> Selbstsignierten Zertifikaten wird standardmäßig nicht vertraut, und es kann schwierig sein, sie zu verwalten. Außerdem kann es sein, dass für sie veraltete Hash- und Verschlüsselungssammlungen verwendet werden, die möglicherweise nicht stark sind. Um eine bessere Sicherheit zu erreichen, sollten Sie ein Zertifikat kaufen, das von einer bekannten Zertifizierungsstelle signiert ist.

In diesem Artikel wird Folgendes behandelt:

- Erstellen einer eigenen benutzerdefinierten Zertifizierungsstelle
- Erstellen eines selbstsignierten Zertifikats, das von Ihrer benutzerdefinierten Zertifizierungsstelle signiert wird
- Hochladen eines selbstsignierten Stammzertifikats auf ein Application Gateway, um den Back-End-Server zu authentifizieren

## <a name="prerequisites"></a>Voraussetzungen

- **[OpenSSL](https://www.openssl.org/) auf einem Computer mit Windows oder Linux** 

   Zwar gibt es möglicherweise andere Tools für die Zertifikatverwaltung, in diesem Tutorial wird aber OpenSSL verwendet. OpenSSL gehört zu vielen Linux-Distributionen, so z. B. Ubuntu.
- **Ein Webserver**

   Beispielsweise Apache, IIS oder NGINX, um die Zertifikate zu testen.

- **Eine Application Gateway v2-SKU**
   
  Wenn Sie kein vorhandenes Anwendungsgateway haben, lesen Sie [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>Erstellen eines Stammzertifikats der Zertifizierungsstelle

Erstellen Sie Ihr Stammzertifikat der Zertifizierungsstelle mit OpenSSL.

### <a name="create-the-root-key"></a>Erstellen des Stammschlüssels

1. Melden Sie sich bei dem Computer an, auf dem OpenSSL installiert ist, und führen Sie den folgenden Befehl aus. Dadurch wird ein kennwortgeschützter Schlüssel erstellt.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Geben Sie an der Eingabeaufforderung ein sicheres Kennwort ein. Beispielsweise mindestens neun Zeichen mit Großbuchstaben, Kleinbuchstaben, Zahlen und Symbolen.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Erstellen eines Stammzertifikats und Selbstsignieren dieses Zertifikats

1. Verwenden Sie die folgenden Befehle, um die Zertifikatsignieranforderung und das Zertifikat zu generieren.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Mit den vorherigen Befehlen wird das Stammzertifikat erstellt. Sie verwenden dieses Zertifikat, um Ihr Serverzertifikat zu signieren.

1. Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort für den Stammschlüssel und die Organisationsinformationen für die benutzerdefinierte Zertifizierungsstelle, z. B. Land, Bundesland/Region, Organisation, Organisationseinheit, und den vollqualifizierten Domänennamen (dies ist die Domäne des Zertifikatausstellers) ein.

   ![Stammzertifikat erstellen](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Erstellen eines Serverzertifikats

Im nächsten Schritt erstellen Sie mit OpenSSL ein Serverzertifikat.

### <a name="create-the-certificates-key"></a>Erstellen des Zertifikatschlüssels

Verwenden Sie den folgenden Befehl, um den Schlüssel für das Serverzertifikat zu generieren.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Erstellen der Zertifikatsignieranforderung

Die Zertifikatsignieranforderung ist ein öffentlicher Schlüssel, der einer Zertifizierungsstelle erteilt wird, wenn ein Zertifikat angefordert wird. Die Zertifizierungsstelle gibt das Zertifikat für diese spezielle Anforderung aus.

> [!NOTE]
> Der allgemeine Name (Common Name, CN) für das Serverzertifikat muss sich von der Domäne des Zertifikatausstellers unterscheiden. In diesem Fall lautet der CN für den Aussteller z. B. `www.contoso.com`, und der CN des Serverzertifikats ist `www.fabrikam.com`.


1. Verwenden Sie den folgenden Befehl, um die Zertifikatsignieranforderung zu generieren:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort für den Stammschlüssel sowie die organisatorischen Informationen für die benutzerdefinierte Zertifizierungsstelle ein: Land, Bundesland/Region, Organisation, Organisationseinheit und der vollqualifizierte Domänenname. Dies ist die Domäne der Website, und diese Domäne muss sich vom Aussteller unterscheiden.

   ![Serverzertifikat](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generieren des Zertifikats mit der Zertifikatsignieranforderung und dem Schlüssel sowie Signieren des Zertifikats mit dem Stammschlüssel der Zertifizierungsstelle

1. Verwenden Sie den folgenden Befehl, um das Zertifikat zu erstellen:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Überprüfen des neu erstellten Zertifikats

1. Verwenden Sie den folgenden Befehl, um die Ausgabe der CRT-Datei zu drucken und deren Inhalt zu überprüfen:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Zertifikatüberprüfung](media/self-signed-certificates/verify-cert.png)

1. Überprüfen Sie die Dateien in Ihrem Verzeichnis, und vergewissern Sie sich, dass Sie folgende Dateien haben:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Konfigurieren des Zertifikats in den TLS-Einstellungen Ihres Webservers

Konfigurieren Sie TLS in Ihrem Webserver mit den Dateien „fabrikam.crt“ und „fabrikam.key“. Wenn Ihr Webserver nicht in der Lage ist, zwei Dateien zu akzeptieren, können Sie diese mit OpenSSL-Befehlen zu einer einzigen PEM- oder PFX-Datei kombinieren.

### <a name="iis"></a>IIS

Anweisungen, wie ein Zertifikat importiert und als Serverzertifikat nach IIS hochgeladen wird, finden Sie unter [Gewusst wie: Installieren importierter Zertifikate auf einem Webserver in Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Anweisungen zur TLS-Bindung finden Sie unter [Einrichtung von SSL auf IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Die folgende Konfiguration ist ein Beispiel für einen [für SSL konfigurierten virtuellen Host](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) in Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Die folgende Konfiguration ist ein Beispiel für einen [NGINX-Serverblock](https://nginx.org/docs/http/configuring_https_servers.html) mit TLS-Konfiguration:

![NGINX mit TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Zugreifen auf den Server, um die Konfiguration zu überprüfen

1. Fügen Sie das Stammzertifikat dem vertrauenswürdigen Stammspeicher Ihres Computers hinzu. Wenn Sie auf die Website zugreifen, vergewissern Sie sich, dass im Browser die gesamte Zertifikatkette angezeigt wird.

   ![Vertrauenswürdige Stammzertifikate](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Es wird vorausgesetzt, dass DNS so konfiguriert ist, dass der Webservername (in diesem Beispiel „ www.fabrikam.com “) auf die IP-Adresse Ihres Webservers verweist. Ist dies nicht der Fall, können Sie die [hosts-Datei](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) bearbeiten, um den Namen aufzulösen.
1. Navigieren Sie zu Ihrer Website, und klicken Sie im Adressfeld Ihres Browsers auf das Schlosssymbol, um die Website- und Zertifikatinformationen zu überprüfen.

## <a name="verify-the-configuration-with-openssl"></a>Überprüfen der Konfiguration mit OpenSSL

Alternativ können Sie OpenSSL verwenden, um das Zertifikat zu überprüfen.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL-Zertifikatüberprüfung](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Hochladen des Stammzertifikats in die HTTP-Einstellungen von Application Gateway

Um das Zertifikat nach Application Gateway hochzuladen, müssen Sie das CRT-Zertifikat in ein Base64-codiertes CER-Format exportieren. Da die CRT-Datei den öffentlichen Schlüssel bereits im Base64-codierten Format enthält, ändern Sie einfach die Dateierweiterung von „.crt“ in „.cer“. 

### <a name="azure-portal"></a>Azure-Portal

Um das vertrauenswürdige Stammzertifikat aus dem Portal hochzuladen, wählen Sie die **HTTP-Einstellungen** und dann das Protokoll **HTTPS** aus.

![Hinzufügen eines Zertifikats über das Portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Alternativ können Sie die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwenden, um das Stammzertifikat hochzuladen. Der folgende Code ist ein Azure PowerShell Beispiel.

> [!NOTE]
> Im folgenden Beispiel wird dem Anwendungsgateway ein vertrauenswürdiges Stammzertifikat hinzugefügt, wird eine neue HTTP-Einstellung erstellt und wird eine neue Regel hinzugefügt, wobei vorausgesetzt wird, dass der Back-End-Pool und der Listener bereits vorhanden sind.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Überprüfen der Back-End-Integrität eines Anwendungsgateways

1. Klicken Sie auf die **Back-End-Integrität**-Ansicht Ihres Anwendungsgateways, um zu prüfen, ob der Test fehlerfrei ist.
1. Sie sollten sehen, dass für den HTTPS-Test der Status **Fehlerfrei** angezeigt wird.

![HTTPS-Test](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SSL/TLS in Application Gateway finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](ssl-overview.md).