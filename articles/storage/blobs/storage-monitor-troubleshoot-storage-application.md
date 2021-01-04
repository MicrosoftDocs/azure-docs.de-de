---
title: Überwachung und Problembehandlung für eine Cloudspeicheranwendung in Azure | Microsoft-Dokumentation
description: Sie können Diagnosetools, Metriken und Warnungen für die Problembehandlung und Überwachung von Cloudanwendungen verwenden.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544784"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Überwachung und Problembehandlung für eine Cloudspeicheranwendung

Dieses Tutorial ist der vierte und letzte Teil einer Serie. Sie erfahren, wie Sie Überwachung und Problembehandlung für eine Cloudspeicheranwendung durchführen.

Im vierten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren von Protokollierung und Metriken
> * Aktivieren von Warnungen für Autorisierungsfehler
> * Ausführen von Testdatenverkehr mit falschen SAS-Token
> * Herunterladen und Analysieren von Protokollen

Die [Azure Storage-Analyse](../common/storage-analytics.md) stellt die Protokollierungs- und Metrikdaten für ein Speicherkonto bereit. Diese Daten bieten Einblicke in die Integrität Ihres Speicherkontos. Sie können Protokollierung, Metriken und Warnungen konfigurieren, um Daten in Azure Storage Analytics zu sammeln. Hierzu müssen Sie die Protokollierung aktivieren, Metriken konfigurieren und Warnungen aktivieren.

Protokolle und Metriken für Speicherkonten werden über die Registerkarte **Diagnose** im Azure-Portal aktiviert. Die Speicherprotokollierung ermöglicht es Ihnen, Details zu erfolgreichen und fehlerhaften Anforderungen in Ihrem Speicherkonto aufzuzeichnen. Anhand dieser Protokolle können Sie Informationen zu Lese-, Schreib- und Löschvorgängen für Ihre Tabellen, Warteschlangen und Blobs in Azure anzeigen. Außerdem können Sie die Ursachen für Anforderungsfehler wie Zeitüberschreitungen, Drosselung und Autorisierungsfehler sehen.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Aktivieren von Protokollierung und Metriken

Wählen Sie im linken Menü **Ressourcengruppen**, **myResourceGroup** und dann Ihr Speicherkonto in der Ressourcenliste aus.

Legen Sie unter **Diagnostics settings (classic)** (Diagnoseeinstellungen (klassisch)) die Option **Status** auf **Ein** fest. Stellen Sie sicher, dass alle Optionen unter **Blob-Eigenschaften** aktiviert sind.

Wenn Sie fertig sind, klicken Sie auf **Speichern**.

![Der Screenshot hebt den Abschnitt hervor, der die Konfigurationseinstellungen zum Aktivieren von Protokollierung und Metriken enthält.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Aktivieren von Warnungen

Warnungen bieten die Möglichkeit, Administratoren per E-Mail zu benachrichtigen oder einen Webhook auszulösen, wenn eine Metrik einen Schwellenwert überschreitet. In diesem Beispiel aktivieren Sie eine Warnung für die Metrik `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navigieren Sie im Azure-Portal zum Speicherkonto.

Wählen Sie im Abschnitt **Überwachung** die Option **Warnungen (klassisch)** aus.

Wählen Sie **Metrikwarnung hinzufügen (klassisch)** , und geben Sie im Formular **Regel hinzufügen** die erforderlichen Informationen ein. Wählen Sie im Dropdownmenü **Metrik** die Option `SASClientOtherError` aus. Wählen Sie im Dropdownmenü **Bedingung** die Option **Größer oder gleich** aus, um die Auslösung der Warnung beim ersten Fehler zu ermöglichen.

![Diagnosebereich](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simulieren eines Fehlers

Zum Simulieren einer gültigen Warnung können Sie ein nicht vorhandenes Blob aus Ihrem Speicherkonto anfordern. Für den folgenden Befehl ist ein Speichercontainername erforderlich. Für dieses Beispiel können Sie entweder den Namen eines vorhandenen Containers verwenden oder einen neuen erstellen.

Ersetzen Sie die Platzhalter durch echte Werte. (Stellen Sie sicher, dass für `<INCORRECT_BLOB_NAME>` ein nicht vorhandener Wert festgelegt wird.) Führen Sie anschließend den Befehl aus.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

Die folgende Abbildung zeigt eine Beispielwarnung für den simulierten Fehler auf Grundlage des oben beschriebenen Beispiels.

 ![Beispiel für Warnung](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Herunterladen und Anzeigen von Protokollen

In Speicherprotokollen werden Daten in einem Satz von Blobs in einem Blobcontainer mit dem Namen **$logs** in Ihrem Speicherkonto gespeichert. Dieser Container wird nicht angezeigt, wenn Sie die Blobcontainer in Ihrem Konto auflisten. Sie können seinen Inhalt aber anzeigen, wenn Sie direkt darauf zugreifen.

In diesem Szenario verwenden Sie [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) für die Interaktion mit Ihrem Azure Storage-Konto.

### <a name="download-microsoft-message-analyzer"></a>Herunterladen von Microsoft Message Analyzer

Laden Sie [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) herunter, und installieren Sie die Anwendung.

Starten Sie die Anwendung, und wählen Sie **File** > **Open** > **From Other File Sources** (Datei > Öffnen > Aus anderen Dateiquellen) aus.

Wählen Sie im Dialogfeld **File Selector** (Dateiauswahl) die Option **+ Add Azure Connection** (+ Azure-Verbindung hinzufügen) aus. Geben Sie den **Speicherkontonamen** und den **Kontoschlüssel** ein, und klicken Sie auf **OK**.

![Microsoft Message Analyzer – Dialogfeld zum Hinzufügen einer Azure Storage-Verbindung](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Wenn die Verbindung hergestellt wurde, erweitern Sie die Container in der Speicherstrukturansicht, um die Protokollblobs anzuzeigen. Wählen Sie das aktuelle Protokoll aus, und klicken Sie auf **OK**.

![Der Screenshot zeigt den Microsoft Message Analyzer und hebt die ausgewählte Protokolldatei hervor.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Klicken Sie im Dialogfeld **New Session** (Neue Sitzung) auf **Start**, um das Protokoll anzuzeigen.

Wenn das Protokoll geöffnet wird, können Sie die Speicherereignisse anzeigen. Wie Sie in der folgenden Abbildung sehen können, wurde `SASClientOtherError` für das Speicherkonto ausgelöst. Weitere Informationen zur Speicherprotokollierung finden Sie unter [Speicheranalyse](../common/storage-analytics.md).

![Microsoft Message Analyzer – Anzeigen von Ereignissen](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) ist ein weiteres Tool für die Interaktion mit Ihren Speicherkonten, einschließlich des Containers **$logs** und der darin enthaltenen Protokolle.

## <a name="next-steps"></a>Nächste Schritte

Im vierten und letzten Teil der Serie wurden Überwachung und Problembehandlung für Speicherkonten behandelt, u.a.:

> [!div class="checklist"]
> * Aktivieren von Protokollierung und Metriken
> * Aktivieren von Warnungen für Autorisierungsfehler
> * Ausführen von Testdatenverkehr mit falschen SAS-Token
> * Herunterladen und Analysieren von Protokollen

Unter diesem Link finden Sie vordefinierte Speicherbeispiele.

> [!div class="nextstepaction"]
> [Beispielskripts für Azure Storage](storage-samples-blobs-cli.md)