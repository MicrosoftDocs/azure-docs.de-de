---
title: Authentifizierung mit Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Lernen Sie die zwei Methoden zur Authentifizierung von Anforderungen in Azure Maps kennen: Authentifizierung mit gemeinsam verwendetem Schlüssel und Azure Active Directory (Azure AD)-Authentifizierung.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d47e98273e696a4b6e827d8ebbc71a297f2861cb
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905314"
---
# <a name="authentication-with-azure-maps"></a>Authentifizierung mit Azure Maps

Azure Maps unterstützt zwei Möglichkeiten für die Authentifizierung von Anforderungen: Authentifizierung per gemeinsam verwendetem Schlüssel und [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md)-Authentifizierung. In diesem Artikel werden die beiden Authentifizierungsmethoden beschrieben, um Ihnen bei Ihrer Implementierung von Azure Maps-Diensten zu helfen.

> [!NOTE]
> Um die sichere Kommunikation mit Azure Maps zu verbessern, unterstützen wir nun TLS 1.2 (Transport Layer Security), und wir stellen die Unterstützung für TLS 1.0 und 1.1 ein. Wenn Sie derzeit TLS 1.x verwenden, evaluieren Sie Ihre TLS 1.2-Bereitschaft, und entwickeln Sie einen Migrationsplan mit den in [Lösen des TLS 1.0-Problems](/security/solving-tls1-problem) beschriebenen Tests.

## <a name="shared-key-authentication"></a>Authentifizierung mit gemeinsam verwendetem Schlüssel

 Nach dem Erstellen des Azure Maps-Kontos werden der Primär- und der Sekundärschlüssel generiert. Sie sollten den Primärschlüssel als Abonnementschlüssel verwenden, wenn Sie Azure Maps mithilfe der Authentifizierung mit gemeinsam verwendetem Schlüssel aufrufen. Bei der Authentifizierung mit einem gemeinsam verwendeten Schlüssel wird ein von einem Azure Maps-Konto generierter Schlüssel an einen Azure Maps-Dienst übergeben. Fügen Sie für jede Anforderung, die an Azure Maps-Dienste gesendet wird, der URL den *Abonnementschlüssel* als Parameter hinzu. Der Sekundärschlüssel kann in Szenarien wie Änderungen beim Schlüsselrollover verwendet werden.  

Informationen zum Anzeigen der Schlüssel im Azure-Portal finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Aus Sicherheitsgründen wird empfohlen, dass Sie zwischen Ihrem Primär- und Sekundärschlüssel wechseln. Aktualisieren Sie zur Schlüsselrotation Ihre App, um den Sekundärschlüssel zu verwenden. Stellen Sie dann die App bereit, und drücken Sie die Taste für die Aktualisierung neben dem Primärschlüssel, um einen neuen Primärschlüssel zu generieren. Der alte Primärschlüssel wird deaktiviert. Weitere Informationen zur Schlüsselrotation finden Sie unter [Einrichten von Azure Key Vault mit Schlüsselrotation und Überwachung](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Azure AD-Authentifizierung

Azure-Abonnements werden mit einem Azure AD-Mandanten bereitgestellt, um eine differenzierte Zugriffssteuerung zu ermöglichen. Azure Maps bietet Authentifizierung für Azure Maps-Dienste unter Verwendung von Azure AD. Azure AD bietet identitätsbasierte Authentifizierung für Benutzer und Anwendungen, die beim Azure AD-Mandanten registriert sind.

Azure Maps akzeptiert **OAuth 2.0**-Zugriffstoken für Azure AD-Mandanten, die einem Azure-Abonnement zugeordnet sind, in dem ein Azure Maps-Konto enthalten ist. Azure Maps akzeptiert auch Token für:

* Azure AD-Benutzer
* Partneranwendungen, für die von Benutzern delegierte Berechtigungen verwendet werden
* Verwaltete Identitäten für Azure-Ressourcen

Azure Maps generiert für jedes Azure Maps-Konto einen *eindeutigen Bezeichner (Client-ID)* . Sie können Token von Azure AD anfordern, wenn Sie diese Client-ID mit zusätzlichen Parametern kombinieren.

Weitere Informationen zum Konfigurieren von Azure AD und Anforderungstoken für Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

Allgemeine Informationen zur Authentifizierung bei Azure AD finden Sie unter [Was ist Authentifizierung?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Verwaltete Identitäten für Azure-Ressourcen und Azure Maps

[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) stellen Azure-Diensten einen automatisch verwalteten anwendungsbasierten Sicherheitsprinzipal bereit, der sich bei Azure AD authentifizieren kann. Mit der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) kann der Sicherheitsprinzipal der verwalteten Identität für den Zugriff auf Azure Maps-Dienste autorisiert werden. Beispiele für verwaltete Identitäten sind: Azure App Service, Azure Functions und Azure Virtual Machines. Eine Liste der verwalteten Identitäten finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Konfigurieren der Azure AD-Anwendungsauthentifizierung

Anwendungen authentifizieren sich beim Azure AD-Mandanten mithilfe eines oder mehrerer unterstützter Szenarien, die von Azure AD bereitgestellt werden. Jedes Azure AD-Anwendungsszenario repräsentiert verschiedene Anforderungen auf der Grundlage der geschäftlichen Anforderungen. Für einige Anwendungen ist möglicherweise eine Benutzeranmeldungserfahrung erforderlich, während für andere Anwendungen eventuell eine Anwendungsanmeldungserfahrung erforderlich ist. Weitere Informationen finden Sie unter [Authentifizierungsflows und Anwendungsszenarien](../active-directory/develop/authentication-flows-app-scenarios.md).

Nachdem die Anwendung ein Zugriffstoken erhalten hat, sendet das SDK und/oder die Anwendung eine HTTPS-Anforderung mit den folgenden erforderlichen HTTP-Headern zusätzlich zu anderen REST-API-HTTP-Headern:

| Headername    | Wert               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Authorization  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` ist die auf dem Azure Maps-Konto basierende GUID, die auf der Azure Maps-Authentifizierungsseite angezeigt wird.

Hier ist ein Beispiel für eine Azure Maps-Routenanforderung, die ein Azure AD-OAuth-Bearertoken verwendet:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Weitere Informationen zum Anzeigen Ihrer Client-ID finden Sie unter [Anzeigen von Authentifizierungsdetails](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Autorisierung mit rollenbasierter Zugriffssteuerung

Azure Maps unterstützt den Zugriff auf alle Prinzipaltypen für die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/overview.md), einschließlich einzelner Azure AD-Benutzer, -Gruppen und -Anwendungen, Azure-Ressourcen sowie verwalteter Azure-Identitäten. Prinzipaltypen wird ein Berechtigungssatz gewährt, der auch als Rollendefinition bezeichnet wird. Eine Rollendefinition bietet Berechtigungen für REST-API-Aktionen. Das Anwenden des Zugriffs auf ein oder mehrere Azure Maps-Konten wird als Bereich bezeichnet. Durch das Anwenden eines Prinzipals, einer Rollendefinition und eines Bereichs wird eine Rollenzuweisung erstellt. 

In den nächsten Abschnitten werden die Konzepte und Komponenten der Azure Maps-Integration mit Azure RBAC diskutiert. Im Rahmen des Prozesses zum Einrichten Ihres Azure Maps Kontos wird ein Azure AD-Verzeichnis dem Azure-Abonnement zugeordnet, in dem sich das Azure Maps-Konto befindet. 

Wenn Sie Azure RBAC konfigurieren, wählen Sie einen Sicherheitsprinzipal aus und wenden ihn auf eine Rollenzuweisung an. Informationen zum Hinzufügen von Rollenzuweisungen im Azure-Portal finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Auswählen einer Rollendefinition

Die folgenden Rollendefinitionstypen sind für die Unterstützung von Anwendungsszenarien vorhanden.

| Azure-Rollendefinition       | BESCHREIBUNG                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps-Datenleser      | Bietet Zugriff auf unveränderliche Azure Maps-REST-APIs.                                                       |
| Azure Maps-Datenmitwirkender | Bietet Zugriff auf veränderliche Azure Maps-REST-APIs. Veränderlichkeit wird durch die Aktionen „Write“ (Schreiben) und „Delete“ (Löschen) definiert. |
| Benutzerdefinierte Rollendefinition      | Erstellen Sie eine gefertigte Rolle, um flexiblen eingeschränkten Zugriff auf Azure Maps-REST-APIs zu ermöglichen.                      |

Einige Azure Maps-Dienste benötigen möglicherweise erhöhte Berechtigungen, um Schreib- oder Löschaktionen an Azure Maps-REST-APIs auszuführen. Die Rolle „Azure Maps-Datenmitwirkender“ ist für Dienste erforderlich, die Schreib- oder Löschaktionen bereitstellen. In der folgenden Tabelle wird beschrieben, auf welche Dienste Azure Maps-Datenmitwirkender gilt, wenn Schreib- oder Lösch Aktionen mit dem angegebenen Dienst verwendet werden. Wenn nur Leseaktionen mit dem Dienst verwendet werden, kann anstelle von „Azure Maps-Datenmitwirkender“ die Rolle „Azure Maps-Datenleser“ verwendet werden.

| Azure Maps-Dienst | Azure Maps-Rollendefinition  |
| :----------------- | :-------------------------- |
| Daten               | Azure Maps-Datenmitwirkender |
| Creator            | Azure Maps-Datenmitwirkender |
| Spatial            | Azure Maps-Datenmitwirkender |

Informationen zum Anzeigen Ihrer Azure RBAC-Einstellungen finden Sie im Artikel zum Thema [Konfigurieren von Azure RBAC für Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Benutzerdefinierte Rollendefinitionen

Ein Aspekt der Anwendungssicherheit ist das Anwenden des Prinzips der geringsten Rechte. Dieses Prinzip impliziert, dass dem Sicherheitsprinzipal nur der erforderliche Zugriff und kein zusätzlicher Zugriff gewährt werden sollte. Mit dem Erstellen benutzerdefinierter Rollendefinitionen lassen sich Anwendungsfälle unterstützen, die für die Zugriffssteuerung eine feinere Granularität benötigen. Um eine benutzerdefinierte Rollendefinition zu erstellen, können Sie bestimmte Datenaktionen auswählen, die in die Definition aufgenommen oder daraus ausgeschlossen werden sollen.

Die benutzerdefinierte Rollendefinition kann dann für jeden Sicherheitsprinzipal in einer Rollenzuweisung verwendet werden. Weitere Informationen zu benutzerdefinierten Azure-Rollendefinitionen finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md).

Hier finden Sie einige Beispielszenarien, in denen benutzerdefinierte Rollen die Anwendungssicherheit verbessern können.

| Szenario                                                                                                                                                                                                                 | Datenaktion(en) für benutzerdefinierte Rollen                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Eine öffentliche oder interaktive Anmeldewebseite mit Basiskartenkacheln und keinen anderen REST-APIs.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Eine Anwendung, die nur umgekehrte Geocodierung und keine anderen REST-APIs erfordert.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Eine Rolle für einen Sicherheitsprinzipal, der das Lesen von Azure Maps Creator-basierten Kartendaten und REST-APIs von Basiskartenkacheln anfordert.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Eine Rolle für einen Sicherheitsprinzipal, der das Lesen, Schreiben und Löschen von Creator-basierten Kartendaten erfordert. Diese kann als die Rolle „Kartendaten-Editor“ definiert werden, erlaubt aber keinen Zugriff auf andere REST-APIs wie Basiskartenkacheln. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Grundlegendes zum Begriff „Bereich“

Beim Erstellen einer Rollenzuweisung wird sie in der Azure-Ressourcenhierarchie definiert. Am Anfang der Hierarchie befindet sich eine [Verwaltungsgruppe](../governance/management-groups/overview.md), und die niedrigste Ebene ist eine Azure-Ressource wie ein Azure Maps-Konto.
Wenn Sie einer Ressourcengruppe eine Rollenzuweisung zuweisen, kann dies den Zugriff auf mehrere Azure Maps-Konten oder -Ressourcen in der Gruppe aktivieren.

> [!TIP]
> Die generelle Empfehlung von Microsoft besteht darin, den Zugriff auf den Bereich des Azure Maps-Kontos zuzuweisen, weil dadurch **unbeabsichtigter Zugriff auf andere Azure Maps-Konten**, die sich im selben Azure-Abonnement befinden, verhindert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure RBAC:
> [!div class="nextstepaction"]
> [Rollenbasierte Zugriffssteuerung (Azure)](../role-based-access-control/overview.md)

Weitere Informationen zur Authentifizierung einer Anwendung mit Azure AD und Azure Maps finden Sie unter
> [!div class="nextstepaction"]
> [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

Weitere Informationen zur Authentifizierung von Azure Maps, des Kartensteuerelements und von Azure AD finden Sie unter
> [!div class="nextstepaction"]
> [Verwenden des Azure Maps-Kartensteuerelements](./how-to-use-map-control.md)