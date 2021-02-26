---
title: Behandeln von Problemen bei der Herausgeberüberprüfung | Azure
titleSuffix: Microsoft identity platform
description: Hier wird die Behandlung von Problemen bei der Herausgeberüberprüfung für Microsoft Identity Platform durch Aufrufen von Microsoft Graph-APIs beschrieben.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 9ae5d2279baab69d762a71b2f6400009747c16fe
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584212"
---
# <a name="troubleshoot-publisher-verification"></a>Problembehandlung bei der Herausgeberüberprüfung
Wenn Sie den Vorgang nicht ausführen können oder bei der [Herausgeberüberprüfung](publisher-verification-overview.md) unerwartetes Verhalten auftritt, sollten Sie zunächst die folgenden Schritte ausführen, wenn Sie Fehlermeldungen erhalten oder unerwartetes Verhalten feststellen: 

1. Überprüfen Sie die [Anforderungen](publisher-verification-overview.md#requirements), und stellen Sie sicher, dass alle Anforderungen erfüllt sind.

1. Lesen Sie die Anweisungen zum [Markieren einer App als durch den Herausgeber verifiziert](mark-app-as-publisher-verified.md), und stellen Sie sicher, dass alle Schritte erfolgreich ausgeführt wurden.

1. Sehen Sie sich die Liste [häufiger Probleme](#common-issues) an.

1. Reproduzieren Sie die Anforderung mit dem [Graph-Tester](#making-microsoft-graph-api-calls), um zusätzliche Informationen zu sammeln und Probleme auf der Benutzeroberfläche auszuschließen.

## <a name="common-issues"></a>Häufige Probleme
Im Folgenden finden Sie einige häufige Probleme, die während des Vorgangs auftreten können. 

- **Ich kenne meine Microsoft Partner Network-ID (MPN-ID) nicht oder weiß nicht, wer der Hauptkontakt für das Konto ist.** 
    1. Navigieren Sie zur [Seite für die MPN-Registrierung](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new).
    1. Melden Sie sich mit einem Benutzerkonto im primären Azure AD-Mandanten der Organisation an. 
    1. Wenn bereits ein MPN-Konto vorhanden ist, wird dies erkannt, und Sie werden dem Konto hinzugefügt. 
    1. Navigieren Sie zur [Partnerprofilseite](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile), auf der die MPN-ID und der Hauptkontakt für das Konto aufgeführt sind.

- **Ich weiß nicht, wer mein globaler Azure AD-Administrator (auch als Unternehmensadministrator oder Mandantenadministrator bezeichnet) ist. Wie finde ich diese Informationen? Was sieht es mit Anwendungs- und Cloudanwendungsadministratoren aus?**
    1. Melden Sie sich mit einem Benutzerkonto im primären Mandanten Ihrer Organisation beim [Azure AD-Portal](https://aad.portal.azure.com) an.
    1. Navigieren Sie zu [Rollenverwaltung](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
    1. Klicken Sie auf die gewünschte Administratorrolle
    1. Es wird eine Liste der Benutzer angezeigt, denen diese Rolle zugewiesen ist.

- **Ich weiß nicht, wer Administrator für mein MPN-Konto ist** Navigieren Sie zur [Seite für die MPN-Benutzerverwaltung](https://partner.microsoft.com/pcv/users), und filtern Sie die Benutzerliste, um die Benutzer in verschiedenen Administratorrollen anzuzeigen.

- **Ich erhalte eine Fehlermeldung, die besagt, dass meine MPN-ID ungültig ist oder dass ich keinen Zugriff darauf habe.**
    1. Navigieren Sie zu Ihrem [Partnerprofil](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile), und stellen Sie Folgendes sicher: 
        - Dass die MPN-ID korrekt ist. 
        - Dass keine Fehler oder „ausstehenden Aktionen“ angezeigt werden und der Überprüfungsstatus unter „Rechtliches Geschäftsprofil“ und „Partnerinformationen“ jeweils „autorisiert“ oder „erfolgreich“ lauten.
    1. Navigieren Sie zur [Seite für die MPN-Mandantenverwaltung](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement), und vergewissern Sie sich, dass der Mandant, in dem die App registriert ist und von dem Sie sich mit einem Benutzerkonto anmelden, in der Liste der zugeordneten Mandanten aufgeführt ist. Befolgen Sie zum Hinzufügen eines weiteren Mandanten [diese Anweisungen](/partner-center/multi-tenant-account). Beachten Sie, dass alle globalen Administratoren jedes Mandanten, den Sie hinzufügen, globale Administratorrechte für Ihr Partner Center-Konto erhalten.
    1. Navigieren Sie zur [Seite für die MPN-Benutzerverwaltung](https://partner.microsoft.com/pcv/users), und vergewissern Sie sich, dass der Benutzer, unter dem Sie sich anmelden, entweder „Globaler Administrator“, „MPN-Administrator“ oder „Kontoadministrator“ ist. Befolgen Sie beim Hinzufügen eines Benutzers zu einer Rolle in Partner Center [diese Anweisungen](/partner-center/create-user-accounts-and-set-permissions).

- **Wenn ich mich beim Azure AD-Portal anmelde, werden mir keine registrierten Apps angezeigt. Warum?** 
    Möglicherweise wurden Ihre App-Registrierungen mit einem anderen Benutzerkonto in diesem Mandanten, oder in einem anderen Mandanten oder mit einem persönlichen/Endbenutzerkonto erstellt. Stellen Sie sicher, dass Sie mit dem richtigen Konto in dem Mandanten angemeldet sind, in dem Ihre App-Registrierungen erstellt wurden.

- **Ich erhalte einen Fehler im Zusammenhang mit Multi-Factor Authentication. Wie soll ich vorgehen?** 
    Stellen Sie sicher, dass [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) aktiviert und sowohl für den Benutzer, mit dem Sie sich anmelden, als auch für dieses Szenario **erforderlich** ist. Für MFA könnte es z. B. folgende Anforderungen geben:
    - Immer erforderlich für den Benutzer, mit dem Sie sich anmelden
    - [Erforderlich für die Azure-Verwaltung](../conditional-access/howto-conditional-access-policy-azure-management.md)
    - [Erforderlich für den Administratortyp](../conditional-access/howto-conditional-access-policy-admin-mfa.md), mit dem Sie sich anmelden

## <a name="making-microsoft-graph-api-calls"></a>Ausführen von Microsoft Graph-API-Aufrufen 

Wenn ein Problem aufgetreten ist, Sie aber basierend auf dem, was auf der Benutzeroberfläche angezeigt wird, nicht verstehen können, warum, kann es hilfreich sein, eine weitere Problembehandlung vorzunehmen, indem Sie mithilfe von Microsoft Graph-Aufrufen dieselben Vorgänge ausführen, die Sie auch im App-Registrierungsportal ausführen können.

Am einfachsten können diese Anforderungen mit dem [Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) ausgeführt werden. Sie können auch andere Optionen in Erwägung ziehen und z. B. [Postman](https://www.postman.com/) oder PowerShell verwenden, um eine [Webanforderung aufzurufen](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

Sie können Microsoft Graph verwenden, um den verifizierten Herausgeber Ihrer App festzulegen und zu löschen und das Ergebnis zu überprüfen, nachdem Sie einen dieser Vorgänge ausgeführt haben. Das Ergebnis kann sowohl für das Ihrer App-Registrierung entsprechende [Anwendungsobjekt](/graph/api/resources/application) als auch für alle von dieser App instanziierten [Dienstprinzipale](/graph/api/resources/serviceprincipal) angezeigt werden. Weitere Informationen zur Beziehung zwischen diesen Objekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](app-objects-and-service-principals.md).  

Nachfolgend finden Sie einige nützliche Anforderungen:  

### <a name="set-verified-publisher"></a>Festlegen des verifizierten Herausgebers 

Anforderung

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Antwort 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* ist Ihre MPN-ID. 

### <a name="unset-verified-publisher"></a>Löschen des verifizierten Herausgebers 

Anforderung:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Antwort 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Abrufen von Informationen des verifizierten Herausgebers von der Anwendung 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Abrufen von Informationen des verifizierten Herausgebers vom Dienstprinzipal 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Fehlerreferenz 

Nachfolgend finden Sie eine Liste möglicher Fehlercodes, die Sie entweder bei der Problembehandlung mit Microsoft Graph oder beim Durchlaufen des Prozesses im App-Registrierungsportal erhalten können.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

Die von Ihnen angegebene MPN-ID (`MPNID`) ist nicht vorhanden, oder Sie haben keinen Zugriff darauf. Geben Sie eine gültige MPN-ID an, und versuchen Sie es noch mal.
    
Dieser Fehler wird meist dadurch verursacht, dass der angemeldete Benutzer kein Mitglied der richtigen Rolle für das MPN-Konto in Partner Center ist. Eine Liste mit den berechtigten Rollen finden Sie unter [Anforderungen](publisher-verification-overview.md#requirements), und weitere Informationen erhalten Sie unter [Häufige Probleme](#common-issues). Der Fehler kann auch durch den Mandanten verursacht werden, bei dem die App registriert ist, wenn dieser nicht dem MPN-Konto hinzugefügt wurde, oder durch eine ungültige MPN-ID.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

Die von Ihnen angegebene MPN-ID (`MPNID`) ist ungültig. Geben Sie eine gültige MPN-ID an, und versuchen Sie es noch mal.
    
Der häufigste Grund für diesen Fehler ist, dass eine MPN-ID angegeben wird, die zu einem Konto des Partnerstandorts (Partner Location Account, PLA) gehört. Nur globale Partnerkonten werden unterstützt. Ausführlichere Informationen finden Sie im Artikel mit den [Informationen zur Kontostruktur](/partner-center/account-structure).

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

Die von Ihnen angegebene MPN-ID (`MPNID`) ist ungültig. Geben Sie eine gültige MPN-ID an, und versuchen Sie es noch mal.
    
Der häufigste Grund ist, dass die falsche MPN-ID angegeben wurde.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

Für die von Ihnen angegebene MPN-ID (`MPNID`) wurde der Überprüfungsvorgang nicht abgeschlossen. Führen Sie diesen Vorgang in Partner Center aus, und versuchen Sie es noch mal. 
    
Der häufigste Grund ist, dass das MPN-Konto den Prozess für die [Überprüfung](/partner-center/verification-responses) nicht abgeschlossen hat.

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

Die von Ihnen angegebene MPN-ID (`MPNID`) ist ungültig. Geben Sie eine gültige MPN-ID an, und versuchen Sie es noch mal. 
   
Der häufigste Grund ist, dass die falsche MPN-ID angegeben wurde.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

Die von Ihnen angegebene MPN-ID (`MPNID`) ist ungültig. Geben Sie eine gültige MPN-ID an, und versuchen Sie es noch mal.
    
Der häufigste Grund ist, dass die falsche MPN-ID angegeben wurde.

### <a name="applicationnotfound"></a>ApplicationNotFound

Die Zielanwendung (`AppId`) konnte nicht gefunden werden. Geben Sie eine gültige Anwendungs-ID an, und versuchen Sie es noch mal.
    
Der häufigste Grund ist ein Fall, bei dem die Überprüfung über die Graph-API durchgeführt wird und die angegebene ID der Anwendung fehlerhaft ist. Beachten Sie, dass nicht die App-ID oder die Client-ID, sondern die ID der Anwendung angegeben werden muss.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Diese Funktion wird in einem Azure AD B2C-Mandanten nicht unterstützt.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Diese Funktion wird in einem per E-Mail verifizierten Mandanten nicht unterstützt.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

Für die Zielanwendung (`AppId`) muss eine Herausgeberdomäne festgelegt sein. Legen Sie eine Herausgeberdomäne fest, und versuchen Sie es noch mal.

Dieser Fehler tritt auf, wenn in der App keine [Herausgeberdomäne](howto-configure-publisher-domain.md) konfiguriert wurde.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

Die Herausgeberdomäne (`publisherDomain`) der Zielanwendung stimmt nicht mit der zum Durchführen der E-Mail-Überprüfung in Partner Center verwendeten Domäne (`pcDomain`) überein. Stellen Sie sicher, dass diese Domänen übereinstimmen, und versuchen Sie es noch mal. 
    
Tritt auf, wenn weder die [Herausgeberdomäne](howto-configure-publisher-domain.md) noch die [benutzerdefinierten Domänen](../fundamentals/add-custom-domain.md), die dem Azure AD-Mandanten hinzugefügt wurden, mit der Domäne übereinstimmen, die zum Durchführen der E-Mail-Überprüfung in Partner Center verwendet wird.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

Sie sind nicht berechtigt, die Eigenschaft für den verifizierten Herausgeber für die Anwendung (`AppId`) festzulegen. 
  
Dieser Fehler wird meist dadurch verursacht, dass der angemeldete Benutzer kein Mitglied der richtigen Rolle für das MPN-Konto in Azure AD ist. Eine Liste mit den berechtigten Rollen finden Sie unter [Anforderungen](publisher-verification-overview.md#requirements), und weitere Informationen erhalten Sie unter [Häufige Probleme](#common-issues).

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

Die MPN-ID wurde im Anforderungstext nicht angegeben, oder der Anforderungsinhaltstyp war nicht „application/json“.

### <a name="msanotsupported"></a>MSANotSupported 

Diese Funktion wird für Microsoft-Kundenkonten nicht unterstützt. Es werden nur von einem Azure AD-Benutzer in Azure AD registrierte Anwendungen unterstützt.

### <a name="interactionrequired"></a>InteractionRequired

Tritt auf, wenn die mehrstufige Authentifizierung nicht durchgeführt wurde, bevor versucht wird, der App einen überprüften Herausgeber hinzuzufügen. Weitere Informationen finden Sie unter [Häufige Probleme](#common-issues). Hinweis: MFA muss in der gleichen Sitzung ausgeführt werden, wenn Sie versuchen, einen verifizierten Herausgeber hinzuzufügen. Wenn MFA aktiviert ist, aber nicht in der Sitzung ausgeführt werden muss, schlägt die Anforderung fehl. 

Die angezeigte Fehlermeldung lautet wie folgt: „Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to proceed.“ (Aufgrund einer Konfigurationsänderung Ihres Administrators oder aufgrund Ihres Wechsels an einen anderen Standort müssen Sie zum Fortsetzen des Vorgangs die mehrstufige Authentifizierung verwenden.)

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

Die angezeigte Fehlermeldung lautet wie folgt: „Ein überprüfter Herausgeber kann dieser Anwendung nicht hinzugefügt werden. Wenden Sie sich an Ihren Administrator, um Unterstützung zu erhalten.“

Überprüfen Sie zunächst, ob Sie die [Anforderungen für die Herausgeberüberprüfung](publisher-verification-overview.md#requirements) erfüllt haben.

Bei einer Anforderung zum Hinzufügen eines überprüften Herausgebers wird anhand einer Reihe von Signalen eine Sicherheitsrisikobewertung vorgenommen. Wird die Anforderung als riskant eingestuft, wird ein Fehler zurückgegeben. Aus Sicherheitsgründen gibt Microsoft nicht die speziellen Kriterien bekannt, anhand denen die Risikoeinstufung einer Anforderung vorgenommen wird.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie alle vorstehenden Informationen gelesen und entsprechend angewendet haben und immer noch einen Fehler von Microsoft Graph erhalten, sammeln Sie möglichst viele der folgenden Informationen in Bezug auf die fehlgeschlagene Anforderung, und [wenden Sie sich an den Microsoft-Support](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID oder UserPrincipalName des angemeldeten Benutzers 
- ObjectId der Zielanwendung
- AppId der Zielanwendung
- ID des Mandanten (TenantId), bei dem die App registriert ist
- MPN-ID
- Die ausgeführte REST-Anforderung 
- Zurückgegebene(r) Fehlercode und Fehlermeldung