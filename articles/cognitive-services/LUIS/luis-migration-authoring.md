---
title: Migrieren zu einem Schlüssel einer Azure-Erstellungsressource
titleSuffix: Azure Cognitive Services
description: Dieser Artikel beschreibt die Migration der Erstellungsauthentifizierung von Language Understanding (LUIS) von einem E-Mail-Konto in eine Azure-Ressource.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/07/2020
ms.openlocfilehash: 243c9834aa256e26d620c00ac0fa7a262919aabd
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762691"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrieren zu einem Schlüssel einer Azure-Erstellungsressource

> [!IMPORTANT]
>  Ab dem 3. Dezember müssen bestehende LUIS-Benutzer den Migrationsprozess abschließen, um weiterhin LUIS-Anwendungen erstellen zu können.

Die Erstellungsauthentifizierung von Language Understanding (LUIS) wurde von einem E-Mail-Konto in eine Azure-Ressource geändert. Verwenden Sie diesen Artikel, um zu erfahren, wie Sie Ihr Konto migrieren können, falls es noch nicht migriert ist.  


## <a name="what-is-migration"></a>Was ist Migration?

Migration ist der Prozess des Änderns der Erstellungsauthentifizierung von einem E-Mail-Konto in eine Azure-Ressource. Ihr Konto wird nach der Migration mit einem Azure-Abonnement und einer Azure-Erstellungsressource verknüpft. *Alle LUIS-Benutzer (App-Besitzer und Projektmitarbeiter) müssen letztendlich migriert werden.*

Eine Migration muss über das [LUIS-Portal](https://www.luis.ai) erfolgen. Wenn Sie die Erstellungsschlüssel beispielsweise mithilfe der LUIS-Befehlszeilenschnittstelle erstellen, müssen Sie den Migrationsvorgang trotzdem im LUIS-Portal abschließen. Nach einer Migration können Sie weiterhin Mitautoren bei Ihren Anwendungen haben, doch diese werden auf der Azure-Ressourcenebene hinzugefügt anstatt auf Anwendungsebene.

> [!Note]
> Vor der Migration werden Mitautoren auf der LUIS-App-Ebene als _Projektmitarbeiter_ bezeichnet. Nach der Migration wird die Azure-Rolle _Mitwirkender_ für dieselbe Funktionalität auf der Azure-Ressourcenebene verwendet.

## <a name="notes-before-you-migrate"></a>Vor der Migration zu beachten

* Die Migration kann nicht rückgängig gemacht werden.
* Wenn Sie sich bei mehr als einem [LUIS-Portal für eine Region](./luis-reference-regions.md#luis-authoring-regions) angemeldet haben, werden Sie aufgefordert, in mehreren Regionen gleichzeitig zu migrieren.
* Anwendungen werden automatisch zusammen mit Ihnen migriert, wenn Sie der Besitzer der Anwendung sind.
* Der Besitzer kann keine Teilmenge zu migrierender Apps auswählen, und der Prozess kann nicht rückgängig gemacht werden.
* Anwendungen verschwinden aus dem Konto des Projektmitarbeiters, nachdem der Besitzer migriert wurde.
* Besitzer werden aufgefordert, E-Mails an Projektmitarbeiter zu senden, um sie über die Migration zu informieren.
* Anwendungen werden nicht zusammen mit Ihnen migriert, wenn Sie ein Projektmitarbeiter der Anwendung sind. Die Projektmitarbeiter werden jedoch dazu aufgefordert, die erforderlichen Apps zu exportieren.
* Es gibt keine Möglichkeit für einen Besitzer, herauszufinden, ob Projektmitarbeiter migriert wurden.
* Eine Migration verschiebt Projektmitarbeiter nicht automatisch in die Azure-Erstellungsressource oder fügt sie zu dieser hinzu. Der App-Besitzer ist derjenige, der diesen Schritt nach der Migration ausführen muss. Dieser Schritt erfordert [Berechtigungen für die Azure-Erstellungsressource](./luis-how-to-collaborate.md).
* Nachdem Projektmitarbeiter der Azure-Ressource zugewiesen wurden, müssen sie eine Migration durchführen, bevor sie auf Anwendungen zugreifen können. Andernfalls haben sie keinen Zugriff zum Erstellen der Anwendungen.
* Ein migrierter Benutzer kann nicht als Projektmitarbeiter der Anwendung hinzugefügt werden.


> [!Note]
> Wenn Sie eine Vorhersageruntimeressource erstellen müssen, gibt es [einen gesonderten Prozess](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal), um diese zu erstellen.

## <a name="migration-prerequisites"></a>Voraussetzungen für die Migration

* Sie müssen einem gültigen Azure-Abonnement zugeordnet sein. Bitten Sie Ihren Mandantenadministrator, Sie dem Abonnement hinzuzufügen, oder [registrieren Sie sich für ein kostenloses Abonnement](https://azure.microsoft.com/free/cognitive-services).
* Sie müssen eine LUIS Azure-Erstellungsressource im LUIS-Portal oder im [Azure-Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) erstellen. Das Erstellen einer Erstellungsressource im LUIS-Portal ist Teil des im nächsten Abschnitt beschriebenen Migrationsprozesses.
* Wenn Sie als Projektmitarbeiter an Anwendungen mitarbeiten, werden die Anwendungen nicht automatisch migriert. Sie werden aufgefordert, diese Apps zu exportieren, während Sie den Migrationsfluss durchlaufen. Sie können auch die [Export-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) verwenden. Sie können die App nach der Migration wieder zurück in LUIS importieren. Beim Importvorgang wird eine neue App mit einer neuen App-ID erstellt, deren Besitzer Sie sind.
* Wenn Sie der Besitzer der Anwendung sind, müssen Sie Ihre Apps nicht exportieren, da sie automatisch migriert werden. Eine E-Mail-Vorlage mit einer Liste aller Projektmitarbeiter für die einzelnen Anwendung wird zur Verfügung gestellt, sodass sie über den Migrationsprozess benachrichtigt werden können.

> [!Note]
> Das Erstellen Ihrer LUIS-App ist kostenlos, wie der F0-Tarif zeigt. Hier finden Sie [weitere Informationen zu Tarifen](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Schritte bei der Migration

1. Wenn Sie sich beim [LUIS-Portal](https://www.luis.ai) anmelden, wird ein Azure-Migrationsfenster mit den Schritten für die Migration geöffnet. Wenn Sie es ablehnen, können Sie nicht mit der Erstellung Ihrer LUIS-Anwendungen fortfahren, und die einzige angezeigte Aktion besteht darin, mit der Migration fortzufahren.

    > [!div class="mx-imgBorder"]
    > ![Migrationsfenster: Einführung](./media/migrate-authoring-key/notify-azure-migration.png)

2. Wenn Sie bei einer Ihrer Apps über Projektmitarbeiter verfügen, wird eine Liste mit den Namen der Anwendungen angezeigt, die sich in Ihrem Besitz befinden, zusammen mit der Erstellungsregion und den E-Mails der Projektmitarbeiter zu den einzelnen Anwendungen. Wir empfehlen Ihnen, Ihre Projektmitarbeiter per E-Mail über die Migration zu informieren, indem Sie auf die Symbolschaltfläche **Senden** links neben dem Namen der Anwendung klicken.
Ein `*`-Symbol wird neben dem Namen der Anwendung angezeigt, wenn ein Projektmitarbeiter Ihrer Anwendung eine Vorhersageressource zugewiesen hat. Nach der Migration werden diesen Apps weiterhin diese Vorhersageressourcen zugewiesen, auch wenn die Projektmitarbeiter keinen Zugriff auf Ihre Anwendungen haben. Diese Zuweisung wird jedoch aufgehoben, wenn der Besitzer der Vorhersageressource [die Schlüssel](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) über das Azure-Portal neu generiert hat.  

   > [!div class="mx-imgBorder"]
   > ![Benachrichtigen der Projektmitarbeiter](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Für jeden Projektmitarbeiter und jede App wird die Standard-E-Mail-Anwendung mit einer einfach formatierten E-Mail geöffnet. Sie können die E-Mail vor dem Senden bearbeiten. Die E-Mail-Vorlage enthält die genaue App-ID und den App-Namen.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Nachdem Sie Ihr Konto zu Azure migriert haben, sind Ihre Apps nicht mehr für Projektmitarbeiter verfügbar.

3. Wenn Sie an einer App mitarbeiten, wird eine Liste der mit Ihnen geteilten Anwendungsnamen sowie die Erstellungsregion und die E-Mails des Besitzers der einzelnen Anwendungen angezeigt. Es wird empfohlen, eine Kopie der Apps zu exportieren, indem Sie auf die Schaltfläche „Exportieren“ links neben dem Namen der Anwendung klicken. Sie können diese Apps nach der Migration wieder zurück importieren, da sie nicht automatisch mit Ihnen migriert werden.
Ein `*`-Symbol wird neben dem Anwendungsnamen angezeigt, wenn Sie einer Anwendung eine Vorhersageressource zugewiesen haben. Nach der Migration wird Ihre Vorhersageressource weiterhin diesen Anwendungen zugewiesen, auch wenn Sie keinen Zugriff mehr auf den Ersteller dieser Apps haben. Wenn Sie die Zuweisung zwischen Ihrer Vorhersageressource und der Anwendung aufheben möchten, müssen Sie zum Azure-Portal wechseln und [die Schlüssel neu generieren](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exportieren Sie Ihre Anwendungen.](./media/migrate-authoring-key/migration-export-apps.png)


4. Im Fenster für die Migration von Regionen werden Sie aufgefordert, Ihre Anwendungen zu einer Azure-Ressource in derselben Region zu migrieren, in der sie erstellt wurden. LUIS verfügt über drei Erstellungsregionen [und Portale](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions). Das Fenster zeigt die Regionen an, in denen Ihre eigenen Anwendungen erstellt wurden. Die angezeigten Migrationsregionen können je nach dem von Ihnen verwendeten regionalen Portal und den von Ihnen erstellten Apps unterschiedlich sein. 

   > [!div class="mx-imgBorder"]
   > ![Migration für mehrere Regionen.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Wählen Sie für jede Region aus, ob Sie eine neue LUIS-Erstellungsressource erstellen oder mithilfe der Schaltflächen zu einer bestehenden Ressource migrieren möchten.

   > [!div class="mx-imgBorder"]
   > ![Auswahl der Erstellung einer Erstellungsressource oder einer vorhandenen Erstellungsressource](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Geben Sie die folgenden Informationen ein:

   * **Mandantenname**: der Mandant, dem Ihr Azure-Abonnement zugeordnet ist. Standardmäßig ist dies auf den Mandanten festgelegt, den Sie derzeit verwenden. Sie können den Mandanten wechseln, indem Sie dieses Fenster schließen und den Avatar mit Ihren Initialen oben rechts auf dem Bildschirm auswählen. Klicken Sie auf **Migrieren zu Azure**, um das Fenster wieder zu öffnen.
   * **Name des Azure-Abonnements**: das Abonnement, das der Ressource zugeordnet wird. Wenn Sie über mehr als ein Abonnement verfügen, das zu Ihrem Mandanten gehört, wählen Sie das gewünschte Abonnement aus der Dropdownliste aus.
   * **Name der Erstellungsressource**: ein von Ihnen gewählter benutzerdefinierter Name. Dieser Name wird als Bestandteil der URL für Ihre Erstellungs- und Vorhersageendpunktabfragen verwendet. Wenn Sie eine neue Erstellungsressource erstellen, beachten Sie, dass der Ressourcenname nur alphanumerische Zeichen und `-` enthalten darf sowie nicht mit `-` beginnen oder enden darf. Wenn andere Symbole im Namen enthalten sind, tritt bei der Ressourcenerstellung und -migration ein Fehler auf.
   * **Name der Azure-Ressourcengruppe**: ein benutzerdefinierter Ressourcengruppenname, den Sie aus der Dropdownliste auswählen. Mit Ressourcengruppen können Sie Azure-Ressourcen für den Zugriff und die Verwaltung gruppieren. Wenn Sie derzeit nicht über eine Ressourcengruppe in Ihrem Abonnement verfügen, können Sie im LUIS-Portal keine Ressourcengruppe erstellen. Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup), um eine zu erstellen, und wechseln Sie dann zu LUIS, um den Anmeldevorgang fortzusetzen.

6. Nachdem Sie in allen Regionen erfolgreich migriert sind, klicken Sie auf „Fertig stellen“. Sie haben jetzt Zugriff auf Ihre Anwendungen. Sie können weiterhin alle Ihre Anwendungen in allen Regionen innerhalb des Portals erstellen und verwalten.


## <a name="using-apps-after-migration"></a>Verwenden von Apps nach der Migration

Nach dem Migrationsprozess sind alle Ihre LUIS-Apps, deren Besitzer Sie sind, einer einzigen LUIS-Erstellungsressource zugewiesen.
In der Liste **Meine Apps** werden die Apps angezeigt, die zur neuen Erstellungsressource migriert wurden. Bevor Sie auf Ihre Apps zugreifen, wählen Sie **Andere Erstellungsressource auswählen** aus, um das Abonnement und die Erstellungsressource zum Anzeigen der Apps auszuwählen, die erstellt werden können.

> [!div class="mx-imgBorder"]
> ![Auswählen einer Abonnement- und Erstellungsressource](./media/migrate-authoring-key/select-sub-and-resource.png)


Wenn Sie Ihre Apps programmgesteuert bearbeiten möchten, benötigen Sie die Erstellungsschlüsselwerte. Diese Werte werden angezeigt, indem Sie im LUIS-Portal oben auf dem Bildschirm auf **Verwalten** klicken und dann **Azure-Ressourcen** auswählen. Sie sind auch im Azure-Portal auf der Seite **Schlüssel und Endpunkte** der Ressource verfügbar. Sie können auch weitere Erstellungsressourcen erstellen und diese auf derselben Seite zuweisen.

## <a name="adding-contributors-to-authoring-resources"></a>Hinzufügen von Projektmitarbeitern zu Erstellungsressourcen

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Erfahren Sie, wie Sie in Ihrer Erstellungsressource [Projektmitarbeiter hinzufügen](luis-how-to-collaborate.md). Projektmitarbeiter erhalten Zugriff auf alle Anwendungen unter dieser Ressource.

Sie können einer Erstellungsressource Mitwirkende hinzufügen, indem Sie im Azure-Portal zur Seite **Zugriffssteuerung (IAM)** für diese Ressource wechseln. Weitere Informationen finden Sie unter [Hinzufügen von Mitwirkenden zu Ihrer App](luis-how-to-collaborate.md).

> [!Note]
> Wenn der Besitzer der LUIS-App migriert ist und den Projektmitarbeiter als Mitwirkenden an der Azure-Ressource hinzugefügt hat, hat der Mitwirkende bis zur eigenen Migration noch keinen Zugriff auf die App.


## <a name="troubleshooting-the-migration-process"></a>Problembehandlung des Migrationsprozesses

Wenn Sie Ihr Azure-Abonnement nicht in der Dropdownliste finden:
* Stellen Sie sicher, dass Sie ein gültiges Azure-Abonnement besitzen, das autorisiert ist, Cognitive Services-Ressourcen zu erstellen. Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com), und überprüfen Sie den Status des Abonnements. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/cognitive-services/).
* Stellen Sie sicher, dass Sie sich im richtigen Mandanten befinden, der Ihrem gültigen Abonnement zugeordnet ist. Sie können den Mandanten wechseln, indem Sie den Avatar mit Ihren Initialen oben rechts auf dem Bildschirm auswählen.

  > [!div class="mx-imgBorder"]
  > ![Seite zum Wechseln von Verzeichnissen](./media/migrate-authoring-key/switch-directories.png)

Wenn Sie über eine Erstellungsressource verfügen, diese jedoch nicht finden können, wenn Sie die Option **Vorhandene Erstellungsressource verwenden** auswählen:
* Ihre Ressource wurde wahrscheinlich in einer anderen Region erstellt als der, zu der Sie zu migrieren versuchen.
* Erstellen Sie eine neue Ressource stattdessen über das LUIS-Portal.

Wenn Sie die Option **Neue Erstellungsressource erstellen** auswählen und bei der Migration die Fehlermeldung „Fehler beim Abrufen der Azure-Informationen des Benutzers. Versuchen Sie es später noch mal.“ angezeigt wird:
* Ihr Abonnement verfügt pro Region und Abonnement über 10 oder mehr Erstellungsressourcen. Sollte dies der Fall sein, können Sie keine neue Erstellungsressource erstellen.
* Führen Sie die Migration durch, indem Sie **Vorhandene Erstellungsressource verwenden** und dann eine der vorhandenen Ressourcen auswählen, die sich unter Ihrem Abonnement befinden.

## <a name="create-new-support-request"></a>Erstellen einer neuen Supportanfrage

Wenn Sie Probleme bei der Migration haben, die nicht im Abschnitt zur Problembehandlung behandelt werden, erstellen Sie [ein Supportthema](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und geben Sie die nachfolgenden Informationen in die folgenden Felder ein:

   * **Problemtyp:** Technisch
   * **Abonnement**: Wählen Sie ein Abonnement in der Dropdownliste aus.
   * **Dienst**: Suchen Sie nach „Cognitive Services“ und wählen Sie es dann aus.
   * **Ressource:** Wählen Sie eine LUIS-Ressource aus, falls eine solche vorhanden ist. Wenn dies nicht der Fall ist, wählen Sie „Allgemeine Frage“ aus.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [Konzepte über Erstellungs- und Runtimeschlüssel](luis-how-to-azure-subscription.md).
* Erfahren Sie, wie Sie [Schlüssel zuweisen](luis-how-to-azure-subscription.md) und [Mitwirkende hinzufügen](luis-how-to-collaborate.md).
