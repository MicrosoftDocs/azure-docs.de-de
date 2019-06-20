---
title: Kostenloses Testen von Speech Services
titleSuffix: Azure Cognitive Services
description: Die ersten Schritte mit dem Speech-Dienst sind einfach und erschwinglich. Mit einer 30-tägigen kostenlosen Testversion können Sie die Funktionen des Diensts kennenlernen und feststellen, ob er sich für Ihre Anwendungen eignet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 09cc38cd5343e8b01b3e704191ea40c133d724f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60541508"
---
# <a name="try-speech-services-for-free"></a>Kostenloses Testen von Speech Services

Die ersten Schritte mit dem Speech-Dienst sind einfach und erschwinglich. Mit einer 30-tägigen kostenlosen Testversion können Sie die Funktionen des Diensts kennenlernen und feststellen, ob er sich für Ihre Anwendungen eignet.

Wenn Sie mehr Zeit benötigen, können Sie sich für ein Microsoft Azure-Konto registrieren. Es sieht ein Guthaben von 200 US-Dollar vor, das Sie bis zu 30 Tage auf ein kostenpflichtiges Abonnement für den Speech-Dienst anwenden können.

Schließlich bieten Speech Services einen kostenlosen Tarif mit geringem Datenvolumen, der sich für die Entwicklung von Anwendungen eignet. Dieses kostenlose Abonnement können Sie auch nach Ablauf des Guthabens behalten.

## <a name="free-trial"></a>Kostenlose Testversion

Mit der 30-tägigen kostenlosen Testversion besitzen Sie während eines begrenzten Zeitraums Zugriff auf den Standardtarif.

So registrieren Sie sich für eine 30-tägige kostenlose Testversion:

1. Navigieren Sie zu [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/).

1. Wählen Sie die Registerkarte **Spracheingabe-APIs** aus.

   ![Registerkarte „Sprachdienste“](media/index/try-speech-api-free-trial1.png)

1. Wählen Sie unter **Speech Services** die Schaltfläche **API-Schlüssel anfordern** aus.

   ![API-Schlüssel](media/index/try-speech-api-free-trial2.png)

1. Stimmen Sie den Bedingungen zu, und wählen Sie im Dropdownmenü Ihr Gebietsschema aus.

   ![Akzeptieren der Bedingungen](media/index/try-speech-api-free-trial3.png)

1. Melden Sie sich mit Ihrem Microsoft-, Facebook-, LinkedIn- oder GitHub-Konto an.

    Sie können sich für ein kostenloses Microsoft-Konto im [Microsoft-Kontoportal](https://account.microsoft.com/account) registrieren. Klicken Sie zunächst auf **Mit Microsoft anmelden** und dann auf **Konto erstellen**, wenn Sie zur Anmeldung aufgefordert werden. Führen Sie die Schritte zum Erstellen und Überprüfen Ihres neuen Microsoft-Kontos aus.

Nachdem Sie sich bei „Cognitive Services ausprobieren“ angemeldet haben, beginnt Ihr kostenloser Testzeitraum. Auf der angezeigten Webseite sind alle Azure Cognitive Services-Dienste aufgeführt, für die Sie zurzeit über Testabonnements verfügen. Neben **Speech Services** werden zwei Abonnementschlüssel angezeigt. Sie können beide Schlüssel in Ihren Anwendungen verwenden.

> [!NOTE]
> Alle kostenlosen Testabonnements sind der Region „USA, Westen“ zugewiesen. Wenn Sie Anforderungen vornehmen, müssen Sie den `westus`-Endpunkt verwenden.

## <a name="new-azure-account"></a>Neues Azure-Konto

Neue Azure-Konten erhalten ein Guthaben von 200 USD, das bis zu 30 Tage lang verfügbar ist. Sie können dieses Guthaben zum weiteren Testen der Speech Services oder zur Anwendungsentwicklung verwenden.

Um sich für ein neues Azure-Konto zu registrieren, navigieren Sie zur [Azure-Registrierungsseite](https://azure.microsoft.com/free/ai/), klicken Sie auf **Kostenlos starten**, und erstellen Sie ein neues Azure-Konto mithilfe Ihres Microsoft-Kontos.

Sie können sich für ein kostenloses Microsoft-Konto im [Microsoft-Kontoportal](https://account.microsoft.com/account) registrieren. Klicken Sie zunächst auf **Mit Microsoft anmelden** und dann auf **Konto erstellen**, wenn Sie zur Anmeldung aufgefordert werden. Führen Sie die Schritte zum Erstellen und Überprüfen Ihres neuen Microsoft-Kontos aus.

Nachdem Sie das Azure-Konto erstellt haben, führen Sie die Schritte im nächsten Abschnitt aus, um ein Abonnement für die Speech Services zu erstellen.

## <a name="create-a-speech-resource-in-azure"></a>Erstellen einer Speech-Ressource in Azure

So fügen Sie Ihrem (kostenlosen oder kostenpflichtigen) Azure-Konto eine Speech Services-Ressource hinzu

1. Melden Sie sich mit Ihrem Microsoft-Konto am [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie links oben im Portal die Option **Ressource erstellen** aus.

    ![Erstellen einer Ressource](media/index/try-speech-api-create-speech1.png)

1. Suchen Sie im Fenster **Neu** nach **Sprache**.

1. Wählen Sie in den Suchergebnissen **Sprache** aus.

    ![Auswählen von „Sprache“](media/index/try-speech-api-create-speech2.png)

1. Wählen Sie unter **Sprache** die Schaltfläche **Erstellen** aus.

    ![Auswählen der Schaltfläche „Erstellen“](media/index/try-speech-api-create-speech3.png)

1. Geben Sie unter **Erstellen** Folgendes ein:

   * Einen Namen für die neue Ressource. Über den Namen können Sie mehrere Abonnements für denselben Dienst unterscheiden.
   * Wählen Sie das Azure-Abonnement aus, dem die neue Ressource zugeordnet ist, um zu bestimmen, wie die Gebühren berechnet werden.
   * Wählen Sie die Region aus, in der die Ressource verwendet wird. Derzeit sind die Speech Services in den Regionen „Asien, Osten“, „Europa, Norden“ und „USA, Westen“ verfügbar.
   * Wählen Sie einen kostenlosen oder einen kostenpflichtigen Tarif aus. Klicken Sie auf **Alle Preisinformationen anzeigen**, um vollständige Informationen zu Preisen und Verwendungskontingenten für die einzelnen Tarife zu erhalten.
   * Erstellen Sie eine neue Ressourcengruppe für dieses Abonnement für den Sprachdienst, oder weisen Sie das Abonnement einer vorhandenen Ressourcengruppe zu. Anhand von Ressourcengruppen können Sie Ihre verschiedenen Azure-Abonnements organisieren.
   * Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, um zukünftig einfach auf Ihr Abonnement zugreifen zu können.
   * Klicken Sie auf **Erstellen**.

     ![Auswählen der Schaltfläche „Erstellen“](media/index/try-speech-api-create-speech4.png)

     Es dauert einen Moment, bis die neue Sprachressource erstellt und bereitgestellt wird. Wählen Sie **Schnellstart** aus, um Informationen zu Ihrer neuen Ressource anzuzeigen.

     ![Bereich „Schnellstart“](media/index/try-speech-api-create-speech5.png)

1. Klicken Sie unter **Schnellstart** auf den Link **Schlüssel** unter Schritt 1, um Ihre Abonnementschlüssel anzuzeigen. Jedem Abonnement sind zwei Schlüssel zugewiesen. Sie können jeden dieser Schlüssel in Ihrem Abonnement verwenden. Wählen Sie jeweils die Schaltfläche neben einem Schlüssel aus, um ihn in die Zwischenablage zu kopieren und in den Code einzufügen.

> [!NOTE]
> Sie können beliebig viele Abonnements mit dem Tarif „Standard“ in einer oder mehreren Regionen erstellen. Sie können dagegen nur ein Abonnement mit dem Tarif „Free“ erstellen. Modellimplementierungen im Tarif „Free“, die 7 Tage lang ungenutzt bleiben, werden automatisch außer Betrieb genommen.

## <a name="switch-to-a-new-subscription"></a>Wechseln zu einem neuen Abonnement

Um von einem Abonnement zu einem anderen zu wechseln (z.B. wenn Ihre kostenlose Testversion abläuft oder wenn Sie Ihre Anwendung veröffentlichen), ersetzen Sie die Region und den Abonnementschlüssel in Ihrem Code durch die Region und den Abonnementschlüssel der neuen Azure-Ressource.

> [!NOTE]
> Schlüssel der kostenlosen Testversion werden in der Region „USA, Westen“ (`westus`) erstellt. Ein über das Azure-Dashboard erstelltes Abonnement kann sich in einer anderen Region befinden, wenn Sie dies wünschen.

* Wenn Ihre Anwendung ein [Speech SDK](speech-sdk.md) verwendet, geben Sie beim Erstellen einer Sprachkonfiguration den Regionscode (z.B. `westus`) an.
* Wenn Ihre Anwendung eine der [REST-APIs](rest-apis.md) der Speech Services verwendet, ist die Region Teil des Endpunkt-URI, den Sie bei der Anforderung verwenden.

Schlüssel, die für eine Region erstellt wurden, sind nur in dieser Region gültig. Der Versuch, sie mit anderen Regionen zu verwenden, führt zu Authentifizierungsfehlern.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie einen unserer 10-Minuten-Schnellstarts durch, oder sehen Sie sich unsere SDK-Beispiele an:

> [!div class="nextstepaction"]
> [Schnellstart: Erkennen von Sprache in C#](quickstart-csharp-dotnet-windows.md)
> [Speech SDK-Beispiele](speech-sdk.md#get-the-samples)
