---
title: Problembehandlung für Synapse Studio
description: Problembehandlung in Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: a96709c7538bda6d6b338c29dc512902e9ca2c87
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445852"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Problembehandlung für Azure Synapse Studio

Dieser Leitfaden zur Problembehandlung enthält Anweisungen dazu, welche Informationen beim Öffnen eines Supporttickets zu Netzwerkkonnektivitätsproblemen bereitgestellt werden müssen. Mit den richtigen Informationen können wir das Problem möglicherweise schneller beheben.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Dienstkonnektivitätsproblem bei serverlosem SQL-Pool

### <a name="symptom-1"></a>Symptom 1

Die Option „Serverloser SQL-Pool“ ist in der Dropdownliste „Verbinden mit“ abgeblendet dargestellt.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptom 2

Wenn Sie die Abfrage mit „Serverloser SQL-Pool“ ausführen, erhalten Sie die Fehlermeldung „Fehler beim Herstellen einer Verbindung zum Server“.

![Symptom 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

> [!NOTE] 
>    Die folgenden Schritte zur Problembehandlung sind für Microsoft Edge (Chromium) und Chrome vorgesehen. Sie können andere Browser (z. B. FireFox) mit den gleichen Schritten zur Problembehandlung verwenden, aber das Fenster „Entwicklertool“ kann ein anderes Layout als die hier enthaltenen Screenshots aufweisen. Verwenden Sie nach Möglichkeit NICHT das klassische Microsoft Edge zur Problembehandlung, da es in bestimmten Situationen ungenaue Informationen anzeigen kann.

Öffnen Sie das Fenster „Diagnoseinformationen“, wählen Sie die Schaltfläche „Diagnose herunterladen“ aus. Behalten Sie die heruntergeladenen Informationen für die Fehlerberichterstellung bei. Sie können stattdessen die „Sitzungs-ID“ kopieren und beim Öffnen des Supporttickets anfügen.

![diagnostic-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Wiederholen Sie den Vorgang, den Sie in Azure Synapse Studio ausgeführt haben, um die Problembehandlung zu beginnen.

- Wählen Sie für Symptom 1 die Schaltfläche „Aktualisieren“ rechts neben dem Dropdownmenü „Datenbank verwenden“ auf der Registerkarte „SQL-Skript“ aus, und prüfen Sie, ob „Serverloser SQL-Pool“ angezeigt wird.
- Versuchen Sie bei Symptom 2, die Abfrage erneut auszuführen, um zu prüfen, ob sie erfolgreich ausgeführt wird.

Wenn das Problem weiterhin besteht, drücken Sie F12 in Ihrem Browser, um „Entwicklertools“ (DevTools) zu öffnen.

Wechseln Sie im Fenster „Entwicklertools“ zum Fenster „Netzwerk“. Wählen Sie bei Bedarf auf der Symbolleiste im Fenster „Netzwerk“ die Schaltfläche „Löschen“ aus.
Stellen Sie sicher, dass im Fenster „Netzwerk“ die Option „Cache deaktivieren“ aktiviert ist.

Wiederholen Sie den Vorgang, den Sie in Azure Synapse Studio ausgeführt haben. Möglicherweise werden neue Elemente in der Liste „Netzwerk“ in „Entwicklertools“ angezeigt. Notieren Sie sich die aktuelle Systemzeit, die im Supportticket bereitgestellt werden soll.

![network-panel 1](media/troubleshooting-synapse-studio/network-panel.png)

Suchen Sie das Element, dessen URL-Spalte mit dem folgenden Muster übereinstimmt:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Dabei ist [*A*] Ihr Arbeitsbereichsname und „-ondemand“ könnte „-sqlod“ sein, während [*B*] ein Datenbankname wie „master“ sein sollte. Es sollten höchstens zwei Elemente mit dem gleichen URL-Wert, aber unterschiedlichen Methodenwerten vorhanden sein: OPTIONS und POST. Überprüfen Sie, ob diese beiden Elemente in der Spalte „Status“ den Wert „200“ oder „20x“ aufweisen, wobei „x“ eine beliebige einzelne Ziffer sein könnte.

Wenn eines der Elemente etwas anderes als „20x“ aufweist und:

- Der Status beginnt mit „(failed)“: Verbreitern Sie die Spalte „Status“, oder zeigen Sie mit dem Mauszeiger auf den Statustext, um den vollständigen Text anzuzeigen. Fügen Sie den Text und/oder den Screenshot ein, wenn Sie das Supportticket öffnen.

    ![Statustext](media/troubleshooting-synapse-studio/status-text.png)

    - Wenn ERR_NAME_NOT_RESOLVED angezeigt wird und Sie Ihren Arbeitsbereich innerhalb von 10 Minuten erstellt haben, warten Sie 10 Minuten und versuchen Sie erneut zu prüfen, ob das Problem immer noch besteht.
    - Wenn ERR_INTERNET_DISCONNECTED oder ERR_NETWORK_CHANGED angezeigt wird, deutet dies möglicherweise darauf hin, dass Ihre PC-Netzwerkverbindung Probleme hat. Überprüfen Sie Ihre Netzwerkverbindung, und wiederholen Sie den Vorgang.
    - Wenn ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR oder andere Fehlercodes angezeigt werden, die „SSL“ enthalten, deutet dies möglicherweise darauf hin, dass Ihre lokale SSL-Konfiguration Probleme hat oder dass Ihr Netzwerkadministrator den Zugriff auf den serverlosen SQL-Poolserver blockiert hat. Öffnen Sie ein Supportticket, und fügen Sie den Fehlercode in der Beschreibung ein.
    - Wenn ERR_NETWORK_ACCESS_DENIED angezeigt wird, müssen Sie möglicherweise mit dem Administrator prüfen, ob Ihre lokale Firewallrichtlinie den Zugriff entweder auf die Domäne „*.database.windows.net“ oder den Remoteport 1443 blockiert hat.
    - Versuchen Sie optional, den gleichen Vorgang direkt auf einem anderen Computer und/oder in einer Netzwerkumgebung auszuführen, um ein Problem mit der Netzwerkkonfiguration auf Ihrem PC auszuschließen.

- Status ist „40x“, „50x“ oder weist einen anderen Wert auf. Wählen Sie die Elemente aus, um Details anzuzeigen. Die Details zum Element sollten auf der rechten Seite angezeigt werden. Suchen Sie den Abschnitt „Antwortheader“, und prüfen Sie dann, ob ein Element namens „access-control-allow-origin“ vorhanden ist. Wenn dies der Fall ist, überprüfen Sie, ob es einen der folgenden Werte aufweist:

    - `*` (einzelnes Sternchen)
    - https://web.azuresynapse.net/ (oder ein anderer Wert, mit dem der Text in der Adressleiste des Browsers beginnt)

Wenn der Antwortheader einen der obigen Werte enthält, bedeutet dies, dass die Fehlerinformationen bereits gesammelt worden sein sollten. Sie können bei Bedarf ein Supportticket öffnen und optional den Screenshot der Elementdetails anfügen.

Wenn der Header nicht angezeigt wird oder der Header nicht über einen der oben aufgeführten Werte verfügt, fügen Sie einen Screenshot der Elementdetails an, wenn Sie das Ticket öffnen.

 
![Elementdetails](media/troubleshooting-synapse-studio/item-details.png)
 
Wenn das Problem durch die oben genannten Schritte nicht gelöst werden kann, müssen Sie möglicherweise ein Supportticket öffnen. Wenn Sie Ihr Supportticket übermitteln, geben Sie die „Sitzungs-ID“ oder „Diagnoseinformationen“ an, die am Anfang dieses Leitfadens heruntergeladen wurden.

Wenn Sie das Problem melden, können Sie optional einen Screenshot der Registerkarte „Konsole“ in der „Entwicklertools“ erstellen und an das Supportticket anfügen. Scrollen Sie durch den Inhalt, und erstellen Sie ggf. mehr als einen Screenshot, um die gesamte Nachricht zu erfassen.

![Entwicklertoolkonsole](media/troubleshooting-synapse-studio/developer-tool-console.png)

Wenn Sie Screenshots anfügen, geben Sie die Uhrzeit (oder einen geschätzten Zeitraum) an, zu der Sie die Screenshots erstellt haben. Es wird uns bei der Untersuchung des Problems helfen.

Bestimmte Browser unterstützen die Anzeige von Zeitstempeln auf der Registerkarte „Konsole“. Für Microsoft Edge (Chromium)/Chrome öffnen Sie das Dialogfeld „Einstellungen“ unter „Entwicklertools“, und aktivieren Sie „Zeitstempel anzeigen“ auf der Registerkarte „Einstellungen“.

![Einstellungen der Entwicklertoolkonsole](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Zeitstempel anzeigen](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Nächste Schritte
Wenn die vorherigen Schritte nicht zur Lösung Ihres Problems beitragen, [erstellen Sie ein Supportticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
