---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 93b92da999c15255f543abca346fc2bf8840006c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999434"
---
* Mehrinstanzenfähige Systeme, die den gesamten Bereich der Tarife unterstützen, mit Ausnahme von „Isoliert“.
* Die App Service-Umgebung, die in Ihrem VNET bereitgestellt wird und Apps im Tarif „App Service (isoliert)“ unterstützt.

Das VNET-Integrationsfeature wird in mehrinstanzenfähigen Apps verwendet. In der [App Service-Umgebung][ASEintro] befindet sich Ihre App bereits in einem VNET und benötigt das VNET-Integrationsfeature nicht, um Ressourcen in demselben VNET zu erreichen. Weitere Informationen zu allen Netzwerkfunktionen finden Sie unter [App Service-Netzwerkfunktionen][Networkingfeatures].

Die VNET-Integration ermöglicht Ihrer App den Zugriff auf Ressourcen in Ihrem VNET, gewährt aber keinen eingehenden privaten Zugriff auf Ihre App aus dem VNET. Privater Websitezugriff bezieht sich darauf, den Zugriff auf eine App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. Die VNET-Integration wird nur für ausgehende Aufrufe aus Ihrer App an Ihr VNET verwendet. Die VNET-Integrationsfunktion verhält sich bei Verwendung mit VNETs in derselben Region und mit VNETs in anderen Regionen unterschiedlich. Die VNET-Integrationsfunktion weist zwei Variationen auf:

* **Regionale VNET-Integration**: Wenn Sie eine Verbindung mit virtuellen Azure Resource Manager-Netzwerken in derselben Region herstellen, benötigen Sie ein dediziertes Subnetz in dem VNET, das Sie integrieren.
* **VNET-Integration, die ein Gateway erfordert**: Wenn Sie eine Verbindung mit VNETs in anderen Regionen oder mit einem klassischen virtuellen Netzwerk in derselben Region herstellen, benötigen Sie ein Azure Virtual Network-Gateway, das im Ziel-VNET bereitgestellt ist.

Für die Funktionen für die VNET-Integration gilt Folgendes:

* Sie erfordern einen Tarifplan des Typs Standard, Premium, PremiumV2, PremiumV3 oder Elastic Premium.
* Sie unterstützen TCP und UDP.
* Sie funktionieren mit Azure App Service-Apps und Funktions-Apps.

Einige Vorgänge werden von der VNET-Integration nicht unterstützt, z. B.:

* Einbindung eines Laufwerks
* Active Directory-Integration
* NetBIOS

Die VNET-Integration mit erforderlichem Gateway ermöglicht nur den Zugriff auf Ressourcen im Ziel-VNET oder in Netzwerken, die mit dem Ziel-VNET über Peering oder VPNs verbunden sind. Die VNET-Integration mit erforderlichem Gateway ermöglicht keinen Zugriff auf Ressourcen, die über Azure ExpressRoute-Verbindungen verfügbar sind oder mit Dienstendpunkten funktionieren.

Unabhängig von der verwendeten Version erhält Ihre App durch die VNET-Integration Zugriff auf Ressourcen in Ihrem VNET, es wird jedoch kein eingehender privater Zugriff auf Ihre App aus dem VNET gewährt. Privater Zugriff bezieht sich darauf, den Zugriff auf Ihre App nur über ein privates Netzwerk zuzulassen, z. B. ein Azure-VNET. Die VNET-Integration dient nur zum Ausführen ausgehender Aufrufe von Ihrer App in Ihr VNET.

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md