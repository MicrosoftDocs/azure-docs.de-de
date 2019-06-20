---
title: 'Tutorial: Zugreifen auf Speicherblobs unter Verwendung einer benutzerdefinierten Azure CDN-Domäne über HTTPS | Microsoft-Dokumentation'
description: ''
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 51fcb1e504f853973d9772bcece7e893a2d94e44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472135"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Tutorial: Zugreifen auf Speicherblobs unter Verwendung einer benutzerdefinierten Azure CDN-Domäne über HTTPS

Nachdem Sie Ihr Azure-Speicherkonto in das Azure Content Delivery Network (CDN) integriert haben, können Sie eine benutzerdefinierte Domäne hinzufügen und HTTPS in dieser Domäne für Ihren benutzerdefinierten Blobspeicher-Endpunkt aktivieren. 

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zuerst Ihr Azure-Speicherkonto im Azure CDN integrieren. Weitere Informationen finden Sie unter [Quickstart: Integrieren eines Azure-Speicherkontos in Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Hinzufügen einer benutzerdefinierten Domäne
Wenn Sie einen CDN-Endpunkt in Ihrem Profil erstellen, wird der Endpunktname, bei dem es sich um eine Unterdomäne von „azureedge.net“ handelt, standardmäßig in die URL für die Bereitstellung von CDN-Inhalten eingefügt. Außerdem haben Sie die Möglichkeit, eine benutzerdefinierte Domäne einem CDN-Endpunkt zuzuordnen. Bei dieser Option stellen Sie Ihren Inhalt über eine benutzerdefinierte Domäne in Ihrer URL und nicht über einen Endpunktnamen bereit. Um Ihrem Endpunkt eine benutzerdefinierte Domäne hinzuzufügen, befolgen Sie die Anweisungen in diesem Tutorial: [Hinzufügen einer benutzerdefinierten Domäne zum Azure CDN-Endpunkt](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurieren von HTTPS
Durch Verwenden des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne stellen Sie sicher, dass Ihre Daten im Internet über die TLS-/SSL-Verschlüsselung sicher zugestellt werden. Wenn Ihr Webbrowser über HTTPS eine Verbindung mit einer Website herstellt, überprüft er das Sicherheitszertifikat der Website, um sicherzustellen, dass es von einer legitimen Zertifizierungsstelle stammt. Um HTTPS für Ihre benutzerdefinierte Domäne zu konfigurieren, befolgen Sie die Anweisungen in diesem Tutorial: [Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Shared Access Signatures
Wenn der Blobspeicher-Endpunkt so konfiguriert ist, dass anonyme Lesezugriffe unterbunden werden, müssen Sie bei jeder Anforderung an Ihre benutzerdefinierte Domäne ein [Shared Access Signature](cdn-sas-storage-support.md)-Token (SAS-Token) bereitstellen. Die Blob-Speicherendpunkte lehnen anonyme Lesezugriffe standardmäßig ab. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../storage/blobs/storage-manage-access-to-resources.md).

Zum SAS-Token hinzugefügte Einschränkungen werden von Azure CDN ignoriert. Beispielsweise verfügen alle SAS-Token über eine Ablaufzeit, d.h., Inhalte sind auch mit einer abgelaufenen SAS weiterhin zugänglich, bis sie von den CDN-POP-Servern (Point-of-Presence) gelöscht werden. Über die Einstellung im Cacheantwortheader können Sie steuern, wie lange die Daten im Azure CDN zwischengespeichert werden. Weitere Informationen hierzu finden Sie unter [Verwalten des Ablaufs von Azure Storage-Blobs im Azure CDN](cdn-manage-expiration-of-blob-content.md).

Wenn Sie für denselben Blobendpunkt mehrere SAS-URLs erstellen, sollten Sie das Aktivieren der Zwischenspeicherung von Abfragezeichenfolge in Betracht ziehen. Dadurch wird sichergestellt, dass jede URL als eindeutige Entität behandelt wird. Weitere Informationen finden Sie unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP-zu-HTTPS-Umleitung
Sie können das Umleiten von HTTP-Datenverkehr an HTTPS festlegen, indem Sie bei der [Azure CDN-Regel-Engine](cdn-verizon-premium-rules-engine.md) eine [URL-Umleitungsregel](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect) erstellen. Für diese Option ist ein **Azure CDN Premium von Verizon**-Profil erforderlich.

![URL-Umleitungsregel](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

In dieser Regel bezieht sich *Cdn-endpoint-name* auf den Namen, den Sie für den CDN-Endpunkt konfiguriert haben und der aus der Dropdownliste ausgewählt werden kann. Der Wert für *origin-path* bezieht sich auf den Pfad in Ihrem Ursprungsspeicherkonto, in dem sich Ihr statischer Inhalt befindet. Wenn Sie alle statischen Inhalte in einem einzelnen Container hosten, ersetzen Sie *origin-path* durch den entsprechenden Containernamen.

## <a name="pricing-and-billing"></a>Preise und Abrechnung
Beim Zugriff auf Blobs über Azure CDN bezahlen Sie [Blobspeichergebühren](https://azure.microsoft.com/pricing/details/storage/blobs/) für den Datenverkehr zwischen POP-Servern und dem Ursprung (Blobspeicher). Weiterhin zahlen Sie [Azure CDN-Gebühren](https://azure.microsoft.com/pricing/details/cdn/) für Daten, auf die von den POP-Servern aus zugegriffen wird.

Wenn Sie beispielsweise in den Vereinigten Staaten ein Konto besitzen, auf das über Azure CDN zugegriffen wird, und jemand in Europa versucht, über Azure CDN auf eines der Blobs in diesem Speicherkonto zuzugreifen, überprüft Azure CDN für dieses Blob zuerst den POP-Server, der Europa am nächsten liegt. Wird Azure CDN fündig, greift es auf diese Kopie des Blobs zu und wendet die CDN-Gebühren an, weil der Zugriff über Azure CDN erfolgt. Wird Azure CDN nicht fündig, wird das Blob auf den POP-Server kopiert, was gemäß den angegebenen Blobspeicherpreisen Gebühren für ausgehenden Datenverkehr und Transaktionsgebühren nach sich zieht. Dann greift Azure CDN auf dem POP-Server auf die Datei zu, was zu einer Azure CDN-Abrechnung führt.

## <a name="next-steps"></a>Nächste Schritte
[Tutorial: Festlegen der Azure CDN-Cacheregeln](cdn-caching-rules-tutorial.md)




