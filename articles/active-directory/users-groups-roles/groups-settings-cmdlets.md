---
title: Konfigurieren von Gruppeneinstellungen mit PowerShell – Azure Active Directory | Microsoft-Dokumentation
description: Verwalten der Einstellungen für Gruppen mithilfe von Azure Active Directory-Cmdlets
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/26/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c9b07e7524488d0336a55af6e1d5f36af59a870
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729822"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen
Dieser Artikel enthält Anweisungen für die Verwendung von PowerShell-Cmdlets für Azure Active Directory (Azure AD), um Gruppen zu erstellen und zu aktualisieren. Dieser Inhalt gilt nur für Office 365-Gruppen (zuweilen auch als einheitliche Gruppen bezeichnet). 

> [!IMPORTANT]
> Für einige Einstellungen ist eine Azure Active Directory Premium P1-Lizenz erforderlich. Weitere Informationen finden Sie in der Tabelle [Vorlageneinstellungen](#template-settings).

Um Benutzer ohne Administratorrechte am Erstellen von Sicherheitsgruppen zu hindern, legen Sie  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` (wie unter [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) beschrieben) fest. 

Office 365-Gruppeneinstellungen werden mithilfe eines Settings- und eines SettingsTemplate-Objekts konfiguriert. Zu Beginn werden keine Einstellungsobjekte in Ihrem Verzeichnis angezeigt, da Ihr Verzeichnis mit den Standardeinstellungen konfiguriert ist. Um die Standardeinstellungen zu ändern, erstellen Sie mithilfe einer Einstellungsvorlage ein neues Einstellungsobjekt. Einstellungsvorlagen werden von Microsoft definiert. Es werden verschiedene Einstellungsvorlagen unterstützt. Zum Konfigurieren von Office 365-Gruppeneinstellungen für Ihr Verzeichnis verwenden Sie die Vorlage mit dem Namen „Group.Unified“. Zum Konfigurieren von Office 365-Gruppeneinstellungen für eine einzelne Gruppe verwenden Sie die Vorlage „Group.Unified.Guest“. Diese Vorlage dient zum Verwalten des Gastzugriffs auf eine Office 365-Gruppe. 

Die Cmdlets gehören zum Modul Azure Active Directory PowerShell V2. Weitere Anweisungen zum Herunterladen und Installieren des Moduls auf Ihrem Computer finden Sie im Artikel [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Sie können die Version 2 des Moduls über den [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureAD/) installieren.



## <a name="create-settings-at-the-directory-level"></a>Erstellen von Einstellungen auf Verzeichnisebene
Mit diesen Schritten werden auf Verzeichnisebene Einstellungen erstellt, die für alle Office 365-Gruppen im Verzeichnis gelten. Das Cmdlet „Get-AzureADDirectorySettingTemplate“ steht nur im [Azure AD PowerShell-Vorschaumodul für Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) zur Verfügung.

1. In den DirectorySettings-Cmdlets müssen Sie die ID des SettingsTemplate-Objekts angeben, das Sie verwenden möchten. Wenn Sie diese ID nicht kennen, gibt dieses Cmdlet die Liste aller Einstellungsvorlagen zurück:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   Bei Aufruf dieses Cmdlets werden alle verfügbaren Vorlagen zurückgegeben:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Um eine URL zu Nutzungsrichtlinien hinzuzufügen, müssen Sie zunächst das SettingsTemplate-Objekt abrufen, das den Wert für die Nutzungsrichtlinien-URL definiert, also die Group.Unified-Vorlage:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Erstellen Sie danach basierend auf dieser Vorlage ein neues Einstellungsobjekt:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Aktualisieren Sie dann den Wert für die Nutzungsrichtlinie:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Wenden Sie dann die Einstellungen an:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Sie können die Werte lesen mithilfe von:

  ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Aktualisieren von Einstellungen auf Verzeichnisebene
Um den Wert für „UsageGuideLinesUrl“ in der Einstellungsvorlage zu aktualisieren, bearbeiten Sie einfach die URL mit Schritt 4 weiter oben, und führen Sie dann Schritt 5 aus, um den neuen Wert festzulegen.

Um den Wert von „UsageGuideLinesUrl“ zu entfernen, bearbeiten Sie die URL so, dass sie eine leere Zeichenfolge wird, indem Sie Schritt 4 weiter oben verwenden:

 ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Führen Sie dann Schritt 5 aus, um den neuen Wert festzulegen.

## <a name="template-settings"></a>Vorlageneinstellungen
Folgende Einstellungen sind im SettingsTemplate-Objekt „Group.Unified“ definiert. Sofern nicht anders angegeben, ist für diese Features eine Azure Active Directory Premium P1-Lizenz erforderlich. 

| **Einstellung** | **Beschreibung** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Geben Sie Folgendes ein:  Boolean<li>Standardwert: True |Das Flag, das angibt, ob die Erstellung von Office 365-Gruppen im Verzeichnis durch Benutzer ohne Administratorrechte zulässig ist. Für diese Einstellung ist keine Azure Active Directory Premium P1-Lizenz erforderlich.|
|  <ul><li>GroupCreationAllowedGroupId<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” |GUID der Sicherheitsgruppe, deren Mitgliedern das Erstellen von Office 365-Gruppen erlaubt ist, auch wenn der EnableGroupCreation-Wert „false“ lautet. |
|  <ul><li>UsageGuidelinesUrl<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” |Ein Link zu den Nutzungsrichtlinien für die Gruppe. |
|  <ul><li>ClassificationDescriptions<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” | Eine durch Trennzeichen getrennte Liste mit Klassifizierungsbeschreibungen. Der Wert von ClassificationDescriptions ist nur in folgendem Format gültig:<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>wobei Classification mit den Zeichenfolgen in ClassificationList übereinstimmt.|
|  <ul><li>DefaultClassification<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” | Die Klassifizierung, die als Standardklassifizierung einer Gruppe verwendet werden soll, falls keine angegeben wurde.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” | Zeichenfolge mit einer maximalen Länge von 64 Zeichen, mit der die für Office 365-Gruppen konfigurierte Namenskonvention definiert wird. Weitere Informationen finden Sie unter [Erzwingen einer Benennungsrichtlinie für Office 365-Gruppen](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” | Eine durch Trennzeichen getrennte Zeichenfolge mit Ausdrücken, deren Verwendung in Gruppennamen oder -aliasen nicht gestattet ist. Weitere Informationen finden Sie unter [Erzwingen einer Benennungsrichtlinie für Office 365-Gruppen](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Geben Sie Folgendes ein:  Boolean<li>Standardwert: „False“ | Nicht verwenden
|  <ul><li>AllowGuestsToBeGroupOwner<li>Geben Sie Folgendes ein:  Boolean<li>Standardwert: False | Boolescher Wert, der angibt, ob ein Gastbenutzer Besitzer von Gruppen sein kann. |
|  <ul><li>AllowGuestsToAccessGroups<li>Geben Sie Folgendes ein:  Boolean<li>Standardwert: True | Boolescher Wert, der angibt, ob ein Gastbenutzer Zugriff auf die Inhalte von Office 365-Gruppen hat.  Für diese Einstellung ist keine Azure Active Directory Premium P1-Lizenz erforderlich.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” | Die URL eines Links zu den Leitlinien für die Nutzung des Gastzugriffs. |
|  <ul><li>AllowToAddGuests<li>Geben Sie Folgendes ein:  Boolean<li>Standardwert: True | Ein boolescher Wert, der angibt, ob das Hinzufügen von Gästen zu diesem Verzeichnis erlaubt ist.|
|  <ul><li>ClassificationList<li>Geben Sie Folgendes ein:  Zeichenfolge<li>Standardwert: “” |Eine durch Trennzeichen getrennte Liste der gültigen Klassifizierungswerte, die auf Office 365-Gruppen angewendet werden können. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Beispiel: Konfigurieren einer Gastrichtlinie für Gruppen auf Verzeichnisebene
1. Rufen Sie alle Einstellungsvorlagen ab:
  ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Um die Gastrichtlinie für Gruppen auf Verzeichnisebene festzulegen, benötigen Sie die Vorlage „Group.Unified“.
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Erstellen Sie danach basierend auf dieser Vorlage ein neues Einstellungsobjekt:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Aktualisieren Sie dann die Einstellung „AllowToAddGuests“.
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Wenden Sie dann die Einstellungen an:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Sie können die Werte lesen mithilfe von:

  ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Lesen von Einstellungen auf Verzeichnisebene

Wenn Sie den Namen der Einstellung kennen, die Sie abrufen möchten, können Sie das untenstehende Cmdlet verwenden, um den aktuellen Einstellungswert abzurufen. In diesem Beispiel rufen wir den Wert für eine Einstellung namens „UsageGuidelinesUrl“ ab. 

  ```powershell
  (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
  ```
Mit diesen Schritten werden auf Verzeichnisebene Einstellungen gelesen, die für alle Office-Gruppen im Verzeichnis gelten.

1. Lesen aller vorhandenen Verzeichniseinstellungen:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Dieses Cmdlet gibt eine Liste aller Verzeichniseinstellungen zurück:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Lesen aller Einstellungen für eine bestimmte Gruppe:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Lesen aller Werte von Verzeichniseinstellungen für ein bestimmtes Verzeichniseinstellungsobjekt mithilfe der Einstellungs-ID „GUID“:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Dieses Cmdlet gibt die Namen und Werte in diesem Einstellungsobjekt für diese spezielle Gruppe zurück:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Entfernen von Einstellungen auf Verzeichnisebene
Mit diesen Schritten werden auf Verzeichnisebene Einstellungen entfernt, die für alle Office-Gruppen im Verzeichnis gelten.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="update-settings-for-a-specific-group"></a>Aktualisieren von Einstellungen für eine bestimmte Gruppe

1. Suchen der Einstellungsvorlage mit dem Namen „Groups.Unified.Guest“
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Abrufen des Vorlagenobjekts für die Vorlage „Groups.Unified.Guest“:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Erstellen eines neuen Einstellungsobjekts anhand der Vorlage:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Festlegen der Einstellung auf den erforderlichen Wert:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Erstellen der neuen Einstellung für die erforderliche Gruppe im Verzeichnis:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $SettingCopy
   ```
6. Um die Einstellungen zu überprüfen, führen Sie diesen Befehl aus:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Referenz der Cmdletsyntax
Weitere Informationen zu Azure Active Directory PowerShell finden Sie unter [Azure Active Directory-Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Zusätzliche Lektüre

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
