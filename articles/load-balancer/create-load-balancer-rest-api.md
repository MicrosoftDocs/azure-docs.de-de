---
title: Erstellen einer Azure Load Balancer-Instanz mit der REST-API
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie eine Azure Load Balancer-Instanz mit der REST-API erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: 159fe9d6a891858d8d2cc2315e9544b79eb44cff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60884978"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Erstellen einer Azure Basic Load Balancer-Instanz mit der REST-API

Eine Azure Load Balancer-Instanz verteilt neue eingehende Datenströme, die im Load Balancer-Front-End eintreffen, gemäß den Regeln und Integritätstests auf die Instanzen des Back-End-Pools. Load Balancer ist in zwei SKUs verfügbar: Basic und Standard. Informationen zum Unterschied zwischen den beiden SKU-Versionen finden Sie unter [Vergleich der Load Balancer-SKUs](load-balancer-overview.md#skus).
 
In dieser Anleitung wird gezeigt, wie eine Azure Basic Load Balancer-Instanz mit der [Azure-REST-API](/rest/api/azure/) erstellt wird, sodass ein Lastenausgleich für eingehende Anforderungen in mehreren virtuellen Computern innerhalb eines virtuellen Azure-Netzwerks vorgenommen werden kann. Eine vollständige Dokumentation und weitere Beispiele sind in der [Referenz zur Azure Load Balancer-REST-API](/rest/api/load-balancer/) verfügbar.
 
## <a name="build-the-request"></a>Erstellen der Anforderung
Verwenden Sie die folgende HTTP PUT-Anforderung, um eine neue Azure Basic Load Balancer-Instanz zu erstellen.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI-Parameter

|NAME  |Geben Sie in  |Erforderlich |Typ |BESCHREIBUNG |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  True       |   string      |  Die Abonnementanmeldeinformationen, die das Microsoft Azure-Abonnement eindeutig identifizieren. Die Abonnement-ID ist Teil des URI für jeden Dienstaufruf.      |
|resourceGroupName     |     path    | True        |  string       |   Der Name der Ressourcengruppe.     |
|loadBalancerName     |  path       |      True   |    string     |    Der Name des Load Balancer.    |
|api-version    |   query     |  True       |     string    |  Client-API-Version.      |



### <a name="request-body"></a>Anforderungstext

Der einzige erforderliche Parameter ist `location`. Wenn Sie die *SKU*-Version nicht definieren, wird standardmäßig eine Basic Load Balancer-Instanz erstellt.  Verwenden Sie [optionale Parameter](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) zum Anpassen der Load Balancer-Instanz.

| NAME | Type | BESCHREIBUNG |
| :--- | :--- | :---------- |
| location | string | Ressourcenspeicherort Rufen Sie mit dem Vorgang [List Locations](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) eine aktuelle Liste der Standorte auf. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Beispiel: Erstellen und Aktualisieren einer Load Balancer-Instanz im Tarif „Basic“

In diesem Beispiel erstellen Sie zunächst eine Basic Load Balancer-Instanz zusammen mit den zugehörigen Ressourcen. Anschließend konfigurieren Sie die Load Balancer-Ressourcen, darunter eine Front-End-IP-Konfiguration, einen Back-End-Adresspool, eine Lastenausgleichsregel, einen Integritätstest und eine eingehende NAT-Regel.

Vor dem Erstellen einer Load Balancer-Instanz anhand des nachfolgenden Beispiels erstellen Sie das virtuelle Netzwerk *vnetlb* mit dem Subnetz *subnetlb* in der Ressourcengruppe *rg1* am Standort **USA, Osten**.

### <a name="step-1-create-a-basic-load-balancer"></a>Schritt 1: Erstellen eines Load Balancers im Tarif „Basic“
In diesem Schritt erstellen Sie eine Basic Load Balancer-Instanz mit dem Namen *lb* am Standort **USA, Osten** in der Ressourcengruppe *rg1*.
#### <a name="sample-request"></a>Beispiel für eine Anforderung

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Anforderungstext

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>Schritt 2: Konfigurieren von Load Balancer-Ressourcen
In diesem Schritt konfigurieren Sie die Ressourcen der Load Balancer-Instanz *lb*, darunter eine Front-End-IP-Konfiguration (*fe-lb*), einen Back-End-Adresspool (*be-lb*), eine Lastenausgleichsregel (*rulelb*), einen Integritätstest (*probe-lb*) und eine eingehende NAT-Regel (*in-nat-rule*).
#### <a name="sample-request"></a>Beispiel für eine Anforderung

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Anforderungstext

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
