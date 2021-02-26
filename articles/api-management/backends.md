---
title: Azure API Management-Back-Ends | Microsoft-Dokumentation
description: Informationen zu benutzerdefinierten Back-Ends in API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99499426"
---
# <a name="backends-in-api-management"></a>Back-Ends in API Management

Ein *Back-End* (oder *API-Back-End*) in API Management ist ein HTTP-Dienst, der Ihre Front-End-API und die zugehörigen Vorgänge implementiert.

Beim Importieren bestimmter APIs wird das API-Back-End in API Management automatisch konfiguriert. Beispielsweise konfiguriert API Management das Back-End beim Importieren einer [OpenAPI-Spezifikation](import-api-from-oas.md), einer [SOAP-API](import-soap-api.md) oder von Azure-Ressourcen, z. B. eine über HTTP ausgelöste [Azure-Funktions-App](import-function-app-as-api.md) oder [Logik-App](import-logic-app-as-api.md).

Außerdem unterstützt API Management die Verwendung anderer Azure-Ressourcen, z. B. eines [Service Fabric-Clusters](how-to-configure-service-fabric-backend.md) oder eines benutzerdefinierten Diensts, als API-Back-End. Die Verwendung dieser benutzerdefinierten Back-Ends erfordert eine zusätzliche Konfiguration, um beispielsweise Anmeldeinformationen von Anforderungen an den Back-End-Dienst zu autorisieren und API-Vorgänge zu definieren. Sie konfigurieren und verwalten diese Back-Ends im Azure-Portal oder mithilfe von Azure-APIs oder Azure-Tools.

Nach dem Erstellen eines Back-Ends können Sie in Ihren APIs auf die Back-End-URL verweisen. Verwenden Sie die Richtlinie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService), um eine eingehende API-Anforderung an das benutzerdefinierte Back-End anstatt an das Standard-Back-End der entsprechenden API umzuleiten.

## <a name="benefits-of-backends"></a>Vorteile von Back-Ends

Ein benutzerdefiniertes Back-End bietet verschiedene Vorteile:

* Abstrahierung der Informationen zum Back-End-Dienst, Förderung der Wiederverwendbarkeit in den APIs und Verbesserung der Governance  
* Einfache Verwendung durch Konfigurieren einer Transformationsrichtlinie für eine vorhandene API
* Nutzung von API Management-Funktionen zum Verwalten von Geheimnissen in Azure Key Vault, wenn [benannte Werte](api-management-howto-properties.md) für die Authentifizierung von Header- oder Abfrageparametern konfiguriert sind

## <a name="next-steps"></a>Nächste Schritte

* Einrichten eines [Service Fabric-Back-Ends](how-to-configure-service-fabric-backend.md) über das Azure-Portal
* Back-Ends können auch über die [REST-API](/rest/api/apimanagement), mit [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) oder [Azure Resource Manager-Vorlagen](../service-fabric/service-fabric-tutorial-deploy-api-management.md) in API Management konfiguriert werden.

