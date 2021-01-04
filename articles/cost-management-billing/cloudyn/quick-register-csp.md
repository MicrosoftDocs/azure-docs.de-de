---
title: Registrieren mithilfe der CSP-Partnerinformationen bei Cloudyn in Azure
description: Erfahren Sie mehr über das Registrierungsverfahren, das von Partnern für das Onboarding ihrer Kunden im Cloudyn-Portal verwendet wird.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543320"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrieren beim CSP-Partnerprogramm und Anzeigen von Kostendaten

Als CSP-Partner und registrierter Cloudyn-Benutzer können Sie Ihre Cloudausgaben in Cloudyn anzeigen und analysieren. [Azure Cost Management ist nativ für direkte Partner verfügbar](../costs/get-started-partners.md), die ihre Kunden in eine Microsoft-Kundenvereinbarung aufgenommen und einen Azure-Plan erworben haben.

Durch die Registrierung erhalten Sie Zugriff auf das Cloudyn-Portal. In diesem Schnellstart wird der Registrierungsvorgang ausführlich erläutert, der zum Erstellen eines Cloudyn-Testabonnements und zum Anmelden beim Cloudyn-Portal erforderlich ist.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurieren des indirekten CSP-Zugriffs in Cloudyn

Standardmäßig ist der Zugriff auf Partner Center-APIs nur für direkte CSPs möglich. Allerdings können direkte CSP-Anbieter den Zugriff für ihre indirekten CSP-Kunden oder -Partner über Entitätsgruppen in Cloudyn konfigurieren.

Um den Zugriff für indirekte CSP-Kunden oder -Partner zu aktivieren, führen Sie die folgenden Schritte aus, um indirekte CSP-Daten mithilfe von Cloudyn-Entitätsgruppen zu segmentieren. Weisen Sie anschließend die entsprechenden Benutzerberechtigungen den Entitätsgruppen zu.

1. Erstellen Sie eine Entitätsgruppe mit den Informationen unter [Erstellen von Entitäten](tutorial-user-access.md#create-and-manage-entities).
2. Führen Sie die Schritte unter [Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Zuweisen von Abonnements zu Kostenentitäten) aus. Verknüpfen Sie die Konten der indirekten CSP-Kunden und ihre Azure-Abonnements mit der Entität, die Sie zuvor erstellt haben.
3. Führen Sie die Schritte unter [Erstellen eines Benutzers mit Administratorzugriff](tutorial-user-access.md#create-a-user-with-admin-access) aus, um ein Benutzerkonto mit Administratorzugriff zu erstellen. Stellen Sie außerdem sicher, dass das Benutzerkonto über Administratorzugriff auf die bestimmten Entitäten verfügt, die Sie zuvor für das indirekte Konto erstellt haben.

Indirekte CSP-Partner melden sich beim Cloudyn-Portal unter Verwendung der Konten an, die Sie für sie erstellt haben.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Ihre CSP-Informationen verwendet, um sich bei Cloudyn zu registrieren. Außerdem haben Sie sich beim Cloudyn-Portal angemeldet und die Anzeige von Kostendaten gestartet. Weitere Informationen zu Cloudyn finden Sie im Tutorial für Cloudyn.

> [!div class="nextstepaction"]
> [Überprüfen der Nutzung und der Kosten](tutorial-review-usage.md)