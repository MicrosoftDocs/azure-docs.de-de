---
title: Hinzufügen und Überprüfen von benutzerdefinierten Domänennamen – Azure Active Directory | Microsoft-Dokumentation
description: Verwaltungskonzepte und Vorgehensweisen für die Verwaltung eines Domänennamens in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/20/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b8867a137e38e7400da877f4c239e517cbebe05
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859979"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Verwalten von benutzerdefinierten Domänennamen in Azure Active Directory

Ein Domänenname ist ein wichtiger Bestandteil des Bezeichners für viele Azure Active Directory-Ressourcen (Azure AD): Er ist Teil eines Benutzernamens oder einer E-Mail-Adresse für einen Benutzer, Teil der Adresse für eine Gruppe und manchmal Teil des App-ID-URI für eine Anwendung. Eine Ressource in Azure AD kann einen Domänennamen umfassen, der zu der Organisation gehört, die die Ressource enthält. Nur ein globaler Administrator kann Domänen in Azure AD verwalten.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>Festlegen des primären Domänennamens für Ihre Azure AD-Organisation

Wenn Ihre Organisation erstellt wird, ist der anfängliche Domänenname (z. B. contoso.onmicrosoft.com) auch der primäre Domänenname. Die primäre Domäne ist der standardmäßige Domänenname für einen neuen Benutzer, wenn Sie einen neuen Benutzer erstellen. Durch das Festlegen eines primären Domänennamens wird die Erstellung neuer Benutzer durch einen Administrator im Portal optimiert. So ändern Sie den primären Domänennamen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das als globaler Administrator für die Organisation konfiguriert ist.
2. Wählen Sie **Azure Active Directory** aus.
3. Wählen Sie **Benutzerdefinierte Domänennamen**.
  
   ![Öffnen der Benutzerverwaltungsseite](./media/domains-manage/add-custom-domain.png)
4. Wählen Sie den Namen der Domäne aus, die als primäre Domäne festgelegt werden soll.
5. Wählen Sie den Befehl **Als primär festlegen**. Bestätigen Sie Ihre Auswahl, wenn Sie dazu aufgefordert werden.
  
   ![Festlegen eines Domänennamens als primär](./media/domains-manage/make-primary-domain.png)

Sie können den primären Domänennamen für Ihre Organisation in jede überprüfte benutzerdefinierte Domäne ändern, die keine Verbunddomäne ist. Durch Ändern der primären Domäne für Ihre Organisation werden die Benutzernamen vorhandener Benutzer nicht geändert.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Hinzufügen benutzerdefinierter Domänennamen zur Azure AD-Organisation

Sie können bis zu 900 Namen für verwaltete Domänen hinzufügen. Wenn Sie alle Ihre Domänen für den Verbund mit der lokalen Active Directory-Instanz konfigurieren, können Sie in jeder Organisation bis zu 450 Domänennamen hinzufügen.

## <a name="add-subdomains-of-a-custom-domain"></a>Hinzufügen von Unterdomänen einer benutzerdefinierten Domäne

Wenn Sie Ihrer Organisation einen Domänennamen wie beispielsweise „europe.contoso.com“ hinzufügen möchten, sollten Sie zuerst die Stammdomäne, d. h. „contoso.com“, hinzufügen und überprüfen. Die Unterdomäne wird automatisch von Azure AD überprüft. Um festzustellen, ob die von Ihnen hinzugefügte Unterdomäne überprüft wurde, aktualisieren Sie die Domänenliste im Browser.

Wenn Sie einer Azure AD-Organisation bereits die Domäne contoso.com hinzugefügt haben, können Sie die Unterdomäne europe.contoso.com auch in einer anderen Azure AD-Organisation überprüfen. Wenn Sie die Unterdomäne hinzufügen, werden Sie aufgefordert, einen TXT-Eintrag im DNS-Hostinganbieter hinzuzufügen.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vorgehensweise beim Ändern der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen

Wenn Sie die DNS-Registrierungsstellen ändern, sind keine weiteren Konfigurationsaufgaben in Azure AD auszuführen. Sie können den Domänennamen in Azure AD ohne jede Unterbrechung weiterverwenden. Wenn Sie Ihren benutzerdefinierten Domänennamen mit Microsoft 365, Intune oder anderen Diensten verwenden, die benutzerdefinierte Domänennamen in Azure AD verwenden, lesen Sie die Dokumentation zu diesen Diensten.

## <a name="delete-a-custom-domain-name"></a>Löschen eines benutzerdefinierten Domänennamens

Sie können einen benutzerdefinierten Domänennamen aus Azure AD löschen, wenn dieser von Ihrem Unternehmen nicht mehr verwendet wird oder wenn Sie den Domänennamen für eine andere Azure AD-Instanz verwenden möchten.

Um einen benutzerdefinierten Domänennamen zu löschen, müssen Sie zunächst sicherstellen, dass dieser Name für keine Ressourcen in Ihrer Organisation verwendet wird. In folgenden Fällen können Sie einen Domänennamen nicht aus Ihrer Organisation löschen:

* Ein Benutzer verwendet einen Benutzernamen, eine E-Mail-Adresse oder eine Proxyadresse mit dem Domänennamen.
* Eine Gruppe verwendet eine E-Mail-Adresse oder Proxyadresse mit dem Domänennamen.
* Eine Anwendung in Ihrem Azure AD-Verzeichnis besitzt eine App-ID-URI mit dem Domänennamen.

Solche Ressourcen müssen Sie in Ihrer Azure AD-Organisation ändern oder löschen, bevor Sie den benutzerdefinierten Domänennamen löschen können.

### <a name="forcedelete-option"></a>ForceDelete-Option

Sie können das Löschen eines Domänennamens im [Azure AD Admin Center](https://aad.portal.azure.com) oder mithilfe der [Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true) per **ForceDelete** erzwingen. Diese Optionen verwenden einen asynchronen Vorgang und aktualisieren alle Verweise vom benutzerdefinierten Domänenamen wie „user@contoso.com“ auf den anfänglichen Standarddomänennamen wie „user@contoso.onmicrosoft.com“.

Um **ForceDelete** im Azure-Portal aufzurufen, müssen Sie sicherstellen, dass weniger als 1.000 Verweise auf den Domänennamen vorhanden sind und dass alle Verweise, bei denen Exchange der Bereitstellungsdienst ist, im [Exchange Admin Center](https://outlook.office365.com/ecp/) aktualisiert oder entfernt werden. Dazu gehören E-Mail-aktivierte Exchange-Sicherheitsgruppen und verteilte Listen. Weitere Informationen finden Sie unter [Entfernen von E-Mail-aktivierten Sicherheitsgruppen](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true). Der **ForceDelete**-Vorgang kann nicht erfolgreich ausgeführt werden, wenn eine der folgenden Aussagen zutrifft:

* Sie haben eine Domäne über Microsoft 365-Domänenabonnementdienste erworben.
* Sie sind Partner und führen die Verwaltung im Auftrag einer anderen Organisation des Kunden aus.

Folgende Aktionen werden im Rahmen des **ForceDelete**-Vorgangs ausgeführt:

* UPNs, E-Mail-Adressen und Proxyadressen von Benutzern mit Verweisen auf den benutzerdefinierten Domänennamen werden in den anfänglichen Standarddomänennamen umbenannt.
* E-Mail-Adressen von Gruppen mit Verweisen auf den benutzerdefinierten Domänennamen werden in den anfänglichen Standarddomänennamen umbenannt.
* Bezeichner-URIs von Anwendungen mit Verweisen auf den benutzerdefinierten Domänennamen werden in den anfänglichen Standarddomänennamen umbenannt.

In folgenden Fällen wird ein Fehler zurückgegeben:

* Die Anzahl von umzubenennenden Objekten ist größer als 1.000.
* Bei einer der umzubenennenden Anwendungen handelt es sich um eine mehrinstanzenfähige App.

### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Warum tritt beim Löschen einer Domäne eine Fehlermeldung auf, die besagt, dass ich über von Exchange verwaltete Gruppen mit diesem Domänennamen verfüge?** <br>
**A:** Heute werden bestimmte Gruppen wie E-Mail-aktivierte Sicherheitsgruppen und verteilte Listen von Exchange bereitgestellt und müssen im [Exchange Admin Center (EAC)](https://outlook.office365.com/ecp/) manuell gelöscht werden. Möglicherweise sind noch Proxyadressen vorhanden, die den benutzerdefinierten Domänennamen verwenden und manuell auf einen anderen Domänennamen aktualisiert werden müssen. 

**F: Ich bin als „admin\@contoso.com “ angemeldet, kann aber den Domänennamen „contoso.com“ nicht löschen?**<br>
**A:** Sie können im Namen Ihres Benutzerkontos nicht auf den benutzerdefinierten Domänennamen verweisen, den Sie löschen möchten. Stellen Sie sicher, dass das Konto des globalen Administrators den anfänglichen Domänennamen (.onmicrosoft.com) verwendet, z.B. „admin@contoso.onmicrosoft.com“. Verwenden Sie für die Anmeldung ein anderes globales Administratorkonto wie z.B. „admin@contoso.onmicrosoft.com“ oder einen anderen benutzerdefinierten Domänenamen wie „fabrikam.com“, bei dem das Konto „admin@fabrikam.com“ lautet.

**F: Ich habe auf die Schaltfläche „Domäne löschen“ geklickt. Jetzt wird der Status `In Progress` für den Löschvorgang angezeigt. Wie lange dauert es? Was passiert, wenn der Vorgang nicht erfolgreich ausgeführt wird?**<br>
**A:** Der Vorgang zum Löschen einer Domäne ist ein asynchroner Hintergrundtask, der alle Verweise auf den Domänennamen umbenennt. Dieser Vorgang sollte innerhalb von ein bis zwei Minuten abgeschlossen sein. Wenn eine Domäne nicht gelöscht werden kann, stellen Sie sicher, dass Folgendes nicht zutrifft:

* Apps sind im Domänennamen mit dem App-Bezeichner-URI konfiguriert.
* Es ist eine E-Mail-aktivierte Gruppe vorhanden, die auf den benutzerdefinierten Domänennamen verweist.
* Es sind mehr als 1.000 Verweise auf den Domänennamen vorhanden.

Wenn eine dieser Bedingungen zutrifft, bereinigen Sie die Verweise manuell, und versuchen Sie erneut, die Domäne zu löschen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Verwenden von PowerShell oder der Microsoft Graph-API zum Verwalten von Domänennamen

Die meisten Verwaltungsaufgaben für Domänennamen in Azure Active Directory können auch über Microsoft PowerShell oder programmgesteuert mit der Microsoft Graph-API durchgeführt werden.

* [Verwenden von PowerShell zum Verwalten von Domänennamen in Azure AD](/powershell/module/azuread/#domains&preserve-view=true)
* [Domänenressourcentyp](/graph/api/resources/domain?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen benutzerdefinierter Domänennamen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Entfernen von E-Mail-aktivierten Exchange-Sicherheitsgruppen in Exchange Admin Center für einen benutzerdefinierten Domänennamen in Azure AD](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [ForceDelete eines benutzerdefinierten Domänennamens mit der Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
