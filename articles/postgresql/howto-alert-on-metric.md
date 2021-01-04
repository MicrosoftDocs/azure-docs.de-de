---
title: Konfigurieren von Warnungen – Azure-Portal – Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal die Warnungen zu Metriken für Azure Database for PostgreSQL (Einzelserver) konfigurieren und auf diese zugreifen.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: f37dcc1d092de10c51c0492bbc8671047e4a90a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020108"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for PostgreSQL (Einzelserver)

In diesem Artikel erfahren Sie, wie Sie mit dem Azure-Portal Azure Database for PostgreSQL-Warnungen einrichten können. Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services eine Warnung empfangen.

Die Warnung wird ausgelöst, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung wird sowohl ausgelöst, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird. 

Sie können konfigurieren, dass bei einer Warnung die folgenden Aktionen ausgeführt werden, wenn sie ausgelöst wird:
* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mails an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks

Sie haben folgende Möglichkeiten zum Konfigurieren von Warnungsregeln und Abrufen zugehöriger Informationen:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-Befehlszeilenschnittstelle](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor-REST-API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Erstellen einer Warnungsregel anhand einer Metrik aus dem Azure-Portal
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) den zu überwachenden Azure Database for PostgreSQL-Server aus.

2. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Warnungen** aus, wie unten gezeigt:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="„Warnungsregeln“ auswählen":::

3. Wählen Sie **Metrikwarnung hinzufügen** (Plussymbol) aus.

4. Die Seite **Regel erstellen** wird geöffnet, wie unten gezeigt. Füllen Sie die erforderlichen Informationen aus:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Formular „Metrikwarnung hinzufügen“":::

5. Wählen Sie im Abschnitt **Bedingung** **Bedingung hinzufügen**.

6. Wählen Sie eine Metrik aus der Liste der Signale aus, bei denen eine Warnung erfolgen soll. Wählen Sie in diesem Beispiel „Speicher in Prozent“ aus.
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Metrik auswählen":::

7. Konfigurieren Sie die Warnungslogik, einschließlich der **Bedingung** (z.B. „Größer als“), **Schwellenwert** (z.B. 85 Prozent), **Zeitaggregation**, **Zeitraum**, die die Metrikregel erfüllen muss, ehe die Warnung ausgelöst wird (z.B. „Innerhalb der letzten 30 Minuten“) und **Häufigkeit**.
   
   Wählen Sie anschließend **Fertig** aus.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Screenshot mit hervorgehobenem Abschnitt „Warnungslogik“ und der Schaltfläche „Fertig“":::

8. Wählen Sie im Abschnitt **Aktionsgruppen** die Option **Neu erstellen** aus, um eine neue Gruppe zum Empfangen von Benachrichtigungen zu Warnungen zu erhalten.

9. Tragen Sie in das Formular „Aktionsgruppe hinzufügen“ einen Namen, Kurznamen, ein Abonnement und eine Ressourcengruppe ein.

10. Konfigurieren Sie den Aktionstyp **E-Mail/SMS/Push/Sprachanruf**.
    
    Wählen Sie „E-Mail an Azure Resource Manager-Rolle“ aus, um Besitzer, Mitwirkende und Leser des Abonnements auszuwählen, die Benachrichtigungen erhalten sollen.
   
    Geben Sie optional einen gültigen URI im Feld **Webhook** an, wenn dieser bei Auslösen der Warnung aufgerufen werden soll.

    Wählen Sie **OK** aus, wenn Sie fertig sind.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Screenshot des Hinzufügens einer neuen Aktionsgruppe":::

11. Geben Sie einen Namen, einen Beschreibung und den Schweregrad für die Warnungsregel an.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Aktionsgruppe"::: 

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