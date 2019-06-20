---
title: Ausfüllen des Formulars „Technische Informationen“
description: Erfahren Sie, wie Sie die Werte im Formular „Technische Informationen“ für eine neue Dynamics 365 Business Central-App eingeben.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dbc38fab5bd8e55f6dd280ecc46af1b1a5ae7ede
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935062"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Ausfüllen des Formulars „Technische Informationen“
===========================================

1.  Laden Sie im Abschnitt **Anwendungstyp auswählen** Ihre Erweiterungspaketdatei (.app) sowie alle weiteren Erweiterungspaketdateien, von denen Ihre Erweiterung möglicherweise abhängt, hoch.

    ![Informationen zum Anwendungspaket](./media/d365-financials/image015.png)

-   **Extensions Package File** (Erweiterungspaketdatei): erforderlich – die Erweiterungspaketdatei (.app).

-   **Dependency package file** (Paketdatei für Abhängigkeiten): erforderlich, wenn die App eine Abhängigkeit von einer anderen App, die in AppSource veröffentlicht wurde, aufweist. Diese ist die APP-Datei einer bereits veröffentlichten Erweiterung in AppSource, von der die aktuelle App abhängig ist. 

-   **Library Package File** (Paketdatei für Bibliothek): erforderlich, wenn die App eine Abhängigkeit zu einer anderen App, die *nicht* in AppSource veröffentlicht wurde, aufweist. Dies ist die APP-Datei einer vorhandenen App, die jedoch nicht in AppSource veröffentlicht wird.

-   **App Test Automation** (App-Testautomatisierung): erforderlich – das VS-codierte Testpaket, das Sie für automatisierte Tests von Erweiterungen erstellen müssen.

1. Laden Sie im Abschnitt **Additional Information for the extension** (Zusätzliche Informationen zur Erweiterung) weitere Informationen zu Ihrer Erweiterung hoch. Diese Informationen werden während der Validierung verwendet.

   ![Formular für zusätzliche Informationen zur App-Erweiterung](./media/d365-financials/image016.png)


-   **URL to Product Documentation** (URL in der Produktdokumentation): erforderlich – URL zur Dokumentation für die Erweiterung.

-   **Key Usage Scenarios** (Wichtige Verwendungsszenarien): erforderlich – ein Dokument, das die Einrichtung und Verwendung der Erweiterung ausführlich beschreibt. Ein Beispiel finden Sie im Artikel [User Scenario Documentation](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/) (Dokumentation eines Benutzerszenarios).

-   **Target Release** (Zielversion): erforderlich – wählen Sie die Version, mit der die App bereitgestellt wird, aus. Wählen Sie **current** (aktuell) aus, um die aktuelle Version im Marketplace bereitzustellen. Wählen Sie **next minor** (nächste Unterversion) aus, um die Bereitstellung mit der nächsten Unterversion auszuführen. Wählen Sie **next major** (nächste Hauptversion) aus, um die Bereitstellung mit der nächsten Hauptversion auszuführen.

-   **Requires Premium SKU** (erfordert Premium-SKU): optional – verwenden Sie die Premium-Schaltfläche, wenn die App die Premium-SKU erfordert. Dienstverwaltung und Produktion sind nur mit Premium verfügbar. Ausführliche Informationen zu Essential und Premium finden Sie im Artikel [Sie können auswählen, welche Funktionen angezeigt werden](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Explanation for Code Analysis Errors** (Erläuterung für Codeanalysefehler): optional – Dokument, in dem Code aufgelistet wird, der die Anforderungen nicht erfüllt, mit zugehöriger Erläuterung.

-   **Explanation of Impacted Core Functionality** (Erläuterung der betroffenen Kernfunktionen): optional – Dokument, in dem alle Kernfunktionen, die von der Erweiterung beeinträchtigt werden, aufgeführt und erläutert werden.

-   **Test Accounts** (Testkonten): optional – Benutzerkonten für Remotedienste, Websites usw., die für die End-to-End-Überprüfung der Verwendung benötigt werden.

-   **UX requirements exceptions** (Ausnahmen bei den Anforderungen an die Benutzererfahrung): optional – Dokument, in dem alle Anforderungen an die Benutzererfahrung, die von der Erweiterung nicht erfüllt werden, aufgelistet und erläutert werden.

Als Nächstes fügen Sie Storefrontdetails für Ihr Angebot hinzu.
