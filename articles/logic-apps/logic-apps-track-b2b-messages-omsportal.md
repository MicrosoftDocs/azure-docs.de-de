---
title: Nachverfolgen von B2B-Nachrichten mit Azure Monitor-Protokollen – Azure Logic Apps | Microsoft-Dokumentation
description: Nachverfolgen der B2B-Kommunikation für Integrationskonten und Azure Logic Apps mit Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 8cf5d9f3ee1503769a2ec199847175899bcd86bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120125"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Nachverfolgen von B2B-Nachrichten mit Azure Monitor-Protokollen

Nachdem Sie die B2B-Kommunikation zwischen Handelspartnern in Ihrem Integrationskonto eingerichtet haben, können diese Partner untereinander Nachrichten mit Protokollen wie AS2, X12 und EDIFACT austauschen. Um zu überprüfen, ob diese Nachrichten richtig verarbeitet werden, können Sie diese Nachrichten mit [Azure Monitor-Protokollen](../log-analytics/log-analytics-overview.md) nachverfolgen. Beispielsweise können Sie diese webbasierten Nachverfolgungsfunktionen nutzen, um Nachrichten nachzuverfolgen:

* Anzahl und Status von Nachrichten
* Bestätigungsstatus
* Korrelieren von Nachrichten mit Bestätigungen
* Ausführliche Fehlerbeschreibung für Fehler
* Suchfunktionen

> [!NOTE]
> Auf dieser Seite wurden zuvor Schritte zum Ausführen dieser Aufgaben mit der Microsoft Operations Management Suite (OMS) beschrieben. Diese wird [im Januar 2019 eingestellt](../azure-monitor/platform/oms-portal-transition.md), und stattdessen werden Schritte mit Azure Log Analytics angegeben. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Eine Logik-App, für die die Diagnoseprotokollierung eingerichtet ist. Informieren Sie sich über das [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md) und das [Einrichten der Protokollierung für diese Logik-App](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ein Integrationskonto, für das die Überwachung und Protokollierung eingerichtet ist. Informieren Sie sich über das [Erstellen eines Integrationskontos](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) und das [Einrichten der Überwachung und Protokollierung für dieses Konto](../logic-apps/logic-apps-monitor-b2b-message.md).

* [Veröffentlichen Sie Diagnosedaten in Azure Monitor-Protokolle](../logic-apps/logic-apps-track-b2b-messages-omsportal.md), falls noch nicht geschehen.

* Wenn Sie die obigen Anforderungen erfüllen, benötigen Sie auch einen Log Analytics-Arbeitsbereich, in dem Sie die Nachverfolgung von B2B-Kommunikation über Log Analytics nutzen. Falls Sie keinen Log Analytics-Arbeitsbereich besitzen, lesen Sie die Informationen zum [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Installieren der Lösung B2B-Logik-App-Verwaltung

Bevor Azure Monitor-Protokolle B2B-Nachrichten für Ihre Logik-App nachverfolgen kann, fügen Sie Azure Monitor-Protokolle die Lösung **B2B-Logik-App-Verwaltung** hinzu. Weitere Informationen zum Hinzufügen von Lösungen zu Azure Monitor-Protokollen finden Sie [hier](../azure-monitor/learn/quick-create-workspace.md).

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Alle Dienste**. Geben Sie im Suchfeld „log analytics“ ein, und wählen Sie **Log Analytics** aus.

   ![Auswählen von „Log Analytics“](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Wählen Sie unter **Log Analytics** Ihren Log Analytics-Arbeitsbereich aus. 

   ![Auswählen des Log Analytics-Arbeitsbereichs](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Wählen Sie unter **Erste Schritte mit Log Analytics** > **Überwachungslösungen konfigurieren** die Option **Lösungen anzeigen** aus.

   ![Auswählen von „Lösungen anzeigen“](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Wählen Sie auf der Seite „Übersicht“ die Option **Hinzufügen** aus, um die Liste **Verwaltungslösungen** zu öffnen. Wählen Sie in dieser Liste die Option **B2B-Logik-App-Verwaltung** aus. 

   ![Auswählen der Lösung B2B-Logik-App-Verwaltung](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Wenn Sie die Lösung nicht finden können, wählen Sie am unteren Rand der Liste **Weitere laden** aus, bis die Lösung angezeigt wird.

1. Wählen Sie **Erstellen** aus, bestätigen Sie den Log Analytics-Arbeitsbereich, in dem Sie die Lösung installieren möchten, und wählen Sie erneut **Erstellen** aus.   

   ![Auswählen von „Erstellen“ für B2B-Logik-App-Verwaltung](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Wenn Sie keinen vorhandenen Arbeitsbereich verwenden möchten, können Sie zu diesem Zeitpunkt auch einen neuen Arbeitsbereich erstellen.

1. Wenn Sie fertig sind, wechseln Sie zurück zur Seite **Übersicht** Ihres Arbeitsbereichs. 

   Die Lösung B2B-Logik-App-Verwaltung wird jetzt auf der Seite „Übersicht“ angezeigt. 
   Beim Verarbeiten von B2B-Nachrichten wird die Nachrichtenanzahl auf dieser Seite aktualisiert.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Anzeigen von B2B-Nachrichteninformationen

Nachdem B2B-Nachrichten verarbeitet wurden, können Sie den Status und die Details für diese Nachrichten auf der Kachel **B2B-Logik-App-Verwaltung** anzeigen.

1. Wechseln Sie zu Ihrem Log Analytics-Arbeitsbereich, und öffnen Sie die Seite „Übersicht“. Wählen Sie **B2B-Logik-App-Verwaltung** aus.

   ![Aktualisierte Nachrichtenanzahl](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Standardmäßig werden auf der Kachel **B2B-Logik-App-Verwaltung** Daten basierend auf einem einzelnen Tag angezeigt. Klicken Sie oben auf der Seite auf das Steuerelement für die Bereichsauswahl, um für den Datenbereich ein anderes Intervall festzulegen:
   > 
   > ![Ändern des Intervalls](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Nachdem das Dashboard mit dem Nachrichtenstatus angezeigt wird, können Sie weitere Details für einen bestimmten Nachrichtentyp anzeigen, für den Daten basierend auf einem einzelnen Tag angezeigt werden. Wählen Sie die Kachel für **AS2**, **X12** oder **EDIFACT** aus.

   ![Anzeigen des Nachrichtenstatus](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Für die von Ihnen gewählte Kachel wird eine Liste mit Nachrichten angezeigt. 
   Weitere Informationen zu den Eigenschaften der einzelnen Nachrichtentypen finden Sie unter diesen Beschreibungen von Nachrichteneigenschaften:

   * [AS2-Nachrichteneigenschaften](#as2-message-properties)
   * [X12-Nachrichteneigenschaften](#x12-message-properties)
   * [EDIFACT-Nachrichteneigenschaften](#EDIFACT-message-properties)

   Eine AS2-Nachrichtenliste kann beispielsweise wie folgt aussehen:

   ![Anzeigen von AS2-Nachrichten](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Wählen Sie diese Nachrichten aus, und wählen Sie anschließend die Option **Herunterladen**, um die Ein- und Ausgaben für bestimmte Nachrichten anzuzeigen oder zu exportieren. Speichern Sie die ZIP-Datei nach der entsprechenden Aufforderung auf Ihrem lokalen Computer, und extrahieren Sie diese Datei. 

   Der extrahierte Ordner enthält einen Ordner für jede ausgewählte Nachricht. 
   Wenn Sie Bestätigungen einrichten, enthält der Nachrichtenordner auch Dateien mit Bestätigungsdetails. 
   Jeder Nachrichtenordner enthält mindestens die folgenden Dateien: 
   
   * Für Menschen lesbare Dateien mit den Details zur Eingabe- und Ausgabenutzlast
   * Codierte Dateien mit den Ein- und Ausgaben

   Die Ordner- und Dateinamenformate für die einzelnen Nachrichtentypen finden Sie hier:

   * [AS2-Ordner- und -Dateinamenformate](#as2-folder-file-names)
   * [X12-Ordner- und -Dateinamenformate](#x12-folder-file-names)
   * [EDIFACT-Ordner- und -Dateinamenformate](#edifact-folder-file-names)

   ![Herunterladen von Nachrichtendateien](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Wählen Sie auf der Seite **Protokollsuche** in der Nachrichtenliste eine Nachricht aus, um alle Aktionen anzuzeigen, die über die gleiche Ausführungs-ID verfügen.

   Sie können diese Aktionen nach Spalte sortieren oder nach bestimmten Ergebnissen suchen.

   ![Aktionen mit der gleichen Ausführungs-ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Wählen Sie **Favoriten**, um Ergebnisse mit vorab erstellten Abfragen zu durchsuchen.

   * Informieren Sie sich über das [Erstellen von Abfragen durch das Hinzufügen von Filtern](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Oder informieren Sie sich über das [Suchen von Daten mithilfe der Protokollsuche in Azure Monitor-Protokollen](../log-analytics/log-analytics-log-searches.md).

   * Gehen Sie wie folgt vor, um die Abfrage im Suchfeld zu ändern: Aktualisieren Sie die Abfrage mit den Spalten und Werten, die Sie als Filter verwenden möchten.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Eigenschaftsbeschreibungen und Namensformate für AS2-, X12- und EDIFACT-Nachrichten

Für jeden Nachrichtentyp sind hier die Eigenschaftsbeschreibungen und Namensformate für heruntergeladene Nachrichtendateien aufgeführt.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Eigenschaftsbeschreibungen für AS2-Nachrichten

Hier sind die Eigenschaftsbeschreibungen für die einzelnen AS2-Nachrichten aufgeführt.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Sender | Der unter **Empfangseinstellungen** angegebene Gastpartner oder der unter **Sendeeinstellungen** angegebene Hostpartner für eine AS2-Vereinbarung |
| Receiver | Der unter **Empfangseinstellungen** angegebene Hostpartner oder der unter **Sendeeinstellungen** angegebene Gastpartner für eine AS2-Vereinbarung |
| Logik-App | Die Logik-App, in der die AS2-Aktionen eingerichtet wurden |
| Status | AS2-Nachrichtenstatus <br>Erfolg = Eine gültige AS2-Nachricht wurde empfangen oder gesendet. Es wird keine MDN eingerichtet. <br>Erfolg = Eine gültige AS2-Nachricht wurde empfangen oder gesendet. Eine MDN wird eingerichtet und empfangen, oder eine MDN wird gesendet. <br>Fehler = Eine ungültige AS2-Nachricht wurde empfangen. Es wird keine MDN eingerichtet. <br>Ausstehend = Eine gültige AS2-Nachricht wurde empfangen oder gesendet. Eine MDN wird eingerichtet, und eine MDN wird erwartet. |
| Ack | MDN-Nachrichtenstatus <br>Akzeptiert = Positive MDN wurde empfangen oder gesendet. <br>Ausstehend = Es wird auf das Empfangen oder Senden einer MDN gewartet. <br>Abgelehnt = Eine negative MDN wurde empfangen oder gesendet. <br>Nicht erforderlich = Eine MDN wurde in der Vereinbarung nicht eingerichtet. |
| Direction | Richtung der AS2-Nachricht |
| Korrelations-ID | ID, die alle Trigger und Aktionen in einer Logik-App korreliert |
| Nachrichten-ID | AS2-Nachrichten-ID aus den AS2-Nachrichtenheadern |
| Timestamp | Der Zeitpunkt, zu dem die AS2-Aktion die Nachricht verarbeitet hat |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2-Namensformate für heruntergeladene Nachrichtendateien

Hier sind die Namensformate für die einzelnen heruntergeladenen AS2-Nachrichtenordner und -dateien aufgeführt.

| Ordner oder Datei | Namensformat |
| :------------- | :---------- |
| Nachrichtenordner | [Absender]\_[Empfänger]\_AS2\_[Korrelations-ID]\_[Nachrichten-ID]\_[Zeitstempel] |
| Eingabe, Ausgabe und, falls eingerichtet, Bestätigungsdateien | **Eingabenutzlast**: [Absender]\_[Empfänger]\_AS2\_[Korrelations-ID]\_input_payload.txt </p>**Ausgabenutzlast**: [Absender]\_[Empfänger]\_AS2\_[Korrelations-ID]\_Ausgabe\_payload.txt </p></p>**Eingaben**: [Absender]\_[Empfänger]\_AS2\_[Korrelations-ID]\_inputs.txt </p></p>**Ausgaben**: [Absender]\_[Empfänger]\_AS2\_[Korrelations-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beschreibungen der Eigenschaften von X12-Nachrichten

Hier sind die Eigenschaftsbeschreibungen für die einzelnen X12-Nachrichten aufgeführt.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Sender | Der unter **Empfangseinstellungen** angegebene Gastpartner oder der unter **Sendeeinstellungen** angegebene Hostpartner für eine X12-Vereinbarung |
| Receiver | Der unter **Empfangseinstellungen** angegebene Hostpartner oder der unter **Sendeeinstellungen** angegebene Gastpartner für eine X12-Vereinbarung |
| Logik-App | Die Logik-App, in der die X12-Aktionen eingerichtet wurden |
| Status | X12-Nachrichtenstatus <br>Erfolg = Eine gültige X12-Nachricht wurde empfangen oder gesendet. Keine Funktionsbestätigung wird eingerichtet. <br>Erfolg = Eine gültige X12-Nachricht wurde empfangen oder gesendet. Eine Funktionsbestätigung wird eingerichtet und empfangen, oder eine Funktionsbestätigung wird gesendet. <br>Fehler = Eine ungültige X12-Nachricht wurde empfangen oder gesendet. <br>Ausstehend = Eine gültige X12-Nachricht wurde empfangen oder gesendet. Eine Funktionsbestätigung wird eingerichtet, und eine Funktionsbestätigung wird erwartet. |
| Ack | Status der Funktionsbestätigung (997) <br>Akzeptiert = Positive Funktionsbestätigung empfangen oder gesendet. <br>Abgelehnt = Negative Funktionsbestätigung empfangen oder gesendet. <br>Ausstehend = Funktionsbestätigung wird erwartet, wurde aber noch nicht empfangen. <br>Ausstehend = Funktionsbestätigung wurde generiert, konnte aber nicht an Partner gesendet werden <br>Nicht erforderlich = Funktionsbestätigung wurde nicht eingerichtet. |
| Direction | Richtung der X12-Nachricht |
| Korrelations-ID | ID, die alle Trigger und Aktionen in einer Logik-App korreliert |
| Msg type | EDI-X12-Nachrichtentyp |
| ICN | Austauschkontrollnummer für die X12-Nachricht |
| TSCN | Transaktionssatz-Kontrollnummer für die X12-Nachricht |
| Timestamp | Der Zeitpunkt, zu dem die X12-Aktion die Nachricht verarbeitet hat |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12-Namensformate für heruntergeladene Nachrichtendateien

Hier sind die Namensformate für die einzelnen heruntergeladenen X12-Nachrichtenordner und -dateien aufgeführt.

| Ordner oder Datei | Namensformat |
| :------------- | :---------- |
| Nachrichtenordner | [Absender]\_[Empfänger]\_X12\_[Austauschkontrollnummer]\_[Globale Kontrollnummer]\_[Transaktionssatz-Kontrollnummer]\_[Zeitstempel] |
| Eingabe, Ausgabe und, falls eingerichtet, Bestätigungsdateien | **Eingabenutzlast**: [Absender]\_[Empfänger]\_X12\_[Austauschkontrollnummer]\_input_payload.txt </p>**Ausgabenutzlast**: [Absender]\_[Empfänger]\_X12\_[Austauschkontrollnummer]\_output\_payload.txt </p></p>**Eingaben**: [Absender]\_[Empfänger]\_X12\_[Austauschkontrollnummer]\_inputs.txt </p></p>**Ausgaben**: [Absender]\_[Empfänger]\_X12\_[Austauschkontrollnummer]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Eigenschaftsbeschreibungen für EDIFACT-Nachrichten

Hier sind die Eigenschaftsbeschreibungen für die einzelnen EDIFACT-Nachrichten aufgeführt.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Sender | Der unter **Empfangseinstellungen** angegebene Gastpartner oder der unter **Sendeeinstellungen** angegebene Hostpartner für eine EDIFACT-Vereinbarung |
| Receiver | Der unter **Empfangseinstellungen** angegebene Hostpartner oder der unter **Sendeeinstellungen** angegebene Gastpartner für eine EDIFACT-Vereinbarung |
| Logik-App | Die Logik-App, in der die EDIFACT-Aktionen eingerichtet wurden |
| Status | EDIFACT-Nachrichtenstatus <br>Erfolg = Eine gültige EDIFACT-Nachricht wurde empfangen oder gesendet. Keine Funktionsbestätigung wird eingerichtet. <br>Erfolg = Eine gültige EDIFACT-Nachricht wurde empfangen oder gesendet. Eine Funktionsbestätigung wird eingerichtet und empfangen, oder eine Funktionsbestätigung wird gesendet. <br>Fehler = Eine ungültige EDIFACT-Nachricht wurde empfangen oder gesendet. <br>Ausstehend = Eine gültige EDIFACT-Nachricht wurde empfangen oder gesendet. Eine Funktionsbestätigung wird eingerichtet, und eine Funktionsbestätigung wird erwartet. |
| Ack | Status der Funktionsbestätigung (997) <br>Akzeptiert = Positive Funktionsbestätigung empfangen oder gesendet. <br>Abgelehnt = Negative Funktionsbestätigung empfangen oder gesendet. <br>Ausstehend = Funktionsbestätigung wird erwartet, wurde aber noch nicht empfangen. <br>Ausstehend = Funktionsbestätigung wurde generiert, konnte aber nicht an Partner gesendet werden. <br>Nicht erforderlich = Funktionsbestätigung wurde nicht eingerichtet. |
| Direction | Richtung der EDIFACT-Nachricht |
| Korrelations-ID | ID, die alle Trigger und Aktionen in einer Logik-App korreliert |
| Msg type | EDIFACT-Nachrichtentyp |
| ICN | Austauschkontrollnummer für die EDIFACT-Nachricht |
| TSCN | Transaktionssatz-Kontrollnummer für die EDIFACT-Nachricht |
| Timestamp | Der Zeitpunkt, zu dem die EDIFACT-Aktion die Nachricht verarbeitet hat |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT-Namensformate für heruntergeladene Nachrichtendateien

Hier sind die Namensformate für die einzelnen heruntergeladenen EDIFACT-Nachrichtenordner und -dateien aufgeführt.

| Ordner oder Datei | Namensformat |
| :------------- | :---------- |
| Nachrichtenordner | [Absender]\_[Empfänger]\_EDIFACT\_[Austauschkontrollnummer]\_[Globale Kontrollnummer]\_[Transaktionssatz-Kontrollnummer]\_[Zeitstempel] |
| Eingabe, Ausgabe und, falls eingerichtet, Bestätigungsdateien | **Eingabenutzlast**: [Absender]\_[Empfänger]\_EDIFACT\_[Austauschkontrollnummer]\_input_payload.txt </p>**Ausgabenutzlast**: [Absender]\_[Empfänger]\_EDIFACT\_[Austauschkontrollnummer]\_output\_payload.txt </p></p>**Eingaben**: [Absender]\_[Empfänger]\_EDIFACT\_[Austauschkontrollnummer]\_inputs.txt </p></p>**Ausgaben**: [Absender]\_[Empfänger]\_EDIFACT\_[Austauschkontrollnummer]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen auf B2B-Nachrichten in Azure Monitor-Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Benutzerdefinierte Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
