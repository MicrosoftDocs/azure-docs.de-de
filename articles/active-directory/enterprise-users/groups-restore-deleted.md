---
title: 'Wiederherstellen einer gelöschten Microsoft 365-Gruppe: Azure AD | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie eine gelöschte Gruppe wiederherstellen, wiederherstellbare Gruppen anzeigen und eine Gruppe in Azure Active Directory endgültig löschen.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1eacba47e68310dd45976bcd9ddff052fa193436
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090136"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Wiederherstellen einer gelöschten Microsoft 365-Gruppe in Azure Active Directory

Wenn Sie eine Microsoft 365-Gruppe in Azure Active Directory (Azure AD) löschen, wird die gelöschte Gruppe beibehalten, ist aber 30 Tage lang nach dem Löschdatum nicht sichtbar. Damit soll sichergestellt werden, dass die Gruppe und dessen Inhalte bei Bedarf wiederhergestellt werden können. Diese Funktionalität ist ausschließlich auf Microsoft 365-Gruppen in Azure AD beschränkt. Sie ist nicht für Sicherheits- und Verteilergruppen verfügbar. Beachten Sie, dass der 30-tägige Wiederherstellungszeitraum für Gruppen nicht angepasst werden kann.

> [!NOTE]
> Verwenden Sie nicht `Remove-MsolGroup`, da hierbei die Gruppe endgültig gelöscht wird. Verwenden Sie immer `Remove-AzureADMSGroup`, um eine Microsoft 365-Gruppe zu löschen.

Die Berechtigungen, die zum Wiederherstellen einer Gruppe erforderlich sind, können Folgende umfassen:

Role | Berechtigungen
--------- | ---------
Globaler Administrator, Gruppenadministrator, Partnersupport der Ebene 2 und Intune-Administrator | Können eine beliebige gelöschte Microsoft 365-Gruppe wiederherstellen
Benutzeradministrator und Partnersupport der Ebene 1 | Können eine beliebige gelöschte Microsoft 365-Gruppe wiederherstellen (mit Ausnahme von Gruppen, die der Rolle „Globaler Administrator“ zugewiesen sind)
Benutzer | Kann eine beliebige gelöschte Microsoft 365-Gruppe wiederherstellen, die sich in seinem Besitz befand

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>Anzeigen und Verwalten der wiederherstellbaren gelöschten Microsoft 365-Gruppen

1. Melden Sie sich mit einem Benutzeradministratorkonto beim [Azure AD Admin Center](https://aad.portal.azure.com) an.

2. Wählen Sie **Gruppen** > **Gelöschte Gruppen** aus, um die gelöschten Gruppen anzuzeigen, die wiederhergestellt werden können.

    ![Anzeigen von Gruppen, die zur Wiederherstellung zur Verfügung stehen](./media/groups-restore-deleted/deleted-groups3.png)

3. Auf dem Blatt **Gelöschte Gruppen** haben Sie folgende Möglichkeiten:

   - Sie können **Gruppe wiederherstellen** auswählen, um die gelöschte Gruppe und deren Inhalt wiederherzustellen.
   - Sie können **Endgültig löschen** auswählen, um die gelöschte Gruppe endgültig zu entfernen. Gruppen können nur von einem Administrator endgültig entfernt werden.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>Anzeigen der wiederherstellbaren gelöschten Microsoft 365-Gruppen mithilfe von PowerShell

Mit den folgenden Cmdlets können gelöschte Gruppen angezeigt werden, um sicherzustellen, dass die gewünschten Gruppen noch nicht endgültig gelöscht wurden. Diese Cmdlets sind Teil des [Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureAD/). Weitere Informationen zu diesem Modul finden Sie im Artikel [Azure Active Directory PowerShell Version 2](/powershell/azure/active-directory/install-adv2).

1.  Führen Sie das folgende Cmdlet aus, um alle gelöschten Microsoft 365-Gruppen in Ihrer Azure AD-Organisation anzuzeigen, die noch zur Wiederherstellung verfügbar sind.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Wenn Sie die ObjectID einer bestimmten Gruppe kennen (und Sie diese über das Cmdlet in Schritt 1 abrufen können), führen Sie alternativ das folgende Cmdlet aus, um sicherzustellen, dass die jeweilige gelöschte Gruppe noch nicht endgültig gelöscht wurde.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>So stellen Sie Ihre gelöschte Microsoft 365-Gruppe mithilfe von PowerShell wieder her

Nachdem Sie sichergestellt haben, dass die Gruppe noch zur Wiederherstellung verfügbar ist, stellen Sie die gelöschte Gruppe über eine der folgenden Methoden wieder her. Wenn die Gruppe Dokumente, SharePoint-Websites oder andere beständige Objekte enthält, kann es bis zu 24 Stunden dauern, bis eine Gruppe und dessen Inhalte vollständig wiederhergestellt werden.

1. Führen Sie das folgende Cmdlet aus, um die Gruppe und dessen Inhalte wiederherzustellen.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Alternativ können Sie das folgende Cmdlet ausführen, um die gelöschte Gruppe dauerhaft zu löschen.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Wie finde ich heraus, ob es funktioniert hat?

Um sicherzustellen, dass Sie eine Microsoft 365-Gruppe erfolgreich wiederhergestellt haben, führen Sie das Cmdlet `Get-AzureADGroup –ObjectId <objectId>` aus, um Informationen zur Gruppe anzuzeigen. Nach Durchführung der Wiederherstellungsanforderung:

- Die Gruppe wird in der linken Navigationsleiste in Exchange angezeigt.
- Der Plan für die Gruppe wird in Planner angezeigt.
- Alle SharePoint-Websites und deren Inhalte sind verfügbar.
- Die Gruppe kann über einen der Exchange-Endpunkte und andere Microsoft 365-Workloads, die Microsoft 365-Gruppen unterstützen, aufgerufen werden.

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory-Gruppen.

* [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Verwalten der Mitglieder einer Gruppe](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)
