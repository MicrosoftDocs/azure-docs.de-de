---
title: Testen und Veröffentlichen eines Azure-Anwendungsangebots
description: Verwenden Sie Partner Center, um Ihr Azure-Anwendungsangebot für die Vorschau zu übermitteln, eine Vorschau Ihres Angebots anzuzeigen und es dann zu testen und im kommerziellen Microsoft-Marketplace zu veröffentlichen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620918"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Testen und Veröffentlichen eines Azure-Anwendungsangebots

In diesem Artikel erfahren Sie, wie Sie mithilfe von Partner Center Ihr Azure-Anwendungsangebot für die Veröffentlichung übermitteln, eine Vorschau Ihres Angebots anzeigen und es testen und dann im kommerziellen Marketplace live veröffentlichen. Sie müssen bereits ein Angebot erstellt haben, das Sie veröffentlichen möchten.

## <a name="submit-your-offer-for-publishing"></a>Übermitteln des Angebots für die Veröffentlichung

1. Melden Sie sich im [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) beim Dashboard „Kommerzieller Marketplace“ an.
1. Wählen Sie auf der Seite **Übersicht** das Angebot aus, das Sie veröffentlichen möchten.
1. Wählen Sie in der oberen rechten Ecke des Portals **Überprüfen und veröffentlichen** aus.
1. Stellen Sie sicher, dass auf jeder Seite in der Spalte **Status** der Eintrag **Abgeschlossen** angezeigt wird. Die drei möglichen Statuswerte lauten wie folgt:
    - **Nicht gestartet**: Die Seite ist unvollständig.
    - **Unvollständig**: Auf der Seite fehlen erforderliche Informationen, oder sie enthält Fehler, die korrigiert werden müssen. Sie müssen zur Seite zurückkehren und diese aktualisieren.
    - **Abgeschlossen**: Die Seite ist vollständig. Alle erforderlichen Daten wurden angegeben, und es liegen keine Fehler vor.
1. Wenn eine der Seiten einen anderen Status als **Abgeschlossen** aufweist, wählen Sie den Namen der Seite aus, beheben das Problem, speichern die Seite und wählen dann erneut **Überprüfen und veröffentlichen** aus, um zu dieser Seite zurückzukehren.
1. Nachdem alle Seiten abgeschlossen sind, stellen Sie dem Zertifizierungsteam unter **Hinweise für Zertifizierung** Testanweisungen zur Verfügung, um sicherzustellen, dass Ihre App ordnungsgemäß getestet wird. Stellen Sie alle ergänzenden Anmerkungen bereit, die das Verständnis Ihrer App unterstützen.
1. Wählen Sie **Veröffentlichen** aus, um den Veröffentlichungsprozess für Ihr Angebot zu starten. Die Seite **Angebotsübersicht** wird mit dem **Veröffentlichungsstatus** des Angebots angezeigt.

Der Veröffentlichungsstatus Ihres Angebots ändert sich, während es den Veröffentlichungsprozess durchläuft. Ausführliche Informationen zu diesem Prozess finden Sie unter [Schritte für die Validierung und Veröffentlichung](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Vorschau und Test Ihres Angebots

Wenn das Angebot für Ihre Genehmigung bereit ist, senden wir Ihnen eine E-Mail mit der Bitte, Ihre Angebotsvorschau zu überprüfen und zu genehmigen. Sie können auch die Seite **Angebotsübersicht** in Ihrem Browser aktualisieren, um festzustellen, ob Ihr Angebot die Phase „Einholen der Genehmigung des Herausgebers“ erreicht hat. Wenn dies der Fall ist, sind die Schaltfläche **Live schalten** und ein Vorschaulink verfügbar. Wenn Sie sich für den Verkauf Ihres Angebots über Microsoft entschieden haben, können alle Mitglieder der Vorschauzielgruppe den Kauf und die Bereitstellung Ihres Angebots testen, um sicherzustellen, dass es Ihre Anforderungen in dieser Phase erfüllt.

Der folgende Screenshot zeigt die Seite **Angebotsübersicht** für ein Angebot mit zwei Vorschaulinks unter der Schaltfläche **Live schalten**. Die auf dieser Seite angezeigten Validierungsschritte variieren je nach der Auswahl, die Sie beim Erstellen des Angebots getroffen haben.

[![Darstellung der Seite „Angebotsübersicht“ für ein Angebot in Partner Center. Die Schaltfläche „Live schalten“ und Vorschaulinks werden angezeigt.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Gehen Sie wie folgt vor, um Ihr Angebot in der Vorschau anzuzeigen.

1. Wählen Sie auf der Seite **Angebotsübersicht** unter der Schaltfläche **Live schalten** einen Vorschaulink aus. 

1. Um den gesamten Ablauf vom Kauf bis zur Einrichtung zu überprüfen, erwerben Sie Ihr Angebot, während es sich in der Vorschau befindet. Benachrichtigen Sie zunächst Microsoft mit einem [Supportticket](https://aka.ms/marketplacesupport), um sicherzustellen, dass wir keine Gebühren erheben.

1. Wenn Ihr Azure-Anwendungsangebot die [getaktete Abrechnung mit dem Messungsdienst des kommerziellen Marketplace](./partner-center-portal/azure-app-metered-billing.md) unterstützt, lesen und befolgen Sie die bewährten Methoden für Tests, die unter [APIs für getaktete Abrechnung im Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices) beschrieben sind.

1. Wenn Sie nach der Vorschau und den Tests Änderungen am Angebot vornehmen möchten, können Sie es bearbeiten und erneut übermitteln, um eine neue Vorschau zu veröffentlichen. Weitere Informationen finden Sie unter [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Veröffentlichen Ihres Angebots im Livemodus

Nachdem Sie Ihre Vorschau vollständig getestet haben, wählen Sie **Live schalten** aus, um das Angebot live im kommerziellen Marketplace zu veröffentlichen.

   > [!TIP]
   > Wenn Ihr Angebot bereits im kommerziellen Marketplace live geschaltet ist, werden alle von Ihnen vorgenommenen Aktualisierungen erst dann live geschaltet, wenn Sie **Live schalten** auswählen.

Nachdem Sie sich entschieden haben, Ihr Angebot im kommerziellen Marketplace verfügbar zu machen, stellen wir mit einer Reihe abschließender Validierungsprüfungen sicher, dass das Liveangebot auf dieselbe Weise wie die Vorschauversion des Angebots konfiguriert ist. Ausführliche Informationen zu diesen Validierungsprüfungen finden Sie unter [Phase „Veröffentlichung“](review-publish-offer.md#publish-phase).

Nachdem die Validierungsprüfungen abgeschlossen wurden, wird Ihr Angebot im Marketplace live geschaltet.

### <a name="errors-and-review-feedback"></a>Fehler und Prüfungsfeedback

Der Schritt **Manuelle Validierung** im Veröffentlichungsvorgang stellt eine umfassende Überprüfung Ihres Angebots und der damit verbundenen technischen Ressourcen (insbesondere der Azure Resource Manager-Vorlage) dar. Probleme werden in der Regel als Pull Request-Links dargestellt. Eine Erklärung, wie Sie diese Pull Requests anzeigen und auf sie reagieren, finden Sie unter [Verarbeiten von Prüfungsfeedback](partner-center-portal/azure-apps-review-feedback.md).

Wenn Fehler bei einem oder mehreren Veröffentlichungsschritten aufgetreten sind, beheben Sie diese, bevor Sie Ihr Angebot erneut veröffentlichen.

## <a name="next-step"></a>Nächster Schritt

- [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](partner-center-portal/analytics.md)
- Erfahren Sie, wie Sie die Programme „Co-Selling mit Microsoft“ und „Verkaufen über CSPs“ für das [Verkaufen Ihres Azure-Anwendungsangebots](create-new-azure-apps-offer-marketing.md) nutzen.
