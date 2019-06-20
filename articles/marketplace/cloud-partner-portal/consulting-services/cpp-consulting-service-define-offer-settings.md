---
title: Definieren der Angebotseinstellungen für ein Beratungsdienstangebot | Azure Marketplace
description: Definieren Sie Angebotseinstellungen eines Azure- oder Dynamics 365-Beratungsdienstangebots im Cloud-Partnerportal für den Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 601ad62bddd1373742b0cab5a388a55cfd52f4bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942788"
---
# <a name="offer-settings-tab"></a>Registerkarte „Angebotseinstellungen“

Der erste Schritt unter **Neues Angebot** besteht darin, die Angebots-ID zu erstellen. Eine Angebots-ID besteht aus drei Teilen: **Angebots-ID**, **Herausgeber-ID** und **Name**. Jeder dieser Teile ist in den nachstehenden Abschnitten beschrieben.

![Erstellen eines neuen Beratungsdienstangebots – Registerkarte „Angebotseinstellungen“](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Angebots-ID*

Dieser Bezeichner ist ein eindeutiger Name, den Sie bei der ersten Übermittlung des Angebots erstellen. Dieser Name darf nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche enthalten. Die **Angebots-ID** ist in der URL sichtbar und wirkt sich auf Suchmaschinenergebnisse aus. Ein Beispiel ist *yourcompanyname_exampleservice*.

Wie im Beispiel gezeigt, wird die **Angebots-ID** Ihrer Herausgeber-ID angefügt, um einen eindeutigen Bezeichner zu erstellen. Dieser eindeutige Bezeichner wird als permanenter Link verfügbar gemacht, der abonniert werden kann und von Suchmaschinen indiziert wird.

>[!Note]
>Nachdem ein Angebot live geschaltet wurde, kann der Bezeichner nicht mehr aktualisiert werden.


### <a name="publisher-id"></a>Herausgeber-ID*

Dieser Bezeichner bezieht sich auf Ihr Konto. Wenn Sie sich mit Ihrem Organisationskonto angemeldet haben, wird Ihre **Herausgeber-ID** im Dropdownmenü angezeigt.


### <a name="name"></a>Name*

Diese Zeichenfolge wird als Angebotsname in AppSource oder Azure Marketplace angezeigt. Das Feld **Name** ist auf 50 Zeichen begrenzt. Der Prüfer muss Ihren Titel ggf. bearbeiten, um die Dauer und den Angebotstyp Ihrem Angebotsnamen anzufügen.

Das folgende Beispiel zeigt, wie der Angebotsname aufgebaut ist. 

![Erstellen eines neuen Beratungsdienstangebots](media/cppsampleconsultingoffer.png)

Ein Angebotsname besteht aus vier Teilen:

-   **Dauer:** Die Dauer wird im Editor auf der Registerkarte **Details der digitalen Ladenzeile** definiert. Eine Dauer kann in Stunden, Tagen oder Wochen ausgedrückt werden.
-   **Diensttyp**: Die Dauer wird im Editor auf der Registerkarte **Details der digitalen Ladenzeile** definiert. Die Diensttypen lauten `Assessment` (Bewertung), `Briefing` (Einweisung), `Implementation` (Implementierung), `Proof of concept` und `Workshop`.
-   **Präposition**: Die Präposition wird vom Prüfer eingefügt.
-   **Name:** Der Name wird auf der Seite **Angebotseinstellungen** definiert.

>[!Note]
>Das Feld **Name** ist auf 50 Zeichen begrenzt. Der Prüfer muss Ihren Titel ggf. bearbeiten, um die Dauer und den Angebotstyp Ihrem Angebotsnamen anzufügen.

In der folgenden Liste sind einige ordnungsgemäß benannte Angebote aufgeführt:

-   Das Wichtigste für professionelle Dienstleistungen: 1-Std. Einweisung
-   Plattform für Cloudmigration: 1-Std. Einweisung
-   PowerApps und Microsoft Flow: 1-Tag Workshop
-   Azure Machine Learning Services: 3-Wo PoC
-   Einzelhandelslösung für Ladengeschäft und Onlineshop: Einstündige Einweisung
-   Aufbereitung Ihrer Daten: 1-Wo Workshop
-   Cloud-Analysen: 3-Tag Workshop
-   Power BI-Schulung: 3-Tag Workshop
-   Vertriebsmanagementlösung: 1-Wo Implementierung
-   CRM-Schnellstart: 1-Tag Workshop
-   Dynamics 365 for Sales: 2-Tag Bewertung

Speichern Sie nach dem Ausfüllen der Registerkarte **Angebotseinstellungen** Ihre Übermittlung. Der Angebotsname wird jetzt über dem Editor angezeigt, und Sie finden ihn in **Alle Angebote**.

## <a name="next-steps"></a>Nächste Schritte

Jetzt können Sie [Details der digitalen Ladenzeile eingeben und festlegen, ob in Azure Marketplace oder in AppSource veröffentlicht werden soll](./cpp-consulting-service-storefront-details.md).
