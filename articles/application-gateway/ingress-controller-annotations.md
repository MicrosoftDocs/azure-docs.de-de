---
title: 'Application Gateway-Eingangscontroller: Anmerkungen'
description: Dieser Artikel enthält Dokumentation zu den Anmerkungen, die für den Application Gateway-Eingangscontroller spezifisch sind.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397449"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anmerkungen für den Azure Application Gateway-Eingangscontroller 

## <a name="introductions"></a>Einführung

Die Kubernetes-Eingangsressource kann mit beliebigen Schlüssel-Wert-Paaren kommentiert werden. AGIC verwendet Anmerkungen zum Programmieren von Application Gateway-Features, die nicht über die YAML-Eingangsdatei konfiguriert werden können. Eingangsanmerkungen werden auf alle HTTP-Einstellungen, Back-End-Pools und Listener angewendet, die von einer Eingangsressource abgeleitet werden.

## <a name="list-of-supported-annotations"></a>Liste unterstützter Anmerkungen

Damit eine Eingangsressource von AGIC beobachtet wird, muss Sie mit `kubernetes.io/ingress.class: azure/application-gateway`**kommentiert** werden. Nur dann arbeitet AGIC mit der fraglichen Eingangsressource zusammen.

| Anmerkungsschlüssel | Werttyp | Standardwert | Zulässige Werte
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (Sekunden) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (Sekunden) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Präfix für Back-End-Pfad

Diese Anmerkung ermöglicht es, dass der in einer Eingangsressource angegebene Back-End-Pfad mit dem in dieser Anmerkung angegebenen Präfix neu geschrieben wird. Dadurch können Benutzer Dienste bereitstellen, deren Endpunkte sich von den Endpunktnamen unterscheiden, die zum Bereitstellen eines Diensts in einer Eingangsressource verwendet werden.

### <a name="usage"></a>Verwendung

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Beispiel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
Im Beispiel oben haben wir eine Eingangsressource namens `go-server-ingress-bkprefix` mit einer Anmerkung `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` definiert. Die Anmerkung weist das Anwendungsgateway an, eine HTTP-Einstellung zu erstellen, die eine Pfadpräfixüberschreibung für den Pfad `/hello` zu `/test/` aufweist.

> [!NOTE] 
> Im Beispiel oben haben wir nur eine Regel definiert. Die Anmerkungen gelten jedoch für die gesamte Eingangsressource. Wenn ein Benutzer also mehrere Regeln definiert hat, wird das Präfix für den Back-End-Pfad für jeden angegebenen Pfad eingerichtet. Wenn ein Benutzer unterschiedliche Regeln mit unterschiedlichen Pfadpräfixen (sogar für denselben Dienst) benötigt, müssten daher andere Eingangsressourcen definiert werden.

## <a name="tls-redirect"></a>TLS-Umleitung

Application Gateway [kann so konfiguriert werden](./redirect-overview.md), dass HTTP-URLs automatisch an Ihre HTTPS-Entsprechungen umgeleitet werden. Wenn diese Anmerkung vorhanden und TLS ordnungsgemäß konfiguriert ist, erstellt der Kubernetes-Eingangscontroller eine [Routingregel mit einer Umleitungskonfiguration](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) und wendet die Änderungen auf Ihr Application Gateway an. Die erstellte Umleitung lautet HTTP `301 Moved Permanently`.

### <a name="usage"></a>Verwendung

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Beispiel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Verbindungsausgleich

`connection-draining`: Mit dieser Anmerkung können Benutzer angeben, ob der Verbindungsausgleich aktiviert werden soll.
`connection-draining-timeout`: Diese Anmerkung ermöglicht es Benutzern, ein Timeout anzugeben, nach dem Application Gateway die Anforderungen an den ausgleichenden Back-End-Endpunkt beendet.

### <a name="usage"></a>Verwendung

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Beispiel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookiebasierte Affinität

Mit dieser Anmerkung können Sie angeben, ob cookiebasierte Affinität aktiviert werden soll.

### <a name="usage"></a>Verwendung

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Beispiel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Anforderungstimeout

Mit dieser Anmerkung kann das Anforderungstimeout in Sekunden angegeben werden, nach dem Application Gateway die Anforderung als fehlerhaft einstuft, wenn keine Antwort empfangen wird.

### <a name="usage"></a>Verwendung

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Beispiel

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Verwenden einer privaten IP-Adresse

Mit dieser Anmerkung können wir angeben, ob dieser Endpunkt für die private IP-Adresse von Application Gateway verfügbar gemacht werden soll.

> [!NOTE]
> * Application Gateway unterstützt nicht mehrere IP-Adressen für den gleichen Port (Beispiel: 80/443). Eingehender Datenverkehr mit der Anmerkung `appgw.ingress.kubernetes.io/use-private-ip: "false"` und weiterer Datenverkehr mit `appgw.ingress.kubernetes.io/use-private-ip: "true"` für `HTTP` führt dazu, dass die Aktualisierung des Application Gateways durch AGIC fehlschlägt.
> * Für Application Gateway-Instanzen, die nicht über eine private IP-Adresse verfügen, wird eingehender Datenverkehr mit `appgw.ingress.kubernetes.io/use-private-ip: "true"` ignoriert. Dies spiegelt sich in den Controllerprotokollen und Eingangsereignissen für diesen eingehenden Datenverkehr durch die Warnung `NoPrivateIP` wider.


### <a name="usage"></a>Verwendung
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Beispiel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Back-End-Protokoll

Mit dieser Anmerkung können wir das Protokoll angeben, das Application Gateway während der Kommunikation mit den Pods verwenden soll. Unterstützte Protokolle: `http`, `https`

> [!NOTE]
> * Obwohl selbstsignierte Zertifikate für Application Gateway unterstützt werden, unterstützt AGIC zurzeit nur `https`, wenn Pods ein Zertifikat verwenden, das von einer bekannten Zertifizierungsstelle signiert wurde.
> * Stellen Sie sicher, dass Port 80 nicht mit HTTPS und Port 443 nicht mit HTTP für die Pods verwendet wird.

### <a name="usage"></a>Verwendung
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Beispiel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```