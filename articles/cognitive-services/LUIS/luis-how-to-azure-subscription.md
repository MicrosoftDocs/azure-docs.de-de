---
title: Abonnementschlüssel
titleSuffix: Language Understanding - Azure Cognitive Services
description: Für Ihre ersten 1000 kostenlosen Endpunktabfragen müssen Sie keine Abonnementschlüssel erstellen. Wenn Sie einen Fehler vom Typ _Kontingent aufgebraucht_ in Form eines HTTP 403- oder 429-Fehlers erhalten, müssen Sie einen Schlüssel erstellen und Ihrer App zuweisen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/01/2019
ms.author: diberry
ms.openlocfilehash: 7315c80ad74eae07e41577fb2ac13742002e729e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198576"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Verwenden von Abonnementschlüsseln mit Ihrer LUIS-App

Für Ihre ersten 1000 kostenlosen Endpunktabfragen müssen Sie keine Abonnementschlüssel erstellen. Sobald diese Endpunktabfragen aufgebraucht sind, erstellen Sie eine Azure-Ressource im [Azure-Portal](https://portal.azure.com) und weisen diese dann einer LUIS-App im [LUIS-Portal](https://www.luis.ai) zu.

Wenn Sie einen Fehler vom Typ _Kontingent aufgebraucht_ in Form eines HTTP 403- oder 429-Fehlers erhalten, müssen Sie einen Schlüssel erstellen und Ihrer App zuweisen. 

Verwenden Sie den Free-Tarif (F0) ausschließlich für Tests und Prototypen. Verwenden Sie für Produktionssysteme einen [bezahlten](https://aka.ms/luis-price-tier) Tarif. Verwenden Sie den [Erstellungsschlüssel](luis-concept-keys.md#authoring-key) nicht für Endpunktabfragen in der Produktion.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Erstellen der Vorhersageendpunkt-Runtimeressource im Azure-Portal

Erfahren Sie mehr im Schnellstart [Erstellen einer App](get-started-portal-build-app.md).

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Zuweisen des Ressourcenschlüssels an die LUIS-App im LUIS-Portal

Erfahren Sie mehr im Schnellstart [Bereitstellung](get-started-portal-deploy-app.md).

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Aufheben der Zuweisung einer Ressource
Wenn Sie die Zuweisung des Endpunktschlüssels aufheben, wird er nicht aus Azure gelöscht. Lediglich die Verknüpfung mit LUIS wird aufgehoben. 

Wenn die Zuweisung eines Endpunktschlüssels aufgehoben oder er der App nicht zugewiesen ist, wird auf alle Anforderungen an die Endpunkt-URL ein Fehler zurückgegeben: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Einschließen aller prognostizierten Absichtsbewertungen
Über das Kontrollkästchen **Include all predicted intent scores** (Alle prognostizierten Absichtsbewertungen einschließen) können Sie bei der Endpunktabfrageantwort die Prognosebewertung für jede Absicht einbeziehen. 

Dank dieser Einstellung kann der Chatbot oder die LUIS aufrufende Anwendung eine programmgesteuerte Entscheidung treffen, die auf den Bewertungen der zurückgegebenen Absichten beruht. Im Allgemeinen sind die obersten zwei Absichten am interessantesten. Wenn die oberste Bewertung die Absicht „None“ ist, kann Ihr Chatbot eine Nachverfolgungsfrage stellen, die endgültig zwischen der Absicht „None“ und der anderen Absicht mit hoher Bewertung entscheidet. 

Die Absichten und ihre Bewertungen sind ebenfalls in den Endpunktprotokollen enthalten. Sie können diese Protokolle [exportieren](luis-how-to-start-new-app.md#export-app) und die Ergebnisse analysieren. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Aktivieren der Bing-Rechtschreibprüfung 
Wenn in den **URL-Einstellungen für den Endpunkt** der Ein-/Ausschalter **Bing spell checker** (Bing-Rechtschreibprüfung) aktiviert ist, kann LUIS falsch geschriebene Wörter vor der Vorhersage korrigieren. Erstellen Sie einen **[Bing-Rechtschreibprüfungs-Schlüssel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Fügen Sie den querystring-Parameter **spellCheck=true** und den **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** hinzu. Ersetzen Sie `{YOUR_BING_KEY_HERE}` durch den Schlüssel Ihrer Bing-Rechtschreibprüfung.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Veröffentlichungsregionen

Erfahren Sie mehr über die [Veröffentlichungsregionen](luis-reference-regions.md), einschließlich der Veröffentlichung in [Europa](luis-reference-regions.md#publishing-to-europe) und [Australien](luis-reference-regions.md#publishing-to-australia). Veröffentlichungsregionen unterscheiden sich von Erstellungsregionen. Erstellen Sie eine App in der Erstellungsregion, die der gewünschten Veröffentlichungsregion für den Abfrageendpunkt entspricht.

## <a name="assign-resource-without-luis-portal"></a>Zuweisen von Ressourcen ohne das LUIS-Portal

Für die Automatisierung, z.B. mit einer CI/CD-Pipeline, empfiehlt es sich, die Zuweisung einer LUIS-Ressource zu einer LUIS-App zu automatisieren. Die folgenden Schritte müssen dazu ausgeführt werden:

1. Rufen Sie ein Azure Resource Manager-Token von dieser [Website](https://resources.azure.com/api/token?plaintext=true) ab. Dieses Token läuft ab, daher sollten Sie es sofort verwenden. Die Anforderung gibt ein Azure Resource Manager-Token zurück.

    ![Anfordern und Empfangen eines Azure Resource Manager-Tokens](./media/luis-manage-keys/get-arm-token.png)

1. Verwenden Sie das Token zum Anfordern der LUIS-Ressourcen aus verschiedenen Abonnements über die [API zum Abrufen von LUIS-Azure-Konten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auf die Ihr Benutzerkonto Zugriff hat. 

    Diese POST-API erfordert folgende Einstellungen:

    |Header|Wert|
    |--|--|
    |`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Beachten Sie, dass dem Tokenwert das Wort `Bearer` und ein Leerzeichen vorangestellt werden müssen.| 
    |`Ocp-Apim-Subscription-Key`|Ihr [Erstellungsschlüssel](luis-how-to-account-settings.md).|

    Diese API gibt ein Array von JSON-Objekten aus Ihren LUIS-Abonnements, einschließlich Abonnement-ID, Ressourcengruppe und Ressourcennamen als Kontonamen zurück. Finden Sie im Array das Element, das die LUIS-Ressource darstellt, die der LUIS-App zugewiesen werden soll. 

1. Weisen Sie das Token mit der API zum [Zuweisen eines LUIS-Azure-Kontos zu einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) der LUIS-Ressource zu. 

    Diese POST-API erfordert folgende Einstellungen:

    |Type|Einstellung|Wert|
    |--|--|--|
    |Header|`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Beachten Sie, dass dem Tokenwert das Wort `Bearer` und ein Leerzeichen vorangestellt werden müssen.|
    |Header|`Ocp-Apim-Subscription-Key`|Ihr [Erstellungsschlüssel](luis-how-to-account-settings.md).|
    |Header|`Content-type`|`application/json`|
    |Abfragezeichenfolge|`appid`|die LUIS-App-ID 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Wenn diese API erfolgreich ist, wird der Status „201 – erstellt“ zurückgegeben. 

## <a name="change-pricing-tier"></a>Ändern des Tarifs

1.  Suchen Sie in [Azure](https://portal.azure.com) nach Ihrem LUIS-Abonnement. Wählen Sie das LUIS-Abonnement aus.
    ![Suchen nach Ihrem LUIS-Abonnement](./media/luis-usage-tiers/find.png)
1.  Wählen Sie **Tarif** aus, um die verfügbaren Tarife anzuzeigen. 
    ![Anzeigen der Tarife](./media/luis-usage-tiers/subscription.png)
1.  Wählen Sie den Tarif aus, und wählen Sie dann **Auswählen** aus, um die Änderung zu speichern. 
    ![Ändern Ihres LUIS-Tarifs](./media/luis-usage-tiers/plans.png)
1.  Wenn die Änderung des Tarifs abgeschlossen ist, wird der neue Tarif in einem Popupfenster bestätigt. 
    ![Überprüfen Ihres LUIS-Tarifs](./media/luis-usage-tiers/updated.png)
1. Denken Sie daran, [diesen Endpunktschlüssel](#assign-endpoint-key) auf der Seite **Publish** (Veröffentlichen) zuzuweisen und für alle Endpunktabfragen zu verwenden. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Beheben von Fehlern vom Typ „Kontingent aufgebraucht“, wenn der Schlüssel die Nutzung im Tarif überschreitet
Jeder Tarif ermöglicht Endpunktanforderungen für Ihr LUIS-Konto mit einer bestimmten Rate. Wenn die Anforderungsrate höher als die zulässige Rate für Ihr gemessenes Konto pro Minute oder pro Monat ist, lösen Anforderungen den HTTP-Fehler „429: zu viele Anfragen“ aus.

Jeder Tarif ermöglicht kumulative Anforderungen pro Monat. Wenn die Gesamtzahl der Anforderungen die zulässige Rate übersteigt, lösen Anforderungen den HTTP-Fehler „403: verboten“ aus.  

## <a name="viewing-summary-usage"></a>Anzeigen der Nutzungszusammenfassung
Sie können in Azure LUIS-Nutzungsinformationen anzeigen. Auf der Seite **Übersicht** werden aktuelle zusammenfassende Informationen angezeigt, einschließlich Aufrufen und Fehlern. Wenn Sie eine LUIS-Endpunktanforderung ausführen, kann es bis zu fünf Minuten dauern, bis die Nutzung auf der Seite **Übersicht** angezeigt wird.

![Anzeigen der Nutzungszusammenfassung](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Anpassen von Nutzungsdiagrammen
Metriken erlauben eine detailliertere Darstellung der Daten.

![Standardmetriken](./media/luis-usage-tiers/metrics-default.png)

Sie können Ihre Metrikdiagramme für Zeitraum und Metriktyp konfigurieren. 

![Benutzerdefinierte Metriken](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Warnung für Gesamttransaktions-Schwellenwert
Wenn Sie informiert werden möchten, wenn Sie einen bestimmten Transaktionsschwellenwert erreicht haben, z.B. 10.000 Transaktionen, können Sie eine Warnung erstellen. 

![Standardwarnungen](./media/luis-usage-tiers/alert-default.png)

Fügen Sie eine Metrikwarnung für die Metrik der **Summe der Aufrufe** für einen bestimmten Zeitraum hinzu. Fügen Sie die E-Mail-Adressen aller Personen hinzu, die die Warnung erhalten sollen. Fügen Sie Webhooks für alle Systeme hinzu, die die Warnung erhalten sollen. Sie können auch eine Logik-App ausführen, wenn die Warnung ausgelöst wird. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwendung von [Versionen](luis-how-to-manage-versions.md) zum Verwalten von Änderungen an Ihrer LUIS-App.
