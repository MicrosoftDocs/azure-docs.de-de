---
title: 'Azure AD: Verwalten des Zugriffs Ihrer Organisation auf Apps und Gruppen'
description: Erfahren Sie, wie Sie eine Zugriffsüberprüfung durchführen, um den Sicherheitszugriff für Apps und Gruppen Ihrer Organisation über das Portal „Meine Apps“ zu verwalten.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/19/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 8da9e97aaa2c6d99990df4221d8b6e99ab18ac83
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124811"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Durchführen einer Zugriffsüberprüfung im Portal „Meine Apps“

Sie können Ihr Geschäfts-, Schul- oder Unikonto mit dem webbasierten Portal **Meine Apps** für folgende Aufgaben verwenden, um Zugriffsüberprüfungen für Ihre Apps und Gruppen durchzuführen. Zugriffsüberprüfungen helfen Ihnen, veraltete oder sich ändernde Zugriffsanforderungen zu verwalten und sicherzustellen, dass diese überprüft und aktualisiert werden.

Sollten Sie keinen Zugriff auf das Portal **Meine Apps** haben, wenden Sie sich an Ihren Helpdesk.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Dieser Inhalt richtet sich an Benutzer von **Meine Apps**. Administratoren können sich in der [Dokumentation zur Anwendungsverwaltung](../manage-apps/index.yml) über die Einrichtung und Verwaltung cloudbasierter Apps informieren.

## <a name="manage-access-reviews"></a>Verwalten von Zugriffsüberprüfungen

Wenn Ihr Administrator Ihnen die Berechtigung zum Durchführen eigener Zugriffsüberprüfungen erteilt hat, können Sie Ihre Gruppen oder Apps verwalten. Dazu wählen Sie im Portal „Meine Apps“ auf der Seite **Apps** die Kachel **Zugriffsüberprüfungen** aus.

>[!Note]
>Wenn die Kachel **Zugriffsüberprüfungen** nicht angezeigt wird, bedeutet dies, dass Sie nicht über die Berechtigung zum Durchführen von Zugriffsüberprüfungen verfügen oder dass keine ausstehenden Überprüfungen auf Ihre Genehmigung warten. Wenn Sie der Meinung sind, dass Sie Zugriff auf die Kachel haben müssten, wenden Sie sich an den Helpdesk, um Unterstützung zu erhalten.

## <a name="to-perform-your-access-reviews"></a>So führen Sie Zugriffsüberprüfungen durch

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an.

2. Öffnen Sie Ihren Webbrowser, und navigieren Sie zu https://myapps.microsoft.com, oder verwenden Sie den von Ihrer Organisation bereitgestellten Link. So können Sie beispielsweise zu einer benutzerdefinierten Seite Ihrer Organisation wie https://myapps.microsoft.com/contoso.com geleitet werden.

    Die Seite **Apps** wird mit allen im Besitz Ihrer Organisation befindlichen Cloud-Apps angezeigt, die Ihnen zur Verfügung stehen.

    ![Seite „Apps“ im Portal „Meine Apps“](media/my-apps-portal/my-apps-home.png)

3. Wählen Sie die Kachel **Zugriffsüberprüfungen** aus, um eine Liste der Zugriffsüberprüfungen anzuzeigen, die auf Ihre Genehmigung warten.

    ![Seite „Zugriffsüberprüfungen“ mit ausstehenden Zugriffsüberprüfungen für die Organisation](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Wählen Sie **Überprüfung starten** aus, um Ihre Zugriffsüberprüfung zu starten.

5. Überprüfen Sie den Zugriff, und entscheiden Sie, ob dieser weiterhin benötigt wird.

    ![Seite „Zugriffsüberprüfung“ mit den Details zur Überprüfung](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Wenn Sie Administrator sind und den Zugriff Ihrer Organisation auf Gruppen und Apps überprüfen können, wird eine andere Seite angezeigt. Weitere Informationen zum Überprüfen des Zugriffs auf Gruppen oder Apps für Ihre Organisation finden Sie unter [Prüfen des Zugriffs auf Gruppen und Anwendungen mit Azure AD-Zugriffsüberprüfungen](../governance/perform-access-review.md).

6. Wählen Sie **Ja** aus, um den Zugriff zu behalten, oder wählen Sie **Nein** aus, um Ihren Zugriff zu entfernen.

    Wenn Sie **Ja** auswählen, müssen Sie möglicherweise im Feld **Grund** eine Begründung angeben.

    ![Seite „Zugriffsüberprüfung“ mit dem Feld „Grund“, das Beispieltext enthält](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Klicken Sie auf **Submit** (Senden).

    Ihre Zugriffsüberprüfung ist abgeschlossen, und Sie kehren zum Portal **Meine Apps** zurück.

    >[!Note]
    >Sie können Ihren Zugriff bis zum Ende des Zeitraums für die Zugriffsüberprüfung jederzeit ändern. Wenn Sie den Zugriff auf eine App oder Gruppe entfernen, wird er nicht sofort entfernt. Das Entfernen erfolgt, wenn der Zeitraum für die Zugriffsüberprüfung endet oder wenn ein Administrator die Überprüfung beendet.

## <a name="next-steps"></a>Nächste Schritte

- [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](my-apps-portal-end-user-access.md)
- [Ändern Ihrer Profilinformationen](./my-account-portal-settings.md)
- [Anzeigen und Aktualisieren gruppenbezogener Informationen](my-apps-portal-end-user-groups.md)