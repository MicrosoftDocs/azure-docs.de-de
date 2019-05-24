---
title: Teamentwicklung mit Azure Dev Spaces mit Node.js und VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: ba679f8b49d212d28ebbdf24e0031d87bef315af
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765221"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Vornehmen einer Codeänderung
Wechseln Sie ins VS Code-Fenster für `mywebapi`, und nehmen Sie eine Codeänderung am GET-Standardhandler `/` in `server.js` vor. Beispiel:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
