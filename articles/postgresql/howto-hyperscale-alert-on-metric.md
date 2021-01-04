---
title: Konfigurieren von Warnungen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: In diesem Artikel wird beschrieben, wie Sie Metrikwarnungen für Azure Database for PostgreSQL – Hyperscale (Citus) konfigurieren und auf diese zugreifen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 73705434aef3ee438c02fbfd6502d30e7620b695
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026453"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for PostgreSQL – Hyperscale (Citus)

In diesem Artikel erfahren Sie, wie Sie mit dem Azure-Portal Azure Database for PostgreSQL-Warnungen einrichten können. Sie können eine Warnung erhalten, die auf [Überwachungsmetriken](concepts-hyperscale-monitoring.md) für Ihre Azure-Dienste basiert.

Wir richten eine Warnung ein, die ausgelöst wird, wenn der Wert für eine bestimmte Metrik einen Schwellenwert überschreitet. Die Warnung wird ausgelöst, wenn die Bedingung erstmals erfüllt ist. Anschließend wird sie weiterhin ausgelöst.

Sie können konfigurieren, dass bei einer Warnung die folgenden Aktionen ausgeführt werden, wenn sie ausgelöst wird:
* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren.
* Senden von E-Mails an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks

Sie haben folgende Möglichkeiten zum Konfigurieren von Warnungsregeln und Abrufen zugehöriger Informationen:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-Befehlszeilenschnittstelle](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor-REST-API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Erstellen einer Warnungsregel anhand einer Metrik aus dem Azure-Portal
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) den zu überwachenden Azure Database for PostgreSQL-Server aus.

2. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Warnungen** aus, wie unten gezeigt:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="„Warnungsregeln“ auswählen":::

3. Wählen Sie **Neue Warnungsregel** (Symbol „+“) aus.

4. Die Seite **Regel erstellen** wird geöffnet, wie unten gezeigt. Füllen Sie die erforderlichen Informationen aus:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Formular „Metrikwarnung hinzufügen“":::

5. Wählen Sie im Abschnitt **Bedingung** die Option **Hinzufügen** aus.

6. Wählen Sie eine Metrik aus der Liste der Signale aus, bei denen eine Warnung erfolgen soll. Wählen Sie in diesem Beispiel „Speicher in Prozent“ aus.
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Screenshot mit der Seite „Signallogik konfigurieren“, auf der Sie mehrere Signale anzeigen können.":::

7. Konfigurieren Sie die Warnungslogik:

    * **Operator** (Beispiel: „Größer als“)
    * **Schwellenwert** (Beispiel: 85 Prozent)
    * **Aggregationsgranularität**: Der Zeitraum, in dem die Metrikregel erfüllen werden muss, bevor die Warnung ausgelöst wird (Beispiel: „Innerhalb der letzten 30 Minuten“)
    * und **Häufigkeit der Auswertung** (Beispiel: „1 Minute“)
   
   Wählen Sie anschließend **Fertig** aus.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Screenshot des Bereichs, in dem Sie die Warnungslogik konfigurieren können.":::

8. Wählen Sie im Abschnitt **Aktionsgruppen** die Option **Neu erstellen** aus, um eine neue Gruppe zum Empfangen von Benachrichtigungen zu Warnungen zu erhalten.

9. Tragen Sie in das Formular „Aktionsgruppe hinzufügen“ einen Namen, Kurznamen, ein Abonnement und eine Ressourcengruppe ein.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Screenshot des Formulars „Aktionsgruppe hinzufügen“, in das Sie die beschriebenen Werte eingeben können.":::

10. Konfigurieren Sie den Aktionstyp **E-Mail/SMS/Push/Sprachanruf**.
    
    Wählen Sie „E-Mail an Azure Resource Manager-Rolle“ aus, um Benachrichtigungen an Besitzer, Mitwirkende und Leser des Abonnements zu senden.
   
    Wählen Sie **OK** aus, wenn Sie fertig sind.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Screenshot des Bereichs „E-Mail/SMS/Push/Sprachanruf“.":::

11. Geben Sie einen Namen, einen Beschreibung und den Schweregrad für die Warnungsregel an.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Screenshot der Seite „Warnungsdetails“."::: 

12. Wählen Sie **Benachrichtigungsregel erstellen** aus, um die Benachrichtigung zu erstellen.

    Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

### <a name="managing-alerts"></a>Verwalten von Warnungen

Nachdem Sie eine Warnung erstellt haben, können Sie sie auswählen und folgende Aktionen ausführen:

* Ein Diagramm anzeigen, das den Schwellenwert der Metrik und die tatsächlichen Werte vom Vortag zeigt, die für diese Warnung relevant sind.
* Die Warnungsregel **bearbeiten** oder **löschen**.
* Die Warnung **deaktivieren** oder **aktivieren**, wenn Sie den Empfang von Benachrichtigungen vorübergehend beenden oder fortsetzen möchten.

## <a name="suggested-alerts"></a>Warnungsvorschläge

### <a name="disk-space"></a>Speicherplatz

Überwachung und Warnungen sind für jede Produktionsservergruppe vom Typ „Hyperscale (Citus)“ wichtig. Die zugrunde liegende PostgreSQL-Datenbank benötigt freien Speicherplatz, damit sie ordnungsgemäß funktioniert. Wenn auf dem Datenträger zu wenig Speicherplatz zur Verfügung steht, wird der Datenbankserverknoten offline geschaltet und lässt sich erst wieder starten, wenn genügend Speicherplatz verfügbar ist. An diesem Punkt ist zur Behebung des Problems eine Microsoft-Supportanfrage erforderlich.

Es empfiehlt sich, Speicherplatzwarnungen für jeden Knoten in jeder Servergruppe festzulegen. Das gilt auch für Knoten, die nicht in der Produktion verwendet werden. Speicherplatzwarnungen ermöglichen es, frühzeitig einzugreifen und die Fehlerfreiheit der Knoten zu gewährleisten. Verwenden Sie am besten eine Reihe von Warnungen, die beispielsweise bei einer Auslastung von 75 Prozent, 85 Prozent und 95 Prozent ausgelöst werden. Die Wahl der Prozentwerte hängt von der Datenerfassungsgeschwindigkeit ab, da der Datenträger bei einer schnellen Datenerfassung schneller voll wird.

Wenn auf dem Datenträger nur noch wenig Speicherplatz zur Verfügung steht, versuchen Sie Folgendes, um mehr Speicherplatz freizugeben:

* Überprüfen Sie die Datenaufbewahrungsrichtlinie. Verschieben Sie ältere Daten nach Möglichkeit in Cold Storage.
* Erwägen Sie das [Hinzufügen von Knoten](howto-hyperscale-scale-grow.md#add-worker-nodes) zur Servergruppe sowie eine Neuverteilung von Shards. Durch die Neuverteilung werden die Daten auf mehr Computer verteilt.
* Erwägen Sie das [Erhöhen der Kapazität](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) von Workerknoten. Jeder Worker kann über bis zu 2 TiB Speicher verfügen. Vor dem Anpassen der Knotengröße sollten Sie jedoch zunächst versuchen, Knoten hinzuzufügen, da das Hinzufügen von Knoten weniger Zeit beansprucht.

### <a name="cpu-usage"></a>CPU-Auslastung

Die Überwachung der CPU-Auslastung ist hilfreich, um eine Baseline für die Leistung zu etablieren. Nehmen wir beispielsweise an, die CPU-Auslastung liegt in der Regel zwischen 40 und 60 Prozent. Wenn sich die CPU-Auslastung nun plötzlich im Bereich von 95 Prozent bewegt, können Sie eine Anomalie erkennen. Die CPU-Auslastung kann mit natürlichem Wachstum zusammenhängen, aber auch auf eine verirrte Abfrage hindeuten. Legen Sie beim Erstellen einer CPU-Warnung eine hohe Aggregationsgranularität fest, um längerfristige Zunahmen zu erfassen und kurzfristige Spitzen zu ignorieren.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Konfigurieren von Webhooks in Warnungen](../azure-monitor/platform/alerts-webhooks.md).
* Verschaffen Sie sich einen Überblick über das [Sammeln von Dienstmetriken](../azure-monitor/platform/data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
