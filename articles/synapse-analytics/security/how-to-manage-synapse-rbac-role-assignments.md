---
title: 'Vorgehensweise: Verwalten von Synapse RBAC-Zuweisungen in Synapse Studio'
description: In diesem Artikel wird beschrieben, wie Synapse RBAC-Rollen den AAD-Sicherheitsprinzipalen zugewiesen und widerrufen werden.
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: f26b54a18ff137575cac208d70e4693c0b9914a8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572285"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Vorgehensweise: Verwalten von Synapse RBAC-Rollenzuweisungen in Synapse Studio

Synapse RBAC weist Benutzern, Gruppen und anderen Sicherheitsprinzipalen mit Rollen Berechtigungen zu, mit denen sie auf Synapse-Ressourcen und Codeartefakte zugreifen und diese verwenden können.  [Weitere Informationen](./synapse-workspace-synapse-rbac.md)

In diesem Artikel wird das Hinzufügen und Löschen von Synapse RBAC-Rollenzuweisungen erläutert.

>[!Note]
>- Zum Verwalten von Synapse RBAC-Rollenzuweisungen müssen Sie im Arbeitsbereich oder einen niedrigeren Bereich, der die Objekte enthält, die Sie verwalten möchten, über die Synapse-Administratorrolle verfügen. Wenn Sie im Arbeitsbereich ein Synapse-Administrator sind, können Sie auf alle Objekte im Arbeitsbereich Zugriff gewähren. 
>- **Gastbenutzer** aus einem anderen AD-Mandanten können Rollenzuweisungen nicht anzeigen oder verwalten, selbst wenn ihnen die Rolle „Synapse-Administrator“ zugewiesen wurde.
>- Um Ihnen zu erleichtern, den Zugriff auf einen Arbeitsbereich wieder zu erlangen, wenn Ihnen keine Synapse-Administratoren zugewiesen sind oder zur Verfügung stehen, können Benutzer mit Berechtigungen zum Verwalten von **Azure RBAC**-Rollenzuweisungen im Arbeitsbereich auch **Synapse RBAC**-Rollenzuweisungen verwalten, was das Hinzufügen von Synapse-Administrator- oder anderen Synapse-Rollenzuweisungen ermöglicht.
>- Der Zugriff auf SQL-Pools wird mithilfe von SQL-Berechtigungen verwaltet.  Mit Ausnahme der Synapse-Administrator- und Synapse SQL-Administratorrollen gewähren Synapse RBAC-Rollen keinen Zugriff auf SQL-Pools.

>[!important]
>- Bis Änderungen an den Synapse RBAC-Rollenzuweisungen in Kraft treten, können 2-5 Minuten vergehen. 
>- Wenn Sie Synapse RBAC-Berechtigungen verwalten, indem Sie die Mitgliedschaft von Sicherheitsgruppen ändern, werden die Änderungen an der Mitgliedschaft mit Azure Active Directory verwaltet.  Bis Änderungen an Gruppenmitgliedschaften in Kraft treten, können 10-15 Minuten vergehen.

## <a name="open-synapse-studio"></a>Öffnen von Synapse Studio  

Wenn Sie einem Benutzer, einer Gruppe, einem Dienstprinzipal oder einer verwalteten Identität eine Rolle zuweisen möchten, müssen Sie [zuerst Synapse Studio öffnen](https://web.azuresynapse.net/) und Ihren Arbeitsbereich auswählen. 

![Anmelden beim Arbeitsbereich](./media/common/login-workspace.png) 
 
 Nachdem Sie den Arbeitsbereich geöffnet haben, erweitern Sie links den Abschnitt **Sicherheit**, und wählen Sie **Zugriffssteuerung** aus. 

 ![Auswählen von „Zugriffssteuerung“ auf der linken Seite im Abschnitt „Sicherheit“](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Auf dem Bildschirm „Zugriffssteuerung“ sind die aktuellen Rollenzuweisungen aufgelistet.  Sie können die Liste nach dem Prinzipalnamen oder der E-Mail-Adresse sowie den enthaltenen Objekttypen, Rollen oder Bereichen einzeln filtern. Auf diesem Bildschirm können Sie Rollenzuweisungen hinzufügen oder entfernen.  

## <a name="add-a-synapse-role-assignment"></a>Hinzufügen einer Synapse-Rollenzuweisung

Wählen Sie auf dem Zugriffssteuerungsbildschirm **+ Hinzufügen** aus, um eine neue Rollenzuweisung zu erstellen.

![Klicken auf „+ Hinzufügen“, um eine neue Rollenzuweisung zu erstellen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Auf der Registerkarte „Rollenzuweisung hinzufügen“ können Sie Rollenzuweisungen im Arbeitsbereich oder Arbeitsbereichselement-Bereich erstellen. 

## <a name="add-workspace-scoped-role-assignment"></a>Hinzufügen einer Rollenzuweisung für den Arbeitsbereich

Wählen Sie zunächst **Arbeitsbereich** als Bereich und dann die **Synapse RBAC-Rolle** aus.  Wählen Sie den/die **Prinzipal(e)** aus, denen die Rolle zugewiesen werden soll, und erstellen Sie dann die Rollenzuweisung(en). 

![Hinzufügen der Rollenzuweisung für den Arbeitsbereich – Rolle auswählen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Die zugewiesene Rolle gilt für alle anwendbaren Objekte im Arbeitsbereich.

## <a name="add-workspace-item-scoped-role-assignment"></a>Hinzufügen eines Arbeitsbereichselements – Rollenzuweisung für den Bereich

Wenn Sie eine Rolle in einem differenzierteren Bereich zuweisen möchten, wählen Sie **Arbeitsbereichselement** als Bereich und dann den Bereich **Elementtyp** aus.       

![Hinzufügen der Rollenzuweisung für das Arbeitsbereichselement – Elementtyp auswählen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Wählen Sie das spezifische **Element** aus, das als Bereich verwendet werden soll, und dann die zuzuweisende **Rolle** aus der Dropdownliste.  In der Dropdownliste werden nur die Rollen aufgelistet, die für den ausgewählten Elementtyp gültig sind. [Weitere Informationen](https://go.microsoft.com/fwlink/?linkid=2148306)  

![Hinzufügen der Rollenzuweisung für das Arbeitsbereichselement – Rolle auswählen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Dann **wählen Sie den/die Prinzipal(e)** aus, dem/denen die Rolle zugewiesen werden soll.  Sie können mehrere Prinzipale iterativ auswählen.  Wählen Sie **Speichern** aus, um die Rollenzuweisung(en) zu erstellen.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Entfernen von Synapse RBAC-Rollenzuweisungen

Um den Synapse RBAC-Zugriff zu widerrufen, entfernen Sie die entsprechenden Rollenzuweisungen.  Verwenden Sie auf dem Bildschirm „Zugriffssteuerung“ die Filter zum Suchen der zu entfernenden Rollenzuweisung (en).  Markieren Sie die Rollenzuweisungen, und wählen Sie dann **Zugriff entfernen** aus.   

![Löschen einer Rollenzuweisung, um den Zugriff zu entfernen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Beachten Sie, dass Änderungen an den Rollenzuweisungen erst nach 2-5 Minuten in Kraft treten.   

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu den Rollen, die zum Ausführen allgemeiner Aufgaben in Synapse erforderlich sind](./synapse-workspace-understand-what-role-you-need.md) 
