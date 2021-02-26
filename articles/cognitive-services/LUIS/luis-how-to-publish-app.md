---
title: 'Veröffentlichen einer App: LUIS'
titleSuffix: Azure Cognitive Services
description: Wenn Sie das Erstellen und Testen Ihrer aktiven LUIS-App abgeschlossen haben, stellen Sie sie Ihrer Clientanwendung zur Verfügung, indem Sie sie am Endpunkt veröffentlichen.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8e78fc5bd49aaf2b31fdc83ced132e2a39ca83d5
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558895"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Veröffentlichen Ihrer aktiven, trainierten App an einem Staging- oder Produktionsendpunkt

Wenn Sie das Erstellen, Trainieren und Testen Ihrer aktiven LUIS-App abgeschlossen haben, stellen Sie sie Ihrer Clientanwendung zur Verfügung, indem Sie sie am Endpunkt veröffentlichen.

## <a name="publishing"></a>Veröffentlichung
1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** den Namen der App auswählen.
1. Um am Endpunkt zu veröffentlichen, wählen Sie im Panel rechts oben **Veröffentlichen** aus.

    ![Schaltfläche „Veröffentlichen“ in oberer rechter Navigationsleiste](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Wählen Sie die Einstellungen für Ihren veröffentlichten Vorhersageendpunkt aus, und wählen Sie dann **Veröffentlichen** aus.

    ![Veröffentlichungseinstellungen und dann die Schaltfläche „Veröffentlichen“ auswählen](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Veröffentlichungsslots

Wählen Sie den richtigen Slot aus, wenn das Popupfenster angezeigt wird:

* Staging
* Bereitstellung

Durch Verwenden beider Veröffentlichungsslots haben Sie die Möglichkeit, zwei unterschiedliche Versionen Ihrer App auf den veröffentlichten Endpunkten oder dieselbe Version auf zwei unterschiedlichen Endpunkten zu nutzen.

### <a name="publishing-regions"></a>Veröffentlichungsregionen

Die App wird in allen Regionen veröffentlicht, die den im LUIS-Portal auf der Seite **Verwalten** ->  **[Azure-Ressourcen](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** hinzugefügten LUIS-Vorhersageendpunkt-Ressourcen zugeordnet sind.

Wenn Sie beispielsweise für eine auf [www.luis.ai](https://www.luis.ai) erstellte App eine LUIS-Ressource in zwei Regionen, **westus** und **eastus**, erstellen und diese der App als Ressourcen hinzufügen, wird die App in beiden Regionen veröffentlicht. Weitere Informationen zu LUIS-Regionen finden Sie unter [Regionen](luis-reference-regions.md).

> [!TIP]
> Es gibt drei Erstellungsregionen. Sie müssen die Erstellung in der Region durchführen, in der Sie veröffentlichen möchten. Wenn Sie in allen Regionen veröffentlichen müssen, müssen Sie den Erstellungsprozess und das daraus resultierende trainierte Modell in allen drei Erstellungsregionen verwalten.


## <a name="configuring-publish-settings"></a>Konfigurieren der Einstellungen für die Veröffentlichung

Nachdem Sie den Slot ausgewählt haben, konfigurieren Sie die Veröffentlichungseinstellungen für:

* Stimmungsanalyse
* Sprachvorbereitung

Nach der Veröffentlichung können Sie diese Einstellungen auf der zum Abschnitt **Verwalten** gehörenden Seite **Veröffentlichungseinstellungen** ändern. Sie können die Einstellungen mit jeder Veröffentlichung ändern. Wenn Sie eine Veröffentlichung abbrechen, werden alle Änderungen, die Sie während der Veröffentlichung vorgenommen haben, ebenfalls abgebrochen.

### <a name="when-your-app-is-published"></a>Nach Veröffentlichung Ihrer App

Wenn Ihre App erfolgreich veröffentlicht wurde, wird eine Erfolgsmeldung am oberen Rand des Browsers angezeigt. Die Benachrichtigung enthält außerdem einen Link zu den Endpunkten.

Wenn Sie die Endpunkt-URL benötigen, wählen Sie den Link aus. Außerdem können Sie die Endpunkt-URLs ermitteln, indem Sie im oberen Menü die Option **Verwalten** und dann im linken Menü die Option **Azure-Ressourcen** auswählen.

## <a name="sentiment-analysis"></a>Stimmungsanalyse

<a name="enable-sentiment-analysis"></a>

Die Standpunktanalyse ermöglicht LUIS die Integration mit der [Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/), um die Analyse von Stimmungen und Formulierungen bereitzustellen.

Sie müssen keine Textanalyseschlüssel bereitstellen, und es entstehen keine Abrechnungskosten für diesen Dienst in Ihrem Azure-Konto.

Stimmungsdaten stellen eine Bewertung zwischen 1 und 0 dar, die eine eher positive (näher an 1) oder negative (näher an 0) Stimmung in den Daten angibt. Die Stimmungsbezeichnung von `positive`, `neutral` und `negative` gilt jeweils pro unterstützter Kultur. Derzeit unterstützt nur Englisch Stimmungsbezeichnungen.

Weitere Informationen zur JSON-Endpunktantwort im Zusammenhang mit der Standpunktanalyse finden Sie unter [Standpunktanalyse](luis-reference-prebuilt-sentiment.md).

## <a name="speech-priming"></a>Sprachvorbereitung

Sprachvorbereitung ist der Prozess, bei dem das LUIS-Modell vor der Sprachsynthese (Konvertierung von Text in Sprache) an Sprachdienste gesendet wird. Dies ermöglicht es dem jeweiligen Sprachdienst, eine genauere Sprachkonvertierung für Ihr Modell bereitzustellen. Außerdem ermöglicht dies sowohl Bot-Sprach- als auch -LUIS-Anforderungen und -Antworten in einem Aufruf, indem ein Sprachaufruf ausgeführt und eine LUIS-Antwort zurückgegeben wird. Dadurch wird die Gesamtwartezeit verkürzt.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Hinzufügen von Schlüsseln zum Azure-Abonnementschlüssel für LUIS und zum Einrichten des Schlüssels der Bing-Rechtschreibprüfung sowie zum Einschließen aller Absichten in Ergebnissen finden Sie unter [Schlüssel verwalten](./luis-how-to-azure-subscription.md).
* Weitere Anweisungen dazu, wie Sie Ihre veröffentlichte App in der Testkonsole testen, finden Sie unter [Trainieren und Testen Ihrer App](luis-interactive-test.md).

