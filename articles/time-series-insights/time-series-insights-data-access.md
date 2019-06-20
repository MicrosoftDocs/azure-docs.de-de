---
title: Konfigurieren der Sicherheit für den Zugriff und die Verwaltung von Azure Time Series Insights Preview | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie die Sicherheit und Berechtigungen als Verwaltungszugriffsrichtlinien und Datenzugriffsrichtlinien konfigurieren, um Azure Time Series Insights Preview zu schützen.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 53489ef9c1264fa31b06f4660e545e6efbd1f616
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237034"
---
# <a name="grant-data-access-to-an-environment"></a>Gewähren von Datenzugriff für eine Umgebung

In diesem Artikel werden die beiden Arten von Zugriffsrichtlinien für Azure Time Series Insights Preview thematisiert.

## <a name="sign-in-to-time-series-insights"></a>Anmelden bei Time Series Insights

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie Ihre Time Series Insights-Umgebung. Geben Sie in das **Suchfeld** `Time Series` ein. Wählen Sie **Time Series Environment** (Time Series-Umgebung) in den Suchergebnissen aus.
1. Wählen Sie Ihre Time Series Insights-Umgebung in der Liste aus.

## <a name="grant-data-access"></a>Gewähren des Datenzugriffs

Führen Sie die folgenden Schritte aus, um einem Benutzerprinzipal Datenzugriff zu gewähren.

1. Wählen Sie **Datenzugriffsrichtlinien** und dann **+ Hinzufügen** aus.

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Wählen Sie **Benutzer auswählen** aus. Suchen Sie den Benutzernamen oder die E-Mail-Adresse des Benutzers, den Sie hinzufügen möchten. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Wählen Sie **Rolle auswählen** aus. Wählen Sie die geeignete Zugriffsrolle für den Benutzer aus:

    * Wählen Sie **Mitwirkender** aus, wenn Sie dem Benutzer das Ändern von Verweisdaten und das Freigeben gespeicherter Abfragen und Perspektiven für andere Benutzer der Umgebung erlauben möchten.

    * Wählen Sie andernfalls **Leser** aus, um dem Benutzer das Abfragen von Daten und das Speichern persönlicher (nicht freigegebener) Abfragen in der Umgebung zu ermöglichen.

   Wählen Sie **OK** aus, um die Rollenauswahl zu bestätigen.

    [![Data-access-three](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Wählen Sie auf der Seite **Benutzerrolle auswählen** die Option **OK** aus.

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Vergewissern Sie sich, dass auf der Seite **Datenzugriffsrichtlinien** die Benutzer und die Rollen der einzelnen Benutzer aufgeführt werden.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Bereitstellen von Gastzugriff über einen anderen AAD-Mandanten

`Guest` ist keine Verwaltungsrolle. Es ist ein Begriff, der ein Konto bezeichnet, das von einem Mandanten zu einem anderem Mandanten eingeladen wird. Nachdem das Gastkonto in das Mandantenverzeichnis eingeladen wurde, kann darauf dieselbe Zugriffssteuerung angewendet werden, wie auf jedes andere Konto. Sie können einer Time Series Insights-Umgebung Verwaltungszugriff gewähren, indem Sie das Blatt „Zugriffssteuerung (IAM)“ verwenden. Oder Sie können den Zugriff auf die Daten in der Umgebung über das Blatt „Datenzugriffsrichtlinien“ gewähren. Weitere Informationen zum Mandantengastzugriff in Azure Active Directory (Azure AD) finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Führen Sie die folgenden Schritte aus, um einem Azure AD-Benutzer über einen anderen Mandanten Zugriff auf die Time Series Insights-Umgebung zu gewähren.

1. Wählen Sie **Datenzugriffsrichtlinien** und dann **+ Einladen** aus.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie einladen möchten. Diese E-Mail-Adresse muss Azure AD zugeordnet sein. Sie können der Einladung eine persönliche Nachricht hinzufügen.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Suchen Sie nach der Bestätigungsmeldung, die am Bildschirm angezeigt wird.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Wählen Sie **Benutzer auswählen** aus. Suchen Sie nach der E-Mail-Adresse des Gastbenutzers, den Sie eingeladen haben, um diesen hinzuzufügen. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Wählen Sie **Rolle auswählen** aus. Wählen Sie eine geeignete Zugriffsrolle für den Gastbenutzer aus:

    * Wählen Sie **Mitwirkender** aus, wenn Sie dem Benutzer das Ändern von Verweisdaten und das Freigeben gespeicherter Abfragen und Perspektiven für andere Benutzer der Umgebung erlauben möchten.

    * Wählen Sie andernfalls **Leser** aus, um dem Benutzer das Abfragen von Daten und das Speichern persönlicher (nicht freigegebener) Abfragen in der Umgebung zu ermöglichen.

   Wählen Sie **OK** aus, um die Rollenauswahl zu bestätigen.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Wählen Sie auf der Seite **Benutzerrolle auswählen** die Option **OK** aus.

1. Vergewissern Sie sich, dass auf der Seite **Datenzugriffsrichtlinien** die Gastbenutzer mit denen ihnen zugewiesenen Rollen aufgeführt werden.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Anschließend muss der Gastbenutzer einige Schritte ausführen, um auf die Umgebung zuzugreifen, die sich in dem Azure-Mandanten befindet, zu dem Sie ihn eingeladen haben. Zunächst muss er die Einladung annehmen, die Sie ihm gesendet haben. Diese Einladung wird per E-Mail an die E-Mail-Adresse gesendet, die Sie in Schritt 5 verwendet haben. Der Benutzer wählt **Get Started** (Erste Schritte) aus, um die Einladung anzunehmen.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Anschließend muss er die Berechtigungen annehmen, die der Organisation des Administrators zugeordnet sind.

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Nachdem sich der Gastbenutzer bei der E-Mail-Adresse angemeldet hat, mit der Sie ihn eingeladen hatten, und dieser die Einladung annimmt, wechselt er zu „insights.azure.com“. Auf dieser Seite wählt er dann den Avatar aus, der neben seiner E-Mail-Adresse in der rechten oberen Ecke des Bildschirms angezeigt wird.

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Anschließend muss der Gastbenutzer Ihren Azure-Mandanten aus dem Dropdownmenü für das Verzeichnis auswählen. Dies ist der Mandant, zu dem Sie ihn eingeladen haben.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Nachdem der Gastbenutzer Ihren Mandanten ausgewählt hat, wird ihm die Time Series Insights-Umgebung angezeigt, zu der Sie ihm Zugriff gewährt haben. Er verfügt nun über alle Funktionen, die mit der Rolle verbunden sind, die Sie ihm in **Schritt 5** zugewiesen haben.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie Ihrer Time Series Insights-Umgebung [Azure Event Hubs-Ereignisquelle hinzufügen](./time-series-insights-how-to-add-an-event-source-eventhub.md).

* Senden [von Ereignissen an die Ereignisquelle](./time-series-insights-send-events.md).

* Anzeigen [Ihrer Umgebung im Times Series Insights Preview-Explorer](./time-series-insights-update-explorer.md).
