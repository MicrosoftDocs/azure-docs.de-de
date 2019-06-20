---
title: Hosten von statischen Websites in Azure Storage
description: Das Hosten von statischen Websites in Azure Storage bietet eine kostengünstige, skalierbare Lösung zum Hosten von modernen Webanwendungen.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427955"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosten von statischen Websites in Azure Storage

Sie können statische Inhalte (HTML, CSS, JavaScript und Bilddateien) direkt über einen Speichercontainer namens *$web* bereitstellen. Das Hosten von Inhalten in Azure Storage ermöglicht die Verwendung serverloser Architekturen mit [Azure Functions](/azure/azure-functions/functions-overview) und anderen PaaS-Diensten (Platform-as-a-Service).

> [!NOTE]
> Sollte Ihre Website von serverseitigem Code abhängig sein, verwenden Sie stattdessen [Azure App Service](/azure/app-service/overview).

## <a name="setting-up-a-static-website"></a>Einrichten einer statischen Website

Das Feature zum Hosten statischer Websites muss für das Speicherkonto aktiviert werden.

Wählen Sie hierzu den Namen Ihrer Standarddatei aus, und geben Sie optional einen Pfad zu einer benutzerdefinierten 404-Seite an. Sollte in Ihrem Konto noch kein Blobspeichercontainer namens **$web** vorhanden sein, wird automatisch einer erstellt. Fügen Sie diesem Container die Dateien Ihrer Website hinzu.

Eine entsprechende Schritt-für-Schritt-Anleitung finden Sie unter [Host a static website in Azure Storage](storage-blob-static-website-how-to.md) (Hosten einer statischen Website in Azure Storage).

![Metriken von statischen Websites in Azure Storage: Metrik](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Bei Dateien im Container **$web** wird die Groß-/Kleinschreibung beachtet. Sie werden über anonyme Zugriffsanforderungen bereitgestellt und sind nur über Lesevorgänge verfügbar.

## <a name="uploading-content"></a>Hochladen von Inhalten

Inhalte können mit einem der folgenden Tools in den Container **$web** hochgeladen werden:

> [!div class="checklist"]
> * [Azure-Befehlszeilenschnittstelle](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-Modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-Erweiterung](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Anzeigen von Inhalten

Benutzer können Websiteinhalte in einem Browser unter der öffentlichen URL der Website anzeigen. Die URL können Sie im Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe von PowerShell ermitteln. Verwenden Sie diese Tabelle als Richtlinie.

|Tool| Anleitungen |
|----|----|
|**Azure-Portal** | [Find the website URL by using the Azure portal](storage-blob-static-website-how-to.md#portal-find-url) (Ermitteln der Website-URL über das Azure-Portal) |
|**Azure-Befehlszeilenschnittstelle** | [Find the website URL by using the Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) (Ermitteln der Website-URL mithilfe der Azure-Befehlszeilenschnittstelle) |
|**Azure PowerShell-Modul** | [Find the website URL by using PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) (Ermitteln der Website-URL mithilfe von PowerShell) |

Die URL Ihrer Website enthält einen Regionscode. Die URL `https://contosoblobaccount.z22.web.core.windows.net/` enthält beispielsweise den Regionscode `z22`.

Dieser Code muss zwar in der URL bleiben, er wird jedoch nur intern genutzt und muss nicht anderweitig verwendet werden.

Das Indexdokument, das Sie beim Aktivieren des Hostens von statischen Websites angeben, wird angezeigt, wenn Benutzer beim Aufrufen der Website keine bestimmte Datei angeben (Beispiel: `https://contosoblobaccount.z22.web.core.windows.net`).  

Wenn der Server einen 404-Fehler zurückgibt und Sie beim Aktivieren der Website kein Fehlerdokument angegeben haben, wird an den Benutzer eine 404-Standardseite zurückgegeben.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Auswirkungen der Einstellung der öffentlichen Zugriffsebene des Webcontainers

Sie können zwar die öffentliche Zugriffsebene des Containers **$web** ändern, dies hat jedoch keinerlei Auswirkungen auf den primären Endpunkt der statischen Website, da diese Dateien über anonyme Zugriffsanforderungen bereitgestellt werden. Das bedeutet öffentlichen (schreibgeschützten) Zugriff auf alle Dateien.

Der folgende Screenshot zeigt die Einstellung der öffentlichen Zugriffsebene im Azure-Portal:

![Screenshot: Festlegen der öffentlichen Zugriffsebene im Azure-Portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Der primäre Endpunkt der statischen Website ist von einer Änderung der öffentlichen Zugriffsebene zwar nicht betroffen, eine solche Änderung hat jedoch Auswirkungen auf den primären Endpunkt des Blobdiensts.

Wenn Sie also beispielsweise die öffentliche Zugriffsebene des Containers **$web** von **Privat (kein anonymer Zugriff)** in **Blob (anonymer Lesezugriff nur für Blobs)** ändern, bleibt die öffentliche Zugriffsebene für den primären Endpunkt der statischen Website (`https://contosoblobaccount.z22.web.core.windows.net/index.html`) unverändert.

Der öffentliche Zugriff auf den primären Endpunkt des Blobdiensts (`https://contosoblobaccount.blob.core.windows.net/$web/index.html`) ändert sich hingegen von privat in öffentlich. Nun können Benutzer die Datei unter Verwendung eines der beiden Endpunkte öffnen.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Unterstützung von Content Delivery Network (CDN) und Secure Socket Layer (SSL)

Informationen dazu, wie Sie Ihre statischen Websitedateien über Ihre benutzerdefinierte Domäne und HTTPS verfügbar machen, finden Sie unter [Verwenden von Azure-CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS](storage-https-custom-domain-cdn.md). Im Rahmen dieses Prozesses müssen Sie Ihr CDN auf den primären Endpunkt der *statischen Website* verweisen (nicht auf den primären Endpunkt des *Blobdiensts*). Unter Umständen dauert es ein paar Minuten, bis Ihre Inhalte angezeigt werden, da die CDN-Konfiguration nicht sofort ausgeführt wird.

Wenn Sie Ihre statische Website aktualisieren, stellen Sie sicher, dass Sie zwischengespeicherte Inhalte auf den CDN-Edge-Servern löschen, indem Sie den CDN-Endpunkt bereinigen. Weitere Informationen finden Sie unter [Löschen eines Azure CDN-Endpunkts](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS wird nativ über den Webendpunkt des Kontos unterstützt, sodass sowohl per HTTP als auch per HTTPS auf den Webendpunkt zugegriffen werden kann. Wenn das Speicherkonto jedoch für die sichere Übertragung über HTTPS konfiguriert ist, müssen Benutzer den HTTPS-Endpunkt verwenden. Weitere Informationen finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md).
>
> Die Verwendung von benutzerdefinierten Domänen über HTTPS erfordert derzeit die Verwendung von Azure CDN.

## <a name="custom-domain-names"></a>Benutzerdefinierte Domänennamen

Sie können Ihre statische Website über eine benutzerdefinierte Domäne verfügbar machen. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihr Azure Storage-Konto](storage-custom-domain-name.md).

Einen detaillierten Einblick in das Hosten Ihrer Domäne in Azure finden Sie unter [Hosten Ihrer Domäne in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Preise

Die Aktivierung des Hostings statischer Websites ist kostenlos. Ihnen werden lediglich der von Ihrer Website genutzte Blobspeicher sowie die Betriebskosten in Rechnung gestellt. Weitere Informationen zu Preisen für Azure Blob Storage finden Sie auf der Seite [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriken

Sie können Metriken für Seiten der statischen Website aktivieren. Nachdem Sie Metriken aktiviert haben, werden Datenverkehrsstatistiken zu Dateien im Container **$web** im Dashboard für Metriken gemeldet.

Informationen zum Aktivieren von Metriken für Seiten Ihrer statischen Website finden Sie unter [Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics) (Aktivieren von Metriken auf Seiten der statischen Website).

## <a name="next-steps"></a>Nächste Schritte

* [Host a static website in Azure Storage](storage-blob-static-website-how-to.md) (Hosten einer statischen Website in Azure Storage)
* [Verwenden von Azure-CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](storage-custom-domain-name.md)
* [Azure-Funktionen](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Build a serverless web app in Azure](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database) (Erstellen einer serverlosen Web-App in Azure)
* [Tutorial: Hosten Ihrer Domäne in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
