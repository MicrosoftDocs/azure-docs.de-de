---
title: Überprüfen des Zugriffs auf Gruppen oder Anwendungen mit Zugriffsüberprüfungen – Azure AD
description: Erfahren Sie, wie Sie den Zugriff von Gruppenmitgliedern oder Anwendungen mit Azure Active Directory-Zugriffsüberprüfungen prüfen können.
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
ms.openlocfilehash: 7e03ba5e7a4a24ee3114946230f78a64ea3d42cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265680"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Überprüfen des Zugriffs auf Gruppen und Anwendungen mit Azure AD-Zugriffsüberprüfungen

Azure Active Directory (Azure AD) erleichtert Unternehmen das Verwalten des Zugriffs auf Gruppen und Anwendungen in Azure AD und anderen Microsoft-Onlinediensten durch die Funktion „Zugriffsüberprüfungen“. In diesem Artikel wird erläutert, wie ein Reviewer eine Zugriffsüberprüfung für Gruppenmitglieder oder Benutzer mit Zugriffsberechtigungen für eine Anwendung durchführt. Wenn Sie den Zugriff auf ein Paket überprüfen möchten, finden Sie unter [Zugriffsüberprüfung für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](entitlement-management-access-reviews-review-access.md) weitere Informationen

## <a name="perform-access-review-using-my-apps"></a>Durchführen der Zugriffsüberprüfung über „Meine Apps“

Sie können die Zugriffsüberprüfung über die Benachrichtigungs-E-Mail oder direkt auf der Website starten.

- **E-Mail**:

>[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachfolgend sehen Sie eine Beispiel-E-Mail, die zur Überprüfen des Zugriffs auf eine Gruppe auffordert.

    ![Screenshot mit einer Beispiel-E-Mail von Microsoft zum Überprüfen des Zugriffs auf eine Gruppe](./media/perform-access-review/access-review-email.png)

1. Klicken Sie auf den Link **Start review** (Jetzt überprüfen), um die Zugriffsüberprüfung zu öffnen.

- **Wenn Sie die E-Mail nicht finden**, können Sie Ihre ausstehenden Zugriffsüberprüfungen mit folgenden Schritten anzeigen.

    1. Melden Sie sich auf [https://myapps.microsoft.com](https://myapps.microsoft.com) beim Portal „Meine Apps“ an.

        ![Das Portal „Meine Apps“ mit einer Liste der Apps, für die Sie Berechtigungen besitzen](./media/perform-access-review/myapps-access-panel.png)

    1. Klicken Sie oben rechts auf der Seite auf das Benutzersymbol, neben dem Ihr Name und Ihre Standardorganisation angezeigt werden. Werden mehrere Organisationen aufgelistet, wählen Sie die Organisation aus, die eine Zugriffsüberprüfung angefordert hat.

    1. Klicken Sie auf die Kachel **Zugriffsüberprüfungen**, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen.

        > [!NOTE]
        > Wenn die Kachel **Zugriffsüberprüfungen** nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

        ![Screenshot mit den ausstehenden Zugriffsüberprüfungen für Apps und Gruppen](./media/perform-access-review/access-reviews-list.png)

    1. Klicken Sie bei der Zugriffsüberprüfung, die Sie durchführen möchten, auf den Link **Überprüfung starten**.

Wenn Sie die Zugriffsüberprüfung öffnen, sehen Sie die Namen der Benutzer, deren Zugriff überprüft werden muss.

Wenn Sie aufgefordert werden, Ihren eigenen Zugriff zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen mit Azure AD-Zugriffsüberprüfungen](review-your-access.md).

![Öffnen der Zugriffsüberprüfung mit einer Liste der Benutzer, die überprüft werden müssen](./media/perform-access-review/perform-access-review.png)

Es gibt zwei Möglichkeiten, Zugriff zu gewähren oder zu verweigern:

- Sie können den Zugriff für einen oder mehrere Benutzer „manuell“ gewähren oder verweigern, indem Sie die jeweilige Aktion für jede Benutzeranforderung auswählen.
- Sie können die Systemempfehlungen übernehmen.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Gewähren oder Verweigern des Zugriffs eines oder mehrerer Benutzer

1. Sehen Sie sich die Liste der Benutzer an, und entscheiden Sie, ob der Zugriff für diese Benutzer weiterhin gewährt oder verweigert werden soll.

    - Um einem einzelnen Benutzer den Zugriff zu gewähren oder zu verweigern, klicken Sie auf die entsprechende Zeile, um ein Fenster zu öffnen, in dem Sie die auszuführende Aktion angeben können. 
    - Um mehreren Benutzern den Zugriff zu gewähren oder zu verweigern, müssen Sie die einzelnen Benutzernamen mit einem Häkchen versehen (aktivieren) und dann auf die Schaltfläche **X Benutzer überprüfen** klicken, um ein Fenster zu öffnen, in dem Sie die auszuführende Aktion angeben können.

1. Klicken Sie auf **Approve** (Gewähren) oder **Deny** (Verweigern). 

    ![Aktionsfenster mit den Optionen „Approve“ (Gewähren), „Deny“ (Verweigern) und „Don't know“ (Nicht bekannt)](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Wenn Sie sich nicht sicher sind, können Sie auch auf **Don‘t know** (Nicht bekannt) klicken. Die Zugriffsberechtigung des Benutzers wird beibehalten, und Ihre Auswahl wird in den Überwachungsprotokollen aufgezeichnet.

1. Der Administrator der Zugriffsüberprüfung kann festlegen, dass Sie im Feld **Grund** eine Begründung für Ihre Entscheidung eingeben müssen. Diese Möglichkeit besteht auch, wenn kein Grund angegeben werden muss. Sie können Ihre Entscheidung dennoch begründen. Ihre Eingabe ist anschließend für alle Reviewer verfügbar.

1. Wenn Sie eine entsprechende Maßnahme angegeben haben, klicken Sie auf **Save** (Speichern).

    >[!NOTE]
    > Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern. Wenn Sie Ihre Entscheidung ändern möchten, klicken Sie auf die Zeile, und ändern Sie diese. Sie können z.B. den Zugriff für einen Benutzer gewähren, der erst nicht gewährt wurde, oder den Zugriff für einen Benutzer verweigern, dem dieser zunächst gewährt wurde.

    >[!IMPORTANT]
    > - Wenn der Zugriff für einen Benutzer verweigert wird, wird dieser nicht sofort entfernt. Diese Benutzer werden nach Ende des Überprüfungszeitraums entfernt bzw. bei aktivierter Option [Automatisch anwenden](complete-access-review.md#apply-the-changes) beim Beenden der Überprüfung durch einen Administrator.
    > - Wenn es mehr als einen Reviewer gibt, wird die letzte abgegebene Antwort übernommen. Stellen Sie sich z.B. vor, dass ein Administrator zwei Reviewer auswählt, Alice und Bob. Alice öffnet die Zugriffsüberprüfung als Erste und bestätigt die Zugriffsanforderung eines Benutzers. Vor Ablauf des Überprüfungszeitraums öffnet Bob die Zugriffsüberprüfung und lehnt die Anforderung ab, die Alice zuvor genehmigt hat. Die letzte Entscheidung, also das Verweigern des Zugriffs, wird aufgezeichnet.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Gewähren und Verweigern von Zugriff auf Grundlage von Empfehlungen

Wir geben Ihnen Empfehlungen, die Sie mit einem einzigen Klick annehmen können, um die Zugriffsüberprüfung so einfach und schnell wie möglich zu gestalten. Diese Empfehlungen werden auf Grundlage der Anmeldeaktivität des Benutzers generiert.

1. Klicken Sie in der blauen Leiste am unteren Rand der Seite auf **Empfehlungen akzeptieren**.

    ![Screenshot der Liste mit den offenen Zugriffsüberprüfungen und ausgewählter Schaltfläche „Empfehlungen akzeptieren“](./media/perform-access-review/accept-recommendations.png)

    Eine Zusammenfassung der Empfehlungen wird angezeigt.

    ![Fenster mit einer Zusammenfassung der empfohlenen Aktionen](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicken Sie auf **OK**, um die Empfehlungen zu akzeptieren.

## <a name="perform-access-review-using-my-access-new"></a>Durchführen der Zugriffsüberprüfung über „Mein Zugriff“ (Neu)

Sie haben verschiedene Möglichkeiten, auf die neue Umgebung mit der aktualisierten Benutzeroberfläche unter „Mein Zugriff“ zuzugreifen:

### <a name="my-apps-portal"></a>Portal „Meine Apps“

1. Melden Sie sich auf [https://myapps.microsoft.com](https://myapps.microsoft.com) bei „Meine Apps“ an.

    ![Das Portal „Meine Apps“ mit einer Liste der Apps, für die Sie Berechtigungen besitzen](./media/perform-access-review/myapps-access-panel.png)

2. Klicken Sie auf die Kachel **Zugriffsüberprüfungen**, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen.

    > [!NOTE]
    > Wenn die Kachel **Zugriffsüberprüfungen** nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

![Liste mit ausstehenden Zugriffsüberprüfungen für Apps und Gruppen. Während der Vorschau wird das Banner zur Verfügbarkeit der neuen Umgebung angezeigt](./media/perform-access-review/banner.png)

3. Klicken Sie auf die Option **Testen**, die oben auf der Seite im Banner angezeigt wird. Dadurch gelangen Sie in die neue Umgebung von „Mein Zugriff“.
  
### <a name="email"></a>Email

  >[!IMPORTANT]
> Beim Empfang von E-Mails kann es Verzögerungen geben, und in einigen Fällen kann es bis zu 24 Stunden dauern. Fügen Sie Ihrer Liste der sicheren Empfänger azure-noreply@microsoft.com hinzu, um sicherzustellen, dass Sie alle E-Mails empfangen.

   1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachstehend finden Sie ein Beispiel für eine solche E-Mail-Nachricht:

   ![Beispiel-E-Mail von Microsoft zum Überprüfen des Zugriffs auf eine Gruppe](./media/perform-access-review/access-review-email-preview.png)

   2. Klicken Sie auf den Link **Start review** (Jetzt überprüfen), um die Zugriffsüberprüfung zu öffnen.

>[!NOTE]
>Wenn Sie auf „Überprüfung starten“ klicken, gelangen Sie zu **Meine Apps**. Führen Sie dann die Schritte aus, die im obigen Abschnitt **Portal „Meine Apps“** aufgeführt sind.

### <a name="navigate-to-my-access-directly"></a>Direktes Navigieren zu „Mein Zugriff“

Sie können Ihre ausstehenden Zugriffsüberprüfungen auch anzeigen, indem Sie „Mein Zugriff“ in Ihrem Browser öffnen.

1. Melden Sie sich auf https://myaccess.microsoft.com/ bei „Mein Zugriff“ an

2. Wählen Sie im Menü auf der linken Navigationsleiste **Zugriffsüberprüfungen** aus, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen, die Ihnen zugewiesen sind.

   ![Zugriffsüberprüfungen im Menü](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Gewähren oder Verweigern des Zugriffs eines oder mehrerer Benutzer

Nachdem Sie „Mein Zugriff“ unter „Gruppen und Apps“ geöffnet haben, wird Folgendes angezeigt:

- **Name**: Der Name der Zugriffsüberprüfung.
- **Fällig**: Das Fälligkeitsdatum der Überprüfung. Nach diesem Datum können abgelehnte Benutzer aus der zu überprüfenden Gruppe oder App entfernt werden.
- **Ressource**: Der Name der zu überprüfenden Ressource.
- **Fortschritt**: Die Anzahl von überprüften Benutzern in Relation zur Gesamtzahl der Benutzer im Rahmen dieser Zugriffsüberprüfung.

Klicken Sie zum Starten des Vorgangs auf den Namen einer Zugriffsüberprüfung.

![Liste mit ausstehenden Zugriffsüberprüfungen für Apps und Gruppen](./media/perform-access-review/access-reviews-list-preview.png)

In der geöffneten Zugriffsüberprüfung wird die Liste der Benutzer angezeigt, die von dieser Überprüfung betroffen sind. Wenn Sie aufgefordert werden, Ihren eigenen Zugriff zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen mit Azure AD-Zugriffsüberprüfungen](review-your-access.md).

Es gibt zwei Möglichkeiten, Zugriff zu gewähren oder zu verweigern:

- Sie können einem oder mehreren Benutzern den Zugriff manuell gewähren oder verweigern.
- Sie können die Systemempfehlungen übernehmen.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Manuelles Genehmigen oder Verweigern des Zugriffs für einen oder mehrere Benutzer

1. Sehen Sie sich die Liste der Benutzer an, und entscheiden Sie, ob der Zugriff für diese Benutzer weiterhin gewährt oder verweigert werden soll.
2. Wählen Sie einen oder mehrere Benutzer aus, indem Sie auf den Kreis neben ihrem Namen klicken.
3. Wählen Sie in der Leiste oberhalb der Liste **Genehmigen** oder **Verweigern** aus.
    - Wenn Sie sich nicht sicher sind, können Sie auch auf **Don‘t know** (Nicht bekannt) klicken. Die Zugriffsberechtigung des Benutzers wird beibehalten, und Ihre Auswahl wird in den Überwachungsprotokollen aufgezeichnet. Beachten Sie, dass alle Informationen, die Sie hier angeben, für alle Reviewer verfügbar sind. Sämtliche Reviewer können Ihre Kommentare lesen und beim Überprüfen der Anforderung berücksichtigen.

    ![Öffnen der Zugriffsüberprüfung mit einer Liste der Benutzer, die überprüft werden sollen](./media/perform-access-review/user-list-preview.png)

4. Der Administrator der Zugriffsüberprüfung kann festlegen, dass Sie im Feld **Grund** eine Begründung für Ihre Entscheidung eingeben müssen. Diese Möglichkeit besteht auch, wenn kein Grund angegeben werden muss. Sie können Ihre Entscheidung dennoch begründen. Ihre Eingabe ist anschließend für andere Reviewer verfügbar.

5. Klicken Sie auf **Submit**(Senden).
    - Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern. Wenn Sie Ihre Entscheidung ändern möchten, klicken Sie auf die Zeile, und ändern Sie diese. Sie können z.B. den Zugriff für einen Benutzer gewähren, der erst nicht gewährt wurde, oder den Zugriff für einen Benutzer verweigern, dem dieser zunächst gewährt wurde.

 >[!IMPORTANT]
 > - Wenn der Zugriff für einen Benutzer verweigert wird, wird dieser nicht sofort entfernt. Diese Benutzer werden nach Ende des Überprüfungszeitraums entfernt bzw. beim Beenden der Überprüfung durch einen Administrator. 
 > - Wenn es mehr als einen Reviewer gibt, wird die letzte abgegebene Antwort übernommen. Stellen Sie sich z. B. vor, dass ein Administrator zwei Reviewer auswählt, Alice und Bob. Alice öffnet die Zugriffsüberprüfung als Erste und bestätigt die Zugriffsanforderung eines Benutzers. Vor Ablauf des Überprüfungszeitraums öffnet Bob die Zugriffsüberprüfung und lehnt die Anforderung ab, die Alice zuvor genehmigt hat. Die letzte Entscheidung, also das Verweigern des Zugriffs, wird aufgezeichnet.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Gewähren und Verweigern von Zugriff auf Grundlage von Empfehlungen

Wir geben Ihnen Empfehlungen, die Sie mit einem einzigen Klick annehmen können, um die Zugriffsüberprüfung so einfach und schnell wie möglich zu gestalten. Diese Empfehlungen werden auf Grundlage der Anmeldeaktivität des Benutzers generiert.

1. Wählen Sie mindestens einen Benutzer aus, und klicken Sie auf **Empfehlungen akzeptieren**.

    ![Öffnen der Liste mit den Zugriffsüberprüfungen mit Anzeige der Schaltfläche „Empfehlungen akzeptieren“](./media/perform-access-review/accept-recommendations-preview.png)

1. Klicken Sie auf **Übermitteln**, um die Empfehlungen zu akzeptieren.

Um die Empfehlungen für alle Benutzer zu akzeptieren, stellen Sie sicher, dass keine Benutzer ausgewählt sind und klicken oben auf der Leiste auf **Empfehlungen akzeptieren**.

>[!NOTE]
>Wenn Sie Empfehlungen akzeptieren, werden vorherige Entscheidungen dadurch nicht geändert.

## <a name="next-steps"></a>Nächste Schritte

- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)
