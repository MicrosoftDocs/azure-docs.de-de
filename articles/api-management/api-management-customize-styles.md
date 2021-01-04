---
title: Anpassen des Seitenstils im API Management-Legacy-Entwicklerportal
titleSuffix: Azure API Management
description: Führen Sie die Schritte in dieser Schnellstartanleitung aus, um den Stil von Elementen im Azure API Management-Entwicklerportal anzupassen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 0cdad245981ad02d3cdaefba447d131c775e242f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145768"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassen des Stils der Seiten im Entwicklerportal

Es gibt drei sehr häufig verwendete Möglichkeiten, das Entwicklerportal in Azure API Management anzupassen:
 
* [Bearbeiten des Inhalts von statischen Seiten und Seitenlayoutelementen](api-management-modify-content-layout.md)
* Aktualisieren der Stile, die für Seitenelemente im gesamten Entwicklerportal verwendet werden (in diesem Leitfaden beschrieben)
* [Ändern der Vorlagen, die für vom Portal generierte Seiten verwendet werden](api-management-developer-portal-templates.md) (z.B. API-Dokumente, Produkte, Benutzerauthentifizierung)

In diesem Artikel erfahren Sie, wie Sie den Stil der Elemente auf den Seiten des Legacy- **Entwicklerportals** anpassen und Ihre Änderungen anzeigen.

![Screenshot, der zeigt, wo Sie Ihre Einstellungen im Legacy-Entwicklerportal ändern.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Absolvieren Sie den folgenden Schnellstart: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md).
+ Schließen Sie darüber hinaus das folgende Tutorial ab: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Anpassen des Entwicklerportals

1. Wählen Sie **Übersicht**.
2. Klicken Sie oben im Fenster **Übersicht** auf die Schaltfläche **Developer portal (legacy)** (Entwicklerportal (Legacy-Version)).
3. Oben links auf dem Bildschirm wird ein aus zwei Pinseln bestehendes Symbol angezeigt. Zeigen Sie auf dieses Symbol, um das Menü für die Portalanpassung zu öffnen.

    ![Screenshot, der das Symbol mit zwei Pinseln hervorhebt.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Klicken Sie im Menü auf **Stile** , um den Bereich für die Anpassung des Stils zu öffnen.

    Alle Elemente, die über **Stile** angepasst werden können, werden auf der Seite angezeigt.
5. Geben Sie im Feld **Change variable values to customize developer portal appearance:** (Variablenwerte ändern, um die Darstellung des Entwicklerportals anzupassen) die Variable „headings-color“ ein.

    Das Element **\@headings-color** wird auf der Seite angezeigt. Diese Variable legt die Farbe des Texts fest.

    ![Anpassen des Stils](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicken Sie auf das Feld für die Variable **\@headings-color**. 
    
    Die Dropdownliste für die Farbauswahl wird geöffnet.
7. Wählen Sie in der Dropdownliste für die Farbauswahl eine neue Farbe aus.

    > [!TIP]
    > Für alle Änderungen ist eine Echtzeitvorschau verfügbar. Oben im Anpassungsbereich wird eine Statusanzeige angezeigt. Nach einigen Sekunden wird die Farbe des Überschriftentexts in die neu ausgewählte Farbe geändert.

8. Klicken Sie unten links im Menü des Anpassungsbereich auf **Veröffentlichen**.
9. Klicken Sie auf **Anpassungen veröffentlichen** , um die Änderungen öffentlich verfügbar zu machen.

## <a name="view-your-change"></a>Anzeigen der Änderungen

1. Navigieren Sie zum Entwicklerportal.
2. Sie können die vorgenommenen Änderung hier sehen.

## <a name="next-steps"></a>Nächste Schritte

Sie sind vielleicht auch daran interessiert, zu erfahren, [wie Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen anpassen können](api-management-developer-portal-templates.md).