---
title: Administratorübernahme eines nicht verwalteten Verzeichnisses – Azure Active Directory | Microsoft-Dokumentation
description: Vorgehensweise bei der Übernahme eines DNS-Domänennamens in einem nicht verwalteten Verzeichnis (Schattenmandant) in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b32ef37c6d61c88a18acd5ddc80cc6154369ca29
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780537"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Übernehmen eines nicht verwalteten Verzeichnisses als Administrator in Azure Active Directory

In diesem Artikel wird beschrieben, wie ein DNS-Domänenname in einem nicht verwalteten Verzeichnis in Azure Active Directory (Azure AD) übernommen wird. Wenn sich ein Self-Service-Benutzer für einen Clouddienst registriert, der Azure AD verwendet, wird er auf der Grundlage seiner E-Mail-Domäne einem nicht verwalteten Azure AD-Verzeichnis hinzugefügt. Weitere Informationen zur Self-Service- oder „viralen“ Registrierung für einen Dienst finden Sie im Artikel zu der Frage, [was die Self-Service-Registrierung für Azure Active Directory ist](directory-self-service-signup.md).

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Entscheiden, wie ein nicht verwaltetes Verzeichnis übernommen werden soll
Während der Administratorübernahme können Sie die Inhaberschaft wie in [Schnellstart: Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md) beschrieben nachweisen. In den nächsten Abschnitten wird die Administratorerfahrung im Detail erläutert. Hier zunächst eine Zusammenfassung:

* Bei einer [„internen“ Administratorübernahme](#internal-admin-takeover) eines nicht verwalteten Azure-Verzeichnisses werden Sie als globaler Administrator des nicht verwalteten Verzeichnisses hinzugefügt. Es werden keine Benutzer, Domänen oder Tarife in ein anderes Verzeichnis migriert, das Sie verwalten.

* Bei einer [„externen“ Administratorübernahme](#external-admin-takeover) eines nicht verwalteten Azure-Verzeichnisses fügen Sie Ihrem verwalteten Azure-Verzeichnis den DNS-Domänennamen des nicht verwalteten Verzeichnisses hinzu. Wenn Sie den Domänennamen hinzufügen, wird eine Zuordnung von Nutzern zu Ressourcen in Ihrem verwalteten Azure-Verzeichnis erstellt, damit Benutzer weiterhin ohne Unterbrechung auf Dienste zugreifen können. 

## <a name="internal-admin-takeover"></a>Interne Administratorübernahme

Einige Produkte, die SharePoint und OneDrive enthalten, z.B. Office 365, unterstützen keine externe Übernahme. Wenn dieses Szenario auf Sie zutrifft oder Sie ein Administrator sind und einen nicht verwalteten Schattenmandanten übernehmen möchten, der von Benutzern erstellt wurde, die die Self-Service-Registrierung verwendet haben, können Sie dies mit einer internen Administratorübernahme tun.

1. Erstellen Sie einen Benutzerkontext im nicht verwalteten Mandanten über eine Registrierung bei Power BI. Genau dies tun Sie in diesem Beispiel.

2. Öffnen Sie die [Power BI-Website](https://powerbi.com), und klicken Sie auf **Kostenloser Einstieg**. Geben Sie ein Benutzerkonto ein, das den Domänennamen für die Organisation verwendet, z.B. `admin@fourthcoffee.xyz`. Nachdem Sie den Prüfcode eingegeben haben, suchen Sie in Ihren E-Mails nach dem Bestätigungscode.

3. Wählen Sie in der Bestätigungs-E-Mail von Power BI **Yes, that's me** (Ja, das bin ich) aus.

4. Melden Sie sich mit dem Power BI-Benutzerkonto im [Microsoft 365 Admin Center](https://admin.microsoft.com) an. Sie erhalten eine Nachricht mit der Aufforderung, der Administrator des Domänennamens zu werden (**Become the Admin**), der bereits im nicht verwalteten Mandanten bestätigt wurde. Wählen Sie **Yes, I want to be the admin** (Ja, ich möchte der Administrator werden) aus.
  
   ![Erster Screenshot für „Become the Admin“](./media/domains-admin-takeover/become-admin-first.png)
  
5. Fügen Sie den TXT-Eintrag hinzu, um nachzuweisen, dass Sie den Domänennamen **fourthcoffee.xyz** bei Ihrer Domänennamen-Registrierungsstelle besitzen. In diesem Beispiel ist dies „GoDaddy.com“.
  
   ![Hinzufügen eines TXT-Eintrags für den Domänennamen](./media/domains-admin-takeover/become-admin-txt-record.png)

Wenn die DNS-TXT-Einträge bei Ihrer Domänennamen-Registrierungsstelle bestätigt wurden, können Sie den Azure AD-Mandanten verwalten.

Wenn Sie die vorherigen Schritte abgeschlossen haben, sind Sie jetzt der globale Administrator des Fourth Coffee-Mandanten in Office 365. Um den Domänennamen in Ihre anderen Azure-Dienste zu integrieren, entfernen Sie ihn aus Office 365, und fügen Sie ihn einem anderen verwalteten Mandanten in Azure hinzu.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Hinzufügen des Domänennamens zu einem verwalteten Mandanten in Azure AD

1. Öffnen Sie das [Microsoft 365 Admin Center](https://admin.microsoft.com).
2. Wählen Sie die Registerkarte **Benutzer** aus, und erstellen Sie ein neues Benutzerkonto mit einem Namen wie *user\@fourthcoffeexyz.onmicrosoft.com*, der keinen benutzerdefinierten Domänennamen verwendet. 
3. Stellen Sie sicher, dass das neue Benutzerkonto globale Administratorberechtigungen für den Azure AD-Mandanten hat.
4. Öffnen Sie die Registerkarte **Domänen** im Microsoft 365 Admin Center, wählen Sie den Domänennamen aus, und klicken Sie auf **Entfernen**. 
  
   ![Entfernen des Domänennamens aus Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Wenn einige Ihrer Benutzer oder Gruppen in Office 365 weiterhin auf den entfernten Domänennamen verweisen, müssen sie in die Domäne .onmicrosoft.com umbenannt werden. Wenn Sie die Löschung des Domänennamens erzwingen, werden alle Benutzer automatisch umbenannt, in diesem Beispiel in *user\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Melden Sie sich beim [Azure AD Admin Center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit dem Konto an, das globale Administratorberechtigungen für den Azure AD-Mandanten hat.
  
7. Klicken Sie auf **Benutzerdefinierte Domänennamen**, und fügen Sie den Domänennamen hinzu. Sie müssen die DNS-TXT-Einträge eingeben, um die Inhaberschaft des Domänennamens zu bestätigen. 
  
   ![Überprüfte Domäne, wie zu Azure AD hinzugefügt](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alle Power BI- oder Azure Rights Management-Benutzer, die im Office 365-Mandanten zugewiesene Lizenzen haben, müssen ihre Dashboards speichern, wenn der Domänenname entfernt wird. Sie müssen sich mit einem Benutzernamen wie *user\@fourthcoffeexyz.onmicrosoft.com* und nicht mit *user\@fourthcoffee.xyz* anmelden.

## <a name="external-admin-takeover"></a>Externe Administratorübernahme

Wenn Sie bereits einen Mandanten mit Azure-Diensten oder Office 365 verwalten, können Sie keinen benutzerdefinierten Domänennamen hinzufügen, wenn er bereits in einem anderen Azure AD-Mandanten bestätigt wurde. Sie haben jedoch die Möglichkeit, aus Ihrem verwalteten Mandanten in Azure AD einen nicht verwalteten Mandanten im Zuge einer externen Administratorübernahme zu übernehmen. Die allgemeine Vorgehensweise wird im Artikel [Schnellstart: Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md) erklärt.

Wenn Sie die Inhaberschaft des Domänennamens bestätigt haben, entfernt Azure AD den Domänennamen aus dem nicht verwalteten Mandanten und verschiebt ihn in Ihren bestehenden Mandanten. Eine externe Administratorübernahme eines nicht verwalteten Verzeichnisses erfordert denselben DNS-TXT-Bestätigungsvorgang wie eine interne Administratorübernahme. Der Unterschied besteht darin, dass Folgendes zusammen mit dem Domänennamen verschoben wird:

- Benutzer
- Abonnements
- Lizenzzuweisungen

### <a name="support-for-external-admin-takeover"></a>Unterstützung für eine externe Administratorübernahme
Die externe Administratorübernahme wird von folgenden Onlinediensten unterstützt:

- Power BI
- Azure Rights Management
- Exchange Online

Die unterstützten Tarife umfassen:

- Power BI Free
- Power BI Pro
- PowerApps Free
- PowerFlow Free
- RMS for Individuals
- Microsoft Stream
- die kostenlose Testversion von Dynamics 365

Die externe Administratorübernahme wird für keinen Dienst mit Tarifen unterstützt, die SharePoint, OneDrive oder Skype For Business enthalten, z. B. über ein kostenloses Abonnement für Office oder das Office Basic SKU. Optional können Sie die [**ForceTakeover**-Option](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) verwenden, um den Domänennamen aus dem nicht verwalteten Mandanten zu entfernen und diesen Namen für den gewünschten Mandanten zu überprüfen. Diese ForceTakeover-Option bewirkt nicht, dass Benutzer verschoben werden oder Zugriff auf das Abonnement erhalten bleibt. Stattdessen bewirkt diese Option nur, dass der Domänenname verschoben wird. 

#### <a name="more-information-about-rms-for-individuals"></a>Weitere Informationen zu RMS for Individuals

Für [RMS for Individuals](/azure/information-protection/rms-for-individuals) werden, wenn sich der nicht verwaltete Mandant in derselben Region befindet wie der Mandant, den Sie besitzen, der automatisch erstellte [Mandantenschlüssel für Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) und [Standardschutzvorlagen](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) zusätzlich mit dem Domänennamen verschoben. 

Die Schlüssel und die Vorlagen werden nicht verschoben, wenn sich der nicht verwaltete Mandant in einer anderen Region befindet. Angenommen, der nicht verwaltete Mandant befindet sich in Europa, und der Mandant, den Sie besitzen, befindet sich in Nordamerika. 

Obwohl RMS for Individuals so konzipiert ist, dass es die Azure AD-Authentifizierung unterstützt, um geschützte Inhalte zu öffnen, hindert es Benutzer nicht daran, ebenfalls Inhalte zu schützen. Haben Benutzer Inhalte mit dem RMS for Individuals-Abonnement geschützt, und wurden die Schlüssel und die Vorlagen nicht verschoben, kann nach der Domänenübernahme nicht auf diese Inhalte zugegriffen werden.

#### <a name="more-information-about-power-bi"></a>Weitere Informationen zu Power BI

Beim Ausführen einer externen Übernahme werden Power BI-Inhalte, die vor der Übernahme erstellt wurden, in einem [archivierten Arbeitsbereich in Power BI](/power-bi/service-admin-power-bi-archived-workspace) platziert. Sie müssen alle Inhalte manuell migrieren, die im neuen Mandanten verwendet werden sollen.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD-PowerShell-Cmdlets für die ForceTakeover-Option
Diese Cmdlets werden im [PowerShell-Beispiel](#powershell-example) verwendet.


Cmdlet | Verwendung 
------- | -------
`connect-msolservice` | Wenn Sie dazu aufgefordert werden, melden Sie sich bei Ihrem verwalteten Mandanten an.
`get-msoldomain` | Zeigt die dem aktuellen Mandanten zugeordneten Domänennamen an.
`new-msoldomain –name <domainname>` | Fügt dem Mandanten den Domänennamen als „Unverified“ (Nicht bestätigt) hinzu (DNS-Bestätigung wurde noch nicht ausgeführt).
`get-msoldomain` | Der Domänenname befindet sich nun in der Liste der Ihrem verwalteten Mandanten zugeordneten Domänennamen, wird jedoch als **Unverified** aufgeführt.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Stellt Informationen bereit, die in den neuen DNS-TXT-Eintrag für die Domäne eingegeben werden müssen (MS=xxxxx). Die Bestätigung erfolgt möglicherweise nicht sofort, da es einige Zeit braucht, bis der TXT-Eintrag übernommen wird. Warten Sie also einfach einige Minuten, bevor Sie die Option **-ForceTakeover** in Betracht ziehen. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Wenn Ihr Domänenname weiterhin unbestätigt bleibt, können Sie die Option **-ForceTakeover** anwenden. Damit wird bestätigt, dass der TXT-Eintrag erstellt wurde, und die Übernahme wird angestoßen.<li>Die Option **-ForceTakeover** darf dem Cmdlet bei einer externen Administratorübernahme nur hinzugefügt werden, wenn z.B. der nicht verwaltete Mandant Office 365-Dienste ausführt, die die Übernahme blockieren.
`get-msoldomain` | In der Liste der Domänen wird der Domänenname nun als **Verified** (Bestätigt) angezeigt.

### <a name="powershell-example"></a>PowerShell-Beispiel

1. Verwenden Sie für die Verbindung mit Azure AD diesen Anmeldeinformationen wir für die Verbindung mit der Self-Service-Lösung:
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Rufen Sie eine Liste von Domänen ab:
  
   ```powershell
    Get-MsolDomain
   ```
3. Führen Sie das Cmdlet „Get-MsolDomainVerificationDns“ aus, um eine Abfrage zu erstellen:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopieren Sie den Wert (die Abfrage), der von diesem Befehl zurückgegeben wird. Beispiel:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Erstellen Sie in Ihrem öffentlichen DNS-Namespace einen "DnsTxt"-Eintrag, der den Wert enthält, den Sie im vorherigen Schritt kopiert haben. Der Name dieses Eintrags ist der Name der übergeordneten Domäne. Wenn Sie also diesen Ressourceneintrag mithilfe der DNS-Rolle von Windows Server erstellen, sollten Sie den Eintragsnamen leer lassen und bloß den Wert in das Textfeld kopieren.
6. Führen Sie das Cmdlet "Confirm-MsolDomain" aus, um die Abfrage zu überprüfen:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Beispiel:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Bei einer erfolgreichen Abfrage kehren Sie ohne Fehler zur Eingabeaufforderung zurück.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](../fundamentals/add-custom-domain.md)
* [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-Cmdlet-Referenz](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
