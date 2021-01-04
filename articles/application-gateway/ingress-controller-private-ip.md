---
title: Verwenden der privaten IP-Adresse für das interne Routing für einen Eingangsendpunkt
description: Dieser Artikel enthält Informationen dazu, wie Sie private IP-Adressen für das interne Routing verwenden und so den Eingangsendpunkt in einem Cluster für den Rest des VNETs zu Verfügung stellen.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397307"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Verwenden der privaten IP-Adresse für das interne Routing für einen Eingangsendpunkt 

Mit dieser Funktion kann der Eingangsendpunkt im `Virtual Network` mit einer privaten IP-Adresse bereitgestellt werden.

## <a name="pre-requisites"></a>Voraussetzungen  
Erstellen einer App Gateway-Instanz mit einer [privaten IP-Konfiguration](./configure-application-gateway-with-private-frontend-ip.md)

Es gibt zwei Möglichkeiten, den Controller für die Verwendung einer privaten IP-Adresse für eingehenden Datenverkehr zu konfigurieren:

## <a name="assign-to-a-particular-ingress"></a>Zuweisen zu einem bestimmtem Eingangsdatenverkehr
Um einen bestimmten Eingangsdatenverkehr über eine private IP-Adresse bereitzustellen, verwenden Sie die Anmerkung [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) in der Eingangsressource.

### <a name="usage"></a>Verwendung
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Für Application Gateway-Instanzen ohne eine private IP-Adresse wird eingehender Datenverkehr, der mit `appgw.ingress.kubernetes.io/use-private-ip: "true"` kommentiert ist, ignoriert. Dies wird im Eingangsereignis und im AGIC-Podprotokoll angegeben.

* Fehler, wie im Eingangsereignis angegeben

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Fehler, wie in AGIC-Protokollen angegeben

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Globale Zuweisung
Falls die Anforderung darin besteht, den gesamten Eingangsdatenverkehr einzuschränken, der über eine private IP-Adresse bereitgestellt werden soll, verwenden Sie `appgw.usePrivateIP: true` in der `helm`-Konfiguration.

### <a name="usage"></a>Verwendung
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Dadurch filtert der Eingangscontroller bei der Konfiguration der Front-End-Listener auf dem Application Gateway die IP-Adresskonfigurationen auf eine private IP-Adresse.
Wenn `usePrivateIP: true` ist und keine private IP-Adresse zugewiesen wurde, gibt AGIC eine Panic-Meldung aus und stürzt ab.

> [!NOTE]
> Die Application Gateway v2-SKU erfordert eine öffentliche IP-Adresse. Wenn Application Gateway privat sein muss, fügen Sie eine [`Network Security Group`](../virtual-network/network-security-groups-overview.md) an das Subnetz von Application Gateway an, um den Datenverkehr einzuschränken.