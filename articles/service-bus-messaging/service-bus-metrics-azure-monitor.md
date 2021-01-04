---
title: Azure Service Bus-Metriken in Azure Monitor | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie mit Azure Monitor Service Bus-Entitäten (Warteschlangen, Themen und Abonnements) überwachen können.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: 1f8bd9484bf2a2106818da1d6e4ef21e937d2ac3
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916881"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus-Metriken in Azure Monitor

Service Bus-Metriken informieren Sie über den Status der Ressourcen in Ihrem Azure-Abonnement. Mithilfe eines umfangreichen Satzes von Metrikdaten können Sie die allgemeine Integrität Ihrer Service Bus-Ressourcen nicht nur auf Namespaceebene, sondern auch auf Entitätsebene bewerten. Diese Statistiken können wichtig sein, da sie Sie beim Überwachen des Zustands von Service Bus unterstützen. Metriken können auch beim Beheben der Ursachen von Problemen helfen, ohne dass Sie sich an den Azure-Support wenden müssen.

Azure Monitor bietet einheitliche Benutzeroberflächen für die übergreifende Überwachung verschiedener Azure-Dienste. Weitere Informationen finden Sie unter [Überwachung in Microsoft Azure](../azure-monitor/overview.md) und im Beispiel zum [Abrufen von Azure Monitor-Metriken mit .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) auf GitHub.

> [!IMPORTANT]
> Wenn seit 2 Stunden keine Interaktion mit einer Entität mehr stattgefunden hat, zeigen die Metriken solange „0“ als Wert an, bis die Entität sich nicht mehr im Leerlauf befindet.

## <a name="access-metrics"></a>Zugreifen auf Metriken

Azure Monitor bietet mehrere Möglichkeiten für den Metrikzugriff. Sie können über das [Azure-Portal](https://portal.azure.com), über die Azure Monitor-APIs (REST und .NET) sowie über Analyselösungen wie Azure Monitor-Protokolle und Event Hubs auf Metriken zugreifen. Weitere Informationen finden Sie unter [Azure Monitor-Metriken](../azure-monitor/platform/data-platform-metrics.md).

Metriken sind standardmäßig aktiviert, und es stehen Daten für die letzten 30 Tage zur Verfügung. Zur längeren Aufbewahrung können Sie Metrikdaten in einem Azure-Speicherkonto archivieren. Dieser Wert wird in den [Diagnoseeinstellungen](../azure-monitor/platform/diagnostic-settings.md) in Azure Monitor konfiguriert.

## <a name="access-metrics-in-the-portal"></a>Zugreifen auf Metriken über das Portal

Im [Azure-Portal](https://portal.azure.com) können Sie Metriken im Zeitverlauf überwachen. Das folgende Beispiel zeigt, wie Sie die erfolgreichen und die eingehenden Anforderungen auf Kontoebene anzeigen:

![Screenshot: Seite „Monitor – Metriken (Vorschauversion)“ im Azure-Portal][1]

Sie können auch direkt über den Namespace auf die Metriken zugreifen. Wählen Sie dazu den Namespace und anschließend **Metriken** aus. Wenn Sie gefilterte Metriken für den Bereich der Entität anzeigen möchten, wählen Sie die Entität und anschließend **Metriken** aus.

![Screenshot: Seite „Monitor – Metriken (Vorschauversion)“, gefiltert nach dem Entitätsbereich][2]

Bei Metriken mit Dimensionsunterstützung müssen Sie den gewünschten Dimensionswert als Filter verwenden.

## <a name="billing"></a>Abrechnung

Metriken und Benachrichtigungen auf Azure Monitor werden pro Benachrichtigung abgerechnet. Diese Gebühren finden Sie im Portal, wenn Sie die Benachrichtigung einrichten und bevor Sie sie speichern. 

Zusätzliche Lösungen, die Metrikdaten erfassen, werden direkt von diesen Lösungen abgerechnet. So werden etwa Gebühren für Azure Storage berechnet, wenn Sie Metrikdaten in einem Azure-Speicherkonto archivieren. Kosten für Log Analytics fallen auch an, wenn Sie Metrikdaten für eine ausführlichere Analyse an Log Analytics streamen.

Die folgenden Metriken bieten Ihnen eine Übersicht über die Integrität eines Diensts. 

> [!NOTE]
> Einige Metriken werden eingestellt, da sie unter einem anderen Namen verwendet werden. Deshalb müssen Sie möglicherweise Ihre Verweise aktualisieren. Metriken, die mit dem Schlüsselwort „veraltet“ markiert sind, werden zukünftig nicht mehr unterstützt.

Alle Metrikwerte werden minütlich an Azure Monitor gesendet. Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Zeitintervall für alle Service Bus-Metriken beträgt 1 Minute.

## <a name="request-metrics"></a>Anforderungsmetriken

Zählt die Anzahl der Anforderungen von Daten und Verwaltungsvorgängen

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| Eingehende Anforderungen| Die Anzahl der Anforderungen an den Service Bus-Dienst in einem bestimmten Zeitraum <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Erfolgreiche Anforderungen|Die Anzahl der erfolgreichen Anforderungen an den Service Bus-Dienst in einem bestimmten Zeitraum<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Serverfehler|Die Anzahl der aufgrund eines Fehlers nicht verarbeiteten Anforderungen an den Service Bus-Dienst in einem bestimmten Zeitraum<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Benutzerfehler (siehe folgender Unterabschnitt)|Die Anzahl der aufgrund von Benutzerfehlern nicht verarbeiteten Anforderungen in einem bestimmten Zeitraum<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Gedrosselte Anforderungen|Die Anzahl der Anforderungen, die aufgrund eines überschrittenen Verbrauchs gedrosselt wurden<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|

### <a name="user-errors"></a>Benutzerfehler

Die beiden folgenden Arten von Fehlern werden als Benutzerfehler klassifiziert:

1. Clientseitige Fehler (in HTTP: Fehler vom Typ 400)
2. Fehler bei der Nachrichtenverarbeitung (beispielsweise [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception))


## <a name="message-metrics"></a>Nachrichtenmetriken

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
|Eingehende Nachrichten|Die Anzahl von Ereignissen oder Nachrichten, die in einem bestimmten Zeitraum an Service Bus gesendet wurden<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Ausgehende Nachrichten|Die Anzahl von Ereignissen oder Nachrichten, die in einem bestimmten Zeitraum von Service Bus empfangen wurden<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
| Meldungen| Anzahl von Nachrichten in einer Warteschlange/einem Thema <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Dimension: Name der Entität |
| Aktive Nachrichten| Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Dimension: Name der Entität |
| Unzustellbare Nachrichten| Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/>Dimension: Name der Entität |
| Geplante Nachrichten| Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average  <br/> Dimension: Name der Entität |
| Abgeschlossene Nachrichten| Anzahl abgeschlossener Nachrichten in einer Warteschlange/einem Thema <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Dimension: Name der Entität |
| Abgebrochene Nachrichten| Anzahl abgebrochener Nachrichten in einer Warteschlange/einem Thema <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Dimension: Name der Entität |
| Size | Größe einer Entität (Warteschlange oder Thema) in Byte <br/><br/>Einheit: Anzahl <br/>Aggregationstyp: Average <br/>Dimension: Name der Entität | 

> [!NOTE]
> Werte für Nachrichten, aktive Nachrichten, unzustellbare Nachrichten, geplante Nachrichten, abgeschlossene Nachrichten und abgebrochene Nachrichten sind Zeitpunktwerte. Eingehende Nachrichten, die unmittelbar nach diesem Zeitpunkt verarbeitet wurden, werden in diesen Metriken möglicherweise nicht berücksichtigt. 

## <a name="connection-metrics"></a>Verbindungsmetriken

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
|Die aktiven Verbindungen.|Die Anzahl der aktiven Verbindungen für einen Namespace sowie für eine Entität im Namespace. Der Wert für diese Metrik ist ein Zeitpunktwert. Verbindungen, die unmittelbar nach diesem Zeitpunkt aktiv waren, werden in der Metrik möglicherweise nicht berücksichtigt.<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Geöffnete Verbindungen |Die Anzahl der geöffneten Verbindungen<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|
|Geschlossene Verbindungen |Die Anzahl der geschlossenen Verbindungen<br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Gesamt <br/> Dimension: Name der Entität|

## <a name="resource-usage-metrics"></a>Metriken zur Ressourcennutzung

> [!NOTE] 
> Die folgenden Metriken sind nur mit dem **Premium**-Tarif verfügbar. 
> 
> Wichtige Metriken, die bei Ausfällen für einen Namespace im Premium-Tarif überwacht werden müssen: **CPU-Auslastung pro Namespace** und **Arbeitsspeichergröße pro Namespace**. [Richten Sie mit Azure Monitor Warnungen](../azure-monitor/platform/alerts-metric.md) für diese Metriken ein.
> 
> Die andere Metrik, die Sie überwachen können, ist **Gedrosselt Anforderungen**. Es sollte jedoch kein Problem vorliegen, solange der Namespace innerhalb der Grenzwerte für Arbeitsspeicher, CPU und vermittelte Verbindungen bleibt. Weitere Informationen finden Sie unter [Drosselung im Tarif „Premium“ von Azure Service Bus](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
|CPU-Auslastung pro Namespace|Der Prozentsatz der CPU-Auslastung des Namespace<br/><br/> Einheit: Percent <br/> Aggregationstyp: Maximum <br/> Dimension: Name der Entität|
|Auslastung der Arbeitsspeichergröße pro Namespace|Der Prozentsatz der Arbeitsspeichernutzung des Namespace<br/><br/> Einheit: Percent <br/> Aggregationstyp: Maximum <br/> Dimension: Name der Entität|

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Service Bus unterstützt folgende Dimensionen für Metriken in Azure Monitor. Das Hinzufügen von Dimensionen zu Ihren Metriken ist optional. Wenn Sie keine Dimensionen hinzufügen, werden Metriken auf Namespaceebene angegeben. 

|Dimensionsname|BESCHREIBUNG|
| ------------------- | ----------------- |
|Entitätsname| Service Bus unterstützt Messagingentitäten unter dem Namespace.|

## <a name="set-up-alerts-on-metrics"></a>Einrichten von Warnungen zu Metriken

1. Wählen Sie auf der Registerkarte **Metriken** der Seite **Service Bus-Namespace** die Option **Warnungen konfigurieren** aus. 

    ![Seite „Metriken“ – Menü „Warnungen konfigurieren“](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Wählen Sie die Option **Ziel auswählen** aus, und führen Sie auf der Seite **Select a resource** (Ressource auswählen) die folgenden Aktionen aus: 
    1. Wählen Sie im Feld **Nach Ressourcentyp filtern** die Option **Service Bus-Namespaces** aus. 
    2. Wählen Sie im Feld **Nach Abonnement filtern** Ihr Abonnement aus.
    3. Wählen Sie in der Liste **Service Bus-Namespace** aus. 
    4. Wählen Sie **Fertig** aus. 
    
        ![Auswählen eines Namespace](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Wählen Sie **Kriterien hinzufügen** aus, und führen Sie auf der Seite **Signallogik konfigurieren** die folgenden Aktionen aus:
    1. Wählen Sie für **Signaltyp** die Option **Metriken** aus. 
    2. Wählen Sie ein Signal aus. Beispiel: **Dienstfehler**. 

        ![Auswählen von Serverfehlern](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Wählen Sie unter **Bedingung** die Option **Größer als** aus.
    2. Wählen Sie unter **Zeitaggregation** die Option **Gesamt** aus. 
    3. Geben Sie unter **Schwellenwert** den Wert **5** ein. 
    4. Wählen Sie **Fertig** aus.    

        ![Angaben einer Bedingung](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Erweitern Sie auf der Seite **Regel erstellen** die Option **Warnungsdetails definieren**, und führen Sie die folgenden Aktionen aus:
    1. Geben Sie einen **Namen** für die Warnung ein. 
    2. Geben Sie eine **Beschreibung** für die Warnung ein.
    3. Wählen Sie den **Schweregrad** für diese Warnung aus. 

        ![Screenshot: Seite „Regel erstellen“. Das Definieren von Warnungsdetails ist erweitert, und die Felder für Name, Beschreibung und Schweregrad der Warnungsregel sind hervorgehoben.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Erweitern Sie auf der Seite **Regel erstellen** die Option **Aktionsgruppe definieren**, wählen Sie **Neue Aktionsgruppe** aus, und führen Sie auf der Gruppenseite **Aktion hinzufügen** die folgenden Aktionen aus. 
    1. Geben Sie einen Namen für die Aktionsgruppe ein.
    2. Geben Sie einen kurzen Namen für die Aktionsgruppe ein. 
    3. Wählen Sie Ihr Abonnement aus. 
    4. Wählen Sie eine Ressourcengruppe aus. 
    5. In dieser exemplarischen Vorgehensweise geben Sie für **AKTIONSNAME** den Text **E-Mail senden** ein.
    6. Wählen Sie unter **AKTIONSTYP** die Option **E-Mail/SMS/Push/Sprachanruf** aus. 
    7. Wählen Sie **Details bearbeiten** aus. 
    8. Führen Sie auf der Seite **E-Mail/SMS/Push/Sprachanruf** die folgenden Aktionen aus:
        1. Wählen Sie **E-Mail** aus. 
        2. Geben Sie die **E-Mail-Adresse** ein. 
        3. Klicken Sie auf **OK**.

            ![Screenshot: Seite „Aktionsgruppe hinzufügen“. Eine Aktion namens „Send email“ (E-Mail senden) mit dem Aktionstyp „Email/SMS/Push/Voice“ wird der Gruppe hinzugefügt.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Wählen Sie im Bereich **Aktionsgruppe hinzufügen** die Option **OK** aus. 
1. Wählen Sie auf der Seite **Regel erstellen** die Option **Warnungsregel erstellen** aus. 

    ![Schaltfläche „Warnungsregel erstellen“](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Azure Monitor – Übersicht](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png