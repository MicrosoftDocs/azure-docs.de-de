---
title: Berechtigungen in Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Berechtigungen in Azure Active Directory verwenden.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi, justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fb4342e024d826c65ed33184aaf33012d09190a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545196"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Berechtigungen und Zustimmung im Azure Active Directory-v1.0-Endpunkt

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

In Azure Active Directory (Azure AD) werden Berechtigungen sowohl für OAuth- als auch für OIDC-Datenflüsse (OpenID Connect) viel eingesetzt. Wenn Ihre App ein Zugriffstoken von Azure AD empfängt, sind darin Ansprüche enthalten. Mit diesen Ansprüchen werden die Berechtigungen beschrieben, über die Ihre App in Bezug auf eine bestimmte Ressource verfügt.

*Berechtigungen*, die auch als *Bereiche* bezeichnet werden, vereinfachen die Autorisierung für die Ressource. Die Ressource muss lediglich prüfen, ob das Token die richtige Berechtigung für die API enthält, die von der App jeweils aufgerufen wird.

## <a name="types-of-permissions"></a>Arten von Berechtigungen

Azure AD definiert zwei Arten von Berechtigungen:

* **Delegierte Berechtigungen:** Werden von Apps verwendet, für die ein angemeldeter Benutzer vorhanden ist. Für diese Apps stimmt entweder der Benutzer oder ein Administrator den von der App angeforderten Berechtigungen zu. An die App wird die Berechtigung delegiert, als angemeldeter Benutzer zu fungieren, wenn eine API aufgerufen wird. Je nach API kann der Benutzer unter Umständen der API nicht direkt zustimmen und [benötigt stattdessen einen Administrator, der seine Zustimmung erteilt](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).
* **Anwendungsberechtigungen:** Werden von Apps verwendet, die ohne vorhandenen angemeldeten Benutzer ausgeführt werden. Dies können beispielsweise Apps ein, die als Hintergrunddienste oder Daemons ausgeführt werden. Anwendungsberechtigungen können ihre [Zustimmung nur durch einen Administrator](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) erhalten, da sie normalerweise leistungsfähig sind und den Zugriff auf Daten über Benutzergrenzen hinweg oder auf Daten zulassen, die sonst nur für Administratoren zugänglich sind.

Effektive Berechtigungen sind die Berechtigungen, über die Ihre App verfügt, wenn Anforderungen an eine API gesendet werden. 

* Bei delegierten Berechtigungen verfügt Ihre App über die folgenden Berechtigungen: Die jeweils geringsten Rechte, die sich zusammen genommen aus den delegierten Berechtigungen, die der App (per Zustimmung) gewährt wurden, und den Berechtigungen des derzeit angemeldeten Benutzers ergeben. Ihre App kann niemals über mehr Berechtigungen als der angemeldete Benutzer verfügen. In Organisationen können die Berechtigungen des angemeldeten Benutzers anhand einer Richtlinie oder der Mitgliedschaft in einer oder mehreren Administratorrollen bestimmt werden. Um zu erfahren, welche Administratorrollen delegierten Berechtigungen zustimmen können, lesen Sie [Berechtigungen der Administratorrolle in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
    Angenommen, für Ihre App wurde in Microsoft Graph die delegierte Berechtigung `User.ReadWrite.All` gewährt. Mit dieser Berechtigung wird Ihrer App nominell die Berechtigung zum Lesen und Aktualisieren des Profils jedes Benutzers einer Organisation gewährt. Wenn es sich beim angemeldeten Benutzer um einen globalen Administrator handelt, kann Ihre App das Profil jedes Benutzers in der Organisation aktualisieren. Falls der angemeldete Benutzer aber keine Administratorrolle innehat, kann Ihre App nur das Profil des angemeldeten Benutzers aktualisieren. Sie kann die Profile von anderen Benutzern der Organisation nicht aktualisieren, da der Benutzer, für den die App die Berechtigung zum Durchführen von Aktionen in dessen Namen hat, nicht über diese Berechtigungen verfügt.
* Für Anwendungsberechtigungen umfassen die geltenden Rechte Ihrer App die vollständige Berechtigungsebene, die mit der Berechtigung verbunden ist. Beispielsweise kann eine App, die über die Anwendungsberechtigung `User.ReadWrite.All` verfügt, das Profil aller Benutzer der Organisation aktualisieren.

## <a name="permission-attributes"></a>Berechtigungsattribute
Berechtigungen in Azure AD weisen eine Reihe von Eigenschaften auf, mit deren Hilfe Benutzer, Administratoren oder App-Entwickler fundierte Entscheidungen darüber treffen können, welche Art von Zugriff mit der Berechtigung gewährt wird.

> [!NOTE]
> Sie können die Berechtigungen anzeigen, die von einer Azure AD-Anwendung oder einem Dienstprinzipal verfügbar gemacht werden, indem Sie das Azure-Portal oder PowerShell verwenden. Probieren Sie dieses Skript aus, um die von Microsoft Graph verfügbar gemachten Berechtigungen anzuzeigen.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Eigenschaftenname | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| `ID` | Ein GUID-Wert, mit dem diese Berechtigung eindeutig identifiziert wird. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Gibt an, ob diese Berechtigung für die Nutzung verfügbar ist. | true |
| `Type` | Gibt an, ob für diese Berechtigung die Zustimmung des Benutzers oder des Administrators erforderlich ist. | Benutzer |
| `AdminConsentDescription` | Eine Beschreibung, die Administratoren auf der Benutzeroberfläche für die Administratorzustimmung angezeigt wird. | Ermöglicht der App, E-Mails in Benutzerpostfächern zu lesen. |
| `AdminConsentDisplayName` | Der Anzeigename, der Administratoren auf der Benutzeroberfläche für die Administratorzustimmung angezeigt wird. | Lesen von Benutzer-E-Mails |
| `UserConsentDescription` | Eine Beschreibung, die Benutzern auf der Benutzeroberfläche für die Benutzerzustimmung angezeigt wird. |  Ermöglicht der App, E-Mails in Ihrem Postfach zu lesen. |
| `UserConsentDisplayName` | Der Anzeigename, der Benutzern auf der Benutzeroberfläche für die Benutzerzustimmung angezeigt wird. | Lesen Ihrer E-Mails |
| `Value` | Die Zeichenfolge, die verwendet wird, um die Berechtigung bei OAuth 2.0-Autorisierungsdatenflüssen zu identifizieren. `Value` kann auch mit der URI-Zeichenfolge für die App-ID kombiniert werden, um einen vollqualifizierten Berechtigungsnamen zu bilden. | `Mail.Read` |

## <a name="types-of-consent"></a>Arten der Zustimmung

Für Anwendungen in Azure AD ist eine Zustimmung erforderlich, um Zugriff auf benötigte Ressourcen oder APIs zu erhalten. Es gibt verschiedene Arten von Zustimmungen, über die Ihre App ggf. Informationen benötigt, um erfolgreich arbeiten zu können. Beim Definieren von Berechtigungen müssen Sie auch wissen, wie Ihre Benutzer Zugriff auf Ihre App oder die API erhalten.

* **Statische Benutzerzustimmung:** Erfolgt automatisch während des [OAuth 2.0-Autorisierungsdatenflusses](v1-protocols-oauth-code.md#request-an-authorization-code), wenn Sie die Ressource angeben, mit der Ihre App interagieren möchte. Beim Szenario mit statischer Benutzerzustimmung müssen für Ihre App alle erforderlichen Berechtigungen bereits im Azure-Portal in der Konfiguration der App angegeben werden. Wenn der Benutzer (bzw. Administrator) die Zustimmung für diese App nicht erteilt hat, wird der Benutzer von Azure AD aufgefordert, die Zustimmung jetzt zu erteilen. 

    Informieren Sie sich weiter über die Registrierung einer Azure AD-App, die den Zugriff auf eine statische Gruppe mit APIs anfordert.
* **Dynamische Benutzerzustimmung:** Dies ist ein Feature der Version 2 (v2) des Azure AD-App-Modells. In diesem Szenario fordert Ihre App eine benötigte Gruppe von Berechtigungen im [OAuth 2.0-Autorisierungsdatenfluss für v2-Apps](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent) an. Wenn der Benutzer nicht bereits zugestimmt hat, wird er aufgefordert, dies jetzt nachzuholen. [Informieren Sie sich weiter über die dynamische Zustimmung](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Die dynamische Zustimmung kann komfortabel sein. Sie stellt aber eine große Herausforderung in Bezug auf Berechtigungen dar, für die die Zustimmung durch einen Administrator erforderlich ist, da diese Berechtigungen in der Umgebung für die Administratorzustimmung zum Zustimmungszeitpunkt nicht bekannt sind. Falls Sie Administratorberechtigungen benötigen oder Ihre App eine dynamische Zustimmung verwendet, müssen Sie alle Berechtigungen im Azure-Portal registrieren (nicht nur die Berechtigungen, für die eine Administratoreinwilligung erforderlich ist). Dadurch können Mandantenadministratoren im Namen aller ihrer Benutzer zustimmen.
  
* **Zustimmung des Administrators:** Ist erforderlich, wenn Ihre App Zugriff auf bestimmte hochrangige Berechtigungen benötigt. Mit der Zustimmung des Administrators wird sichergestellt, dass Administratoren über einige zusätzliche Steuerelemente verfügen, bevor sie für Apps oder Benutzer den Zugriff auf Organisationsdaten mit hoher Berechtigungsebene autorisieren. [Erfahren Sie mehr dazu, wie Sie die Zustimmung des Administrators erteilen](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Bewährte Methoden

### <a name="client-best-practices"></a>Bewährte Methoden für Kunden

- Fordern Sie nur Berechtigungen an, die für Ihre App erforderlich sind. Für Apps mit zu vielen Berechtigungen besteht die Gefahr, dass Benutzerdaten offengelegt werden, wenn sie kompromittiert werden.
- Wählen Sie basierend auf dem Szenario, das von Ihrer App unterstützt wird, zwischen delegierten Berechtigungen und Anwendungsberechtigungen.
    - Verwenden Sie immer delegierte Berechtigungen, wenn der Aufruf im Namen eines Benutzers durchgeführt wird.
    - Nutzen Sie Anwendungsberechtigungen nur dann, wenn die App nicht interaktiv ist und keine Aufrufe im Namen von bestimmten Benutzern durchführt. Anwendungsberechtigungen verfügen über weit reichende Privilegien und sollten nur verwendet werden, wenn dies unbedingt erforderlich ist.
- Bei Verwendung einer App basierend auf dem v2.0-Endpunkt sollten Sie die statischen Berechtigungen (in Ihrer Anwendungsregistrierung angegeben) immer als Obermenge der dynamischen Berechtigungen festlegen, die Sie zur Laufzeit anfordern (im Code angegeben und als Abfrageparameter in der Autorisierungsanforderung gesendet). So wird sichergestellt, dass Szenarien wie die Zustimmung des Administrators richtig funktionieren.

### <a name="resourceapi-best-practices"></a>Bewährte Methoden für Ressourcen und APIs

- Ressourcen, mit denen APIs verfügbar gemacht werden, sollten Berechtigungen definieren, die spezifisch für die zu schützenden Daten oder Aktionen sind. Bei Einhaltung dieser bewährten Methode wird sichergestellt, dass Clients keine Berechtigungen für den Zugriff auf Daten erhalten, die sie nicht benötigen, und dass Benutzer gut darüber informiert sind, für welche Daten sie ihre Zustimmung erteilen.
- Für Ressourcen sollten die Berechtigungen `Read` und `ReadWrite` separat explizit definiert werden.
- Ressourcen sollten alle Berechtigungen, die Zugriff auf Daten über Benutzergrenzen hinweg zulassen, als Berechtigungen vom Typ `Admin` kennzeichnen.
- Für Ressourcen sollte das Namensmuster `Subject.Permission[.Modifier]` befolgt werden. Hierbei gilt Folgendes:
  - `Subject` entspricht der Art der verfügbaren Daten.
  - `Permission` entspricht der Aktion, die ein Benutzer für diese Daten ausführen kann.
  - `Modifier` wird optional verwendet, um Spezialisierungen einer anderen Berechtigung zu beschreiben.
    
    Beispiel: 
  - Mail.Read: Ermöglicht Benutzern das Lesen von E-Mails.
  - Mail.ReadWrite: Ermöglicht Benutzern das Lesen oder Schreiben von E-Mails.
  - Mail.ReadWrite.All: Ermöglicht einem Administrator oder Benutzer den Zugriff auf alle E-Mails der Organisation.
