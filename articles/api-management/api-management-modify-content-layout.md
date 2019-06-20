---
title: Ändern von Seiteninhalten im Entwicklerportal in Azure API Management | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Seiteninhalte im Entwicklerportal in Azure API Management bearbeiten.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: fd9902a83a813daffc52e089e6e8bfc4137c5b8f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656632"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Ändern des Inhalts und Layouts von Seiten im Entwicklerportal in Azure API Management
Es gibt drei grundlegende Möglichkeiten, das Entwicklerportal in Azure API Management anzupassen:

* [Bearbeiten des Inhalts von statischen Seiten und Seitenlayoutelementen][modify-content-layout] (in diesem Leitfaden beschrieben)
* [Aktualisieren der Stile, die für Seitenelemente im gesamten Entwicklerportal verwendet werden][customize-styles]
* [Ändern der Vorlagen, die für vom Portal generierte Seiten verwendet werden][portal-templates] (beispielsweise API-Dokumente, Produkte, Benutzerauthentifizierung usw.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="page-structure"></a>Struktur von Seiten im Entwicklerportal

Das Entwicklerportal basiert auf einem Content Management-System. Das Layout jeder Seite wird basierend auf einer Gruppe von kleinen Seitenelementen erstellt, die als „Widgets“ bezeichnet werden:

![Seitenstruktur im Entwicklerportal][api-management-customization-widget-structure]

Alle Widgets können bearbeitet werden.
* Die Kerninhalte für jede einzelne Seite befinden sich im Widget „Contents“ (Inhalt). Das Bearbeiten einer Seite ist also gleichbedeutend mit dem Bearbeiten des Inhalts dieses Widgets.
* Alle Seitenlayoutelemente sind in den verbleibenden Widgets enthalten. An diesen Widgets vorgenommene Änderungen werden auf alle Seiten angewendet. Sie werden als „Layoutwidgets“ bezeichnet.

Bei der alltäglichen Bearbeitung von Seiten wird häufig nur das Inhaltswidget geändert, das unterschiedliche Inhalte für die einzelnen Seiten enthält.

## <a name="modify-layout-widget"></a>Ändern des Inhalts eines Layoutwidgets

Das Entwicklerportal kann über das Azure-Portal aufgerufen werden.

1. Klicken Sie auf der Symbolleiste Ihrer API Management-Instanz auf **Entwicklerportal**.
2. Klicken Sie auf der linken Seite im Menü des **Entwicklerportals** auf das Symbol mit den beiden Pinseln, um den Inhalt von Widgets zu ändern.
3. Scrollen Sie zum Ändern des Headerinhalts in der Liste auf der linken Seite zum Abschnitt **Header**.

    Die Widgets können über die Felder bearbeitet werden.
4. Klicken Sie am unteren Rand der Seite auf **Veröffentlichen**, wenn Sie zum Veröffentlichen Ihrer Änderungen bereit sind.

Die neue Kopfzeile wird jetzt auf allen Seiten im Entwicklerportal angezeigt.

## <a name="next-steps"> </a>Nächste Schritte
* [Aktualisieren der Stile, die für Seitenelemente im gesamten Entwicklerportal verwendet werden][customize-styles]
* [Ändern der Vorlagen, die für vom Portal generierte Seiten verwendet werden][portal-templates] (beispielsweise API-Dokumente, Produkte, Benutzerauthentifizierung usw.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
