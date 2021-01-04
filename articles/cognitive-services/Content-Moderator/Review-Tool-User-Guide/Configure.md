---
title: Konfigurieren der Einstellungen des Prüfungstools – Content Moderator
titleSuffix: Azure Cognitive Services
description: Sie können Ihr Team, Ihre Tags, Konnektoren, Workflows und Anmeldeinformationen mit dem Prüfungstool für Content Moderator konfigurieren oder abrufen.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912020"
---
# <a name="configure-the-review-tool"></a>Konfigurieren des Prüfungstools

Das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) verfügt über mehrere wichtige Features, auf die Sie über das Menü **Einstellungen** auf dem Dashboard zugreifen können.

![Einstellungsmenü des Prüfungstools in Content Moderator](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Verwalten von Teams und untergeordneten Teams

Auf der Registerkarte **Team** können Sie Ihr Team und untergeordnete Teams verwalten, d. h. Gruppen von Benutzern, die benachrichtigt werden können, wenn bestimmte [Prüfungen durch Personen](../review-api.md#reviews) gestartet werden. Sie können nur ein übergeordnetes Team verwenden (das Sie bei der Anmeldung mit dem Prüfungstool erstellen), aber Sie können mehrere untergeordnete Teams erstellen. Der Teamadministrator kann Mitglieder einladen, ihre Berechtigungen festlegen und sie verschiedenen untergeordneten Teams zuordnen.

![Teameinstellungen des Prüfungstools](images/settings-2-team.png)

Untergeordnete Teams sind nützlich für die Erstellung von Eskalationsteams oder Teams, die sich mit der Überprüfung bestimmter Inhaltskategorien befassen. So können Sie z. B. nicht jugendfreie Inhalte zur weiteren Überprüfung an ein separates Team senden.

In diesem Abschnitt wird erklärt, wie Sie untergeordnete Teams erstellen und Überprüfungen schnell und einfach zuweisen können. Sie können jedoch [Workflows](workflows.md) verwenden, um Überprüfungen anhand bestimmter Kriterien zuzuweisen.

### <a name="create-a-subteam"></a>Erstellen eines untergeordnetes Teams

Wechseln Sie zum Abschnitt **Untergeordnete Teams** , und klicken Sie auf **Untergeordnetes Team hinzufügen**. Geben Sie den Namen Ihres untergeordneten Teams im Dialogfeld ein, und klicken Sie auf **Speichern**.

![Name des untergeordneten Teams](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Teamkollegen einladen

Sie können keine Person einem untergeordneten Team zuweisen, wenn diese Person nicht bereits Mitglied des Standardteams ist, daher müssen Sie zunächst dem Standardteam Prüfer hinzufügen. Klicken Sie auf der Registerkarte **Team** auf **Einladen**.

![Benutzer einladen](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Teamkollegen zu untergeordneten Teams zuweisen

Klicken Sie auf **Mitglied hinzufügen** , um Mitglieder Ihres Standardteam zu einem oder mehreren untergeordneten Teams hinzuzufügen. Sie können nur vorhandene Benutzer zu einem untergeordneten Team hinzufügen. Um neue Benutzer hinzuzufügen, die noch nicht ins Prüfungstool aufgenommen wurden, laden Sie sie ein, indem Sie auf der Seite mit den Teameinstellungen auf „Einladung“ klicken.

![Mitglieder zu untergeordneten Teams hinzufügen](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Überprüfungen zu untergeordneten Teams zuweisen

Sobald Sie Ihre untergeordnete Teams erstellt und Mitglieder zugewiesen haben, können Sie damit beginnen, diesen untergeordneten Teams [Inhaltsüberprüfungen](../review-api.md#reviews) zuzuweisen. Dies erfolgt auf der Registerkarte **Überprüfung** der Website.
Um Inhalte einem untergeordneten Team zuzuweisen, klicken Sie auf die Auslassungspunkte in der rechten oberen Ecke. Dann wählen Sie **Verschieben nach** und anschließend ein untergeordnetes Team aus.

![Bildüberprüfung einem untergeordneten Team zuweisen](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Zwischen untergeordneten Teams wechseln

Wenn Sie Mitglied mehrerer untergeordneter Teams sind, können Sie zwischen diesen untergeordneten Teams wechseln, um zu ändern, welche Inhaltsüberprüfungen für Sie angezeigt werden. Wählen Sie auf der Registerkarte **Überprüfung** das Dropdownmenü **Standard** und dann **Untergeordnetes Team auswählen** aus. Sie können die Inhaltsüberprüfungen für verschiedene untergeordnete Teams anzeigen, jedoch nur für diejenigen, in denen Sie Mitglied sind.

![Zwischen untergeordneten Teams wechseln](images/3-review-image-subteam-2.png)

## <a name="tags"></a>`Tags`

Auf der Registerkarte **Tags** können Sie zusätzlich zu den beiden Standardmoderationstags – &mdash;**isadult** ( **a** ) and **isracy** ( **r** ) – benutzerdefinierte Moderationstags definieren. Wenn Sie ein benutzerdefiniertes Tag erstellen, wird es in Überprüfungen neben den Standardtags verfügbar. Sie können ändern, welche Tags in Überprüfungen angezeigt werden, indem Sie deren Sichtbarkeitseinstellungen ändern.

![Ansicht „Tags“, einschließlich der Kontrollkästchen „Ist sichtbar“.](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Benutzerdefinierten Tags erstellen

Um ein neues Tag zu erstellen, müssen Sie in den entsprechenden Feldern einen kurzen Code, einen Namen und eine Beschreibung eingeben.

- **Kurzer Code** : Geben Sie einen zweistelligen Code für Ihr Tag ein. Beispiel: **cb**
- **Name** : Geben Sie einen kurzen und aussagekräftigen Tagnamen in Kleinbuchstaben ohne Leerzeichen ein. Beispiel: **isbullying**.
- **Beschreibung** : (Optional) Geben Sie eine Beschreibung der Art des Inhalts ein, auf den Ihr Tag ausgerichtet ist. Beispiel: **Darstellungen oder Fälle von Cybermobbing**.

Klicken Sie auf **Hinzufügen** , um ein Tag hinzuzufügen, und klicken Sie auf **Speichern** , wenn Sie die Erstellung von Tags abgeschlossen haben.

![Prüfungstool – Dialogfeld zum Erstellen neuer Tags](images/settings-3-tags.png)

### <a name="delete-tags"></a>Tags löschen

Sie können benutzerdefinierte Tags löschen, indem Sie das Papierkorbsymbol neben ihren Einträgen in der Liste der Tags auswählen, aber Sie können die Standardtags nicht löschen.

## <a name="connectors"></a>Connectors

Auf der Registerkarte **Connectors** können Sie Ihre Connectors verwalten, d. h. dienstabhängige Plug-Ins, die Inhalte als Teil von [Workflows](../review-api.md#workflows) für Inhalte auf unterschiedliche Weise verarbeiten können.

Der Standardconnector beim Erstellen eines Workflows ist der Content Moderator-Connector, der Inhalte als **nicht jugendfrei** oder **freizügig** markieren sowie Obszönitäten finden kann usw. Sie können jedoch auch andere Connectors verwenden, die hier aufgelistet sind, solange Sie über Zugangsdaten für die jeweiligen Dienste verfügen (um z. B. den Gesichtserkennungsconnector zu verwenden, müssen Sie einen Abonnementschlüssel für die [Gesichtserkennung](../../face/overview.md) erhalten).

Das [Prüfungstool](./human-in-the-loop.md) umfasst die folgenden Connectors:

- Emotion
- Gesicht
- PhotoDNA-Clouddienst
- Textanalyse

### <a name="add-a-connector"></a>Hinzufügen eines Connectors

Um einen Connector hinzuzufügen (und ihn für die Verwendung in [Workflows](../review-api.md#workflows) für Inhalte verfügbar zu machen), wählen Sie die entsprechende Schaltfläche **Verbinden** aus. Geben Sie im nächsten Dialogfeld Ihren Abonnementschlüssel für diesen Dienst ein. Wenn Sie fertig sind, sollte Ihr neuer Connector oben auf der Seite angezeigt werden.

![Konnektoreinstellungen in Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Workflows

Auf der Registerkarte **Workflows** können Sie Ihre [Workflows](../review-api.md#workflows) verwalten. Workflows sind cloudbasierte Filter für Inhalte, und sie arbeiten mit Connectors zusammen, um Inhalte auf unterschiedliche Weise zu sortieren und geeignete Maßnahmen zu ergreifen. Hier können Sie Ihre Workflows definieren, bearbeiten und testen. Eine Anleitung dazu finden Sie unter [Definieren und Verwenden von Workflows](Workflows.md).

![Workfloweinstellungen in Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Anmeldeinformationen

Die Registerkarte **Anmeldeinformationen** bietet schnellen Zugriff auf Ihren Content Moderator-Abonnementschlüssel, den Sie benötigen, um von einem REST-Anruf oder Client-SDK aus auf einen der Moderationsdienste zuzugreifen.

![Anmeldeinformationen in Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Verwenden von externen Anmeldeinformationen für Workflows

Das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) generiert bei der Registrierung einen kostenlosen Testschlüssel für die Azure Content Moderator-Dienste, aber Sie können ihn auch so konfigurieren, dass er einen vorhandenen Schlüssel aus Ihrem Azure-Konto verwendet. Dies wird für umfangreichere Szenarien empfohlen, da kostenlose Testschlüssel strenge Nutzungsbeschränkungen aufweisen ([Preise und Einschränkungen](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Wenn Sie eine [Content Moderator-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) in Azure erstellt haben, navigieren Sie zu ihr im Azure-Portal, und wählen Sie das Blatt **Schlüssel** aus. Kopieren Sie einen Ihrer Schlüssel.

![Content Moderator-Schlüssel im Azure-Portal](images/credentials-azure-portal-keys.PNG)

Wechseln Sie auf der Registerkarte **Anmeldeinformationen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com) zum Bereich **Workfloweinstellungen** , wählen Sie **Bearbeiten** aus, und fügen Sie Ihren Schlüssel in das Feld **Ocp-Apim-Subscription-Key** ein. Jetzt verwenden Workflows, die die APIs der Moderation aufrufen, Ihre Azure-Anmeldeinformationen.

> [!NOTE]
> Die beiden anderen Felder im Bereich **Workfloweinstellungen** sind für Listen von benutzerdefinierten Begriffen und Bildern vorgesehen. Weitere Informationen hierzu finden Sie in den Anleitungen [Benutzerdefinierte Begriffe](../try-terms-list-api.md) oder [Benutzerdefinierte Bilder](../try-image-list-api.md).

### <a name="use-your-azure-account-with-the-review-apis"></a>Verwenden Ihres Azure-Kontos mit den Überprüfungs-APIs

Um Ihren Azure-Schlüssel mit den Überprüfungs-APIs zu verwenden, müssen Sie Ihre Ressourcen-ID abrufen. Wechseln Sie zu Ihrer Content Moderator-Ressource im Azure-Portal, und wählen Sie das Blatt **Eigenschaften** aus. Kopieren Sie den Wert der Ressourcen-ID, und fügen Sie ihn in das Feld **Ressourcen-ID(s) in der Whitelist** der Registerkarte **Anmeldeinformationen** des Prüfungstools ein.

![Ressourcen-ID für Content Moderator im Azure-Portal](images/credentials-azure-portal-resourceid.PNG)

Wenn Sie Ihren Abonnementschlüssel an beiden Stellen eingegeben haben, wird der Testschlüssel, der mit Ihrem Prüfungstool-Konto bereitgestellt wird, nicht verwendet, bleibt aber verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie den Schnellstart des [Prüfungstools](../quick-start.md), um die Verwendung des Prüfungstools in Szenarien zur Inhaltsmoderation zu starten.