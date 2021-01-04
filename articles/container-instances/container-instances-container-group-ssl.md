---
title: Aktivieren von TLS mit Sidecar-Container
description: Erstellen eines SSL- oder TLS-Endpunkts für eine in Azure Container Instances ausgeführte Containergruppe durch Ausführen von Nginx in einem Sidecar-Container
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 6587a84e7cbe655c509f74e9e39e93010e7058be
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558078"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Aktivieren eines TLS-Endpunkts in einem Sidecar-Container

Dieser Artikel zeigt, wie Sie eine [Containergruppe](container-instances-container-groups.md) mit einem Anwendungscontainer und einem Sidecar-Container mit einem TLS/SSL-Anbieter erstellen. Durch die Einrichtung einer Containergruppe mit einem separaten TLS-Endpunkt aktivieren Sie TLS-Verbindungen für Ihre Anwendung, ohne Ihren Anwendungscode zu ändern.

Sie richten eine Beispielcontainergruppe mit zwei Containern ein:
* Ein Anwendungscontainer, der eine einfache Web-App mit dem öffentlichen Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld)-Image ausführt. 
* Ein Sidecar-Container, der das öffentliche [Nginx](https://hub.docker.com/_/nginx)-Image ausführt und für die Verwendung von TLS konfiguriert ist. 

In diesem Beispiel stellt die Containergruppe nur den Port 443 für Nginx mit seiner öffentlichen IP-Adresse zur Verfügung. Nginx leitet HTTPS-Anforderungen an die begleitende Web-App weiter, die intern an Port 80 lauscht. Sie können das Beispiel für Container-Apps anpassen, die an anderen Ports lauschen. 

Weitere Ansätze zum Aktivieren von TLS in einer Containergruppe finden Sie unter [Nächste Schritte](#next-steps).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.55 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Um Nginx als TLS-Anbieter einzurichten, benötigen Sie ein TLS/SSL-Zertifikat. Dieser Artikel zeigt, wie Sie ein selbstsigniertes TLS/SSL-Zertifikat erstellen und einrichten. Für Produktionsszenarien sollten Sie ein Zertifikat von einer Zertifizierungsstelle einholen.

Um ein selbstsigniertes TLS/SSL-Zertifikat zu erstellen, verwenden Sie das in Azure Cloud Shell und vielen Linux-Distributionen verfügbare Tool [OpenSSL](https://www.openssl.org/) oder ein vergleichbares Clienttool in Ihrem Betriebssystem.

Erstellen Sie zunächst eine Zertifikatsanforderung (.csr-Datei) in einem lokalen Arbeitsverzeichnis:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Folgen Sie den Anweisungen, um die Identifikationsinformationen hinzuzufügen. Geben Sie unter „Allgemeiner Name“ den dem Zertifikat zugeordneten Hostnamen ein. Wenn zur Eingabe eines Kennworts aufgefordert werden, drücken Sie die Eingabetaste, ohne es einzugeben, um das Hinzufügen eines Kennworts zu überspringen.

Führen Sie den folgenden Befehl aus, um das selbstsignierte Zertifikat (.crt-Datei) aus der Zertifikatsanforderung zu erstellen. Beispiel:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Es sollten nun drei Dateien im Verzeichnis angezeigt werden: die Zertifikatsanforderung (`ssl.csr`), der private Schlüssel (`ssl.key`) und das selbstsignierte Zertifikat (`ssl.crt`). Verwenden Sie `ssl.key` und `ssl.crt` in späteren Schritten.

## <a name="configure-nginx-to-use-tls"></a>Konfigurieren von Nginx zur Verwendung von TLS

### <a name="create-nginx-configuration-file"></a>Erstellen der Nginx-Konfigurationsdatei

In diesem Abschnitt erstellen Sie eine Konfigurationsdatei für Nginx zur Verwendung von TLS. Beginnen Sie, indem Sie den folgenden Text in eine neue Datei mit dem Namen `nginx.conf` kopieren. In Azure Cloud Shell können Sie die Datei mit Visual Studio Code in Ihrem Arbeitsverzeichnis erstellen:

```console
code nginx.conf
```

Stellen Sie unter `location` sicher, dass `proxy_pass` den richtigen Port für die App hat. In diesem Beispiel legen wir Port 80 für die `aci-helloworld`-Container fest.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-codierte Geheimnisse und Konfigurationsdatei

Base64-Codierung der Nginx-Konfigurationsdatei, des TLS/SSL-Zertifikats und des TLS-Schlüssels. Im nächsten Abschnitt geben Sie den codierten Inhalt in eine YAML-Datei ein, die für die Bereitstellung der Containergruppe verwendet wird.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Bereitstellen einer Containergruppe

Stellen Sie nun die Containergruppe bereit, indem Sie die Containerkonfigurationen in einer [YAML-Datei](container-instances-multi-container-yaml.md) angeben.

### <a name="create-yaml-file"></a>Erstellen einer YAML-Datei

Kopieren Sie den folgenden YAML-Code in eine neue Datei namens `deploy-aci.yaml`. In Azure Cloud Shell können Sie die Datei mit Visual Studio Code in Ihrem Arbeitsverzeichnis erstellen:

```console
code deploy-aci.yaml
```

Geben Sie den Inhalt der Base64-codierten Dateien ein, die unter `secret` angegeben sind. Zum Beispiel `cat` jede der Base64-codierten Dateien, um ihren Inhalt zu sehen. Während der Bereitstellung werden diese Dateien zu einem [geheimen Volume](container-instances-volume-secret.md) in der Containergruppe hinzugefügt. In diesem Beispiel wird das geheime Volume auf dem Nginx-Container bereitgestellt.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Bereitstellen der Containergruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe:

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Stellen Sie die Containergruppe mit dem Befehl [az container create](/cli/azure/container#az-container-create) bereit, und geben Sie die YAML-Datei als Argument weiter.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Zusammenfassung der Bereitstellungen anzeigen

Um den Bereitstellungsstatus anzuzeigen, verwenden Sie den folgenden Befehl [az container show](/cli/azure/container#az-container-show):

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Ist die Bereitstellung erfolgreich, sieht die Ausgabe etwa wie folgt aus:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Überprüfen der TLS-Verbindung

Navigieren Sie in Ihrem Browser zur öffentlichen IP-Adresse der Containergruppe. Die in diesem Beispiel angezeigte IP-Adresse lautet `52.157.22.76`, die URL lautet daher **https://52.157.22.76** . Aufgrund der Konfiguration des Nginx-Servers müssen Sie HTTPS verwenden, um die laufende Anwendung zu sehen. Beim Versuch, eine Verbindung über HTTP herzustellen, tritt ein Fehler auf.

![Screenshot eines Browsers mit ausgeführter Anwendung in einer Azure-Containerinstanz](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Da in diesem Beispiel ein selbstsigniertes Zertifikat und nicht eines von einer Zertifizierungsstelle verwendet wird, zeigt der Browser eine Sicherheitswarnung an, wenn er sich über HTTPS mit der Website verbindet. Sie müssen möglicherweise die Warnung akzeptieren oder die Browser- bzw. Zertifikateinstellungen anpassen, um zu der Seite zu gelangen. Dies ist das erwartete Verhalten.

>

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel hat Ihnen veranschaulicht, wie Sie einen Nginx-Container einrichten, um TLS-Verbindungen zu einer Web-App zu aktivieren, die in der Containergruppe ausgeführt wird. Sie können dieses Beispiel für Anwendungen anpassen, die an anderen Ports als Port 80 lauschen. Sie können auch die Nginx-Konfigurationsdatei aktualisieren, um Serververbindungen auf Port 80 (HTTP) automatisch umzuleiten, um HTTPS zu verwenden.

Dieser Artikel verwendet zwar Nginx im Sidecar, aber Sie können einen anderen TLS-Anbieter verwenden, wie beispielsweise [Caddy](https://caddyserver.com/).

Wenn Sie die Containergruppe in einem [virtuellen Azure-Netzwerk](container-instances-vnet.md) bereitstellen, können Sie andere Optionen zum Aktivieren eines TLS-Endpunkts für eine Back-End-Containerinstanz in Erwägung ziehen, einschließlich:

* [Azure Functions-Proxys](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway:](../application-gateway/overview.md) Sehen Sie sich ein Beispiel für eine [Bereitstellungsvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) an.
