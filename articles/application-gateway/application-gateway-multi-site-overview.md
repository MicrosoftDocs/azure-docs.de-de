---
title: Hosten mehrerer Websites mit Azure Application Gateway
description: Diese Seite bietet eine Übersicht über die Azure Application Gateway-Unterstützung für mehrere Websites.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/16/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: d01ff17966d2e6b9aeaaf3c1f69817dbc84c3a6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "57792632"
---
# <a name="application-gateway-multiple-site-hosting"></a>Anwendungsgateways – Hosten mehrerer Websites

Das Hosten mehrerer Websites ermöglicht es Ihnen, mehr als eine Webanwendung auf derselben Anwendungsgatewayinstanz zu konfigurieren. Mit diesem Feature können Sie eine effizientere Topologie für Ihre Bereitstellungen konfigurieren, indem Sie bis zu 100 Websites zu einem einzigen Anwendungsgateway hinzufügen. Jede Website kann an ihren eigenen Back-End-Pool weitergeleitet werden. Im folgenden Beispiel verarbeitet Application Gateway den Datenverkehr für „contoso.com“ und „fabrikam.com“ mit zwei Back-End-Serverpools: ContosoServerPool und FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> Regeln werden in der Reihenfolge verarbeitet, in der sie im Portal aufgeführt sind. Es wird dringend empfohlen, vor dem Konfigurieren eines einfachen Listeners zuerst Listener für mehrere Standorte zu konfigurieren.  So wird sichergestellt, dass der Datenverkehr an das richtige Back-End geleitet wird. Wenn ein einfacher Listener zuerst aufgeführt wird und sich dafür eine Übereinstimmung mit einer eingehenden Anforderung ergibt, wird die Verarbeitung von diesem Listener durchgeführt.

Anforderungen für http://contoso.com werden an „ContosoServerPool“ und Anforderungen für http://fabrikam.com an „FabrikamServerPool“ weitergeleitet.

Dementsprechend können zwei Unterdomänen derselben übergeordneten Domäne in der gleichen Anwendungsgatewaybereitstellung gehostet werden. Beispiele für die Verwendung von untergeordneten Domänen sind http://blog.contoso.com und http://app.contoso.com, die unter nur einer Anwendungsgatewaybereitstellung gehostet werden.

## <a name="host-headers-and-server-name-indication-sni"></a>Hostheader und Servernamensanzeige

Es gibt drei allgemeine Mechanismen, um das Hosten mehrerer Websites in der gleichen Infrastruktur zu ermöglichen.

1. Hosten von mehreren Webanwendungen, jeweils mit einer eindeutigen IP-Adresse.
2. Verwenden der Hostnamen zum Hosten mehrerer Webanwendungen mit der gleichen IP-Adresse.
3. Verwenden verschiedener Ports zum Hosten mehrerer Webanwendungen mit der gleichen IP-Adresse.

Derzeit bezieht ein Anwendungsgateway eine einzelne öffentliche IP-Adresse, an der es auf Datenverkehr lauscht. Daher wird das Hosten mehrerer Anwendungen mit jeweils einer eigenen IP-Adresse zurzeit nicht unterstützt. Application Gateway unterstützt das Hosten mehrerer Anwendungen, die jeweils an verschiedenen Ports lauschen. Für dieses Szenario müssten die Anwendungen aber Datenverkehr über nicht standardmäßige Ports akzeptieren. Diese Konfiguration ist jedoch häufig nicht erwünscht. Application Gateway verwendet HTTP 1.1-Hostheader, um mehrere Websites an der gleichen öffentlichen IP-Adresse und dem gleichen Port zu hosten. Die in Application Gateway gehosteten Websites können mit der TLS-Erweiterung für die Servernamensanzeige (Server Name Indication, SNI) auch die SSL-Auslagerung unterstützen. Das bedeutet, dass der Clientbrowser und die Back-End-Webfarm HTTP/1.1- und TLS-Erweiterungen unterstützen müssen, wie in RFC 6066 definiert.

## <a name="listener-configuration-element"></a>Listenerkonfigurationselement

Das vorhandene HTTPListener-Konfigurationselement wird erweitert, um Elemente zur Anzeige von Host- und Servernamen zu unterstützen – diese werden vom Anwendungsgateway verwendet, um den Datenverkehr an den geeigneten Back-End-Pool zu leiten. Das folgende Codebeispiel ist der Codeausschnitt des HttpListeners-Elements aus der Vorlagendatei.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Eine vollständige vorlagenbasierte Bereitstellung finden Sie unter [Application Gateway for Multi Hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Anwendungsgateway für das Multihosting).

## <a name="routing-rule"></a>Routingregel

Für die Routingregel ist keine Änderung erforderlich. Verwenden Sie weiterhin die Basisroutingregel, um den geeigneten Websitelistener mit dem entsprechenden Back-End-Adresspool zu verknüpfen.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie nun mit dem Artikel [Erstellen eines Anwendungsgateways zum Hosten mehrerer Webanwendungen](application-gateway-create-multisite-azureresourcemanager-powershell.md) fort, um ein Anwendungsgateway zu erstellen, das mehr als eine Webanwendung hosten kann.