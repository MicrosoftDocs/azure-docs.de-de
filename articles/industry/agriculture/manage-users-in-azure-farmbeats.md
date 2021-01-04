---
title: Verwalten von Benutzern in Azure FarmBeats
description: In diesem Artikel wird beschrieben, wie Sie Benutzer in Azure FarmBeats verwalten.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: e2769e97dde7f0b99bd29da9c6eb65954bc84dd2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677555"
---
# <a name="manage-users"></a>Verwalten von Benutzern

Azure FarmBeats umfasst die Benutzerverwaltung für Personen, die Teil Ihres Azure Active Directory-Instanz (Azure AD) sind. Sie können Ihrer Azure FarmBeats-Instanz Benutzer hinzufügen, um auf die APIs zuzugreifen, die generierten Karten anzuzeigen sowie auf die Sensortelemetriedaten aus dem landwirtschaftlichen Betrieb zuzugreifen.

## <a name="prerequisites"></a>Voraussetzungen

- Die Installation von Azure FarmBeats ist erforderlich. Weitere Informationen finden Sie unter [Installieren von Azure FarmBeats](install-azure-farmbeats.md).
- Die E-Mail-IDs der Benutzer, die Sie Ihrer Azure FarmBeats-Instanz hinzufügen oder die Sie aus ihr entfernen möchten.

## <a name="manage-azure-farmbeats-users"></a>Verwalten von Azure FarmBeats-Benutzern

Azure FarmBeats verwendet Azure AD für Authentifizierung, Zugriffssteuerung und Rollen. Sie können Benutzer im Azure AD-Mandanten als Benutzer in Azure FarmBeats hinzufügen.

> [!NOTE]
> Wenn ein Benutzer kein Azure AD-Mandantenbenutzer ist, befolgen Sie die Anweisungen im Abschnitt **Hinzufügen von Azure AD-Benutzern** , um das Setup abzuschließen.

Azure FarmBeats unterstützt zwei Arten von Benutzerrollen:

 - **Administrator** : Der Administrator hat vollständigen Zugriff auf Azure FarmBeats-Datenhub-APIs. Benutzer mit dieser Rolle können alle Datenhub-Objekte von Azure FarmBeats abfragen und alle Vorgänge vom FarmBeats-Accelerator aus durchführen.
 - **Schreibgeschützt** : Schreibgeschützter Zugriff auf FarmBeats-Datenhub-APIs. Benutzer können die Datenhub-APIs, die Accelerator-Dashboards und die Karten anzeigen. Benutzer mit schreibgeschütztem Zugriff können keine Vorgänge wie das Erstellen von Karten, das Zuordnen von Geräten oder das Erstellen von landwirtschaftlichen Betrieben durchführen.

## <a name="add-users-to-azure-farmbeats"></a>Hinzufügen von Benutzern zu Azure FarmBeats

So fügen Sie Benutzer zu Azure FarmBeats hinzu

1. Melden Sie sich beim Accelerator an, und wählen Sie dann das Symbol **Einstellungen** aus.
2. Wählen Sie **Zugriffssteuerung** aus.

    ![Bereich für Farmeinstellungen](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Geben Sie die E-Mail-ID des Benutzers ein, dem Sie Zugriff gewähren möchten.
4. Wählen Sie die gewünschte Rolle aus – **Administrator** oder **Schreibgeschützt** .
5. Wählen Sie **Rolle hinzufügen** aus.

Die hinzugefügten Benutzer können jetzt auf Azure FarmBeats (sowohl Datenhub als auch Accelerator) zugreifen.

## <a name="delete-users-from-azure-farmbeats"></a>Löschen von Benutzern aus Azure FarmBeats

So entfernen Sie Benutzer aus dem Azure FarmBeats-System

1. Melden Sie sich beim Accelerator an, und wählen Sie dann das Symbol **Einstellungen** aus.
2. Wählen Sie **Zugriffssteuerung** aus.
3. Klicken Sie auf **Löschen** .

   Der Benutzer wurde aus dem System gelöscht. Sie erhalten die folgende Bestätigungsmeldung:

   ![Azure FarmBeats-Bestätigungsmeldung](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Hinzufügen von Azure AD-Benutzern

> [!NOTE]
> Azure FarmBeats-Benutzer müssen im Azure AD-Mandanten vorhanden sein, bevor Sie Ihnen Anwendungen und Rollen zuweisen können. Wenn ein Benutzer nicht im Azure AD-Mandanten vorhanden ist, befolgen Sie die Anweisungen in diesem Abschnitt. Überspringen Sie die Anweisungen, wenn ein Benutzer bereits im Azure AD-Mandanten vorhanden ist.

Führen Sie die Schritte aus, um Benutzer zu Azure AD hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie oben rechts Ihr Konto aus und wechseln Sie dann zum Azure AD-Mandanten, der FarmBeats zugeordnet ist.
3. Wählen Sie **Azure Active Directory** > **Benutzer** aus.

    Eine Liste der Azure AD-Benutzer wird angezeigt.

4. Um einen Benutzer zum Verzeichnis hinzuzufügen, wählen Sie **Neuer Benutzer** aus. Zum Hinzufügen externer Benutzer wählen Sie **Neuer Gastbenutzer** aus.

    ![Bereich „Alle Benutzer“](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Wählen Sie den Namen des neuen Benutzers aus und füllen Sie dann die erforderlichen Felder für diesen Benutzer aus.
6. Klicken Sie auf **Erstellen** .

Weitere Informationen zum Verwalten von Azure AD-Benutzern finden Sie unter [Hinzufügen oder Löschen von Benutzern in Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfolgreich Benutzer zu Ihrer Azure FarmBeats-Instanz hinzugefügt. Erfahren Sie jetzt, wie Sie [landwirtschaftliche Betriebe erstellen und verwalten](manage-farms-in-azure-farmbeats.md#create-farms).