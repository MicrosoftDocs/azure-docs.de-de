---
title: Aktivieren von cookiebasierter Affinität mit Application Gateway
description: Dieser Artikel enthält Informationen zum Aktivieren von cookiebasierter Affinität mit Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397415"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Aktivieren von cookiebasierter Affinität mit Application Gateway
Wie in der [Azure Application Gateway-Dokumentation](./application-gateway-components.md#http-settings) beschrieben, unterstützt Application Gateway cookiebasierte Affinität, d.h. das Gateway kann nachfolgenden Datenverkehr von einer Benutzersitzung an den gleichen Server zur Verarbeitung weiterleiten.

## <a name="example"></a>Beispiel
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```