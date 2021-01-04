---
title: Einschränken der Zugriffsberechtigungen für Gastbenutzer – Azure Active Directory | Microsoft-Dokumentation
description: Einschränken der Zugriffsberechtigungen für Gastbenutzer über das Azure-Portal, PowerShell oder Microsoft Graph in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/03/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e940c6eb2710ea43e756e4ea7956a39df9e0ce8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575549"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Einschränken von Gastzugriffsberechtigungen (Vorschau) in Azure Active Directory

Mit Azure Active Directory (Azure AD) können Sie einschränken, was externe Gastbenutzer in ihrer Organisation in Azure AD sehen können. Gastbenutzern ist in Azure AD standardmäßig eine eingeschränkte Berechtigungsstufe zugewiesen, während die Standardeinstellung für Mitgliedsbenutzer den vollständige Satz von Standardbenutzerberechtigungen umfasst. Dies ist eine Vorschau einer neuen Berechtigungsstufe für Gastbenutzer in den Einstellungen für die externe Zusammenarbeit Ihrer Azure AD-Organisation, die einen noch eingeschränkteren Zugriff bietet. Somit sind für den Gastzugriff jetzt folgende Auswahlmöglichkeiten vorhanden:

Berechtigungsstufe         | Zugriffsebene
----------------         | ------------
Mit Mitgliedsbenutzern identisch     | Gäste haben denselben Zugriff auf Azure AD-Ressourcen wie Mitgliedsbenutzer.
Beschränkter Zugriff (Standardeinstellung) | Gäste können die Mitgliedschaft in allen nicht ausgeblendeten Gruppen sehen.
**Eingeschränkter Zugriff (neu)**  | **Gäste können keine Mitgliedschaft in Gruppen sehen.**

Wenn der Gastzugriff eingeschränkt ist, können Gäste nur das eigene Benutzerprofil anzeigen. Die Berechtigung zum Anzeigen anderer Benutzer ist auch dann nicht gegeben, wenn der Gast nach Benutzerprinzipalname oder objectId sucht. Bei eingeschränktem Zugriff ist für Gastbenutzer auch die Anzeige der Mitgliedschaft in Gruppen, denen sie zugeordnet sind, eingeschränkt. Weitere Informationen zu den allgemeinen Standardbenutzerberechtigungen, einschließlich der Gastbenutzerberechtigungen, finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Berechtigungen und Lizenzen

Sie müssen der Rolle „Globaler Administrator“ angehören, um die Einstellungen für die externe Zusammenarbeit zu konfigurieren. Zum Einschränken des Gastzugriffs bestehen keine zusätzlichen Lizenzierungsanforderungen.

## <a name="update-in-the-azure-portal"></a>Aktualisieren im Azure-Portal

Es wurden Änderungen an den vorhandenen Azure-Portalsteuerelementen für Gastbenutzerberechtigungen vorgenommen.

1. Melden Sie sich mit globalen Administratorberechtigungen beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie auf der **Azure Active Directory**-Übersichtsseite für Ihre Organisation die Option **Benutzereinstellungen** aus.
1. Wählen Sie unter **Externe Benutzer** die Option **Externe Einstellungen zur Zusammenarbeit verwalten** aus.
1. Wählen Sie auf der Seite **Einstellungen für externe Zusammenarbeit** die Option **Der Gastbenutzerzugriff ist auf Eigenschaften und Mitgliedschaften eigener Verzeichnisobjekte beschränkt** aus.

    ![Seite mit Einstellungen für externe Zusammenarbeit in Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Wählen Sie **Speichern** aus. Es kann bis zu 15 Minuten dauern, bis die Änderungen für Gastbenutzer wirksam werden.

## <a name="update-with-the-microsoft-graph-api"></a>Aktualisieren mit der Microsoft Graph-API

Es wurde eine neue Microsoft Graph-API zum Konfigurieren von Gastberechtigungen in Ihrer Azure AD-Organisation hinzugefügt. Mit den folgenden API-Aufrufen kann eine beliebige Berechtigungsebene zugewiesen werden. Der hier verwendete Wert für „guestUserRoleId“ dient zur Veranschaulichung der Einstellung für Gastbenutzer mit der größten Einschränkung. Weitere Informationen zum Festlegen von Gastberechtigungen mithilfe von Microsoft Graph finden Sie unter dem [Ressourcentyp „authorizationPolicy“](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Erstmaliges Konfigurieren

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Die Antwort sollte „Success 204“ lauten.

### <a name="updating-the-existing-value"></a>Aktualisieren des vorhandenen Werts

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Die Antwort sollte „Success 204“ lauten.

### <a name="view-the-current-value"></a>Anzeigen des aktuellen Werts

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Beispielantwort:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Aktualisieren mit PowerShell-Cmdlets

Bei diesem Feature besteht die Möglichkeit, die eingeschränkten Berechtigungen über PowerShell v2-Cmdlets zu konfigurieren. In Version 2.0.2.85 wurden PowerShell-Cmdlets vom Typ „Get“ und „Set“ veröffentlicht.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get-Befehl: Get-AzureADMSAuthorizationPolicy

Beispiel:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set-Befehl: Set-AzureADMSAuthorizationPolicy

Beispiel:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Sie müssen bei Aufforderung „authorizationPolicy“ als ID eingeben.

## <a name="supported-microsoft-365-services"></a>Unterstützte Microsoft 365-Dienste

### <a name="supported-services"></a>Unterstützte Dienste

„Unterstützt“ bedeutet, dass die Benutzeroberfläche erwartungsgemäß angezeigt wird. Das heißt insbesondere, dass sie der aktuellen Gastbenutzeroberfläche entspricht.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>Derzeit nicht unterstützte Dienste

Dienste, die aktuell nicht unterstützt werden, weisen möglicherweise Kompatibilitätsprobleme mit der neuen Einschränkungseinstellung für Gäste auf.

- Formulare
- Planner in Teams
- Planner-App
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

Frage | Antwort
-------- | ------
Wo gelten diese Berechtigungen? | Diese Berechtigungen auf Verzeichnisebene werden für alle Azure AD-Dienste und -Portale erzwungen, einschließlich Microsoft Graph, PowerShell v2, das Azure-Portal und das Portal „Meine Apps“. Microsoft 365-Dienste, die Microsoft 365-Gruppen für Zusammenarbeitsszenarien nutzen, sind ebenfalls betroffen, insbesondere Outlook, Microsoft Teams und SharePoint.
Wie wirken sich eingeschränkte Berechtigungen darauf aus, welche Gruppen Gäste sehen können? | Unabhängig davon, ob Sie die standardmäßigen oder eingeschränkte Gastberechtigungen verwenden, können Gäste die Liste der Gruppen oder Benutzer nicht auflisten. Gäste können Gruppen anzeigen, in denen sie Mitglied sind. Abhängig von ihren Berechtigungen können sie dafür das Azure-Portal oder das Portal „Meine Apps“ verwenden:<li>**Standardberechtigungen:** Um im Azure-Portal die Gruppen zu finden, deren Mitglied sie sind, müssen Gäste in der Liste **Alle Benutzer** nach ihrer Objekt-ID suchen und dann **Gruppen** auswählen. Dort wird die Liste der Gruppen angezeigt, denen sie angehören. Dies schließt auch alle Gruppendetails wie Name, E-Mail-Adresse usw. ein. Im Portal „Meine Apps“ wird eine Liste der Gruppen angezeigt, deren Besitzer sie sind und in denen sie Mitglied sind.</li><li>**Eingeschränkte Berechtigungen für Gastbenutzer:** Sie können im Azure-Portal weiterhin die Liste der Gruppen finden, deren Mitglied sie sind, indem Sie in der Liste „Alle Benutzer“ nach ihrer Objekt-ID suchen und dann „Gruppen“ auswählen. Es werden jedoch nur sehr begrenzte Details zur Gruppe angezeigt, insbesondere die Objekt-ID. Die Spalten „Name“ und „E-Mail-Adresse“ sind standardmäßig leer, und der Gruppentyp lautet „Unbekannt“. Im Portal „Meine Apps“ können sie nicht auf die Liste der Gruppen zugreifen, deren Besitzer sie sind oder in denen sie Mitglied sind.</li><br>Einen ausführlicheren Vergleich der Verzeichnisberechtigungen aus der Graph-API finden Sie unter [Standardbenutzerberechtigungen](../fundamentals/users-default-permissions.md#member-and-guest-users).
Auf welche Bereiche des Portals „Meine Apps“ wirkt sich auf dieses Feature aus? | Diese neuen Berechtigungen werden in der Gruppenfunktion im Portal „Meine Apps“ berücksichtigt. Dies umfasst alle Pfade zum Anzeigen der Gruppenliste und Gruppenmitgliedschaften in „Meine Apps“. An der Verfügbarkeit der Gruppenkachel wurden keine Änderungen vorgenommen. Die Verfügbarkeit der Gruppenkachel wird weiterhin durch die vorhandene Gruppeneinstellung im Azure-Portal gesteuert.
Haben diese Berechtigungen Vorrang vor Gasteinstellungen in SharePoint oder Microsoft Teams? | Nein. Diese vorhandenen Einstellungen steuern weiterhin die Benutzeroberfläche und den Zugriff in diesen Anwendungen. Wenn beispielsweise Probleme in SharePoint angezeigt werden, überprüfen Sie Ihre Einstellungen für externe Freigaben.
Welche Kompatibilitätsprobleme in Planner und Yammer sind bekannt? | <li>Wenn die Berechtigungen auf „eingeschränkt“ festgelegt sind, haben Gäste, die sich bei der Planner-App angemeldet haben oder auf Planner in Microsoft Teams zugreifen, keinen Zugriff auf Ihre Pläne oder Aufgaben.<li>Wenn die Berechtigungen auf „eingeschränkt“ festgelegt sind, können Gäste, die sich bei Yammer angemeldet haben, die Gruppe nicht verlassen.
Werden die vorhandenen Gastberechtigungen in meinem Mandanten geändert? | An Ihren aktuellen Einstellungen wurden keine Änderungen vorgenommen. Die Abwärtskompatibilität mit Ihren vorhandenen Einstellungen bleibt gewahrt. Sie entscheiden, wann Sie Änderungen vornehmen möchten.
Werden diese Berechtigungen standardmäßig festgelegt? | Nein. Die vorhandenen Standardberechtigungen bleiben unverändert. Sie können die Berechtigungen optional so festlegen, dass sie stärker einschränkend sind.
Gibt es Lizenzanforderungen für dieses Feature? | Nein, für dieses Feature bestehen keine neuen Lizenzierungsanforderungen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu vorhandenen Gastberechtigungen in Azure AD finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Informationen zu den Microsoft Graph-API-Methoden für das Einschränken des Gastzugriffs finden Sie unter [Ressourcentyp „authorizationPolicy“](/graph/api/resources/authorizationpolicy).
- Informationen zum Widerrufen des gesamten Zugriffs für einen Benutzer finden Sie unter [Widerrufen des Benutzerzugriffs in Azure AD](users-revoke-access.md).