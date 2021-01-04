---
title: Hosten von statischen Websites in Azure Storage
description: Das Hosten von statischen Websites in Azure Storage bietet eine kostengünstige, skalierbare Lösung zum Hosten von modernen Webanwendungen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: b9eb65311951706863c3b18c5fc91bae8c41c7dc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96007340"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosten von statischen Websites in Azure Storage

Sie können statische Inhalte (HTML, CSS, JavaScript und Bilddateien) direkt über einen Speichercontainer namens *$web* bereitstellen. Das Hosten von Inhalten in Azure Storage ermöglicht die Verwendung serverloser Architekturen mit [Azure Functions](../../azure-functions/functions-overview.md) und anderen PaaS-Diensten (Platform-as-a-Service). Das statische Hosting von Websites mit Azure Storage ist eine ideale Option für Fälle, in denen Sie keinen Webserver zum Rendern von Inhalten benötigen.

[Static Web Apps von App Service](https://azure.microsoft.com/services/app-service/static/) sind eine hervorragend Alternative zum statischen Hosting von Websites mit Azure Storage, die sich auch für Fälle eignet, in denen Sie keinen Webserver zum Rendern von Inhalten benötigen. Static Web Apps von App Service bieten Ihnen einen vollständig verwalteten Continuous Integration- und Continuous Delivery-Workflow (CI/CD) von der GitHub-Quelle bis zur globalen Bereitstellung.

Wenn Sie einen Webserver zum Rendern von Inhalten benötigen, können Sie [Azure App Service](https://azure.microsoft.com/services/app-service/)verwenden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Erstellen Sie unbedingt ein Standardspeicherkonto vom Typ „Universell v2 (GPv2)“. Statische Websites sind in keinem anderen Speicherkontotyp verfügbar.

## <a name="setting-up-a-static-website"></a>Einrichten einer statischen Website

Das Feature zum Hosten statischer Websites muss für das Speicherkonto aktiviert werden.

Wählen Sie hierzu den Namen Ihrer Standarddatei aus, und geben Sie optional einen Pfad zu einer benutzerdefinierten 404-Seite an. Sollte in Ihrem Konto noch kein Blobspeichercontainer namens **$web** vorhanden sein, wird automatisch einer erstellt. Fügen Sie diesem Container die Dateien Ihrer Website hinzu.

Eine entsprechende Schritt-für-Schritt-Anleitung finden Sie unter [Host a static website in Azure Storage](storage-blob-static-website-how-to.md) (Hosten einer statischen Website in Azure Storage).

![Metriken von statischen Websites in Azure Storage: Metrik](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Bei Dateien im Container **$web** wird die Groß-/Kleinschreibung beachtet. Sie werden über anonyme Zugriffsanforderungen bereitgestellt und sind nur über Lesevorgänge verfügbar.

## <a name="uploading-content"></a>Hochladen von Inhalten

Inhalte können mit einem der folgenden Tools in den Container **$web** hochgeladen werden:

> [!div class="checklist"]
> * [Azure-Befehlszeilenschnittstelle](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell-Modul](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-Erweiterung](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Anzeigen von Inhalten

Benutzer können Websiteinhalte in einem Browser unter der öffentlichen URL der Website anzeigen. Die URL können Sie im Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe von PowerShell ermitteln. Siehe [Suchen der Website-URL](storage-blob-static-website-how-to.md#portal-find-url).

Wenn der Server einen 404-Fehler zurückgibt und Sie beim Aktivieren der Website kein Fehlerdokument angegeben haben, wird an den Benutzer eine 404-Standardseite zurückgegeben.

> [!NOTE]
> [Cross-Origin Resource Sharing-Unterstützung (CORS) für Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) wird bei statischen Websites nicht unterstützt.

### <a name="regional-codes"></a>Regionscodes

Die URL Ihrer Website enthält einen Regionscode. Die URL `https://contosoblobaccount.z22.web.core.windows.net/` enthält beispielsweise den Regionscode `z22`.

Dieser Code muss zwar in der URL verbleiben, wird jedoch nur intern genutzt und muss nicht anderweitig verwendet werden.

Das Indexdokument, das Sie beim Aktivieren des Hostens von statischen Websites angeben, wird angezeigt, wenn Benutzer beim Aufrufen der Website keine bestimmte Datei angeben (Beispiel: `https://contosoblobaccount.z22.web.core.windows.net`).

### <a name="secondary-endpoints"></a>Sekundäre Endpunkte

Wenn Sie [Redundanz in einer sekundären Region](../common/storage-redundancy.md#redundancy-in-a-secondary-region) einrichten, können Sie auch über einen sekundären Endpunkt auf Websiteinhalte zugreifen. Da Daten asynchron in sekundäre Regionen repliziert werden, sind die am sekundären Endpunkt verfügbaren Dateien nicht immer mit den Dateien synchron, die auf dem primären Endpunkt verfügbar sind.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Auswirkungen der Einstellung der öffentlichen Zugriffsebene des Webcontainers

Sie können zwar die öffentliche Zugriffsebene des Containers **$web** ändern, dies hat jedoch keinerlei Auswirkungen auf den primären Endpunkt der statischen Website, da diese Dateien über anonyme Zugriffsanforderungen bereitgestellt werden. Das bedeutet öffentlichen (schreibgeschützten) Zugriff auf alle Dateien.

Der folgende Screenshot zeigt die Einstellung der öffentlichen Zugriffsebene im Azure-Portal:

![Screenshot: Festlegen der öffentlichen Zugriffsebene im Azure-Portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Der primäre Endpunkt der statischen Website ist von einer Änderung der öffentlichen Zugriffsebene zwar nicht betroffen, eine solche Änderung hat jedoch Auswirkungen auf den primären Endpunkt des Blobdiensts.

Wenn Sie also beispielsweise die öffentliche Zugriffsebene des Containers **$web** von **Privat (kein anonymer Zugriff)** in **Blob (anonymer Lesezugriff nur für Blobs)** ändern, bleibt die öffentliche Zugriffsebene für den primären Endpunkt der statischen Website (`https://contosoblobaccount.z22.web.core.windows.net/index.html`) unverändert.

Der öffentliche Zugriff auf den primären Endpunkt des Blobdiensts (`https://contosoblobaccount.blob.core.windows.net/$web/index.html`) ändert sich hingegen von privat in öffentlich. Nun können Benutzer die Datei unter Verwendung eines der beiden Endpunkte öffnen.

Die Deaktivierung des öffentlichen Zugriffs auf ein Speicherkonto wirkt sich nicht auf statische Websites aus, die in diesem Speicherkonto gehostet werden. Weitere Informationen finden Sie unter [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Zuordnen einer benutzerdefinierten Domäne zu einer URL einer statischen Website

Sie können Ihre statische Website über eine benutzerdefinierte Domäne verfügbar machen.

Es ist einfacher, den HTTP-Zugriff für Ihre benutzerdefinierte Domäne zu aktivieren, weil dies von Azure Storage nativ unterstützt wird. Zum Aktivieren von HTTPS müssen Sie Azure CDN verwenden, weil Azure Storage HTTPS bei benutzerdefinierten Domänen noch nicht nativ unterstützt. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt](storage-custom-domain-name.md).

Wenn das Speicherkonto für die [sichere Übertragung](../common/storage-require-secure-transfer.md) über HTTPS konfiguriert ist, müssen Benutzer den HTTPS-Endpunkt verwenden.

> [!TIP]
> Ziehen Sie das Hosten Ihrer Domäne in Azure in Betracht. Weitere Informationen finden Sie unter [Hosten Ihrer Domäne in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Hinzufügen von HTTP-Headern

Es gibt keine Möglichkeit, Header im Rahmen des Features für die statische Website zu konfigurieren. Sie können jedoch Azure CDN zum Hinzufügen von Headern und Anfügen (oder Überschreiben) von Headerwerten verwenden. Informationen dazu finden Sie in der [Referenz zur Standardregel-Engine für Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Wenn Sie Header zum Steuern der Zwischenspeicherung verwenden möchten, finden Sie Informationen dazu unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Chacheregeln](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Hosting von Websites in mehreren Regionen

Wenn Sie beabsichtigen, eine Website in mehreren Regionen zu hosten, empfiehlt es sich, ein [Content Delivery Network](../../cdn/index.yml) für regionales Zwischenspeichern zu verwenden. Setzen Sie [Azure Front Door](../../frontdoor/index.yml) ein, wenn Sie in den einzelnen Regionen unterschiedliche Inhalte bereitstellen möchten. Zudem werden von dieser Lösung Failoverfunktionen geboten. [Azure Traffic Manager](../../traffic-manager/index.yml) wird nicht empfohlen, wenn Sie den Einsatz einer benutzerdefinierten Domäne vorhaben. Probleme können sich daraus ergeben, wie Azure Storage benutzerdefinierte Domänennamen verifiziert.


## <a name="pricing"></a>Preise

Die Aktivierung des Hostings statischer Websites ist kostenlos. Ihnen werden lediglich der von Ihrer Website genutzte Blobspeicher sowie die Betriebskosten in Rechnung gestellt. Weitere Informationen zu Preisen für Azure Blob Storage finden Sie auf der Seite [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriken

Sie können Metriken für Seiten der statischen Website aktivieren. Nachdem Sie Metriken aktiviert haben, werden Datenverkehrsstatistiken zu Dateien im Container **$web** im Dashboard für Metriken gemeldet.

Informationen zum Aktivieren von Metriken für Seiten Ihrer statischen Website finden Sie unter [Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics) (Aktivieren von Metriken auf Seiten der statischen Website).

## <a name="next-steps"></a>Nächste Schritte

* [Host a static website in Azure Storage](storage-blob-static-website-how-to.md) (Hosten einer statischen Website in Azure Storage)
* [Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt](storage-custom-domain-name.md)
* [Azure-Funktionen](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [Build a serverless web app in Azure](/azure/functions/tutorial-static-website-serverless-api-with-database) (Erstellen einer serverlosen Web-App in Azure)
* [Tutorial: Hosten Ihrer Domäne in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)