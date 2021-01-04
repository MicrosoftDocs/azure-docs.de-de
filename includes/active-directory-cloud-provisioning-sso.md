---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558188"
---
## <a name="steps-to-enable-single-sign-on"></a>Schritte zum Aktivieren des einmaligen Anmeldens
Die Cloudbereitstellung funktioniert mit dem einmaligen Anmelden.  Derzeit gibt es keine Option zum Aktivieren des einmaligen Anmeldens (SSO) bei der Installation des Agents. Sie können SSO jedoch mit den folgenden Schritten aktivieren und verwenden. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Schritt 1: Herunterladen und Extrahieren von Azure AD Connect-Dateien
1.  Laden Sie zuerst die aktuelle Version von [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) herunter.
2.  Öffnen Sie mit Administratorrechten eine Eingabeaufforderung, und navigieren Sie zu der MSI, die Sie soeben heruntergeladen haben.
3.  Führen Sie Folgendes aus: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Ändern Sie „filePath“ und „extractfolder“ entsprechend Ihrem Dateipfad und dem Namen Ihres Extraktionsordners.  Der Inhalt sollte sich nun im Extraktionsordner befinden.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Schritt 2: Importieren Sie das PowerShell-Modul „Nahtlose SSO“

1. Laden Sie [Azure AD PowerShell](/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importieren Sie das PowerShell-Modul für nahtloses SSO mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Schritt 3: Rufen Sie die Liste der Active Directory-Gesamtstrukturen ab, für die nahtloses einmaliges Anmelden aktiviert wurde.

1. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Geben Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten an, wenn Sie dazu aufgefordert werden.
2. Rufen Sie `Get-AzureADSSOStatus` auf. Dadurch erhalten Sie die Liste der Active Directory-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Schritt 4: Aktivieren Sie nahtloses SSO für jede Active Directory-Gesamtstruktur.

1. Rufen Sie `Enable-AzureADSSOForest` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene Active Directory-Gesamtstruktur ein.

   > [!NOTE]
   >Der Benutzername der Anmeldeinformationen des Domänenadministrators muss im Namensformat für das SAM-Konto („contoso\johndoe“ oder „contoso.com\johndoe“) eingegeben werden. Wir verwenden den Domänenteil des Benutzernamens, um den Domänencontroller des Domänenadministrators mithilfe von DNS zu suchen.

   >[!NOTE]
   >Das verwendete Domänenadministratorkonto darf kein Mitglied der Gruppe „Geschützte Benutzer“ sein. Andernfalls schlägt der Vorgang fehl.

2. Wiederholen Sie die vorhergehenden Schritte für Active Directory-Gesamtstruktur, in der Sie die Funktion einrichten möchten.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Schritt 5: Aktivieren Sie das Feature für Ihren Mandanten.

Zum Aktivieren des Features für Ihren Mandanten rufen Sie `Enable-AzureADSSO -Enable $true` auf.