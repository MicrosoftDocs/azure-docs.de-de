---
title: Konfigurieren eines Gateways zum Weiterleiten von Anforderungen
description: Erhalten Sie Informationen zum Konfigurieren des Gateways, das den eingehenden Datenverkehr für Ihre Anwendung(en) unter Service Fabric Mesh verarbeitet.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 40bba03c35568c013e383c08f0a13f765c1cc8bd
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625853"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurieren einer Gatewayressource zum Weiterleiten von Anforderungen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Vorhandene Bereitstellungen werden noch bis zum 28. April 2021 unterstützt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Mithilfe einer Gatewayressource wird eingehender Datenverkehr an das Netzwerk weitergeleitet, in dem Ihre Anwendung enthalten ist. Konfigurieren Sie sie anhand von Regeln, über die Anforderungen basierend auf der Struktur der Anforderung an bestimmte Dienste oder Endpunkte weitergeleitet werden. Weitere Informationen zu Netzwerken und Gateways in Mesh finden Sie unter [Einführung in Netzwerke unter Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md). 

Gatewayressourcen müssen als Teil Ihrer Bereitstellungsvorlage (JSON oder YAML) deklariert werden und sind von einer Netzwerkressource abhängig. Dieses Dokument umreißt die verschiedenen Eigenschaften, die für Ihr Gateway festgelegt werden können, und behandelt eine Beispielkonfiguration für das Gateway.

## <a name="options-for-configuring-your-gateway-resource"></a>Optionen zum Konfigurieren der Gatewayressource

Die Gatewayressource dient als Brücke zwischen dem Netzwerk Ihrer Anwendung und dem Netzwerk der zugrunde liegenden Infrastruktur (dem `open`-Netzwerk). Sie müssen nur ein Gateway konfigurieren. (Die Mesh-Vorschauversion ist auf ein Gateway pro App beschränkt.) Die Deklaration für die Ressource besteht aus zwei Hauptteilen: den Metadaten und den Eigenschaften der Ressource. 

### <a name="gateway-resource-metadata"></a>Metadaten der Gatewayressource

Ein Gateway wird mit den folgenden Metadaten deklariert:
* `apiVersion`: muss auf „2018-09-01-preview“ (bzw. in der Zukunft auf eine höhere Version) festgelegt werden
* `name`: ein Zeichenfolgenname für dieses Gateway
* `type`: Microsoft.ServiceFabricMesh/gateways
* `location`: muss auf den Speicherort Ihrer App/Ihres Netzwerks festgelegt werden; in der Regel ein Verweis auf den location-Parameter in Ihrer Bereitstellung
* `dependsOn`: das Netzwerk, für das dieses Gateway als Eingangspunkt dient

Hier sehen Sie, wie diese Daten in einer Azure Resource Manager-Bereitstellungsvorlage (JSON) aufgeführt werden: 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Eigenschaften des Gateways

Im Abschnitt mit den Eigenschaften werden die Netzwerke, zwischen denen sich das Gateway befindet, und die Regeln zum Weiterleiten von Anforderungen definiert. 

#### <a name="source-and-destination-network"></a>Quell- und Zielnetzwerk 

Jedes Gateway benötigt ein `sourceNetwork` und ein `destinationNetwork`. Das Quellnetzwerk wird als das Netzwerk definiert, von dem Ihre App eingehende Anforderungen erhält. Die Namenseigenschaft muss immer auf „Open“ festgelegt werden. Das Zielnetzwerk ist das Netzwerk, auf das die Anforderungen ausgerichtet sind. Der zugehörige Namenswert muss auf den Ressourcennamen des lokalen Netzwerks Ihrer App festgelegt werden (und den vollständigen Verweis auf die Ressource enthalten). Nachfolgend finden Sie eine Beispielkonfiguration für eine Bereitstellung in einem Netzwerk namens „myNetwork“.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regeln 

Ein Gateway kann mehrere Routingregeln aufweisen, die die Verarbeitung des eingehenden Datenverkehrs festlegen. Eine Routingregel definiert die Beziehung zwischen dem lauschenden Port und dem Zielendpunkt für eine bestimmte Anwendung. Für TCP-Routingregeln gibt es eine 1:1-Zuordnung zwischen Port und Endpunkt. Für HTTP-Routingregeln können Sie komplexere Routingregeln festlegen, die den Pfad der Anforderung und optional Header untersuchen, um über die Weiterleitung der Anforderung zu entscheiden. 

Routingregeln werden pro Port angegeben. Jeder Eingangsport weist innerhalb des Eigenschaftenabschnitts Ihrer Gatewaykonfiguration ein eigenes Array von Regeln auf. 

#### <a name="tcp-routing-rules"></a>TCP-Routingregeln 

Eine TCP-Routingregel besteht aus den folgenden Eigenschaften: 
* `name`: Verweis auf die Regel (eine beliebige Zeichenfolge Ihrer Wahl) 
* `port`: Port, an dem auf eingehende Anforderungen gelauscht wird 
* `destination`: Endpunktspezifikation mit `applicationName`, `serviceName` und `endpointName` zur Angabe des Ziels für die Anforderungsweiterleitung

Hier sehen Sie ein Beispiel für eine TCP-Routingregel:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-Routingregeln 

Eine HTTP-Routingregel besteht aus den folgenden Eigenschaften: 
* `name`: Verweis auf die Regel (eine beliebige Zeichenfolge Ihrer Wahl) 
* `port`: Port, an dem auf eingehende Anforderungen gelauscht wird 
* `hosts`: ein Array von Richtlinien für Anforderungen, die bei den verschiedenen „Hosts“ am oben angegebenen Port eingehen. Hosts sind die Gruppe von Anwendungen und Diensten, die im Netzwerk ausgeführt werden und eingehende Anforderungen verarbeiten können, z.B. eine Web-App. Hostrichtlinien werden der Reihenfolge nach interpretiert, daher sollten Sie die folgenden Einträge mit absteigender Spezifität erstellen.
    * `name`: der DNS-Name des Hosts, für den die folgenden Routingregeln angegeben werden. Mit „*“ werden Routingregeln für alle Hosts erstellt.
    * `routes`: ein Array von Richtlinien für diesen bestimmten Host.
        * `match`: Spezifikation der Struktur eingehender Anforderungen, auf die diese Regel angewendet werden soll, basierend auf einem `path`.
            * `path`: enthält einen `value` (eingehender URI), `rewrite` (Angabe zur Weiterleitung der Anforderung) und einen `type` (kann derzeit nur „Prefix“ lauten).
            * `header`: ist ein optionales Array von Headerwerten, die mit dem Header der Anforderung abgeglichen werden, wenn die Anforderung der path-Spezifikation (oben) entspricht.
              * Jeder Eintrag enthält `name` (Zeichenfolgenname des abzugleichenden Headers), `value` (Zeichenfolgenwert des Headers in der Anforderung) und `type` (kann derzeit nur „Exact“ lauten).
        * `destination`: Wenn die Anforderung übereinstimmt, wird sie an dieses Ziel weitergeleitet, das über `applicationName`, `serviceName` und `endpointName` angegeben wurde.

Hier sehen Sie ein Beispiel für eine HTTP-Routingregel, die auf Anforderungen angewendet wird, die an Port 80 eingehen und für alle Hosts gelten, die von Apps in diesem Netzwerk verarbeitet werden. Wenn die Struktur der Anforderungs-URL der Pfadspezifikation (`<IPAddress>:80/pickme/<requestContent>`) entspricht, wird die URL an den `myListener`-Endpunkt geleitet.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Beispielkonfiguration für eine Gatewayressource 

Die vollständige Konfiguration einer Gatewayressource sieht folgendermaßen aus (basierend auf dem Eingangsbeispiel im [Mesh-Beispielrepository](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Dieses Gateway wird für eine Linux-Anwendung „MeshAppLinux“ konfiguriert, die aus mindestens zwei Diensten besteht („helloWorldService“ und „counterService“) und an Port 80 lauscht. Abhängig von der URL-Struktur der eingehenden Anforderung wird die Anforderung an einen dieser Dienste weitergeleitet. 
* Bei „\<IPAddress>:80/helloWorld/\<request\>“ wird eine Anforderung an helloWorldListener in helloWorldService weitergeleitet. 
* Bei „\<IPAddress>:80/counter/\<request\>“ wird eine Anforderung an counterListener in counterService weitergeleitet. 

## <a name="next-steps"></a>Nächste Schritte
* Bereitstellen des [Eingangsbeispiels](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress), um Gateways in Aktion zu erleben
