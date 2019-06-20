---
title: Rollenbasierte Zugriffssteuerung für Media Services-Konten – Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die rollenbasierte Zugriffssteuerung (RBAC) für Azure Media Services-Konten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236916"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Rollenbasierte Zugriffssteuerung (RBAC) für Media Services-Konten

Derzeit definiert Azure Media Services keine benutzerdefinierten dienstspezifischen Rollen. Um vollen Zugriff auf das Media Services-Konto zu erhalten, können Kunden die integrierten Rollen von **Besitzer** oder **Mitwirkender** verwenden. Der Hauptunterschied zwischen diesen Rollen ist, dass der **Besitzer** im Gegensatz zu einem **Mitwirkenden** steuern kann, wer Zugriff auf eine Ressource hat. Die integrierte Rolle **Leser** kann ebenfalls verwendet werden, aber der Benutzer oder die Anwendung hat nur Lesezugriff auf die Media Services-APIs. 

## <a name="design-principles"></a>Entwurfsprinzipien

Eines der wichtigsten Entwurfsprinzipien der v3-API ist es, die API sicherer zu machen. v3-APIs geben bei einem **Get**- oder **List**-Vorgang keine geheimen Schlüssel oder Anmeldeinformationen zurück. Die Schlüssel sind immer NULL, leer oder aus der Antwort bereinigt. Der Benutzer muss eine separate Aktionsmethode zum Abrufen von Geheimnissen oder Anmeldeinformationen aufrufen. Die **Leser**-Rolle kann keine Vorgänge wie „Asset.ListContainerSas“, „StreamingLocator.ListContentKeys“ oder „ContentKeyPolicies.GetPolicyPropertiesWithSecrets“ aufrufen. Getrennte Aktionen ermöglichen es Ihnen, bei Bedarf noch genauer abgestimmte RBAC-Sicherheitsberechtigungen in einer benutzerdefinierten Rolle festzulegen.

So listen Sie die von Media Services unterstützten Vorgänge auf

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Im Artikel zu den [integrierten Rollendefinitionen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) erfahren Sie genau, was die Rolle gewährt. 

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Administratorrollen für klassische Abonnements, Azure RBAC-Rollen und Azure AD-Administratorrollen](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Was ist die rollenbasierte Zugriffssteuerung (RBAC) für Azure-Ressourcen?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Verwenden der rollenbasierten Zugriffssteuerung (RBAC) zum Verwalten des Zugriffs](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services-Ressourcenanbietervorgänge](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md)
- [Abrufen von Richtlinien für symmetrische Schlüssel mithilfe von Media Services .NET](get-content-key-policy-dotnet-howto.md)
