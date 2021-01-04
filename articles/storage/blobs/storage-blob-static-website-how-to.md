---
title: Hosten einer statischen Website in Azure Storage
description: Hier erfahren Sie, wie Sie statische Inhalte (HTML-, CSS-, JavaScript- und Bilddateien) direkt über einen Container in einem Azure Storage GPv2-Konto bereitstellen.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8ca670049b49500e6b6310bca25cb78ded31a294
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537848"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hosten einer statischen Website in Azure Storage

Sie können statische Inhalte (HTML-, CSS-, JavaScript- und Bilddateien) direkt über einen Container in einem Azure Storage GPv2-Konto bereitstellen. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](storage-blob-static-website.md).

In diesem Artikel erfahren Sie, wie Sie das Hosting statischer Websites über das Azure-Portal, mit der Azure-CLI oder mit Azure PowerShell aktivieren.

> [!NOTE]
> Erstellen Sie unbedingt ein Standardspeicherkonto vom Typ „Universell v2 (GPv2)“. Statische Websites sind in keinem anderen Speicherkontotyp verfügbar.

## <a name="enable-static-website-hosting"></a>Aktivieren des Hostings statischer Websites

Das Feature zum Hosten statischer Websites muss für das Speicherkonto aktiviert werden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

3. Wählen Sie die Option **Statische Website**, um die Konfigurationsseite für statische Websites anzuzeigen.

4. Wählen Sie **Aktiviert**, um das Hosten von statischen Websites für das Speicherkonto zu aktivieren.

5. Geben Sie im Feld **Name des Indexdokuments**  die Standardindexseite an (z. B. *index.html*). 

   Die Standardindexseite wird angezeigt, wenn ein Benutzer zum Stammverzeichnis Ihrer statischen Website navigiert.  

6. Geben Sie im Feld **Pfad zum Fehlerdokument** die Standardfehlerseite an (z. B. *404.html*). 

   Die Standardfehlerseite wird angezeigt, wenn ein Benutzer versucht, zu einer Seite zu navigieren, die auf Ihrer statischen Website nicht vorhanden ist.

7. Klicken Sie auf **Speichern**. Im Azure-Portal wird jetzt der Endpunkt Ihrer statischen Website angezeigt. 

    ![Aktivieren des Hostens von statischen Websites für ein Speicherkonto](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

<a id="cli"></a>

Sie können das Hosting statischer Websites über die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/?view=azure-cli-latest) aktivieren.

1. Öffnen Sie zunächst [Azure Cloud Shell](../../cloud-shell/overview.md?view=azure-cli-latest), oder falls Sie die Azure-CLI lokal [installiert](/cli/azure/install-azure-cli?view=azure-cli-latest) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Aktivieren Sie das Hosting statischer Websites.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   * Ersetzen Sie den Platzhalter `<error-document-name>` durch den Namen des Fehlerdokuments, das den Benutzern angezeigt wird, wenn ein Browser eine nicht vorhandene Seite auf Ihrer Website anfordert.

   * Ersetzen Sie den Platzhalter `<index-document-name>` durch den Namen des Indexdokuments. Dieses Dokument ist üblicherweise „index.html“.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Sie können das Hosting statischer Websites über das Azure PowerShell-Modul aktivieren.

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster.

2. Vergewissern Sie sich, dass Sie über Version 0.7 oder höher des Azure PowerShell-Moduls Az verfügen.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

3. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

4. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

5. Rufen Sie den Kontext des Speicherkontos ab, der das zu verwendende Speicherkonto definiert.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

6. Aktivieren Sie das Hosting statischer Websites.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Ersetzen Sie den Platzhalter `<error-document-name>` durch den Namen des Fehlerdokuments, das den Benutzern angezeigt wird, wenn ein Browser eine nicht vorhandene Seite auf Ihrer Website anfordert.

   * Ersetzen Sie den Platzhalter `<index-document-name>` durch den Namen des Indexdokuments. Dieses Dokument ist üblicherweise „index.html“.

---

## <a name="upload-files"></a>Hochladen von Dateien 

### <a name="portal"></a>[Portal](#tab/azure-portal)

In diesen Anweisungen wird gezeigt, wie Sie Dateien mit der Version von Storage-Explorer hochladen, die im Azure-Portal angezeigt wird. Sie können aber auch die Version von [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, die außerhalb vom Azure-Portal ausgeführt wird. Sie können [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, die CLI oder eine beliebige benutzerdefinierte Anwendung verwenden, die Dateien in den **$web**-Container Ihres Kontos hochladen kann. Ein Schritt-für-Schritt-Tutorial, in dem Dateien mithilfe von Visual Studio Code hochgeladen werden, finden Sie unter [Tutorial: Host a static website on Blob Storage](./storage-blob-static-website-host.md) (Tutorial: Hosten einer statischen Website in Blob Storage) aus, bevor Sie mit diesem Tutorial beginnen.

1. Wählen Sie **Storage-Explorer (Vorschau)** aus.

2. Erweitern Sie den Knoten **BLOBCONTAINER**, und wählen Sie dann den **$web**-Container aus.

3. Wählen Sie die Schaltfläche **Hochladen** aus, um Dateien hochzuladen.

   ![Hochladen von Dateien](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Wenn Sie möchten, dass im Browser der Inhalt der Datei angezeigt wird, stellen Sie sicher, dass der Inhaltstyp dieser Datei auf `text/html` festgelegt ist. 

   ![Prüfen von Inhaltstypen](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage-Explorer legt diese Eigenschaft für häufig erkannte Erweiterungen wie `.html` automatisch auf `text/html` fest. In einigen Fällen müssen Sie dies jedoch selbst festlegen. Wenn Sie diese Eigenschaft nicht auf `text/html` festlegen, werden die Benutzer vom Browser aufgefordert, die Datei herunterzuladen, anstatt den Inhalt zu rendern. Zum Festlegen dieser Eigenschaft klicken Sie mit der rechten Maustaste auf die Datei und dann auf **Eigenschaften**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Laden Sie Objekte aus einem Quellverzeichnis in den Container *$web* hoch.

In diesem Beispiel wird davon ausgegangen, dass Sie Befehle aus einer Azure Cloud Shell-Sitzung ausführen.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Wenn der Browser Benutzer auffordert, die Datei herunterzuladen, anstatt den Inhalt zu rendern, können Sie `--content-type 'text/html; charset=utf-8'` an den Befehl anhängen. 

* Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalter `<source-path>` durch einen Pfad zum Speicherort der Dateien, die Sie hochladen möchten.

> [!NOTE]
> Wenn Sie eine Speicherortinstallation der Azure-CLI verwenden, können Sie den Pfad zu einem beliebigen Speicherort auf Ihrem lokalen Computer verwenden (z.B. `C:\myFolder`).
>
> Wenn Sie Azure Cloud Shell verwenden, müssen Sie auf eine Dateifreigabe verweisen, die für Cloud Shell sichtbar ist. Dieser Speicherort kann die Dateifreigabe der Cloudfreigabe selbst sein oder eine vorhandene Dateifreigabe, die Sie aus Cloud Shell bereitstellen. Informationen zur Vorgehensweise finden Sie unter [Beibehalten von Dateien in Azure Cloud Shell](../../cloud-shell/persisting-shell-storage.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Laden Sie Objekte aus einem Quellverzeichnis in den Container *$web* hoch.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Wenn der Browser Benutzer auffordert, die Datei herunterzuladen, anstatt den Inhalt zu rendern, können Sie `-Properties @{ ContentType = "text/html; charset=utf-8";}` an den Befehl anhängen.

* Ersetzen Sie den Platzhalterwert `<path-to-file>` durch den vollqualifizierten Pfad zu der Datei, die Sie hochladen möchten (z.B. `C:\temp\index.html`).

* Ersetzen Sie den Platzhalterwert `<blob-name>` durch den Namen, den Sie dem resultierenden Blob geben möchten (z.B. `index.html`).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Suchen der Website-URL

Mit der öffentlichen URL der Website können Sie die Seiten Ihrer Website in einem Browser anzeigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie in dem Bereich, der neben der Übersichtsseite für Ihr Speicherkonto angezeigt wird, die Option **Statische Website** aus. Die URL Ihrer Website wird im Feld **Primärer Endpunkt** angezeigt.

![Metriken von statischen Websites in Azure Storage: Metrik](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Suchen Sie die öffentliche URL Ihrer statischen Website mit dem folgenden Befehl:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Suchen Sie die öffentliche URL Ihrer statischen Website mit dem folgenden Befehl:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

* Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Aktivieren von Metriken auf Seiten der statischen Website

Nachdem Sie Metriken aktiviert haben, werden Datenverkehrsstatistiken zu Dateien im Container **$web** im Dashboard für Metriken gemeldet.

1. Klicken Sie im Menü des Speicherkontos im Abschnitt **Überwachung** auf **Metriken**.

   > [!div class="mx-imgBorder"]
   > ![Link „Metriken“](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Metrikdaten werden durch Einbinden verschiedener Metrik-APIs generiert. Das Portal zeigt nur die API-Elemente an, die innerhalb eines bestimmten Zeitrahmens verwendet werden. Dies ermöglicht eine Konzentration auf die Elemente, die Daten zurückgeben. Um sicherzustellen, dass Sie die erforderlichen API-Elemente auswählen können, müssen Sie zuerst den Zeitrahmen erweitern.

2. Klicken Sie auf die Schaltfläche für den Zeitrahmen, wählen Sie einen Zeitrahmen aus, und klicken Sie dann auf **Übernehmen**.

   ![Metriken von statischen Websites in Azure Storage: Zeitbereich](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wählen Sie **Blob** aus der Dropdownliste *Namespace* aus.

   ![Metriken von statischen Websites in Azure Storage: Namespace](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Wählen Sie dann die Metrik **Ausgehend** aus.

   ![Der Screenshot zeigt die Metrik „Ausgehend“ für statische Websites in Azure Storage.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wählen Sie **Summe** aus dem Selektor *Aggregation* aus.

   ![Metriken von statischen Websites in Azure Storage: Aggregation](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicken Sie auf die Schaltfläche **Filter hinzufügen**, und wählen Sie **API-Name** aus dem Selektor *Eigenschaft* aus.

   ![Metriken von statischen Websites in Azure Storage: API-Name](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Aktivieren Sie das Kontrollkästchen neben **GetWebContent** im Selektor *Werte*, um den Metrikbericht zu füllen.

   ![Metriken von statischen Websites in Azure Storage: GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Das Kontrollkästchen **GetWebContent** wird nur angezeigt, wenn dieses API-Element innerhalb eines bestimmten Zeitrahmens verwendet wurde. Das Portal zeigt nur die API-Elemente an, die innerhalb eines bestimmten Zeitrahmens verwendet werden. Dies ermöglicht eine Konzentration auf die Elemente, die Daten zurückgeben. Wenn ein bestimmtes API-Element in dieser Liste nicht zu finden ist, erweitern Sie den Zeitrahmen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie eine benutzerdefinierte Domäne mit Ihrer statischen Website konfigurieren. Weitere Informationen finden Sie unter [Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt](storage-custom-domain-name.md).