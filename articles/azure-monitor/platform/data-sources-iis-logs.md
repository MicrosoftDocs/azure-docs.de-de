---
title: Sammeln von IIS-Protokollen mit dem Log Analytics-Agent in Azure Monitor
description: IIS (Internet Information Services, Internetinformationsdienste) speichern Benutzeraktivitäten in Protokolldateien, die von Azure Monitor gesammelt werden können.  In diesem Artikel wird erläutert, wie Sie das Sammeln von IIS-Protokollen konfigurieren. Darüber hinaus enthält der Artikel Details zu den Datensätzen, die in Azure Monitor erstellt werden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2020
ms.openlocfilehash: a089631ab199b0fe997bba001561c6b027034e2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993685"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Sammeln von IIS-Protokollen mit dem Log Analytics-Agent in Azure Monitor
IIS (Internet Information Services, Internetinformationsdienste) speichern Benutzeraktivitäten in Protokolldateien, die vom Log Analytics-Agent gesammelt und in [Azure Monitor-Protokollen](data-platform.md) gespeichert werden können.

> [!IMPORTANT]
> In diesem Artikel wird das Sammeln von IIS-Protokollen mit dem [Log Analytics-Agent](log-analytics-agent.md) beschrieben, einem der von Azure Monitor verwendeten Agents. Andere Agents sammeln andere Daten und werden anders konfiguriert. Eine Liste der verfügbaren Agents und der von ihnen gesammelten Daten finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md).

![IIS-Protokolle](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurieren von IIS-Protokollen
Azure Monitor sammelt Einträge aus von IIS erstellten Protokolldateien. Daher müssen Sie [IIS für die Protokollierung konfigurieren](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Azure Monitor unterstützt nur IIS-Protokolldateien im W3C-Format, aber keine benutzerdefinierten Felder oder die erweiterte IIS-Protokollierung. Protokolle im NCSA- oder nativen IIS-Format werden nicht gesammelt.

IIS-Protokolle können in Azure Monitor über das Menü [Erweiterte Einstellungen](agent-data-sources.md#configuring-data-sources) für den Log Analytics-Agent konfiguriert werden.  Abgesehen von der Auswahl der Option **IIS-Protokolldateien im W3C-Format sammeln** ist keine Konfiguration erforderlich.


## <a name="data-collection"></a>Datensammlung
Azure Monitor erfasst bei jeder Änderung des Protokollzeitstempels IIS-Protokolleinträge von allen Agents. Das Protokoll wird alle **fünf Minuten** gelesen. Falls IIS den Zeitstempel beim Erstellen einer neuen Datei nicht vor der Rolloverzeit aktualisiert, werden nach der Erstellung der neuen Datei Einträge erfasst. Die Häufigkeit der Erstellung neuer Dateien wird mit der Einstellung **Log File Rollover Schedule** (Rolloverzeitplan der Protokolldatei) für die IIS-Website gesteuert, wobei einmal täglich als Standardeinstellung festgelegt ist. Ist die Einstellung auf **Stündlich** festgelegt, erfasst Azure Monitor das Protokoll jede Stunde. Wenn die Einstellung auf **Täglich** festgelegt ist, erfasst Azure Monitor das Protokoll alle 24 Stunden.

> [!IMPORTANT]
> Es empfiehlt sich, den **Rolloverzeitplan der Protokolldatei** auf **Stündlich** festzulegen. Bei Verwendung der Einstellung **Täglich** kommt es möglicherweise zu Datenspitzen, da die Daten nur einmal pro Tag erfasst werden.

## <a name="iis-log-record-properties"></a>Eigenschaften der IIS-Protokolldatensätze
IIS-Protokolldatensätze weisen den Typ **W3CIISLog** auf und besitzen die in der folgenden Tabelle aufgeführten Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Computer |Name des Computers, auf dem das Ereignis gesammelt wurde. |
| cIP |IP-Adresse des Clients. |
| csMethod |Methode der Anforderung, beispielsweise GET oder POST. |
| csReferer |Website, von der der Benutzer über einen Link auf die aktuelle Website gelangt ist. |
| csUserAgent |Browsertyp des Clients. |
| csUserName |Name des authentifizierten Benutzers, der auf den Server zugegriffen hat. Anonyme Benutzer werden durch einen Bindestrich gekennzeichnet. |
| csUriStem |Ziel der Anforderung, beispielsweise eine Webseite. |
| csUriQuery |Abfrage, die der Client versucht hat auszuführen. |
| ManagementGroupName |Name der Verwaltungsgruppe für Operations Manager-Agents.  Bei anderen Agents lautet diese „AOI-\<workspace ID\>“. |
| RemoteIPCountry |Land/Region der IP-Adresse des Clients. |
| RemoteIPLatitude |Breitengrad der Client-IP-Adresse. |
| RemoteIPLongitude |Längengrad der Client-IP-Adresse. |
| scStatus |HTTP-Statuscode. |
| scSubStatus |Unterstatus-Fehlercode. |
| scWin32Status |Windows-Statuscode. |
| sIP |IP-Adresse des Webservers. |
| SourceSystem |Operations Manager |
| sPort |Port auf dem Server, mit dem der Client verbunden ist. |
| sSiteName |Name der IIS-Website. |
| TimeGenerated |Datum und Uhrzeit, zu der der Eintrag protokolliert wurde. |
| TimeTaken |Verarbeitungsdauer der Anforderung in Millisekunden. |

## <a name="log-queries-with-iis-logs"></a>Protokollieren von Abfragen mit IIS-Protokollen
Die folgende Tabelle zeigt verschiedene Beispiele für Protokollabfragen, die IIS-Protokolldatensätze abrufen.

| Abfrage | BESCHREIBUNG |
|:--- |:--- |
| W3CIISLog |Alle IIS-Protokolldatensätze. |
| W3CIISLog &#124; where scStatus==500 |Alle IIS-Protokolleinträge mit dem Rückgabestatus 500 |
| W3CIISLog &#124; summarize count() by cIP |Anzahl der IIS-Protokolleinträge nach Client-IP-Adresse. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Anzahl der IIS-Protokolleinträge nach URL für den Host www\.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Gesamtzahl an Bytes, die von jedem IIS-Computer empfangen wurden. |

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren Sie Azure Monitor für die Sammlung von Daten aus anderen [Datenquellen](agent-data-sources.md) zur Analyse.
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.
