---
title: Verwenden von Azure CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS
description: In diesem Artikel wird erläutert, wie Azure CDN in Blobspeicher integriert werden kann, um mit benutzerdefinierten Domänen über HTTPS auf Blobs zugreifen zu können.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: da3a6dcb0d125ac4666bc375e843c57cf12fb2fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148407"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Verwenden von Azure CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS

Azure Content Delivery Network (Azure CDN) unterstützt nun HTTPS für benutzerdefinierte Domänennamen. Mit Azure CDN können Sie unter Verwendung Ihres benutzerdefinierten Domänennamens über HTTPS auf Blobs zugreifen. Aktivieren Sie zu diesem Zweck Azure CDN auf Ihrem Blob- oder Webendpunkt, und ordnen Sie dann Azure CDN einem benutzerdefinierten Domänennamen zu. Danach ermöglicht Azure dank Zugriff mit nur einem Klick und vollständiger Zertifikatverwaltung eine einfachere Aktivierung von HTTPS für Ihre benutzerdefinierte Domäne. Die üblichen Azure CDN-Preise bleiben gleich.

Azure CDN trägt während der Datenübertragung zum Schutz und zur Integrität der Webanwendungsdaten bei. Azure CDN nutzt das SSL-Protokoll während der Übertragung über HTTPS. Die Daten sind also verschlüsselt, wenn sie über das Internet gesendet werden. Die Verwendung von HTTPS mit Azure CDN trägt zum Schutz Ihrer Webanwendungen vor Angriffen bei.

> [!NOTE]  
> Zusätzlich zur Bereitstellung von SSL-Unterstützung für benutzerdefinierte Domänennamen kann Azure CDN Sie bei der Skalierung Ihrer Anwendung unterstützen, damit diese weltweit Inhalte mit hoher Bandbreite übermitteln kann. Weitere Informationen finden Sie in der [Übersicht über Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Schnellstart

Führen Sie zum Aktivieren von HTTPS für Ihren benutzerdefinierten Blobspeicher-Endpunkt folgende Schritte aus:

1.  [Integrieren eines Azure-Speicherkontos in CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Dieser Artikel führt Sie durch das Erstellen eines Speicherkontos im Azure-Portal, sofern Sie dies nicht bereits erledigt haben.

    > [!NOTE]  
    > Wählen Sie während der Vorschauphase der Unterstützung statischer Websites in Azure Storage im Dropdownmenü **Ursprungstyp** die Option **Benutzerdefinierter Ursprung** aus, um Ihren Speicher-Webendpunkt hinzuzufügen. Im Azure-Portal müssen Sie diesen Schritt über Ihr Azure CDN-Profil ausführen (nicht direkt in Ihrem Speicherkonto).

2.  [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Aktivieren von HTTPS für eine benutzerdefinierte Azure CDN-Domäne](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Shared Access Signatures

Die Blobspeicher-Endpunkte lehnen anonymen Lesezugriff standardmäßig ab. Wenn der Blobspeicher-Endpunkt so konfiguriert ist, dass anonymer Lesezugriff unterbunden wird, müssen Sie bei jeder Anforderung an Ihre benutzerdefinierte Domäne ein [Shared Access Signature](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Token (SAS-Token) bereitstellen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](storage-manage-access-to-resources.md).

Azure CDN berücksichtigt keine dem SAS-Token hinzugefügten Einschränkungen. Beispielsweise laufen alle SAS-Token ab. Sie können mit einem abgelaufenen SAS-Token weiterhin auf Inhalte zugreifen, bis diese endgültig aus den Azure CDN-Edgeknoten gelöscht werden. Über die Einstellung im Cacheantwortheader können Sie steuern, wie lange die Daten im Azure CDN zwischengespeichert werden. Informationen dazu finden Sie unter [Verwalten des Ablaufs von Azure Blob-Speicher in Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Wenn Sie mindestens zwei SAS-URLs für den gleichen Blobendpunkt erstellen, empfehlen wir Ihnen, in Azure CDN das Zwischenspeichern der Abfragezeichenfolge zu aktivieren. Dadurch wird sichergestellt, dass Azure jede URL als eindeutige Entität behandelt. Weitere Informationen finden Sie unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP-HTTPS-Umleitung

Sie können HTTP-Datenverkehr an HTTPS umleiten. Dafür ist das Azure CDN-Premiumangebot von Verizon erforderlich. [Überschreiben Sie das HTTP-Verhalten mithilfe der Azure CDN-Regel-Engine](../../cdn/cdn-rules-engine.md) mit der folgenden Regel:

![Regel zur Umleitung von HTTP an HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

Der in der Dropdownliste ausgewählte Eintrag *cdn-endpoint-name* bezieht sich auf den Namen, den Sie für Ihren Azure CDN-Endpunkt konfiguriert haben. *origin-path* bezieht sich auf den Pfad in Ihrem Ursprungsspeicherkonto, an dem Ihr statischer Inhalt gespeichert ist. Wenn Sie alle statischen Inhalte in einem einzelnen Container hosten, ersetzen Sie *origin-path* durch den entsprechenden Containernamen.

Eine weiterführende Beschreibung der Regeln finden Sie in [Features der Azure CDN-Regel-Engine](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Beim Zugriff auf Blobs über Azure CDN bezahlen Sie [Blobspeichergebühren](https://azure.microsoft.com/pricing/details/storage/blobs/) für den Datenverkehr zwischen den Edgeknoten und dem Ursprung (Blobspeicher). Sie bezahlen [Azure CDN-Gebühren](https://azure.microsoft.com/pricing/details/cdn/) für Daten, auf die von den Edgeknoten aus zugegriffen wird.

Angenommen, Sie verfügen über ein Speicherkonto in der Region „USA, Westen“, auf das über Azure CDN zugegriffen wird. Wenn nun eine Person im Vereinigten Königreich versucht, über Azure CDN auf ein Blob in diesem Speicherkonto zuzugreifen, sucht Azure das Blob zunächst auf dem Edgeknoten, der dem Vereinigten Königreich am nächsten liegt. Wenn Azure das Blob findet, wird auf eine Kopie zugegriffen. Dabei gelten die Azure CDN-Preise, da Azure CDN auf die Kopie zugreift. Findet Azure das Blob nicht, wird es auf den Edgeknoten kopiert. Diese Aktion zieht gemäß den angegebenen Blobspeicherpreisen Gebühren für ausgehenden Datenverkehr sowie Transaktionsgebühren nach sich. Azure greift dann auf die Datei auf dem Edgeknoten zu. Dies wird über Azure CDN abgerechnet.

Auf der Seite mit den [Azure CDN-Preisen](https://azure.microsoft.com/pricing/details/cdn/) wird HTTPS-Unterstützung für benutzerdefinierte Domänennamen nur für Azure CDN aus Verizon-Produkten (Standard und Premium) angeboten.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob-Speicherendpunkt](storage-custom-domain-name.md)
* [Hosten von statischen Websites in Azure Storage](storage-blob-static-website.md)
