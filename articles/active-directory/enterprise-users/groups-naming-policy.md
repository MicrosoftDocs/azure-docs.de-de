---
title: Erzwingen von Benennungsrichtlinien in Azure Active Directory | Microsoft-Dokumentation
description: Einrichten einer Benennungsrichtlinie für Microsoft 365-Gruppen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c1c30e4651136ada7264cad8ff4d3921c3aeeb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547915"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Durchsetzen einer Benennungsrichtlinie für Microsoft 365-Gruppen in Azure Active Directory

Wenn Sie einheitliche Benennungskonventionen für Microsoft 365-Gruppen erzwingen möchten, die von Ihren Benutzern erstellt oder bearbeitet werden, richten Sie eine Gruppenbenennungsrichtlinie für Ihre Organisationen in Azure Active Directory (Azure AD) ein. Beispielsweise können Sie mithilfe der Benennungsrichtlinie die Funktion einer Gruppe, die Mitgliedschaft, die geografische Region oder den Ersteller der Gruppe kommunizieren. Sie können mit der Benennungsrichtlinie auch die Kategorisierung von Gruppen im Adressbuch unterstützen. Es ist auch möglich, die Richtlinie zu verwenden, um bestimmte Wörter in Gruppennamen und Aliasen zu verhindern.

> [!IMPORTANT]
> Zur Verwendung der Azure AD-Benennungsrichtlinie für Microsoft 365-Gruppen müssen Sie für jeden einzelnen Benutzer, der Mitglied einer oder mehrerer Ihrer Microsoft 365-Gruppen ist, über eine Azure Active Directory Premium P1-Lizenz oder eine Azure AD Basic EDU-Lizenz verfügen. Sie müssen diese Lizenzen aber nicht unbedingt zuweisen.

Die Benennungsrichtlinie wird bei der Erstellung oder Bearbeitung von Gruppen aller Workloads (z.B. Outlook, Microsoft Teams, SharePoint, Exchange oder Planner) angewendet. Sie wird sowohl auf Gruppennamen als auch auf Gruppenaliase angewendet. Wenn Sie Ihre Benennungsrichtlinie in Azure AD einrichten und bereits über eine Benennungsrichtlinie für Exchange-Gruppen verfügen, wird die Azure AD-Benennungsrichtlinie für Ihr gesamtes Unternehmen erzwungen.

Nachdem die Benennungsrichtlinie für Gruppen konfiguriert wurde, wird sie auf neue Microsoft 365-Gruppen angewendet, die von Endbenutzern erstellt wurden. Eine Benennungsrichtlinie gilt nicht für bestimmte Verzeichnisrollen, z. B. „Globaler Administrator“ oder „Benutzeradministrator“. (Eine vollständige Liste der Rollen, die von der Benennungsrichtlinie für Gruppen ausgeschlossen sind, finden Sie weiter unten.) Die Richtlinie wird nicht direkt zum Zeitpunkt der Konfiguration auf vorhandene Microsoft 365-Gruppen angewendet. Nachdem der Gruppenbesitzer den Gruppennamen für diese Gruppen bearbeitet hat, wird die Benennungsrichtlinie erzwungen.

## <a name="naming-policy-features"></a>Features für Benennungsrichtlinien

Sie können Benennungsrichtlinien für Gruppen auf zwei unterschiedliche Arten erzwingen:

- **Präfix-Suffix-Benennungsrichtlinie:** Sie können Präfixe und Suffixe festlegen, die dann automatisch hinzugefügt werden, um eine Namenskonvention für Ihre Gruppen zu erzwingen (z.B. lauten im Gruppennamen „GRP\_JAPAN\_Meine Gruppe\_Engineering“ das Präfix „GRP\_JAPAN\_“ und das Suffix „\_Engineering“). 

- **Benutzerdefinierte blockierte Wörter:** Sie können eine Reihe von blockierten Wörtern festlegen, die in Ihrer Organisation bei Gruppen, die von Benutzern erstellt werden, nicht verwendet werden dürfen (z.B. „CEO, Gehalt, Personal“).

### <a name="prefix-suffix-naming-policy"></a>Präfix-Suffix-Benennungsrichtlinie

Die allgemeine Struktur der Namenskonvention lautet: „Präfix[Gruppenname]Suffix“. Sie können zwar mehrere Präfixe und Suffixe definieren, es darf jedoch nur eine Instanz von [Gruppenname] in der Einstellung geben. Die Präfixe und Suffixe können feste Zeichenfolgen oder Benutzerattribute wie z.B. \[Abteilung\] sein, die basierend auf dem Benutzer, der die Gruppe erstellt, ersetzt werden. Die zulässige Gesamtanzahl von Zeichen für die Präfix- und Suffixzeichenfolgen beträgt inklusive Gruppenname 53 Zeichen. 

Präfixe und Suffixe können alle Sonderzeichen enthalten, die bei Gruppennamen und Gruppenaliasen unterstützt werden. Alle Zeichen im Präfix oder Suffix, die beim Gruppenalias nicht unterstützt werden, werden zwar weiterhin auf den Gruppennamen angewendet, aber aus dem Gruppenalias entfernt. Aufgrund dieser Einschränkung können sich die Präfixe und Suffixe, die auf den Gruppennamen angewendet werden, von den auf den Gruppenalias angewendeten unterscheiden. 

#### <a name="fixed-strings"></a>Feste Zeichenfolgen

Sie können Zeichenfolgen verwenden, damit Gruppen in der globalen Adressliste und in den Navigationslinks auf der linken Seite der Gruppenworkloads einfacher überprüft und unterschieden werden können. Einige der häufig verwendeten Präfixe sind Schlüsselwörter wie „Grp\_Name“, „\#Name“, „\_Name“.

#### <a name="user-attributes"></a>Benutzerattribute

Anhand von Attributen können Sie und Ihre Benutzer einfacher erkennen, für welche Abteilung, welches Büro oder welche geografische Region die Gruppe erstellt wurde. Wenn Sie z.B. eine Benennungsrichtlinie mit `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` und `User’s department = Engineering` festlegen, würde ein erzwungener Gruppenname „GRP Meine Gruppe Engineering“ lauten. Unterstützte Azure AD-Attribute sind \[Department\], \[Company\], \[Office\], \[StateOrProvince\], \[CountryOrRegion \], \[Title\]. Nicht unterstützte Benutzerattribute werden wie feste Zeichenfolgen behandelt – z.B. „\[postalCode\]“. Erweiterungsattribute und benutzerdefinierte Attribute werden nicht unterstützt.

Es wird empfohlen, Attribute zu verwenden, die Werte für alle Benutzer in Ihrer Organisation enthalten, aber keine langen Werte aufweisen.

### <a name="custom-blocked-words"></a>Benutzerdefinierte blockierte Wörter

Eine Liste der blockierten Wörter ist eine durch Trennzeichen getrennte Liste von Ausdrücken, die in Gruppennamen und Aliasen nicht verwendet werden dürfen. Es wird keine Suche nach Teilzeichenfolgen durchgeführt. Eine genaue Übereinstimmung zwischen dem Gruppennamen und mindestens einem der benutzerdefinierten blockierten Wörter löst einen Fehler aus. Da keine Suche nach Teilzeichenfolgen durchgeführt wird, können Benutzer häufig auftretende Wörter wie „Klasse“ auch dann verwenden, wenn „lasse“ ein blockiertes Wort ist.

Regeln für die Liste blockierter Wörter:

- Bei blockierten Wörtern wird die Groß-/Kleinschreibung nicht beachtet.
- Wenn ein Benutzer ein blockiertes Wort als Teil eines Gruppennamens eingibt, wird diesem eine Fehlermeldung mit dem blockierten Wort angezeigt.
- Es gibt keine Zeichenbegrenzung für blockierte Wörter.
- Es gibt eine Obergrenze von 5.000 Ausdrücken, die in der Liste der blockierten Wörter konfiguriert werden können. 

### <a name="roles-and-permissions"></a>Rollen und Berechtigungen

Zum Konfigurieren der Benennungsrichtlinie ist eine der folgenden Rollen erforderlich:
- Globaler Administrator
- Gruppenadministrator


Ausgewählte Administratoren können von diesen Richtlinien für alle Gruppenworkloads und Endpunkte ausgenommen werden, sodass sie Gruppen erstellen können, die blockierte Wörter enthalten oder ihren eigenen Namenskonventionen entsprechen. Die folgende Liste enthält die Administratorrollen, die von der Benennungsrichtlinie für Gruppen ausgenommen sind.

- Globaler Administrator
- Partnersupport der Ebene 1
- Partnersupport der Ebene 2
- Benutzeradministrator

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurieren einer Benennungsrichtlinie im Azure-Portal

1. Melden Sie sich mit einem Gruppenadministratorkonto beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie **Gruppen** aus, und wählen Sie dann **Benennungsrichtlinie** aus, um die Seite „Benennungsrichtlinie“ zu öffnen.

    ![Öffnen der Seite „Benennungsrichtlinie“ im Admin Center](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Anzeigen und Bearbeiten von Präfix-/Suffixbenennungsrichtlinien

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Benennungsrichtlinie für Gruppen** aus.
1. Sie können die aktuellen Präfix- oder Suffixbenennungsrichtlinien einzeln anzeigen oder bearbeiten, indem Sie die Attribute oder Zeichenfolgen auswählen, die Sie als Teil der Benennungsrichtlinie erzwingen möchten.
1. Um ein Präfix oder Suffix aus der Liste zu entfernen, wählen Sie das Präfix oder Suffix aus, und wählen Sie dann **Löschen** aus. Es können mehrere Elemente gleichzeitig gelöscht werden.
1. Speichern Sie Ihre Änderungen für die neue Richtlinie, damit diese wirksam werden, indem Sie auf **Speichern** klicken.

### <a name="edit-custom-blocked-words"></a>Bearbeiten benutzerdefinierter blockierter Wörter

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Blockierte Wörter** aus.

    ![Bearbeiten und Hochladen der Liste der blockierten Wörter für die Benennungsrichtlinie](./media/groups-naming-policy/blockedwords.png)

1. Wählen Sie **Herunterladen** aus, um die aktuelle Liste der benutzerdefinierten blockierten Wörter anzuzeigen oder zu bearbeiten.
1. Laden Sie die neue Liste der benutzerdefinierten blockierten Wörter durch Auswählen des Dateisymbols hoch.
1. Speichern Sie Ihre Änderungen für die neue Richtlinie, damit diese wirksam werden, indem Sie auf **Speichern** klicken.

## <a name="install-powershell-cmdlets"></a>Installieren von PowerShell-Cmdlets

Achten Sie darauf, dass Sie alle älteren Versionen von Azure Active Directory-PowerShell für das Graph-Modul für Windows PowerShell deinstallieren und [Azure Active Directory-PowerShell für Graph – öffentliche Vorschauversion 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) vor dem Ausführen der PowerShell-Befehle installieren.

1. Führen Sie die Windows PowerShell-App als Administrator aus.
2. Deinstallieren Sie alle vorherigen Versionen von AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Installieren Sie die neueste Version von AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Wenn Sie über den Zugriff auf ein nicht vertrauenswürdiges Repository informiert werden, geben Sie **Y** ein. Es dauert möglicherweise einige Minuten, bis das neue Modul installiert wird.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurieren von Benennungsrichtlinien in PowerShell

1. Öffnen Sie ein Windows PowerShell-Fenster auf Ihrem Computer. Sie können es ohne erhöhte Rechte öffnen.

1. Führen Sie die folgenden Befehle aus, um die Ausführung der Cmdlets vorzubereiten.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Geben Sie auf dem angezeigten Bildschirm **Bei Ihrem Konto anmelden** Ihr Administratorkonto und das zugehörige Kennwort ein, um eine Verbindung mit dem Dienst herzustellen, und wählen Sie **Anmelden** aus.

1. Um Gruppeneinstellungen für diese Organisation zu erstellen, führen Sie die Schritte in [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md) aus.

### <a name="view-the-current-settings"></a>Anzeigen der aktuellen Einstellungen

1. Rufen Sie die aktuelle Benennungsrichtlinie ab, um die derzeitigen Einstellungen anzuzeigen.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Zeigen Sie die aktuellen Gruppeneinstellungen an.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Festlegen von Benennungsrichtlinie und benutzerdefinierten blockierten Wörtern

1. Legen Sie die Präfixe und Suffixe für Gruppennamen in Azure AD PowerShell fest. Für die ordnungsgemäße Funktion des Features muss [GroupName] in der Einstellung enthalten sein.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Legen Sie benutzerdefinierte blockierte Wörter fest, deren Verwendung Sie einschränken möchten. Im folgenden Beispiel wird veranschaulicht, wie Sie Ihre eigenen benutzerdefinierten Wörter hinzufügen können.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Speichern Sie die Einstellungen wie im folgenden Beispiel gezeigt, damit die neue Richtlinie angewendet wird.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Das ist alles. Sie haben Ihre Benennungsrichtlinie festgelegt und eigene blockierte Wörter hinzugefügt.

## <a name="export-or-import-custom-blocked-words"></a>Exportieren oder Importieren benutzerdefinierter blockierter Wörter

Weitere Informationen finden Sie im Artikel [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md).

Hier ist ein PowerShell-Beispielskript zum Exportieren mehrerer blockierter Wörter:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Hier ist ein PowerShell-Beispielskript zum Importieren mehrerer blockierter Wörter:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Entfernen der Benennungsrichtlinie

### <a name="remove-the-naming-policy-using-azure-portal"></a>Entfernen der Benennungsrichtlinie im Azure-Portal

1. Wählen Sie auf der Seite **Benennungsrichtlinie** die Option **Richtlinie löschen** aus.
1. Nach dem Bestätigen des Löschvorgangs wird die Benennungsrichtlinie einschließlich aller Präfix-/Suffixbenennungsrichtlinien und benutzerdefinierten blockierten Wörter entfernt.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Entfernen der Benennungsrichtlinie mit Azure AD PowerShell

1. Leeren Sie die Präfixe und Suffixe für Gruppennamen in Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Leeren Sie die benutzerdefinierten blockierten Wörter.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Speichern Sie die Einstellungen.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Benutzeroberfläche von Microsoft 365-Apps

Nachdem Sie eine Gruppenbenennungsrichtlinie in Azure AD festgelegt haben, wird Benutzern beim Erstellen einer Gruppe in einer Microsoft 365-App Folgendes angezeigt:

- Eine Vorschau des Namens gemäß der Benennungsrichtlinie (mit Präfixen und Suffixen) während der Eingabe des Gruppennamens
- Eine Fehlermeldung, wenn der Benutzer blockierte Wörter eingibt, damit er diese entfernen kann

Workload | Compliance
----------- | -------------------------------
Azure Active Directory-Portale | Das Azure AD-Portal und das Zugriffsbereichs-Portal zeigen den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer beim Erstellen oder Bearbeiten einer Gruppen einen Gruppennamen eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
Outlook Web Access (OWA) | Outlook Web Access zeigt den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer einen Gruppennamen oder Gruppenalias eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung auf der Benutzeroberfläche mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
Outlook-Desktop | In Outlook-Desktop erstellte Gruppen sind mit den Einstellungen der Benennungsrichtlinie konform. Die Outlook-Desktop-App zeigt bisher noch keine Vorschau des erzwungenen Gruppennamens an, und es werden auch keine Fehler bei benutzerdefinierten blockierten Wörtern zurückgeben, wenn der Benutzer den Gruppennamen eingibt. Allerdings wird die Benennungsrichtlinie automatisch beim Erstellen oder Bearbeiten einer Gruppen angewendet, und den Benutzern wird eine Fehlermeldung angezeigt, wenn der Gruppenname oder -alias benutzerdefinierte blockierte Wörter enthält.
Microsoft Teams | Microsoft Teams zeigt den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer einen Teamnamen eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
SharePoint  |  SharePoint zeigt den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer einen Standortnamen oder die E-Mail-Adresse einer Gruppe eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
Microsoft Stream | Microsoft Stream zeigt den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer einen Gruppennamen oder den E-Mail-Alias einer Gruppe eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
Outlook-App für iOS und Android | In Outlook-Apps erstellte Gruppen sind mit der konfigurierten Benennungsrichtlinie konform. Die mobile Outlook-App zeigt bisher noch keine Vorschau des durch die Benennungsrichtlinie erzwungenen Gruppennamens an, und es werden auch keine Fehler bei benutzerdefinierten blockierten Wörtern zurückgeben, wenn der Benutzer den Gruppennamen eingibt. Allerdings wird die Benennungsrichtlinie automatisch beim Klicken auf „Erstellen“ oder „Bearbeiten“ angewendet, und den Benutzern wird eine Fehlermeldung angezeigt, wenn der Gruppenname oder -alias benutzerdefinierte blockierte Wörter enthält.
Mobile Groups-App | In der mobilen Groups-App erstellte Gruppen sind mit der Benennungsrichtlinie konform. Die mobile Groups-App zeigt bisher noch keine Vorschau des durch die Benennungsrichtlinie erzwungenen Gruppennamens an, und es werden auch keine Fehler bei benutzerdefinierten blockierten Wörtern zurückgeben, wenn der Benutzer den Gruppennamen eingibt. Allerdings wird die Benennungsrichtlinie automatisch beim Erstellen oder Bearbeiten einer Gruppen angewendet, und den Benutzern wird ein entsprechender Fehler angezeigt, wenn der Gruppenname oder -alias benutzerdefinierte blockierte Wörter enthält.
Planner | Planner ist mit der Benennungsrichtlinie konform. Planner zeigt bei der Eingabe des Plannamens eine Vorschau des durch die Benennungsrichtlinie erzwungenen Namens an. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird beim Erstellen des Plans eine Fehlermeldung angezeigt.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement ist mit der Benennungsrichtlinie konform. Dynamics 365 zeigt den durch die Benennungsrichtlinie erzwungenen Namen an, wenn der Benutzer einen Gruppennamen oder den E-Mail-Alias einer Gruppe eingibt. Wenn ein Benutzer ein benutzerdefiniertes blockiertes Wort eingibt, wird eine Fehlermeldung mit dem blockierten Wort angezeigt, damit der Benutzer es entfernen kann.
School Data Sync (SDS) | Gruppen, die mit SDS erstellt werden, sind mit der Benennungsrichtlinie konform, die Benennungsrichtlinie wird jedoch nicht automatisch angewendet. SDS-Administratoren müssen die Präfixe und Suffixe an Klassennamen, für die Gruppen erstellt und hochgeladen werden müssen, anfügen. Das Erstellen oder Bearbeiten der Gruppe würde andernfalls zu einem Fehler führen.
Classroom-App | Die in der Classroom-App erstellten Gruppen sind mit der Benennungsrichtlinie konform, die jedoch nicht automatisch angewendet wird. Den Benutzern wird während der Eingabe des Classroom-Gruppennamens auch keine Vorschau des durch die Benennungsrichtlinie erzwungenen Namens angezeigt. Die Benutzer müssen den erzwungenen Classroom-Gruppennamen inklusive Präfixen und Suffixen eingeben. Andernfalls tritt beim Erstellen oder Bearbeiten der Classroom-Gruppe ein Fehler auf.
Power BI | Power BI-Arbeitsbereiche sind mit der Benennungsrichtlinie konform.    
Yammer | Wenn ein Benutzer, der sich mit seinem Azure Active Directory-Konto bei Yammer angemeldet hat, eine Gruppe erstellt oder einen Gruppennamen bearbeitet, entspricht der Gruppenname der Benennungsrichtlinie. Dies gilt sowohl für mit Microsoft 365 verbundene Gruppen als auch für alle anderen Yammer-Gruppen.<br>Wenn eine mit Microsoft 365 verbundene Gruppe erstellt wurde, bevor die Benennungsrichtlinie vorhanden war, entspricht der Gruppenname nicht automatisch den Benennungsrichtlinien. Wenn ein Benutzer den Gruppennamen bearbeitet, wird er aufgefordert, das Präfix und das Suffix hinzuzufügen.
StaffHub  | StaffHub-Teams unterliegen nicht der Benennungsrichtlinie, die zugrunde liegende Microsoft 365-Gruppe aber schon. Auf StaffHub-Teamnamen werden nicht die Präfixe und Suffixe angewendet, und es erfolgt keine Prüfung auf benutzerdefinierte blockierte Wörter. StaffHub wendet die Präfixe und Suffixe allerdings auf die zugrunde liegende Microsoft 365-Gruppe an und entfernt dort blockierte Wörter.
Exchange PowerShell | Exchange PowerShell-Cmdlets sind mit der Benennungsrichtlinie konform. Benutzer erhalten entsprechende Fehlermeldungen mit Vorschlägen für Präfixe und Suffixe und den benutzerdefinierten blockierten Wörtern, wenn sie die Benennungsrichtlinie beim Gruppennamen oder -alias (mailNickname) nicht einhalten.
Azure Active Directory PowerShell-Cmdlets | Azure Active Directory PowerShell-Cmdlets sind mit der Benennungsrichtlinie konform. Benutzer erhalten entsprechende Fehlermeldungen mit Vorschlägen für Präfixe und Suffixe und den benutzerdefinierten blockierten Wörtern, wenn sie die Benennungsrichtlinie bei Gruppennamen oder -aliasen nicht einhalten.
Exchange Admin Center | Exchange Admin Center ist mit der Benennungsrichtlinie konform. Benutzer erhalten entsprechende Fehlermeldungen mit Vorschlägen für Präfixe und Suffixe und den benutzerdefinierten blockierten Wörtern, wenn sie die Benennungsrichtlinie bei Gruppennamen oder -aliasen nicht einhalten.
Microsoft 365 Admin Center | Microsoft 365 Admin Center ist mit der Benennungsrichtlinie konform. Wenn Benutzer Gruppennamen erstellen oder bearbeiten, wird die Benennungsrichtlinie automatisch angewendet, und die Benutzer erhalten entsprechende Fehler, wenn sie benutzerdefinierte blockierte Wörter eingeben. Microsoft 365 Admin Center zeigt bisher noch keine Vorschau des durch die Benennungsrichtlinie erzwungenen Gruppennamens an, und es werden auch keine Fehler bei benutzerdefinierten blockierten Wörtern zurückgeben, wenn Benutzer den Gruppennamen eingeben.

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure AD-Gruppen.

- [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ablaufrichtlinie für Microsoft 365-Gruppen](groups-lifecycle.md)
- [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten der Mitglieder einer Gruppe](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)
