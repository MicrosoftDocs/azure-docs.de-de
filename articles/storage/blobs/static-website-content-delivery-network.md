---
title: Integrieren einer statischen Website in Azure CDN – Azure Storage
description: Erfahren Sie, wie Sie den Inhalt einer statischen Website aus einem Azure Storage-Konto mithilfe von Azure Content Delivery Network (CDN) zwischenspeichern.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527325"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrieren einer statischen Website in Azure CDN

Sie können [Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) aktivieren, um den Inhalt einer in einem Azure-Speicherkonto gehosteten [statischen Website](storage-blob-static-website.md) zwischenzuspeichern. Mit Azure CDN können Sie den benutzerdefinierten Domänenendpunkt für Ihre statische Website konfigurieren, benutzerdefinierte TLS/SSL-Zertifikate bereitstellen und benutzerdefinierte Umschreibungsregeln konfigurieren. Durch die Konfiguration von Azure CDN fallen zusätzliche Gebühren an, der Dienst bietet jedoch auf der ganzen Welt durchgängig kurze Wartezeiten für Ihre Website. Azure CDN bietet auch eine TLS-Verschlüsselung mit Ihrem eigenen Zertifikat. 

Weitere Informationen zu den Preisen von Azure CDN finden Sie unter [Azure Content Delivery Network – Preise](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Aktivieren von Azure CDN für Ihre statische Website

Sie können Azure CDN direkt über Ihr Speicherkonto für Ihre statische Website aktivieren. Wenn Sie erweiterte Konfigurationseinstellungen (etwa die [Optimierung des Downloads großer Dateien](../../cdn/cdn-optimization-overview.md#large-file-download)) für den CDN-Endpunkt angeben möchten, können Sie stattdessen die [Azure CDN-Erweiterung](../../cdn/cdn-create-new-endpoint.md) verwenden, um ein CDN-Profil und einen CDN-Endpunkt zu erstellen.

1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

1. Wählen Sie im Menü **Blob-Dienst** die Option **Azure CDN** aus, um die Seite **Azure CDN** zu öffnen:

    ![Erstellen eines CDN-Endpunkts](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. Geben Sie im Abschnitt **CDN-Profil** an, ob ein neues CDN-Profil erstellt oder ein vorhandenes Profil verwendet werden soll. Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten mit demselben Tarif und Anbieter. Geben Sie dann einen Namen für das CDN ein, der innerhalb Ihres Abonnements eindeutig ist.

1. Geben Sie einen Tarif für den CDN-Endpunkt an. Weitere Informationen zur Preisgestaltung finden Sie unter [Azure Content Delivery Network – Preise ](https://azure.microsoft.com/pricing/details/cdn/). Weitere Informationen zu den in den einzelnen Tarifen verfügbaren Features finden Sie unter [Vergleichen von Azure CDN-Produktfeatures](../../cdn/cdn-features.md).

1. Geben Sie im Feld **Name des CDN-Endpunkts** einen Namen für Ihren CDN-Endpunkt an. Der CDN-Endpunkt muss innerhalb von Azure eindeutig sein und dient als erster Teil der Endpunkt-URL. Das Formular überprüft, ob der Endpunktname eindeutig ist.

1. Geben Sie im Feld **Hostname des Ursprungs** den Endpunkt Ihrer statischen Website an. 

   Navigieren Sie zum Ermitteln des Endpunkts Ihrer statischen Website zum Abschnitt **Statische Website** Ihres Speicherkontos.  Kopieren Sie den primären Endpunkt, und fügen Sie ihn in die CDN-Konfiguration ein.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie die Protokoll-ID (*z. B.* HTTPS) und den nachgestellten Schrägstrich in der URL entfernen. Wenn der Endpunkt der statischen Website z. B. `https://mystorageaccount.z5.web.core.windows.net/` lautet, geben Sie im Feld **Hostname des Ursprungs** den Namen `mystorageaccount.z5.web.core.windows.net` ein.

   Die folgende Abbildung zeigt eine Beispiel-Endpunktkonfiguration:

   ![Screenshot einer Beispiel-CDN-Endpunktkonfiguration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Wählen Sie **Erstellen** aus, und warten Sie, bis das CDN bereitgestellt ist. Der erstellte Endpunkt wird in der Liste mit den Endpunkten angezeigt. (Wenn Fehler im Formular vorhanden sind, wird neben dem entsprechenden Feld ein Ausrufezeichen angezeigt.)

1. Überprüfen Sie, ob der CDN-Endpunkt korrekt konfiguriert ist. Klicken Sie dazu auf den Endpunkt, um die Einstellungen anzuzeigen. Suchen Sie wie in der folgenden Abbildung dargestellt in der CDN-Übersicht für Ihr Speicherkonto nach dem Endpunkthostnamen, und navigieren Sie zum Endpunkt. Das Format Ihres CDN-Endpunkts sieht in etwa wie folgt aus: `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot mit der Übersicht des CDN-Endpunkts](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Wenn Sie nach der Bereitstellung zum CDN-Endpunkt navigieren, wird der Inhalt der Datei „index.html“ angezeigt, die Sie zuvor in Ihre statische Website hochgeladen haben.

1. Navigieren Sie zum Überprüfen der Ursprungseinstellungen Ihres CDN-Endpunkts im Abschnitt **Einstellungen** für den CDN-Endpunkt zu **Ursprung**. Das Feld **Ursprungstyp** ist auf *Benutzerdefinierter Ursprung* festgelegt, und im Feld **Hostname des Ursprungs** wird der Endpunkt Ihrer statischen Website angezeigt.

    ![Screenshot der Ursprungseinstellungen für den CDN-Endpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Entfernen von Inhalten aus Azure CDN

Wenn ein Objekt nicht mehr in Azure CDN zwischengespeichert werden soll, können Sie einen der folgenden Schritte ausführen:

* Legen Sie den öffentlichen Container als privat fest. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](./anonymous-read-access-configure.md).
* Deaktivieren oder löschen Sie den CDN-Endpunkt mit dem Azure-Portal.
* Ändern Sie den gehosteten Dienst so, dass er nicht mehr auf Anforderungen für das Objekt antwortet.

Ein bereits in Azure CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die Lebensdauer des Objekts abgelaufen ist oder der Endpunkt [gelöscht](../../cdn/cdn-purge-endpoint.md) wird. Wenn die Lebensdauer abläuft, bestimmt Azure CDN, ob der CDN-Endpunkt weiterhin gültig ist und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall, wird das Objekt nicht mehr zwischengespeichert.

## <a name="next-steps"></a>Nächste Schritte

(Optional) Fügen Sie dem Azure CDN-Endpunkt eine benutzerdefinierte Domäne hinzu. Siehe [Tutorial: Hinzufügen einer benutzerdefinierten Domäne zum Azure CDN-Endpunkt](../../cdn/cdn-map-content-to-custom-domain.md).