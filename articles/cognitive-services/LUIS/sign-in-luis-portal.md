---
title: Anmelden beim LUIS-Portal
description: Wenn Sie ein neuer Benutzer sind, der sich beim LUIS-Portal anmeldet, wird der Anmeldevorgang je nach Ihrem aktuellen Benutzerkonto leicht abweichen.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: b8382b76496976054ebb452e39866765d986ccbb
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368176"
---
# <a name="sign-in-to-luis-portal"></a>Anmelden beim LUIS-Portal

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Verwenden Sie diesen Artikel für die ersten Schritte mit dem LUIS-Portal, und erstellen Sie eine Erstellungsressource. Nachdem Sie die Schritte in diesem Artikel abgeschlossen haben, können Sie LUIS-Apps erstellen und veröffentlichen.

## <a name="access-the-portal"></a>Zugriff auf das Portal


1. Wechseln Sie für die ersten Schritte mit LUIS zum [LUIS-Portal](https://www.luis.ai). Wenn Sie noch nicht über ein Abonnement verfügen, werden Sie aufgefordert, ein [kostenloses Konto](https://azure.microsoft.com//free/cognitive-services/) zu erstellen und zum Portal zurückzukehren.
2. Aktualisieren Sie die Seite, um sie mit Ihrem neu erstellten Abonnement zu aktualisieren.
3. Wählen Sie in der Dropdownliste Ihr Abonnement aus.

    > [!div class="mx-imgBorder"]
    > ![„Abonnements“ auswählen](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Wenn sich Ihr Abonnement unter einem anderen Mandanten befindet, können Sie den Mandanten nicht über das vorhandene Fenster wechseln. Sie können den Mandanten wechseln, indem Sie dieses Fenster schließen und in der oberen Leiste den ganz rechten Avatar mit Ihren Initialen auswählen. Klicken Sie oben auf **Andere Erstellungsressource auswählen**, um das Fenster erneut zu öffnen.

    > [!div class="mx-imgBorder"]
    > ![Wechseln von Verzeichnissen](./media/migrate-authoring-key/switch-directories.png)

5. Wenn Ihrem Abonnement eine vorhandene LUIS-Erstellungsressource zugeordnet ist, wählen Sie diese aus der Dropdownliste aus. Sie können alle Anwendungen anzeigen, die unter dieser Erstellungsressource erstellt werden.
6. Wenn dies nicht der Fall ist, dann klicken Sie auf **Neue Erstellungsressource erstellen** am Ende dieses modalen Fensters.
7.  Geben Sie beim Erstellen einer neuen Erstellungsressource die folgenden Informationen an:

    > [!div class="mx-imgBorder"]
    > ![Neue Ressource erstellen](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Mandantenname**: Der Mandant, dem Ihr Azure-Abonnement zugeordnet ist. Sie können die Mandanten nicht über das bestehende Fenster wechseln. Sie können den Mandanten wechseln, indem Sie dieses Fenster schließen und den Avatar mit Ihren Initialen oben rechts auf dem Bildschirm auswählen. Wählen Sie oben die Option **Andere Erstellungsressource auswählen** aus, um das Fenster erneut zu öffnen.
    * **Azure-Ressourcengruppenname**: Ein benutzerdefinierter Ressourcengruppenname, den Sie in Ihrem Abonnement auswählen. Mit Ressourcengruppen können Sie Azure-Ressourcen für den Zugriff und die Verwaltung gruppieren. Wenn Sie derzeit nicht über eine Ressourcengruppe in Ihrem Abonnement verfügen, können Sie im LUIS-Portal keine Ressourcengruppe erstellen. Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup), um eine zu erstellen, und wechseln Sie dann zu LUIS, um den Anmeldevorgang fortzusetzen.
    * **Azure-Ressourcenname**: Ein von Ihnen gewählter benutzerdefinierter Name, der als Teil der URL für Ihre Erstellungstransaktionen verwendet wird. Ihr Ressourcenname darf nur alphanumerische Zeichen und „`-`“ enthalten und darf nicht mit „`-`“ beginnen oder enden. Wenn andere Symbole im Namen enthalten sind, wird beim Erstellen einer Ressource ein Fehler auftreten.
    * **Standort**: Wählen Sie aus, ob Sie Ihre Anwendungen an einem der [drei Erstellungsstandorte](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) erstellen möchten, die derzeit von LUIS unterstützt werden, einschließlich: „USA, Westen“, „Europa, Westen“ und „Australien, Osten“.
    * **Tarif**: Standardmäßig ist der Erstellungstarif F0 ausgewählt, da dies der empfohlene Tarif ist. Erstellen Sie einen [kundenseitig verwalteten Schlüssel](https://docs.microsoft.com/azure/cognitive-services/luis/luis-encryption-of-data-at-rest#customer-managed-keys-for-language-understanding) über das Azure-Portal, wenn Sie nach einer zusätzlichen Ebene der Sicherheit suchen.
8. Jetzt haben Sie sich erfolgreich bei LUIS angemeldet. Sie können jetzt damit beginnen, Anwendungen zu erstellen.

## <a name="troubleshooting"></a>Problembehandlung

* Achten Sie beim Erstellen einer neuen Ressource darauf, dass der Ressourcenname nur alphanumerische Zeichen und „-“, enthält und weder mit „-“ beginnen oder enden darf. Andernfalls schlägt der Vorgang fehl.
* Stellen Sie sicher, dass Sie über die [entsprechenden Berechtigungen für Ihr Abonnement verfügen, um eine Azure-Ressource](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu erstellen. Wenn Sie nicht über die entsprechenden Berechtigungen verfügen, wenden Sie sich an den Administrator Ihres Abonnements, um ausreichende Berechtigungen zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [eine neue App starten](luis-how-to-start-new-app.md).
