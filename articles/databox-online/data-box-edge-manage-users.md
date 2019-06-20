---
title: 'Azure Data Box Edge: Verwalten von Benutzern | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie über das Azure-Portal Benutzer für Ihre Azure Data Box Edge-Ressource verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 68f8ad903f967812c4a416c732b35fa1712404cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756661"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Verwalten von Benutzern für Ihre Azure Data Box Edge-Ressource über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie Benutzer für Ihre Azure Data Box Edge-Ressource verwalten. Azure Data Box Edge kann über das Azure-Portal oder über die lokale Webbenutzeroberfläche verwaltet werden. Verwenden Sie das Azure-Portal, um Benutzer hinzuzufügen, zu ändern oder zu löschen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Hinzufügen eines Benutzers
> * Ändern eines Benutzers
> * Löschen eines Benutzers

## <a name="about-users"></a>Informationen zu Benutzern

Benutzer können über Lesezugriff oder über Vollzugriff verfügen. Wie der Name schon sagt, können Benutzer mit Lesezugriff die Freigabedaten nur anzeigen. Benutzer mit Vollzugriff können Freigabedaten lesen, Schreibvorgänge für diese Freigaben ausführen sowie Freigabedaten ändern oder löschen.

 - **Benutzer mit Vollzugriff:** Lokaler Benutzer mit Vollzugriff.
 - **Benutzer mit Lesezugriff:** Lokaler Benutzer, der nur über Lesezugriff verfügt. Diese Benutzer werden Freigaben zugeordnet, die schreibgeschützte Vorgänge zulassen.

Die Benutzerberechtigungen werden definiert, wenn der Benutzer im Rahmen der Freigabeerstellung erstellt wird. Nachdem die Berechtigungen für einen Benutzer definiert wurden, können sie über den Explorer geändert werden. 


## <a name="add-a-user"></a>Hinzufügen eines Benutzers

Gehen Sie im Azure-Portal wie folgt vor, um einen Benutzer hinzuzufügen:

1. Navigieren Sie im Azure-Portal zu Ihrer Data Box Edge-Ressource und anschließend zu **Übersicht > Benutzer**. Wählen Sie auf der Befehlsleiste die Option **+ Benutzer hinzufügen** aus.

    ![Auswählen von „Benutzer hinzufügen“](media/data-box-edge-manage-users/add-user-1.png)

2. Geben Sie den Benutzernamen und das Kennwort für den Benutzer an, den Sie hinzufügen möchten. Bestätigen Sie das Kennwort, und wählen Sie **Hinzufügen** aus.

    ![Angeben des Benutzernamens und Kennworts](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Folgende Benutzer sind für das System reserviert und dürfen nicht verwendet werden: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Eine Benachrichtigung wird angezeigt, wenn die Benutzererstellung beginnt und wenn sie abgeschlossen ist. Wählen Sie nach Abschluss der Benutzererstellung auf der Befehlsleiste die Option **Aktualisieren** aus, um die aktualisierte Benutzerliste anzuzeigen.


## <a name="modify-user"></a>Ändern eines Benutzers

Das Kennwort für einen Benutzer kann nach der Benutzererstellung geändert werden. Wählen Sie in der Benutzerliste einen Benutzer aus. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Speichern Sie die Änderungen.
 
![Ändern eines Benutzers](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Löschen eines Benutzers

Gehen Sie im Azure-Portal wie folgt vor, um einen Benutzer zu löschen:


1. Navigieren Sie im Azure-Portal zu Ihrer Data Box Edge-Ressource und anschließend zu **Übersicht > Benutzer**.

    ![Auswählen des zu löschenden Benutzers](media/data-box-edge-manage-users/delete-user-1.png)

2. Wählen Sie in der Benutzerliste einen Benutzer und anschließend **Löschen** aus.  

   ![Auswählen von „Löschen“](media/data-box-edge-manage-users/delete-user-2.png)

3. Bestätigen Sie den Löschvorgang, wenn Sie dazu aufgefordert werden. 

   ![Bestätigen des Löschens](media/data-box-edge-manage-users/delete-user-3.png)

Die Benutzerliste wird nach dem Löschen des Benutzers entsprechend aktualisiert.

![Aktualisierte Benutzerliste](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Bandbreite verwalten](data-box-edge-manage-bandwidth-schedules.md).
