---
title: Authentifizierung mit Azure Maps | Microsoft-Dokumentation
description: Enthält Informationen zur Authentifizierung für die Verwendung von Azure Maps-Diensten.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 22aba19e16e4349a5b495b307c9906f7ded5a636
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393675"
---
# <a name="authentication-with-azure-maps"></a>Authentifizierung mit Azure Maps

Azure Maps unterstützt zwei Möglichkeiten für die Authentifizierung von Anforderungen: gemeinsam verwendeter Schlüssel und Azure Active Directory (Azure AD). In diesem Artikel werden diese Authentifizierungsmethoden als Anleitung für Ihre Implementierung beschrieben.

## <a name="shared-key-authentication"></a>Authentifizierung mit gemeinsam verwendetem Schlüssel

Bei der Authentifizierung mit einem gemeinsam verwendeten Schlüssel werden bei jeder Anforderung an Azure Maps Schlüssel übergeben, die mit einem Azure Maps-Konto generiert werden.  Bei der Erstellung Ihres Azure Maps-Kontos werden zwei Schlüssel generiert. Für jede Anforderung, die an Azure Maps-Dienste gesendet wird, muss der Abonnementschlüssel der URL als Parameter hinzugefügt werden.

> [!Tip]
> Wir empfehlen Ihnen, Ihre Schlüssel regelmäßig neu zu generieren. Es werden zwei Schlüssel bereitgestellt, damit Sie die Verbindungen mit einem Schlüssel aufrechterhalten können, während Sie den anderen neu generieren. Bei der Neugenerierung Ihrer Schlüssel müssen Sie alle Anwendungen, die auf das Konto zugreifen, so aktualisieren, dass die neuen Schlüssel verwendet werden.

Weitere Informationen zum Anzeigen Ihrer Schlüssel finden Sie unter [Anzeigen von Authentifizierungsdetails](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Authentifizierung mit Azure Active Directory (Vorschauversion)

Azure Maps verfügt jetzt über eine Integration von [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis), mit der Anforderungen für Azure Maps-Dienste authentifiziert werden können. Azure AD ermöglicht eine identitätsbasierte Authentifizierung mit [rollenbasierter Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/overview) (Role-Based Access Control, RBAC), um den Zugriff auf Azure Maps-Ressourcen auf der Benutzer- oder Anwendungsebene zu gewähren. In den folgenden Abschnitten können Sie sich mit den Konzepten und Komponenten der Azure Maps-Integration in Azure AD vertraut machen.

## <a name="authentication-with-oauth-access-tokens"></a>Authentifizierung mit OAuth-Zugriffstoken

Azure Maps akzeptiert **OAuth 2.0**-Zugriffstoken für Azure AD-Mandanten, die einem Azure-Abonnement zugeordnet sind, in dem ein Azure Maps-Konto enthalten ist. Azure Maps akzeptiert Token für:

* Azure AD-Benutzer. 
* Partneranwendungen, für die von Benutzern delegierte Berechtigungen verwendet werden.
* Verwaltete Identitäten für Azure-Ressourcen.

Azure Maps generiert für jedes Azure Maps-Konto einen *eindeutigen Bezeichner (Client-ID)* . Wenn Sie diese Client-ID mit zusätzlichen Parametern kombinieren, können Sie Token von Azure AD anfordern, indem Sie den folgenden Wert angeben:

```
https://login.microsoftonline.com
```
Weitere Informationen zum Konfigurieren von Azure AD und Anforderungstoken für Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allgemeine Informationen zum Anfordern von Token aus Azure AD finden Sie unter [Was ist Authentifizierung?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Anfordern von Azure Maps-Ressourcen mit OAuth-Token

Nachdem ein Token von Azure AD empfangen wurde, kann eine Anforderung an Azure Maps gesendet werden, für die die beiden folgenden Anforderungsheader festgelegt sind:

| Anforderungsheader    |    Wert    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorisierung     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` ist die auf dem Azure Maps-Konto basierende GUID, die auf der Azure Maps-Authentifizierungsseite angezeigt wird.

Hier ist ein Beispiel für eine Azure Maps-Routenanforderung angegeben, für die ein OAuth-Token verwendet wird:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Weitere Informationen zum Anzeigen Ihrer Client-ID finden Sie unter [Anzeigen von Authentifizierungsdetails](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Steuern des Zugriffs mit RBAC

Mit Azure AD können Sie den Zugriff auf geschützte Ressourcen per RBAC steuern. Nachdem Sie Ihr Azure Maps-Konto erstellt und Ihre Azure Maps-Azure AD-Anwendung in Ihrem Azure AD-Mandanten registriert haben, können Sie RBAC für einen Benutzer, eine Anwendung oder eine Azure-Ressource auf der Portalseite des Azure Maps-Kontos einrichten.

Azure Maps unterstützt die Steuerung des Lesezugriffs für einzelne Azure AD-Benutzer, Anwendungen und Azure-Dienste über verwaltete Identitäten für Azure-Ressourcen.

![Azure Maps-Datenleser (Vorschauversion)](./media/azure-maps-authentication/concept.png)

Informationen zum Anzeigen Ihrer RBAC-Einstellungen finden Sie im Artikel zum Thema [Konfigurieren von RBAC für Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Verwaltete Identitäten für Azure-Ressourcen und Azure Maps

[Verwaltete Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) stellen Azure-Dienste (Azure App Service, Azure Functions, Azure Virtual Machines usw.) mit einer automatisch verwalteten Identität bereit, die für den Zugriff auf Azure Maps-Dienste autorisiert werden kann.  

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Authentifizierung einer Anwendung mit Azure AD und Azure Maps finden Sie im Artikel zum Thema [Verwalten der Authentifizierung in Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Weitere Informationen zur Authentifizierung von Azure Maps, des Kartensteuerelements und von Azure AD finden Sie unter [Verwenden des Azure Maps-Kartensteuerelements](https://aka.ms/amaadmc).