---
title: Hosten einer statischen Website in Azure Storage
description: Hier erfahren Sie, wie Sie statische Inhalte (HTML-, CSS-, JavaScript- und Bilddateien) direkt über einen Container in einem Azure Storage GPv2-Konto bereitstellen.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 61477767c59dd521e3f46db4445238a5a1ea759e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071435"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hosten einer statischen Website in Azure Storage

Sie können statische Inhalte (HTML-, CSS-, JavaScript- und Bilddateien) direkt über einen Container in einem Azure Storage GPv2-Konto bereitstellen. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](storage-blob-static-website.md).

In diesem Artikel erfahren Sie, wie Sie das Hosting statischer Websites über das Azure-Portal, mit der Azure-CLI oder mit Azure PowerShell aktivieren.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Ein Tutorial mit schrittweisen Anleitungen finden Sie unter [Tutorial: Host a static website on Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host) (Tutorial: Hosten einer statischen Website in Blob Storage) aus, bevor Sie mit diesem Tutorial beginnen.

Nachdem Sie das Hosting statischer Websites aktiviert haben, können Sie die Seiten Ihrer Website über deren öffentliche URL in einem Browser anzeigen.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Suchen der Website-URL über das Azure-Portal

Wählen Sie in dem Bereich, der neben der Übersichtsseite für Ihr Speicherkonto angezeigt wird, die Option **Statische Website** aus. Die URL Ihrer Website wird im Feld **Primärer Endpunkt** angezeigt.

![Metriken von statischen Websites in Azure Storage: Metrik](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Sie können das Hosting statischer Websites über die [Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) aktivieren.

1. Öffnen Sie zunächst [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), oder falls Sie die Azure-CLI lokal [installiert](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Installieren Sie über das soeben geöffnete Befehlsfenster die Speichererweiterung in der Vorschauversion.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

4. Aktivieren Sie das Hosting statischer Websites.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   * Ersetzen Sie den Platzhalter `<error-document-name>` durch den Namen des Fehlerdokuments, das den Benutzern angezeigt wird, wenn ein Browser eine nicht vorhandene Seite auf Ihrer Website anfordert.

   * Ersetzen Sie den Platzhalter `<index-document-name>` durch den Namen des Indexdokuments. Dieses Dokument ist üblicherweise „index.html“.

5. Laden Sie Objekte aus einem Quellverzeichnis in den Container *$web* hoch.

   > [!NOTE]
   > Wenn Sie Azure Cloud Shell verwenden, vergewissern Sie sich, dass Sie beim Verweisen auf den `$web`-Container ein Escapezeichen `\` hinzufügen (z.B. `\$web`). Wenn Sie eine lokale Installation der Azure-CLI verwenden, müssen Sie das Escapezeichen nicht verwendet.

   In diesem Beispiel wird davon ausgegangen, dass Sie Befehle aus einer Azure Cloud Shell-Sitzung ausführen.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   * Ersetzen Sie den Platzhalter `<source-path>` durch einen Pfad zum Speicherort der Dateien, die Sie hochladen möchten.

   > [!NOTE]
   > Wenn Sie eine Speicherortinstallation der Azure-CLI verwenden, können Sie den Pfad zu einem beliebigen Speicherort auf Ihrem lokalen Computer verwenden (z.B. `C:\myFolder`).
   >
   > Wenn Sie Azure Cloud Shell verwenden, müssen Sie auf eine Dateifreigabe verweisen, die für Cloud Shell sichtbar ist. Dieser Speicherort kann die Dateifreigabe der Cloudfreigabe selbst sein oder eine vorhandene Dateifreigabe, die Sie aus Cloud Shell bereitstellen. Informationen zur Vorgehensweise finden Sie unter [Beibehalten von Dateien in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Suchen der Website-URL mit der Azure-CLI

Mit der öffentlichen URL der Website können Sie Inhalte in einem Browser anzeigen.

Suchen Sie mit dem folgenden Befehl nach der URL:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

* Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

<a id="powershell" />

## <a name="use-powershell"></a>Verwenden von PowerShell

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

7. Laden Sie Objekte aus einem Quellverzeichnis in den Container *$web* hoch.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Ersetzen Sie den Platzhalterwert `<path-to-file>` durch den vollqualifizierten Pfad zu der Datei, die Sie hochladen möchten (z.B. `C:\temp\index.html`).

   * Ersetzen Sie den Platzhalterwert `<blob-name>` durch den Namen, den Sie dem resultierenden Blob geben möchten (z.B. `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Suchen der Website-URL mit PowerShell

Mit der öffentlichen URL der Website können Sie Inhalte in einem Browser anzeigen.

Suchen Sie mit dem folgenden Befehl nach der URL:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

* Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Aktivieren von Metriken auf Seiten der statischen Website

Nachdem Sie Metriken aktiviert haben, werden Datenverkehrsstatistiken zu Dateien im Container **$web** im Dashboard für Metriken gemeldet.

1. Klicken Sie auf **Einstellungen** > **Überwachung** > **Metriken**.

   Metrikdaten werden durch Einbinden verschiedener Metrik-APIs generiert. Das Portal zeigt nur die API-Elemente an, die innerhalb eines bestimmten Zeitrahmens verwendet werden. Dies ermöglicht eine Konzentration auf die Elemente, die Daten zurückgeben. Um sicherzustellen, dass Sie die erforderlichen API-Elemente auswählen können, müssen Sie zuerst den Zeitrahmen erweitern.

2. Klicken Sie auf die Schaltfläche für den Zeitrahmen, und wählen Sie **Letzte 24 Stunden** aus. Klicken Sie dann auf **Übernehmen**.

   ![Metriken von statischen Websites in Azure Storage: Zeitbereich](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wählen Sie **Blob** aus der Dropdownliste *Namespace* aus.

   ![Metriken von statischen Websites in Azure Storage: Namespace](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Wählen Sie dann die Metrik **Ausgehend** aus.

   ![Metriken von statischen Websites in Azure Storage: Metrik](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wählen Sie **Summe** aus dem Selektor *Aggregation* aus.

   ![Metriken von statischen Websites in Azure Storage: Aggregation](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicken Sie auf die Schaltfläche **Filter hinzufügen**, und wählen Sie **API-Name** aus dem Selektor *Eigenschaft* aus.

   ![Metriken von statischen Websites in Azure Storage: API-Name](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Aktivieren Sie das Kontrollkästchen neben **GetWebContent** im Selektor *Werte*, um den Metrikbericht zu füllen.

   ![Metriken von statischen Websites in Azure Storage: GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Nächste Schritte

* [Hosten von statischen Websites in Azure Storage](storage-blob-static-website.md)
* [Verwenden von Azure-CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](storage-custom-domain-name.md)
* [Azure-Funktionen](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Build a serverless web app in Azure](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database) (Erstellen einer serverlosen Web-App in Azure)
* [Tutorial: Hosten Ihrer Domäne in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
