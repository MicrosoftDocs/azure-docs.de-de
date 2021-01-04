---
title: Verwalten von Einstellungen – Custom Translator
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie in Custom Translator Einstellungen verwalten, Arbeitsbereiche erstellen und freigeben sowie Abonnementschlüssel verwalten.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e556ca2051fd75eb9bf6411c5479e63554606d48
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368337"
---
# <a name="how-to-manage-settings"></a>Verwalten von Einstellungen

Auf der Seite „Einstellungen“ von Benutzerdefinierter Translator können Sie Ihren Arbeitsbereich freigeben, Ihren Translator-Abonnementschlüssel ändern sowie den Arbeitsbereich löschen.

So greifen Sie auf die Seite „Einstellungen“ zu

1. Melden Sie sich beim Portal [Custom Translator](https://portal.customtranslator.azure.ai/) an.
2. Klicken Sie im Custom Translator-Portal in der Seitenleiste auf das Zahnradsymbol.

    ![Verknüpfung zu Einstellungen](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Zuordnen des Translator-Abonnements

Zum Trainieren oder Bereitstellen von Modellen muss Ihrem Arbeitsbereich ein Translator-Abonnementschlüssel zugeordnet sein.

Wenn Sie über kein Abonnement verfügen, gehen Sie folgendermaßen vor:

1. Abonnieren Sie Translator, um eine Translator-Ressource zu erstellen. Befolgen Sie die Schritte unter [Registrieren für Translator](../translator-how-to-signup.md), um Translator zu abonnieren und einen Translator-Schlüssel abzurufen.
2. Notieren Sie sich den Schlüssel Ihres Translator-Abonnements. „Key1“ oder „Key2“ sind zulässig.
3. Navigieren Sie zurück zum Custom Translator-Portal.

## <a name="create-a-new-workspace"></a>Erstellen eines neuen Arbeitsbereichs

1. Klicken Sie in der Randleiste „Benutzerdefinierter Translator“ auf die Schaltfläche „+ Arbeitsbereich erstellen“.

    ![Erstellen eines neuen Arbeitsbereichs](media/how-to/create-new-workspace.png)

2. Geben Sie im Dialogfeld den Namen des neuen Arbeitsbereichs ein.
3. Klicken Sie auf „Weiter“.
4. Wählen Sie den Abonnementtyp aus.
5. Wählen Sie die Abonnementregion aus. Die Region muss der Region entsprechen, die beim Erstellen des Translator-Schlüssels ausgewählt war.
6. Geben Sie den Schlüssel für Ihr Translator-Abonnement ein, und klicken Sie dann auf die Schaltfläche „Speichern“.

    ![Dialogfeld „Neuen Arbeitsbereich erstellen“](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Der benutzerdefinierte Translator unterstützt nicht das Erstellen eines Arbeitsbereichs für eine Textübersetzungs-API-Ressource (auch als Azure-Abonnementschlüssel bezeichnet), die innerhalb von [Enabled VNET](../../../api-management/api-management-using-with-vnet.md) erstellt wurde.

### <a name="modify-existing-key"></a>Ändern eines vorhandenen Schlüssels

1. Navigieren Sie zur Seite „Einstellungen“ für Ihren Arbeitsbereich.
2. Klicken Sie auf „Schlüssel ändern“.

    ![Hinzufügen eines Abonnementschlüssels](media/how-to/how-to-add-subscription-key.png)

3. Geben Sie im Dialogfeld den Schlüssel für Ihr Translator-Abonnement ein, und klicken Sie dann auf die Schaltfläche „Speichern“.

    ![Dialogfenster zum Hinzufügen eines Abonnementschlüssels](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Verwalten Ihres Arbeitsbereichs

Ein Arbeitsbereich ist ein Bereich zum Erstellen und Entwickeln Ihres benutzerdefinierten Übersetzungssystems. Er kann mehrere Projekte, Modelle und Dokumente enthalten.

Wenn Sie unterschiedliche Teilbereiche Ihrer Arbeit für verschiedene Personen freigegeben möchten, ist es sinnvoll, mehrerer Arbeitsbereiche zu erstellen.

## <a name="share-your-workspace"></a>Freigeben Ihres Arbeitsbereichs

In Custom Translator können Sie Ihren Arbeitsbereich für andere freigeben, z.B. wenn verschiedene Teilbereiche Ihrer Arbeit mit unterschiedlichen Personen geteilt werden sollen.

1. Navigieren Sie im Arbeitsbereich zur Seite „Einstellungen“.
2. Klicken Sie im Abschnitt „Freigabeeinstellungen“ auf die Schaltfläche „Personen hinzufügen“.

    ![Freigeben des Arbeitsbereichs](media/how-to/share-workspace.png)

3. Geben Sie im Dialogfeld eine durch Trennzeichen getrennte Liste der E-Mail-Adressen ein, für die Sie diesen Arbeitsbereich freigeben möchten. Achten Sie darauf, dass Sie die E-Mail-Adressen angeben, mit denen sich die Benutzer bei Custom Translator anmelden. Wählen Sie dann die gewünschte Stufe für die Freigabeberechtigung aus, und klicken Sie auf die Schaltfläche „Speichern“.

    ![Dialogfeld „Arbeitsbereich freigeben“](media/how-to/share-workspace-dialog.png)

4. Wenn Ihr Arbeitsbereich noch den Standardnamen „Mein Arbeitsbereich“ hat, müssen Sie diesen vor der Freigabe ändern.
5. Klicken Sie auf „Speichern“.

## <a name="sharing-permissions"></a>Freigabeberechtigungen

1. **Leser** : Benutzer mit dieser Freigabeberechtigung können alle Informationen im Arbeitsbereich anzeigen.

2. **Bearbeiter** : Benutzer mit dieser Freigabeberechtigung können im Arbeitsbereich Dokumente hinzufügen, Modelle trainieren sowie Dokumente und Projekte löschen. Außerdem haben sie die Möglichkeit, einen Abonnementschlüssel hinzuzufügen. Allerdings können diese Benutzer nicht ändern, für wen der Arbeitsbereich freigegeben ist, den Arbeitsbereich löschen oder den Arbeitsbereichsnamen ändern.

3. **Besitzer** : Benutzer mit dieser Freigabeberechtigung verfügen über uneingeschränkte Berechtigungen für den Arbeitsbereich.

## <a name="change-sharing-permission"></a>Ändern der Freigabeberechtigung

Wenn ein Arbeitsbereich freigegeben wurde, zeigt der Abschnitt „Freigabeeinstellungen“ alle E-Mail-Adressen an, für die die Freigabe gilt. Sie können die aktuelle Freigabeberechtigung jeder E-Mail-Adresse ändern, wenn Sie für den Arbeitsbereich über die Freigabeberechtigung „Besitzer“ verfügen.

1. Im Abschnitt „Freigabeeinstellungen“ zeigt ein Dropdownmenü die aktuelle Berechtigungsstufe jeder E-Mail-Adresse an.

2. Klicken Sie auf das Dropdownmenü, und wählen Sie die neue Berechtigungsstufe aus, die Sie der jeweiligen E-Mail-Adresse zuweisen möchten.

    ![Einstellungen für die Freigabeberechtigung](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Anheften Ihres Arbeitsbereichs

Der erste erstellte Arbeitsbereich wird standardmäßig angeheftet. Jedes Mal, wenn Sie sich anmelden, wird der angeheftete Arbeitsbereich beim Laden der Website angezeigt. Wenn Sie viele Arbeitsbereiche erstellt haben und einen von diesen bei der Anmeldung als Standard festlegen möchten, müssen Sie ihn anheften.

1. Klicken Sie auf der Randleiste auf den Namen des Arbeitsbereichs, den Sie anheften möchten.
2. Navigieren Sie zur Seite „Einstellungen“ für Ihren Arbeitsbereich.
3. Klicken Sie auf das Stecknadelsymbol.

    ![Anheften des Arbeitsbereichs](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Erstellen Ihres Arbeitsbereichs und Ihrer Projekte](workspace-and-project.md)