---
title: Verzögern der nächsten Aktion in Workflows – Azure Logic Apps
description: In diesem Artikel wird erläutert, wie Sie mit dem Ausführen der nächsten Aktion in Logik-App-Workflows warten, indem Sie die Aktion „Verzögern“ oder „Verzögern bis“ in Azure Logic Apps verwenden.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297602"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Verzögern der Ausführung der nächsten Aktion in Azure Logic Apps

Damit Ihre Logik-App vor dem Ausführen der nächsten Aktion eine bestimmte Zeit wartet, können Sie die integrierte Aktion **Verzögern – Zeitplan** vor einer anderen Aktion im Workflow Ihrer Logik-App hinzufügen. Alternativ dazu können Sie auch die Aktion **Verzögern bis – Zeitplan** hinzufügen, damit mit dem Ausführen der nächsten Aktion bis zu einem bestimmten Datum und zu einer bestimmten Uhrzeit gewartet wird. Weitere Informationen zu den integrierten Zeitplanaktionen und -triggern finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Verzögern**: Es wird eine bestimmte Anzahl von Zeiteinheiten lang, z.B. Sekunden, Minuten, Stunden, Tage, Wochen oder Monate, auf die Ausführung der nächsten Aktion gewartet.

* **Verzögern bis**: Mit der Ausführung der nächsten Aktion wird bis zum angegebenen Datum und zur angegebenen Uhrzeit gewartet.

Im Folgenden finden Sie einige Beispiele für die Verwendung dieser Aktion:

* Versand einer E-Mail mit einem Statusupdate bis zum Erreichen eines Werktags verzögern.

* Verzögern des Workflows, bis ein HTTP-Aufruf beendet wird, bevor der Workflow fortgesetzt und die Daten abgerufen werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Grundlegende Kenntnisse zu [Logik-Apps](../logic-apps/logic-apps-overview.md). Bevor Sie eine Aktion verwenden können, muss Ihre Logik-App zunächst mit einem Trigger gestartet werden. Sie können jeden beliebigen Trigger verwenden und vor einer Verzögerungsaktion andere Aktionen hinzufügen. In diesem Thema wird ein Office 365-Outlook-Trigger verwendet. Falls Sie noch nicht mit Logik-Apps gearbeitet haben, sollten Sie zunächst die Schnellstartanleitung zum [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) lesen.

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Hinzufügen der Aktion „Verzögern“

1. Wählen Sie im Logik-App-Designer in dem Schritt, in dem Sie die Verzögerungsaktion hinzufügen möchten, die Option **Neuer Schritt** aus.

   Wenn die Verzögerungsaktion zwischen zwei Schritten hinzugefügt werden soll, bewegen Sie den Mauszeiger über den Pfeil, der die Schritte verbindet. Klicken Sie auf das angezeigte Pluszeichen (+), und wählen Sie **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld den Begriff „Verzögern“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Verzögern**

   ![Hinzufügen der Aktion „Verzögern“](./media/connectors-native-delay/add-delay-action.png)

1. Geben Sie an, wie lange bis zur Ausführung der nächsten Aktion gewartet werden soll.

   ![Festlegen der Zeitspanne für die Verzögerung](./media/connectors-native-delay/delay-time-intervals.png)

   | Eigenschaft | JSON-Name | Erforderlich | Typ | BESCHREIBUNG |
   |----------|-----------|----------|------|-------------|
   | Count | count | Ja | Integer | Die Anzahl von Zeiteinheiten für die Verzögerung. |
   | Unit | unit | Ja | Zeichenfolge | Die Zeiteinheit, z.B.: `Second`, `Minute`, `Hour`, `Day`, `Week` oder `Month` |
   ||||||

1. Fügen Sie weitere Aktionen hinzu, die in Ihrem Workflow ausgeführt werden sollen.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Hinzufügen der Aktion „Verzögern bis“

1. Wählen Sie im Logik-App-Designer in dem Schritt, in dem Sie die Verzögerungsaktion hinzufügen möchten, die Option **Neuer Schritt** aus.

   Wenn die Verzögerungsaktion zwischen zwei Schritten hinzugefügt werden soll, bewegen Sie den Mauszeiger über den Pfeil, der die Schritte verbindet. Klicken Sie auf das angezeigte Pluszeichen (+), und wählen Sie **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld den Begriff „Verzögern“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Verzögern bis**

   ![Hinzufügen der Aktion „Verzögern bis“](./media/connectors-native-delay/add-delay-until-action.png)

1. Geben Sie das Datum und die Uhrzeit für den Zeitpunkt ein, an dem der Workflow fortgesetzt werden soll.

   ![Angeben des Zeitstempels für das Ende der Verzögerung](./media/connectors-native-delay/delay-until-timestamp.png)

   | Eigenschaft | JSON-Name | Erforderlich | Typ | BESCHREIBUNG |
   |----------|-----------|----------|------|-------------|
   | Zeitstempel | timestamp | Ja | Zeichenfolge | Das Datum und die Uhrzeit für die Fortsetzung des Workflows in folgendem Format: <p>YYYY-MM-DDThh:mm:ssZ <p>Beispiel: Für den 18. September 2017 um 14:00 Uhr würden Sie „2017-09-18T14:00:00Z“ angeben. <p>**Hinweis:** Dieses Zeitformat muss dem [ISO 8601-Format für Datums-/Uhrzeitangaben](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) entsprechen und im [UTC-Datums-/Uhrzeitformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) angegeben werden, jedoch ohne [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset). Wenn Sie keine Zeitzone auswählen, müssen Sie den Buchstaben „Z“ ohne Leerzeichen anhängen. „Z“ bezieht sich auf die entsprechende [nautische Zeit](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Fügen Sie weitere Aktionen hinzu, die in Ihrem Workflow ausgeführt werden sollen.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Serientrigger](../connectors/connectors-native-recurrence.md)
* [Connectors für Logic Apps](../connectors/apis-list.md)