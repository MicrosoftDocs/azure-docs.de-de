---
title: Verknüpfen von Symantec ICDx-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Finden Sie heraus, wie Sie den Symantec ICDx-Connector verwenden, um alle Protokolle Ihrer Symantec-Sicherheitslösung ganz einfach mit Azure Sentinel zu verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092705"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Herstellen einer Verbindung mit Ihrer Symantec ICDx-Appliance 



Mit dem Connector für Symantec ICDx können Sie alle Protokolle Ihrer Symantec-Sicherheitslösungen einfach mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Dadurch erhalten Sie einen besseren Einblick in das Netzwerk Ihrer Organisation und bessere Möglichkeiten für Sicherheitsvorgänge. Die Integration von Symantec ICDx und Azure Sentinel stützt sich auf die REST-API.


> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurieren von und Herstellen einer Verbindung mit Symantec ICDx 

Symantec ICDx kann mit Azure Sentinel integriert werden und Protokolle dann direkt an Azure Sentinel exportieren.

1. Öffnen Sie die ICDx-Verwaltungskonsole zum Hinzufügen von Weiterleitungen von Microsoft Azure Sentinel (Log Analytics).
2. Klicken Sie in der ICDX-Navigationsleiste auf **Configuration** (Konfiguration). 
3. Klicken Sie am oberen Rand des Bildschirms **Configuration** (Konfiguration) auf **Forwarders** (Weiterleitungen).
4. Klicken Sie unter **Forwarders** (Weiterleitungen) neben Microsoft Azure Sentinel (Log Analytics) auf **Add** (Hinzufügen). 
4. Klicken Sie im Fenster **Microsoft Azure Sentinel (Log Analytics)** auf **Show Advanced** (Erweiterte anzeigen). 
5. Gehen Sie am oberen Rand des erweiterten Fensters „Microsoft Azure Sentinel (Log Analytics)“ folgendermaßen vor:
    -   **Name**: Geben Sie einen Namen für die Weiterleitung ein, der nicht mehr als 30 Zeichen lang ist. Wählen Sie einen eindeutigen, aussagekräftigen Namen. Dieser Name wird in der Liste der Weiterleitungen auf dem Bildschirm **Configuration** (Konfiguration) und in den Dashboards auf dem Bildschirm **Dashboard** angezeigt. Beispiel: Microsoft Azure Log Analytics East. Dies ist ein Pflichtfeld.
    -   **Beschreibung**: Geben Sie eine Beschreibung für die Weiterleitung ein. Diese Beschreibung wird auch in der Liste der Weiterleitungen auf dem Bildschirm **Configuration** (Konfiguration) angezeigt. Beziehen Sie Details wie z.B. den weitergeleiteten Ereignistyp und die Gruppe ein, die benötigt wird, um die Daten zu überprüfen.
    -   **Startup Type** (Starttyp): Wählen Sie die Startmethode für die Weiterleitungskonfiguration aus. Sie können zwischen manuell und automatisch wählen.<br>Der Standard ist „Automatic“ (Automatisch). 
6. Führen Sie unter **Events** (Ereignisse) folgende Schritte aus: 
    - **Quelle**: Wählen Sie mindestens ein Archiv aus, von wo Ereignisse weitergeleitet werden. Sie können aktive Collectorarchive (einschließlich Common Archive), verwaiste Collectorarchive (d.h. Archive für die Collectoren, die Sie gelöscht haben), ICDx-Empfängerarchive oder das System Archive auswählen. <br>Der Standard ist das Common Archive.
      > [!NOTE]
      > ICDx-Empfängerarchive werden separat nach Namen aufgelistet. 
 
    - **Filter**: Hinzufügen eines Filters, der die Teilmenge der weiterzuleitenden Ereignisse angibt. Führen Sie eines der folgenden Verfahren aus:
        - Um eine Filterbedingung auszuwählen, klicken Sie auf einen Typ, Attribut, Operator und Wert. 
        - Überprüfen Sie im Feld „Filter“ Ihre Filterbedingung. Sie können sie, wie erforderlich, direkt im Feld bearbeiten oder löschen.
        - Klicken Sie auf AND bzw. OR, um Ihre Filterbedingung hinzuzufügen.
        - Sie können auch auf „Saved Queries“ (Gespeicherte Abfragen) klicken, um eine gespeicherte Abfrage anzuwenden.
    - **Included Attributes** (Enthaltene Attribute): Geben Sie die durch Trennzeichen getrennte Liste von Attributen ein, die in den weitergeleiteten Daten enthalten sein sollen. Die enthaltenen Attribute haben Vorrang vor ausgeschlossenen Attributen.
    - **Excluded Attributes** (Ausgeschlossene Attribute): Geben Sie die durch Trennzeichen getrennte Liste von Attributen ein, die von den weitergeleiteten Daten ausgeschlossen sein sollen.
    - **Batch Size** (Batchgröße): Wählen Sie die Anzahl der pro Batch zu sendenden Ereignisse aus. Die Optionen sind 10, 50, 100, 500 und 1.000.<br>Der Standardwert ist 100. 
    - **Rate Limit** (Übertragungsratenlimit): Wählen Sie die Übertragungsrate ausgedrückt in Ereignissen pro Sekunde aus, mit der Ereignisse weitergeleitet werden. Mögliche Optionen sind „Unlimited“ (Unbegrenzt), 500, 1.000, 5.000, 10.000. <br> Der Standard ist 5.000. 
7. Gehen Sie unter **Azure Destination** (Azure-Ziel) folgendermaßen vor: 
    - **Workspace ID** (Arbeitsbereichs-ID): Fügen Sie die Arbeitsbereichs-ID aus dem Folgenden ein. Dies ist ein Pflichtfeld.
    - **Primary Key** (Primärschlüssel): Fügen Sie den Primärschlüssel aus dem Folgenden ein. Dies ist ein Pflichtfeld.
    - **Custom Log Name** (Name des benutzerdefinierten Protokolls): Geben Sie den Namen des benutzerdefinierten Protokolls im Log Analytics-Arbeitsbereich des Microsoft Azure-Portals ein, zu dem Sie Ereignisse weiterleiten möchten. Der Standardwert ist SymantecICDx. Dies ist ein Pflichtfeld.
8. Klicken Sie auf *Save* (Speichern), um die Weiterleitungskonfiguration abzuschließen. 
9. Um die Weiterleitung zu starten, klicken Sie unter **Options** (Optionen) auf **More** (Weitere) und dann **Start** (Starten).
10. Um in Log Analytics das relevante Schema für die Symantec ICDx-Ereignisse zu verwenden, suchen Sie nach **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 



## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Symantec ICDx mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.


