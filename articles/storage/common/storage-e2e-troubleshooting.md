---
title: Problembehandlung für Azure Storage mit Diagnose und Message Analyzer | Microsoft-Dokumentation
description: Ein Lernprogramm zur End-to-End-Problembehandlung mit Azure-Speicheranalyse, AzCopy und Microsoft Message Analyzer
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2707081adafa74237e3fb7730837f581e0c8b790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154225"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>End-to-End-Problembehandlung mit Azure Storage-Metriken und -Protokollierung sowie AzCopy und Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Die Diagnose und das Beheben von Problemen ist eine wichtige Fähigkeit für die Erstellung und den Support von Clientanwendungen mit Microsoft Azure-Speicher. Aufgrund der dezentralen Art einer Azure-Anwendung kann sich die Diagnose und Behebung von Fehlern und Leistungsproblemen als komplexer erweisen als in herkömmlichen Umgebungen.

In diesem Tutorial wird veranschaulicht, wie bestimmte Fehler identifiziert werden, die sich möglicherweise auf die Leistung auswirken, und wie die End-to-End-Problembehandlung mithilfe von Tools von Microsoft und Azure Storage zur Optimierung der Clientanwendung funktioniert.

Dieses Lernprogramm bietet eine praktische Beschreibung eines Szenarios der End-to-End-Problembehandlung. Ein detailliertes konzeptionelles Handbuch für die Problembehandlung von Azure-Speicheranwendungen finden Sie unter [Überwachung, Diagnose und Problembehandlung in Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Tools zur Problembehandlung von Azure-Speicheranwendungen
Um Probleme in Clientanwendungen mit Microsoft Azure-Speicher zu beheben, können Sie eine Kombination verschiedener Tools verwenden, um zu ermitteln, wann ein Problem aufgetreten ist und was die Ursache des Problems sein kann. Zu diesen Tools zählen:

* **Azure-Speicheranalyse**. [Azure-Speicheranalyse](/rest/api/storageservices/Storage-Analytics) bietet Metriken und Protokolle für den Azure-Speicher.

  * **Speichermetriken** zeichnet Transaktions- und Kapazitätsmetriken für das Speicherkonto auf. Mithilfe von Metriken können Sie die Leistung Ihrer Anwendung anhand einer Vielzahl verschiedener Messwerte bestimmen. Weitere Informationen zu den Arten der von der Speicheranalyse erfassten Metriken finden Sie unter [Schema der Tabellen für Speicheranalysemetriken](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) .
  * **Speicherprotokollierung** erfasst jede Anforderung an die Azure Storage-Dienste in einem serverseitigen Protokoll. Das Protokoll erfasst ausführliche Daten für jede Anfrage, einschließlich des ausgeführten Vorgangs, des Status des Vorgangs und der Latenzinformationen. Weitere Informationen zu den Anfrage- und Antwortdaten, die von der Speicheranalyse in die Protokolle geschrieben werden, finden Sie unter [Protokollformat der Speicheranalyse](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Azure-Portal**. Sie können die Protokollierung und Metriken für das Speicherkonto im [Azure-Portal](https://portal.azure.com) konfigurieren. Sie können auch Diagramme und Grafiken anzeigen, die darstellen, welche Leistung die Anwendung über einen gewissen Zeitraum hinweg erbringt, und Warnungen konfigurieren, die Sie benachrichtigen, wenn die Leistung bezüglich einer bestimmten Metrik von den Erwartungen abweicht.

    Informationen zum Konfigurieren der Überwachung im Azure-Portal finden Sie unter [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md).
* **AzCopy**. Serverprotokolle für den Azure-Speicher werden als Blobs gespeichert, damit Sie AzCopy verwenden können, um die Protokoll-Blobs für die Analyse mithilfe von Microsoft Message Analyzer in ein lokales Verzeichnis zu kopieren. Weitere Informationen zu AzCopy finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md) .
* **Microsoft Message Analyzer**. Message Analyzer ist ein Tool, das Protokolldateien in einem grafischen Format anzeigt, das es Ihnen erleichtert, Protokolldaten zu filtern, zu durchsuchen und zu gruppieren und so nützliche Zusammenstellungen für die Analyse von Fehlern und Leistungsproblemen zu erhalten. Weitere Informationen zu Message Analyzer finden Sie unter [Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx) (in englischer Sprache).

## <a name="about-the-sample-scenario"></a>Informationen zum Beispielszenario
Für dieses Lernprogramm wird ein Szenario untersucht, in dem Metriken des Azure-Speichers für eine Anwendung, die den Azure-Speicher aufruft, eine niedrige prozentuale Erfolgsrate anzeigen. Die Metrik zur niedrigen prozentualen Erfolgsrate (im [Azure-Portal](https://portal.azure.com) und den Metrikentabellen als **PercentSuccess** angezeigt) erfasst Vorgänge, die zwar erfolgreich waren, aber einen HTTP-Statuscode über 299 zurückgeben. In den serverseitigen Speicher-Protokollierungsdateien sind diese Vorgänge mit dem Transaktionsstatus **ClientOtherErrors**erfasst. Ausführliche Informationen zur Metrik zur niedrigen prozentualen Erfolgsrate finden Sie unter [Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokolleinträge enthalten Vorgänge mit Transaktionsstatus "ClientOtherErrors"](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure-Speichervorgänge geben möglicherweise als Teil ihrer normalen Funktionalität HTTP-Statuscodes zurück, die höher sind als 299. Diese Fehler zeigen jedoch in einigen Fällen an, dass Sie Ihre Clientanwendung optimieren können, um eine bessere Leistung zu erzielen.

In diesem Szenario gehen wir davon aus, dass es sich bei Werten unter 100 % um eine niedrige prozentuale Erfolgsrate handelt. Sie können jedoch Ihren Anforderungen entsprechend eine andere Metrikgrenze auswählen. Es wird empfohlen, dass Sie beim Testen der Anwendung eine grundlegende Toleranz für die wichtigen Leistungsmetriken festlegen. Beispielsweise können Sie basierend auf Tests bestimmen, dass Ihre Anwendung eine gleichbleibende prozentuale Erfolgsrate von 90 oder 85 % erzielen sollte. Wenn Ihre Metrikdaten anzeigen, dass die Anwendung von dieser Zahl abweicht, können Sie die Ursache der Abweichung untersuchen.

Nachdem wir in unserem Beispielszenario eingerichtet haben, dass die Metrik für die prozentuale Erfolgsrate unter 100 % liegt, untersuchen wir die Protokolle, um die Fehler zu finden, die mit diesen Metriken zusammenhängen, und nutzen sie, um die Ursache der geringeren prozentualen Erfolgsrate zu finden. Insbesondere konzentrieren wir uns auf Fehler im Bereich von 400. Daraufhin untersuchen wir Fehler 404 (Nicht gefunden) genauer.

### <a name="some-causes-of-400-range-errors"></a>Einige Ursachen von Fehlern im Bereich von 400
Die Beispiele unten zeigen Stichproben einiger Fehler im Bereich von 400 für Anfragen an Azure Blob Storage sowie ihre möglichen Ursachen. Jeder dieser Fehler sowie Fehler im Bereich von 300 und 500 können die geringere prozentuale Erfolgsrate verursachen.

Beachten Sie, dass die folgenden Listen nicht vollständig sind. Informationen zu allgemeinen Azure-Speicherfehlern und Fehlern der einzelnen Speicherdienste finden Sie unter [Status- und Fehlercodes](https://msdn.microsoft.com/library/azure/dd179382.aspx) auf MSDN.

**Beispiele für Statuscode 404 (Nicht gefunden)**

Tritt auf, wenn ein Lesevorgang für einen Container oder einen Blob fehlschlägt, weil der Blob oder Container nicht gefunden wurde.

* Tritt auf, wenn ein Container oder Blob vor dieser Anfrage von einem anderen Client gelöscht wurde.
* Tritt auf, wenn Sie einen API-Aufruf verwenden, der den Container oder Blob erstellt, nachdem überprüft wurde, ob er vorhanden ist. Die APIs CreateIfNotExists führen zunächst einen HEAD-Aufruf durch, um das Vorhandensein des Containers oder Blobs zu überprüfen. Wenn hierbei festgestellt wird, dass er nicht vorhanden ist, wird ein Fehler 404 zurückgegeben und ein zweiter PUT-Aufruf durchgeführt, um den Container oder Blob zu erstellen.

**Beispiele für Statuscode 409 (Konflikt)**

* Tritt auf, wenn Sie eine Create-API verwenden, um einen neuen Container oder Blob zu erstellen, ohne zunächst sein Vorhandensein zu überprüfen, und bereits ein Container oder Blob mit diesem Namen vorhanden ist.
* Tritt auf, wenn ein Container gelöscht wird und Sie versuchen, einen neuen Container mit demselben Namen zu erstellen, bevor der Löschvorgang abgeschlossen ist.
* Tritt auf, wenn Sie eine Lease für einen Container oder Blob angeben, obwohl bereits eine Lease vorhanden ist.

**Beispiele für Statuscode 412 (Vorbedingung nicht erfüllt)**

* Tritt auf, wenn die von einem Bedingungsheader angegebene Bedingung nicht erfüllt wurde.
* Tritt auf, wenn die angegebene Lease-ID nicht mit der Lease-ID des Containers oder Blobs übereinstimmt.

## <a name="generate-log-files-for-analysis"></a>Generieren von Protokolldateien für die Analyse
In diesem Lernprogramm verwenden wir Message Analyzer, um mit drei verschiedenen Typen von Protokolldateien zu arbeiten. Sie können jedoch mit jeder der folgenden Dateien arbeiten:

* Das **Serverprotokoll**, das bei Aktivierung der Azure-Speicherprotokollierung erstellt wird. Das Serverprotokoll enthält Daten zu jedem Vorgang, der für einen der Azure-Speicherdienste aufgerufen wird – Blob, Warteschlange, Tabelle und Datei. Das Serverprotokoll gibt an, welcher Vorgang aufgerufen und welcher Statuscode zurückgegeben wurde, und enthält weitere Details zur Anfrage und Antwort.
* Das **.NET-Clientprotokoll**, das bei Aktivierung der clientseitigen Protokollierung innerhalb Ihrer .NET-Anwendung erstellt wird. Das Clientprotokoll enthält detaillierte Informationen darüber, wie der Client die Anfrage vorbereitet und die Antwort empfängt und verarbeitet.
* Das **HTTP-Netzwerk-Ablaufverfolgungsprotokoll**, das Daten zur HTTP/HTTPS-Anfrage und zur Antwort sammelt, einschließlich der Vorgänge im Azure-Speicher. In diesem Lernprogramm generieren wir die Netzwerkablaufverfolgung über Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurieren der serverseitigen Protokollierung und Metriken
Zunächst müssen wir die Azure-Speicherprotokollierung und -metriken konfigurieren, damit uns Daten von Dienstseite zur Analyse vorliegen. Sie können die Protokollierung und die Metriken auf verschiedene Arten konfigurieren: über das [Azure-Portal](https://portal.azure.com), mithilfe von PowerShell oder programmgesteuert. Ausführliche Informationen zum Konfigurieren von Protokollierung und Metriken finden Sie unter [Aktivieren von Metriken](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) und [Aktivieren der Protokollierung](storage-analytics-logging.md#enable-storage-logging).

### <a name="configure-net-client-side-logging"></a>Konfigurieren der clientseitigen .NET-Protokollierung
Aktivieren Sie zum Konfigurieren der clientseitigen Protokollierung für eine .NET-Anwendung die .NET-Diagnose in der Konfigurationsdatei der Anwendung (web.config oder app.config). Weitere Informationen finden Sie unter [Clientseitige Protokollierung mit der .NET Storage Client Library](https://msdn.microsoft.com/library/azure/dn782839.aspx) und [Clientseitige Protokollierung mit dem Microsoft Azure Storage SDK für Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) auf der MSDN-Website.

Das clientseitige Protokoll enthält detaillierte Informationen darüber, wie der Client die Anfrage vorbereitet und die Antwort empfängt und verarbeitet.

Die Speicherclientbibliothek speichert clientseitige Protokolldaten an dem in der Konfigurationsdatei der Anwendung (web.config oder app.config) festgelegten Speicherort.

### <a name="collect-a-network-trace"></a>Erfassen einer Netzwerkablaufverfolgung
Sie können Message Analyzer verwenden, um eine HTTP-/HTTPS-Ablaufverfolgung zu erfassen, während Ihre Clientanwendung ausgeführt wird. Message Analyzer verwendet [Fiddler](https://www.telerik.com/fiddler) am Back-End. Bevor Sie die Netzwerkablaufverfolgung erfassen, empfehlen wir, Fiddler zum Aufzeichnen von unverschlüsseltem HTTPS-Datenverkehr zu konfigurieren:

1. Installieren Sie [Fiddler](https://www.telerik.com/download/fiddler).
2. Starten Sie Fiddler.
3. Wählen Sie **Tools | Fiddler Options** (Extras | Fiddler-Optionen).
4. Stellen Sie sicher, dass im Dialogfeld „Options“ (Optionen) die Optionen **Capture HTTPS CONNECTs** (HTTPS CONNECTs erfassen) und **Decrypt HTTPS Traffic** (HTTPS-Datenverkehr entschlüsseln) wie unten dargestellt aktiviert sind.

![Konfigurieren von Fiddler-Optionen](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Erfassen und speichern Sie für das Lernprogramm zunächst eine Netzwerkablaufverfolgung in Message Analyzer, und erstellen Sie daraufhin eine Analysesitzung, um die Ablaufverfolgung und die Protokolle zu analysieren. So erfassen Sie eine Netzwerkablaufverfolgung in Message Analyzer:

1. Wählen Sie in Message Analyzer **File | Quick Trace | Unencrypted HTTPS** (Datei | Schnelle Ablaufverfolgung | Unverschlüsselter HTTPS-Datenverkehr) aus.
2. Die Ablaufverfolgung beginnt sofort. Wählen Sie **Stop** aus, um die Ablaufverfolgung anzuhalten, sodass sie für die ausschließliche Verfolgung des Speicherverkehrs konfiguriert werden kann.
3. Wählen Sie **Edit** aus, um die Ablaufverfolgungssitzung zu bearbeiten.
4. Wählen Sie rechts neben dem ETW-Anbieter **Microsoft-Pef-WebProxy** die Option **Configure** (Konfigurieren).
5. Klicken Sie im Dialogfeld **Advanced Settings** (Erweiterte Einstellungen) auf die Registerkarte **Provider** (Anbieter).
6. Geben Sie im Feld **Hostname Filter** die Speicherendpunkte an (getrennt durch Leerzeichen). Sie können beispielsweise Ihre Endpunkte wie folgt angeben (ändern Sie hierbei `storagesample` in den Namen Ihres Speicherkontos):

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Schließen Sie das Dialogfeld, und klicken Sie auf **Restart** , um mit dem Erfassen der Ablaufverfolgung mit dem Hostnamensfilter zu beginnen, sodass nur der Azure-Speichernetzwerkverkehr in die Ablaufverfolgung einbezogen wird.

> [!NOTE]
> Nach dem Erfassen der Netzwerkablaufverfolgung wird dringend empfohlen, dass Sie die Einstellungen wiederherstellen, die Sie in Fiddler bezüglich der Entschlüsselung von HTTPS-Datenverkehr geändert haben. Deaktivieren Sie im Dialogfeld mit den Fiddler-Optionen die Kontrollkästchen **Capture HTTPS CONNECTs** (HTTPS CONNECTs erfassen) und **Decrypt HTTPS Traffic** (HTTPS-Datenverkehr entschlüsseln).
>
>

Weitere Informationen finden Sie unter [Using the Network Tracing Features](https://technet.microsoft.com/library/jj674819.aspx) (in englischer Sprache) auf Technet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Überprüfen der Metrikdaten im Azure-Portal
Sobald die Anwendung über einen bestimmten Zeitraum hinweg ausgeführt wurde, können Sie die Metrikdiagramme überprüfen, die im [Azure-Portal](https://portal.azure.com) zur Untersuchung der Dienstleistung angezeigt werden.

Navigieren Sie zunächst im Azure-Portal zum Speicherkonto. Standardmäßig wird ein Überwachungsdiagramm mit der Metrik **Prozentsatz erfolgreich** auf dem Blatt für das Konto angezeigt. Wenn Sie das Diagramm zuvor bearbeitet haben, um andere Metriken anzuzeigen, fügen Sie die Metrik **Prozentsatz erfolgreich** hinzu.

Im Überwachungsdiagramm wird nun **Prozentsatz erfolgreich** neben sämtlichen anderen Metriken angezeigt, die Sie möglicherweise hinzugefügt haben. Im Szenario, das wir im Folgenden durch die Analyse der Protokolle in Message Analyzer untersuchen, liegt die prozentuale Erfolgsrate unter 100 %.

Weitere Informationen zum Hinzufügen und Anpassen von Metrikdiagrammen finden Sie unter [Anpassen von Metrikdiagrammen](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Es dauert einige Zeit, bis die Metrikdaten im Azure-Portal angezeigt werden, nachdem Sie die Speichermetriken aktiviert haben. Dies liegt daran, dass stündliche Metriken für die vergangene Stunde im Azure-Portal erst angezeigt werden, wenn die Stunde vergangen ist. Darüber hinaus werden Minutenmetriken derzeit nicht im Azure-Portal angezeigt. Je nach Zeitpunkt der Metrikaktivierung kann es also bis zu zwei Stunden dauern, bis die Metrikdaten angezeigt werden.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Verwenden von AzCopy zum Kopieren von Serverprotokollen in ein lokales Verzeichnis
Azure Storage schreibt Serverprotokolldaten in Blobs, während Metriken in Tabellen geschrieben werden. Protokoll-Blobs finden Sie im wohlbekannten Container `$logs` für Ihr Speicherkonto. Protokoll-Blobs werden hierarchisch nach Jahr, Monat, Tag und Stunde benannt, damit Sie problemlos auf den Zeitbereich zugreifen können, den Sie untersuchen möchten. Der Container für die Protokoll-Blobs im Konto `storagesample` für den 02.01.2015 von 8:00 bis 9:00 Uhr lautet beispielsweise `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Die einzelnen Blobs in diesem Container werden sequenziell benannt, beginnend mit `000000.log`.

Sie können das AzCopy-Befehlszeilentool verwenden, um diese serverseitigen Protokolldateien an einen beliebigen Speicherort auf dem lokalen Computer herunterzuladen. Sie können z. B. den folgenden Befehl zum Herunterladen von Protokolldateien für Blobvorgänge verwenden, die am 2. Januar 2015 in den Ordner `C:\Temp\Logs\Server` stattgefunden haben. Ersetzen Sie hierbei `<storageaccountname>` durch den Namen Ihres Speicherkontos und `<storageaccountkey>` durch Ihren Zugriffsschlüssel:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy steht zum Download auf der Seite [Azure-Downloads](https://azure.microsoft.com/downloads/) zur Verfügung. Details zur Verwendung von AzCopy finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

Weitere Informationen zum Herunterladen serverseitiger Protokolle finden Sie unter [Herunterladen von Protokolldaten der Speicherprotokollierung](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analysieren von Protokolldaten mithilfe von Microsoft Message Analyzer
Microsoft Message Analyzer ist ein Tool zum Erfassen, Anzeigen und Analysieren von Messagingverkehr, -ereignissen und anderen System- oder Anwendungsnachrichten in Problembehandlungs- und Diagnoseszenarien. Message Analyzer ermöglicht außerdem das Laden, Zusammenfassen und Analysieren von Daten aus dem Protokoll und den gespeicherten Ablaufverfolgungsdateien. Weitere Informationen zu Message Analyzer finden Sie unter [Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx)(in englischer Sprache).

Message Analyzer enthält Ressourcen für Azure Storage, mit denen Sie Server-, Client- und Netzwerkprotokolle analysieren können. In diesem Abschnitt beschreiben wir die Verwendung dieser Tools zum Umgang mit Problemen niedriger prozentualer Erfolgsraten in den Speicherprotokollen.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Herunterladen und Installieren von Message Analyzer und der Azure-Speicherressourcen
1. Laden Sie [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) aus dem Microsoft Download Center herunter, und führen Sie das Installationsprogramm aus.
2. Starten Sie Message Analyzer.
3. Wählen Sie im Menü **Extras** die Option **Asset-Manager**. Wählen Sie im Dialogfeld **Asset-Manager** die Option **Downloads**, und filtern Sie die Anzeige nach **Azure Storage**. Die Azure-Speicherressourcen werden angezeigt, wie in der folgenden Abbildung dargestellt.
4. Klicken Sie auf **Sync All Displayed Items** , um die Azure-Speicherressourcen zu installieren. Die verfügbaren Ressourcen umfassen:
   * **Azure Storage-Farbregeln:** Die Azure Storage-Farbregeln ermöglichen die Angabe bestimmter Filter, die mithilfe von Farbe, Text und Schriftarten Nachrichten hervorheben, die bestimmte Informationen einer Nachverfolgung enthalten.
   * **Azure Storage-Diagramme:** Azure Storage-Diagramme sind vordefinierte Diagramme, die Serverprotokolldaten grafisch darstellen. Beachten Sie, dass für die Verwendung der Azure-Speicherdiagramme zum jetzigen Zeitpunkt das Serverprotokoll nur in das Analyseraster geladen werden kann.
   * **Azure Storage-Parser:** Die Azure Storage-Parser analysieren die Client-, Server- und HTTP-Protokolle von Azure Storage, um sie im Analyseraster anzuzeigen.
   * **Azure Storage-Filter:** Azure Storage-Filter sind vordefinierte Kriterien, die Sie für die Abfrage der Daten im Analyseraster verwenden können.
   * **Azure Storage-Anzeigelayouts:** Azure Storage-Anzeigelayouts sind vordefinierte Spaltenlayouts und -gruppierungen im Analyseraster.
5. Starten Sie Message Analyzer nach der Installation der Ressourcen neu.

![Message Analyzer: Asset-Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installieren Sie für dieses Lernprogramm alle angezeigten Azure-Speicherressourcen.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importieren der Protokolldateien in Message Analyzer
Sie können alle Ihre gespeicherten Protokolldateien (serverseitig, clientseitig und Netzwerk) in einer einzigen Sitzung in Microsoft Message Analyzer für die Analyse importieren.

1. Klicken Sie im Menü **File** (Datei) von Microsoft Message Analyzer auf **New Session** (Neue Sitzung), und klicken Sie dann auf **Blank Session** (Leere Sitzung). Geben Sie im Dialogfeld **New Session** einen Namen für Ihre Analysesitzung ein. Klicken Sie im Bereich **Session Details** (Sitzungsdetails) auf die Schaltfläche **Files** (Dateien).
2. Um die von Message Analyzer generierten Netzwerkablaufverfolgungs-Daten zu laden, klicken Sie auf **Add Files** (Dateien hinzufügen), navigieren Sie zum Speicherort, an dem Sie die MATP-Datei der Web-Protokollierungssitzung gespeichert haben, wählen Sie die MATP-Datei aus, und klicken Sie auf **Open** (Öffnen).
3. Klicken Sie zum Laden der serverseitigen Protokolldaten auf **Add Files** (Dateien hinzufügen), navigieren Sie zum Speicherort, an den Sie die serverseitigen Protokolle heruntergeladen haben, wählen Sie die Protokolldateien für den zu analysierenden Zeitraum aus, und klicken Sie auf **Open** (Öffnen). Legen Sie im Dropdownmenü **Text Log Configuration** (Textprotokollkonfiguration) jeder serverseitigen Protokolldatei im Bereich **Session Details** (Sitzungsdetails) die Option **AzureStorageLog** fest, um sicherzustellen, dass Microsoft Message Analyzer die Protokolldatei ordnungsgemäß analysieren kann.
4. Klicken Sie zum Laden der clientseitigen Protokolldaten auf **Add Files** (Dateien hinzufügen), navigieren Sie zum Speicherort, an dem Sie die clientseitigen Protokolle gespeichert haben, wählen Sie die zu analysierenden Protokolldateien aus, und klicken Sie auf **Open** (Öffnen). Legen Sie im Dropdownmenü **Text Log Configuration** (Textprotokollkonfiguration) jeder clientseitigen Protokolldatei im Bereich **Session Details** (Sitzungsdetails) die Option **AzureStorageClientDotNetV4** fest, um sicherzustellen, dass Microsoft Message Analyzer die Protokolldatei ordnungsgemäß analysieren kann.
5. Klicken Sie im Dialogfeld **New Session** (Neue Sitzung) auf **Start**, um die Daten zu laden und zu analysieren. Die Protokolldaten werden im Analyseraster von Message Analyzer angezeigt.

Die folgende Abbildung zeigt eine Beispielsitzung, die mit Server-, Client- und Netzwerk-Ablaufverfolgungsprotokoll-Dateien konfiguriert ist.

![Konfigurieren der Message Analyzer-Sitzung](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Beachten Sie, dass Message Analyzer Protokolldateien in den Arbeitsspeicher lädt. Wenn Sie über eine große Menge von Protokolldaten verfügen, sollten Sie den Filter verwenden, um die Leistung von Message Analyzer zu optimieren.

Bestimmen Sie zunächst den Zeitraum, den Sie überprüfen möchten, und halten Sie diesen so kurz wie möglich. In vielen Fällen sollten Sie einen Zeitraum von Minuten oder höchstens Stunden überprüfen. Importieren Sie den kleinsten Satz an Protokollen, der Ihre Anforderungen erfüllt.

Wenn Sie dennoch über eine große Menge von Protokolldaten verfügen, empfiehlt es sich möglicherweise, einen Sitzungsfilter festzulegen, der die Protokolldaten vor dem Laden filtert. Klicken Sie im Feld **Session Filter** (Sitzungsfilter) auf die Schaltfläche **Library** (Bibliothek), um einen vordefinierten Filter auszuwählen. Wählen Sie beispielsweise **Global Time Filter I** (Globaler Zeitfilter I) in den Azure-Speicherfiltern aus, um nach einem Zeitraum zu filtern. Anschließend können Sie die Filterkriterien bearbeiten, um das Start- und Enddatum des gewünschten Zeitraums festzulegen. Sie können auch nach bestimmten Status filtern: Sie können z. B. auswählen, dass nur Protokolleinträge mit dem Statuscode 404 geladen werden.

Weitere Informationen zum Importieren von Daten in Microsoft Message Analyzer finden Sie unter [Retrieving Message Data](https://technet.microsoft.com/library/dn772437.aspx) (in englischer Sprache) auf TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Verwenden der Client-ID zum Zuordnen von Protokolldaten
Die Azure-Speicherclientbibliothek erzeugt automatisch eine eindeutige Clientanfragen-ID für jede Anfrage. Dieser Wert wird in das Clientprotokoll, das Serverprotokoll und die Netzwerkablaufverfolgung geschrieben, damit Sie sie zum Zuordnen der Daten zu den drei Protokollen in Message Analyzer verwenden können. Weitere Informationen zur Clientanfragen-ID finden Sie unter [Clientanfrage-ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) .

In den folgenden Abschnitten wird die Verwendung vorkonfigurierter Layouts und benutzerdefinierter Ansichten zum Zuordnen und Gruppieren von Daten basierend auf der Clientanfragen-ID beschrieben.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Auswählen eines Ansichtslayouts zur Anzeige im Analyseraster
Die Speicherressourcen für Message Analyzer umfassen Azure-Speicheransichtslayouts, bei denen es sich um vorkonfigurierte Ansichten handelt, die Sie zum Anzeigen Ihrer Daten in nützlichen Gruppen und Spalten für verschiedene Szenarien verwenden können. Sie können auch benutzerdefinierte Ansichtslayouts erstellen und zur Wiederverwendung speichern.

Die Abbildung unten zeigt das Menü **View Layout** (Layout anzeigen) das über die Schaltfläche **View Layout** (Layout anzeigen) auf der Symbolleiste verfügbar ist. Die Ansichtslayouts für Azure-Speicher werden in dem Menü unter dem Knoten **Azure Storage** zusammengefasst. Sie können im Suchfeld nach `Azure Storage` suchen, um nur Azure-Speicheransichtslayouts anzuzeigen. Sie können auch das Sternsymbol neben einem Ansichtslayout auswählen, um es als Favorit zu markieren und oben im Menü anzuzeigen.

![Menü "Layout anzeigen"](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Wählen Sie zunächst **Grouped by ClientRequestID and Module**aus. Dieses Ansichtslayout gruppiert Protokolldaten aus allen drei Protokollen nach Clientanfragen-ID und dann nach Quellprotokolldatei (oder **Module** in Message Analyzer). Mit dieser Ansicht können Sie eine bestimmte Clientanfragen-ID überprüfen und die Daten aller drei Protokolldateien für diese Clientanfragen-ID anzeigen.

Die Abbildung unten zeigt die Anwendung dieses Ansichtslayouts auf die Beispielprotokolldaten mit einem angezeigten Teilsatz der Spalten. Sie können erkennen, dass das Analyseraster für eine bestimmte Clientanfragen-ID Daten aus dem Clientprotokoll, dem Serverprotokoll und der Netzwerkablaufverfolgung anzeigt.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Verschiedene Protokolldateien verfügen über unterschiedliche Spalten, sodass bei der Anzeige der Daten aus verschiedenen Protokolldateien im Analyseraster einige Spalten für bestimmte Zeilen möglicherweise keine Daten enthalten. die Clientprotokollzeilen keine Daten für die Spalten **Timestamp**, **TimeElapsed**, **Source** und **Destination**, da diese Spalten nicht im Clientprotokoll, jedoch in der Netzwerkablaufverfolgung vorhanden sind. Auf ähnliche Weise zeigt die Spalte **Timestamp** Zeitdaten aus dem Serverprotokoll an, jedoch werden keine Daten für die Spalten **TimeElapsed**, **Source** und **Destination** angezeigt, da diese nicht Teil des Serverprotokolls sind.
>
>

Zusätzlich zur Verwendung der Azure-Speicheransichtslayouts können Sie auch eigene Ansichtslayouts speichern. Sie können andere gewünschte Felder zum Gruppieren von Daten auswählen und die Gruppierung als Teil des benutzerdefinierten Layouts speichern.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Anwenden von Farbregeln auf das Analyseraster
Die Speicherressourcen umfassen auch Farbregeln, die ein visuelles Mittel zum Identifizieren der verschiedenen Arten von Fehlern im Analyseraster bieten. Die vordefinierten Farbregeln gelten für HTTP-Fehler, sodass sie nur für das Serverprotokoll und die Netzwerkablaufverfolgung angezeigt werden.

Um die Farbregeln anzuwenden, wählen Sie auf der Symbolleiste die Option **Color Rules** aus. Daraufhin werden die Azure-Speicherfarbregeln im Menü angezeigt. Wählen Sie für das Lernprogramm **Client Errors (StatusCode between 400 and 499)** aus, wie in der folgenden Abbildung dargestellt.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Zusätzlich zur Verwendung der Azure-Speicherfarbregeln können Sie auch eigene Farbregeln speichern.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppieren und Filtern von Protokolldaten zum Suchen von Fehlern im Bereich von 400
Als Nächstes gruppieren und filtern wir die Protokolldaten, um alle Fehler im Bereich von 400 zu finden.

1. Suchen Sie die Spalte **StatusCode** im Analyseraster, klicken Sie mit der rechten Maustaste auf die Spaltenüberschrift, und wählen Sie **Group** (Gruppieren) aus.
2. Gruppieren Sie daraufhin die Spalte **ClientRequestId** . Die Daten im Analyseraster werden daraufhin nach Statuscode und Clientanfragen-ID sortiert.
3. Öffnen Sie das Toolfenster "View Window", wenn es nicht bereits angezeigt wird. Wählen Sie auf der Symbolleiste die Option **Tool Windows** (Werkzeug „Fenster“) und dann **View Filter** (Filter anzeigen).
4. Um die Protokolldaten zur Anzeige der Fehler im Bereich 400 zu filtern, fügen Sie die folgenden Filterkriterien im Fenster **View Filter** (Filter anzeigen) ein, und klicken Sie auf **Apply** (Übernehmen):

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Die folgende Abbildung zeigt die Ergebnisse dieser Gruppierung und Filterung. Durch Erweitern des Felds **ClientRequestID** unter der Gruppierung für den Statuscode 409 wird beispielsweise der Vorgang angezeigt, der diesen Statuscode verursacht hat.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Nach dem Anwenden dieses Filters werden die Zeilen aus dem Clientprotokoll ausgeschlossen, da das Clientprotokoll keine **StatusCode** -Spalte enthält. Zunächst überprüfen wir die Server- und Netzwerkablaufverfolgungs-Protokolle, um die 404-Fehler zu finden, und kehren dann zum Clientprotokoll zurück, um die Clientvorgänge zu untersuchen, die die Fehler verursacht haben.

> [!NOTE]
> Sie können die Spalte **StatusCode** filtern und dennoch die Daten aller drei Protokolle anzeigen, einschließlich des Clientprotokolls, indem Sie dem Filter einen Ausdruck hinzufügen, der Protokolleinträge mit dem Statuscode null einschließt. Verwenden Sie für das Erstellen dieses Filters folgende Elemente:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Dieser Filter gibt alle Zeilen aus dem Clientprotokoll sowie die Zeilen des Serverprotokolls und des HTTP-Protokolls mit einem Statuscode über 400 zurück. Wenn Sie den Filter auf das Ansichtslayout anwenden, das nach Clientanfragen-ID und -modul gruppiert ist, können Sie die Protokolleinträge durchsuchen, um diejenigen zu finden, in denen alle drei Protokolle dargestellt werden.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>Filtern von Protokolldaten, um 404-Fehler zu finden
Die Speicherressourcen enthalten vordefinierte Filter, die Sie verwenden können, um Protokolldaten zu filtern und so die gesuchten Fehler oder Trends zu finden. Als Nächstes wenden wir zwei vordefinierte Filter an: einer, der die Server- und Netzwerkablaufverfolgungs-Protokolle nach 404-Fehlern filtert, und einen anderen, der die Daten nach einem angegebenen Zeitraum filtert.

1. Öffnen Sie das Toolfenster "View Window", wenn es nicht bereits angezeigt wird. Wählen Sie auf der Symbolleiste die Option **Tool Windows** (Werkzeug „Fenster“) und dann **View Filter** (Filter anzeigen).
2. Wählen Sie im Fenster „View Filter“ die Option **Library** (Bibliothek), und suchen Sie in `Azure Storage`, um die Azure Storage-Filter zu finden. Wählen Sie den Filter für **Meldungen vom Typ „404 (Nicht gefunden)“ in allen Protokollen** aus.
3. Öffnen Sie das Menü **Library** (Bibliothek) erneut, und wählen Sie **Global Time Filter** (Globaler Zeitfilter) aus.
4. Ändern Sie die im Filter angezeigten Zeitstempel zu dem gewünschten Zeitraum. So können Sie den Zeitraum der zu analysierenden Daten eingrenzen.
5. Der Filter wird ähnlich wie im folgenden Beispiel angezeigt. Klicken Sie auf **Apply** , um den Filter auf das Analyseraster anzuwenden.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysieren der Protokolldaten
Nach der Gruppierung und Filterung der Daten können Sie die Detailinformationen der einzelnen Anfragen untersuchen, die 404-Fehler verursacht haben. In dem aktuellen Ansichtslayout werden die Daten nach Kundenanfragen-ID und dann nach Protokollquelle gruppiert. Da wir die Anfragen danach filtern, ob das Feld "StatusCode" den Wert "404" enthält, werden nur die Server- und Netzwerkablaufverfolgungs-Daten, aber nicht die Clientprotokolldaten angezeigt.

Die folgende Abbildung zeigt eine bestimmte Anfrage, bei der der Vorgang "Get Blob" einen 404-Fehler zurückgegeben hat, da der Blob nicht vorhanden war. Beachten Sie, dass einige Spalten in der Standardansicht entfernt wurden, um die relevanten Daten anzuzeigen.

![Gefilterte Server- und Netzwerk-Ablaufverfolgungsprotokolle](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Als Nächstes müssen wir diese Clientanfragen-ID den Clientprotokolldaten zuordnen, um anzuzeigen, welche Aktionen der Client ausgeführt hat, als der Fehler aufgetreten ist. Sie können eine neue Analyserasteransicht für diese Sitzung öffnen, um die Clientprotokolldaten in einer zweiten Registerkarte anzuzeigen:

1. Kopieren Sie zunächst den Wert des Felds **ClientRequestId** in die Zwischenablage. Sie können dazu eine beliebige Zeile auswählen, das Feld **ClientRequestId** suchen, mit der rechten Maustaste auf den Datenwert klicken und **Copy 'ClientRequestId'** („ClientRequestId“ kopieren) auswählen.
2. Wählen Sie auf der Symbolleiste **New Viewer** (Neue Anzeige) und dann **Analysis Grid** (Analyseraster) aus, um eine neue Registerkarte zu öffnen. Die neue Registerkarte zeigt alle Daten in den Protokolldateien ohne Gruppierung, Filterung oder Farbregeln an.
3. Wählen Sie auf der Symbolleiste die Option **View Layout** (Layout anzeigen) und dann im Bereich **Azure Storage** die Option **All .NET Client Columns** (Alle .NET-Clientspalten). Dieses Ansichtslayout zeigt Daten aus dem Clientprotokoll sowie den Server- und Netzwerkablaufverfolgungs-Protokollen an. Standardmäßig wird die Anzeige nach der Spalte **MessageNumber** sortiert.
4. Als Nächstes durchsuchen wir das Clientprotokoll nach der Clientanfragen-ID. Wählen Sie auf der Symbolleiste die Option **Find Messages** (Nachrichten suchen), und legen Sie dann einen benutzerdefinierten Filter für die Clientanfragen-ID im Feld **Find** (Suchen) fest. Verwenden Sie folgende Syntax für den Filter unter Angabe Ihrer eigenen Clientanfragen-ID:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer sucht den ersten Protokolleintrag, bei dem die Clientanfragen-ID den Suchkriterien entspricht, und wählt ihn aus. Im Clientprotokoll sind mehrere Einträge für jede Clientanfragen-ID vorhanden, Sie sollten sie also nach dem Feld **ClientRequestId** gruppieren, um die Anzeige aller entsprechenden Elemente zu erleichtern. Die folgende Abbildung zeigt alle Nachrichten im Clientprotokoll für die angegebene Clientanfragen-ID.

![Clientprotokoll mit 404-Fehler](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Durch Verwenden der in den Ansichtslayouts auf diesen beiden Registerkarten angezeigten Daten können Sie die Anfragedaten analysieren, um zu bestimmen, was den Fehler verursacht hat. Sie können auch Anfragen anzeigen, die der aktuellen Anfrage vorangegangen sind, um zu ermitteln, ob ein vorheriges Ereignis den 404-Fehler verursacht hat. Sie können beispielsweise die Clientprotokolleinträge vor dieser Clientanfragen-ID überprüfen, um zu bestimmen, ob der Blob möglicherweise gelöscht wurde oder ob der Fehler verursacht wurde, weil die Clientanwendung eine CreateIfNotExists-API für einen Container oder Blob aufgerufen hat. Im Clientprotokoll finden Sie die Blobadresse im Feld **Description** (Beschreibung). In den Server- und Netzwerkablaufverfolgungs-Protokollen wird diese Information im Feld **Summary** (Zusammenfassung) angezeigt.

Wenn Sie die Adresse des Blobs kennen, der den 404-Fehler verursacht hat, können Sie weitere Untersuchungen anstellen. Wenn Sie die Protokolleinträge für andere Nachrichten durchsuchen möchten, die mit den Vorgängen desselben Blobs zusammenhängen, können Sie überprüfen, ob der Client die Entität zuvor gelöscht hat.

## <a name="analyze-other-types-of-storage-errors"></a>Analysieren anderer Arten von Speicherfehlern
Nachdem Sie nun mit der Verwendung von Message Analyzer zum Analysieren Ihrer Daten vertraut sind, können Sie andere Arten von Fehlern mithilfe von Ansichtslayouts, Farbregeln und Such-/Filterfunktionen analysieren. Einige mögliche Probleme und die erforderlichen Filterkriterien zum Finden dieser Probleme sind in den folgenden Tabellen aufgeführt. Weitere Informationen zum Erstellen von Filtern und zur Filtersyntax von Message Analyzer finden Sie unter [Filtering Message Data](https://technet.microsoft.com/library/jj819365.aspx)(in englischer Sprache).

| Zum Untersuchen von... | Verwenden Sie folgenden Filterausdruck... | Ausdruck gilt für Protokoll (Client, Server, Netzwerk, Alle) |
| --- | --- | --- |
| Unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange |AzureStorageClientDotNetV4.Description   contains "Retrying failed operation." |Client |
| HTTP-Zunahme von PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Netzwerk |
| Zunahme von PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Netzwerk |
| Zunahme von PercentTimeoutError (alle) |*StatusCode   == 500 |Alle |
| Anstieg bei „PercentNetworkError“ |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Client |
| HTTP 403 (Verboten)-Meldungen |HTTP.Response.StatusCode   == 403 |Netzwerk |
| HTTP 404 (Nicht gefunden)-Meldungen |HTTP.Response.StatusCode   == 404 |Netzwerk |
| 404 (alle) |*StatusCode   == 404 |Alle |
| Problem mit der Shared Access Signature(SAS)-Authentifizierung |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Netzwerk |
| Meldungen vom Typ HTTP 409 (Konflikt) |HTTP.Response.StatusCode   == 409 |Netzwerk |
| 409 (alle) |*StatusCode   == 409 |Alle |
| Niedriger Wert für „PercentSuccess“, oder Analyseprotokolleinträge enthalten Vorgänge mit dem Transaktionsstatus „ClientOtherErrors“. |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Server |
| Nagle-Warnung |((AzureStorageLog.EndToEndLatencyMS   - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS *   1.5)) and (AzureStorageLog.RequestPacketSize <1460) and (AzureStorageLog.EndToEndLatencyMS -   AzureStorageLog.ServerLatencyMS >= 200) |Server |
| Zeitraum in Server- und Netzwerkprotokollen |#Timestamp   >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 |Server, Netzwerk |
| Zeitraum in Serverprotokollen |AzureStorageLog.Timestamp   >= 2014-10-20T16:36:38 and AzureStorageLog.Timestamp <=   2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Problembehandlung in End-to-End-Szenarien im Azure-Speicher finden Sie hier:

* [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md)
* [Speicheranalyse](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md)
* [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer Operating Guide (in englischer Sprache)](https://technet.microsoft.com/library/jj649776.aspx)
