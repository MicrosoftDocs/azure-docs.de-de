---
title: Anmelden mit einer E-Mail-Adresse als alternative Anmelde-ID für Azure Active Directory
description: Hier erfahren Sie, wie Sie die Benutzeranmeldung bei Azure Active Directory über E-Mail-Adressen als alternative Anmelde-ID konfigurieren und aktivieren (Vorschau).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: ff6ae6ea6812397e737deb4b97bf1cd15e022c03
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743172"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Anmelden bei Azure Active Directory per E-Mail-Adresse als alternative Anmelde-ID (Vorschauversion)

> [!NOTE]
> Die Anmeldung bei Azure AD mit der E-Mail-Adresse als alternativer Anmelde-ID ist ein öffentliches Vorschaufeature von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Viele Organisationen möchten es Benutzern ermöglichen, sich mit denselben Anmeldeinformationen bei Azure Active Directory (Azure AD) anzumelden, die sie auch für ihre lokale Verzeichnisumgebung verwenden. Bei diesem Ansatz, der als Hybridauthentifizierung bezeichnet wird, müssen sich Benutzer nur eine Kombination von Anmeldeinformationen merken.

Einige Organisationen sind aus folgenden Gründen nicht auf die Hybridauthentifizierung umgestiegen:

* Standardmäßig wird der Benutzerprinzipalname (User Principal Name, UPN) von Azure Active Directory (Azure AD) auf den gleichen UPN wie für das lokale Verzeichnis festgelegt.
* Wenn Sie den Azure AD-UPN ändern, stimmen lokale und Azure AD-Umgebung nicht mehr überein, was zu Problemen mit bestimmten Anwendungen und Diensten führen kann.
* Aufgrund von betrieblichen oder Compliancegründen möchte die Organisation den lokalen UPN nicht für die Anmeldung bei Azure AD verwenden.

Zur Unterstützung der Umstellung auf die Hybridauthentifizierung können Sie nun Azure AD konfigurieren, damit Benutzer sich mit einer E-Mail-Adresse als alternativer Anmelde-ID in ihrer verifizierten Domäne anmelden können. Wenn *Contoso* beispielsweise in *Fabrikam* umbenannt wird, kann jetzt die E-Mail-Adresse als alternative Anmelde-ID anstelle des alten UPN `balas@contoso.com` verwendet werden. Für den Zugriff auf eine Anwendung oder Dienste melden sich Benutzer mit ihrer zugewiesenen E-Mail-Adresse wie `balas@fabrikam.com` bei Azure AD an.

In diesem Artikel erfahren Sie, wie Sie die E-Mail-Adresse als alternative Anmelde-ID aktivieren und verwenden. Diese Funktion ist in der Azure AD Free Edition und höher verfügbar.

> [!NOTE]
> Diese Funktion ist nur für Azure AD-Benutzer mit Cloudauthentifizierung verfügbar.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Übersicht über Ansätze für die Anmeldung bei Azure AD

Für die Anmeldung bei Azure AD geben Benutzer einen Namen ein, der ihr Konto eindeutig identifiziert. Bisher konnte nur der Azure AD-UPN als Anmeldename verwendet werden.

Für Organisationen, bei denen der lokale UPN der bevorzugten E-Mail-Adresse für Anmeldungen des Benutzers entspricht, war dieser Ansatz perfekt geeignet. In diesen Organisationen kann der Azure AD-UPN auf denselben Wert wie der lokale UPN festgelegt werden, und die Anmeldung erfolgt für Benutzer überall gleich.

In manchen Organisationen wird der lokale UPN jedoch als Anmeldename verwendet. In lokalen Umgebungen können Sie die lokale AD DS-Instanz so konfigurieren, dass Anmeldungen mit einer alternativen Anmelde-ID möglich sind. Es ist nicht möglich, den Azure AD-UPN auf denselben Wert wie den lokalen UPN festzulegen, da sich Benutzer in Azure AD dann mit diesem Wert anmelden müssten.

Die typische Umgehung für dieses Problem besteht darin, den Azure AD-UPN auf die E-Mail-Adresse festzulegen, mit der sich der Benutzer anmelden möchte. Dieser Ansatz funktioniert, führt jedoch zu unterschiedlichen UPNs zwischen lokalen Azure AD-Instanzen und Azure AD selbst. Außerdem ist diese Konfiguration nicht mit allen Microsoft 365-Workloads kompatibel.

Ein anderer Ansatz besteht darin, die UPNs für Azure AD und die lokalen UPNs zu synchronisieren, damit sie denselben Wert aufweisen, und Azure AD dann so zu konfigurieren, dass Benutzer sich mit einer verifizierten E-Mail-Adresse in Azure AD anmelden können. Dazu definieren Sie mindestens eine E-Mail-Adresse im *ProxyAddresses*-Attribut des Benutzers im lokalen Verzeichnis. Das *ProxyAddresses*-Attribut wird dann mithilfe von Azure AD Connect automatisch mit Azure AD synchronisiert.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

Die Anmeldung bei Azure AD mit einer E-Mail-Adresse als alternativer Anmelde-ID ist in der Azure AD Free Edition und höher verfügbar.

In der aktuellen Vorschauversion gelten folgende Einschränkungen, wenn sich ein Benutzer mit einem Nicht-UPN-Wert (E-Mail-Adresse) als alternative Anmelde-ID anmeldet:

* Benutzern wird ihr UPN auch dann angezeigt, wenn sie sich mit ihrer E-Mail-Adresse (Nicht-UPN-Wert) angemeldet haben. Beobachten lässt sich das folgende Beispielverhalten:
    * Der Benutzer wird aufgefordert, sich mit dem UPN anzumelden, wenn er mit `login_hint=<non-UPN email>` zur Azure AD-Anmeldung umgeleitet wird.
    * Wenn ein Benutzer sich mit einem Nicht-UPN-Wert (E-Mail-Adresse) anmeldet und ein falsches Kennwort eingibt, wird die Seite *Kennwort eingeben* geändert und zeigt den UPN an.
    * Auf einigen Microsoft-Websites und in einigen Microsoft-Apps (z. B. [https://portal.azure.com](https://portal.azure.com) und Microsoft Office) wird in der Regel in der oberen rechten Ecke des Steuerelements **Kontomanager** der UPN des Benutzers und nicht der zum Anmelden verwendete Nicht-UPN-Wert (E-Mail-Adresse) angezeigt.

* Einige Flows sind derzeit nicht mit Nicht-UPN-Werten (E-Mail-Adressen) kompatibel:
    * Identity Protection gleicht derzeit keine E-Mail-Adressen als alternative Anmelde-IDs mit der Risikoerkennung *Kompromittierte Anmeldeinformationen* ab. Diese Risikoerkennung verwendet zum Abgleichen kompromittierter Anmeldeinformationen den UPN. Weitere Informationen finden Sie unter [Risikoerkennung und Problembehandlung in Azure AD Identity Protection][identity-protection].
    * An eine E-Mail-Adresse als alternative Anmelde-ID gesendete B2B-Einladungen werden nicht vollständig unterstützt. Wenn Sie eine Einladung angenommen haben, die an eine E-Mail-Adresse als alternative Anmelde-ID gesendet wurde, funktioniert die Anmeldung mit der alternativen E-Mail-Adresse möglicherweise bei dem Benutzer auf dem Mandantenendpunkt nicht.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchronisieren von Anmelde-E-Mail-Adressen mit Azure AD

Die Authentifizierung herkömmlicher Active Directory Domain Services (AD DS) oder Active Directory-Verbunddienste (AD FS) erfolgt direkt in Ihrem Netzwerk und wird von Ihrer AD DS-Infrastruktur verarbeitet. Bei der Hybridauthentifizierung können sich Benutzer stattdessen direkt bei Azure AD anmelden.

Zur Unterstützung dieses Hybridauthentifizierungsansatzes synchronisieren Sie Ihre lokale AD DS-Umgebung mithilfe von [Azure AD Connect][azure-ad-connect] mit Azure AD und konfigurieren sie für die Verwendung der Kennworthashsynchronisierung (Password Hash Sync, PHS) oder der Passthrough-Authentifizierung (PTA).

Bei beiden Konfigurationsoptionen übermittelt der Benutzer seinen Benutzernamen und sein Kennwort an Azure AD, das die Anmeldeinformationen überprüft und ein Ticket ausstellt. Wenn sich Benutzer bei Azure AD anmelden, ist es nicht mehr erforderlich, dass Ihre Organisation eine AD FS-Infrastruktur hostet und verwaltet.

![Diagramm der Azure AD-Hybrididentität mit Kennworthashsynchronisierung](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagramm der Azure AD-Hybrididentität mit Passthrough-Authentifizierung](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Eines der Benutzerattribute, das automatisch von Azure AD Connect synchronisiert wird, ist *ProxyAddresses*. Wenn Benutzer eine E-Mail-Adresse besitzen, die in der lokalen AD DS-Umgebung als Teil des *ProxyAddresses*-Attributs definiert ist, wird diese automatisch mit Azure AD synchronisiert. Diese E-Mail-Adresse kann dann direkt beim Azure AD-Anmeldevorgang als alternative Anmelde-ID verwendet werden.

> [!IMPORTANT]
> Nur E-Mail-Adressen in für den Mandanten verifizierten Domänen werden mit Azure AD synchronisiert. Jeder Azure AD-Mandant verfügt über mindestens eine verifizierte Domäne, deren Eigentümer er nachweislich ist, und die eindeutig an ihren Mandanten gebunden ist.
>
> Weitere Informationen finden Sie unter [Hinzufügen und Verifizieren eines benutzerdefinierten Domänennamens in Azure][verify-domain].

Weitere Informationen finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Aktivieren der Benutzeranmeldung mit einer E-Mail-Adresse

Nachdem Benutzer mit angewendetem *ProxyAddresses*-Attribut mithilfe von Azure AD Connect mit Azure AD synchronisiert wurden, müssen Sie die Funktion aktivieren, mit der sich Benutzer per E-Mail-Adresse als alternativer Anmelde-ID für Ihren Mandanten anmelden können. Diese Funktion weist die Azure AD-Anmeldeserver an, den Anmeldenamen nicht nur mit UPN-Werten, sondern auch mit *ProxyAddresses*-Werten für die E-Mail-Adresse zu vergleichen.

Während der Vorschauphase können Sie die Funktion zur Anmeldung per E-Mail-Adresse als alternativer Anmelde-ID derzeit nur mit PowerShell aktivieren. Sie benötigen die Berechtigungen als *Mandantenadministrator*, um die folgenden Schritte auszuführen:

1. Öffnen Sie eine PowerShell-Sitzung als Administrator, und installieren Sie dann das [AzureADPreview][Install-Module]-Modul mithilfe des Cmdlets *Install-Module*:

    ```powershell
    Install-Module AzureADPreview
    ```

    Wenn Sie dazu aufgefordert werden, wählen Sie **Y**, um NuGet zu installieren oder das Modul aus einem nicht vertrauenswürdigen Repository zu installieren.

1. Melden Sie sich bei Ihrem Azure AD-Mandanten als *Mandantenadministrator* an, indem Sie das Cmdlet [Connect-AzureAD][Connect-AzureAD] verwenden:

    ```powershell
    Connect-AzureAD
    ```

    Der Befehl gibt die Informationen zu Ihrem Konto, Ihrer Umgebung und Ihrer Mandanten-ID zurück.

1. Überprüfen Sie, ob die Richtlinie *HomeRealmDiscoveryPolicy* in Ihrem Mandanten bereits vorhanden ist, indem Sie das Cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] wie folgt ausführen:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Wenn derzeit keine Richtlinie konfiguriert ist, gibt der Befehl nichts zurück. Wenn eine Richtlinie zurückgegeben wird, können Sie diesen Schritt überspringen und mit dem nächsten Schritt fortfahren, um eine vorhandene Richtlinie zu aktualisieren.

    Um die Richtlinie *HomeRealmDiscoveryPolicy* dem Mandanten hinzuzufügen, verwenden Sie das Cmdlet [New-AzureADPolicy][New-AzureADPolicy], und legen Sie das *AlternateIdLogin*-Attribut auf *"Enabled": true* wie im folgenden Beispiel fest:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Wenn die Richtlinie erfolgreich erstellt wurde, gibt der Befehl die Richtlinien-ID zurück, wie in der folgenden Beispielausgabe dargestellt:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Wenn bereits eine Richtlinie konfiguriert ist, überprüfen Sie, ob das  *AlternateIdLogin* -Attribut aktiviert ist, wie in der folgenden Beispielrichtlinienausgabe dargestellt:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Wenn die Richtlinie vorhanden ist, das *AlternateIdLogin*-Attribut jedoch nicht vorhanden oder nicht aktiviert ist, oder wenn andere Attribute für die Richtlinie vorhanden sind, die Sie beibehalten möchten, aktualisieren Sie die vorhandene Richtlinie mithilfe des Cmdlets [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Wenn Sie die Richtlinie aktualisieren, stellen Sie sicher, dass Sie alle alten Einstellungen und das neue *AlternateIdLogin*-Attribut einschließen.

    Im folgenden Beispiel wird das  *AlternateIdLogin*-Attribut hinzugefügt und das möglicherweise bereits festgelegte  *AllowCloudPasswordValidation*-Attribut beibehalten:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Vergewissern Sie sich, dass die aktualisierte Richtlinie ihre Änderungen aufweist und dass das *AlternateIdLogin*-Attribut jetzt aktiviert ist:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

Nachdem die Richtlinie angewendet wurde, kann es bis zu einer Stunde dauern, bis Benutzer sich mit Ihrer alternativen Anmelde-ID anmelden können.

## <a name="test-user-sign-in-with-email"></a>Testen der Benutzeranmeldung per E-Mail-Adresse

Um zu testen, ob Benutzer sich mit ihrer E-Mail-Adresse anmelden können, navigieren Sie zu [https://myprofile.microsoft.com][my-profile], und melden Sie sich mit einem Benutzerkonto an, das auf ihrer E-Mail-Adresse basiert, z. B. `balas@fabrikam.com`, und nicht auf ihrem UPN, z. B. `balas@contoso.com`. Die Anmeldung sollte wie bei einem UPN-basierten Anmeldeereignis aussehen und funktionieren.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Aktivieren von gestaffelten Rollouts zum Testen der Benutzeranmeldung mit einer E-Mail-Adresse  

Ein [gestaffelter Rollout][staged-rollout] ermöglicht es Mandantenadministratoren, Features für bestimmte Gruppen zu aktivieren. Es wird empfohlen, dass Mandantenadministratoren einen gestaffelten Rollout verwenden, um die Benutzeranmeldung mit einer E-Mail-Adresse zu testen. Sobald das Feature für den gesamten Mandanten bereitgestellt werden kann, sollten die Administratoren eine Richtlinie zur Startbereichsermittlung verwenden.  


Sie benötigen die Berechtigungen als *Mandantenadministrator*, um die folgenden Schritte auszuführen:

1. Öffnen Sie eine PowerShell-Sitzung als Administrator, und installieren Sie dann das [AzureADPreview][Install-Module]-Modul mithilfe des Cmdlets *Install-Module*:

    ```powershell
    Install-Module AzureADPreview
    ```

    Wenn Sie dazu aufgefordert werden, wählen Sie **Y**, um NuGet zu installieren oder das Modul aus einem nicht vertrauenswürdigen Repository zu installieren.

2. Melden Sie sich bei Ihrem Azure AD-Mandanten als *Mandantenadministrator* an, indem Sie das Cmdlet [Connect-AzureAD][Connect-AzureAD] verwenden:

    ```powershell
    Connect-AzureAD
    ```

    Der Befehl gibt die Informationen zu Ihrem Konto, Ihrer Umgebung und Ihrer Mandanten-ID zurück.

3. Führen Sie die folgenden Cmdlets aus, um alle vorhandenen Richtlinien für gestaffelte Rollouts aufzulisten:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Wenn für dieses Feature keine Richtlinien für gestaffelte Rollouts vorhanden sind, erstellen Sie eine neue Richtlinie für gestaffelte Rollouts, und notieren Sie sich die Richtlinien-ID:

   ```powershell
   New-AzureADMSFeatureRolloutPolicy -Feature EmailAsAlternateId -DisplayName "EmailAsAlternateId Rollout Policy" -IsEnabled $true
   ```

5. Suchen Sie die directoryObject-ID für die Gruppe, die der Richtlinie für gestaffelte Rollouts hinzugefügt werden soll. Notieren Sie sich den Wert, der für den *Id*-Parameter zurückgegeben wird, da Sie ihn im nächsten Schritt benötigen.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Fügen Sie die Gruppe wie im folgenden Beispiel der Richtlinie für gestaffelte Rollouts hinzu. Ersetzen Sie den Wert im Parameter *-Id* durch den Wert, der in Schritt 4 für die Richtlinien-ID zurückgegeben wurde, und den Wert im Parameter *-RefObjectId* durch die *Id*, die Sie sich in Schritt 5 notiert haben. Es kann bis zu einer Stunde dauern, bis Benutzer in der Gruppe ihre Proxyadressen für die Anmeldung verwenden können.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Bei neuen Mitgliedern, die der Gruppe hinzugefügt werden, kann es bis zu 24 Stunden dauern, bis sie ihre Proxyadressen für die Anmeldung verwenden können.

### <a name="removing-groups"></a>Entfernen von Gruppen

Führen Sie den folgenden Befehl aus, um eine Gruppe aus einer Richtlinie für gestaffelte Rollouts zu entfernen:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Entfernen von Richtlinien

Um eine Richtlinie für gestaffelte Rollouts zu entfernen, deaktivieren Sie die Richtlinie zunächst und entfernen sie dann aus dem System:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Problembehandlung

Wenn Benutzer Probleme mit Anmeldeereignissen mit Ihrer E-Mail-Adresse haben, versuchen Sie es mit den folgenden Schritten zur Problembehandlung:

1. Stellen Sie sicher, dass für das Benutzerkonto in der lokalen AD DS-Umgebung eine E-Mail-Adresse für das *ProxyAddresses*-Attribut festgelegt ist.
1. Vergewissern Sie sich, dass Azure AD Connect konfiguriert ist und die Benutzerkonten aus der lokalen AD DS-Umgebung erfolgreich mit Azure AD synchronisiert.
1. Vergewissern Sie sich, dass für die Azure AD-Richtlinie *HomeRealmDiscoveryPolicy* das *AlternateIdLogin*-Attribut auf *"Enabled": true* festgelegt ist:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Hybrididentität, z. B. mit Azure AD App Proxy oder Azure AD Domain Services, finden Sie unter [Azure AD Hybrididentität für den Zugriff und die Verwaltung lokaler Workloads][hybrid-overview].

Weitere Informationen zu Hybrididentitätsvorgängen finden Sie unter [Funktionsweise der Kennworthashsynchronisierung][phs-overview] und [Funktionsweise der Passthrough-Authentifizierung][pta-overview].

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
