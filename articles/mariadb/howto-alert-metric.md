---
title: Konfigurieren von Warnungen zu Metriken – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal die Warnungen zu Metriken für Azure Database for MariaDB konfigurieren und auf diese zugreifen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: e108ca3aa346af20b31ff446130014e1250f49c0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541078"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for MariaDB

In diesem Artikel erfahren Sie, wie Sie mit dem Azure-Portal Azure Database for MariaDB-Warnungen einrichten können. Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services eine Warnung empfangen.

Die Warnung wird ausgelöst, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung wird sowohl ausgelöst, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird.

Sie können konfigurieren, dass bei einer Warnung die folgenden Aktionen ausgeführt werden, wenn sie ausgelöst wird:
* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mails an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks

Sie haben folgende Möglichkeiten zum Konfigurieren von Warnungsregeln und Abrufen zugehöriger Informationen:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-Befehlszeilenschnittstelle](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor-REST-API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Erstellen einer Warnungsregel für eine Metrik
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) den zu überwachenden Azure Database for MariaDB-Server aus.

2. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Warnungen** aus, wie unten gezeigt:

   ![„Warnungsregeln“ auswählen](./media/howto-alert-metric/2-alert-rules.png)

3. Wählen Sie **Metrikwarnung hinzufügen** (Plussymbol) aus.

4. Die Seite **Regel erstellen** wird geöffnet, wie unten gezeigt. Füllen Sie die erforderlichen Informationen aus:

   ![Formular „Metrikwarnung hinzufügen“](./media/howto-alert-metric/4-add-rule-form.png)

5. Wählen Sie im Abschnitt **Bedingung** **Bedingung hinzufügen**.

6. Wählen Sie eine Metrik aus der Liste der Signale aus, bei denen eine Warnung erfolgen soll. Wählen Sie in diesem Beispiel „Speicher in Prozent“ aus.
   
   ![Metrik auswählen](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Konfigurieren Sie die Warnungslogik, einschließlich der **Bedingung** (z.B. „Größer als“), **Schwellenwert** (z.B. 85 Prozent), **Zeitaggregation**, **Zeitraum**, die die Metrikregel erfüllen muss, ehe die Warnung ausgelöst wird (z.B. „Innerhalb der letzten 30 Minuten“) und **Häufigkeit**.
   
   Wählen Sie anschließend **Fertig** aus.

   ![Metrik 2 auswählen](./media/howto-alert-metric/7-set-threshold-time.png)

8. Wählen Sie im Abschnitt **Aktionsgruppen** die Option **Neu erstellen** aus, um eine neue Gruppe zum Empfangen von Benachrichtigungen zu Warnungen zu erhalten.

9. Tragen Sie in das Formular „Aktionsgruppe hinzufügen“ einen Namen, Kurznamen, ein Abonnement und eine Ressourcengruppe ein.

10. Konfigurieren Sie den Aktionstyp **E-Mail/SMS/Push/Sprachanruf**.
    
    Wählen Sie „E-Mail an Azure Resource Manager-Rolle“ aus, um Besitzer, Mitwirkende und Leser des Abonnements auszuwählen, die Benachrichtigungen erhalten sollen.
   
    Geben Sie optional einen gültigen URI im Feld **Webhook** an, wenn dieser bei Auslösen der Warnung aufgerufen werden soll.

    Wählen Sie **OK** aus, wenn Sie fertig sind.

    ![Aktionsgruppe](./media/howto-alert-metric/10-action-group-type.png)

11. Geben Sie einen Namen, einen Beschreibung und den Schweregrad für die Warnungsregel an.

    ![Aktionsgruppe 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. Wählen Sie **Benachrichtigungsregel erstellen** aus, um die Benachrichtigung zu erstellen.

    Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

## <a name="manage-your-alerts"></a>Verwalten Ihrer Warnungen
Nachdem Sie eine Warnung erstellt haben, können Sie sie auswählen und folgende Aktionen ausführen:

* Ein Diagramm anzeigen, das den Schwellenwert der Metrik und die tatsächlichen Werte vom Vortag zeigt, die für diese Warnung relevant sind.
* Die Warnungsregel **bearbeiten** oder **löschen**.
* Die Warnung **deaktivieren** oder **aktivieren**, wenn Sie den Empfang von Benachrichtigungen vorübergehend beenden oder fortsetzen möchten.


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Konfigurieren von Webhooks in Warnungen](../azure-monitor/platform/alerts-webhooks.md).
* Verschaffen Sie sich einen Überblick über das [Sammeln von Dienstmetriken](../azure-monitor/platform/data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.