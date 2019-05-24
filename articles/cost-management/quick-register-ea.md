---
title: Registrieren Ihres Azure Enterprise Agreements bei Cloudyn | Microsoft-Dokumentation
description: In diesem Schnellstart wird der Registrierungsvorgang ausführlich erläutert, der zum Erstellen eines Cloudyn-Testabonnements und zum Anmelden beim Cloudyn-Portal erforderlich ist.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 95d1292374e1ec408c107245cde4cd1e12270f50
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967210"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrieren eines Azure Enterprise Agreements und Anzeigen von Kostendaten

Sie verwenden Ihr Azure Enterprise Agreement, um sich bei Cloudyn zu registrieren. Durch die Registrierung erhalten Sie Zugriff auf das Cloudyn-Portal. In diesem Schnellstart wird der Registrierungsvorgang ausführlich erläutert, der zum Erstellen eines Cloudyn-Testabonnements und zum Anmelden beim Cloudyn-Portal erforderlich ist. Es wird auch gezeigt, wie die Anzeige von Kostendaten sofort gestartet werden kann.

Azure Cost Management bietet ähnliche Funktionen wie Cloudyn. Azure Cost Management ist eine native Azure-Kostenverwaltungslösung. Die Lösung unterstützt Sie beim Analysieren von Kosten, beim Erstellen und Verwalten von Budgets, beim Exportieren von Daten sowie beim Prüfen von Optimierungsempfehlungen und beim Reagieren auf diese – und damit beim Sparen von Geld. Weitere Informationen finden Sie unter [Azure Cost Management](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

- Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="register-with-cloudyn"></a>Registrieren bei Cloudyn

1. Klicken Sie im Azure-Portal in der Liste der Dienste auf **Kostenverwaltung und Abrechnung**.
2. Klicken Sie unter **Übersicht** auf **Cloudyn**.  
    ![Cloudyn-Seite, die im Azure-Portal angezeigt wird](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Klicken Sie auf der Seite **Cloudyn** auf **Zu Cloudyn wechseln**, um die Cloudyn-Registrierungsseite in einem neuen Fenster zu öffnen.
4. Geben Sie im Cloudyn-Portal auf der Registrierungsseite für die Testversion den Namen Ihres Unternehmens ein, und wählen Sie dann **Azure Enterprise Enrollment Administrator** aus.  
5. Geben Sie Ihren Enterprise Portal-API-Registrierungsschlüssel ein. Wenn Ihr Schlüssel zurzeit nicht griffbereit ist, klicken Sie auf den Link [Enterprise Portal](https://ea.azure.com), und führen Sie dann die folgenden Schritte aus:  
    ![Einfügen Ihres API-Schlüssels auf der Registerkarte „Abrechnung“](./media/quick-register-ea/trial-reg.png)
   1. Melden Sie sich auf der Azure Enterprise-Website an, und klicken Sie auf **Berichte**. Klicken Sie auf **API-Zugriffsschlüssel**, und kopieren Sie dann Ihren Primärschlüssel.  
    ![Beispiel: EA-API-Schlüssel im EA-Portal](./media/quick-register-ea/ea-key.png)
   3. Navigieren Sie zurück zur Registrierungsseite, und fügen Sie Ihren API-Schlüssel ein.
6. Stimmen Sie den Nutzungsbedingungen zu, und überprüfen Sie dann Ihren Schlüssel. Klicken Sie auf **Weiter**, um Cloudyn für das Erfassen von Azure-Ressourcendaten zu autorisieren. Die erfassten Daten beziehen sich auf Nutzungs-, Leistungs- und Abrechnungs- sowie Tagdaten aus Ihren Abonnements.  
    ![Beispiel: Erfolgreiche Überprüfung eines EA-API-Schlüssels](./media/quick-register-ea/ea-key-validated.png)
7. Unter **Invite other stakeholders** (Andere Beteiligte einladen) können Sie Benutzer hinzufügen, indem Sie ihre E-Mail-Adressen eingeben. Klicken Sie auf **Weiter**, wenn Sie fertig sind. Je nach Größe Ihrer Azure-Registrierung kann es bis zu 24 Stunden dauern, bis Ihre gesamten Abrechnungsdaten Cloudyn hinzugefügt wurden.
8. Klicken Sie auf **Zu Cloudyn wechseln**, um das Cloudyn-Portal zu öffnen. Auf der Seite **Cloudkontenverwaltung** sollten Ihre registrierten EA-Kontoinformationen angezeigt werden.

Ein Videotutorial zur Registrierung Ihres Enterprise Agreements finden Sie unter [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Ermitteln Ihrer EA-Registrierungs-ID und Ihres API-Schlüssels zur Verwendung in Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Ihre Azure Enterprise Agreement-Informationen verwendet, um sich bei Cloudyn zu registrieren. Außerdem haben Sie sich beim Cloudyn-Portal angemeldet und die Anzeige von Kostendaten gestartet. Weitere Informationen zu Cloudyn finden Sie im Tutorial für Cloudyn.

> [!div class="nextstepaction"]
> [Überprüfen der Nutzung und der Kosten](./tutorial-review-usage.md)
