---
title: Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln (Vorschau)
titleSuffix: Azure Storage
description: Sie können von Clients erzwingen, für das Autorisieren von Anforderungen Azure AD zu verwenden. Dazu verhindern Sie Anforderungen an das Speicherkonto mit einem gemeinsam verwendeten Schlüssel (Vorschau).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: ce0ea938cac4afa043b8770a4d6a98f08ec145ec
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484888"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto (Vorschau)

Jede sichere Anforderung an ein Azure Storage-Konto muss autorisiert werden. Standardmäßig können Anforderungen entweder mit Azure AD-Anmeldeinformationen (Azure Active Directory) oder mithilfe des Kontozugriffsschlüssels (bei einer Autorisierung mit einem gemeinsam verwendeten Schlüssel) autorisiert werden. Von diesen beiden Autorisierungsarten bietet Azure AD eine höhere Sicherheit und einfachere Verwendung gegenüber einem gemeinsam verwendeten Schlüssel und wird von Microsoft empfohlen. Sie können von Clients erzwingen, für das Autorisieren von Anforderungen Azure AD zu verwenden. Dazu verhindern Sie Anforderungen an das Speicherkonto mit einem gemeinsam verwendeten Schlüssel (Vorschau).

Wenn Sie die Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Speicherkonto nicht zulassen, lehnt Azure Storage alle nachfolgenden Anforderungen an dieses Konto ab, die mit den Kontozugriffsschlüsseln autorisiert sind. Nur sichere Anforderungen, die durch Azure AD autorisiert wurden, werden erfolgreich ausgeführt. Weitere Informationen zur Verwendung von Azure AD finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](storage-auth-aad.md).

> [!WARNING]
> Azure Storage unterstützt die Azure AD-Autorisierung nur bei Anforderungen an Blob Storage und Queue Storage. Wenn Sie die Autorisierung mit einem gemeinsam genutzten Schlüssel für ein Speicherkonto nicht mehr zulassen, führen Anforderungen an Azure Files oder Table Storage, die die Autorisierung mit einem gemeinsam verwendeten Schlüssel nutzen, zu einem Fehler.
>
> Während der Vorschauphase werden Anforderungen an Azure Files oder Table Storage, die mithilfe der Kontozugriffsschlüssel generierte SAS-Token (Shared Access Signature) verwenden, auch dann erfolgreich ausgeführt, wenn die Autorisierung mit gemeinsam verwendeten Schlüsseln nicht zulässig ist. Weitere Informationen finden Sie unter [Informationen zur Vorschau](#about-the-preview).
>
> Das Verhindern des Zugriffs auf ein Speicherkonto mit gemeinsam verwendeten Schlüsseln wirkt sich nicht auf SMB-Verbindungen mit Azure Files aus.
>
> Microsoft empfiehlt, entweder alle Daten aus Azure Files oder Table Storage zu einem separaten Speicherkonto zu migrieren, bevor Sie den Zugriff auf das Konto über gemeinsam verwendete Schlüssel verhindern, oder diese Einstellung nicht auf Speicherkonten anzuwenden, die Azure Files- oder Table Storage-Workloads unterstützen.

In diesem Artikel wird beschrieben, wie Sie Anforderungen erkennen, die mit einem gemeinsam verwendeten Schlüssel autorisiert wurden, und wie Sie die Autorisierung mit gemeinsam verwendeten Schlüsseln für Ihr Speicherkonto aufheben. Informationen zum Registrieren für die Vorschauversion finden Sie unter [Informationen zur Vorschau](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Ermitteln der von Clientanwendungen verwendeten Autorisierung

Wenn Sie die Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Speicherkonto aufheben, treten bei Anforderungen von Clients, die die Kontozugriffsschlüssel für die Autorisierung mit einem gemeinsam verwendeten Schlüssel verwenden, Fehler auf. Um vor dieser Änderung zu verstehen, welche Auswirkungen das Aufheben der Autorisierung mithilfe von gemeinsam verwendeten Schlüsseln auf Clientanwendungen haben kann, aktivieren Sie Protokollierung und Metriken für das Speicherkonto. Anschließend können Sie Muster der Anforderungen an Ihr Konto über einen Zeitraum analysieren und ermitteln, wie Anforderungen autorisiert werden.

Verwenden Sie Metriken, um zu ermitteln, wie viele Anforderungen, die mit einem gemeinsam verwendeten Schlüssel oder einer Shared Access Signature (SAS) autorisiert wurden, das Speicherkonto empfängt. Ermitteln Sie anhand der Protokolle, welche Clients diese Anforderungen senden.

Weitere Informationen zum Interpretieren von Anforderungen, die während der Vorschau mit einer Shared Access Signature vorgenommen werden, finden Sie unter [Informationen zur Vorschau](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Überwachen der Anzahl von Anforderungen mit einem gemeinsam verwendeten Schlüssel

Verwenden Sie den Azure-Metrik-Explorer im Azure-Portal, um die Autorisierung von Anforderungen an ein Speicherkonto nachzuverfolgen. Weitere Informationen zum Metrik-Explorer finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Gehen Sie wie folgt vor, um eine Metrik zur Nachverfolgung von Anforderungen mit einem gemeinsam verwendeten Schlüssel oder einer Shared Access Signature zu erstellen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal. Wählen Sie im Abschnitt **Überwachung** die Option **Metriken** aus.
1. Wählen Sie **Metrik hinzufügen** aus. Geben Sie im Dialogfeld **Metrik** die folgenden Werte an:
    1. Behalten Sie im Feld **Bereich** den Namen des Speicherkontos bei.
    1. Legen Sie **Metriknamespace** auf *Konto* fest. Mit dieser Metrik werden alle Anforderungen für das Speicherkonto gemeldet.
    1. Legen Sie das Feld **Metrik** auf *Transaktionen* fest.
    1. Legen Sie das Feld **Aggregation** auf *Summe* fest.

    Die neue Metrik liefert die Summe der Transaktionen für das Speicherkonto in einem bestimmten Zeitraum. Die resultierende Metrik sieht wie folgt aus:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Screenshot: Konfigurieren einer Metrik zum Summieren von Transaktionen mit einem gemeinsam verwendeten Schlüssel oder einer SAS":::

1. Wählen Sie als Nächstes die Schaltfläche **Filter hinzufügen** aus, um einen Filter für die Metrik für den Autorisierungstyp zu erstellen.
1. Geben Sie im Dialogfeld **Filter** die folgenden Werte an:
    1. Legen Sie **Eigenschaft** auf *Authentifizierung* fest.
    1. Legen Sie das Feld **Operator** auf das Gleichheitszeichen (=) fest.
    1. Wählen Sie im Feld **Werte** die Optionen *Kontoschlüssel* und *SAS* aus.
1. Wählen Sie rechts oben den gewünschten Zeitbereich für die Metrik aus. Sie können auch angeben, wie präzise die Aggregation von Anforderungen sein soll. Hierzu können Intervalle zwischen einer Minute und einem Monat angegeben werden. Legen Sie z. B. **Zeitbereich** auf 30 Tage und **Zeitgranularität** auf 1 Tag fest, um die Anforderungen anzuzeigen, die pro Tag in den letzten 30 Tagen aggregiert wurden.

Nachdem Sie die Metrik konfiguriert haben, werden nach und nach Anforderungen an Ihr Speicherkonto im Diagramm angezeigt. In der folgenden Abbildung sind Anforderungen aufgeführt, die mit einem gemeinsam verwendeten Schlüssel autorisiert oder mit einem SAS-Token durchgeführt wurden. Anforderungen werden tageweise für die letzten 30 Tage aggregiert.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Screenshot: aggregierte Anforderungen, die mit einem gemeinsam verwendeten Schlüssel autorisiert wurden":::

Sie können auch eine Warnungsregel konfigurieren, um benachrichtigt zu werden, wenn eine bestimmte Anzahl von Anforderungen an Ihr Speicherkonto gerichtet wird, die mit einem gemeinsam verwendeten Schlüssel autorisiert wurden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analysieren von Protokollen zum Identifizieren von Clients, die Anforderungen mit gemeinsam verwendeten Schlüsseln oder SAS autorisieren

In Azure Storage-Protokollen werden Details zu Anforderungen für das Speicherkonto erfasst. Hierzu zählt auch die Information, wie eine Anforderung autorisiert wurde. Sie können die Protokolle analysieren, um zu ermitteln, welche Clients Anforderungen mit gemeinsam verwendeten Schlüsseln oder SAS-Token autorisieren.

Wenn Sie Anforderungen an Ihr Azure Storage-Konto protokollieren möchten, um deren Autorisierung zu untersuchen, können Sie die Azure Storage-Protokollierung in Azure Monitor (Vorschauversion) verwenden. Weitere Informationen finden Sie unter [Überwachen von Azure Storage](../blobs/monitor-blob-storage.md).

Die Azure Storage-Protokollierung in Azure Monitor unterstützt die Verwendung von Protokollabfragen für die Analyse von Protokolldaten. Für die Abfrage von Protokollen können Sie einen Azure Log Analytics-Arbeitsbereich verwenden. Weitere Informationen zu Protokollabfragen finden Sie unter [Tutorial: Erste Schritte mit Log Analytics-Abfragen](../../azure-monitor/log-query/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Erstellen einer Diagnoseeinstellung im Azure-Portal

Wenn Sie Azure Storage-Daten mit Azure Monitor protokollieren und mit Azure Log Analytics analysieren möchten, müssen Sie zunächst eine Diagnoseeinstellung erstellen, die angibt, welche Anforderungstypen und für welche Speicherdienste Daten protokolliert werden sollen. Führen Sie zum Erstellen einer Diagnoseeinstellung im Azure-Portal die folgenden Schritte aus:

1. Registrieren Sie sich für die [Azure Storage-Protokollierung in Azure Monitor (Vorschauversion)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Erstellen Sie in dem Abonnement, das Ihr Azure Storage-Konto enthält, einen neuen Log Analytics-Arbeitsbereich, oder verwenden Sie einen vorhandenen. Nachdem Sie die Protokollierung für Ihr Speicherkonto konfiguriert haben, sind die Protokolle im Log Analytics-Arbeitsbereich verfügbar. Weitere Informationen finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Klicken Sie im Abschnitt „Überwachung“ auf **Diagnoseeinstellungen (Vorschau)** .
1. Wählen Sie den Azure Storage-Dienst aus, für den Sie Anforderungen protokollieren möchten. Wählen Sie beispielsweise **Blob** aus, um Anforderungen an den Blobspeicher zu protokollieren.
1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Geben Sie einen Namen für die Diagnoseeinstellung an.
1. Wählen Sie im Abschnitt **Protokoll** unter **Kategoriedetails** die Optionen **StorageRead**, **StorageWrite** und **StorageDelete** aus, um alle Datenanforderungen an den ausgewählten Dienst zu protokollieren.
1. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** aus. Wählen Sie Ihr Abonnement und den zuvor erstellten Log Analytics-Arbeitsbereich aus, wie in der folgenden Abbildung zu sehen:

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot: Erstellen einer Diagnoseeinstellung für die Protokollierung von Anforderungen":::

Sie können für jeden Typ von Azure Storage-Ressource in Ihrem Speicherkonto eine Diagnoseeinstellung erstellen.

Nachdem Sie die Diagnoseeinstellung erstellt haben, werden Anforderungen für das Speicherkonto gemäß dieser Einstellung protokolliert. Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../../azure-monitor/platform/diagnostic-settings.md).

Eine Referenz der Felder, die in Azure Storage-Protokollen in Azure Monitor verfügbar sind, finden Sie unter [Ressourcenprotokolle (Vorschau)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Abfrageprotokolle für Anforderungen, die mit einem gemeinsam verwendeten Schlüssel oder einer SAS ausgeführt wurden

Azure Storage-Protokolle in Azure Monitor enthalten die Art der Autorisierung, die bei einer Anforderung für ein Speicherkonto verwendet wurde. Zum Abrufen von Protokollen für Anforderungen, die in den letzten sieben Tagen durchgeführt und mit einem gemeinsam verwendeten Schlüssel oder einer SAS autorisiert wurden, öffnen Sie den Log Analytics-Arbeitsbereich. Fügen Sie dann die folgende Abfrage in eine neue Protokollabfrage ein, und führen Sie sie aus. Diese Abfrage zeigt die zehn IP-Adressen an, die am häufigsten mit einem gemeinsam verwendeten Schlüssel oder SAS autorisierte Anforderungen gesendet haben:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Sie können auch eine auf dieser Abfrage basierende Warnungsregel konfigurieren, um über Anforderungen informiert zu werden, die mit einem gemeinsam verwendeten Schlüssel oder einer SAS autorisiert wurden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Aufheben der Autorisierung mit gemeinsam verwendeten Schlüsseln

Nachdem Sie analysiert haben, wie Anforderungen an Ihr Speicherkonto autorisiert werden, können Sie Maßnahmen ergreifen, um den Zugriff mit gemeinsam verwendeten Schlüsseln zu verhindern. Zuerst müssen Sie jedoch alle Anwendungen, die die Autorisierung mit gemeinsam verwendeten Schlüsseln verwenden, so aktualisieren, dass sie stattdessen Azure AD verwenden. Sie können Protokolle und Metriken überwachen, wie in [Ermitteln der von Clientanwendungen verwendeten Autorisierung](#detect-the-type-of-authorization-used-by-client-applications) beschrieben, um den Übergang nachzuverfolgen. Weitere Informationen zur Verwendung von Azure AD mit Blob- und Warteschlangendaten finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](storage-auth-aad.md).

Wenn Sie sicher sind, dass Sie Anforderungen, die mit gemeinsam genutzten Schlüsseln autorisiert wurden, ablehnen können, können Sie die **AllowSharedKeyAccess**-Eigenschaft für das Speicherkonto auf **FALSE** festlegen.

Die **AllowSharedKeyAccess**-Eigenschaft ist standardmäßig nicht festgelegt und gibt erst dann einen Wert zurück, wenn Sie sie explizit festgelegt haben. Das Speicherkonto lässt Anforderungen zu, die mit einem gemeinsam verwendeten Schlüssel autorisiert wurden, wenn der Eigenschaftswert **NULL** oder **TRUE** ist.

> [!WARNING]
> Wenn ein Client derzeit mit einem gemeinsam verwendeten Schlüssel auf Daten in Ihrem Speicherkonto zugreift, empfiehlt Microsoft, diese Clients zu Azure AD zu migrieren, bevor Sie den Zugriff auf das Speicherkonto mit einem gemeinsam verwendeten Schlüssel verhindern.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Befolgen Sie diese Schritte, um die Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Speicherkonto im Azure-Portal zu verweigern:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Suchen Sie unter **Einstellungen** die Einstellung **Konfiguration**.
1. Legen Sie **Zugriff mit gemeinsam verwendetem Schlüssel zulassen** auf **Deaktiviert** fest.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Screenshot: Verweigern des Zugriffs auf das Konto mit einem gemeinsam verwendeten Schlüssel":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie die Azure-Befehlszeilenschnittstelle Version 2.9.1 oder höher, um die Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Speicherkonto mit der Azure-Befehlszeilenschnittstelle zu verhindern. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Konfigurieren Sie als Nächstes die **allowSharedKeyAccess**-Eigenschaft für ein neues oder vorhandenes Speicherkonto.

Im folgenden Beispiel wird gezeigt, wie Sie die **allowSharedKeyAccess**-Eigenschaft mit der Azure-Befehlszeilenschnittstelle festlegen. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Nachdem Sie die Autorisierung mit gemeinsam verwendeten Schlüsseln aufgehoben haben, führen Anforderungen an das Speicherkonto, die mit gemeinsam verwendeten Schlüsseln autorisiert wurden, zu einem Fehler mit dem Fehlercode 403 (Verboten). Azure Storage gibt einen Fehler zurück, der angibt, dass die schlüsselbasierte Autorisierung für das Speicherkonto nicht zulässig ist.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Überprüfen, ob der Zugriff mit gemeinsam verwendeten Schlüsseln verhindert wird

Wenn Sie sich vergewissern möchten, dass die Autorisierung mit gemeinsam verwendeten Schlüsseln nicht mehr zulässig ist, können Sie versuchen, einen Datenvorgang mit dem Kontozugriffsschlüssel aufzurufen. Im folgenden Beispiel wird versucht, mit dem Zugriffsschlüssel einen Container zu erstellen. Dieser Aufruf führt zu einem Fehler, wenn die Autorisierung mit gemeinsam verwendeten Schlüsseln für das Speicherkonto nicht zulässig ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Anonyme Anforderungen werden nicht autorisiert und können weiter verwendet werden, wenn Sie das Speicherkonto und den Container für den anonymen öffentlichen Lesezugriff konfiguriert haben. Weitere Informationen finden Sie unter [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Überprüfen der Einstellung für den Zugriff mit gemeinsam verwendeten Schlüsseln für mehrere Konten

Wenn Sie die Einstellung für den Zugriff mit gemeinsam verwendeten Schlüsseln für mehrere Speicherkonten mit optimaler Leistung überprüfen möchten, können Sie den Azure Resource Graph-Explorer im Azure-Portal verwenden. Weitere Informationen zur Verwendung des Resource Graph-Explorers finden Sie unter [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md).

Wenn Sie die folgende Abfrage im Resource Graph-Explorer ausführen, wird eine Liste der Speicherkonten zurückgegeben und für jedes Konto die Einstellung für den Zugriff mit gemeinsam verwendeten Schlüsseln angezeigt:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Grundlegendes zu den Auswirkungen der Aufhebung gemeinsam verwendeter Schlüssel auf SAS-Token

Wenn gemeinsam verwendete Schlüssel für das Speicherkonto nicht zulässig sind, verarbeitet Azure Storage SAS-Token basierend auf dem Typ der SAS und dem Dienst, der Ziel der Anforderung ist. In der folgenden Tabelle wird gezeigt, wie die einzelnen SAS-Typen autorisiert werden und wie Azure Storage diese SAS behandelt, wenn die **AllowSharedKeyAccess**-Eigenschaft für das Speicherkonto **FALSE** ist.

| SAS-Typ | Autorisierungstyp | Verhalten, wenn AllowSharedKeyAccess FALSE ist |
|-|-|-|
| SAS für Benutzerdelegierung (nur Blobspeicher) | Azure AD | Die Anforderung wird zugelassen. Microsoft empfiehlt, für höhere Sicherheit nach Möglichkeit eine SAS für die Benutzerdelegierung zu verwenden. |
| Dienst-SAS | Gemeinsam verwendeter Schlüssel | Die Anforderung wird für Blob Storage verweigert. Die Anforderung wird für Queue und Table Storage sowie für Azure Files zugelassen. Weitere Informationen finden Sie unter [Anforderungen mit SAS-Token sind für Warteschlangen, Tabellen und Dateien zulässig, wenn AllowSharedKeyAccess den Wert FALSE hat](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) im Abschnitt **Informationen zur Vorschau**. |
| Konto-SAS | Gemeinsam verwendeter Schlüssel | Die Anforderung wird für Blob Storage verweigert. Die Anforderung wird für Queue und Table Storage sowie für Azure Files zugelassen. Weitere Informationen finden Sie unter [Anforderungen mit SAS-Token sind für Warteschlangen, Tabellen und Dateien zulässig, wenn AllowSharedKeyAccess den Wert FALSE hat](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) im Abschnitt **Informationen zur Vorschau**. |

Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Kompatibilität mit anderen Azure-Tools und -Diensten

Einige Azure-Dienste verwenden die Autorisierung mit gemeinsam verwendeten Schlüsseln, um mit Azure Storage zu kommunizieren. Wenn Sie die Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Speicherkonto aufheben, können diese Dienste nicht auf Daten in diesem Konto zugreifen, und Ihre Anwendungen können beeinträchtigt werden.

Einige Azure-Tools bieten die Möglichkeit, die Azure AD-Autorisierung für den Zugriff auf Azure Storage zu verwenden. In der folgenden Tabelle sind einige beliebte Azure-Tools aufgelistet, und es ist angegeben, ob sie Azure AD für das Autorisieren von Anforderungen an Azure Storage verwenden.

| Azure-Tool | Azure AD-Autorisierung für Azure Storage |
|-|-|
| Azure-Portal | Unterstützt. Informationen zur Autorisierung mit Ihrem Azure AD-Konto über das Azure-Portal finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Für Blob Storage unterstützt. Weitere Informationen zur Autorisierung von AzCopy-Vorgängen finden Sie in der AzCopy-Dokumentation unter [Auswählen, wie Sie die Autorisierungsanmeldeinformationen bereitstellen](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials). |
| Azure Storage-Explorer | Nur für Blob Storage und Azure Data Lake Storage Gen2 unterstützt. Azure AD-Zugriff auf Queue Storage wird nicht unterstützt. Wählen Sie unbedingt den richtigen Azure AD-Mandanten aus. Weitere Informationen finden Sie unter [Erste Schritte mit Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure). |
| Azure PowerShell | Unterstützt. Informationen zum Autorisieren von PowerShell-Befehlen für Befehle für Blob- oder Warteschlangenvorgänge mit Azure AD finden Sie unter [Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blobdaten](../blobs/authorize-data-operations-powershell.md) und [Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Warteschlangendaten](../queues/authorize-data-operations-powershell.md). |
| Azure CLI | Unterstützt. Informationen zum Autorisieren von Azure CLI-Befehlen mit Azure AD für den Zugriff auf Blob- und Warteschlangendaten finden Sie unter [Ausführen von Azure CLI-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blob- oder Warteschlangendaten](../blobs/authorize-data-operations-cli.md). |
| Azure IoT Hub | Unterstützt. Weitere Informationen finden Sie unter [IoT Hub-Unterstützung für virtuelle Netzwerke](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell ist eine integrierte Shell im Azure-Portal. Azure Cloud Shell hostet Dateien für Persistenz in einer Azure-Dateifreigabe in einem Speicherkonto. Auf diese Dateien kann nicht mehr zugegriffen werden, wenn die Autorisierung mit gemeinsam verwendeten Schlüsseln für dieses Speicherkonto aufgehoben wird. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem Microsoft Azure Files-Speicher](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Zum Ausführen von Befehlen in Azure Cloud Shell zum Verwalten von Speicherkonten, für die der Zugriff mit gemeinsam verwendeten Schlüsseln nicht zulässig ist, müssen Sie zunächst sicherstellen, dass Ihnen die erforderlichen Berechtigungen für diese Konten über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) erteilt wurden. Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md). |

## <a name="about-the-preview"></a>Informationen zur Vorschau

Die Vorschauversion für das Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln ist in der öffentlichen Azure-Cloud verfügbar. Sie ist nur für Speicherkonten verfügbar, die das Azure Resource Manager-Bereitstellungsmodell verwenden. Informationen dazu, welche Speicherkonten das Azure Resource Manager-Bereitstellungsmodell verwenden, finden Sie unter [Typen von Speicherkonten](storage-account-overview.md#types-of-storage-accounts).

Informationen zum Registrieren für die Vorschauversion finden Sie unter [Azure Storage Allow Shared Key Access Limited Public Preview](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u) (Azure Storage: Zulassen den Zugriffs mit gemeinsam verwendeten Schlüsseln – eingeschränkte Public Preview).

> [!IMPORTANT]
> Diese Vorschau ist nur für die Verwendung außerhalb der Produktion bestimmt.

Für die Vorschau gelten einige Einschränkungen, die in den folgenden Abschnitten beschrieben werden.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Metriken und Protokolle melden alle Anforderungen, die mit einer SAS durchgeführt wurden, unabhängig von ihrer Autorisierung.

Azure-Metriken und die Protokollierung in Azure Monitor unterscheiden in der Vorschauversion nicht zwischen den verschiedenen Typen von Shared Access Signatures. Der Filter **SAS** in Azure-Metrik-Explorer und das Feld **SAS** bei der Azure Storage-Protokollierung in Azure Monitor melden alle Anforderungen, die mit einem beliebigen SAS-Typ autorisiert wurden. Die verschiedenen Typen von Shared Access Signatures werden jedoch unterschiedlich autorisiert und verhalten sich unterschiedlich, wenn der Zugriff mit gemeinsam verwendeten Schlüsseln nicht zulässig ist:

- Ein SAS-Token für einen Dienst oder ein Konto wird mit einem gemeinsam verwendeten Schlüssel autorisiert und ist daher für eine Anforderung an Blob Storage nicht zulässig, wenn die **AllowSharedKeyAccess**-Eigenschaft auf **FALSE** festgelegt ist.
- Eine SAS zur Benutzerdelegierung wird mit Azure AD autorisiert und ist daher für eine Anforderung an Blob Storage zulässig, wenn die **AllowSharedKeyAccess**-Eigenschaft auf **FALSE** festgelegt ist.

Wenn Sie den Datenverkehr zu Ihrem Speicherkonto auswerten, sollten Sie beachten, dass Metriken und Protokolle (wie in [Ermitteln der von Clientanwendungen verwendeten Autorisierung](#detect-the-type-of-authorization-used-by-client-applications) beschrieben) auch Anforderungen enthalten können, die mit einer Benutzerdelegierungs-SAS erfolgt sind. Weitere Informationen zur Reaktion von Azure Storage auf eine SAS, wenn die **AllowSharedKeyAccess**-Eigenschaft auf **FALSE** festgelegt ist, finden Sie unter [Grundlegendes zu den Auswirkungen der Aufhebung gemeinsam verwendeter Schlüssel auf SAS-Token](#understand-how-disallowing-shared-key-affects-sas-tokens).

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>Anforderungen mit SAS-Token sind für Warteschlangen, Tabellen und Dateien zulässig, wenn AllowSharedKeyAccess den Wert FALSE aufweist.

Wenn der Zugriff mit gemeinsam verwendeten Schlüsseln für das Speicherkonto während der Vorschauphase aufgehoben wird, werden Shared Access Signatures, die auf Warteschlangen-, Tabellen- oder Azure Files-Ressourcen abzielen, weiterhin zugelassen. Diese Einschränkung gilt sowohl für Dienst-SAS-Token als auch für Konto-SAS-Token. Beide SAS-Typen werden mit einem gemeinsam verwendeten Schlüssel autorisiert.

## <a name="next-steps"></a>Nächste Schritte

- [Autorisierung des Datenzugriffs in Azure Storage](storage-auth.md)
- [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](storage-auth-aad.md)
- [Autorisieren mit einem gemeinsam verwendeten Schlüssel](/rest/api/storageservices/authorize-with-shared-key)