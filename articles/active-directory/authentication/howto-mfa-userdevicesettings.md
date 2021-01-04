---
title: Verwalten von Authentifizierungsmethoden für Azure AD Multi-Factor Authentication – Azure Active Directory
description: Hier erfahren Sie, wie Sie Azure Active Directory-Benutzereinstellungen für Azure AD Multi-Factor Authentication konfigurieren können.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96f01d38fb41f64336d0a2b1d2aa33a96ca9f1a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742747"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Verwalten von Benutzerauthentifizierungsmethoden für Azure AD Multi-Factor Authentication

Kontaktinformationen der Benutzer in Azure AD fallen in zwei unterschiedliche Kategorien:  

- Kontaktinformationen für öffentliche Profile, die im Benutzerprofil verwaltet werden und für Mitglieder Ihrer Organisation sichtbar sind. Bei Benutzern, die aus dem lokalen Active Directory synchronisiert werden, werden diese Informationen in den lokalen Windows Server Active Directory-Domänendiensten verwaltet.
- Authentifizierungsmethoden, die immer privat sind und nur für die Authentifizierung verwendet werden, einschließlich Multi-Factor Authentication (MFA). Administratoren können diese Methoden auf dem Blatt zur Authentifizierungsmethode eines Benutzers verwalten, und Benutzer können Ihre Methoden auf der Seite „Sicherheitsinformationen“ von MyAccount verwalten.

Bei der Verwaltung von Azure AD Multi-Factor Authentication-Methoden für Ihre Benutzer können Authentifizierungsadministratoren folgende Aktionen ausführen: 

1. Hinzufügen von Authentifizierungsmethoden für einen bestimmten Benutzer, einschließlich der für MFA verwendeten Telefonnummern.
1. Setzt das Kennwort eines Benutzers zurück.
1. Anfordern einer erneuten Registrierung des Benutzers für MFA.
1. Widerrufen vorhandener MFA-Sitzungen.
1. Löschen der vorhandenen App-Kennwörter eines Benutzers  

## <a name="add-authentication-methods-for-a-user"></a>Hinzufügen von Authentifizierungsmethoden für einen Benutzer 

Sie können Authentifizierungsmethoden für einen Benutzer über das Azure-Portal oder Microsoft Graph hinzufügen.  

> [!NOTE]
> Aus Sicherheitsgründen sollten die Felder für die öffentlichen Kontaktinformationen eines Benutzers nicht zum Ausführen von MFA verwendet werden. Stattdessen sollten die Benutzer die Nummern für ihre Authentifizierungsmethoden eintragen, die für die MFA verwendet werden sollen.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Hinzufügen von Authentifizierungsmethoden über das Azure-Portal":::

So fügen Sie Authentifizierungsmethoden für einen Benutzer über das Azure-Portal hinzu  

1. Melden Sie sich beim **Azure-Portal** an. 
1. Gehen Sie zu **Azure Active Directory** > **Benutzer** > **Alle Benutzer**. 
1. Wählen Sie den Benutzer aus, für den Sie eine Authentifizierungsmethode hinzufügen möchten, und wählen Sie **Authentifizierungsmethoden** aus.  
1. Wählen Sie oben im Fenster **+ Authentifizierungsmethode hinzufügen** aus.
   1. Wählen Sie eine Methode aus (Telefonnummer oder E-Mail). „E-Mail“ kann für die Self-Service-Kennwortrücksetzung, aber nicht für die Authentifizierung verwendet werden. Wählen Sie beim Hinzufügen einer Telefonnummer einen Telefontyp aus, und geben Sie eine Telefonnummer in einem gültigen Format ein (z. B. +1 4255551234).
   1. Wählen Sie **Hinzufügen**.

> [!NOTE]
> In der Vorschauversion können Administratoren alle verfügbaren Authentifizierungsmethoden für Benutzer hinzufügen, während die ursprüngliche Version nur das Aktualisieren der Methoden für ein Telefon und ein alternatives Telefon zulässt.

### <a name="manage-methods-using-powershell"></a>Verwalten von Methoden mit PowerShell:  

Installieren Sie das Modul „Microsoft.Graph.Identity.Signins PowerShell“ mithilfe der folgenden Befehle. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Listet die Telefonauthentifizierungsmethoden für einen bestimmten Benutzer auf.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Erstellt eine Mobiltelefon-Authentifizierungsmethode für einen bestimmten Benutzer.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

Entfernt eine bestimmte Telefonmethode für einen Benutzer.

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Authentifizierungsmethoden können auch mithilfe von Microsoft Graph-APIs verwaltet werden. Weitere Informationen finden Sie im Dokument [Übersicht über die Azure AD Authentifizierungsmethoden-API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

## <a name="manage-user-authentication-options"></a>Verwalten von Authentifizierungsoptionen

Wenn Ihnen die Rolle *Authentifizierungsadministrator* zugewiesen wurde, können Sie festlegen, dass Benutzer ihr Kennwort zurücksetzen, sich für die mehrstufige Authentifizierung noch mal registrieren oder vorhandene MFA-Sitzungen von ihrem Benutzerobjekt widerrufen müssen. Um Benutzereinstellungen zu verwalten, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der linken Seite **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie den Benutzer aus, für den Sie eine Aktion ausführen möchten, und wählen Sie anschließend **Authentifizierungsmethoden** aus. Wählen Sie dann am oberen Rand des Fensters eine der folgenden Optionen für den Benutzer aus:
   - **Kennwort zurücksetzen**: Das Kennwort des Benutzers wird zurückgesetzt, und ein temporäres Kennwort wird zugewiesen, das bei der nächsten Anmeldung geändert werden muss.
   - **Erneute MFA-Registrierung erforderlich**: Sorgt dafür, dass der Benutzer bei der nächsten Anmeldung zur Einrichtung einer neuen MFA-Authentifizierungsmethode aufgefordert wird.
   
      > [!NOTE]
      > Die derzeit registrierten Authentifizierungsmethoden des Benutzers werden nicht gelöscht, wenn ein Administrator eine erneute Registrierung für MFA benötigt. Nachdem sich ein Benutzer erneut für die MFA registriert hat, empfiehlt es sich, seine Sicherheitsinformationen zu überprüfen und alle zuvor registrierten Authentifizierungsmethoden zu löschen, die nicht mehr verwendet werden können.
   
   - **MFA-Sitzungen widerrufen**: Löscht die gespeicherten MFA-Sitzungen des Benutzers. Wenn auf dem Gerät gemäß Richtlinie das nächste Mal eine MFA erforderlich ist, muss der Benutzer diese durchführen.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Verwalten von Authentifizierungsmethoden im Azure-Portal":::

## <a name="delete-users-existing-app-passwords"></a>Löschen vorhandener App-Kennwörter eines Benutzers

Bei Benutzern, die App-Kennwörter definiert haben, können Administratoren diese Kennwörter auch löschen, sodass keine Legacy-Authentifizierung in diesen Anwendungen mehr möglich ist. Diese Aktionen sind möglicherweise erforderlich, wenn Sie einen Benutzer unterstützen oder seine Authentifizierungsmethoden zurücksetzen müssen. Nicht-Browser-Apps, die diesen App-Kennwörtern zugeordnet waren, funktionieren erst wieder, wenn ein neues App-Kennwort erstellt wurde. 

Um die App-Kennwörter eines Benutzers zu löschen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der linken Seite **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie **Multi-Factor Authentication** aus. Scrollen Sie bei Bedarf nach rechts, um diese Menüoption anzuzeigen. Wählen Sie den unten gezeigten Beispielscreenshot aus, um das gesamte Fenster des Azure-Portals sowie die Menüposition anzuzeigen: [![Aktivieren von MFA (Multi-Factor Authentication) im Fenster „Benutzer“ in Azure AD](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Aktivieren Sie das Kontrollkästchen neben den Benutzern, die Sie verwalten möchten. Auf der rechten Seite wird eine Liste mit Optionen für schnelle Schritte angezeigt.
1. Wählen Sie **Benutzereinstellungen verwalten** aus, und aktivieren Sie dann das Kontrollkästchen **Alle vorhandenen App-Kennwörter löschen, die von den ausgewählten Benutzern erstellt wurden**, wie im folgenden Beispiel gezeigt: ![Alle vorhandenen App-Kennwörter löschen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Wählen Sie **Speichern** und dann **Schließen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel lernten Sie, wie Sie die Konfiguration individueller Benutzereinstellungen unterstützen. Informationen zum Konfigurieren der Azure AD Multi-Factor Authentication-Diensteinstellungen insgesamt finden Sie unter [Konfigurieren von Azure AD Multi-Factor Authentication-Einstellungen](howto-mfa-mfasettings.md).

Wenn Ihre Benutzer Hilfe benötigen, finden Sie weitere Informationen im [Benutzerhandbuch für Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
