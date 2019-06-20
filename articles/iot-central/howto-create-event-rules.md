---
title: Erstellen und Verwalten von Ereignisregeln in Ihre Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Azure IoT Central-Ereignisregeln ermöglichen Ihnen, Ihre Geräte nahezu in Echtzeit zu überwachen und Aktionen, wie das Senden einer E-Mail, durch Auslösen der Regel automatisch aufzurufen.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4754e6b571845d286ef22014f87b86fae2f6633d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053020"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Erstellen einer Ereignisregel und Einrichten von Benachrichtigungen in Ihrer Azure IoT Central-Anwendung

*Dieser Artikel gilt für Betreiber, Ersteller und Administratoren.*

Mithilfe von Azure IoT Central können Sie Ihre verbundenen Geräte remote überwachen. Azure IoT Central-Regeln ermöglichen Ihnen, Ihre Geräte nahezu in Echtzeit zu überwachen und automatisch Aktionen aufzurufen, z. B. das Senden einer E-Mail oder das Auslösen von Microsoft Flow. Mit nur wenigen Mausklicks können Sie die Bedingung definieren, um Ihre Gerätedaten zu überwachen, und die entsprechende Aktion konfigurieren. In diesem Artikel wird erläutert, wie Regeln zum Überwachen von Ereignissen erstellt werden, die vom Gerät gesendet werden.

Geräte können Ereignismessungen zum Senden von wichtigen Geräteereignissen oder Informationen verwenden. Eine Ereignisregel wird ausgelöst, wenn das ausgewählte Geräteereignis vom Gerät gemeldet wird.

## <a name="create-an-event-rule"></a>Erstellen einer Ereignisregel

Um eine Ereignisregel zu erstellen, muss für die Gerätevorlage mindestens eine Ereignismessung definiert sein. In diesem Beispiel wird ein Warenautomat mit Kühlung verwendet, der einen Fehler des Lüftermotors meldet. Die Regel überwacht das vom Gerät gemeldete Ereignis und sendet eine E-Mail, sobald das Ereignis gemeldet wird.

1. Navigieren Sie über die Seite **Gerätevorlagen** zu der Gerätevorlage, für die Sie die Regel hinzufügen.

1. Wenn Sie noch keine Regeln erstellt haben, gelangen Sie auf folgenden Bildschirm:

    ![Noch keine Regeln](media/howto-create-event-rules/rules_landing_page1.png)

1. Wählen Sie auf der Registerkarte **Regeln** die Option **+ Neue Regel** aus, um die Regeltypen anzuzeigen, die Sie erstellen können.

1. Wählen Sie die Kachel **Ereignis** aus, um eine Ereignisüberwachungsregel zu erstellen.

    ![Regeltypen](media/howto-create-event-rules/rule_types1.png)

1. Geben Sie einen aussagekräftigen Namen ein, damit Sie die Regel in dieser Gerätevorlage einfach identifizieren können.

1. Um die Regel sofort für alle Geräte zu aktivieren, die mit dieser Vorlage erstellt wurden, aktivieren Sie die Option **Regel für alle Geräte dieser Vorlage aktivieren**.

    ![Regeldetails](media/howto-create-event-rules/rule_detail1.png)

    Die Regel gilt automatisch für alle Geräte unter der Gerätevorlage.

### <a name="configure-the-rule-conditions"></a>Konfigurieren der Regelbedingungen

Die Bedingung definiert die Kriterien, die von der Regel überwacht werden.

1. Wählen Sie **+** neben **Bedingungen** aus, um eine neue Bedingung hinzuzufügen.

1. Wählen Sie in der Dropdownliste „Messung“ das Ereignis aus, das Sie überwachen möchten. In diesem Beispiel wurde das Ereignis **Fan Motor Error** (Lüftermotorfehler) ausgewählt.

   ![Bedingung](media/howto-create-event-rules/condition_filled_out1.png)

1. Optional können Sie auch **Anzahl** als **Aggregation** festlegen und den entsprechenden Schwellenwert angeben.

   - Ohne Aggregation wird diese Regel für jeden Ereignisdatenpunkt ausgelöst, der die Bedingung erfüllt. Wenn Sie z.B. die Bedingung der Regel so konfigurieren, dass sie ausgelöst wird, wenn ein Ereignis **Fan Motor Error** auftritt, wird die Regel fast sofort ausgelöst, wenn das Gerät dieses Ereignis meldet.
   - Wenn „Anzahl“ als Aggregatfunktion verwendet wird, müssen Sie einen **Schwellenwert** und ein **Aggregatzeitfenster** angeben, in dem die Bedingung ausgewertet werden soll. In diesem Fall wird die Anzahl der Ereignisse aggregiert, und die Regel wird nur dann ausgelöst, wenn die aggregierte Ereignisanzahl mit dem Schwellenwert übereinstimmt.

     Wenn Sie z.B. bei mehr als drei Geräteereignissen innerhalb von 5 Minuten warnen möchten, wählen Sie das Ereignis aus und legen die Aggregatfunktion als „Anzahl“, den Operator als „größer als“ und den „Schwellenwert“ als 3 fest. Legen Sie „Aggregationszeitraum“ auf „5 Minuten“ fest. Die Regel wird ausgelöst, wenn mehr als drei Ereignisse innerhalb von 5 Minuten vom Gerät gesendet werden. Die Regelauswertungshäufigkeit ist identisch mit dem **Aggregationszeitfenster**. Dies bedeutet in diesem Beispiel, dass die Regel ein Mal alle 5 Minuten ausgewertet wird.

     ![Hinzufügen einer Ereignisbedingung](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Weitere Ereignismessungen können unter **Bedingung** hinzugefügt werden. Wenn mehrere Bedingungen angegeben werden, müssen alle Bedingungen erfüllt sein, damit die Regel ausgelöst wird. Jede Bedingung wird implizit durch eine „UND“-Klausel verknüpft. Wenn Sie die Aggregatfunktion verwenden, müssen alle Messungen aggregiert werden.

### <a name="configure-actions"></a>Konfigurieren von Aktionen

In diesem Abschnitt erfahren Sie, wie Aktionen eingerichtet werden, die ausgeführt werden sollen, wenn die Regel ausgelöst wird. Aktionen werden aufgerufen, wenn alle in der Regel angegebenen Bedingungen als TRUE ausgewertet werden.

1. Wählen Sie **+** neben der Option **Aktionen**. Hier sehen Sie die Liste der verfügbaren Aktionen.

    ![Hinzufügen einer Aktion](media/howto-create-event-rules/add_action1.png)

1. Wählen Sie die Aktion **E-Mail**, geben Sie eine gültige E-Mail-Adresse in das Feld **An** ein, und geben Sie eine Notiz ein, die im Text der E-Mail erscheint, wenn die Regel ausgelöst wird.

    > [!NOTE]
    > E-Mails werden nur an die Benutzer gesendet, die der Anwendung hinzugefügt wurden und sich mindestens einmal angemeldet haben. Erfahren Sie mehr über die [Benutzerverwaltung](howto-administer.md) in Azure IoT Central.

   ![Konfigurieren einer Aktion](media/howto-create-event-rules/configure_action1.png)

1. Um die neue Regel zu speichern, wählen Sie **Speichern** aus. Die Regel geht innerhalb weniger Minuten live und beginnt mit der Überwachung der Ereignisse, die an Ihre Anwendung gesendet werden. Wenn die in der Regel festgelegte Bedingung erfüllt ist, löst die Regel die konfigurierte E-Mail-Aktion aus.

Sie können der Regel auch andere Aktionen wie Microsoft Flow und Webhooks hinzufügen. Sie können bis zu fünf Aktionen pro Regel hinzufügen.

- [Microsoft Flow-Aktion](howto-add-microsoft-flow.md) zum Initiieren eines Workflows in Microsoft Flow bei Auslösung einer Regel 
- [Webhookaktion](howto-create-webhooks.md) zum Benachrichtigen anderer Dienste bei Auslösung einer Regel

## <a name="parameterize-the-rule"></a>Parametrisieren der Regel

Aktionen können auch über **Geräteeigenschaft** als Parameter konfiguriert werden. Wenn eine E-Mail-Adresse als Geräteeigenschaft gespeichert ist, kann sie bei der Definition der Empfängeradresse **verwendet** werden.

## <a name="delete-a-rule"></a>Löschen einer Regel

Wenn Sie eine Regel nicht mehr benötigen, löschen Sie sie, indem Sie die Regel öffnen und **Löschen** wählen. Das Löschen der Regel entfernt sie aus der Gerätevorlage und allen zugehörigen Geräten.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Aktivieren oder Deaktivieren einer Regel für eine Gerätevorlage

Navigieren Sie zu dem Gerät, und wählen Sie die Regel, die Sie aktivieren oder deaktivieren möchten. Aktivieren Sie in der Regel die Option **Regel für alle Geräte dieser Vorlage aktivieren**, um die Regel für alle Geräte zu aktivieren bzw. zu deaktivieren, die der Gerätevorlage zugeordnet sind.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Aktivieren oder Deaktivieren einer Regel für ein Gerät

Navigieren Sie zu dem Gerät, und wählen Sie die Regel, die Sie aktivieren oder deaktivieren möchten. Schalten Sie die Option **Regel für dieses Gerät aktivieren** ein bzw. aus, je nachdem, ob Sie die Regel für dieses Gerät aktivieren bzw. deaktivieren möchten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Regeln in Ihrer Azure IoT Central-Anwendung erstellt werden, können Sie die nächsten Schritte ausführen:

- [Hinzufügen einer Microsoft Flow-Aktion in Regeln](howto-add-microsoft-flow.md)
- [Hinzufügen einer Webhookaktion in Regeln](howto-create-webhooks.md)
- [Gruppieren mehrerer Aktionen für die Ausführung über eine oder mehrere Regeln](howto-use-action-groups.md)
- [Verwalten von Geräten](howto-manage-devices.md)
