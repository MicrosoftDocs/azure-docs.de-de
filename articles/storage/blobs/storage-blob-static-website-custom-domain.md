---
title: 'Tutorial: Aktivieren einer benutzerdefinierten Domäne mit SSL für eine statische Website mit Azure CDN: Azure Storage'
description: Erfahren Sie, wie Sie eine benutzerdefinierte Domäne für das Hosten von statischen Websites konfigurieren.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226083"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Tutorial: Verwenden von Azure CDN zum Aktivieren einer benutzerdefinierten Domäne mit SSL für eine statische Website

Dieses Tutorial ist der zweite Teil einer Reihe. In diesem Teil erfahren Sie, wie Sie einen benutzerdefinierten Domänenendpunkt mit SSL für Ihre statische Website aktivieren. 

Das Tutorial veranschaulicht, wie Sie mit [Azure CDN](../../cdn/cdn-overview.md) den benutzerdefinierten Domänenendpunkt für Ihre statische Website konfigurieren. Mit Azure CDN können Sie gleichzeitig benutzerdefinierte SSL-Zertifikate bereitstellen, eine benutzerdefinierte Domäne verwenden und benutzerdefinierte Regeln für das erneute Generieren konfigurieren. Durch die Konfiguration von Azure CDN fallen zusätzliche Gebühren an, der Dienst bietet jedoch auf der ganzen Welt durchgängig kurze Wartezeiten für Ihre Website. Azure CDN bietet auch eine SSL-Verschlüsselung mit Ihrem eigenen Zertifikat. Weitere Informationen zu den Preisen von Azure CDN finden Sie unter [Azure Content Delivery Network – Preise](https://azure.microsoft.com/pricing/details/cdn/).

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines CDN-Endpunkts für den Endpunkt der statischen Website
> * Aktivieren der benutzerdefinierten Domäne und von SSL

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie Teil 1 [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md) (Tutorial: Hosten einer statischen Website in Blob Storage) aus, bevor Sie mit diesem Tutorial beginnen. 

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Erstellen eines CDN-Endpunkts für den Endpunkt der statischen Website

1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.
1. Wählen Sie im Menü **Blob-Dienst** die Option **Azure CDN** aus, um Azure CDN zu konfigurieren.
1. Geben Sie im Abschnitt **CDN-Profil** ein neues oder vorhandenes CDN-Profil an. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure CDN-Profils und -Endpunkts](../../cdn/cdn-create-new-endpoint.md).
1. Geben Sie einen Tarif für den CDN-Endpunkt an. In diesem Tutorial wird der Tarif **Standard Akamai** verwendet, da die Verteilung bei diesem Tarif schnell erfolgt (in der Regel innerhalb weniger Minuten). Bei anderen Tarifen dauert die Verteilung möglicherweise länger, sie bieten jedoch u. U. andere Vorteile. Weitere Informationen finden Sie unter [Vergleichen von Azure CDN-Produktfeatures](../../cdn/cdn-features.md).
1. Geben Sie im Feld **Name des CDN-Endpunkts** einen Namen für Ihren CDN-Endpunkt an. Der Name des CDN-Endpunkts muss innerhalb von Azure eindeutig sein.
1. Geben Sie im Feld **Hostname des Ursprungs** den Endpunkt Ihrer statischen Website an. Navigieren Sie zum Ermitteln des Endpunkts Ihrer statischen Website zum Abschnitt **Statische Website** Ihres Speicherkontos. Kopieren Sie den primären Endpunkt, fügen Sie ihn in die CDN-Konfiguration ein, und entfernen Sie dabei die Protokoll-ID (*z. B.* HTTPS).

    Die folgende Abbildung zeigt eine Beispiel-Endpunktkonfiguration:

    ![Screenshot einer Beispiel-CDN-Endpunktkonfiguration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Erstellen Sie den CDN-Endpunkt, und warten Sie, bis er verteilt wird.
1. Überprüfen Sie, ob der CDN-Endpunkt korrekt konfiguriert ist. Klicken Sie dazu auf den Endpunkt, um die Einstellungen anzuzeigen. Suchen Sie wie in der folgenden Abbildung dargestellt in der CDN-Übersicht für Ihr Speicherkonto nach dem Endpunkthostnamen, und navigieren Sie zum Endpunkt. Das Format Ihres CDN-Endpunkts sieht in etwa wie folgt aus: `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot mit der Übersicht des CDN-Endpunkts](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Wenn Sie nach Abschluss der Verteilung zum CDN-Endpunkt navigieren, wird der Inhalt der Datei „index.html“ angezeigt, die Sie zuvor in Ihre statische Website hochgeladen haben.

1. Navigieren Sie zum Überprüfen der Ursprungseinstellungen Ihres CDN-Endpunkts im Abschnitt **Einstellungen** für den CDN-Endpunkt zu **Ursprung**. Das Feld **Ursprungstyp** ist auf *Benutzerdefinierter Ursprung* festgelegt, und im Feld **Hostname des Ursprungs** wird der Endpunkt Ihrer statischen Website angezeigt.

    ![Screenshot der Ursprungseinstellungen für den CDN-Endpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aktivieren der benutzerdefinierten Domäne und von SSL

1. Erstellen Sie einen CNAME-Eintrag beim Anbieter Ihres Domänennamens, um Ihre benutzerdefinierte Domäne zum CDN-Endpunkt umzuleiten. Der CNAME-Eintrag für die *www*-Unterdomäne sollte in etwa wie folgt aussehen:

    ![Angeben des CNAME-Eintrags für die www-Unterdomäne](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Zeigen Sie im Azure-Portal die Einstellungen für Ihren CDN-Endpunkt an. Navigieren Sie unter **Einstellungen** zu **Benutzerdefinierte Domänen**, um die benutzerdefinierte Domäne und das SSL-Zertifikat zu konfigurieren.
1. Wählen Sie **Benutzerdefinierte Domäne hinzufügen** aus, geben Sie Ihren Domänennamen ein, und klicken Sie dann auf **Hinzufügen**.
1. Wählen Sie die neue benutzerdefinierte Domänenzuordnung aus, um ein SSL-Zertifikat bereitzustellen.
1. Legen Sie **HTTPS für benutzerdefinierte Domänen** auf **EIN** fest, und klicken Sie dann auf **Speichern**. Die Konfiguration Ihrer benutzerdefinierten Domäne nimmt mehrere Stunden in Anspruch. Der Status wird wie in der folgenden Abbildung dargestellt im Portal angezeigt.

    ![Screenshot der Anzeige des Status der Konfiguration der benutzerdefinierten Domäne](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Testen Sie die Zuordnung zwischen Ihrer statischen Website und Ihrer benutzerdefinierten Domäne, indem Sie auf die URL für Ihre benutzerdefinierte Domäne zugreifen.

Weitere Informationen zum Aktivieren von HTTPS für benutzerdefinierte Domänen finden Sie unter [Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil dieses Tutorials haben Sie erfahren, wie Sie eine benutzerdefinierte Domäne mit SSL in Azure CDN für Ihre statische Website konfigurieren.

Weitere Informationen zum Konfigurieren und Verwenden von Azure CDN finden Sie unter [Was ist ein Content Delivery Network in Azure?](../../cdn/cdn-overview.md).