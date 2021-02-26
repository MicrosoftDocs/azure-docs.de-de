---
title: 'Verwenden von Erstellungs- und Laufzeitschlüsseln: LUIS'
description: Wenn Sie LUIS zum ersten Mal verwenden, müssen Sie keinen Erstellungsschlüssel erstellen. Wenn Sie die App veröffentlichen möchten und dann Ihren Laufzeitendpunkt verwenden, müssen Sie den Laufzeitschlüssel erstellen und ihn der App zuweisen.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988160"
---
# <a name="create-luis-resources"></a>Erstellen von LUIS-Ressourcen

Erstellungs- und Abfragevorhersagelaufzeit-Ressourcen ermöglichen die Authentifizierung bei Ihrer LUIS-App (Language Understanding) und bei Ihrem Vorhersageendpunkt.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-Ressourcen

LUIS lässt drei Arten von Azure-Ressourcen sowie eine Azure-fremde Ressource zu:

|Resource|Zweck|Cognitive Service `kind`|Cognitive Service `type`|
|--|--|--|--|
|Erstellungsressource|Ermöglicht Ihnen das Erstellen, Verwalten, Trainieren, Testen und Veröffentlichen von Anwendungen. [Erstellen Sie eine LUIS-Erstellungsressource](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal), wenn Sie LUIS-Apps programmgesteuert oder über das LUIS-Portal erstellen möchten. Sie müssen [Ihr LUIS-Konto migrieren](luis-migration-authoring.md#what-is-migration), bevor Sie Ihre Azure-Erstellungsressourcen mit der Anwendung verknüpfen. Sie können die Berechtigungen für die Erstellungsressource steuern, indem Sie Personen [die Rolle „Mitwirkender“](#contributions-from-other-authors) zuweisen. <br><br> Für die LUIS-Erstellungsressource ist ein Tarif verfügbar:<br> <ul> <li>Die **Erstellungsressource „Free F0“** , die Ihnen monatlich 1 Million kostenlose Erstellungstransaktionen und 1.000 kostenlose Testanforderungen für den Vorhersageendpunkt bietet. |`LUIS.Authoring`|`Cognitive Services`|
|Vorhersageressource| Nachdem Sie die LUIS-Anwendung veröffentlicht haben, verwenden Sie die Vorhersageressource/den Vorhersageschlüssel, um Anforderungen für den Vorhersageendpunkt abzufragen. Erstellen Sie eine LUIS-Vorhersageressource, bevor Ihre Client-App Vorhersagen über die 1.000 Anforderungen hinaus anfordert, die durch die Erstellungs- oder Startressource zur Verfügung gestellt werden. <br><br> Für die Vorhersageressource sind zwei Tarife verfügbar:<br><ul> <li> Die **F0-Vorhersageressource „Free“** , die Ihnen monatlich 10.000 kostenlose Anforderungen für den Vorhersageendpunkt bietet.<br> <li> Die **S0-Vorhersageressource „Standard“** , die der kostenpflichtige Tarif ist. [Weitere Informationen zu Preisen](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Start-/Testressource|Ermöglicht Ihnen das Erstellen, Verwalten, Trainieren, Testen und Veröffentlichen von Anwendungen. Diese Ressource wird standardmäßig erstellt, wenn Sie bei der ersten Anmeldung bei LUIS die Option „Startressource“ auswählen. Der Startschlüssel wird schließlich veraltet sein. Alle LUIS-Benutzer müssen [ihre Konten migrieren](luis-migration-authoring.md#what-is-migration) und ihre LUIS-Anwendungen mit einer Erstellungsressource verknüpfen. Im Gegensatz zur Erstellungsressource erhalten Sie mit dieser Ressource keine Berechtigungen für die rollenbasierte Zugriffssteuerung in Azure. <br><br> Wie die Erstellungsressource bietet Ihnen die Startressource 1 Million kostenlose Erstellungstransaktionen und 1.000 kostenlose Testanforderungen für den Vorhersageendpunkt.|-|Keine Azure-Ressource|
|[Cognitive Services-Ressourcenschlüssel für mehrere Dienste](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Anforderungen von Abfragevorhersageendpunkten, die mit LUIS und anderen unterstützten Cognitive Services geteilt werden.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS bietet zwei Arten von F0-Ressourcen (kostenloser Tarif): eine für Erstellungstransaktionen und eine für Vorhersagetransaktionen. Wenn das Kontingent kostenloser Vorhersagetransaktionen erschöpft ist, stellen Sie sicher, dass Sie die F0-Vorhersageressource verwenden, die Ihnen monatlich 10.000 kostenlose Transaktionen bietet, und nicht die Erstellungsressource, die Ihnen monatlich 1.000 Vorhersagetransaktionen bietet.

Nach Abschluss der Azure-Ressourcenerstellung können Sie der App im LUIS-Portal die [Ressource zuweisen](#assign-a-resource-to-an-app).

> [!important]
> Sie sollten LUIS-Apps in den [Regionen](luis-reference-regions.md#publishing-regions) erstellen, in denen sie veröffentlicht und abgefragt werden sollen.

## <a name="resource-ownership"></a>Ressourcenbesitz

Eine Azure-Ressource gehört (wie eine LUIS-Ressource) zu dem Abonnement, das die Ressource enthält.

Um den Besitzer einer Ressource zu ändern, können Sie eine der folgenden Aktionen durchführen:
* Übertragen Sie das [Eigentum](../../cost-management-billing/manage/billing-subscription-transfer.md) Ihres Abonnements.
* Exportieren Sie die LUIS-App als Datei, und importieren Sie die App anschließend in einem anderen Abonnement. Die Exportfunktion finden Sie im LUIS-Portal auf der Seite **Meine Apps**.

## <a name="resource-limits"></a>Ressourceneinschränkungen

### <a name="authoring-key-creation-limits"></a>Grenzwerte für die Erstellung von Erstellungsschlüsseln

Sie können bis zu 10 Erstellungsschlüssel pro Region und Abonnement erstellen. Veröffentlichungsregionen unterscheiden sich von Erstellungsregionen. Stellen Sie sicher, dass Sie eine App in der Erstellungsregion erstellen, die der Veröffentlichungsregion entspricht, in der sich Ihre Clientanwendung befinden soll. Weitere Informationen wie Erstellungsregionen Veröffentlichungsregionen zugeordnet werden können, finden Sie unter [Erstellungs- und Veröffentlichungsregionen und die dazugehörigen Schlüssel](luis-reference-regions.md). 

Weitere Informationen finden Sie unter [Erstellungsressourcenlimits](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Fehler für Grenzwerte bei der Schlüsselnutzung

Nutzungslimits sind tarifabhängig.

Wenn Sie Ihr TPS-Kontingent (Transaktionen pro Sekunde) überschreiten, erhalten Sie den HTTP-Fehler 429. Wenn Sie Ihr TPM-Kontingent (Transaktionen pro Monat) überschreiten, erhalten Sie den HTTP-Fehler 403.


### <a name="reset-an-authoring-key"></a>Zurücksetzen des Erstellungsschlüssels

Apps, die [zur Erstellungsressource migriert](luis-migration-authoring.md) wurden: Wurde Ihr Erstellungsschlüssel kompromittiert, setzen Sie den Schlüssel für diese Erstellungsressource im Azure-Portal auf der Seite **Schlüssel** zurück.

Für Apps, die nicht migriert wurden: Der Schlüssel wird für all Ihre Apps im LUIS-Portal zurückgesetzt. Wenn Sie Ihre Apps mithilfe der Authoring-APIs verfassen, müssen Sie den Wert von `Ocp-Apim-Subscription-Key` auf den neuen Schlüssel ändern.

### <a name="regenerate-an-azure-key"></a>Erneutes Generieren eines Azure-Schlüssels

Sie können einen Azure-Schlüssel über die Seite **Schlüssel** im Azure-Portal neu generieren.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>App-Besitz, -Zugriff und -Sicherheit

Eine App ist durch ihre Azure-Ressourcen definiert, die durch das Abonnement des Besitzers bestimmt sind.

Sie können Ihre LUIS-App verschieben. Verwenden Sie die folgenden Azure-Ressourcen, damit diese Ihnen über das Azure-Portal oder die Azure CLI dabei helfen können:

* [Verschieben einer App zwischen LUIS-Erstellungsressourcen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Verschieben einer Ressource in eine neue Ressourcengruppe oder in ein Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Verschieben einer Ressource innerhalb desselben Abonnements oder zwischen Abonnements](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Beiträge von anderen Autoren

Apps, die [zur Erstellungsressource migriert](luis-migration-authoring.md) wurden: Sie können _Mitwirkende_ für eine Erstellungsressource im Azure-Portal verwalten, indem Sie die Seite **Zugriffssteuerung (IAM)** verwenden. Erfahren Sie, [wie Sie einen Benutzer hinzufügen](luis-how-to-collaborate.md), indem Sie die E-Mail-Adresse des Projektmitarbeiters und die Rolle „Mitwirkender“ verwenden.

Für Apps, die noch nicht migriert wurden: Sie können alle _Projektmitarbeiter_ auf der Seite **Verwalten -> Projektmitarbeiter** im LUIS-Portal verwalten.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Abfragevorhersagezugriff für private und öffentliche Apps

Bei privaten Apps ist der Zugriff auf die Abfragevorhersagelaufzeit für Besitzer und Mitwirkende möglich. Für eine öffentliche Apps ist der Laufzeitzugriff für jede Person möglich, die über eine eigene Azure [Cognitive Service](../cognitive-services-apis-create-account.md)- oder [LUIS](#create-resources-in-the-azure-portal)-Laufzeitressource und die ID der öffentlichen App verfügt.

Derzeit gibt es keinen Katalog für öffentliche Apps.

### <a name="authoring-permissions-and-access"></a>Erstellungsberechtigungen und Zugriff
Der Zugriff auf eine App aus dem [LUIS](luis-reference-regions.md#luis-website)-Portal oder über die [Erstellungs-APIs](https://go.microsoft.com/fwlink/?linkid=2092087) wird von der Azure-Erstellungsressource gesteuert.

Der Besitzer und alle Mitwirkenden haben Zugriff für die Erstellung der App.

|Der Zugriff für die Erstellung umfasst:|Notizen|
|--|--|
|Hinzufügen oder Entfernen von Endpunktschlüsseln||
|Exportieren einer Version||
|Exportieren von Endpunktprotokollen||
|Importversion||
|Öffentlichmachen von Apps|Wenn eine App öffentlich ist, kann jede Person mit einem Erstellungs- oder Endpunktschlüssel die App abfragen.|
|Ändern von Modellen|
|Veröffentlichen|
|Überprüfen von Endpunktäußerungen für das [aktive Lernen](luis-how-to-review-endpoint-utterances.md)|
|Trainieren|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Laufzeitzugriff auf Vorhersageendpunkt

Der Zugriff zum Abfragen des Vorhersageendpunkts wird von einer Einstellung auf der Seite **Anwendungsinformationen** im Abschnitt **Verwalten** gesteuert.

|[Privater Endpunkt](#runtime-security-for-private-apps)|[Öffentlicher Endpunkt](#runtime-security-for-public-apps)|
|:--|:--|
|Für Besitzer und Mitwirkende verfügbar|Für Besitzer, Mitwirkende und alle Personen verfügbar, denen die App-ID bekannt ist|

Sie können steuern, wer Ihren LUIS-Laufzeitschlüssel sieht, indem Sie ihn in einer Server-zu-Server-Umgebung aufrufen. Wenn Sie LUIS über einen Bot verwenden, ist die Verbindung zwischen dem Bot und LUIS bereits sicherer. Wenn Sie den LUIS-Endpunkt direkt aufrufen, sollten Sie eine serverseitige API (z. B. als Azure-[Funktion](https://azure.microsoft.com/services/functions/)) mit kontrolliertem Zugang (z. B. über [Azure AD](https://azure.microsoft.com/services/active-directory/)) erstellen. Wenn die serverseitige API aufgerufen und authentifiziert und die Autorisierung überprüft wurde, übergeben Sie den Aufruf an LUIS. Diese Strategie verhindert keine Man-in-the-Middle-Angriffe. Es verschleiert jedoch die Schlüssel- und Endpunkt-URL Ihrer Benutzer, ermöglicht Ihnen die Nachverfolgung des Zugriffs und das Hinzufügen von Endpunktantwortprotokollen (wie [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Laufzeitsicherheit für private Apps

Die Laufzeitumgebung einer privaten App ist nur für folgende Schlüssel verfügbar:

|Schlüssel und Benutzer|Erklärung|
|--|--|
|Erstellungsschlüssel des Besitzers| Bis zu 1.000 Endpunkttreffer|
|Erstellungsschlüssel für Projektmitarbeiter/Mitwirkende| Bis zu 1.000 Endpunkttreffer|
|Jeder Schlüssel, der LUIS von einem Ersteller oder Projektmitarbeiter/Mitwirkenden zugewiesen wurde|Basierend auf dem Tarif für die Schlüsselverwendung|

### <a name="runtime-security-for-public-apps"></a>Laufzeitsicherheit für öffentliche Apps

Wenn Ihre App als öffentlich konfiguriert ist, können _alle_ gültigen LUIS-Erstellungsschlüssel oder LUIS-Endpunktschlüssel Ihre App abfragen, solange der Schlüssel nicht das gesamte Endpunktkontingent verbraucht hat.

Ein Benutzer, der nicht Besitzer oder Mitwirkender ist, hat nur dann Zugriff auf die Laufzeitumgebung einer öffentlichen App, wenn er über App-ID verfügt. LUIS verfügt nicht über einen öffentlichen Markt oder eine andere Möglichkeit der Suche nach einer öffentlichen App.

Eine öffentliche App wird in allen Regionen veröffentlicht. Somit kann ein Benutzer mit einem regionsbasierten LUIS-Ressourcenschlüssel auf die App in jeder Region zugreifen, die dem Ressourcenschlüssel zugeordnet ist.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Steuern des Zugriffs auf Ihren Abfragevorhersage-Endpunkt

Sie können steuern, wer Ihren LUIS-Laufzeitschlüssel für den Vorhersageendpunkt sehen kann, indem Sie ihn in einer Server-zu-Server-Umgebung aufrufen. Wenn Sie LUIS über einen Bot verwenden, ist die Verbindung zwischen dem Bot und LUIS bereits sicherer. Wenn Sie den LUIS-Endpunkt direkt aufrufen, sollten Sie eine serverseitige API (z. B. als Azure-[Funktion](https://azure.microsoft.com/services/functions/)) mit kontrolliertem Zugang (z. B. über [Azure AD](https://azure.microsoft.com/services/active-directory/)) erstellen. Wenn die serverseitige API aufgerufen und die Authentifizierung und Autorisierung überprüft wurden, übergeben Sie den Aufruf an LUIS. Diese Strategie verhindert keine Man-in-the-Middle-Angriffe. Es verschleiert jedoch Ihren Endpunkt vor Ihren Benutzern, ermöglicht Ihnen die Nachverfolgung des Zugriffs und das Hinzufügen von Endpunktantwortprotokollen (wie [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Anmelden beim LUIS-Portal und Beginnen mit der Erstellung

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und stimmen Sie den Nutzungsbedingungen zu.
1. Wählen Sie für die Erstellung Ihrer LUIS-App zunächst Ihren Azure-LUIS-Erstellungsschlüssel aus:

   ![Der Screenshot zeigt den Begrüßungsbildschirm.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wenn Sie die Ressourcenauswahl getroffen haben, [erstellen Sie eine neue App](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Erstellen von Ressourcen über die Azure CLI

Verwenden Sie die [Azure CLI](/cli/azure/install-azure-cli), um die einzelnen Ressourcen individuell zu erstellen.

Ressource `kind`:

* Erstellung: `LUIS.Authoring`
* Vorhersage: `LUIS`

1. Melden Sie sich bei der Azure CLI an:

    ```azurecli
    az login
    ```

    Dieser Befehl öffnet einen Browser, in dem Sie das richtige Konto auswählen und die Authentifizierung bereitstellen können.

1. Erstellen Sie eine LUIS-Erstellungsressource der Art `LUIS.Authoring` namens `my-luis-authoring-resource`. Erstellen Sie sie in der _vorhandenen_ Ressourcengruppe namens `my-resource-group` für die Region `westus`.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Erstellen Sie eine LUIS-Vorhersageendpunkt-Ressource der Art `LUIS` namens `my-luis-prediction-resource`. Erstellen Sie sie in der _vorhandenen_ Ressourcengruppe namens `my-resource-group` für die Region `westus`. Wenn Sie einen höheren Durchsatz als für den Free-Tarif wünschen, ändern Sie `F0` in `S0`. [Weitere Informationen zu Tarifen und Durchsatz.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Diese Schlüssel werden vom LUIS-Portal erst dann verwendet, wenn sie auf der Seite **Verwalten** > **Azure-Ressourcen** im LUIS-Portal zugewiesen wurden.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Zuweisen von Ressourcen im LUIS-Portal

Sie können eine Erstellungsressource für eine einzelne App oder für alle Apps in LUIS zuweisen. In der folgenden Vorgehensweise werden alle Apps einer einzelnen Erstellungsressource zugewiesen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an.
1. Wählen Sie oben rechts Ihr Benutzerkonto und dann in der Liste **Einstellungen** aus.
1. Wählen Sie auf der Seite **Benutzereinstellungen** die Option **Erstellungsressource hinzufügen** aus, und wählen Sie dann eine vorhandene Erstellungsressource aus. Wählen Sie **Speichern** aus.

## <a name="assign-a-resource-to-an-app"></a>Zuweisen einer Ressource zu einer App

>[!NOTE]
>Wenn Sie kein Azure-Abonnement besitzen, können Sie keine neue Azure-Ressource zuweisen oder erstellen. Sie müssen ein [kostenloses Azure-Konto](https://azure.microsoft.com/en-us/free/) erstellen und dann zu LUIS zurückkehren, um eine neue Ressource über das Portal zu erstellen.

Sie können dieses Verfahren verwenden, um eine Erstellungs- oder Vorhersageressource zu erstellen oder einer Anwendung zuzuweisen: 

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an. Wählen Sie in der Liste **Meine Apps** eine App aus.
1. Wechseln Sie zu **Verwalten** > **Azure-Ressourcen**:

    ![Der Screenshot zeigt die Seite „Azure-Ressourcen“ an.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Wählen Sie auf der Registerkarte **Vorhersageressource** oder **Erstellungsressource** die Schaltfläche **Vorhersageressource hinzufügen** oder **Erstellungsressource hinzufügen** aus.
1. Verwenden Sie die Felder im Formular, um die richtige Ressource zu finden, und wählen Sie dann **Speichern** aus.
1. Wenn Sie noch nicht über eine vorhandene Ressource verfügen, können Sie eine erstellen, indem Sie **Neue LUIS-Ressource erstellen?** am unteren Rand des Fensters auswählen.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Zuweisen einer Abfragevorhersagelaufzeit-Ressource ohne Verwendung des LUIS-Portals

Für automatisierte Prozesse wie CI/CD-Pipelines möchten Sie möglicherweise die Zuordnung einer LUIS-Runtimeressource zu einer LUIS-App automatisieren. Führen Sie dazu diese Schritte aus:

1. Rufen Sie ein Azure Resource Manager-Token von [dieser Website](https://resources.azure.com/api/token?plaintext=true) ab. Dieses Token läuft ab, daher sollten Sie es sofort verwenden. Die Anforderung gibt ein Azure Resource Manager-Token zurück.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Der Screenshot zeigt die Website zur Anforderung eines Azure Resource Manager-Tokens.](./media/luis-manage-keys/get-arm-token.png)

1. Verwenden Sie das Token, um die LUIS-Runtimeressourcen abonnementübergreifend anzufordern. Verwenden Sie die [API zum Abrufen von LUIS-Azure-Konten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auf die Ihr Benutzerkonto Zugriff hat.

    Diese POST-API erfordert folgende Werte:

    |Header|Wert|
    |--|--|
    |`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|

    Die API gibt ein Array von JSON-Objekten zurück, die Ihre LUIS-Abonnements darstellen. Die zurückgegebenen Werte umfassen die Abonnement-ID, die Ressourcengruppe und den Ressourcennamen, der als `AccountName` zurückgegeben wurde. Finden Sie im Array das Element, das die LUIS-Ressource darstellt, die Sie der LUIS-App zuweisen möchten.

1. Weisen Sie das Token mit der API zum [Zuweisen eines LUIS-Azure-Kontos zu einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) der LUIS-Ressource zu.

    Diese POST-API erfordert folgende Werte:

    |type|Einstellung|Wert|
    |--|--|--|
    |Header|`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |Header|`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|
    |Header|`Content-type`|`application/json`|
    |Abfragezeichenfolge|`appid`|die LUIS-App-ID
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Wenn diese API erfolgreich ist, gibt sie `201 - created status` zurück.

## <a name="unassign-a-resource"></a>Aufheben der Zuweisung einer Ressource

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie dann in der Liste **Meine Apps** eine App aus.
1. Wechseln Sie zu **Verwalten** > **Azure-Ressourcen**.
1. Wählen Sie auf der Registerkarte **Vorhersageressource** oder **Erstellungsressource** die Schaltfläche **Ressourcenzuweisung aufheben** für die Ressource aus.

Wenn Sie die Zuweisung einer Ressource aufheben, wird diese nicht aus Azure gelöscht. Lediglich die Verknüpfung mit LUIS wird aufgehoben.


## <a name="delete-an-account"></a>Löschen eines Kontos

Informationen zu den Daten, die beim Löschen Ihres Kontos gelöscht werden, finden Sie unter [Datenspeicherung und -löschung](luis-concept-data-storage.md#accounts).

## <a name="change-the-pricing-tier"></a>Ändern des Tarifs

1.  Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrem LUIS-Abonnement, und wählen Sie es aus:

    ![Der Screenshot zeigt ein LUIS-Abonnement im Azure-Portal.](./media/luis-usage-tiers/find.png)
1.  Wählen Sie **Tarif** aus, um die verfügbaren Tarife anzuzeigen:

    ![Der Screenshot zeigt das Menüelement „Tarif“.](./media/luis-usage-tiers/subscription.png)
1.  Wählen Sie den Tarif aus, und klicken Sie dann auf **Auswählen**, um die Änderung zu speichern:

    ![Der Screenshot zeigt, wie Sie einen Tarif auswählen und speichern können.](./media/luis-usage-tiers/plans.png)

    Wenn die Änderung des Tarifs abgeschlossen ist, wird der aktualisierte Tarif in einem Popupfenster bestätigt:

    ![Der Screenshot des Popupfensters, in dem die Tarifaktualisierung bestätigt wird.](./media/luis-usage-tiers/updated.png)
1. Denken Sie daran, [diesen Endpunktschlüssel](#assign-a-resource-to-an-app) auf der Seite **Publish** (Veröffentlichen) zuzuweisen und für alle Endpunktabfragen zu verwenden.

## <a name="view-azure-resource-metrics"></a>Anzeigen von Azure-Ressourcenmetriken

### <a name="view-a-summary-of-azure-resource-usage"></a>Anzeigen einer Zusammenfassung der Azure-Ressourcennutzung
Sie können LUIS-Nutzungsinformationen im Azure-Portal anzeigen. Die Seite **Übersicht** zeigt eine Zusammenfassung, einschließlich der letzten Aufrufe und Fehler. Wenn Sie eine LUIS-Endpunktanforderung stellen, kann es bis zu fünf Minuten dauern, bis die Änderung angezeigt wird.

![Screenshot der Seite „Übersicht“](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Anpassen von Nutzungsdiagrammen für Azure-Ressourcen
Die Seite **Metriken** bietet eine detailliertere Ansicht der Daten:

![Screenshot der Seite „Metriken“](./media/luis-usage-tiers/metrics-default.png)

Sie können Ihre Metrikdiagramme für einen bestimmten Zeitraum und Metriktyp konfigurieren.

![Der Screenshot zeigt ein benutzerdefiniertes Diagramm.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Warnung für Gesamttransaktions-Schwellenwert
Wenn Sie wissen möchten, wann Sie einen bestimmten Transaktionsschwellenwert erreichen, z. B. 10.000 Transaktionen, können Sie eine Warnung erstellen:

![Der Screenshot zeigt die Seite mit den Warnungsregeln.](./media/luis-usage-tiers/alert-default.png)

Fügen Sie eine Metrikwarnung für die Metrik der **Summe der Aufrufe** für einen bestimmten Zeitraum hinzu. Fügen Sie die E-Mail-Adressen aller Personen hinzu, die die Warnung erhalten sollen. Fügen Sie Webhooks für alle Systeme hinzu, die die Warnung erhalten sollen. Sie können auch eine Logik-App ausführen, wenn die Warnung ausgelöst wird.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Versionen verwenden](luis-how-to-manage-versions.md), um den Lebenszyklus ihrer App zu steuern.
* Migrieren Sie zu der neuen [Erstellungsressource](luis-migration-authoring.md).
