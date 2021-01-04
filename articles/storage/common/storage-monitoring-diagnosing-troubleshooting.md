---
title: Überwachung, Diagnose und Problembehandlung in Azure Storage | Microsoft Docs
description: Verwenden Sie Features wie Speicheranalyse, clientseitige Protokollierung und andere Tools von Drittanbietern, um Probleme im Zusammenhang mit Azure Storage zu erkennen, zu diagnostizieren und zu beheben.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f8b555c4022fcf2532a7350839d2357c96562f4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791850"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Übersicht
Diagnose und Problembehandlung können in einer verteilten Anwendung, die in einer Cloudumgebung gehostet wird, komplexer sein als in herkömmlichen Umgebungen. Anwendungen können in einer PaaS- oder IaaS-Infrastruktur, lokal, auf einem mobilen Gerät oder in einer Kombination dieser Umgebungen bereitgestellt werden. In der Regel kann der Netzwerkverkehr der Anwendung öffentliche und private Netzwerke durchlaufen, und Ihre Anwendung kann mehrere Speichertechnologien wie Microsoft Azure Storage-Tabellen, Blobs, Warteschlangen oder Dateien neben anderen Datenspeichern wie relationalen und Dokumentendatenbanken verwenden.

Um solche Anwendungen erfolgreich zu verwalten, sollten Sie sie proaktiv überwachen und verstehen, wie die Diagnose und Problembehandlung aller Anwendungsaspekte und ihrer abhängigen Technologien durchzuführen sind. Als Nutzer des Azure-Speicherdiensts sollten Sie ständig die von Ihrer Anwendung verwendeten Speicherdienste überwachen, um unerwartete Verhaltensveränderungen zu erkennen (beispielsweise langsamere Antwortzeiten als üblich), und Protokollierung verwenden, um detailliertere Daten zu sammeln und ein Problem in der Tiefe zu analysieren. Die Diagnoseinformationen, die Sie sowohl aus der Überwachung als auch der Protokollierung erhalten, werden Ihnen helfen, die Ursache des Problems, das bei Ihrer Anwendung auftritt, zu bestimmen. Dann können Sie das Problem behandeln und die entsprechenden Schritte, die Sie für seine Beseitigung ergreifen können, festlegen. Azure-Speicher ist ein Kerndienst von Azure und ein wichtiger Bestandteil der meisten Lösungen, die Kunden auf der Azure-Infrastruktur bereitstellen. Azure-Speicher beinhaltet Funktionen zur vereinfachten Überwachung, Diagnose und Problembehandlung von Speicherproblemen in Ihren cloudbasierten Anwendungen.

* [Einführung]
  * [Wie diese Anleitung aufgebaut ist]
* [Überwachung Ihres Speicherdiensts]
  * [Überwachung der Dienstintegrität]
  * [Kapazitätsüberwachung]
  * [Verfügbarkeitsüberwachung]
  * [Leistungsüberwachung]
* [Diagnose von Speicherproblemen]
  * [Probleme mit der Dienstintegrität]
  * [Leistungsprobleme]
  * [Fehlerdiagnose]
  * [Probleme mit dem Speicheremulator]
  * [Speicher-Protokollierungstools]
  * [Verwendung von Netzwerk-Protokollierungstools]
* [Durchgängige Verfolgung]
  * [Korrelation von Protokollierungsdaten]
  * [Clientanfrage-ID]
  * [Serveranfrage-ID]
  * [Zeitstempel]
* [Anleitungen zur Problembehandlung]
  * [Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]
  * [Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]
  * [Metriken zeigen hohe AverageServerLatency an]
  * [Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]
  * [Metriken zeigen Anstieg bei PercentThrottlingError an]
  * [Metriken zeigen Anstieg bei PercentTimeoutError an]
  * [Metriken zeigen Anstieg bei PercentNetworkError an]
  * [Der Client empfängt HTTP 403 (Verboten)-Meldungen]
  * [Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]
  * [Der Client empfängt HTTP 409 (Konflikt)-Meldungen]
  * [Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]
  * [Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]
  * [Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]
  * [Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]
  * [Sie haben ein anderes Problem mit einem Speicherdienst]
  * [Troubleshoot attached VHDs on Azure Windows virtual machines](../../virtual-machines/troubleshooting/index.yml) (Problembehandlung für angefügte VHDs auf virtuellen Windows-Computern)   
  * [Troubleshoot attached VHDs on Azure Linux virtual machines](../../virtual-machines/troubleshooting/index.yml) (Problembehandlung für angefügte VHDs auf virtuellen Linux-Computern)
  * [Behandlung von Problemen in Azure Files in Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Beheben von Problemen mit Azure Files unter Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Anhänge]
  * [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr]
  * [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr]
  * [Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten]
  * [Anhang 5: Überwachung mit Application Insights für Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Einführung
Diese Anleitung zeigt Ihnen, wie Sie Funktionen wie Azure Storage Analytics, clientseitige Protokollierung in der Azure-Speicherclient-Bibliothek und andere Tools von Drittanbietern verwenden, um mit Azure-Speicher verbundene Probleme zu erkennen, zu diagnostizieren und zu beheben.

![Diagramm, das den Informationsfluss zwischen Clientanwendungen und Azure-Speicherdiensten zeigt.][1]

Dieser Leitfaden richtet sich in erster Linie an Entwickler von Online-Diensten, die Azure-Speicherdienste verwenden, sowie an IT-Experten, die für die Verwaltung solcher Online-Dienste verantwortlich sind. Mit dieser Anleitung möchten wir:

* Ihnen helfen, die Integrität und Leistungsfähigkeit Ihres Azure-Speicherkontos aufrechtzuerhalten
* Ihnen die notwendigen Prozesse und Tools für die Diagnose an die Hand geben, ob ein Problem in einer Anwendung mit Azure Storage zusammenhängt.
* Ihnen umsetzbare Anleitungen für die Lösung von Problemen in Verbindung mit Azure-Speicher bereitstellen

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Wie diese Anleitung aufgebaut ist
Der Abschnitt "[Überwachung Ihres Speicherdiensts]" beschreibt, wie Sie die Integrität und Leistungsfähigkeit Ihres Azure-Speicherdiensts mit Azure-Speicher-Analytikmetriken (Speichermetriken) überwachen können.

Der Abschnitt "[Diagnose von Speicherproblemen]" beschreibt, wie Probleme unter Verwendung von Azure-Speicheranalytik-Protokollierung (Speicherprotokollierung) diagnostiziert werden. Er beschreibt auch, wie Sie die clientseitige Protokollierung unter Verwendung einer der Clientbibliotheken wie der Speicher-Clientbibliothek für .NET oder des Azure SDK für Java aktivieren.

Der Abschnitt "[Durchgängige Verfolgung]" beschreibt, wie Sie die in Protokollierungsdateien und Metrikdaten enthaltenen Informationen zuordnen.

Der Abschnitt "[Anleitungen zur Problembehandlung]" stellt Anleitungen für die Behandlung einiger der allgemeinen speicherbezogenen Probleme bereit, auf die Sie stoßen könnten.

Die [Anhänge] enthalten Informationen zur Verwendung anderer Tools wie Wireshark und Netmon für die Analyse von Netzwerkpaketdaten und Fiddler für die Analyse von HTTP-/HTTPS-Nachrichten.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Überwachung Ihres Speicherdiensts
Wenn Sie mit der Windows-Leistungsüberwachung vertraut sind, können Sie von Speichermetriken als einem Azure-Speicher-Pendant zu Windows-Leistungsüberwachungsindikatoren ausgehen. In Speichermetriken finden Sie einen umfassenden Metriksatz (Indikatoren in der Windows Performance Monitor-Terminologie) wie Dienstverfügbarkeit, Gesamtzahl der Dienstanfragen oder Prozentsatz der erfolgreichen Dienstanfragen. Eine vollständige Liste der verfügbaren Kennzahlen finden Sie im Thema zu den [Kennzahlen zur Speicheranalyse – Tabellenschema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Sie können spezifizieren, ob der Speicherdienst die Metriken jede Stunde oder jede Minute sammeln und aggregieren soll. Weitere Informationen zur Metrik-Aktivierung und Überwachung Ihrer Speicherkonten finden Sie unter [Aktivieren der Speichermetriken und Anzeigen von Metrikdaten](../blobs/monitor-blob-storage.md).

Sie können auswählen, welche Stundenmetriken Sie im [Azure-Portal](https://portal.azure.com) anzeigen möchten, und Regeln konfigurieren, die den Administrator per E-Mail benachrichtigen, wenn eine Stundenmetrik einen bestimmten Schwellenwert überschreitet. Weitere Informationen finden Sie unter [Empfangen von Warnungsbenachrichtigungen](../../azure-monitor/platform/alerts-overview.md).

Es wird empfohlen, [Azure Monitor für Storage](../../azure-monitor/insights/storage-insights-overview.md) (Vorschau) zu überprüfen. Dieses Feature von Azure Monitor ermöglicht eine umfassende Überwachung ihrer Azure Storage-Konten, indem eine einheitliche Ansicht der Leistung, Kapazität und Verfügbarkeit Ihrer Azure Storage-Dienste bereitgestellt wird. Sie müssen nichts aktivieren oder konfigurieren und können diese Metriken aus den vordefinierten interaktiven Diagrammen und anderen darin enthaltenen Visualisierungen sofort anzeigen.

Der Speicherdienst sammelt Metriken nach dem Best-Effort-Prinzip, kann aber nicht jeden Speichervorgang aufzeichnen.

Im Azure-Portal können Sie Metriken wie Verfügbarkeit, Gesamtanforderungen und durchschnittliche Latenzzahlen für ein Speicherkonto anzeigen. Zudem wurde eine Benachrichtigungsregel eingerichtet, um einen Administrator zu benachrichtigen, wenn die Verfügbarkeit unter ein bestimmtes Niveau sinkt. Aus der Anzeige dieser Daten ergibt sich als möglicher Untersuchungsbereich der unter 100 Prozent liegende Erfolgsprozentsatz des Tabellenspeicherdiensts. (Weitere Informationen finden Sie im Abschnitt [Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"].)

Sie sollten Ihre Azure-Anwendungen kontinuierlich überwachen, um sicherzustellen, dass sie wie erwartet integer und leistungsfähig sind, durch:

* Festlegung einiger Baseline-Metriken für die Anwendung, mit denen Sie die aktuellen Daten vergleichen und alle wesentlichen Änderungen im Verhalten des Azure-Speichers und Ihrer Anwendung identifizieren können. Die Werte Ihrer Baseline-Metriken werden in vielen Fällen anwendungsspezifisch sein und sollten von Ihnen bei der Durchführung der Leistungstest für Ihre Anwendung festgelegt werden.
* Aufzeichnung von Minutenmetriken und ihre Verwendung zur aktiven Überwachung unerwarteter Fehler und Anomalien wie Spitzen in Fehlerzahlen oder Anfrageraten.
* Aufzeichnung von Stundenmetriken und ihre Verwendung zur Überwachung von Durchschnittswerten wie durchschnittliche Fehlerzahlen und Anfrageraten.
* Untersuchung potentieller Probleme unter Verwendung von Diagnosetools, entsprechend der Darstellung weiter unten im Abschnitt "[Diagnose von Speicherproblemen]."

Die Diagramme in der folgenden Abbildung illustrieren, wie die Durchschnittsberechnung für die Stundenmetrik Aktivitätsspitzen verstecken kann. Die Stundenmetriken scheinen eine konstante Anfragerate anzuzeigen, während die Minutenmetriken die wirklichen Schwankungen offenbaren.

![Charts, die illustrieren, wie Aktivitätsspitzen bei der Durchschnittsberechnung für die Stundenmetrik ausgeblendet werden können.][3]

Im verbliebenen Teil dieses Abschnitts wird beschrieben, welche Metriken Sie überwachen sollten und warum.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Überwachung der Dienstintegrität
Sie können das [Azure-Portal](https://portal.azure.com) verwenden, um die Integrität des Speicherdiensts (und anderer Azure-Dienste) in allen Azure-Regionen der Welt anzuzeigen. Dank der Überwachung können Sie sofort sehen, ob ein Problem außerhalb Ihrer Kontrolle den Speicherdienst in der Region beeinträchtigt, in der Sie Ihre Anwendung verwenden.

Das [Azure-Portal](https://portal.azure.com) kann auch Benachrichtigungen zu Vorfällen bereitstellen, die die verschiedenen Azure-Dienste beeinträchtigen.
Hinweis: Diese Informationen waren bisher, zusammen mit Verlaufsdaten, auf dem [Azure-Dienstdashboard](https://status.azure.com)verfügbar.

Während das [Azure-Portal](https://portal.azure.com) Zustandsinformationen innerhalb der Azure-Rechenzentren (Inside-out-Überwachung) sammelt, könnten Sie auch einen Outside-in-Ansatz in Erwägung ziehen, um synthetische Transaktionen zu generieren, die in regelmäßigen Abständen von mehreren Standorten aus auf Ihre in Azure gehostete Webanwendung zugreifen. Die von [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) und Application Insights für Azure DevOps angebotenen Dienste sind Beispiele für diesen Ansatz. Weitere Informationen zu Application Insights für Azure DevOps finden Sie in [Anhang 5: Überwachung mit Application Insights für Azure DevOps](#appendix-5).

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Kapazitätsüberwachung
Speichermetriken speichern nur Kapazitätsmetriken für den Blob-Dienst, weil Blobs in der Regel den größten Teil der gespeicherten Daten ausmachen. (Es ist zum Redaktionszeitpunkt nicht möglich, Speichermetriken zu verwenden, um die Kapazität Ihrer Tabellen und Warteschlangen zu überwachen.) Sie finden diese Daten in der Tabelle **$MetricsCapacityBlob** , wenn Sie die Überwachung für den Blob-Dienst aktiviert haben. Speichermetriken zeichnen diese Daten einmal täglich auf, und Sie können anhand des Wert von **RowKey** bestimmen, ob die Zeile eine Entität enthält, die sich auf Benutzerdaten (Wert **data** ) oder Analysedaten (Wert **analytics** ) bezieht. Jede gespeicherte Entität enthält Informationen zum verwendeten Speicheranteil ( **Capacity** , gemessen in Bytes) sowie zur aktuellen Anzahl von Containern ( **ContainerCount** ) und Blobs ( **ObjectCount** ) im Speicherkonto. Weitere Informationen über die in der Tabelle **$MetricsCapacityBlob** gespeicherten Kapazitätsmetriken finden Sie unter [Tabellenschema der Speicher-Analytikmetriken](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Sie sollten diese Werte überwachen, um früh zu erkennen, dass Sie sich den Kapazitätsgrenzen Ihres Speicherkontos nähern. Im Azure-Portal können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die Gesamtspeichernutzung die von Ihnen festgelegten Grenzwerte über- oder unterschreitet.
>
>

Hilfe für die Schätzung der Größe der verschiedenen Speicherobjekte wie Blobs finden Sie im Blogbeitrag [Microsoft Azure-Speicherabrechnung verstehen – Bandbreite, Transaktionen und Kapazität](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Verfügbarkeitsüberwachung
Sie sollten die Verfügbarkeit des Speicherdiensts in Ihrem Speicherkonto überwachen, indem Sie in den Tabellen für Stunden- oder Minutenmetriken den Wert in der Spalte **Availability** überwachen: **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable** , **$MetricsHourPrimaryTransactionsQueue** , **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable** , **$MetricsMinutePrimaryTransactionsQueue** , **$MetricsCapacityBlob** . Die Spalte **Availability** enthält einen Prozentwert, der die Verfügbarkeit des Diensts oder API-Vorgangs in der jeweiligen Zeile darstellt. (Der Wert von **RowKey** gibt Aufschluss darüber, ob die Zeile allgemeine Metriken für den Dienst oder Metriken für einen bestimmten API-Vorgang enthält.)

Jeder unter 100 % liegende Wert zeigt an, dass einige Speicheranfragen fehlschlagen. Zur Ermittlung der Fehlerursache können Sie die anderen Spalten in den Metrikdaten untersuchen, in denen die Anzahl von Anfragen mit verschiedenen Fehlerarten (beispielsweise **ServerTimeoutError** ) angezeigt wird. Aufgrund von vorübergehenden Servertimeouts, die beispielsweise auftreten können, wenn der Server Partitionen verschiebt, um eine bessere Lastverteilung der Anforderungen zu erreichen, ist mit dem vorübergehenden Absinken der **Verfügbarkeit** auf unter 100 Prozent zu rechnen. Solche zeitweiligen Bedingungen sollten von der Wiederholungslogik in Ihrer Clientanwendung behandelt werden. Im Artikel [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) sind die Transaktionsarten aufgeführt, die die Speichermetriken in ihrer Berechnung der **Verfügbarkeit** enthalten.

Im [Azure-Portal](https://portal.azure.com) können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die **Verfügbarkeit** den von Ihnen festgelegten Grenzwert unterschreitet.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme in Verbindung mit der Verfügbarkeit.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Leistungsüberwachung
Um die Leistung des Speicherdiensts zu überwachen, können Sie die folgenden Metriken aus den Stunden- und Minuten-Metriktabellen verwenden.

* Die Werte in den Spalten **AverageE2ELatency** und **AverageServerLatency** zeigen die vom Speicherdienst oder der API-Operationsart benötigte Durchschnittszeit für die Anfragebearbeitung an. **AverageE2ELatency** ist eine Kennzahl für die End-to-End-Latenz, die neben der Zeit für die Anforderungsverarbeitung auch die Zeit beinhaltet, die zum Lesen der Anforderung und zum Senden der Antwort benötigt wird. (Der Wert beinhaltet also die Netzwerklatenz nach Erreichen des Speicherdiensts). **AverageServerLatency** ist eine reine Kennzahl für die Verarbeitungszeit und enthält daher keine mit der Clientkommunikation verbundene Netzwerklatenz. Informationen zu den Unterschieden zwischen diesen beiden Werten finden Sie weiter unten in dieser Anleitung im Abschnitt [Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an].
* Die Werte in den Spalten **TotalIngress** und **TotalEgress** zeigen die Gesamtdatenmenge (in Bytes), die bei Ihrem Speicherdienst eingehen oder von ihm ausgehen bzw. eine bestimmte API-Vorgangsart durchlaufen.
* Die Werte in der Spalte **TotalRequests** zeigen die Gesamtzahl der Anfragen an, die beim Speicherdienst der API-Operation eingehen. **TotalRequests** ist die Gesamtzahl der Anfragen, die beim Speicherdienst eingehen.

In der Regel werden Sie unerwartete Veränderungen bei einem dieser Werte überwachen. Sie sind der Indikator für ein Problem, das untersucht werden muss.

Im [Azure-Portal](https://portal.azure.com) können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die Leistungsmetriken für diesen Dienst einen von Ihnen festgelegten Grenzwert über- oder unterschreiten.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme in Verbindung mit der Verfügbarkeit.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnose von Speicherproblemen
Es gibt verschiedene Möglichkeiten, um Probleme in Ihrer Anwendung zu erkennen. Dazu gehören:

* Ein Hauptfehler, der zum Absturz oder Ausfall der Anwendung führt
* Wesentliche Änderungen gegenüber den Ausgangswerten in den von Ihnen überwachten Metriken, entsprechend der Beschreibung im vorhergegangenen Abschnitt "[Überwachung Ihres Speicherdiensts]"
* Berichte von Anwendungsbenutzern, dass bestimmte Vorgänge nicht wie erwartet abgeschlossen wurden oder dass einige Funktionen nicht funktionieren
* In Ihrer Anwendung erzeugte Fehler, die in Protokollierungsdateien oder durch eine andere Benachrichtigungsmethode angezeigt werden

In der Regel fallen Probleme zu Azure-Speicherdiensten unter eine von vier weit gefassten Kategorien:

* Ihre Anwendung hat ein Leistungsproblem gemeldet, das entweder von Ihren Benutzern gemeldet oder durch Änderungen in den Leistungskennzahlen angezeigt wurde.
* Es gibt ein Problem mit der Azure-Speicher-Infrastruktur in einer oder mehrerer Regionen.
* In Ihrer Anwendung ist ein Fehler aufgetreten, der entweder von Ihren Benutzern gemeldet oder durch einen Anstieg in einer der von Ihnen überwachten Fehlerzahl-Metriken aufgezeigt wurde.
* Während der Entwicklung und Tests können Sie den lokalen Speicheremulator verwenden; es können einige speziell mit der Speicheremulator-Nutzung verbundene Probleme auftreten.

Die folgenden Abschnitte beschreiben die Schritte, die Sie befolgen sollten, um Probleme in jeder dieser vier Kategorien zu diagnostizieren und zu beheben. Der unten in dieser Anleitung folgende Abschnitt "[Anleitungen zur Problembehandlung]" liefert weitere Informationen zu einigen bekannten Problemen, die auftreten können.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Probleme mit der Dienstintegrität
Probleme mit der Dienstintegrität liegen in der Regel außerhalb Ihrer Kontrolle. Das [Azure-Portal](https://portal.azure.com) bietet Informationen zu allen auftretenden Problemen mit Azure-Diensten, inklusive Speicherdiensten. Falls Sie sich beim Erstellen Ihres Speicherkontos für georedundante Speicher mit Lesezugriff entschieden haben, sollte Ihre Anwendung im Falle der Nichtverfügbarkeit Ihrer Daten im primären Speicherort vorübergehend auf die schreibgeschützte Kopie im sekundären Speicherort umschalten können. Zum Lesen des sekundären Speicherorts muss die Anwendung in der Lage sein, zwischen der Verwendung der primären und sekundären Speicherorte zu wechseln und in einem Modus mit eingeschränkter Funktionalität mit schreibgeschützten Daten zu arbeiten. Mit den Azure Speicher-Clientbibliotheken können Sie eine Wiederholungsrichtlinie für das Lesen aus dem Sekundärspeicher definieren, falls der Primärort ausfällt. Ihre Anwendung muss auch erkennen können, ob die Daten am Sekundärort konsistent sind. Weitere Informationen finden Sie im Blogbeitrag [Microsoft Azure-Speicher: Redundanzoptionen und Lesezugriff georedundanter Speicher](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Leistungsprobleme
Die Leistungsfähigkeit einer Anwendung kann subjektiv sein, vor allem aus der Benutzerperspektive. Daher ist es wichtig, Baseline-Metriken zur Verfügung zu haben, um Leistungsprobleme identifizieren zu können. Viele Faktoren können die Leistung eines Azure-Speicherdiensts aus der Client-Anwendungs-Perspektive beeinflussen. Die Ursachen für Leistungsprobleme können im Speicherdienst, im Client oder in der Netzwerkinfrastruktur liegen, daher ist es wichtig, über eine Strategie für ihre Identifizierung zu verfügen.

Nachdem Sie anhand der Messdaten ermittelt haben, wo die mögliche Ursache des Leistungsproblems liegt, können Sie anschließend die Protokollierungsdateien verwenden, um detaillierte Informationen zur Diagnose und weiteren Problembehandlung zu erhalten.

Die [Anleitungen zur Problembehandlung] weiter unten liefern weitere Informationen zu einigen bekannten leistungsbezogenen Problemen, die auftreten können.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Fehlerdiagnose
Benutzer Ihrer Anwendung können Sie über Fehler informieren, die von der Clientanwendung gemeldet werden. Speichermetriken erfassen auch die Anzahl verschiedener Fehlerarten in Ihren Speicherdiensten (beispielsweise wie **NetworkError** , **ClientTimeoutError** oder **AuthorizationError** ). Während Speichermetriken nur die Anzahl der verschiedenen Fehlerarten verzeichnen, können Sie durch die Untersuchung von Serverseite, Clientseite und Netzwerkprotokollen weitere Informationen zu Einzelanfragen erhalten. In der Regel liefert der vom Speicherdienst zurückgegebene HTTP-Statuscode einen Hinweis auf den Grund für das Scheitern der Anfrage.

> [!NOTE]
> Denken Sie daran, dass Sie mit einigen intermittierenden Fehlern rechnen müssen, beispielsweise Fehler aufgrund von vorübergehenden Netzwerkbedingungen oder Anwendungsfehler.
>
>

Die folgenden Ressourcen sind nützlich für das Verständnis von speicherbezogenem Status und Fehlercodes:

* [Bekannte REST API-Fehlercodes](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Blob-Dienst-Fehlercodes](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Warteschlangendienst-Fehlercodes](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Tabellendienst-Fehlercodes](/rest/api/storageservices/Table-Service-Error-Codes)
* [Dateidienst-Fehlercodes](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Probleme mit dem Speicheremulator
Azure SDK enthält einen Speicheremulator, den Sie auf einer Entwicklungsworkstation ausführen können. Dieser Emulator simuliert die meisten Verhaltensweisen des Azure-Speicherdiensts und ist nützlich bei Entwicklung und Test. Mit ihm können Sie Anwendungen, die Azure-Speicherdienste verwenden, ohne Azure-Abonnement und ohne Azure-Speicherkonto ausführen.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme, die bei der Verwendung des Speicheremulators aufgetreten sind.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Speicher-Protokollierungstools
Speicherprotokollierung stellt serverseitige Protokollierung von Speicheranfragen in Ihrem Azure-Speicherkonto bereit. Weitere Informationen zu Aktivierung serverseitiger Protokollierung und Zugriff auf die Protokollierungsdaten finden Sie unter [Aktivieren der Speicherprotokollierung und Zugreifen auf Protokolldaten](./storage-analytics-logging.md).

Mit der Speicher-Clientbibliothek für .NET können Sie clientseitige Protokollierungsdaten sammeln, die sich auf Speicheroperationen in Ihrer Anwendung beziehen. Weitere Informationen finden Sie unter [Clientseitige Protokollierung mit der .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).

> [!NOTE]
> In einigen Fällen (wie SAS-Authentifizierungsfehler) kann ein Benutzer einen Fehler melden, für den Sie keine Anfragedaten in den serverseitigen Speicherprotokollen finden. Sie können die Protokollierungsfunktionen der Speicher-Clientbibliothek verwenden, um zu untersuchen, ob die Ursache des Problems im Client liegt, oder Überwachungstools verwenden, um das Netzwerk zu untersuchen.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Verwendung von Netzwerk-Protokollierungstools
Sie können den Verkehr zwischen Client und Server erfassen, um detaillierte Informationen über die Daten, die Client und Server austauschen, und die zugrunde liegenden Netzwerkbedingungen bereitzustellen. Nützliche Netzwerkprotokollierungstools sind:

* [Fiddler](https://www.telerik.com/fiddler) ist ein kostenloser Web Debugging Proxy, mit dem Sie die Header und Nutzlastdaten von HTTP- und HTTPS-Anfrage- und Antwortnachrichten untersuchen können. Weitere Informationen finden Sie in [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://download.cnet.com/s/network-monitor/) und [Wireshark](https://www.wireshark.org/) sind kostenlose Tools zur Netzwerkprotokollanalyse, mit denen Sie detaillierte Paketinformationen für eine Vielzahl von Netzwerkprotokollen anzeigen können. Weitere Informationen zu Wireshark finden Sie in [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr](#appendix-2).
* Wenn Sie einen Basis-Konnektivitätstest durchführen möchten, um zu überprüfen, ob sich der Client-Computer über das Netzwerk mit dem Azure-Speicherdienst verbinden kann, können Sie dazu nicht das standardmäßige **Ping** -Tool auf dem Client verwenden. Dennoch können Sie das Tool [**tcping**](https://www.elifulkerson.com/projects/tcping.php) zur Überprüfung der Konnektivität verwenden.

In vielen Fällen reichen die Protokollierungsdaten aus der Speicherprotokollierung und der Speicher-Clientbibliothek aus, um ein Problem zu diagnostizieren, aber in einigen Szenarien können detailliertere Informationen benötigt werden, als diese Netzwerkprotokollierungstools bereitstellen können. Beispielsweise können Sie Fiddler zur Anzeige von HTTP- und HTTPS-Nachrichten verwenden. Anhand der Header- und Nutzlastdaten, die an die und von den Speicherdiensten gesendet werden, können Sie untersuchen, wie eine Clientanwendung Speicheroperationen wiederholt. Protokollierungsanalysatoren wie Wireshark operieren auf Paketebene und erlauben Ihnen dadurch die Anzeige von TCP-Daten, mit denen Sie Probleme bezüglich verlorener Pakete und Konnektivität beheben können. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>End-to-End-Ablaufverfolgung
Durchgängige Verfolgung unter Verwendung einer Vielzahl von Protokollierungsdateien ist eine nützliche Technik für die Untersuchung möglicher Probleme. Sie können die Datums-/Zeitinformationen aus Ihren Metrikdaten als Hinweis darauf verwenden, wo Sie in den Protokollierungsdateien nach den detaillierteren Informationen suchen müssen, die Ihnen bei der Fehlerbehebung helfen.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Korrelation von Protokollierungsdaten
Bei der Anzeige von Protokollen aus Clientanwendungen, Netzwerkverfolgungen und serverseitiger Speicherprotokollierung ist es entscheidend, Anfragen über die verschiedenen Protokollierungsdateien korrelieren zu können. Die Protokollierungsdateien enthalten eine Reihe von unterschiedlichen Feldern, die als Korrelationskennungen von Bedeutung sind. Die Clientanforderungs-ID ist das nützlichste Feld, das für die Korrelation von Einträgen in den verschiedenen Protokollen verwendet werden kann. Dennoch kann es manchmal nützlich sein, entweder die Serveranforderungs-ID oder Zeitstempel zu verwenden. Die folgenden Abschnitte stellen weitere Informationen zu diesen Optionen bereit.

### <a name="client-request-id"></a><a name="client-request-id"></a>Clientanfrage-ID
Die Speicherclientbibliothek erzeugt automatisch eine eindeutige Clientanforderungs-ID für jede Anforderung.

* Für das clientseitige Protokoll, das die Speicherclientbibliothek erstellt, wird die Clientanforderungs-ID im Feld **Client Request ID** in jedem auf die Anforderung bezogenen Protokolleintrag angezeigt.
* In einer Netzwerkablaufverfolgung wie mit Fiddler wird die Clientanforderungs-ID in Anforderungsnachrichten als HTTP-Headerwert **x-ms-client-request-id** angezeigt.
* Im Protokoll der serverseitigen Speicherprotokollierung wird die Clientanforderungs-ID in der Spalte „Client request ID“ angezeigt.

> [!NOTE]
> Mehrere Anforderungen können die gleiche Clientanforderungs-ID besitzen, da dieser Wert vom Client zugewiesen werden kann (obwohl die Speicherclientbibliothek automatisch einen neuen Wert zuweist). Bei Wiederholungsversuchen seitens des Clients nutzen alle Versuche die gleiche Clientanforderungs-ID. Bei einem vom Client gesendeten Batch verfügt der Batch über eine einzelne Clientanforderungs-ID.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Serveranfrage-ID
Der Speicherdienst generiert automatisch Serveranfrage-IDs.

* Im Protokoll der serverseitigen Speicherprotokollierung wird die Serveranforderungs-ID in der Spalte **Request ID header** angezeigt.
* In einer Netzwerkablaufverfolgung wie mit Fiddler wird die Serveranforderungs-ID in Antwortnachrichten als HTTP-Headerwert **x-ms-request-id** angezeigt.
* Im Protokoll der clientseitigen Protokollierung, das die Speicherclientbibliothek erstellt, wird die Serveranforderungs-ID in der Spalte **Operation Text** für den Protokolleintrag mit Details der Serverantwort angezeigt.

> [!NOTE]
> Der Speicherdienst teilt jeder empfangenen Anforderung immer eine eindeutige Serveranforderungs-ID zu, sodass jeder Wiederholungsversuch vom Client und jeder in einem Batch enthaltene Vorgang eine eindeutige Serveranforderungs-ID hat.
>
>

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Im folgenden Codebeispiel wird die Verwendung einer benutzerdefinierten Clientanforderungs-ID veranschaulicht. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Wenn die Speicherclientbibliothek im Client eine Speicherausnahme ( **StorageException** ) ausgibt, enthält die Eigenschaft **RequestInformation** ein Objekt vom Typ **RequestResult** mit einer Eigenschaft vom Typ **ServiceRequestID** . Auf ein Objekt vom Typ **RequestResult** kann auch über eine **OperationContext** -Instanz zugegriffen werden.

Das folgende Codebeispiel zeigt, wie zum Festlegen eines benutzerdefinierten Werts vom Typ **ClientRequestId** ein **OperationContext** -Objekt mit dem Speicherdienst verknüpft wird. Es zeigt auch, wie der Wert **ServerRequestId** aus der Antwortnachricht abgerufen werden kann.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>Zeitstempel
Sie können auch Zeitstempel verwenden, um verbundene Protokolleinträge zu finden, aber achten Sie auf eventuellen Zeitversatz zwischen Client und Server. Suchen Sie etwa 15 Minuten nach passenden serverseitigen Einträgen, die auf dem Zeitstempel auf dem Client basieren. Denken Sie daran, dass die Blobmetadaten für die Metriken enthaltenden Blobs den Zeitbereich für die im Blob gespeicherten Metriken anzeigen. Dieser Zeitbereich ist nützlich, wenn Sie viele Metrikblobs für die gleiche Minute oder Stunde haben.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Anleitungen zur Problembehandlung
Dieser Abschnitt wird Sie bei der Diagnose und Behandlung einiger bekannter Probleme unterstützen, die auftreten können, wenn Ihre Anwendung Azure-Speicherdienste verwendet. In der Liste unten finden Sie die für Ihre spezifische Fragestellung relevanten Informationen.

**Entscheidungsbaum für die Problembehandlung**

---
Bezieht sich Ihr Problem auf die Leistungsfähigkeit eines Speicherdiensts?

* [Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]
* [Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]
* [Metriken zeigen hohe AverageServerLatency an]
* [Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]

---
Bezieht sich Ihr Problem auf die Verfügbarkeit eines Speicherdiensts?

* [Metriken zeigen Anstieg bei PercentThrottlingError an]
* [Metriken zeigen Anstieg bei PercentTimeoutError an]
* [Metriken zeigen Anstieg bei PercentNetworkError an]

---
 Empfängt Ihre Clientanwendung eine HTTP 4XX-Antwort (beispielsweise 404) von einem Speicherdienst?

* [Der Client empfängt HTTP 403 (Verboten)-Meldungen]
* [Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]
* [Der Client empfängt HTTP 409 (Konflikt)-Meldungen]

---
[Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]

---
[Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]

---
[Sie stoßen auf unerwartete Neustarts von virtuellen Computern, die über eine große Anzahl angeschlossener virtueller VHDs verfügen.]

---
[Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]

---
[Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]

---
[Sie haben ein anderes Problem mit einem Speicherdienst]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an
Die Abbildung unten aus dem Überwachungstool des [Azure-Portals](https://portal.azure.com) zeigt ein Beispiel, in dem die **AverageE2ELatency** wesentlich höher als die **AverageServerLatency** ist.

![Abbildung aus dem Azure-Portal, die ein Beispiel zeigt, in dem „AverageE2ELatency“ wesentlich höher als „AverageServerLatency“ ist.][4]

Der Speicherdienst berechnet die Metrik **AverageE2ELatency** nur für erfolgreiche Anforderungen und bezieht dabei im Gegensatz zu **AverageServerLatency** die Zeit ein, die der Client zum Senden der Daten und zum Empfangen der Bestätigung des Speicherdiensts benötigt. Eine Differenz zwischen **AverageE2ELatency** und **AverageServerLatency** kann daher entweder durch eine langsame Reaktion der Clientanwendung oder durch das Netzwerk bedingt sein.

> [!NOTE]
> Sie können **E2ELatency** und **ServerLatency** auch für einzelne Speichervorgänge in den Protokolldaten der Speicherprotokollierung anzeigen.
>
>

#### <a name="investigating-client-performance-issues"></a>Untersuchung von Clientleistungsproblemen
Mögliche Gründe für eine langsame Clientreaktion sind z. B. eine begrenzte Anzahl verfügbarer Verbindungen oder Threads oder begrenzte Ressourcen wie CPU, Arbeitsspeicher oder Netzwerkbandbreite. Möglicherweise können Sie das Problem lösen, indem Sie den Clientcode zugunsten einer höheren Effizienz ändern (z. B. durch Verwendung von asynchronen Aufrufen an den Speicherdienst) oder indem Sie einen größeren virtuellen Computer (mit mehr Kernen und mehr Arbeitsspeicher) verwenden.

Für den Tabellen- und Warteschlangendienst kann der Nagle-Algorithmus auch höhere **AverageE2ELatency** -Werte verglichen mit **AverageServerLatency** verursachen: Weitere Informationen finden Sie im Beitrag [Nagle's Algorithm is Not Friendly towards Small Requests](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests) (Nagle-Algorithmus geht nicht freundlich mit kleinen Anfragen um). Sie können den Nagle-Algorithmus im Code deaktivieren, indem Sie die Klasse **ServicePointManager** im **System.Net** -Namespace verwenden. Sie sollten dies tun, bevor Sie Aufrufe an die Tabellen- oder Warteschlangendienste in Ihrer Anwendung senden, da dies keinen Einfluss auf die bereits offenen Verbindungen hat. Das folgende Beispiel stammt aus der Methode **Application_Start** in einer Workerrolle.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Sie sollten die clientseitigen Protokollierungen überprüfen, um zu sehen, wie viele Anfragen Ihre Clientanwendung übermittelt, und allgemeine .NET- bezogene Leistungsengpässe in Ihrem Client wie CPU, .NET Garbage Collection, Netzwerkauslastung oder Speicher überprüfen. Als Ausgangspunkt für die Fehlerbehandlung bei .NET-Clientanwendungen siehe [Debuggen, Ablaufverfolgung und Profilerstellung](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Untersuchung von Netzwerklatenzproblemen
In der Regel wird eine hohe durchgängige Latenz durch die Übermittlungsbedingungen im Netzwerk verursacht. Sie können sowohl vorübergehende als auch dauerhafte Netzwerkprobleme wie verlorene Pakete mithilfe von Tools wie Wireshark untersuchen.

Weitere Informationen zu Wireshark zur Behandlung von Netzwerkproblemen finden Sie in [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz
In diesem Szenario ist ein verzögertes Erreichen des Speicherdiensts durch die Speicheranfragen die wahrscheinlichste Ursache. Sie sollten untersuchen, warum Clientanfragen den Blob-Dienst nicht erreichen.

Ein möglicher Grund für das verzögerte Senden von Anfragen durch den Client ist z. B. eine begrenzte Anzahl verfügbarer Verbindungen oder Threads.

Prüfen Sie auch, ob der Client mehrere Wiederholungen ausführt, und untersuchen Sie den Grund, falls dies der Fall ist. Um zu ermitteln, ob der Client mehrere Wiederholungen ausführt, können Sie folgende Aktionen ausführen:

* Überprüfen Sie die Storage Analytics-Protokolle. Wenn mehrere Wiederholungen auftreten, sehen Sie mehrere Vorgänge mit der gleichen Client-ID, aber unterschiedliche Serveranfrage-IDs.
* Überprüfen Sie die Clientprotokolle. Bei einer ausführlichen Protokollierung wird auch angezeigt, dass ein erneuter Versuch erfolgt ist.
* Debuggen Sie den Code, und überprüfen Sie die Eigenschaften des **OperationContext** -Objekts, das mit der Anfrage verknüpft ist. Wenn der Vorgang wiederholt wurde, enthält die **RequestResults** -Eigenschaft mehrere eindeutige Serveranforderungs-IDs. Sie können auch die Start- und Endzeiten der einzelnen Anforderungen überprüfen. Weitere Information finden Sie im Codebeispiel im Abschnitt [Serveranfrage-ID].

Wenn das Problem nicht beim Client liegt, sollten Sie potenzielle Netzwerkprobleme wie Paketverlust untersuchen. Sie können Tools wie Wireshark verwenden, um Netzwerkprobleme zu untersuchen.

Weitere Informationen zu Wireshark zur Behandlung von Netzwerkproblemen finden Sie in [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Metriken zeigen hohe AverageServerLatency an
Im Fall von hoher **AverageServerLatency** bei Blob-Download-Anfragen sollten Sie die Speicherprotokollierungen verwenden, um herauszufinden, ob wiederholte Anfragen für den gleichen Blob (oder die gleiche Blob-Gruppe) vorliegen. In Bezug auf Blobuploadanfragen sollten Sie untersuchen, welche Blockgröße der Client verwendet (zum Beispiel können Blöcke mit weniger als 64 K zu Overheads führen, wenn Lesevorgänge auch in Blöcken von weniger als 64 K erfolgen) und ob mehrere Clients parallel Blöcke in den gleichen Blob hochladen. Sie sollten auch die Pro-Minute-Metriken für Anfrageanzahlspitzen überprüfen, die zu einer Überschreitung der Pro-Sekunde-Skalierbarkeitsziele führen "[Metriken zeigen Anstieg bei PercentTimeoutError an]".

Im Falle von hoher **AverageServerLatency** bei wiederholten Blob-Downloadanforderungen für das gleiche Blob oder die gleiche Blobgruppe empfiehlt es sich unter Umständen, diese Blobs mithilfe von Azure Cache oder Azure Content Delivery Network (CDN) zwischenzuspeichern. In Bezug auf Upload-Anfragen können Sie den Durchlauf durch Verwendung einer größeren Blockgröße verbessern. Für Abfragen in Tabellen kann auch clientseitiges Caching auf Clients implementiert werden, die die gleichen Abfrageoperationen durchführen und deren Daten sich nicht häufig ändern.

Hohe Werte bei der **AverageServerLatency** können auch ein Zeichen für schlecht entworfene Tabellen oder Abfragen sein, die zu Scanoperationen führen oder die dem angehängten/vorangestellten Gegenmuster folgen. Weitere Informationen finden Sie unter [Metriken zeigen Anstieg bei PercentThrottlingError an].

> [!NOTE]
> Hier finden Sie eine umfassende Leistungscheckliste: [Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage](../blobs/storage-performance-checklist.md)
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange
Wenn Sie eine Verszögerung zwischen dem Zeitpunkt feststellen, in dem eine Anwendung eine Nachricht in eine Warteschlange einfügt, und dem Zeitpunkt, an dem sie zur Verfügung steht, um aus der Warteschlage gelesen zu werden, sollten Sie die folgenden Schritte ausführen, um das Problem zu diagnostizieren:

* Stellen Sie sicher, dass die Anwendung die Nachrichten erfolgreich in die Warteschlange einfügt. Überprüfen Sie, ob die Anwendung die Methode **AddMessage** nicht mehrmals wiederholt, bevor sie zum Erfolg führt. In den Protokollen der Speicherclientbibliothek werden alle wiederholten Versuche der Speicheroperationen angezeigt.
* Stellen Sie sicher, dass kein Zeitunterschied zwischen der Workerrolle, die die Nachricht in die Warteschlange einfügt, und der Workerrolle, die die Nachricht aus der Warteschlange liest, besteht, der zum Eindruck einer Bearbeitungsverzögerung führt.
* Prüfen Sie, ob der Fehler bei der Workerrolle liegt, die die Nachrichten aus der Warteschlange liest. Wenn ein Warteschlangenclient die Methode **GetMessage** aufruft, aber nicht mit einer Bestätigung reagiert, bleibt die Nachricht in der Warteschlange unsichtbar, bis der Zeitraum **invisibilityTimeout** abläuft. An diesem Punkt steht die Nachricht wieder zur Verarbeitung zur Verfügung.
* Prüfen Sie, ob die Länge der Warteschlange im Laufe der Zeit wächst. Dies kann auftreten, wenn Sie nicht über genügend Worker verfügen, um alle Nachrichten zu verarbeiten, die andere Worker in der Warteschlange platzieren. Überprüfen Sie auch die Metriken, um festzustellen, ob Löschanfragen scheitern, sowie die Anzahl der aus der Warteschlange entfernten Nachrichten, was wiederholt gescheiterte Nachrichtenlöschversuche bedeuten könnte.
* Untersuchen Sie die Protokolle der Speicherprotokollierung auf Warteschlangenvorgänge, die für einen ungewöhnlich langen Zeitraum über höhere Werte für **E2ELatency** und **ServerLatency** verfügen.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metriken zeigen Anstieg bei PercentThrottlingError an
Drosselungsfehler treten auf, wenn Sie die Skalierbarkeitsziele eines Speicherdiensts überschreiten. Der Speicherdienst drosselt, um sicherzustellen, dass kein einzelner Client oder Mandant diesen Dienst auf Kosten anderer verwenden kann. Weitere Informationen zu Skalierbarkeitszielen für Speicherkonten und Leistungszielen für Partitionen in Speicherkonten finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](scalability-targets-standard-account.md).

Wenn die Metrik **PercentThrottlingError** einen Anstieg des Prozentsatzes der Anfragen anzeigt, die mit einem Drosselungsfehler scheitern, müssen Sie eines der folgenden beiden Szenarien untersuchen:

* [Vorübergehender Anstieg bei PercentThrottlingError]
* [Permanenter Anstieg bei PercentThrottlingError]

Ein Anstieg bei **PercentThrottlingError** erfolgt oft gleichzeitig mit einem Anstieg der Anzahl an Speicheranfragen oder tritt auf, wenn Sie Ihre Anwendung zu Beginn Lasttests unterziehen. Dies kann sich auch im Client als HTTP-Status-Meldungen "503 Server Busy" oder "500 Operation Timeout" bei Speicheroperationen äußern.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Vorübergehender Anstieg bei PercentThrottlingError
Im Fall der Übereinstimmung der Wertespitzen von **PercentThrottlingError** mit Zeiträumen hoher Aktivität für die Anwendung implementieren Sie eine exponentielle (nicht lineare) Backoff-Strategie für Wiederholungen in Ihrem Client. Backoff-Wiederholungen reduzieren die sofortige Auslastung auf der Partition und unterstützen Ihre Anwendung beim Ausgleich von Lastspitzen. Weitere Informationen zur Implementierung von Wiederholungsrichtlinien unter Verwendung der Speicher-Clientbibliothek finden Sie unter [Microsoft.Azure.Storage.RetryPolicies namespace](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Es können auch Spitzen in den Werten von **PercentThrottlingError** auftreten, die nicht mit Zeiträumen hoher Aktivität für die Anwendung übereinstimmen. Die wahrscheinlichste Ursache ist hier die Verschiebung von Partitionen durch den Speicherdienst zur Verbesserung des Lastausgleichs.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Permanenter Anstieg bei PercentThrottlingError
Wenn Sie einen gleichbleibend hohen Wert für **PercentThrottlingError** nach einer dauerhaften Steigerung Ihres Transaktionsvolumens feststellen oder wenn Sie Ihre Anwendung zu Beginn Lasttests unterziehen, müssen Sie bewerten, wie Ihre Anwendung Speicherpartitionen verwendet und ob sie sich an die Skalierbarkeitsziele für ein Speicherkonto annähert. Wenn Sie beispielsweise Drosselungsfehler in einer (als Einzelpartition zählende) Warteschlange sehen, sollten Sie die Verwendung zusätzlicher Warteschlangen zur Transaktionsverteilung über mehrere Partitionen in Erwägung ziehen. Wenn Sie Drosselungsfehler in einer Tabelle feststellen, müssen Sie die Verwendung eines anderen Partitionsschemas für die Verteilung Ihrer Transaktionen über mehrere Partitionen mithilfe einer breiteren Auswahl von Partitionsschlüsseln in Erwägung ziehen. Eine häufige Ursache für dieses Problem sind die vorangestellten/angehängten Gegenmuster, in denen Sie das Datum als Partitionsschlüssel auswählen und anschließend alle Daten an einem bestimmten Tag auf eine Partition geschrieben werden: Unter Last kann dies zu einem Schreibengpass führen. Verwenden Sie entweder ein anderes Partitionierungsmuster, oder prüfen Sie, ob Blobspeicher die bessere Lösung wäre. Prüfen Sie außerdem, ob die Drosselung als Folge der Lastspitzen auftreten, und untersuchen Sie Möglichkeiten des Ausgleich Ihrer Anfragemuster.

Wenn Sie Ihre Transaktionen über mehrere Partitionen verteilen, müssen Sie die für das Speicherkonto eingestellten Skalierbarkeitsgrenzen kennen. Wenn Sie beispielsweise zehn Warteschlangen verwenden, die jeweils maximal 2.000 1KB-Nachrichten pro Sekunde verarbeiten, erreichen Sie eine Gesamtgrenze von 20.000 Nachrichten pro Sekunde für das Speicherkonto. Wenn Sie mehr als 20.000 Einheiten pro Sekunde verarbeiten müssen, sollten Sie die Verwendung mehrerer Speicherkonten in Betracht ziehen. Sie sollten auch bedenken, dass die Größe Ihrer Anfragen und Einheiten einen Einfluss darauf hat, wann der Speicherdienst Ihre Clients drosselt: Größere Anfragen und Einheiten könnten früher gedrosselt werden.

Ineffiziente Abfragemuster können ebenfalls dazu führen, dass Sie an die Skalierbarkeitsgrenzen für Tabellenpartitionen stoßen. Zum Beispiel benötigt eine Abfrage mit einem Filter, der nur ein Prozent der Einheiten in einer Partition auswählt, aber alle Objekte in einer Partition scannt, Zugriff auf jede Entität. Jede Entitätsanzeige wird gegen die Gesamtzahl der Transaktionen in dieser Partition gezählt; daher können Sie die Skalierbarkeitsziele einfach erreichen.

> [!NOTE]
> Ihr Leistungstest sollte ineffiziente Abfragemuster in Ihrer Anwendung aufdecken.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metriken zeigen Anstieg bei PercentTimeoutError an
Ihre Metriken zeigen einen Anstieg bei **PercentTimeoutError** für einen Ihrer Speicherdienste an. Zugleich erhält der Client ein hohes Volumen an HTTP-Status-Meldungen "500 Operation Timeout" von Speicheroperationen.

> [!NOTE]
> Timeout-Fehler werden vorübergehend angezeigt, während der Speicherdienst Lasten durch Verschieben einer Partition zu einem neuen Server ausgleicht.
>
>

Die Metrik **PercentTimeoutError** ist eine Aggregation der folgenden Metriken: **ClientTimeoutError** , **AnonymousClientTimeoutError** , **SASClientTimeoutError** , **ServerTimeoutError** , **AnonymousServerTimeoutError** und **SASServerTimeoutError** .

Die Server-Timeouts werden durch einen Fehler auf dem Server verursacht. Clienttimeouts treten auf, wenn ein Vorgang auf dem Server das vom Client angegebene Timeout überschritten hat. Beispiel: Ein Client, der die Speicherclientbibliothek verwendet, kann mithilfe der Eigenschaft **ServerTimeout** der Klasse **QueueRequestOptions** ein Timeout für einen Vorgang festlegen.

Server-Timeouts zeigen ein Problem mit dem Speicherdienst an, das weitere Untersuchung erfordert. Sie können Metriken verwenden, um zu überprüfen, ob Sie die Skalierbarkeitsgrenzen für den Dienst erreichen, und alle Verkehrsspitzen zu identifizieren, die dieses Problem verursachen könnten. Wenn das Problem periodisch auftritt, kann es durch Lastenausgleich im Dienst verursacht sein. Wenn das Problem anhält und nicht dadurch verursacht wird, dass Ihre Anwendung die Dienst-Skalierbarkeitsgrenzen erreicht, sollten Sie eine Support-Problemstellung eröffnen. In Bezug auf Client-Timeouts müssen Sie entscheiden, ob das Zeitlimit auf einen angemessenen Wert im Client festgelegt ist und entweder den im Client eingestellten Timeout-Wert ändern oder untersuchen, wie Sie die Leistung des Speicherdiensts verbessern können – beispielsweise durch die Optimierung Ihrer Tabellenabfragen oder die Reduzierung der Größe Ihrer Nachrichten.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metriken zeigen Anstieg bei PercentNetworkError an
Ihre Metriken zeigen einen Anstieg bei **PercentNetworkError** für einen Ihrer Speicherdienste an. Die Metrik **PercentNetworkError** ist eine Aggregation der folgenden Metriken: **NetworkError** , **AnonymousNetworkError** und **SASNetworkError** . Diese treten auf, wenn der Speicherdienst bei einer Speicheranfrage des Client einen Netzwerkfehler entdeckt.

Die häufigste Ursache für diesen Fehler ist eine Trennung der Client-Verbindung vor Ablauf eines Timeouts im Speicherdienst. Untersuchen Sie den Code in Ihrem Client, um herauszufinden, warum und wann der Client die Verbindung zum Speicherdienst abbricht. Sie können auch Wireshark oder TCPing verwenden, um Netzwerkverbindungsprobleme über den Client zu untersuchen. Die Beschreibung dieser Tools finden Sie in den [Anhänge].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Der Client empfängt HTTP 403 (Verboten)-Meldungen
Wenn Ihre Clientanwendung einen HTTP 403 (Verboten)-Fehler ausgibt, ist eine wahrscheinliche Ursache, dass der Client eine abgelaufene Shared Access Signature (SAS) verwendet, wenn er eine Speicheranfrage versendet. (Weitere mögliche Ursachen sind Zeitverzögerung, ungültige Schlüssel und leere Header). Wenn ein abgelaufener Schlüssel die Ursache ist, werden Sie keine Einträge in den serverseitigen Speicherprotokollierungsdaten finden. Die folgende Tabelle zeigt ein Beispiel für eine von der Speicher-Clientbibliothek generierte Clientprotokollierung, die folgende Problemstellung veranschaulicht:

| `Source` | Ausführlichkeit | Ausführlichkeit | Clientanfrage-ID | Operation Text |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Vorgang mit Primärspeicherort pro Speicherortmodus PrimaryOnly starten. |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Synchrone Anforderung starten an <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Warten auf Antwort. |
| Microsoft.Azure.Storage |Warnung |2 |85d077ab-… |Beim Warten auf Antwort ausgelöste Ausnahme: The remote server returned an error: (403) Verboten. |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Antwort erhalten. Statuscode = 403, Anfrage-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Warnung |2 |85d077ab-… |Während des Vorgangs ausgelöste Ausnahme: The remote server returned an error: (403) Verboten: |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Prüfung, ob Vorgang wiederholt werden sollte. Wiederholungsanzahl = 0, HTTP-Statuscode = 403, Ausnahme = Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten: |
| Microsoft.Azure.Storage |Information |3 |85d077ab-… |Der nächste Speicherort wurde auf Primär gesetzt, basierend auf dem Speicherortmodus. |
| Microsoft.Azure.Storage |Fehler |1 |85d077ab-… |Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten. |

In diesem Szenario sollten Sie untersuchen, warum der SAS-Token abläuft, bevor der Client den Token an den Server sendet:

* In der Regel sollten Sie keine Startzeit festlegen, wenn Sie eine SAS für einen Client zur sofortigen Verwendung erstellen. Wenn es kleine Zeitunterschiede zwischen dem die SAS generierenden Host und dem Speicherdienst gibt, kann der Speicherdienst eine noch nicht gültige SAS empfangen.
* Stellen Sie keine sehr kurze Ablaufzeit für eine SAS ein. Auch hier können kleine Zeitunterschiede zwischen dem die SAS generierenden Host und dem Speicherdienst dazu führen, dass eine SAS scheinbar früher als erwartet abläuft.
* Stimmt der Versionsparameter im SAS-Schlüssel (zum Beispiel **sv=2015-04-05** ) mit der von Ihnen verwendeten Version der Speicher-Clientbibliothek überein? Sie sollten immer die neueste Version der [Speicherclientbibliothek](https://www.nuget.org/packages/WindowsAzure.Storage/) verwenden.
* Wenn Sie Ihren Speicherzugriffsschlüssel neu erstellen, können dadurch vorhandene SAS-Token ungültig werden. Dieses Problem kann auftreten, wenn Sie SAS-Token mit einer langen Ablaufzeit zum Cachen von Clientanwendungen generieren.

Wenn Sie die Speicher-Clientbibliothek verwenden, um SAS-Token zu erstellen, ist es einfach, einen gültigen Token anzulegen. Wenn Sie allerdings die Speicher-REST-API verwenden und die SAS-Token manuell anlegen, sollten Sie sorgfältig das Thema [Delegating Access with a Shared Access Signature](/rest/api/storageservices/delegate-access-with-shared-access-signature) (Zugriffsdelegierung mit einer Shared Access Signature) lesen.

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen
Wenn die Clientanwendung eine HTTP 404 (Nicht gefunden)-Meldung vom Server empfängt, bedeutet dies, dass das Objekt, das der Client verwenden will (z. B. eine Entität, Tabelle, Blob, Container oder Warteschlange) nicht im Speicherdienst vorhanden ist. Hierfür gibt es eine Reihe möglicher Gründe, beispielsweise:

* [Der Client oder ein anderer Prozess haben das Objekt vorher gelöscht]
* [Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung]
* [Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt]
* [Netzwerkausfall]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Der Client oder ein anderer Prozess haben das Objekt vorher gelöscht
In Szenarien, in denen der Client versucht, Daten in einem Speicherdienst zu lesen, zu aktualisieren oder zu löschen, kann eine vorherige Operation, die das fragliche Objekt aus dem Storage-Service gelöscht hat, normalerweise einfach anhand der Serverprotokollierung identifiziert werden. Häufig ergeben die Protokollierungsdaten, dass ein anderer Benutzer oder Prozess das Objekt gelöscht hat. In der serverseitigen Speicherprotokollierung zeigen die Spalten mit der Vorgangsart und den angeforderten Objektschlüsseln, wann ein Client ein Objekt gelöscht hat.

Im Szenario, in dem ein Client versucht, ein Objekt einzufügen, ist es möglicherweise nicht sofort offensichtlich, warum dies zu einer HTTP 404 (Nicht gefunden)-Antwort führt, da der Client ein neues Objekt erstellt. Wenn der Client allerdings ein Blob anlegt, muss er den Blob-Container finden können, wenn der Client eine Nachricht anlegt, muss er eine Warteschlange finden können, und wenn der Client eine Zeile hinzufügt, muss er die Tabelle finden können.

Sie können die clientseitige Protokollierung aus der Speicher-Clientbibliothek verwenden, um genauer zu erkennen, wann der Client spezifische Anfragen an den Speicherdienst sendet.

Die folgende von der Speicher-Clientbibliothek erzeugte Clientprotokollierung verdeutlicht das Problem, wenn der Client den Container für den Blob, den er erstellt, nicht findet. Die Protokollierung enthält Details zu den folgenden Speicheroperationen:

| Anfrage-ID | Vorgang |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** : Methode, um den Blob-Container zu löschen. Beachten Sie, dass dieser Vorgang eine **HEAD** -Anforderung zur Prüfung der Existenz des Containers beinhaltet. |
| e2d06d78… |**CreateIfNotExists** : Methode zur Erstellung des Blob-Containers. Beachten Sie, dass dieser Vorgang eine **HEAD** -Anforderung beinhaltet, welche die Existenz des Containers prüft. Die **HEAD** -Anforderung gibt eine 404-Meldung zurück, fährt aber fort. |
| de8b1c3c-... |**UploadFromStream** : Methode, um das Blob zu erstellen. Die **PUT** -Anforderung schlägt mit einer 404-Meldung fehl |

Protokollierungseinträge:

| Anfrage-ID | Operation Text |
| --- | --- |
| 07b26a5d-... |Synchronanfrage starten an `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Warten auf Antwort. |
| 07b26a5d-... |Antwort erhalten. Statuscode = 200, Request ID = eeead849-...Content-MD5 = , ETag =    &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation. |
| 07b26a5d-... |Antworttext downloaden. |
| 07b26a5d-... |Operation erfolgreich abgeschlossen. |
| 07b26a5d-... |Synchronanfrage starten an `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Warten auf Antwort. |
| 07b26a5d-... |Antwort erhalten. Statuscode = 202, Anfrage-ID = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation. |
| 07b26a5d-... |Antworttext downloaden. |
| 07b26a5d-... |Operation erfolgreich abgeschlossen. |
| e2d06d78-... |Synchronanfrage starten an `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Warten auf Antwort. |
| de8b1c3c-... |Synchronanfrage starten an `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Vorbereitung, um Anfragedaten zu schreiben. |
| e2d06d78-... |Beim Warten auf Antwort ausgelöste Ausnahme: The remote server returned an error: (404) Nicht gefunden. |
| e2d06d78-... |Antwort erhalten. Statuscode = 404, Anfrage-ID = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation. |
| e2d06d78-... |Antworttext downloaden. |
| e2d06d78-... |Operation erfolgreich abgeschlossen. |
| e2d06d78-... |Synchronanfrage starten an `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Warten auf Antwort. |
| de8b1c3c-... |Anfragedaten schreiben. |
| de8b1c3c-... |Warten auf Antwort. |
| e2d06d78-... |Beim Warten auf Antwort ausgelöste Ausnahme: The remote server returned an error: 409 (Konflikt). |
| e2d06d78-... |Antwort erhalten. Statuscode = 409, Anfrage-ID = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Fehlerantworttext downloaden. |
| de8b1c3c-... |Beim Warten auf Antwort ausgelöste Ausnahme: The remote server returned an error: (404) Nicht gefunden. |
| de8b1c3c-... |Antwort erhalten. Statuscode = 404, Anfrage-ID = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Während des Vorgangs ausgelöste Ausnahme: The remote server returned an error: (404) Nicht gefunden. |
| de8b1c3c-... |Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden. |
| e2d06d78-... |Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: 409 (Konflikt). |

In diesem Beispiel zeigt das Protokoll, dass der Client Anforderungen der Methode **CreateIfNotExists** (Anforderungs-ID e2d06d78…) mit den Anforderungen aus der Methode **UploadFromStream** (de8b1c3c-...) verschachtelt. Diese Verschachtelung tritt auf, da die Clientanwendung diese Methoden asynchron aufruft. Ändern Sie den asynchronen Code im Client, um sicherzustellen, dass er die Container erstellt, bevor er versucht, Daten an ein Blob in diesem Container hochzuladen. Idealerweise sollten Sie alle Ihre Container im Voraus erstellen.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung
Wenn die Clientanwendung versucht, einen SAS-Schlüssel ohne die notwendigen Genehmigungen für den Vorgang zu verwenden, liefert der Speicherdienst eine HTTP 404 (Nicht gefunden)-Meldung an den Client zurück. Zugleich wird auch ein Wert für **SASAuthorizationError** in den Metriken angezeigt, der nicht null ist.

Die folgende Tabelle zeigt eine Muster-Serverprotokollierungsnachricht aus der Speicherprotokollierungsdatei:

| Name | Wert |
| --- | --- |
| Anfragestartzeit | 2014-05-30T06:17:48.4473697Z |
| Vorgangsart     | GetBlobProperties            |
| Anfragestatus     | SASAuthorizationError        |
| HTTP-Statuscode   | 404                            |
| Authentifizierungsart| SAS                          |
| Dienstart       | Blob                         |
| Anfrage-URL         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Anforderungs-ID-Header  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Clientanfrage-ID  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Untersuchen Sie, warum Ihre Clientanwendung versucht, einen Vorgang auszuführen, für die ihr keine Berechtigungen zugeteilt wurden.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt
Wenn Sie einen JavaScript-Client verwenden und der Speicherdienst HTTP 404-Meldungen zurückliefert, überprüfen Sie die folgenden JavaScript-Fehler im Browser:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Sie können die F12-Entwicklertools im Internet Explorer verwenden, um die zwischen Browser und Speicherdienst ausgetauschten Meldungen zu verfolgen, wenn Sie clientseitige JavaScript-Probleme behandeln.
>
>

Diese Fehler treten auf, weil im Webbrowser die [Same Origin Policy](https://www.w3.org/Security/wiki/Same_Origin_Policy) als Sicherheitsbeschränkung implementiert ist, welche die Webseite daran hindert, eine API aus einer anderen Domäne aufzurufen.

Um eine Übergangslösung für das JavaScript-Problem zu finden, können Sie Cross-Origin Resource Sharing (CORS) für den Speicherdienst konfigurieren, auf den der Client zugreift. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing (CORS)-Support für die Azure-Speicherdienste](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Das folgende Codebeispiel zeigt, wie Sie Ihren Blob-Dienst so konfigurieren, dass JavaScript in der Contoso-Domäne ausgeführt wird, um auf ein Blob in Ihrem Blob-Speicherdienst zuzugreifen:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>Netzwerkfehler
In einigen Fällen können verlorene Netzwerkpakete dazu führen, dass der Speicherdienst HTTP 404-Nachrichten an den Client zurückliefert. Wenn Ihre Clientanwendung beispielsweise eine Entität aus dem Tabellendienst löscht, liefert der Client eine Speicherausnahme mit einer "HTTP 404 (Nicht gefunden)"-Statusmeldung vom Tabellendienst zurück. Wenn Sie die Tabelle im Tabellenspeicherdienst untersuchen, sehen Sie, dass der Dienst die Entität wie gefordert gelöscht hat.

Zu den Ausnahmedetails im Client gehört die vom Tabellenspeicherdienst zugewiesene Anforderungs-ID (7e84f12d...) für die Anforderung. Anhand dieser Information können Sie die Anforderungsdetails in den serverseitigen Speicherprotokollen ermitteln, indem Sie in der Protokolldatei die Spalte **request-id-header** durchsuchen. Sie könnten die Metriken auch verwenden, um zu identifizieren, wann solche Fehler auftreten, und anschließend die Protokollierungsdateien durchsuchen, ausgehend vom Zeitpunkt, an dem die Metriken diesen Fehler aufgezeichnet haben. Dieser Protokolleintrag zeigt, dass das Löschen mit einer HTTP 404-Status-Meldung "Client Other Error" fehlgeschlagen ist. Derselbe Protokolleintrag enthält in der Spalte **client-request-id** auch die vom Client generierte Anfrage-ID (813ea74f…).

Das serverseitige Protokoll enthält noch einen anderen Eintrag mit dem gleichen Wert für **client-request-id** (813ea74f…). Bei diesem handelt es sich um einen erfolgreichen Löschvorgang für die gleiche Entität (vom gleichen Client). Dieser erfolgreiche Löschvorgang erfolgte unmittelbar vor der fehlgeschlagenen Löschanfrage.

Die wahrscheinlichste Ursache dieses Szenarios ist, dass der Client eine Löschanforderung für die Entität an den Tabellendienst gesendet hat, die erfolgreich war, für die er jedoch keine Bestätigung vom Server empfangen hat (vielleicht aufgrund eines temporären Netzwerkproblems). Der Client hat den Vorgang dann automatisch wiederholt (unter Verwendung derselben **Clientanfrage-ID** ), und diese Wiederholung ist fehlgeschlagen, weil die Entität bereits gelöscht wurde.

Wenn dieses Problem häufig auftritt, sollten Sie untersuchen, warum der Client keine Bestätigungen aus dem Tabellendienst erhält. Wenn das Problem intermittierend ist, sollten Sie den Fehler "HTTP (404) Nicht gefunden" eingrenzen und im Client protokollieren, aber dem Client die Fortführung erlauben.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Der Client empfängt HTTP 409 (Konflikt)-Meldungen
Die folgende Tabelle enthält einen Auszug aus dem serverseitigen Protokoll für zwei Clientvorgänge: **DeleteIfExists** , unmittelbar gefolgt von **CreateIfNotExists** unter Verwendung des gleichen Blobcontainernamens. Jeder Clientvorgang führt zu zwei an den Server gesendeten Anforderungen: **GetContainerProperties** (um zu prüfen, ob der Container vorhanden ist) und anschließend entweder **DeleteContainer** oder **CreateContainer** .

| Timestamp | Vorgang | Ergebnis | Containername | Clientanfrage-ID |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Der Code in der Clientanwendung löscht einen Blobcontainer und erstellt ihn umgehend unter Verwendung des gleichen Namens neu. Für die Methode **CreateIfNotExists** (Clientanforderungs-ID bc881924-…) tritt letztlich der HTTP-Fehler 409 (Konflikt) auf. Wenn ein Client Blob-Container, -Tabellen oder -Warteschlangen löscht, gibt es eine kurze Verzögerung, bevor der Name wieder verfügbar ist.

Beim Erstellen neuer Container sollte die Clientanwendung eindeutige Containernamen verwenden, wenn das Lösch-/Wiedererstellungsmuster übereinstimmt.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"
Die **PercentSuccess** -Metrik erfasst den Prozentsatz der erfolgreichen Vorgänge, basierend auf ihrem HTTP-Statuscode. Vorgänge mit Statuscodes im Bereich 2XX gelten als erfolgreich, während Vorgänge mit Statuscodes in den Bereichen 3XX, 4XX und 5XX als nicht erfolgreich gelten und den Metrikwert **PercentSucess** senken. In den serverseitigen Speicher-Protokollierungsdateien sind diese Vorgänge mit dem Transaktionsstatus **ClientOtherErrors** erfasst.

Es ist wichtig zu beachten, dass diese Vorgänge erfolgreich abgeschlossen wurden und somit keinen Einfluss auf andere Metriken wie die Verfügbarkeit haben. Hier sind einige Beispiele für Vorgänge, die erfolgreich ausgeführt werden, aber zu erfolglosen HTTP-Statuscodes führen:

* **ResourceNotFound** (Nicht Gefunden, 404), beispielsweise von einer GET-Anfrage an ein nicht existierendes Blob
* **ResourceAlreadyExists** (Konflikt, 409), beispielsweise von einem Vorgang des Typs **CreateIfNotExist** , wenn die Ressource bereits vorhanden ist.
* **ConditionNotMet** (Nicht geändert, 304), beispielsweise von einem bedingten Vorgang, bei dem ein Client einen **ETag** -Wert und einen HTTP-Header vom Typ **If-None-Match** sendet, um ein Image nur dann anzufordern, wenn es seit dem letzten Vorgang aktualisiert wurde.

Eine Liste bekannter REST API-Fehlercodes, die von den Speicherdiensten zurückgegeben werden, finden Sie auf der Seite [Bekannte REST API-Fehlercodes](/rest/api/storageservices/Common-REST-API-Error-Codes).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an
Wenn Sie plötzliche, unerwartete Änderungen in der Kapazitätsauslastung Ihres Speicherkontos feststellen, können Sie die Gründe untersuchen, indem Sie zuerst einen Blick auf Ihre Verfügbarkeitsmetriken werfen. Zum Beispiel könnte eine Erhöhung der Anzahl fehlgeschlagener Löschanforderungen, die Sie als anwendungsspezifische Bereinigungsvorgänge verwenden, nicht wie erwartet Platz freimachen (zum Beispiel, weil die für die Bereinigung verwendeten SAS-Token abgelaufen sind) und zu einem Anstieg der Blob-Speichergröße führen.

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test
Sie verwenden den Speicheremulator in der Regel während der Entwicklungs- und Testphase, um kein Azure-Speicherkonto einrichten zu müssen. Gängige Probleme, die bei der Verwendung des Speicheremulators auftreten können, sind:

* [Funktion "X" funktioniert im Speicheremulator nicht]
* [Fehler „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators]
* [Das Ausführen des Speicheremulators erfordert Administratorrechte]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funktion "X" funktioniert im Speicheremulator nicht
Der Speicheremulator unterstützt nicht alle Funktionen des Azure-Speicherdiensts wie beispielsweise den Dateidienst. Weitere Informationen finden Sie unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md).

Für die nicht vom Speicheremulator unterstützten Funktionen verwenden Sie den Azure-Speicherdienst in der Cloud.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Fehler „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators
Sie testen Ihre die Speicherclientbibliothek verwendende Anwendung mit dem lokalen Speicheremulator, und Methodenaufrufe wie **CreateIfNotExists** scheitern mit der Fehlermeldung „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format“. Dies weist darauf hin, dass die von Ihnen verwendete Version des Speicheremulators die Version Ihrer Speicher-Clientbibliothek nicht unterstützt. Die Speicher-Clientbibliothek fügt den Header **x-ms-version** zu allen getätigten Anfragen hinzu. Wenn der Speicheremulator den Wert im **x-ms-version** -Header nicht erkennt, lehnt er die Anfrage ab.

Sie können die Speicher-Clientbibliothek-Protokollierung verwenden, um den Wert des gesendeten **x-ms-version-Headers** anzuzeigen. Sie können den Wert des **x-ms-version-Headers** auch anzeigen, wenn Sie Fiddler verwenden, um die Anfragen Ihrer Clientanwendung zu verfolgen.

Dieses Szenario tritt in der Regel ein, wenn Sie die neueste Version der Speicher-Clientbibliothek installieren und verwenden, ohne den Speicheremulator zu aktualisieren. Sie sollten entweder die neueste Version des Speicheremulators installieren oder für Entwicklung und Test Cloudspeicher anstelle des Emulators verwenden.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Das Ausführen des Speicheremulators erfordert Administratorrechte
Sie benötigen Administratorrechte, wenn Sie den Speicheremulator ausführen. Dies ist nur erforderlich, wenn Sie den Speicheremulator zum ersten Mal initialisieren. Nachdem Sie den Speicheremulator initialisiert haben, benötigen Sie keine Administratorrechte, um ihn wieder auszuführen.

Weitere Informationen finden Sie unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md). Sie können den Speicheremulator auch in Visual Studio initialisieren. Dafür sind ebenfalls Administratorrechte erforderlich.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme
Wenn Sie versuchen, SDK zu installieren, scheitert dies beim Versuch, den Speicheremulator auf Ihrem lokalen Computer zu installieren. Die Installationsprotokollierung beinhaltet eine der folgenden Nachrichten:

* CAQuietExec:  Error: Zugriff auf SQL-Instanz nicht möglich
* CAQuietExec:  Error: Erstellen der Datenbank nicht möglich

Die Ursache ist ein Problem mit der vorhandenen LocalDB- Installationen. Standardmäßig verwendet der Speicheremulator LocalDB zur Datenaufbewahrung, wenn er die Azure-Speicherdienste simuliert. Sie können Ihre LocalDB-Instanz zurücksetzen, indem Sie die folgenden Befehle in einem Eingabeaufforderungs-Fenster ausführen, bevor Sie versuchen, SDK zu installieren.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Der **Lösch** -Befehl entfernt alle alten Datenbankdateien von vorhergehenden Installationen aus dem Speicheremulator.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Sie haben ein anderes Problem mit einem Speicherdienst
Wenn die vorherigen Abschnitte nicht Ihr Problem mit dem Speicherdienst enthalten, sollten Sie den folgenden Ansatz zur Diagnose und Fehlerbehebung Ihres Problems verfolgen.

* Überprüfen Sie Ihre Metriken, um zu sehen, ob es eine Abweichung von erwarteten Baseline-Verhalten gibt. Anhand der Metriken können Sie feststellen, ob das Problem vorübergehend oder dauerhaft ist und welche Speicheroperationen das Problem beeinträchtigt.
* Die Metrikinformationen unterstützen Sie bei der Suche nach serverseitigen Protokollierungsdaten, um weitere detaillierte Informationen über alle auftretenden Fehler zu erhalten. Diese Informationen können Ihnen dabei helfen, das Problem zu finden und zu beheben.
* Wenn die Informationen in den serverseitigen Protokollen nicht ausreichen, um das Problem erfolgreich zu beheben, können Sie die clientseitigen Protokolle der Storage-Clientbibliothek zur Untersuchung des Verhaltens Ihrer Clientanwendung und Tools wie Fiddler und Wireshark zur Untersuchung Ihres Netzwerks verwenden.

Weitere Informationen zur Verwendung von Fiddler finden Sie in [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr].

Weitere Informationen zur Verwendung von Wireshark finden Sie in [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

## <a name="appendices"></a><a name="appendices"></a>Anhänge
Die Anhänge beschreiben verschiedene Tools, die bei der Diagnose und Fehlerbehandlung von Problemen mit Azure-Speicher (und anderen Diensten) nützlich sein könnten. Diese Werkzeuge sind nicht Teil von Azure-Speicher, und einige sind Produkte von Drittanbietern. Als solche sind die in diesen Anhängen diskutierten Tools nicht durch Support-Vereinbarungen abgedeckt, die Sie ggf. mit Microsoft Azure oder Azure-Speicher getroffenen haben. Als Bestandteil Ihrer Evaluierung sollten Sie daher die Lizenz- und Supportoptionen prüfen, die die Anbieter dieser Tools zur Verfügung stellen.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr.
[Fiddler](https://www.telerik.com/fiddler) ist ein nützliches Werkzeug für die Analyse des HTTP- und HTTPS-Datenverkehrs zwischen Ihrer Clientanwendung und dem von Ihnen verwendeten Azure-Speicherdienst.

> [!NOTE]
> Fiddler kann HTTPS-Datenverkehr entschlüsseln. Sie sollten die Fiddler-Dokumentation sorgfältig lesen, um zu verstehen, wie dies erfolgt und welche Auswirkungen es auf die Sicherheit hat.
>
>

Dieser Anhang enthält eine kurze Anleitung, wie Sie Fiddler konfigurieren, damit der Datenverkehr zwischen dem lokalen Rechner, auf dem Sie Fiddler installiert haben, und den Azure-Speicherdiensten erfasst wird.

Nachdem Sie Fiddler gestartet haben, beginnt die Erfassung von HTTP- und HTTPS-Datenverkehr auf dem lokalen Rechner. Hier folgen einige nützliche Befehle zur Steuerung von Fiddler:

* Erfassung des Datenverkehrs stoppen und starten. Navigieren Sie im Hauptmenü zu **File** (Datei), und klicken Sie auf **Capture Traffic** (Datenverkehr erfassen), um die Erfassung ein- bzw. auszuschalten.
* Erfasste Verkehrsdaten speichern. Navigieren Sie im Hauptmenü zu **File** (Datei), klicken Sie auf **Save** (Speichern), und klicken Sie anschließend auf **All Sessions** (Alle Sitzungen). Dadurch können Sie den Datenverkehr in einer Sitzungsarchivdatei speichern. Sie können ein Session-Archiv später für die Analyse neu laden oder senden, falls der Microsoft-Support dies benötigt.

Um die Menge des von Fiddler erfassten Verkehrs einzuschränken, können Sie Filter verwenden, die Sie auf der Registerkarte **Filter** konfigurieren. Der folgende Screenshot zeigt einen Filter, der nur an den Speicherendpunkt **contosoemaildist.table.core.windows.net** gesendeten Verkehr erfasst:

![Screenshot, der einen Filter zeigt, der nur an den Speicherendpunkt „contosoemaildist.table.core.windows.net“ gesendeten Verkehr erfasst.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr.
[Wireshark](https://www.wireshark.org/) ist ein Netzwerkprotokoll-Analysator, mit dem Sie detaillierte Paketinformationen für einen breiten Netzwerkprotokollbereich anzeigen können.

Das folgende Verfahren zeigt, wie Sie detaillierte Paketinformationen für Datenverkehr aus dem lokalen Computer erfassen, auf dem Sie Wireshark für den Tabellendienst Ihres Azure-Speicherkontos installiert haben.

1. Starten Sie Wireshark auf Ihrem lokalen Computer.
2. Wählen Sie im Abschnitt **Start** die lokale Netzwerkschnittstelle oder mit dem Internet verbundene Schnittstellen aus.
3. Klicken Sie auf **Capture Options** .
4. Fügen Sie einen Filter zum Textfeld **Capture Filter** hinzu. Beispielsweise wird Wireshark durch **host contosoemaildist.table.core.windows.net** so konfiguriert, dass nur Pakete erfasst werden, die an den Tabellenspeicherdienst-Endpunkt im **contosoemaildist** -Speicherkonto oder von diesem Tabellenspeicherdienst-Endpunkt gesendet werden. Sehen Sie sich die [vollständige Liste der Erfassungsfilter](https://wiki.wireshark.org/CaptureFilters)an.

   ![Screenshot, der zeigt, wie Sie einen Filter zum Textfeld „Erfassungsfilter“ hinzufügen.][6]
5. Klicken Sie auf **Start** . Wireshark wird nun alle an oder aus dem Tabellendienstendpunkt gesendeten Pakete erfassen, wenn Sie Ihre Clientanwendung auf dem lokalen Computer verwenden.
6. Wenn Sie fertig sind, klicken Sie im Hauptmenü auf **Capture** (Erfassen) und anschließend auf **Stop** (Beenden).
7. Um die erfassten Daten in einer Wireshark-Erfassungsdatei zu speichern, klicken Sie im Hauptmenü auf **File** (Datei) und anschließend auf **Save** (Speichern).

WireShark wird alle aufgetretenen Fehler im Fenster **Packetlist** markieren. Sie können auch eine Zusammenfassung der Fehler und Warnungen im Fenster **Expert Info** (Experteninformationen) anzeigen. Klicken Sie hierzu auf **Analyze** (Analysieren) und dann auf **Expert Info** (Experteninformationen).

![Screenshot, der das Fenster „Experteninformationen“ zeigt, in dem Sie eine Zusammenfassung der Fehler und Warnungen anzeigen können.][7]

Sie können die TCP-Daten auch so anzeigen, wie sie in der Anwendungsschicht vorliegen, indem Sie mit der rechten Maustaste auf die TCP-Daten klicken und **Follow TCP Stream** auswählen. Dies ist nützlich, wenn Sie Ihre Sicherung ohne Erfassungsfilter erfassen. Weitere Informationen finden Sie in unter [Following TCP-Streams](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Screenshot, der zeigt, wie die TCP-Daten in der gleichen Ansicht wie in der Anwendungsschicht angezeigt werden.][8]

> [!NOTE]
> Weitere Informationen zu Wireshark finden Sie im [Wireshark-Benutzerhandbuch](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten
Viele Tools ermöglichen das Herunterladen von Speichermetrikdaten aus dem Azure-Tabellenspeicher in einem abgegrenzten Format, wodurch die Daten einfach in Excel für die Anzeige und Analyse geladen werden können. Speicherprotokollierungsdaten von Azure Blob Storage verfügen bereits über ein durch Trennzeichen getrenntes Format, das Sie in Excel laden können. Allerdings müssen Sie basierend auf den Informationen in [Protokollformat der Speicheranalyse](/rest/api/storageservices/Storage-Analytics-Log-Format) und [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) entsprechende Spaltenüberschriften hinzufügen.

Um Speicherprotokollierungsdaten in Excel zu importieren, nachdem Sie diese aus dem BLOB-Speicher heruntergeladen haben, gehen Sie folgendermaßen vor:

* Klicken Sie im Menü **Daten** auf **Von Text** .
* Navigieren Sie zur Protokollierungsdatei, die Sie anzeigen möchten, und klicken Sie auf **Importieren** .
* Wählen Sie in Schritt 1 des **Textimport-Assistenten** die Option **Getrennt** aus.

Wählen Sie in Schritt 1 des **Textimport-Assistenten** die Option **Semikolon** als einziges Trennzeichen und doppelte Anführungszeichen als **Textbegrenzungszeichen** aus. Klicken Sie anschließend auf **Fertigstellen** und wählen Sie aus, wo Sie die Datei in Ihrer Arbeitsmappe ablegen möchten.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Anhang 5: Überwachung mit Application Insights für Azure DevOps.
Sie können die Application Insights-Funktion für Azure DevOps auch als Bestandteil der Leistungs- und Verfügbarkeitsüberwachung verwenden. Dieses Tool kann:

* Sicherstellen, dass Ihr Webdienst verfügbar und reaktionsschnell ist. Ob Ihre Anwendung eine Website ist, die einen Webdienst verwendet, oder eine Geräteanwendung: Sie kann Ihre URL alle paar Minuten von Standorten auf der ganzen Welt testen, und Ihnen mitteilen, ob es ein Problem gibt.
* Leistungsprobleme oder Ausnahmen in Ihrem Webdienst schnell diagnostizieren. Finden Sie heraus, ob für CPU oder andere Ressourcen Stretching durchgeführt wird, gewinnen Sie Stapelnachverfolgungen aus Ausnahmen, und durchsuchen Sie ganz einfach Protokollablaufverfolgungen. Wenn die Leistung der Anwendung unter akzeptable Grenzwerte fällt, kann Microsoft Ihnen eine E-Mail senden. Sie können sowohl .NET- als auch Java-Webdienste überwachen.

Weitere Informationen finden Sie unter [Was ist Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Analysen in Azure Storage finden Sie unter den folgenden Ressourcen:

* [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md)
* [Speicheranalyse](storage-analytics.md)
* [Metriken der Speicheranalyse](storage-analytics-metrics.md)
* [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Speicheranalyseprotokolle](storage-analytics-logging.md)
* [Protokollformat der Speicheranalyse](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Einführung]: #introduction
[Wie diese Anleitung aufgebaut ist]: #how-this-guide-is-organized

[Überwachung Ihres Speicherdiensts]: #monitoring-your-storage-service
[Überwachung der Dienstintegrität]: #monitoring-service-health
[Kapazitätsüberwachung]: #monitoring-capacity
[Verfügbarkeitsüberwachung]: #monitoring-availability
[Leistungsüberwachung]: #monitoring-performance

[Diagnose von Speicherproblemen]: #diagnosing-storage-issues
[Probleme mit der Dienstintegrität]: #service-health-issues
[Leistungsprobleme]: #performance-issues
[Fehlerdiagnose]: #diagnosing-errors
[Probleme mit dem Speicheremulator]: #storage-emulator-issues
[Speicher-Protokollierungstools]: #storage-logging-tools
[Verwendung von Netzwerk-Protokollierungstools]: #using-network-logging-tools

[Durchgängige Verfolgung]: #end-to-end-tracing
[Korrelation von Protokollierungsdaten]: #correlating-log-data
[Clientanfrage-ID]: #client-request-id
[Serveranfrage-ID]: #server-request-id
[Zeitstempel]: #timestamps

[Anleitungen zur Problembehandlung]: #troubleshooting-guidance
[Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metriken zeigen hohe AverageServerLatency an]: #metrics-show-high-AverageServerLatency
[Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metriken zeigen Anstieg bei PercentThrottlingError an]: #metrics-show-an-increase-in-PercentThrottlingError
[Vorübergehender Anstieg bei PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanenter Anstieg bei PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Metriken zeigen Anstieg bei PercentTimeoutError an]: #metrics-show-an-increase-in-PercentTimeoutError
[Metriken zeigen Anstieg bei PercentNetworkError an]: #metrics-show-an-increase-in-PercentNetworkError

[Der Client empfängt HTTP 403 (Verboten)-Meldungen]: #the-client-is-receiving-403-messages
[Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]: #the-client-is-receiving-404-messages
[Der Client oder ein anderer Prozess haben das Objekt vorher gelöscht]: #client-previously-deleted-the-object
[Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung]: #SAS-authorization-issue
[Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt]: #JavaScript-code-does-not-have-permission
[Netzwerkausfall]: #network-failure
[Der Client empfängt HTTP 409 (Konflikt)-Meldungen]: #the-client-is-receiving-409-messages

[Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]: #metrics-show-low-percent-success
[Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]: #capacity-metrics-show-an-unexpected-increase
[Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]: #your-issue-arises-from-using-the-storage-emulator
[Funktion "X" funktioniert im Speicheremulator nicht]: #feature-X-is-not-working
[Fehler „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators]: #error-HTTP-header-not-correct-format
[Das Ausführen des Speicheremulators erfordert Administratorrechte]: #storage-emulator-requires-administrative-privileges
[Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Sie haben ein anderes Problem mit einem Speicherdienst]: #you-have-a-different-issue-with-a-storage-service

[Anhänge]: #appendices
[Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr]: #appendix-1
[Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr]: #appendix-2
[Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten]: #appendix-4
[Anhang 5: Überwachung mit Application Insights für Azure DevOps]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png