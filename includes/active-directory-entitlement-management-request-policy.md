---
title: include file
description: include file
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: b7029c5cdd004910caa83425536bbe481e4b01f3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554630"
---
## <a name="for-users-in-your-directory"></a>Für Benutzer in Ihrem Verzeichnis

Gehen Sie folgendermaßen vor, wenn Sie möchten, dass Benutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. Beim Definieren der Anforderungsrichtlinie können Sie einzelne Benutzer oder Gruppen von Benutzern angeben. Beispielsweise verfügt Ihre Organisation möglicherweise bereits über eine Gruppe wie **Alle Mitarbeiter**.  Wenn diese Gruppe in die Richtlinie für Benutzer eingefügt wird, die Zugriff anfordern können, können alle Mitglieder dieser Gruppe Zugriff anfordern.

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für in Ihrem Verzeichnis befindliche Benutzer**.

    Wenn Sie diese Option auswählen, werden neue Optionen angezeigt, um weiter zu verfeinern, wer in Ihrem Verzeichnis dieses Zugriffspaket anfordern kann.

    ![Zugriffspaket – Anforderungen – Für in Ihrem Verzeichnis befindliche Benutzer](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Wählen Sie eine der folgenden Optionen aus:

    |  |  |
    | --- | --- |
    | **Bestimmte Benutzer und Gruppen** | Wählen Sie diese Option aus, wenn Sie möchten, dass nur die von Ihnen angegebenen Benutzer und Gruppen in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. |
    | **Alle Mitglieder (keine Gäste)** | Wählen Sie diese Option aus, wenn Sie möchten, dass alle Mitgliedsbenutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. Diese Option umfasst keine Gastbenutzer, die Sie möglicherweise in Ihr Verzeichnis eingeladen haben. |
    | **Alle Benutzer (einschließlich Gästen)** | Wählen Sie diese Option aus, wenn Sie möchten, dass alle Mitgliedsbenutzer und Gastbenutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. |

    Gastbenutzer verweisen auf externe Benutzer, die mit [Azure AD B2B-](../articles/active-directory/external-identities/what-is-b2b.md) in Ihr Verzeichnis eingeladen wurden. Weitere Informationen zu den Unterschieden zwischen Mitglieds- und Gastbenutzern finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Wenn Sie **Bestimmte Benutzer und Gruppen** ausgewählt haben, klicken Sie auf **Benutzer und Gruppen hinzufügen**.

1. Wählen Sie im Bereich „Benutzer und Gruppen auswählen“ die Benutzer und Gruppen aus, die Sie hinzufügen möchten.

    ![Zugriffspaket – Anforderungen – Benutzer und Gruppen auswählen](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klicken Sie auf **Auswählen**, um die Benutzer und Gruppen hinzuzufügen.

1. Fahren Sie mit dem Abschnitt [Genehmigung](#approval) fort.

## <a name="for-users-not-in-your-directory"></a>Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden

 **Benutzer, die sich nicht in Ihrem Verzeichnis befinden** bezieht sich auf Benutzer in einem anderen Azure AD-Verzeichnis bzw. einer anderen Domäne. Diese Benutzer wurden möglicherweise noch nicht zu Ihrem Verzeichnis eingeladen. Azure AD-Verzeichnisse müssen in den **Einschränkungen bei der Zusammenarbeit** so konfiguriert werden, dass sie Einladungen zulassen. Weitere Informationen finden Sie unter [Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann](../articles/active-directory/external-identities/delegate-invitations.md).

> [!NOTE]
> Für einen sich noch nicht in Ihrem Verzeichnis befindenden Benutzer, dessen Anforderung genehmigt oder automatisch genehmigt wird, wird ein Gastbenutzerkonto erstellt. Der Gast wird eingeladen, erhält jedoch keine Einladungs-E-Mail. Stattdessen erhält er eine E-Mail, wenn seine Zugriffspaketzuweisung bereitgestellt wird. Wenn dieser Gastbenutzer zu einem späteren Zeitpunkt keine Zugriffspaketzuweisungen mehr besitzt, weil die letzte Zuweisung abgelaufen ist oder abgebrochen wurde, wird das Gastbenutzerkonto standardmäßig für die Anmeldung blockiert und anschließend gelöscht. Wenn Gastbenutzer dauerhaft in Ihrem Verzeichnis bleiben sollen, auch wenn sie keine Zugriffspaketzuweisungen haben, können Sie die Einstellungen für Ihre Berechtigungsverwaltungskonfiguration ändern. Weitere Informationen zum Gastbenutzerobjekt finden Sie unter [Eigenschaften eines Azure Active Directory B2B-Zusammenarbeitsbenutzers](../articles/active-directory/external-identities/user-properties.md).

Gehen Sie folgendermaßen vor, wenn Sie Benutzern, die sich nicht in Ihrem Verzeichnis befinden, die Möglichkeit geben möchten, dieses Zugriffspaket anzufordern:

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für nicht in Ihrem Verzeichnis befindliche Benutzer**.

    Wenn Sie diese Option auswählen, werden neue Optionen angezeigt.

    ![Zugriffspaket – Anforderungen – Für nicht in Ihrem Verzeichnis befindliche Benutzer](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Wählen Sie eine der folgenden Optionen aus:

    |  |  |
    | --- | --- |
    | **Bestimmte verbundene Organisationen** | Wählen Sie diese Option aus, wenn Sie aus einer Liste mit Organisationen auswählen möchten, die Ihr Administrator zuvor hinzugefügt hat. Alle Benutzer aus den ausgewählten Organisationen können dieses Zugriffspaket anfordern. |
    | **Alle verbundenen Organisationen** | Wählen Sie diese Option aus, wenn alle Benutzer aus allen Ihren verbundenen Organisationen dieses Zugriffspaket anfordern können. |
    | **Alle Benutzer (alle verbundenen Organisationen und alle neuen externen Benutzer)** | Wählen Sie diese Option aus, wenn alle Benutzer von allen verbundenen Organisationen dieses Zugriffspaket anfordern können und die Einstellungen der B2B-Zulassungsliste oder -Verweigerungsliste für jeden neuen externen Benutzer Vorrang haben soll. |

    Eine verbundene Organisation ist ein externes Azure AD-Verzeichnis bzw. eine externe Domäne, mit der eine Beziehung besteht.

1. Wenn Sie **Bestimmte verbundene Organisationen**  ausgewählt haben, klicken Sie auf **Verzeichnisse hinzufügen**, um aus einer Liste der verbundenen Organisationen auszuwählen, die Ihr Administrator zuvor hinzugefügt hat.

1. Geben Sie den Namen oder Domänennamen ein, um nach einer zuvor verbundenen Organisation zu suchen.

    ![Zugriffspaket – Anforderungen – Verzeichnisse auswählen](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Wenn die Organisation, mit der Sie zusammenarbeiten möchten, nicht in der Liste enthalten ist, können Sie Ihren Administrator bitten, sie als verbundene Organisation hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen einer verbundenen Organisation](../articles/active-directory/governance/entitlement-management-organization.md).

1. Nachdem Sie alle Ihre verbundenen Organisationen ausgewählt haben, klicken Sie auf **Auswählen**.

    > [!NOTE]
    > Alle Benutzer aus den ausgewählten verbundenen Organisationen werden dieses Zugriffspaket anfordern können. Dies schließt in Azure AD auch Benutzer aller Unterdomänen ein, die der Organisation zugeordnet sind, sofern diese Domänen nicht über die Zulassungs- oder Verweigerungsliste von Azure B2B blockiert werden. Weitere Informationen finden Sie unter [Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen](../articles/active-directory/external-identities/allow-deny-list.md).

1. Fahren Sie mit dem Abschnitt [Genehmigung](#approval) fort.

## <a name="none-administrator-direct-assignments-only"></a>Keine (nur direkte Administratorzuweisungen)

Gehen Sie folgendermaßen vor, wenn Sie Zugriffsanforderungen umgehen und Administratoren ermöglichen soll, bestimmte Benutzer direkt diesem Zugriffspaket zuzuweisen. Benutzer müssen das Zugriffspaket nicht anfordern. Sie können weiterhin Lebenszykluseinstellungen festlegen, aber es gibt keine Anforderungseinstellungen.

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Keine (nur direkte Zuweisungen eines Administrators)** .

    ![Zugriffspaket – Anforderungen – Keine (nur direkte Administratorzuweisungen)](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Nach der Erstellung des Zugriffspakets können Sie direkt bestimmte interne und externe Benutzer dem Zugriffspaket zuweisen. Wenn Sie einen externen Benutzer angeben, wird ein Gastbenutzerkonto in Ihrem Verzeichnis erstellt. Weitere Informationen zum direkten Zuweisen eines Benutzers finden Sie unter [Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket ](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Fahren Sie mit dem Abschnitt [Ermöglichen von Anforderungen](#enable-requests) fort.

## <a name="approval"></a>Genehmigung

Im Abschnitt „Anforderung“ geben Sie an, ob eine Genehmigung erforderlich ist, wenn Benutzer dieses Zugriffspaket anfordern. Die Genehmigungseinstellungen funktionieren wie folgt:

- Bei einstufiger Genehmigung muss nur eine der ausgewählten genehmigenden Personen bzw. genehmigenden Fallbackpersonen eine Anforderung genehmigen. 
- Bei zweistufiger Genehmigung muss nur eine der ausgewählten genehmigenden Personen pro Stufe eine Anforderung genehmigen.
- Die genehmigende Person kann ein Vorgesetzter, ein interner Sponsor oder ein externer Sponsor sein, je nachdem, bei wem die Steuerung des Zugriffs gemäß der Richtlinie liegt.
- Weder für die einstufige noch für die zweistufige Genehmigung ist die Genehmigung durch jede ausgewählte genehmigende Person erforderlich.
- Die Genehmigungsentscheidung basiert darauf, welche genehmigende Person die Anforderung zuerst überprüft.

Eine Demonstration, wie einer Anforderungsrichtlinie genehmigende Personen hinzugefügt werden, finden Sie im folgenden Video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Eine Demonstration, wie einer Anforderungsrichtlinie mehrstufige Genehmigung hinzugefügt wird, finden Sie im folgenden Video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Führen Sie diese Schritte aus, um die Genehmigungseinstellungen für Anforderungen des Zugriffspakets festzulegen:

1. Um die Genehmigung für Anforderungen der ausgewählten Benutzer anzufordern, setzen Sie **Genehmigung anfordern** auf **Ja**. Stellen Sie den Umschalter auf **Nein**, um Anforderungen automatisch genehmigen zu lassen.

1. Um von Benutzern eine Begründung für die Anforderung des Zugriffspakets anzufordern, legen Sie die Umschaltfläche **Begründung des Anforderers erforderlich** auf **Ja** fest.
    
1. Bestimmen Sie jetzt, ob für Anforderungen eine einstufige oder eine zweistufige Genehmigung erforderlich ist. Stellen Sie den Umschalter **Wie viele Phasen** für einstufige Genehmigung auf **1** oder für zweistufige Genehmigung auf **2**.

    ![Zugriffspaket – Anforderungen – Genehmigungseinstellungen](./media/active-directory-entitlement-management-request-policy/approval.png)

Verwenden Sie die folgenden Schritte, um genehmigende Personen hinzuzufügen, nachdem Sie die Anzahl der erforderlichen Stufen festgelegt haben: 

### <a name="single-stage-approval"></a>Einstufige Genehmigung

1. Fügen Sie die **erste genehmigende Person** hinzu:
    
    Wenn Sie Richtlinie Ihnen die Steuerung des Zugriffs für Benutzer in Ihrem Verzeichnis gestattet, können Sie **Vorgesetzter als genehmigende Person** auswählen. Fügen Sie alternativ einen bestimmten Benutzer hinzu, indem Sie nach dem Auswählen von „Bestimmte genehmigende Personen auswählen“ im Dropdownmenü´auf **Genehmigende Personen hinzufügen** klicken.
    
    ![Zugriffspaket – Anforderungen – Für Benutzer im Verzeichnis – Erste genehmigende Person](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Wenn diese Richtlinie so eingestellt ist, dass sie den Zugriff für Benutzer steuert, die sich nicht in Ihrem Verzeichnis befinden, können Sie **Externer Sponsor** oder **Interner Sponsor** auswählen. Oder fügen Sie einen bestimmten Benutzer oder eine Gruppe hinzu, indem Sie unter „Bestimmte genehmigende Personen auswählen“ auf **Genehmigende Personen hinzufügen** klicken.
    
    ![Zugriffspaket – Anforderungen – Für Benutzer außerhalb des Verzeichnisses – Erste genehmigende Person](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Wenn Sie **Vorgesetzter** als erste genehmigende Person ausgewählt haben, klicken Sie auf **Fallback hinzufügen**, um einen oder mehrere Benutzer oder Gruppen in Ihrem Verzeichnis auszuwählen, die als genehmigende Fallbackperson gelten sollen. Genehmigende Fallbackpersonen empfangen die Anforderung, wenn die Berechtigungsverwaltung den Vorgesetzten des Benutzers, der Zugriff anfordert, nicht finden kann.

    Der Vorgesetzte wird von der Berechtigungsverwaltung anhand des Attributs **Vorgesetzter** gefunden. Das Attribut befindet sich im Profil des Benutzers in Azure AD. Weitere Informationen finden Sie unter [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen mit Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Wenn Sie **Bestimmte genehmigende Personen auswählen** ausgewählt haben, klicken Sie auf **Genehmigende Person hinzufügen**, um einen oder mehrere Benutzer oder Gruppen in Ihrem Verzeichnis auszuwählen, die genehmigende Personen sein sollen.

1. Geben Sie im Feld unter **Zulässige Anzahl von Tagen für Entscheidungsfindung** die Anzahl der Tage an, die eine genehmigende Person Zeit hat, um eine Anforderung für dieses Zugriffspaket zu überprüfen.

    Wenn eine Anforderung nicht innerhalb dieses Zeitraums genehmigt wird, wird sie automatisch abgelehnt. Der Benutzer muss eine weitere Anforderung für das Zugriffspaket senden.

1. Um von genehmigenden Personen eine Rechtfertigung ihrer Entscheidung anzufordern, legen Sie „Begründung der genehmigenden Person erforderlich“ auf **Ja** fest.

    Die Begründung ist für andere genehmigende Personen und den Anfordernden sichtbar.

### <a name="2-stage-approval"></a>Zweistufige Genehmigung

Wenn Sie eine zweistufige Genehmigung ausgewählt haben, müssen Sie eine zweite genehmigende Person hinzufügen.

1. Hinzufügen der **zweiten genehmigenden Person**: 
    
    Wenn sich die Benutzer in Ihrem Verzeichnis befinden, fügen Sie einen bestimmten Benutzer als zweite genehmigende Person hinzu, indem Sie unter „Bestimmte genehmigende Personen auswählen“ auf **Genehmigende Personen hinzufügen** klicken.

    ![Zugriffspaket – Anforderungen – Für Benutzer im Verzeichnis – Zweite genehmigende Person](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Wenn sich die Benutzer nicht in Ihrem Verzeichnis befinden, wählen Sie **Interner Sponsor** oder **Externer Sponsor** als zweite genehmigende Person aus. Fügen Sie nach dem Auswählen der genehmigenden Person die genehmigenden Fallbackpersonen hinzu.

    ![Zugriffspaket – Anforderungen – Für Benutzer außerhalb des Verzeichnisses – Zweite genehmigende Person](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Geben Sie in dem Feld unter **Zulässige Anzahl von Tagen für Entscheidungsfindung** die Anzahl der Tage an, die der zweiten genehmigenden Person für die Genehmigung der Anforderung zur Verfügung stehen. 

1. Legen Sie den Umschalter „Begründung der genehmigenden Person erforderlich“ auf **Ja** oder **Nein** fest.

### <a name="alternate-approvers"></a>Alternative genehmigende Personen

Sie können neben den ersten und zweiten genehmigenden Personen alternative genehmigende Personen angeben, die Anforderungen genehmigen können. Das Festlegen alternativer genehmigender Personen trägt dazu bei, dass die Anforderungen vor ihrem Ablauf (Timeout) genehmigt oder abgelehnt werden. Beim Auflisten genehmigender Personen kann bei zweistufiger Genehmigung zwischen erster und zweiter genehmigender Person unterschieden werden. 

Durch das Angeben von alternativen genehmigenden Personen kann eine ausstehende Anforderung gemäß dem von Ihnen bei der Richtlinieneinrichtung angegebenen Weiterleitungsplan an die alternativen genehmigenden Personen weitergeleitet werden, wenn die ersten oder zweiten genehmigenden Personen die Anforderung nicht genehmigen oder ablehnen können. Die alternativen genehmigenden Personen erhalten eine E-Mail mit der Aufforderung, die ausstehende Anforderung zu genehmigen oder abzulehnen.

Nachdem die Anforderung an die alternativen genehmigenden Personen weitergeleitet wurde, können die ersten oder zweiten genehmigenden Personen den Antrag dennoch weiterhin genehmigen oder ablehnen. Alternative genehmigende Personen nutzen dieselbe Website „Mein Zugriff“, um die ausstehende Anforderung zu genehmigen oder abzulehnen.

Es können Personen oder Gruppen von Personen aufgelistet werden, die als genehmigende Personen und alternative genehmigende Personen fungieren sollen. Bitte achten Sie darauf, dass Sie verschiedene Gruppen von Personen auflisten, die als erste, zweite und alternative genehmigende Personen fungieren sollen.
Wenn Sie beispielsweise Alice und Bob als erste genehmigende Personen aufgelistet haben, führen Sie Carol und Dave als alternative genehmigende Personen auf. Führen Sie die folgenden Schritte aus, um einem Zugriffspaket alternative genehmigende Personen hinzuzufügen:

1. Klicken Sie unter der ersten genehmigenden Person, der zweiten genehmigenden Person oder unter beiden auf **Erweiterte Anforderungseinstellungen anzeigen**.

    ![Zugriffspakete – Richtlinie – Erweiterte Anforderungseinstellungen anzeigen](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Legen Sie die Umschaltfläche **Wenn keine Aktion ausgeführt wird, an alternative genehmigende Personen weiterleiten?** auf **Ja** fest.

1. Klicken Sie auf **Alternative genehmigende Personen hinzufügen**, und wählen Sie die alternativen genehmigende Personen aus der Liste aus.

    ![Zugriffspaket – Richtlinie – Alternative genehmigende Personen hinzufügen](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. Geben Sie im Feld **Nach wie viel Tagen an alternative genehmigende Personen weiterleiten?** die Anzahl von Tagen ein, die Sie den genehmigenden Personen zum Genehmigen oder Ablehnen einer Anforderung einräumen. Wenn nach Ablauf der Anforderungsdauer keine der genehmigenden Personen die Anforderung genehmigt oder abgelehnt hat, läuft die Anforderung ab (Timeout), und der Benutzer muss eine weitere Anforderung für das Zugangspaket senden. 

    Anforderungen können erst einen Tag nach Erreichen der Hälfte der Anforderungsdauer an alternative genehmigende Personen weitergeleitet werden, und für die Entscheidung der Hauptgenehmiger muss ein Timeout von mindestens vier Tagen verwendet werden. Bei einem Anforderungstimeout von bis zu drei Tagen reicht die Zeit nicht aus, um die Anforderung an alternative genehmigende Personen weiterzuleiten. In diesem Beispiel beträgt die Dauer der Anforderung 14 Tage. Die Hälfte der Anforderungsdauer wird also an Tag 7 erreicht. Die Anforderung kann deshalb erst an Tag 8 weitergeleitet werden. Darüber hinaus können Anforderungen nicht am letzten Tag der Antragsdauer weitergeleitet werden. Im Beispiel kann die Anforderung also spätestens an Tag 13 weitergeleitet werden.

## <a name="enable-requests"></a>Ermöglichen von Anforderungen

1. Wenn das Zugriffspaket Benutzern in der Anforderungsrichtlinie sofort zum Anfordern zur Verfügung gestellt werden soll, legen Sie den Umschalter auf **Ja** fest.

    Sie können sie in der Zukunft immer aktivieren, nachdem Sie das Erstellen des Zugriffspakets abgeschlossen haben.

    Wenn Sie **Keine (nur direkte Administratorzuweisungen)** ausgewählt haben und Sie die Aktivierung auf **Nein** festlegen, können Administratoren dieses Zugriffspaket nicht direkt zuweisen.

    ![Screenshot: Option zum Aktivieren neuer Anforderungen und Zuweisungen](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klicken Sie auf **Weiter**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Hinzufügen von Informationen zum Anforderer (Vorschau) zu einem Zugriffspaket

1. Wechseln Sie zur Registerkarte **Anfordererinformationen**, und klicken Sie auf die Unterregisterkarte **Fragen**.
 
1. Geben Sie als Frage in das Feld **Frage** ein, was Sie den Anforderer fragen möchten (auch als Anzeigezeichenfolge bezeichnet).

    ![Zugriffspaket – Richtlinie – Einstellung „Anfordererinformationen aktivieren“](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Wenn Sie Ihre eigenen Lokalisierungsoptionen hinzufügen möchten, klicken Sie auf **Lokalisierung hinzufügen**.
    1. Wenn Sie sich im Bereich **Lokalisierungen für Frage hinzufügen** befinden, wählen Sie den **Sprachcode** für die Sprache aus, in die Sie die Frage lokalisieren.
    1. Geben Sie die Frage in der von Ihnen konfigurierten Sprache das Feld **Lokalisierter Text** ein.
    1. Nachdem Sie alle benötigten Lokalisierungen hinzugefügt haben, klicken Sie auf **Speichern**.

    ![Zugriffspaket – Richtlinie – Lokalisierten Text konfigurieren](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Wählen Sie das **Antwortformat** aus, in dem Anforderer antworten sollen. Antwortformate umfassen: *Kurzer Text*, *Mehrfachauswahl* und *Langer Text*.
 
    ![Zugriffspaket – Richtlinie – „Anzeigen und bearbeiten“ für Antwortformat von Multiple Choice auswählen](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Wenn Sie „Multiple Choice“ auswählen, klicken Sie auf die Schaltfläche **Anzeigen und bearbeiten**, um die Antwortoptionen zu konfigurieren.
    1. Nachdem Sie „Anzeigen und bearbeiten“ ausgewählt haben, wird der Bereich **Frage anzeigen/bearbeiten** angezeigt.
    1. Geben Sie die Antwortoptionen in die Felder **Antwortwerte** ein, die Sie dem Anforderer zur Verfügung stellen möchten, wenn dieser die Frage beantwortet.
    1. Geben Sie so viele Antworten ein, wie Sie benötigen, und klicken Sie dann auf **Speichern**.
    
    ![Zugriffspaket – Richtlinie – Multiple Choice-Optionen eingeben](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Um vom Anforderer die Beantwortung der Frage zu fordern, wenn dieser den Zugriff auf ein Zugriffspaket anfordert, klicken Sie auf das Kontrollkästchen unter **Erforderlich**.

1. Klicken Sie auf „Weiter“.