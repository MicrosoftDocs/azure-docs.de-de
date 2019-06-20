---
title: Einrichten des Ablaufs für Office 365-Gruppen – Azure Active Directory | Microsoft-Dokumentation
description: Einrichten des Ablaufs für Office 365-Gruppen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1de17429dfe89506445b2d47999b102f3becb15b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604395"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurieren der Ablaufrichtlinie für Office 365-Gruppen

Der Lebenszyklus von Office 365-Gruppen kann jetzt durch das Festlegen einer Ablaufrichtlinie verwaltet werden. Sie können eine Ablaufrichtlinie ausschließlich für Office 365-Gruppen in Azure Active Directory (Azure AD) festlegen.

Das Festlegen eines Ablaufs für eine Gruppe bewirkt Folgendes:

- Besitzer der Gruppe werden vor Erreichen des Ablaufs aufgefordert, die Gruppe zu erneuern.
- Nicht erneuerte Gruppen werden gelöscht.
- Gelöschte Office 365-Gruppen können innerhalb von 30 Tagen von den Gruppenbesitzern oder vom Administrator wiederhergestellt werden.

Derzeit kann für Office 365-Gruppen auf einem Mandanten nur eine Ablaufrichtlinie konfiguriert werden.

> [!NOTE]
> Beim Konfigurieren und Verwenden der Ablaufrichtlinie für Office 365-Gruppen müssen Sie über Azure AD Premium-Lizenzen für alle Mitglieder der Gruppen verfügen, auf die die Ablaufrichtlinie angewendet wird.

Informationen zum Herunterladen und Installieren der Azure AD-PowerShell-Cmdlets finden Sie unter [Azure Active Directory PowerShell for Graph – 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory: PowerShell für Graph – 2.0.0.137).

## <a name="roles-and-permissions"></a>Rollen und Berechtigungen

Nachfolgend sind Rollen aufgeführt, mit denen der Ablauf für Office 365-Gruppen in Azure AD konfiguriert und verwendet werden kann.

Rolle | Berechtigungen
-------- | --------
Globaler Administrator oder Benutzeradministrator | Kann die Einstellungen der Ablaufrichtlinie für Office 365-Gruppen erstellen, lesen, aktualisieren und löschen.<br>Kann beliebige Office 365-Gruppen verlängern
Benutzer | Kann eine eigene Office 365-Gruppe verlängern<br>Kann eine eigene Office 365-Gruppe wiederherstellen<br>Kann die Einstellungen der Ablaufrichtlinie lesen

Weitere Informationen zu Berechtigungen zum Wiederherstellen von gelöschten Gruppen finden Sie unter [Wiederherstellen einer gelöschten Office 365-Gruppe in der Azure Active Directory-Vorschau](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Festlegen des Gruppenablaufs

1. Öffnen Sie das [Azure AD Admin Center](https://aad.portal.azure.com) mit dem Konto eines globalen Administrators in Ihrem Azure AD-Mandanten.

2. Wählen Sie zuerst **Gruppen** und dann **Ablauf** aus, um die Ablaufeinstellungen zu öffnen.
  
   ![Ablaufeinstellungen für Gruppen](./media/groups-lifecycle/expiration-settings.png)

3. Auf dem Blatt **Ablauf** können Sie:

  * Die Gruppenlebensdauer in Tagen festlegen. Sie könnten einen der vordefinierten Werte oder einen benutzerdefinierten Wert wählen (mindestens 31 Tage). 
  * Geben Sie eine E-Mail-Adresse an, an die die Verlängerungs- und Ablaufbenachrichtigungen gesendet werden sollten, wenn eine Gruppe keinen Besitzer hat. 
  * Wählen Sie, welche Office 365-Gruppen ablaufen. Sie können den Ablauf für **alle** Office 365-Gruppen aktivieren oder eine **Auswahl** aus den Office 365-Gruppen treffen, oder Sie wählen **Keine**, um den Ablauf für alle Gruppen zu deaktivieren.
  * Speichern Sie die Einstellungen durch Auswahl von **Speichern**.

> [!NOTE]
> Beim erstmaligen Einrichten des Ablaufs wird für alle Gruppen, deren Alter das Ablaufintervall übersteigt, ein Ablaufzeitraum von 30 Tagen festgelegt. Die erste Verlängerungsbenachrichtigung wird innerhalb eines Tages gesendet. Beispielsweise wurde Gruppe A vor 400 Tagen erstellt, und das Ablaufintervall ist auf 180 Tage festgelegt. Wenn Sie eine Ablaufrichtlinie anwenden, hat Gruppe A 30 Tage, bevor sie gelöscht wird, falls sie der Besitzer nicht verlängert.
> Wenn eine dynamische Gruppe gelöscht und wiederhergestellt wird, gilt sie als neue Gruppe und wird der Regel entsprechend erneut aufgefüllt. Dieser Vorgang kann bis zu 24 Stunden dauern.

## <a name="email-notifications"></a>E-Mail-Benachrichtigungen

E-Mail-Benachrichtigungen, wie diese werden an die Office 365-Gruppenbesitzer 30 Tage, 15 Tage und 1 Tag vor Ablauf der Gruppe gesendet. Die Sprache der E-Mail richtet sich nach der bevorzugten Sprache des Gruppenbesitzers oder der Sprache des Mandanten. Wenn der Gruppenbesitzer eine bevorzugte Sprache definiert hat oder mehrere Besitzer die gleiche bevorzugte Sprache haben, wird diese Sprache verwendet. In allen anderen Fällen wird die Sprache des Mandanten verwendet.

![E-Mail-Benachrichtigungen zum Ablauf](./media/groups-lifecycle/expiration-notification.png)

Aus der Benachrichtigungs-E-Mail zu **Gruppe verlängern** können Gruppenbesitzer direkt auf die Seite mit den Gruppendetails im Zugriffsbereich zugreifen. Auf dieser Seite erhalten Benutzer weitere Informationen zur Gruppe, z.B. Beschreibung, Zeitpunkt der letzten Verlängerung, Ablaufzeitpunkt und Möglichkeit zur Verlängerung der Gruppe. Die Seite mit den Gruppendetails enthält jetzt auch Links zu den Office 365-Gruppenressourcen, sodass der Gruppenbesitzer den Inhalt und die Aktivitäten der Gruppe bequem anzeigen kann.

Wenn eine Gruppe abläuft, wird die Gruppe einen Tag nach dem Ablaufdatum gelöscht. Eine E-Mail-Benachrichtigung wie diese wird an die Office 365-Gruppenbesitzer gesendet, um sie über den Ablauf und die nachfolgende Löschung ihrer Office 365-Gruppe zu informieren.

![E-Mail-Benachrichtigungen zur Gruppenlöschung](./media/groups-lifecycle/deletion-notification.png)

Die Gruppe kann innerhalb von 30 Tagen nach ihrer Löschung wiederhergestellt werden, indem **Gruppe wiederherstellen** gewählt oder PowerShell-Cmdlets verwendet werden. Dies ist unter [Wiederherstellen einer gelöschten Office 365-Gruppe in der Azure Active Directory-Vorschau](groups-restore-deleted.md) beschrieben. Beachten Sie, dass der 30-tägige Wiederherstellungszeitraum für Gruppen nicht angepasst werden kann.
    
Wenn die Gruppe, die Sie wiederherstellen, Dokumente, SharePoint-Websites oder andere beständige Objekte enthält, kann es bis zu 24 Stunden dauern, bis die Gruppe und deren Inhalte vollständig wiederhergestellt werden.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Abrufen des Ablaufdatums für eine Office 365-Gruppe
Zusätzlich zum Zugriffsbereich, in dem Benutzer Gruppendetails mit Ablaufdatum und letztem Erneuerungsdatum anzeigen können, kann das Ablaufdatum einer Office 365-Gruppe aus der Betaversion der Microsoft Graph-REST-API abgerufen werden. expirationDateTime als Gruppeneigenschaft wurde in der Betaversion von Microsoft Graph aktiviert. Sie kann mit einer GET-Anforderung abgerufen werden. Weitere Informationen finden Sie in [diesem Beispiel](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Um Gruppenmitgliedschaften im Zugriffsbereich zu verwalten, muss „Zugriff auf Gruppen im Zugriffsbereich einschränken“ in der Allgemeinen Einstellung für Azure Active Directory-Gruppen auf „Nein“ festgelegt werden.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Funktionsweise des Office 365-Gruppenablaufs mit einem Postfach mit gesetzlicher Aufbewahrungspflicht
Wenn eine Gruppe abläuft und gelöscht wird, werden die Daten der Gruppe für Apps wie Planner, Sites oder Teams 30 Tage nach dem Löschvorgang endgültig gelöscht. Das Postfach der Gruppe, für das eine gesetzliche Aufbewahrungspflicht gilt, wird beibehalten und nicht endgültig gelöscht. Der Administrator kann Exchange-Cmdlets verwenden, um das Postfach zum Abrufen der Daten wiederherzustellen. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Funktionsweise des Office 365-Gruppenablaufs mit Aufbewahrungsrichtlinie
Die Aufbewahrungsrichtlinie wird über das Security & Compliance Center konfiguriert. Wenn Sie eine Aufbewahrungsrichtlinie für Office 365-Gruppen eingerichtet haben, werden die Gruppenunterhaltungen im Postfach der Gruppe und die Dateien auf der Site der Gruppe nach dem Ablauf und der Löschung einer Gruppe im Aufbewahrungscontainer gemäß der Angabe in der Aufbewahrungsrichtlinie (in Tagen) beibehalten. Benutzer können die Gruppe oder ihren Inhalt nach dem Ablauf nicht mehr anzeigen, aber sie können die Site- und Postfachdaten per E-Discovery wiederherstellen.

## <a name="powershell-examples"></a>PowerShell-Beispiele
Hier sind Beispiele dafür angegeben, wie Sie PowerShell-Cmdlets zum Konfigurieren der Ablaufeinstellungen für Office 365-Gruppen in Ihrem Mandanten verwenden können:

1. Installieren Sie das PowerShell v2.0-Modul, und melden Sie sich an der PowerShell-Eingabeaufforderung an:
   ```powershell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```
2. Konfigurieren Sie die Ablaufeinstellungen per „New-AzureADMSGroupLifecyclePolicy“:  Mit diesem Cmdlet können Sie die Lebensdauer für alle Office 365-Gruppen im Mandanten auf 365 Tage festlegen. Verlängerungsbenachrichtigungen für Office 365-Gruppen ohne Besitzer werden an emailaddress@contoso.com gesendet.
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Abrufen der vorhandenen Richtlinie per „Get-AzureADMSGroupLifecyclePolicy“: Mit diesem Cmdlet können Sie die aktuell konfigurierten Einstellungen zum Office 365-Gruppenablauf abrufen. In diesem Beispiel sehen Sie Folgendes:
   * Richtlinien-ID 
   * Festlegung der Lebensdauer für alle Office 365-Gruppen im Mandanten auf 365 Tage
   * Verlängerungsbenachrichtigungen für Office 365-Gruppen ohne Besitzer werden an emailaddress@contoso.com gesendet.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Aktualisieren der vorhandenen Richtlinie per „Set-AzureADMSGroupLifecyclePolicy“: Dieses Cmdlet dient zum Aktualisieren einer vorhandenen Richtlinie. Im folgenden Beispiel wird die Lebensdauer der Gruppe in der vorhandenen Richtlinie von 365 Tagen in 180 Tage geändert. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Hinzufügen von spezifischen Gruppen zur Richtlinie per „Add-AzureADMSLifecyclePolicyGroup“: Mit diesem Cmdlet können Sie der Lebenszyklusrichtlinie eine Gruppe hinzufügen. Beispiel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Entfernen der vorhandenen Richtlinie per „Policy Remove-AzureADMSGroupLifecyclePolicy“: Mit diesem Cmdlet können Sie die Einstellungen für den Office 365-Gruppenablauf löschen (unter Angabe der die Richtlinien-ID). Hierdurch wird der Ablauf für Office 365-Gruppen deaktiviert. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Die folgenden Cmdlets können verwendet werden, um die Richtlinie ausführlicher zu konfigurieren. Weitere Informationen finden Sie in der [PowerShell-Dokumentation](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Nächste Schritte
Diese Artikel enthalten zusätzliche Informationen zu Azure AD-Gruppen.

* [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Verwalten der Mitglieder einer Gruppe](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)
