---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177964"
---
Die folgende Computerkonfiguration wurde für die unten stehenden Schritte verwendet:

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Abhängigkeiten| strongSwan |

#### <a name="1-install-strongswan"></a>1. Installieren von strongSwan

Verwenden Sie die folgenden Befehle, um die erforderliche strongSwan-Konfiguration zu installieren:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. Generieren von Schlüsseln und Zertifikat

Generieren des Zertifizierungsstellenzertifikats

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Drucken Sie das Zertifizierungsstellenzertifikat im Base64-Format. Dies ist das Format, das von Azure unterstützt wird. Sie werden dies später in Azure als Teil der P2S-Konfiguration hochladen.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Generieren Sie das Benutzerzertifikat.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Generieren Sie ein p12-Paket mit dem Benutzerzertifikat. Dieses Paket wird in den nächsten Schritten bei der Arbeit mit den Clientkonfigurationsdateien verwendet.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```