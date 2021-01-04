---
title: 'Tutorial: Zugreifen auf und Anpassen des Entwicklerportals – Azure API Management | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie das API Management-Entwicklerportal anpassen. Hierbei handelt es sich um eine automatisch generierte und vollständig anpassbare Website mit der Dokumentation Ihrer APIs.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012975"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Tutorial: Zugreifen auf und Anpassen des Entwicklerportals

Das *Entwicklerportal* ist eine automatisch generierte und vollständig anpassbare Website mit der Dokumentation Ihrer APIs. Dort können API-Consumer Ihre APIs entdecken, erfahren, wie diese verwendet werden, und Zugriff anfordern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Zugreifen auf die verwaltete Version des Entwicklerportals
> * Navigieren durch die Administratoroberfläche
> * Anpassen des Inhalts
> * Veröffentlichen der Änderungen
> * Anzeigen des veröffentlichten Portals

Weitere Informationen zum Entwicklerportal finden Sie in der [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management-Entwicklerportal: Administratormodus" border="false":::

## <a name="prerequisites"></a>Voraussetzungen

- Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
- Importieren und veröffentlichen Sie eine Azure API Management-Instanz. Weitere Informationen finden Sie unter [Importieren und Veröffentlichen](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Zugreifen auf das Portal als Administrator

Führen Sie die folgenden Schritte aus, um auf die verwaltete Version des Portals zuzugreifen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie in der oberen Navigationsleiste die Schaltfläche **Entwicklerportal** aus. Eine neue Browserregisterkarte mit einer administrativen Version des Portals wird geöffnet.

## <a name="understand-the-portals-administrative-interface"></a>Grundlegendes zur Administratoroberfläche des Portals

### <a name="default-content"></a>Standardinhalt 

Wenn Sie zum ersten Mal auf das Portal zugreifen, wird der Standardinhalt automatisch im Hintergrund bereitgestellt. Der Standardinhalt ist dafür konzipiert, die Funktionen des Portals zu veranschaulichen und den Anpassungsaufwand zu minimieren, der für die Personalisierung des Portals erforderlich ist. Weitere Informationen dazu, was im Portal enthalten ist, finden Sie in der [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Visueller Editor

Sie können den Inhalt des Portals mithilfe des visuellen Editors anpassen. 
* Über die Menüabschnitte auf der linken Seite können Sie Seiten, Medien, Layouts, Menüs, Formatvorlagen oder Websiteeinstellungen erstellen und ändern. 
* Mit den Menüelementen im unteren Bereich können Sie zwischen Viewports wechseln (z. B. mobil und Desktop), die Elemente des Portals anzeigen, die für authentifizierte oder anonyme Benutzer sichtbar sind, und Aktionen speichern oder rückgängig machen.
* Fügen Sie einer Seite Zeilen hinzu, indem Sie auf ein blaues Symbol mit einem Pluszeichen klicken. 
* Widgets (z. B. Text, Bilder oder API-Listen) können durch Klicken auf ein graues Symbol mit Pluszeichen hinzugefügt werden.
* Ordnen Sie die Elemente auf einer Seite per Drag & Drop neu an. 

### <a name="layouts-and-pages"></a>Layouts und Seiten

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Seiten und Layouts" border="false":::

Layouts definieren, wie Seiten angezeigt werden. Im Standardinhalt gibt es beispielsweise zwei Layouts: eines für die Startseite und das andere für alle anderen Seiten.

Ein Layout wird auf eine Seite angewendet, indem die zugehörige URL-Vorlage mit der URL der Seite abgeglichen wird. Beispielsweise wird ein Layout mit einer URL-Vorlage vom Typ `/wiki/*` auf jede Seite mit dem Segment `/wiki/` angewendet: `/wiki/getting-started`, `/wiki/styles` usw.

In der obigen Abbildung ist zum Layout gehöriger Inhalt blau markiert und die Seite selbst rot umrandet. Die Menüabschnitte sind entsprechend gekennzeichnet.

### <a name="styling-guide"></a>Gestaltungsvorlagen

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Gestaltungsvorlagen" border="false":::

Gestaltungsvorlagen sind ein Panel, das speziell für Designer erstellt wurde. Hier können alle visuellen Elemente in Ihrem Portal angezeigt und gestaltet werden. Die Gestaltung erfolgt hierarchisch: Viele Elemente erben Eigenschaften von anderen Elementen. Ein Beispiel: Schaltflächenelemente verwenden Farben für Text und Hintergrund. Um die Farbe einer Schaltfläche zu ändern, müssen Sie die ursprüngliche Farbvariante ändern.

Um eine Variante zu bearbeiten, wählen Sie die gewünschte Variante und dann das angezeigte Stiftsymbol aus. Schließen Sie das Popupfenster, nachdem Sie darin die gewünschten Änderungen vorgenommen haben.

### <a name="save-button"></a>Schaltfläche „Speichern“

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Schaltfläche „Speichern“" border="false":::

Wenn Sie eine Änderung im Portal vorgenommen haben, müssen Sie diese manuell speichern, indem Sie im Menü unten die Schaltfläche **Speichern** auswählen oder STRG+S drücken. Nachdem Sie die Änderungen gespeichert haben, wird der geänderte Inhalt automatisch in Ihren API Management-Dienst hochgeladen.

## <a name="customize-the-portals-content"></a>Anpassen des Portalinhalts

Bevor Sie Ihr Portal Besuchern zur Verfügung stellen, sollten Sie den automatisch generierten Inhalt personalisieren. Empfohlen werden Änderungen an Layouts, Formatvorlagen und dem Inhalt der Startseite.

> [!NOTE]
> Aufgrund von Überlegungen in Bezug auf die Integration können die folgenden Seiten nicht entfernt oder zu einer anderen URL verschoben werden: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Startseite

Die Standard-**Startseite** ist mit Platzhalterinhalt gefüllt. Sie können entweder ganze Abschnitte mit diesem Inhalt entfernen oder die Struktur beibehalten und die Elemente einzeln anpassen. Ersetzen Sie die generierten Texte und Bilder durch eigene, und stellen Sie sicher, dass die Links auf die gewünschte Speicherorte zeigen.

### <a name="layouts"></a>Layouts

Ersetzen Sie das automatisch generierte Logo in der Navigationsleiste durch ein eigenes Bild.

### <a name="styling"></a>Gestaltung

Sie müssen zwar keine Formatvorlagen ändern, aber möglicherweise möchten Sie bestimmte Elemente anpassen. Ändern Sie beispielsweise die primäre Farbe in die Farbe Ihrer Marke.

### <a name="customization-example"></a>Anpassungsbeispiel

Im folgenden Video demonstrieren wir, wie Sie den Inhalt des Portals bearbeiten, das Aussehen der Website anpassen und die Änderungen veröffentlichen.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Veröffentlichen des Portals

Um Ihr Portal mit den neuesten Änderungen für Besucher zur Verfügung zu stellen, müssen Sie es *veröffentlichen*. Sie können das Portal über die Verwaltungsschnittstelle des Portals oder über das Azure-Portal veröffentlichen.

### <a name="publish-from-the-administrative-interface"></a>Veröffentlichen über die Verwaltungsschnittstelle

1. Stellen Sie sicher, dass Ihre Änderungen gespeichert wurden, indem Sie das Symbol **Speichern** auswählen.
1. Wählen Sie im Menüabschnitt **Vorgänge** die Option **Website veröffentlichen** aus. Dieser Vorgang kann einige Minuten dauern.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portal veröffentlichen" border="false":::

### <a name="publish-from-the-azure-portal"></a>Veröffentlichen über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie im Menü auf der linken Seite unter **Entwicklerportal** die Option **Portalübersicht** aus.
1. Wählen Sie im Fenster **Portalübersicht** die Option **Veröffentlichen** aus.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Veröffentlichen des Portals über das Azure-Portal":::

> [!NOTE]
> Das Portal muss erneut veröffentlicht werden, nachdem sich die Konfiguration des API Management-Diensts geändert hat. Führen Sie beispielsweise eine erneute Veröffentlichung des Portals durch, nachdem eine benutzerdefinierte Domäne zugewiesen wurde, die Identitätsanbieter aktualisiert wurden, die Delegierung festgelegt wurde oder Bedingungen für die Anmeldung und für Produkte angegeben wurden.


## <a name="visit-the-published-portal"></a>Besuchen des veröffentlichten Portals

Nachdem Sie das Portal veröffentlicht haben, können Sie über die gleiche URL wie für das Verwaltungspanel darauf zugreifen, z. B. über `https://contoso-api.developer.azure-api.net`. Sie können es in einer separaten Browsersitzung (Inkognito oder privater Modus) als externer Besucher anzeigen.

## <a name="apply-the-cors-policy-on-apis"></a>Anwenden der CORS-Richtlinie auf APIs

Aktivieren Sie CORS (Cross-Origin Resource Sharing) für Ihre APIs, damit die Besucher Ihres Portals die APIs über die integrierte interaktive Konsole testen können. Weitere Informationen finden Sie unter [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
- [Migrieren zum neuen Entwicklerportal](developer-portal-deprecated-migration.md) aus dem veralteten Legacyportal