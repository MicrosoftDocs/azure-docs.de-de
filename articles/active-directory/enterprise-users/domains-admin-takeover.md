---
title: Administratorübernahme eines nicht verwalteten Verzeichnisses – Azure AD | Microsoft-Dokumentation
description: Vorgehensweise bei der Übernahme eines DNS-Domänennamens in einer nicht verwalteten Azure AD-Organisation (Schattenmandant)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af74967e2de47afeb357e2ac31b1a0ee849ef36
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547099"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Übernehmen eines nicht verwalteten Verzeichnisses als Administrator in Azure Active Directory

In diesem Artikel wird beschrieben, wie ein DNS-Domänenname in einem nicht verwalteten Verzeichnis in Azure Active Directory (Azure AD) übernommen wird. Wenn sich ein Self-Service-Benutzer für einen Clouddienst registriert, der Azure AD verwendet, wird er auf der Grundlage seiner E-Mail-Domäne einem nicht verwalteten Azure AD-Verzeichnis hinzugefügt. Weitere Informationen zur Self-Service- oder „viralen“ Registrierung für einen Dienst finden Sie im Artikel zu der Frage, [was die Self-Service-Registrierung für Azure Active Directory ist](directory-self-service-signup.md).

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Entscheiden, wie ein nicht verwaltetes Verzeichnis übernommen werden soll
Während der Administratorübernahme können Sie die Inhaberschaft wie in [Schnellstart: Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md) beschrieben nachweisen. In den nächsten Abschnitten wird die Administratorerfahrung im Detail erläutert. Hier zunächst eine Zusammenfassung:

* Bei einer [„internen“ Administratorübernahme](#internal-admin-takeover) eines nicht verwalteten Azure-Verzeichnisses werden Sie als globaler Administrator des nicht verwalteten Verzeichnisses hinzugefügt. Es werden keine Benutzer, Domänen oder Tarife in ein anderes Verzeichnis migriert, das Sie verwalten.

* Bei einer [„externen“ Administratorübernahme](#external-admin-takeover) eines nicht verwalteten Azure-Verzeichnisses fügen Sie Ihrem verwalteten Azure-Verzeichnis den DNS-Domänennamen des nicht verwalteten Verzeichnisses hinzu. Wenn Sie den Domänennamen hinzufügen, wird eine Zuordnung von Nutzern zu Ressourcen in Ihrem verwalteten Azure-Verzeichnis erstellt, damit Benutzer weiterhin ohne Unterbrechung auf Dienste zugreifen können. 

## <a name="internal-admin-takeover"></a>Interne Administratorübernahme

Einige Produkte, die SharePoint und OneDrive enthalten, z. B. Microsoft 365, unterstützen keine externe Übernahme. Wenn dieses Szenario auf Sie zutrifft oder Sie ein Administrator sind und eine nicht verwaltete Azure AD-Organisation (Schattenorganisation) übernehmen möchten, die von Benutzern erstellt wurde, die die Self-Service-Registrierung verwendet haben, können Sie dies mit einer internen Administratorübernahme tun.

1. Erstellen Sie einen Benutzerkontext in der nicht verwalteten Organisation über eine Registrierung bei Power BI. Genau dies tun Sie in diesem Beispiel.

2. Öffnen Sie die [Power BI-Website](https://powerbi.com), und klicken Sie auf **Kostenloser Einstieg**. Geben Sie ein Benutzerkonto ein, das den Domänennamen für die Organisation verwendet, z.B. `admin@fourthcoffee.xyz`. Nachdem Sie den Prüfcode eingegeben haben, suchen Sie in Ihren E-Mails nach dem Bestätigungscode.

3. Wählen Sie in der Bestätigungs-E-Mail von Power BI **Yes, that's me** (Ja, das bin ich) aus.

4. Melden Sie sich mit dem Power BI-Benutzerkonto im [Microsoft 365 Admin Center](https://portal.office.com/admintakeover) an. Sie erhalten eine Nachricht mit der Aufforderung, der Administrator des Domänennamens zu werden (**Become the Admin**), der bereits in der nicht verwalteten Organisation bestätigt wurde. Wählen Sie **Yes, I want to be the admin** (Ja, ich möchte der Administrator werden) aus.
  
   ![Erster Screenshot für „Become the Admin“](./media/domains-admin-takeover/become-admin-first.png)
  
5. Fügen Sie den TXT-Eintrag hinzu, um nachzuweisen, dass Sie den Domänennamen **fourthcoffee.xyz** bei Ihrer Domänennamen-Registrierungsstelle besitzen. In diesem Beispiel ist dies „GoDaddy.com“.
  
   ![Hinzufügen eines TXT-Eintrags für den Domänennamen](./media/domains-admin-takeover/become-admin-txt-record.png)

Wenn die DNS-TXT-Einträge bei Ihrer Domänennamen-Registrierungsstelle bestätigt wurden, können Sie die Azure AD-Organisation verwalten.

Wenn Sie die vorherigen Schritte abgeschlossen haben, sind Sie jetzt der globale Administrator der Fourth Coffee-Organisation in Microsoft 365. Um den Domänennamen in Ihre anderen Azure-Dienste zu integrieren, entfernen Sie ihn aus Microsoft 365, und fügen Sie ihn einer anderen verwalteten Organisation in Azure hinzu.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Hinzufügen des Domänennamens zu einer verwalteten Organisation in Azure AD

1. Öffnen Sie das [Microsoft 365 Admin Center](https://admin.microsoft.com).
2. Wählen Sie die Registerkarte **Benutzer** aus, und erstellen Sie ein neues Benutzerkonto mit einem Namen wie *user\@fourthcoffeexyz.onmicrosoft.com*, der keinen benutzerdefinierten Domänennamen verwendet. 
3. Stellen Sie sicher, dass das neue Benutzerkonto globale Administratorberechtigungen für die Azure AD-Organisation hat.
4. Öffnen Sie die Registerkarte **Domänen** im Microsoft 365 Admin Center, wählen Sie den Domänennamen aus, und klicken Sie auf **Entfernen**. 
  
   ![Entfernen des Domänennamens aus Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Wenn einige Ihrer Benutzer oder Gruppen in Microsoft 365 weiterhin auf den entfernten Domänennamen verweisen, müssen sie in die Domäne .onmicrosoft.com umbenannt werden. Wenn Sie die Löschung des Domänennamens erzwingen, werden alle Benutzer automatisch umbenannt, in diesem Beispiel in *user\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Melden Sie sich beim [Azure AD Admin Center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit dem Konto an, das globale Administratorberechtigungen für die Azure AD-Organisation hat.
  
7. Klicken Sie auf **Benutzerdefinierte Domänennamen**, und fügen Sie den Domänennamen hinzu. Sie müssen die DNS-TXT-Einträge eingeben, um die Inhaberschaft des Domänennamens zu bestätigen. 
  
   ![Überprüfte Domäne, wie zu Azure AD hinzugefügt](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alle Power BI- oder Azure Rights Management-Benutzer, die in der Microsoft 365-Organisation zugewiesene Lizenzen haben, müssen ihre Dashboards speichern, wenn der Domänenname entfernt wird. Sie müssen sich mit einem Benutzernamen wie *user\@fourthcoffeexyz.onmicrosoft.com* und nicht mit *user\@fourthcoffee.xyz* anmelden.

## <a name="external-admin-takeover"></a>Externe Administratorübernahme

Wenn Sie bereits eine Organisation mit Azure-Diensten oder Microsoft 365 verwalten, können Sie keinen benutzerdefinierten Domänennamen hinzufügen, wenn er bereits in einer anderen Azure AD-Organisation überprüft wurde. Sie haben jedoch die Möglichkeit, aus Ihrer verwalteten Organisation in Azure AD eine nicht verwaltete Organisation im Zuge einer externen Administratorübernahme zu übernehmen. Die allgemeine Vorgehensweise wird im Artikel [Schnellstart: Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md) erklärt.

Wenn Sie die Inhaberschaft des Domänennamens bestätigt haben, entfernt Azure AD den Domänennamen aus der nicht verwalteten Organisation und verschiebt ihn in Ihre bestehende Organisation. Eine externe Administratorübernahme eines nicht verwalteten Verzeichnisses erfordert denselben DNS-TXT-Bestätigungsvorgang wie eine interne Administratorübernahme. Der Unterschied besteht darin, dass Folgendes zusammen mit dem Domänennamen verschoben wird:

- Benutzer
- Abonnements
- Lizenzzuweisungen

### <a name="support-for-external-admin-takeover"></a>Unterstützung für eine externe Administratorübernahme
Die externe Administratorübernahme wird von folgenden Onlinediensten unterstützt:

- Azure Rights Management
- Exchange Online

Die unterstützten Tarife umfassen:

- PowerApps Free
- PowerFlow Free
- RMS for Individuals
- Microsoft Stream
- die kostenlose Testversion von Dynamics 365

Die externe Administratorübernahme wird für keinen Dienst mit Tarifen unterstützt, die SharePoint, OneDrive oder Skype For Business enthalten, z. B. über ein kostenloses Abonnement für Office. 

Optional können Sie die Option [**ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) verwenden, um den Domänennamen aus der nicht verwalteten Organisation zu entfernen und diesen Namen für die gewünschte Organisation zu überprüfen. 

#### <a name="more-information-about-rms-for-individuals"></a>Weitere Informationen zu RMS for Individuals

Bei [RMS for Individuals](/azure/information-protection/rms-for-individuals) werden der automatisch erstellte [Organisationsschlüssel für Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) und [Standardschutzvorlagen](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) zusätzlich mit dem Domänennamen verschoben, wenn sich die nicht verwaltete Organisation in derselben Region befindet wie die Organisation, die Sie besitzen.

Der Schlüssel und die Vorlagen werden nicht verschoben, wenn sich die nicht verwaltete Organisation in einer anderen Region befindet. Angenommen, die nicht verwaltete Organisation befindet sich in Europa, und die Organisation, die Sie besitzen, befindet sich in Nordamerika.

Obwohl RMS for Individuals so konzipiert ist, dass es die Azure AD-Authentifizierung unterstützt, um geschützte Inhalte zu öffnen, hindert es Benutzer nicht daran, ebenfalls Inhalte zu schützen. Haben Benutzer Inhalte mit dem RMS for Individuals-Abonnement geschützt, und wurden die Schlüssel und die Vorlagen nicht verschoben, kann nach der Domänenübernahme nicht auf diese Inhalte zugegriffen werden.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD-PowerShell-Cmdlets für die ForceTakeover-Option
Diese Cmdlets werden im [PowerShell-Beispiel](#powershell-example) verwendet.

Cmdlet | Verwendung
------- | -------
`connect-msolservice` | Wenn Sie dazu aufgefordert werden, melden Sie sich bei Ihrer verwalteten Organisation an.
`get-msoldomain` | Zeigt die der aktuellen Organisation zugeordneten Domänennamen an.
`new-msoldomain –name <domainname>` | Fügt der Organisation den Domänennamen als „Nicht überprüft “ hinzu (DNS-Überprüfung wurde noch nicht ausgeführt).
`get-msoldomain` | Der Domänenname befindet sich nun in der Liste der Ihrer verwalteten Organisation zugeordneten Domänennamen, wird jedoch als **Nicht überprüft** aufgeführt.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Stellt Informationen bereit, die in den neuen DNS-TXT-Eintrag für die Domäne eingegeben werden müssen (MS=xxxxx). Die Bestätigung erfolgt möglicherweise nicht sofort, da es einige Zeit braucht, bis der TXT-Eintrag übernommen wird. Warten Sie also einfach einige Minuten, bevor Sie die Option **-ForceTakeover** in Betracht ziehen. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Wenn Ihr Domänenname weiterhin unbestätigt bleibt, können Sie die Option **-ForceTakeover** anwenden. Damit wird bestätigt, dass der TXT-Eintrag erstellt wurde, und die Übernahme wird angestoßen.<li>Die Option **-ForceTakeover** sollte dem Cmdlet nur beim Erzwingen einer externen Administratorübernahme hinzugefügt werden, z. B. wenn die nicht verwaltete Organisation Microsoft 365-Dienste ausführt, die die Übernahme blockieren.
`get-msoldomain` | In der Liste der Domänen wird der Domänenname nun als **Verified** (Bestätigt) angezeigt.

> [!NOTE]
> Die nicht verwaltete Azure AD-Organisation wird 10 Tage nach dem Ausführen der externen ForceTakeover-Option gelöscht.

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
   ```
    Beispiel:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopieren Sie den Wert (die Abfrage), der von diesem Befehl zurückgegeben wird. Beispiel:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Erstellen Sie in Ihrem öffentlichen DNS-Namespace einen "DnsTxt"-Eintrag, der den Wert enthält, den Sie im vorherigen Schritt kopiert haben. Der Name dieses Eintrags ist der Name der übergeordneten Domäne. Wenn Sie also diesen Ressourceneintrag mithilfe der DNS-Rolle von Windows Server erstellen, sollten Sie den Eintragsnamen leer lassen und bloß den Wert in das Textfeld kopieren.
6. Führen Sie das Cmdlet "Confirm-MsolDomain" aus, um die Abfrage zu überprüfen:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Beispiel:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Bei einer erfolgreichen Abfrage kehren Sie ohne Fehler zur Eingabeaufforderung zurück.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](../fundamentals/add-custom-domain.md)
* [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure-Cmdlet-Referenz](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
