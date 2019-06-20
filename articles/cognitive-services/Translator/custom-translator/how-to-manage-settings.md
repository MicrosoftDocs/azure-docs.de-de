---
title: Verwalten von Einstellungen – Custom Translator
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie in Custom Translator Einstellungen verwalten, Arbeitsbereiche erstellen und freigeben sowie Abonnementschlüssel verwalten.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: fb766a8cc6ae0de2407f44982a58fcc448e4b00c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382668"
---
# <a name="how-to-manage-settings"></a>Verwalten von Einstellungen

Auf der Custom-Translator-Seite „Einstellungen“ können Sie einen neuen Arbeitsbereich erstellen, Ihren Arbeitsbereich freigeben und Ihren Abonnementschlüssel für Microsoft Translator hinzufügen oder ändern.

So greifen Sie auf die Seite „Einstellungen“ zu

1. Melden Sie sich beim Portal [Custom Translator](https://portal.customtranslator.azure.ai/) an.
2. Klicken Sie im Custom Translator-Portal in der Seitenleiste auf das Zahnradsymbol.

    ![Verknüpfung zu Einstellungen](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Zuordnen des Microsoft Translator-Abonnements

Zum Trainieren oder Bereitstellen von Modellen benötigen Sie den Abonnementschlüssel der Microsoft-Textübersetzungs-API, der Ihrem Arbeitsbereich zugeordnet ist.

Wenn Sie über kein Abonnement verfügen, gehen Sie folgendermaßen vor:

1. Abonnieren Sie die Microsoft-Textübersetzungs-API. In diesem Artikel finden Sie die genaue Anleitung.
2. Notieren Sie sich den Schlüssel Ihres Microsoft Translator-Abonnements. „Key1“ oder „Key2“ sind zulässig.
3. Navigieren Sie zurück zum Custom Translator-Portal.

### <a name="add-existing-key"></a>Hinzufügen eines vorhandenen Schlüssels

1.  Navigieren Sie zur Seite „Einstellungen“ für Ihren Arbeitsbereich.
2.  Klicken Sie auf „Schlüssel hinzufügen“.

    ![Hinzufügen eines Abonnementschlüssels](media/how-to/how-to-add-subscription-key.png)

3. Geben Sie im Dialogfeld den Schlüssel für Ihr Microsoft Translator-Abonnement ein, und klicken Sie dann auf die Schaltfläche „Hinzufügen“.

    ![Hinzufügen eines Abonnementschlüssels](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Wenn Sie einen Schlüssel hinzugefügt haben, können Sie den Schlüssel jederzeit ändern oder löschen.

    ![Abonnementschlüssel nach dem Hinzufügen](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Verwalten Ihres Arbeitsbereichs

Ein Arbeitsbereich ist ein Bereich zum Erstellen und Entwickeln Ihres benutzerdefinierten Übersetzungssystems. Er kann mehrere Projekte, Modelle und Dokumente enthalten.

Wenn Sie unterschiedliche Teilbereiche Ihrer Arbeit für verschiedene Personen freigegeben möchten, ist es sinnvoll, mehrerer Arbeitsbereiche zu erstellen.

## <a name="create-a-new-workspace"></a>Erstellen eines neuen Arbeitsbereichs

1.  Navigieren Sie im Arbeitsbereich zur Seite „Einstellungen“.
2.  Klicken Sie im Abschnitt „Neuen Arbeitsbereich erstellen“ auf die Schaltfläche „Neuer Arbeitsbereich“.

    ![Erstellen eines neuen Arbeitsbereichs](media/how-to/create-new-workspace.png)

4.  Geben Sie im Dialogfeld den Namen des neuen Arbeitsbereichs ein.
5.  Klicken Sie auf „Erstellen“.

    ![Dialogfeld „Neuen Arbeitsbereich erstellen“](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Freigeben Ihres Arbeitsbereichs

In Custom Translator können Sie Ihren Arbeitsbereich für andere freigeben, z.B. wenn verschiedene Teilbereiche Ihrer Arbeit mit unterschiedlichen Personen geteilt werden sollen.

1.  Navigieren Sie im Arbeitsbereich zur Seite „Einstellungen“.
2.  Klicken Sie im Abschnitt „Freigabeeinstellungen“ auf die Schaltfläche „Freigeben“.

    ![Freigeben des Arbeitsbereichs](media/how-to/share-workspace.png)

3.  Geben Sie im Dialogfeld eine durch Trennzeichen getrennte Liste der E-Mail-Adressen ein, für die Sie diesen Arbeitsbereich freigeben möchten. Achten Sie darauf, dass Sie die E-Mail-Adressen angeben, mit denen sich die Benutzer bei Custom Translator anmelden. Wählen Sie dann die gewünschte Stufe für die Freigabeberechtigung aus.

4.  Wenn Ihr Arbeitsbereich noch den Standardnamen „Mein Arbeitsbereich“ hat, müssen Sie diesen vor der Freigabe ändern.
5.  Klicken Sie auf „Speichern“.

## <a name="sharing-permissions"></a>Freigabeberechtigungen

1.  **Leser**: Benutzer mit dieser Freigabeberechtigung können alle Informationen im Arbeitsbereich anzeigen.

2.  **Bearbeiter**: Benutzer mit dieser Freigabeberechtigung können im Arbeitsbereich Dokumente hinzufügen, Modelle trainieren sowie Dokumente und Projekte löschen. Außerdem haben sie die Möglichkeit, einen Abonnementschlüssel hinzuzufügen. Allerdings können diese Benutzer nicht ändern, für wen der Arbeitsbereich freigegeben ist, den Arbeitsbereich löschen oder den Arbeitsbereichsnamen ändern.

3.  **Besitzer**: Benutzer mit dieser Freigabeberechtigung verfügen über uneingeschränkte Berechtigungen für den Arbeitsbereich.

## <a name="change-sharing-permission"></a>Ändern der Freigabeberechtigung

Wenn ein Arbeitsbereich freigegeben wurde, zeigt der Abschnitt „Freigabeeinstellungen“ alle E-Mail-Adressen an, für die das gilt. Sie können die aktuelle Freigabeberechtigung jeder E-Mail-Adresse ändern, wenn Sie für den Arbeitsbereich über die Freigabeberechtigung „Besitzer“ verfügen.

1.  Im Abschnitt „Freigabeeinstellungen“ zeigt ein Dropdownmenü die aktuelle Berechtigungsstufe jeder E-Mail-Adresse an.

2.  Klicken Sie auf das Dropdownmenü, und wählen Sie die neue Berechtigungsstufe aus, die Sie der jeweiligen E-Mail-Adresse zuweisen möchten.

    ![Einstellungen für die Freigabeberechtigung](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Migrieren Ihres Arbeitsbereichs und Projekts](how-to-migrate.md) aus [Microsoft Translator Hub](https://hub.microsofttranslator.com).
