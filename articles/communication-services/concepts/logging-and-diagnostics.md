---
title: Communication Services-Protokolle
titleSuffix: An Azure Communication Services concept document
description: Enthält Informationen zur Protokollierung in Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d8afa769c90c5cf9450343cda1a65809062468fb
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888690"
---
# <a name="communication-services-logs"></a>Communication Services-Protokolle

Azure Communication Services bietet Protokollierungsfunktionen, mit denen Sie Ihre Communication Services-Lösung überwachen und debuggen können. Diese Funktionen können über das Azure-Portal konfiguriert werden.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Aktivieren von Diagnoseprotokollen in der Ressource

Die Protokollierung ist standardmäßig deaktiviert, wenn eine Ressource erstellt wird. Navigieren Sie zum Aktivieren der Protokollierung im Ressourcenmenü unter dem Abschnitt **Überwachung** zum Blatt **Diagnoseeinstellungen**. Klicken Sie dann auf **Diagnoseeinstellung hinzufügen**.

Wählen Sie als nächstes das gewünschte Archivziel aus. Derzeit unterstützen wir Speicherkonten und Log Analytics als Archivziele. Nachdem Sie die Arten von Protokollen ausgewählt haben, die Sie erfassen möchten, speichern Sie die Diagnoseeinstellungen.
 
Neue Einstellungen werden in etwa zehn Minuten wirksam. Die Protokolle werden im konfigurierten Archivierungsziel im Bereich „Protokolle“ Ihrer Communication Services-Ressource angezeigt.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Optionen für ACS-Diagnoseeinstellungen":::

Weitere Informationen zum Konfigurieren der Diagnose finden Sie in der [Übersicht über Azure-Ressourcenprotokolle](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Ressourcenprotokollkategorien

Communication Services bietet drei Arten von Protokollen, die Sie aktivieren können:

* **Nutzungsprotokolle** – Stellt Nutzungsdaten bereit, die den einzelnen abgerechneten Dienstangeboten zugeordnet sind.
* **Chat-Betriebsprotokolle**: Stellt grundlegende Informationen zum Chatdienst bereit.
* **SMS-Betriebsprotokolle**: Stellt grundlegende Informationen zum SMS-Dienst bereit.

### <a name="usage-logs-schema"></a>Schema für Nutzungsprotokolle

| Eigenschaft | BESCHREIBUNG |
| -------- | ---------------|
| Timestamp | Der Zeitstempel (UTC), wann das Protokoll generiert wurde. |
| Vorgangsname | Der mit der Protokollaufzeichnung verbundene Vorgang. |
| Vorgangsversion | Die `api-version`, die dem Vorgang zugeordnet ist, wenn für „operationName“ eine API verwendet wurde. Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| Kategorie | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. |
| Korrelations-ID | Die ID für korrelierte Ereignisse. Kann verwendet werden, um korrelierte Ereignisse zwischen verschiedenen Tabellen zu identifizieren. |
| Eigenschaften | Andere Daten, die auf verschiedene Communication Services-Modi anwendbar sind. |
| Datensatz-ID | Die eindeutige ID für einen angegebenen Nutzungsdatensatz. |
| Nutzungstyp | Die Art der Nutzung. (Beispiel: Chat, PSTN, NAT usw.) |
| Unit-Typ | Der Typ der Einheit, auf dem die Nutzung für eine bestimmte Nutzungsart basiert. (Beispiel Minuten, Megabyte, Nachrichten usw.) |
| Menge | Die Anzahl der für diesen Datensatz verwendeten oder verbrauchten Einheiten. |

### <a name="chat-operational-logs"></a>Protokolle zum Chatbetrieb

| Eigenschaft | BESCHREIBUNG |
| -------- | ---------------|
| TimeGenerated | Der Zeitstempel (UTC), wann das Protokoll generiert wurde. |
| Vorgangsname | Der mit der Protokollaufzeichnung verbundene Vorgang. |
| CorrelationID | Die ID für korrelierte Ereignisse. Kann verwendet werden, um korrelierte Ereignisse zwischen verschiedenen Tabellen zu identifizieren. |
| OperationVersion | Die API-Version, die dem Vorgang zugeordnet ist, wenn für „operationName“ eine API verwendet wurde. Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| Kategorie | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. |
| ResultType | Der Status des Vorgangs. |
| ResultSignature | Der Unterstatus des Vorgangs. Wenn dieser Vorgang einem REST-API-Aufruf entspricht, ist dieses Feld der HTTP-Statuscode des entsprechenden REST-Aufrufs. |
| ResultDescription | Die statische Textbeschreibung dieses Vorgangs. |
| DurationMs | Die Dauer des Vorgangs in Millisekunden. |
| CallerIpAddress | Die IP-Adresse des Aufrufers, wenn der Vorgang einem API-Aufruf entspricht, der von einer Entität mit einer öffentlich verfügbaren IP-Adresse stammt. |
| Ebene | Der Schweregrad des Ereignisses. |
| URI | Der URI der Anforderung. |
| UserId | Die Benutzer-ID des Absenders der Anforderung. |
| ChatThreadId | Die mit der Anforderung verknüpfte Chatthread-ID. |
| ChatMessageId | Die mit der Anforderung verknüpfte Chatnachrichten-ID. |
| SdkType | Der in der Anforderung verwendete SDK-Typ. |
| PlatformType | Der in der Anforderung verwendete Plattformtyp. |

### <a name="sms-operational-logs"></a>Protokolle zum SMS-Betrieb

| Eigenschaft | BESCHREIBUNG |
| -------- | ---------------|
| TimeGenerated | Der Zeitstempel (UTC), wann das Protokoll generiert wurde. |
| Vorgangsname | Der mit der Protokollaufzeichnung verbundene Vorgang. |
| CorrelationID | Die ID für korrelierte Ereignisse. Kann verwendet werden, um korrelierte Ereignisse zwischen verschiedenen Tabellen zu identifizieren. |
| OperationVersion | Die API-Version, die dem Vorgang zugeordnet ist, wenn für „operationName“ eine API verwendet wurde. Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| Kategorie | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. |
| ResultType | Der Status des Vorgangs. |
| ResultSignature | Der Unterstatus des Vorgangs. Wenn dieser Vorgang einem REST-API-Aufruf entspricht, ist dieses Feld der HTTP-Statuscode des entsprechenden REST-Aufrufs. |
| ResultDescription | Die statische Textbeschreibung dieses Vorgangs. |
| DurationMs | Die Dauer des Vorgangs in Millisekunden. |
| CallerIpAddress | Die IP-Adresse des Aufrufers, wenn der Vorgang einem API-Aufruf entspricht, der von einer Entität mit einer öffentlich verfügbaren IP-Adresse stammt. |
| Ebene | Der Schweregrad des Ereignisses. |
| URI | Der URI der Anforderung. |
| OutgoingMessageLength | Die Anzahl der Zeichen in der ausgehenden Nachricht. |
| IncomingMessageLength | Die Anzahl der Zeichen in der eingehenden Nachricht. |
| DeliveryAttempts | Die Anzahl der Versuche, die unternommen wurden, um diese Nachricht zuzustellen. |
| PhoneNumber | Die Telefonnummer, an die die SMS-Nachricht gesendet wird. |
| SdkType | Der in der Anforderung verwendete SDK-Typ. |
| PlatformType | Der in der Anforderung verwendete Plattformtyp. |
| Methode | Die in der Anforderung verwendete Methode. |