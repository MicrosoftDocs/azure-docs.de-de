---
title: Tutorial für das Masseneinladen von Benutzern für die B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mit PowerShell und einer CSV-Datei Masseneinladungen an externe Gastbenutzer für die Azure AD B2B-Zusammenarbeit senden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e047f11cc243ab1a36a8c61dd1b229d9e115115
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892484"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Masseneinladen von Benutzern für die Azure AD B2B-Zusammenarbeit mithilfe von PowerShell

Wenn Sie Azure Active Directory (Azure AD) für die B2B-Zusammenarbeit mit externen Partnern verwenden, können Sie mehrere Gastbenutzer gleichzeitig in Ihre Organisation einladen. In diesem Tutorial erfahren Sie, wie Sie mit PowerShell Masseneinladungen an externe Benutzer senden. Das Tutorial umfasst vor allem die folgenden Aufgaben:

> [!div class="checklist"]
> * Vorbereiten einer durch Trennzeichen getrennten CSV-Datei mit den Benutzerinformationen
> * Ausführen eines PowerShell-Skripts zum Senden von Einladungen
> * Überprüfen, ob Benutzer dem Verzeichnis hinzugefügt wurden

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="install-the-latest-azureadpreview-module"></a>Installieren des aktuellen AzureADPreview-Moduls

Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls (AzureADPreview). 

Überprüfen Sie zunächst, welche Module Sie installiert haben. Öffnen Sie dafür Windows PowerShell als Benutzer mit erhöhten Rechten („Als Administrator ausführen“), und führen Sie den folgenden Befehl aus:

```powershell
Get-Module -ListAvailable AzureAD*
```

Führen Sie je nach Ausgabe einen der folgenden Schritte aus:

- Wenn keine Ergebnisse zurückgegeben werden, führen Sie den folgenden Befehl aus, um das AzureADPreview-Modul zu installieren:
  
   ```powershell
   Install-Module AzureADPreview
   ```

- Wenn nur das AzureADPreview-Modul in den Ergebnissen angezeigt wird, führen Sie die folgenden Befehle aus, um das AzureADPreview-Modul zu installieren: 

   ```powershell
   Uninstall-Module AzureAD
   Install-Module AzureADPreview
   ```

- Wenn nur das AzureADPreview-Modul in den Ergebnissen angezeigt wird, eine Meldung Sie jedoch darauf hinweist, dass eine neuere Version verfügbar ist, führen Sie die folgenden Befehle aus, um das Modul zu aktualisieren:

   ```powershell
   Uninstall-Module AzureADPreview
   Install-Module AzureADPreview
   ```

Möglicherweise erscheint ein Hinweis, dass Sie das Modul aus einem nicht vertrauenswürdigen Repository installieren. Das passiert, wenn Sie das PSGallery-Repository nicht zuvor als vertrauenswürdiges Repository festgelegt haben. Drücken Sie **Y** , um das Modul zu installieren.

### <a name="get-test-email-accounts"></a>Einrichten von Test-E-Mail-Konten

Sie benötigen mindestens zwei Test-E-Mail-Konten, an die Sie die Einladungen senden können. Die Konten müssen sich außerhalb Ihrer Organisation befinden. Sie können jede Art von Konto verwenden, einschließlich Konten bei sozialen Netzwerken wie „gmail.com“- oder „outlook.com“-Adressen.

## <a name="prepare-the-csv-file"></a>Vorbereiten der CSV-Datei

Erstellen Sie in Microsoft Excel eine CSV-Datei mit der Liste der Benutzernamen und E-Mail-Adressen der einzuladenden Personen. Achten Sie darauf, dass Sie die Spaltenüberschriften **Name** und **InvitedUserEmailAddress** angeben.

Erstellen Sie beispielsweise ein Arbeitsblatt im folgenden Format:

![PowerShell-Ausgabe: ausstehende Annahme durch Benutzer](media/tutorial-bulk-invite/AddUsersExcel.png)

Speichern Sie die Datei unter **C:\BulkInvite\Invitations.csv**. 

Wenn Sie Excel nicht verwenden, können Sie eine CSV-Datei in jedem beliebigen Text-Editor erstellen, z.B. Notepad. Trennen Sie Werte durch Kommas und Zeilen durch eine neue Zeile. 

## <a name="sign-in-to-your-tenant"></a>Anmelden im Mandanten

Führen Sie den folgenden Befehl aus, um eine Verbindung mit der Mandantendomäne herzustellen:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Beispiel: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein.

## <a name="send-bulk-invitations"></a>Senden von Masseneinladungen

Führen Sie das folgende PowerShell-Skript aus (mit **c:\bulkinvite\invitations.csv** als CSV-Dateipfad), um die Einladungen zu senden:

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

Dieses Skript sendet Einladungen an die E-Mail-Adressen in der Datei „Invitations.csv“. Die Ausgabe sollte für jeden Benutzer ungefähr so aussehen:

![Screenshot: PowerShell-Ausgabe, die die ausstehende Annahme durch Benutzer enthält](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Überprüfen, ob Benutzer im Verzeichnis vorhanden sind

Um zu überprüfen, ob die eingeladenen Benutzer Azure AD hinzugefügt wurden, führen Sie den folgenden Befehl aus:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Die von Ihnen eingeladenen Benutzer sollten mit einem Benutzerprinzipalnamen (UPN) im Format *E-Mail-Adresse* #EXT#\@*Domäne* angezeigt werden. Beispiel: *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com* , wobei „contoso.onmicrosoft.com“ der Organisation entspricht, für die Sie die Einladungen gesendet haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Testbenutzerkonten nicht mehr benötigen, können Sie sie aus dem Verzeichnis löschen. Führen Sie den folgenden Befehl aus, um ein Benutzerkonto zu löschen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Beispiel: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Masseneinladungen an Gastbenutzer außerhalb Ihrer Organisation gesendet. Als Nächstes erfahren Sie, wie Einladungen eingelöst werden.

> [!div class="nextstepaction"]
> [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
