---
title: Überprüfen Ihres Zugriffs auf Gruppen und Apps mit Zugriffsüberprüfungen – Azure AD
description: Erfahren Sie, wie Sie Ihren eigenen Zugriff auf Gruppen oder Anwendungen mit Azure Active Directory-Zugriffsüberprüfungen überprüfen.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959837ff540fd95d186497858d7b9de1f8b1124d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274009"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen mit Azure AD-Zugriffsüberprüfungen

Azure Active Directory (Azure AD) erleichtert Unternehmen das Verwalten des Zugriffs auf Gruppen oder Anwendungen in Azure AD und anderen Microsoft-Onlinediensten durch eine Funktion namens „Azure AD-Zugriffsüberprüfungen“.

In diesem Artikel wird beschrieben, wie Sie Ihren eigenen Zugriff auf eine Gruppe oder Anwendung überprüfen.

## <a name="review-your-access-using-my-apps"></a>Überprüfen des Zugriffs mithilfe von „Meine Apps“

Bei einer Zugriffsüberprüfung müssen Sie zuerst die Zugriffsüberprüfung suchen und öffnen.

>[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachfolgend ist eine Beispiel-E-Mail zum Überprüfen Ihres Zugriffs auf eine Gruppe abgebildet.

    ![Beispiel-E-Mail von Microsoft zum Überprüfen Ihres Zugriffs auf eine Gruppe](./media/review-your-access/access-review-email.png)

1. Klicken Sie auf den Link **Zugriff überprüfen**, um die Zugriffsüberprüfung zu öffnen.

Wenn Sie keine E-Mail haben, können Sie Ihre ausstehenden Zugriffsüberprüfungen mit folgenden Schritten anzeigen.

1. Melden Sie sich auf [https://myapps.microsoft.com](https://myapps.microsoft.com) im Portal „Meine Apps“ an.

    ![Portal „Meine Apps“ mit einer Auflistung der Apps, für die Sie Berechtigungen besitzen](./media/review-your-access/myapps-access-panel.png)

1. Klicken Sie oben rechts auf der Seite auf das Benutzersymbol, neben dem Ihr Name und Ihre Standardorganisation angezeigt werden. Werden mehrere Organisationen aufgelistet, wählen Sie die Organisation aus, die eine Zugriffsüberprüfung angefordert hat.

1. Klicken Sie rechts auf der Seite auf die Kachel **Zugriffsüberprüfungen**, um eine Liste mit den ausstehenden Zugriffsüberprüfungen anzuzeigen.

    Wenn die Kachel nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

    ![Liste mit ausstehenden Zugriffsüberprüfungen für Ihre Apps und Gruppen](./media/review-your-access/access-reviews-list.png)

1. Klicken Sie bei der Zugriffsüberprüfung, die Sie durchführen möchten, auf den Link **Überprüfung starten**.

### <a name="perform-the-access-review"></a>Durchführen der Zugriffsüberprüfung

Nachdem Sie die Zugriffsüberprüfung geöffnet haben, wird Ihr Zugriff angezeigt.

1. Überprüfen Sie Ihren Zugriff, und entscheiden Sie, ob Sie weiterhin Zugriff benötigen.

    Wenn Sie aufgefordert werden, den Zugriff für andere Benutzer zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Zugriffsüberprüfung von Gruppen oder Anwendungen](perform-access-review.md).

    ![Screenshot: Offene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen](./media/review-your-access/perform-access-review.png)

1. Klicken Sie auf **Ja**, um den Zugriff zu behalten, oder klicken Sie auf **Nein**, um Ihren Zugriff zu entfernen.

1. Wenn Sie auf **Ja** klicken, müssen Sie möglicherweise im Feld **Grund** eine Begründung angeben.

    ![Screenshot: Abgeschlossene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen, und in der „Ja“ ausgewählt ist](./media/review-your-access/perform-access-review-submit.png)

1. Klicken Sie auf **Submit**(Senden).

    Ihre Auswahl wird übermittelt, und Sie gelangen zurück zum Portal „Meine Apps“.

    Wenn Sie Ihre Antwort ändern möchten, öffnen Sie erneut die Seite „Zugriffsüberprüfungen“, und aktualisieren Sie Ihre Antwort. Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern.

    > [!NOTE]
    > Wenn Sie angegeben haben, dass Sie keinen Zugriff mehr benötigen, werden Sie nicht sofort entfernt. Sie werden nach Ende der Überprüfung (oder wenn ein Administrator die Überprüfung beendet) entfernt.

## <a name="review-your-own-access-using-my-access-new"></a>Überprüfen des eigenen Zugriffs mithilfe von „Mein Zugriff“ (neu)

Sie haben verschiedene Möglichkeiten, die neue Umgebung mit der aktualisierten Benutzeroberfläche unter „Mein Zugriff“ auszuprobieren:

### <a name="my-apps-portal"></a>Portal „Meine Apps“

1. Melden Sie sich auf [https://myapps.microsoft.com](https://myapps.microsoft.com) im Portal „Meine Apps“ an.

    ![Portal „Meine Apps“ mit einer Auflistung der Apps, für die Sie Berechtigungen besitzen](./media/review-your-access/myapps-access-panel.png)

2. Klicken Sie auf die Kachel **Zugriffsüberprüfungen**, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen.

    > [!NOTE]
    > Wenn die Kachel **Zugriffsüberprüfungen** nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

3. Klicken Sie auf **Testen**. Diese Schaltfläche befindet sich im Banner am oberen Rand der Seite. Dadurch wechseln Sie zur neuen Umgebung „Mein Zugriff“.

    ![Liste mit ausstehenden Zugriffsüberprüfungen für Apps und Gruppen in der neuen Umgebung über das während der Vorschau angezeigte Banner](./media/review-your-access/banner-your-access.png)

4. Weiter mit dem Abschnitt **Durchführen der Zugriffsüberprüfung**

### <a name="email"></a>Email

>[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachstehend finden Sie ein Beispiel für eine E-Mail-Nachricht:

 ![Beispiel-E-Mail von Microsoft zum Überprüfen des Zugriffs auf eine Gruppe](./media/review-your-access/access-review-email-preview.png)

2. Klicken Sie auf den Link **Zugriff überprüfen**, um die Zugriffsüberprüfung zu öffnen.

3. Weiter mit dem Abschnitt **Durchführen der Zugriffsüberprüfung**

>[!NOTE]
>Wenn Sie auf „Überprüfung starten“ klicken, gelangen Sie zu **Meine Apps**. Führen Sie dann die Schritte aus, die im obigen Abschnitt **Portal „Meine Apps“** aufgeführt sind.

### <a name="directly-at-my-access"></a>Direkt über „Mein Zugriff“

Sie können Ihre ausstehenden Zugriffsüberprüfungen auch anzeigen, indem Sie Ihren Browser zum Öffnen von „Mein Zugriff“ verwenden.

1. Anmelden auf https://myaccess.microsoft.com/ bei „Mein Zugriff“

2. Wählen Sie im Menü auf der linken Navigationsleiste **Zugriffsüberprüfungen** aus, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen, die Ihnen zugewiesen sind.

   ![Zugriffsüberprüfungen im Menü](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Durchführen der Zugriffsüberprüfung

1. Unter „Gruppen und Apps“ wird Folgendes angezeigt:
    
    - **Name**: Der Name der Zugriffsüberprüfung.
    - **Fällig**: Das Fälligkeitsdatum der Überprüfung. Nach diesem Datum können abgelehnte Benutzer aus der zu überprüfenden Gruppe oder App entfernt werden.
    - **Ressource**: Der Name der zu überprüfenden Ressource.
    - **Fortschritt**: Die Anzahl der überprüften Benutzer zur Gesamtzahl der Benutzer im Rahmen dieser Zugriffsüberprüfung.
    
2. Klicken Sie zum Starten des Vorgangs auf den Namen einer Zugriffsüberprüfung.

   ![Liste mit ausstehenden Zugriffsüberprüfungen für Apps und Gruppen](./media/review-your-access/access-reviews-list-preview.png)

3. Überprüfen Sie Ihren Zugriff, und entscheiden Sie, ob Sie weiterhin Zugriff benötigen.

    Wenn Sie aufgefordert werden, den Zugriff für andere Benutzer zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Zugriffsüberprüfung von Gruppen oder Anwendungen](perform-access-review.md).

    ![Offene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen](./media/review-your-access/review-access-preview.png)

1. Wählen Sie **Ja** aus, um Ihren Zugriff beizubehalten, oder wählen Sie **Nein** aus, um Ihren Zugriff zu entfernen.

1. Wenn Sie auf **Ja** klicken, müssen Sie möglicherweise im Feld **Grund** eine Begründung angeben.

    ![Abgeschlossene Zugriffsüberprüfung, in der Sie gefragt werden, ob Sie weiterhin Zugriff auf eine Gruppe benötigen](./media/review-your-access/review-access-yes-preview.png)

1. Klicken Sie auf **Submit**(Senden).

    Ihre Auswahl wird übermittelt, und Sie gelangen wieder zur Seite „Mein Zugriff“.

    Wenn Sie Ihre Antwort ändern möchten, öffnen Sie erneut die Seite „Zugriffsüberprüfungen“, und aktualisieren Sie Ihre Antwort. Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern.

    > [!NOTE]
    > Wenn Sie angegeben haben, dass Sie keinen Zugriff mehr benötigen, werden Sie nicht sofort entfernt. Sie werden nach Ende der Überprüfung (oder wenn ein Administrator die Überprüfung beendet) entfernt.

## <a name="next-steps"></a>Nächste Schritte

- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)
