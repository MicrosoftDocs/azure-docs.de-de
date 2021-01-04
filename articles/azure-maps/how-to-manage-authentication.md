---
title: Verwalten der Authentifizierung
titleSuffix: Azure Maps
description: Machen Sie sich mit der Authentifizierung von Azure Maps vertraut. Sehen Sie, welcher Ansatz in welchem Szenario am besten funktioniert. Erfahren Sie, wie Sie das Portal zum Anzeigen von Authentifizierungseinstellungen verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57e847116febcea66e1e3ac4ba131617463b6c94
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895765"
---
# <a name="manage-authentication-in-azure-maps"></a>Verwalten der Authentifizierung in Azure Maps

Nachdem Sie ein Azure Maps-Konto erstellt haben, werden eine Client-ID und Schlüssel für die Authentifizierung über Azure Active Directory (Azure AD) und über gemeinsam verwendete Schlüssel erstellt.

## <a name="view-authentication-details"></a>Anzeigen von Authentifizierungsdetails

Nach dem Erstellen eines Azure Maps-Kontos werden der Primärschlüssel und sekundäre Schlüssel generiert. Es wird empfohlen, einen Primärschlüssel als Abonnementschlüssel zu verwenden, wenn Sie [Azure Maps mithilfe der Authentifizierung mit gemeinsam verwendetem Schlüssel aufrufen](./azure-maps-authentication.md#shared-key-authentication). Sie können einen sekundären Schlüssel in Szenarien wie Änderungen beim Schlüsselrollover verwenden. Weitere Informationen finden Sie unter [Authentifizierung in Azure Maps](./azure-maps-authentication.md).

Sie können Ihre Authentifizierungsdetails im Azure-Portal anzeigen. Wählen Sie dort in Ihrem Konto im Menü **Einstellungen** die Option **Authentifizierung** aus.

> [!div class="mx-imgBorder"]
> ![Authentifizierungsdetails](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Kategorie und Szenario ermitteln

Abhängig von den Anwendungsanforderungen gibt es bestimmte Wege, um die Anwendung zu schützen. Azure AD definiert Kategorien zur Unterstützung einer breiten Palette von Authentifizierungsflows. Informationen zu der passenden Kategorie für Ihre Anwendung finden Sie unter [Anwendungskategorien](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories).

> [!NOTE]
> Selbst wenn Sie die Authentifizierung mit gemeinsam verwendetem Schlüssel verwenden, hilft Ihnen das Verständnis von Kategorien und Szenarien dabei, die Anwendung zu schützen.

## <a name="determine-authentication-and-authorization"></a>Bestimmen von Authentifizierung und Autorisierung

In der folgenden Tabelle werden gängige Authentifizierungs- und Autorisierungsszenarien in Azure Maps umrissen. Die Tabelle enthält einen Vergleich der Arten von Schutz, die das jeweilige Szenario bietet.

> [!IMPORTANT]
> Microsoft empfiehlt die Implementierung von Azure Active Directory (Azure AD) mit der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) für Produktionsanwendungen.

| Szenario                                                                                    | Authentifizierung | Authorization | Entwicklungsaufwand | Betriebsaufwand |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Vertrauenswürdiger Daemon/nicht interaktive Clientanwendung](./how-to-secure-daemon-app.md)        | Gemeinsam verwendeter Schlüssel     | –           | Medium             | High               |
| [Vertrauenswürdiger Daemon/nicht interaktive Clientanwendung](./how-to-secure-daemon-app.md)        | Azure AD       | High          | Niedrig                | Medium             |
| [Schützen einer Single-Page-Webanwendung mit interaktiver einmaliger Anmeldung](./how-to-secure-spa-users.md) | Azure AD       | High          | Medium             | Medium             |
| [Single-Page-Webanwendung für mit nicht interaktiver Anmeldung](./how-to-secure-spa-app.md)      | Azure AD       | High          | Medium             | Medium             |
| [Webanwendung mit interaktiver einmaliger Anmeldung](./how-to-secure-webapp-users.md)          | Azure AD       | High          | High               | Medium             |
| [IoT-Gerät/Gerät mit eingeschränkter Eingabe](./how-to-secure-device-code.md)                     | Azure AD       | High          | Medium             | Medium             |

Über die Links in der Tabelle gelangen Sie zu detaillierten Konfigurationsinformationen für jedes Szenario.

## <a name="view-role-definitions"></a>Anzeigen von Rollendefinitionen

Navigieren Sie zum Anzeigen der für Azure Maps verfügbaren Azure-Rollen zu **Zugriffssteuerung (IAM)** . Wählen Sie **Rollen** aus, und suchen Sie dann nach Rollen, die mit *Azure Maps* beginnen. Hierbei handelt es sich um die Azure Maps-Rollen, denen Sie den Zugriff gewähren können.

> [!div class="mx-imgBorder"]
> ![Anzeigen verfügbarer Rollen](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Anzeigen von Rollenzuweisungen

Um Benutzer und Apps anzuzeigen, denen Zugriff für Azure Maps gewährt wurde, navigieren Sie zu **Zugriffssteuerung (IAM)** . Wählen Sie dort die Option **Rollenzuweisungen** aus, und filtern Sie dann nach **Azure Maps** .

> [!div class="mx-imgBorder"]
> ![Anzeigen von Benutzern und Apps, denen Zugriff gewährt wurde](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Anfordern von Token für Azure Maps

Anfordern eines Tokens vom Azure AD-Tokenendpunkt. Verwenden Sie in Ihrer Azure AD-Anforderung die folgenden Details:

| Azure-Umgebung      | Azure AD-Tokenendpunkt             | Azure-Ressourcen-ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Öffentliche Azure-Cloud     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government-Cloud | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Weitere Informationen zum Anfordern von Zugriffstoken aus Azure AD für Benutzer und Dienstprinzipale finden Sie unter [Authentifizierungszenarien für Azure AD](../active-directory/develop/authentication-vs-authorization.md), und sehen Sie sich spezifische Szenarien in der Tabelle mit [Szenarien](./how-to-manage-authentication.md#determine-authentication-and-authorization) an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure AD und Azure Maps Web SDK](./how-to-use-map-control.md).

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:

> [!div class="nextstepaction"]
> [Beispiele für die Azure AD-Authentifizierung](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)