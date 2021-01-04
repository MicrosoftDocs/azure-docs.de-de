---
title: Konfigurieren der automatischen Anmeldebeschleunigung mit der Startbereichsermittlung
description: Erfahren Sie, wie Sie eine Richtlinie für die Startbereichsermittlung für die Azure Active Directory-Authentifizierung für Verbundbenutzer konfigurieren, einschließlich automatischer Beschleunigung und Domänenhinweisen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/08/2019
ms.author: kenwith
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 873a87ed2c75d41e0a249bde4b6a29921b7e5ce5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648054"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurieren des Verhaltens der Azure Active Directory-Anmeldung für eine Anwendung mit einer Richtlinie für die Startbereichsermittlung (Home Realm Discovery, HDR)

Dieser Artikel enthält eine Einführung zum Konfigurieren des Azure Active Directory-Authentifizierungsverhaltens für Verbundbenutzer. Es behandelt die Konfiguration der automatischen Beschleunigung und Authentifizierungseinschränkungen für Benutzer in Verbunddomänen.

## <a name="home-realm-discovery"></a>Startbereichsermittlung (Home Realm Discovery, HDR)
Die Startbereichsermittlung (Home Realm Discovery, HDR) bezeichnet den Prozess, mit dem Azure Active Directory (Azure AD) ermitteln kann, wo sich ein Benutzer bei der Anmeldung authentifizieren muss.  Wenn sich ein Benutzer bei einem Azure AD-Mandanten oder auf der allgemeinen Anmeldeseite von Azure AD anmeldet, um auf eine Ressource zuzugreifen, gibt er einen Benutzernamen (UPN) ein. Azure AD verwendet diesen, um zu ermitteln, wo sich der Benutzer anmelden muss. 

Der Benutzer muss möglicherweise an einen der folgenden Speicherorte weitergeleitet werden, um sich zu authentifizieren:

- Basismandant des Benutzers (dies kann der gleiche Mandant wie die Ressource sein, auf die der Benutzer zugreifen möchte). 

- Microsoft-Konto:  Der Benutzer ist ein Gast des Ressourcenmandanten.

-  Lokaler Identitätsanbieter, z.B. Active Directory-Verbunddienste (AD FS).

- Anderer Identitätsanbieter, der einen Verbund mit dem Azure AD-Mandanten bildet.

## <a name="auto-acceleration"></a>Automatische Beschleunigung 
Einige Unternehmen konfigurieren Domänen in ihrem Azure Active Directory-Mandanten so, dass er mit einem anderen Identitätsanbieter wie AD FS zur Benutzerauthentifizierung einen Verbund bildet.  

Wenn sich ein Benutzer bei einer Anwendung anmeldet, wird zunächst eine Azure AD-Anmeldeseite angezeigt. Falls er sich in einer Verbunddomäne befindet, wird er nach Eingabe seines UPN zur Anmeldeseite des Identitätsanbieters für diese Domäne weitergeleitet. Unter bestimmten Umständen können Administratoren Benutzer auf die Anmeldeseite weiterleiten, wenn sie sich bei bestimmten Anwendungen anmelden. 

Daher können Benutzer die erste Seite von Azure Active Directory überspringen. Dieser Vorgang wird als „automatische Beschleunigung der Anmeldung“ bezeichnet.

In den Fällen, in denen der Mandant mit einem anderen IdP für die Anmeldung einen Verbund bildet, wird die Benutzeranmeldung durch die automatische Beschleunigung optimiert.  Sie können die automatische Beschleunigung für einzelne Anwendungen konfigurieren.

>[!NOTE]
>Wenn Sie eine Anwendung für die automatische Beschleunigung konfigurieren, können sich Gastbenutzer nicht anmelden. Wenn Sie einen Benutzer direkt an einen Verbund-IdP für die Authentifizierung weiterleiten, gibt es für ihn keine Möglichkeit, zur Anmeldeseite von Azure Active Directory zurückzukehren. Gastbenutzer, die möglicherweise an andere Mandanten oder an einen externen IdP wie ein Microsoft-Konto weitergeleitet werden müssen, können sich nicht bei dieser Anwendung anmelden, weil sie den Schritt „Startbereichsermittlung“ (Home Realm Discovery, HDR) überspringen.  

Es gibt zwei Möglichkeiten, die automatische Beschleunigung zu einem Verbundidentitätsanbieter zu steuern:   

- Verwenden Sie einen Domänenhinweis für Authentifizierungsanforderungen für eine Anwendung. 
- Konfigurieren Sie eine Richtlinie zur Startbereichsermittlung, um die automatische Beschleunigung zu aktivieren.

### <a name="domain-hints"></a>Domänenhinweise    
Domänenhinweise sind in der Authentifizierungsanforderung einer Anwendung enthaltene Anweisungen. Sie können verwendet werden, um die beschleunigte Anmeldung des Benutzers auf der Anmeldeseite seines Verbundidentitätsanbieters zu ermöglichen. Sie können auch von einer Anwendung für mehrere Mandanten verwendet werden, um den Benutzer beschleunigt direkt zur organisationsspezifischen Azure AD-Anmeldeseite für ihren Mandanten zu leiten.  

Die Anwendung „largeapp.com“ könnte es z.B. ihren Kunden ermöglichen, auf die Anwendung über eine benutzerdefinierte URL wie „contoso.largeapp.com“ zuzugreifen. Dabei könnte die App einen Domänenhinweis auf „contoso.com“ in die Authentifizierungsanforderung einbeziehen. 

Die Syntax der Domänenhinweise variiert je nach verwendetem Protokoll und wird in der Regel in der Anwendung konfiguriert.

**WS-Verbund**: „whr=contoso.com“ in der Abfragezeichenfolge.

**SAML**:  Eine SAML-Authentifizierungsanforderung, die einen Domänenhinweis enthält, oder eine Abfragezeichenfolge „whr=contoso.com“.

**OpenID Connect**: Eine Abfragezeichenfolge „domain_hint=contoso.com“. 

Wenn ein Domänenhinweis in der Authentifizierungsanforderung der Anwendung enthalten und der Mandant mit dieser Domäne verbunden ist, versucht Azure AD, die Anmeldung an den für diese Domäne konfigurierten Identitätsanbieter umzuleiten. 

Wenn sich der Domänenhinweis nicht auf eine überprüfte Verbunddomäne bezieht, wird diese ignoriert und die normale Startbereichsermittlung (Home Realm Discovery, HDR) aufgerufen.

Weitere Informationen zur automatischen Beschleunigung unter Verwendung der Domänenhinweise, die von Azure Active Directory unterstützt werden, finden Sie im Blog [Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Wenn ein Domänenhinweis in einer Authentifizierungsanforderung enthalten ist, wird dadurch die automatische Beschleunigung außer Kraft gesetzt, die für die Anwendung in der Richtlinie zur Startbereichsermittlung festgelegt ist.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Richtlinie zur Startbereichsermittlung für die automatische Beschleunigung
Einige Anwendungen bieten keine Möglichkeit, die von ihnen ausgegebene Authentifizierungsanforderung zu konfigurieren. In diesen Fällen ist es nicht möglich, Domänenhinweise zur Steuerung der automatischen Beschleunigung zu verwenden. Die automatische Beschleunigung kann über Richtlinien konfiguriert werden, um das gleiche Verhalten zu erreichen.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Aktivieren der direkten ROPC-Authentifizierung von Verbundbenutzern für Legacyanwendungen
Als bewährte Methode verwenden Anwendungen AAD-Bibliotheken und interaktive Anmeldung zur Authentifizierung von Benutzern. Die Bibliotheken kümmern sich um die Verbundbenutzerflows.  Manchmal übermitteln Legacyanwendungen Benutzername und Kennwort direkt an Azure AD, insbesondere diejenigen, die ROPC-Zuweisungen verwenden. Sie wurden nicht so geschrieben, dass sie den Verbund verstehen. Sie führen keine Startbereichsermittlung durch und interagieren nicht mit dem richtigen Verbundendpunkt, um einen Benutzer zu authentifizieren. Falls gewünscht, können Sie mithilfe einer Richtlinie zur Startbereichsermittlung (Home Realm Discovery, HRD) bestimmten älteren Anwendungen das Übermitteln von Anmeldeinformationen (Benutzername und Kennwort) mithilfe der ROPC-Zuweisung ermöglichen, um eine direkte Authentifizierung bei Azure Active Directory durchzuführen. Die Kennworthashsynchronisierung muss aktiviert sein. 

> [!IMPORTANT]
> Aktivieren Sie die direkte Authentifizierung nur dann, wenn die Kennworthashsynchronisierung aktiviert ist und Sie sicher sind, dass diese Anwendung problemlos authentifiziert werden kann, ohne dass Richtlinien von Ihrem lokalen Identitätsanbieter implementiert werden. Wenn Sie die Kennworthashsynchronisierung oder die Verzeichnissynchronisierung mit AD Connect aus irgendeinem Grund deaktivieren, müssen Sie diese Richtlinie entfernen, um die Möglichkeit der direkten Authentifizierung mit einem veralteten Kennworthash zu verhindern.

## <a name="set-hrd-policy"></a>Festlegen der Richtlinie zur Startbereichsermittlung
Um in einer Anwendung für die automatische Beschleunigung bei der Verbundanmeldung oder für direkte cloudbasierte Anwendungen eine Richtlinie zur Startbereichsermittlung festzulegen, sind drei Schritte durchzuführen:

1. Erstellen einer Richtlinie zur Startbereichsermittlung

2. Ermitteln des Dienstprinzipals, an den die Richtlinie angefügt wird

3. Anfügen der Richtlinie an den Dienstprinzipal 

Richtlinien treten für eine bestimmte Anwendung nur in Kraft, wenn sie an einen Dienstprinzipal angefügt werden. 

Es kann immer nur eine Richtlinie zur Startbereichsermittlung auf einem Dienstprinzipal aktiv sein.  

Sie können eine Richtlinie zur Startbereichsermittlung mit den Azure Active Directory PowerShell-Cmdlets erstellen und verwalten.

Das folgende Beispiel zeigt eine Definition für eine Richtlinie zur Startbereichsermittlung:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false
    }
   }
```

Der Richtlinientyp ist „HomeRealmDiscoveryPolicy“.

**AccelerateToFederatedDomain** ist optional. Wenn **AccelerateToFederatedDomain** FALSE ist, besitzt die Richtlinie keine Auswirkungen auf die automatische Beschleunigung. Wenn **AccelerateToFederatedDomain** TRUE ist und der Mandant nur eine verifizierte Verbunddomäne aufweist, werden Benutzer zur Anmeldung direkt zum Verbundidentitätsanbieter weitergeleitet. Wenn die Option TRUE ist und der Mandant mehrere verifizierte Domänen aufweist, muss **PreferredDomain** angegeben werden.

**PreferredDomain** ist optional. **PreferredDomain** sollte eine Domäne angeben, zu der die Beschleunigung erfolgt. Die Angabe kann ausgelassen werden, wenn der Mandant nur eine Verbunddomäne besitzt.  Wenn diese Angabe ausgelassen wird und mehrere überprüfte Verbunddomänen verfügbar sind, besitzt die Richtlinie keine Auswirkungen.

 Wenn **PreferredDomain** angegeben wird, muss diese Angabe mit einer überprüften Verbunddomäne für den Mandanten übereinstimmen. Alle Benutzer der Anwendung müssen sich bei der Domäne anmelden können.

**AllowCloudPasswordValidation** ist optional. Wenn **AllowCloudPasswordValidation** TRUE ist, darf die Anwendung einen Verbundbenutzer durch direkte Übermittlung von Anmeldeinformationen (Benutzername/Kennwort) an den Tokenendpunkt von Azure Active Directory authentifizieren. Dies funktioniert nur, wenn die Kennworthashsynchronisierung aktiviert ist.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorität und Auswertung von Richtlinien zur Startbereichsermittlung
Richtlinien zur Startbereichsermittlung können erstellt und dann bestimmten Organisationen und Dienstprinzipalen zugewiesen werden. Dies bedeutet, dass mehrere Richtlinien auf eine bestimmte Anwendung angewandt werden können. Die Richtlinie zur Startbereichsermittlung, die in Kraft tritt, folgt diesen Regeln:


- Wenn in der Authentifizierungsanforderung ein Domänenhinweis vorhanden ist, wird jede Richtlinie zur Startbereichsermittlung bei der automatischen Beschleunigung ignoriert. Das Verhalten, das vom Domänenhinweis angegeben wird, wird verwendet.

- Andernfalls wird sie erzwungen, wenn eine Richtlinie explizit dem Dienstprinzipal zugewiesen ist. 

- Wenn kein Domänenhinweis vorhanden und dem Dienstprinzipal nicht explizit eine Richtlinie zugewiesen ist, wird eine Richtlinie erzwungen, die ausdrücklich der übergeordneten Organisation des Dienstprinzipals zugewiesen ist. 

- Wenn kein Domänenhinweis vorhanden ist und dem Dienstprinzipal oder der Organisation keine Richtlinie zugewiesen wurde, wird das standardmäßige Verhalten der Startbereichsermittlung verwendet.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial zum Festlegen einer Richtlinie zur Startbereichsermittlung für eine Anwendung 
Wir verwenden Azure AD PowerShell-Cmdlets, um einige Szenarien zu erläutern, darunter:


- Einrichten einer Richtlinie zur Startbereichsermittlung zum Durchführen der automatischen Beschleunigung für eine Anwendung in einem Mandanten mit einer einzigen Verbunddomäne.

- Einrichten einer Richtlinie zur Startbereichsermittlung zum Durchführen der automatischen Beschleunigung für eine Anwendung in einer von mehreren Domänen, die für Ihren Mandanten überprüft wurden.

- Einrichten einer Richtlinie zur Startbereichsermittlung, um einer älteren Anwendung die direkte Authentifizierung eines Verbundbenutzers bei Azure Active Directory über Benutzername/Kennwort zu ermöglichen.

- Auflisten der Anwendungen, für die eine Richtlinie konfiguriert ist.


### <a name="prerequisites"></a>Voraussetzungen
In den folgenden Beispielen erstellen, aktualisieren, verknüpfen und löschen Sie Richtlinien für Anwendungsdienstprinzipale in Azure AD.

1.  Laden Sie zunächst die aktuelle Preview zu Azure AD PowerShell-Cmdlets herunter. 

2.  Nach dem Herunterladen der Azure AD PowerShell-Cmdlets führen Sie den Befehl „Connect“ aus, um sich bei Azure AD mit Ihrem Administratorkonto anzumelden:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Führen Sie den folgenden Befehl aus, um alle Richtlinien in Ihrer Organisation anzuzeigen:

    ``` powershell
    Get-AzureADPolicy
    ```

Wenn nichts zurückgegeben wird, bedeutet dies, dass in Ihrem Mandanten keine Richtlinien erstellt wurden.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Beispiel: Festlegen einer Richtlinie zur Startbereichsermittlung für eine Anwendung 

In diesem Beispiel erstellen Sie eine Richtlinie, die eine der folgenden Aktionen durchführt, sobald Sie einer Anwendung zugewiesen wird: 
- Wenn Ihr Mandant eine einzige Domäne aufweist, werden Benutzer beim Anmelden bei einer Anwendung automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet. 
- Wenn Ihr Mandant mehrere Verbunddomänen aufweist, werden Benutzer automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet.
- Ermöglicht Verbundbenutzern die nicht interaktive direkte Anmeldung bei Azure Active Directory über Benutzername/Kennwort für die Anwendungen, denen die Richtlinie zugewiesen ist.

#### <a name="step-1-create-an-hrd-policy"></a>Schritt 1: Erstellen einer Richtlinie zur Startbereichsermittlung

Wenn Ihr Mandant eine einzige Domäne aufweist, werden Benutzer beim Anmelden bei einer Anwendung durch die folgende Richtlinie automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Wenn Ihr Mandant mehrere Verbunddomänen aufweist, werden Benutzer durch die folgende Richtlinie automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet. Wenn mehrere Verbunddomänen vorliegen, die Benutzer für Anwendungen authentifizieren, müssen Sie die Domäne für die automatische Beschleunigung angeben.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Um eine Richtlinie zum Aktivieren der direkten Authentifizierung von Verbundbenutzern bei Azure Active Directory über Benutzername/Kennwort für bestimmte Anwendungen zu erstellen, führen Sie den folgenden Befehl aus:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Um Ihre neue Richtlinie und deren **ObjectId** abzurufen, führen Sie den folgenden Befehl aus:

``` powershell
Get-AzureADPolicy
```


Um die Richtlinie zur Startbereichsermittlung nach ihrer Erstellung anzuwenden, können Sie sie mehreren Anwendungsdienstprinzipalen zuweisen.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Schritt 2: Ermitteln des Dienstprinzipals, dem die Richtlinie zugewiesen werden soll  
Sie benötigen die **ObjectID** der Dienstprinzipale, denen Sie die Richtlinie zuweisen möchten. Es gibt mehrere Möglichkeiten, die **ObjectID** von Dienstprinzipalen zu ermitteln.    

Sie können das Portal verwenden, oder Sie können [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta) abfragen. Sie können auch zum [Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) navigieren und sich bei Ihrem Azure AD-Konto anmelden, um alle Dienstprinzipale Ihrer Organisation anzuzeigen. 

Da Sie PowerShell verwenden, können Sie das folgende Cmdlet verwenden, um die Dienstprinzipale sowie deren IDs aufzulisten.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Schritt 3: Zuweisen der Richtlinie zu Ihrem Dienstprinzipal  
Nachdem Sie über die **ObjectID** des Dienstprinzipals der Anwendung verfügen, für die Sie automatische Beschleunigung konfigurieren möchten, führen Sie den folgenden Befehl aus. Dieser Befehl ordnet die Richtlinie zur Startbereichsermittlung, die Sie in Schritt 1 erstellt haben, dem Dienstprinzipal zu, den Sie in Schritt 2 ermittelt haben.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Sie können diesen Befehl für jeden Dienstprinzipal wiederholen, dem Sie die Richtlinie hinzufügen möchten.

Falls einer Anwendung bereits eine HomeRealmDiscovery-Richtlinie zugewiesen wurde, können Sie keine zweite hinzufügen.  In diesem Fall ändern Sie die Definition der Richtlinie zur Startbereichsermittlung, die der Anwendung zugewiesen ist, und fügen Sie zusätzliche Parameter hinzu.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Schritt 4: Überprüfen, welchen Anwendungsdienstprinzipalen Ihre Richtlinie zur Startbereichsermittlung zugewiesen ist
Um zu überprüfen, welche Anwendungen über eine konfigurierte Richtlinie zur Startbereichsermittlung verfügen, verwenden Sie das Cmdlet **Get-AzureADPolicyAppliedObject**. Übergeben Sie ihm die **ObjectID** der Richtlinie, die Sie überprüfen möchten.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Schritt 5: Sie haben es geschafft!
Testen Sie die Anwendung, um zu überprüfen, ob die neue Richtlinie funktioniert.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Beispiel: Auflisten der Anwendungen, für die eine Richtlinie zur Startbereichsermittlung konfiguriert ist

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Schritt 1: Auflisten aller Richtlinien, die in Ihrer Organisation erstellt wurden 

``` powershell
Get-AzureADPolicy
```

Notieren Sie sich die **ObjectID** der Richtlinie, für die Sie Zuordnungen auflisten möchten.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Schritt 2: Auflisten der Dienstprinzipale, denen die Richtlinie zugewiesen ist  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Beispiel: Entfernen einer Richtlinie zur Startbereichsermittlung aus einer Anwendung
#### <a name="step-1-get-the-objectid"></a>Schritt 1: Abrufen der ObjectID
Verwenden Sie das vorherige Beispiel, um die **ObjectID** der Richtlinie und die ObjectID des Anwendungsdienstprinzipals abzurufen, von dem Sie sie entfernen möchten. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Schritt 2: Entfernen der Richtlinienzuordnung vom Anwendungsdienstprinzipal  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Schritt 3: Überprüfen der Entfernung durch Auflisten der Dienstprinzipale, denen die Richtlinie zugewiesen ist 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Funktionsweise der Authentifizierung in Azure AD finden Sie unter [Authentifizierungsszenarien für Azure AD](../develop/authentication-vs-authorization.md).
- Weitere Informationen zum einmaligen Anmelden von Benutzern finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](what-is-single-sign-on.md).
- Besuchen Sie die [Microsoft Identity Platform](../develop/v2-overview.md), um eine Übersicht über alle entwicklerbezogenen Inhalte zu erhalten.