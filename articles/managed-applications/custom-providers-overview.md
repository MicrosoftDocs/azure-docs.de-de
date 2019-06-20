---
title: Übersicht über die Vorschauversion zu benutzerdefinierten Azure-Anbietern
description: Beschreibt die Konzepte zum Erstellen eines benutzerdefinierten Ressourcenprovider mit Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157918"
---
# <a name="azure-custom-providers-preview-overview"></a>Vorschauversion zu benutzerdefinierten Azure-Anbietern – Übersicht

Mit dem benutzerdefinierten Azure-Anbietern können Sie Azure erweitern, dass es mit Ihrem Dienst zusammenarbeitet. Sie erstellen Ihren eigenen Ressourcenanbieter, einschließlich benutzerdefinierter Ressourcentypen und Aktionen. Der benutzerdefinierte Anbieter ist in den Azure Resource Manager integriert. Sie können die Features des Resource Managers, wie Vorlagenbereitstellungen und rollenbasierte Zugriffskontrolle, nutzen, um Ihren Dienst bereitzustellen und zu schützen.

Dieser Artikel gibt eine Übersicht über benutzerdefinierte Anbieter und deren Funktionen. Die folgende Abbildung zeigt den Workflow für Ressourcen und Aktionen, die in einem benutzerdefinierten Anbieter definiert sind.

![Übersicht zu benutzerdefinierten Anbietern](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Die benutzerdefinierten Anbieter sind derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definieren Ihres benutzerdefinierten Anbieters

Sie beginnen damit, dass Sie den Azure Resource Manager über Ihren benutzerdefinierten Anbieter informieren. Sie stellen Azure eine benutzerdefinierte Anbieterressource zur Verfügung, die den Ressourcentyp **Microsoft.CustomProviders/ResourceProvider** verwendet. In dieser Ressource definieren Sie die Ressourcen und Aktionen für Ihren Dienst.

Wenn Ihr Dienst beispielsweise einen Ressourcentyp namens **users** benötigt, nehmen Sie diesen Ressourcentyp in Ihre benutzerdefinierte Anbieterdefinition auf. Für jeden Ressourcentyp geben Sie einen Endpunkt an, der die REST-Vorgänge (PUT, GET, DELETE) für diesen Ressourcentyp anbietet. Der Endpunkt kann in jeder Umgebung gehostet werden und enthält die Logik, wie Ihr Dienst mit Vorgängen für den Ressourcentyp umgeht.

Sie können auch benutzerdefinierte Aktionen für Ihren Ressourcenanbieter definieren. Aktionen stellen POST-Vorgänge dar. Verwenden Sie Aktionen für Vorgänge wie Start, Stopp oder Neustart. Sie stellen einen Endpunkt zur Verfügung, der die Anforderung verarbeitet.

Das folgende Beispiel zeigt, wie Sie einen benutzerdefinierten Anbieter mit einer Aktion und einem Ressourcentyp definieren.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Die zulässigen Werte für **routingType** sind `Proxy` und `Cache`. „Proxy“ bedeutet, dass Anforderungen für den Ressourcentyp oder die Aktion vom Endpunkt verarbeitet werden. Die Cacheeinstellung wird nur für Ressourcentypen unterstützt, nicht für Aktionen. Um den Cache anzugeben, müssen Sie auch den Proxy angeben. Im Cache werden Antworten vom Endpunkt gespeichert, um die Lesevorgänge zu optimieren. Die Verwendung der Cacheeinstellung erleichtert die Implementierung einer API, die konsistent und mit anderen Resource Manager-Diensten kompatibel ist.

## <a name="deploy-your-resource-types"></a>Bereitstellen Ihrer Ressourcentypen

Nachdem Sie Ihren benutzerdefinierten Anbieter definiert haben, können Sie Ihre benutzerdefinierten Ressourcentypen bereitstellen. Das folgende Beispiel zeigt den JSON-Code, den Sie in Ihre Vorlage aufnehmen, um den Ressourcentyp für Ihren benutzerdefinierten Anbieter bereitzustellen. Dieser Ressourcentyp kann in derselben Vorlage mit anderen Azure-Ressourcen bereitgestellt werden.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Verwalten des Zugriffs

Verwenden Sie die [rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md) zum Verwalten des Zugriffs auf Ihren Ressourcenanbieter. Sie können Benutzern integrierte [Rollen](../role-based-access-control/built-in-roles.md) wie „Besitzer“, „Mitwirkender“ oder „Leser“ zuweisen. Oder Sie können [benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md) definieren, die für die Vorgänge in Ihrem Ressourcenanbieter spezifisch sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Grundlegendes über benutzerdefiniert Anbieter gelernt. Im nächsten Artikel erstellen Sie einen benutzerdefinierten Anbieter.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines benutzerdefinierten Anbieters und Bereitstellen von benutzerdefinierten Ressourcen](create-custom-provider.md)
