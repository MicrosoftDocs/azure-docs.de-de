---
title: Erstellen und Verwalten von Telemetrieregeln in Ihre Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Azure IoT Central-Telemetrieregeln ermöglichen Ihnen, Ihre Geräte nahezu in Echtzeit zu überwachen und Aktionen, wie das Senden einer E-Mail, durch Auslösen der Regel automatisch aufzurufen.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8684301b83e01989c745b63848995142cb766188
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052968"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Erstellen einer Telemetrieregel und Einrichten von Benachrichtigungen in Ihrer Azure IoT Central-Anwendung

*Dieser Artikel gilt für Betreiber, Ersteller und Administratoren.*

Mithilfe von Azure IoT Central können Sie Ihre verbundenen Geräte remote überwachen. Azure IoT Central-Regeln ermöglichen Ihnen, Ihre Geräte nahezu in Echtzeit zu überwachen und automatisch Aktionen aufzurufen, z. B. das Senden einer E-Mail oder das Auslösen von Microsoft Flow. Mit nur wenigen Mausklicks können Sie die Bedingung für die Überwachung Ihrer Gerätedaten definieren und die entsprechende Aktion konfigurieren. In diesem Artikel wird erläutert, wie Regeln zum Überwachen von Telemetriedaten erstellt werden, die vom Gerät gesendet werden.

Geräte können mithilfe von Telemetriemessungen numerische Daten vom Gerät senden. Eine Telemetrieregel wird ausgelöst, wenn die ausgewählten Gerätetelemetriedaten einen angegebenen Schwellenwert überschreiten.

## <a name="create-a-telemetry-rule"></a>Erstellen einer Telemetrieregel

Um eine Telemetrieregel zu erstellen, muss für die Gerätevorlage mindestens eine Telemetriemessung definiert sein. In diesem Beispiel wird ein gekühlter Verkaufsautomat verwendet, der Temperatur- und Feuchtetelemetriedaten sendet. Die Regel überwacht die vom Gerät gemeldete Temperatur und sendet eine E-Mail, sobald diese 80 Grad übersteigt.

1. Navigieren Sie über die Seite **Gerätevorlagen** zu der Gerätevorlage, für die Sie die Regel hinzufügen.

1. Wenn Sie noch keine Regeln erstellt haben, gelangen Sie auf folgenden Bildschirm:

    ![Noch keine Regeln](media/howto-create-telemetry-rules/rules_landing_page1.png)

1. Wählen Sie auf der Registerkarte **Regeln** die Option **+ Neue Regel** aus, um die Regeltypen anzuzeigen, die Sie erstellen können.

1. Klicken Sie auf **Telemetrie**, um eine Regel zum Überwachen von Gerätetelemetriedaten zu erstellen.

    ![Regeltypen](media/howto-create-telemetry-rules/rule_types1.png)

1. Geben Sie einen aussagekräftigen Namen ein, damit Sie die Regel in dieser Gerätevorlage einfach identifizieren können.

1. Um die Regel sofort für alle Geräte zu aktivieren, die für diese Vorlage erstellt wurden, aktivieren Sie die Option **Regel für alle Geräte für diese Vorlage aktivieren**.

   ![Regeldetails](media/howto-create-telemetry-rules/rule_detail1.png)

    Die Regel gilt automatisch für alle Geräte unter der Gerätevorlage.

### <a name="configure-the-rule-conditions"></a>Konfigurieren der Regelbedingungen

Eine Bedingung definiert die Kriterien, die von der Regel überwacht werden.

1. Klicken Sie neben **Bedingungen** auf **+** , um eine neue Bedingung hinzuzufügen.

1. Wählen Sie in der Dropdownliste **Messung** die Telemetriedaten aus, die Sie überwachen möchten.

1. Wählen Sie anschließend die **Aggregation** und den **Operator** aus, und geben Sie einen **Schwellenwert** an.
   - Die Aggregation ist optional. Ohne Aggregation wird diese Regel für jeden Telemetriedatenpunkt ausgelöst, der die Bedingung erfüllt. Wenn beispielswiese die Regel so konfiguriert ist, dass sie bei einem Temperaturwert über 80 ausgelöst wird, erfolgt die Auslösung nahezu sofort, wenn das Gerät eine Temperatur von mehr als 80 meldet.
   - Wenn Sie eine Aggregatfunktion wie „Durchschnitt“, „Min“, „Max“, „Anzahl“ auswählen, müssen Sie ein **Aggregatzeitfenster** angeben, in dem die Bedingung ausgewertet werden soll. Wenn Sie beispielsweise den Zeitraum auf „5 Minuten“ festlegen und die Regel nach einer Durchschnittstemperatur von über 80 sucht, löst die Regel aus, wenn die Durchschnittstemperatur für mindestens 5 Minuten 80 Grad übersteigt. Die Regelauswertungshäufigkeit ist identisch mit dem **Aggregationszeitfenster**. Dies bedeutet in diesem Beispiel, dass die Regel ein Mal alle 5 Minuten ausgewertet wird.

     ![Bedingung](media/howto-create-telemetry-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Weitere Telemetriemessungen können unter **Bedingung** hinzugefügt werden. Wenn mehrere Bedingungen angegeben werden, müssen alle Bedingungen erfüllt sein, damit die Regel ausgelöst wird. Jede Bedingung wird implizit durch eine „UND“-Klausel verknüpft. Wenn Sie die Aggregatfunktion verwenden, müssen alle Messungen aggregiert werden.

### <a name="configure-actions"></a>Konfigurieren von Aktionen

In diesem Abschnitt erfahren Sie, wie Aktionen eingerichtet werden, die ausgeführt werden sollen, wenn die Regel ausgelöst wird. Aktionen werden aufgerufen, wenn alle in der Regel angegebenen Bedingungen als TRUE ausgewertet werden.

1. Wählen Sie **+** neben der Option **Aktionen**. Hier sehen Sie die Liste der verfügbaren Aktionen.  

    ![Hinzufügen einer Aktion](media/howto-create-telemetry-rules/add_action1.png)

1. Wählen Sie die Aktion **E-Mail**, geben Sie eine gültige E-Mail-Adresse in das Feld **An** ein, und geben Sie eine Notiz ein, die im Text der E-Mail erscheint, wenn die Regel ausgelöst wird.

    > [!NOTE]
    > E-Mails werden nur an die Benutzer gesendet, die der Anwendung hinzugefügt wurden und sich mindestens einmal angemeldet haben. Erfahren Sie mehr über die [Benutzerverwaltung](howto-administer.md) in Azure IoT Central.

   ![Konfigurieren einer Aktion](media/howto-create-telemetry-rules/configure_action1.png)

1. Um die neue Regel zu speichern, wählen Sie **Speichern** aus. Die Regel geht innerhalb weniger Minuten live und beginnt mit der Überwachung der Telemetriedaten, die an Ihre Anwendung gesendet werden. Wenn die in der Regel festgelegte Bedingung erfüllt ist, löst die Regel die konfigurierte E-Mail-Aktion aus.

Sie können der Regel auch andere Aktionen wie Microsoft Flow und Webhooks hinzufügen. Sie können bis zu fünf Aktionen pro Regel hinzufügen.

- [Microsoft Flow-Aktion](howto-add-microsoft-flow.md) zum Initiieren eines Workflows in Microsoft Flow bei Auslösung einer Regel 
- [Webhookaktion](howto-create-webhooks.md) zum Benachrichtigen anderer Dienste bei Auslösung einer Regel

## <a name="parameterize-the-rule"></a>Parametrisieren der Regel

Regeln können bestimmte Werte aus den **Geräteeigenschaften** als Parameter ableiten. Die Verwendung von Parametern ist in Szenarien hilfreich, in denen die Telemetrieschwellenwerte für verschiedene Geräte variieren. Wenn Sie die Regel erstellen, wählen Sie eine Geräteeigenschaft, die den Schwellenwert angibt, z.B. **Maximaler Idealschwellenwert**, anstatt einen absoluten Wert, z.B. 80 Grad. Wenn die Regel ausgeführt wird, gleicht sie die Gerätetelemetrie mit dem in der Geräteeigenschaft festgelegten Wert ab.

Die Verwendung von Parametern ist eine effektive Möglichkeit, die Anzahl der zu verwaltenden Regeln pro Gerätevorlage zu reduzieren.

Aktionen können auch über **Geräteeigenschaft** als Parameter konfiguriert werden. Wenn eine E-Mail-Adresse als Eigenschaft gespeichert ist, kann sie bei der Definition der Empfängeradresse (**An**) verwendet werden.

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
