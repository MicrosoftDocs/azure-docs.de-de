---
title: Erstellen eines Microsoft-Entwicklerkontos | Azure Marketplace
description: Anforderungen und Schritte für das Erstellen eines Microsoft-Entwicklerkontos.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4fde5d81fb97bec23fdb46ff53b05874c88d9d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935864"
---
<a name="create-a-microsoft-developer-account"></a>Erstellen eines Microsoft-Entwicklerkontos
====================================

In diesem Artikel ist beschrieben, wie Sie ein genehmigter Microsoft-Entwickler für Veröffentlichung in Azure Marketplace werden.

## <a name="create-a-microsoft-account"></a>Erstellen eines Microsoft-Kontos

Am Anfang des Veröffentlichungsprozesses steht die Registrierung im **Microsoft Developer Center**. Sie nutzen dieses registrierte Konto im **[Cloud-Partnerportal](https://cloudpartner.azure.com/)** , um den Veröffentlichungsprozess zu starten.

### <a name="general-account-guidelines"></a>Allgemeine Kontorichtlinien

Es empfiehlt sich, dass Sie für Ihre Azure Marketplace-Angebote nur ein einziges Microsoft-Konto verwenden. Dieses Konto sollte nicht speziell für Dienste oder Angebote vorgesehen sein.

Die Adresse, die für den Benutzernamen maßgeblich ist, sollte sich in Ihrer Domäne befinden und von Ihrem IT-Team gesteuert werden. Alle auf die Veröffentlichung bezogenen Aktivitäten sollten über dieses Konto durchgeführt werden.

>[!WARNING]
>Wörter wie „Azure“ und „Microsoft“ werden bei der Registrierung eines Microsoft-Kontos nicht unterstützt. Vermeiden Sie diese Wörter, um den Prozess der Kontoerstellung und -registrierung abzuschließen.

### <a name="company-account-guidelines"></a>Richtlinien für ein Unternehmenskonto

Beachten Sie diese Richtlinien, wenn mehrere Personen auf das Konto zugreifen müssen, indem sie sich bei dem Microsoft-Konto anmelden, das das Konto geöffnet hat.

>[!IMPORTANT]
>Wenn Sie den Zugriff auf Ihr Developer Center-Konto für mehrere Benutzer zulassen möchten, empfehlen wir Ihnen die Verwendung von Azure Active Directory, um den einzelnen Benutzern Rollen zuzuweisen. Diese Benutzer können auf das Konto zugreifen, indem sie sich mit ihren jeweiligen Azure AD-Anmeldeinformationen anmelden. Weitere Informationen finden Sie unter [Verwalten von Kontobenutzern](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Erstellen Sie Ihr Microsoft-Konto mit einer E-Mail-Adresse, die zur Domäne Ihres Unternehmens, nicht zu einer Einzelperson gehört. Beispiel: windowsapps\@fabrikam.com.
-   Beschränken Sie den Zugriff auf dieses Microsoft-Konto auf die kleinste mögliche Anzahl von Entwicklern.
-   Richten Sie eine Unternehmens-E-Mail-Verteilerliste ein, die alle Personen enthält, die auf das Entwicklerkonto zugreifen müssen, und fügen Sie diese E-Mail-Adresse Ihren Sicherheitsinfos hinzu. So können alle Mitarbeiter auf der Liste bei Bedarf Sicherheitscodes erhalten und die Sicherheitsinfos Ihres Microsoft-Kontos verwaltet werden. Wenn das Einrichten einer Verteilerliste nicht möglich ist, muss der Besitzer des einzelnen E-Mail-Kontos bei Aufforderung verfügbar sein, um auf den Sicherheitscode zuzugreifen und ihn zu teilen (z.B. wenn dem Konto neue Sicherheitsinfos hinzugefügt werden, oder wenn ein neues Gerät darauf zugreifen muss).
-   Fügen Sie eine Telefonnummer hinzu, die keine Durchwahl benötigt, und auf die wichtige Teammitglieder zugreifen können.
-   Sorgen Sie im Allgemeinen dafür, dass Entwickler vertrauenswürdige Geräten verwenden, um sich beim Entwicklerkonto Ihres Unternehmens anzumelden. Alle wichtigen Teammitglieder sollten auf diese vertrauenswürdigen Geräten zugreifen können. Dies reduziert die Notwendigkeit, beim Zugriff auf das Konto Sicherheitscodes senden zu müssen.
-   Wenn Sie Zugriff auf das Konto von einem nicht vertrauenswürdigen PC zulassen müssen, begrenzen Sie den Zugriff auf ein Maximum von fünf Entwicklern. Im Idealfall sollten diese Entwickler mit Computern auf das Konto zugreifen, die den gleichen geografischen und Netzwerkstandort haben.
-   Überprüfen Sie die [Sicherheitsinformationen für Ihr Unternehmen ](https://account.live.com/proofs/Manage) regelmäßig, um sicherzustellen, dass sie aktuell sind.

>[!IMPORTANT]
>Auf Ihr Entwicklerkonto sollte in erster Linie mit vertrauenswürdigen PCs zugegriffen werden. Dies ist wichtig, da die Anzahl der pro Konto und Woche generierten Codes beschränkt ist. Darüber hinaus ermöglicht dies die nahtloseste Anmeldeerfahrung.
>
>Weitere Informationen finden Sie unter [Zusätzliche Richtlinien für Unternehmenskonten](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>So erstellen Sie ein Microsoft-Konto

1.  Öffnen Sie eine neue Chrome-Inkognito- oder Internet Explorer-InPrivate-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem vorhandenen Konto angemeldet werden.
2.  Registrieren Sie die E-Mail-Adresse (gemäß den obigen Richtlinien) als Microsoft-Konto, indem Sie diesen [Link](https://signup.live.com/signup.aspx) verwenden. Führen Sie die folgenden Registrierungsanweisungen:

    - Wenn Sie Ihr Konto als Microsoft-Konto registrieren möchten, müssen Sie eine gültige Telefonnummer angeben, damit das System Ihnen einen Kontoüberprüfungscode als SMS oder automatisierten Anruf senden kann.
    - Wenn Sie Ihr Konto als Microsoft-Konto registrieren möchten, müssen Sie eine gültige E-Mail-ID angeben, damit Sie eine automatisierte E-Mail zur Kontoüberprüfung empfangen können.
    - Verifizieren Sie die an die Verteilerliste gesendete E-Mail-Adresse.

    Sie können das neue Microsoft-Konto jetzt im Microsoft Developer Center verwenden.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrieren Ihres Kontos beim Microsoft Developer Center

Das Microsoft Developer Center wird für die einmalige Registrierung der Unternehmensinformationen verwendet. Die Person, die das Konto registriert, muss ein offizieller Vertreter des Unternehmens sein und ihre persönlichen Informationen zum Zweck der Identitätsüberprüfung bereitstellen. Diese Person muss ein Microsoft-Konto verwenden, das für das Unternehmen freigegeben ist. **Das gleiche Konto muss im Cloudpartnerportal verwendet werden.** Sie sollten überprüfen, ob Ihr Unternehmen nicht bereits über ein Microsoft Developer Center-Konto verfügt, bevor Sie ein Konto erstellen. Während des Prozesses erfassen wir Informationen zu Unternehmensadresse, Bankdaten und Steuer. Diese Informationen erhalten Sie üblicherweise über Bank- oder Geschäftskontakte.

>[!IMPORTANT]
>Sie müssen folgende Komponenten des Entwicklerprofils ausfüllen, um die verschiedenen Phasen der Angebotserstellung und -bereitstellung durchlaufen zu können.

| Entwicklerprofil     | Entwurf starten    | Staging       | Kostenlos veröffentlichen und Lösungsvorlage   | Für den Erwerb veröffentlichen   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Unternehmensregistrierung  | Verpflichtend         | Verpflichtend     | Verpflichtend                             | Verpflichtend             |
| Steuer-ID        | Optional          | Optional      | Optional                              | Verpflichtend             |
| Bankkonto          | Optional          | Optional      | Optional                              | Verpflichtend             |

>[!NOTE]
>Bring Your Own License (BYOL) wird nur für virtuelle Computer unterstützt und gilt als ein kostenloses Angebot.

### <a name="register-your-company-account"></a>Registrieren Ihres Unternehmenskontos

1. Öffnen Sie eine neue Internet Explorer-InPrivate- oder Chrome-Inkognito-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.

2. Wechseln Sie zum [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure), um sich als Verkäufer zu registrieren. Bitte lesen Sie den folgenden wichtigen Hinweis, bevor Sie fortfahren.

   ![Microsoft-Kontoüberprüfung](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Stellen Sie sicher, dass die E-Mail-ID oder Verteilerliste (eine Verteilerliste wird empfohlen, um die Abhängigkeit von einzelnen Personen aufzuheben), die Sie für die Registrierung im Developer Center verwenden werden, zunächst als Microsoft-Konto registriert wird. Falls nicht, registrieren Sie sich bitte über diesen Link. Darüber hinaus kann für die Dev Center-Registrierung keine E-Mail-ID unter der Microsoft-Unternehmensdomäne verwendet werden.

   ![Dev Center-Anmeldung](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Führen Sie alle Schritte des Assistenten „Unterstützen Sie uns beim Schutz Ihres Kontos“ aus, um Ihre Identität über eine Telefonnummer oder E-Mail-Adresse zu prüfen.

4. Wählen Sie in „Registrierung – Kontoinformationen“ im Dropdownmenü Ihr **Land/Region des Kontos** aus, und wählen Sie dann **Weiter** aus.

   ![Land/Region auswählen](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >„Verkäuferländer/-regionen“: Um Ihre Dienste im Azure Marketplace verkaufen zu können, muss die registrierte Entität in einem der genehmigten Verkäuferländer/-regionen ansässig sein, die in der Dropdownliste aufgeführt sind. Diese Einschränkung hat auszahlungstechnische und steuerliche Gründe. Weitere Informationen finden Sie in den Marketplace-Teilnahmerichtlinien.

5. Wählen Sie **Unternehmen** als „Kontotyp“ aus und wählen Sie dann **Weiter** aus.

    >[!IMPORTANT]
    >Um sich mehr über Kontotypen zu informieren und entscheiden zu können, welcher Typ der beste für Sie ist, sehen Sie sich Kontotyp, Standorte und Gebühren an, die auf dem nächsten Bildschirmfoto gezeigt sind.

    ![Kontotypen für Verkäufer](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Geben Sie den gewünschten Namen in **Anzeigename des Herausgebers** ein. Dies ist in der Regel der Name Ihres Unternehmens.

    >[!NOTE]
    >Der im Dev Center eingegebene Anzeigename des Herausgebers wird im Azure Marketplace nicht angezeigt, nachdem Ihr Angebot aufgeführt wird. Aber diese Information wird benötigt, um den Registrierungsprozess abzuschließen.

7. Geben Sie die **Kontaktinformationen** für die Kontoüberprüfung ein.

    >[!IMPORTANT]
    >Es ist erforderlich, dass Sie genaue Kontaktinformationen angeben, denn diese werden in unserem Überprüfungsprozess für Ihr Unternehmen verwendet, damit es im Developer Center genehmigt werden kann.

8. Geben Sie die Kontaktinformationen für den **Genehmiger des Unternehmens**ein. Der Genehmiger des Unternehmens ist die Person, die gewährleisten kann, dass Sie berechtigt sind, im Developer Center im Auftrag Ihres Unternehmens ein Konto zu erstellen. Nachdem Sie diese Informationen angegeben haben, wählen Sie **Weiter** aus, um zum Abschnitt für **Bezahlung** zu gelangen.

    ![Genehmiger des Unternehmens angeben](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Geben Sie die Zahlungsinformationen für Ihr Konto ein. Wenn Sie einen Aktionscode haben, der die Kosten der Registrierung abdeckt, können Sie ihn hier eingeben. Geben Sie andernfalls Ihre Kreditkartendaten an (oder PayPal-Daten, wenn dies für Ihren Markt unterstützt wird). Klicken Sie auf **Weiter**, um zur abschließenden **Überprüfung** zu gelangen.

   ![Registrierung für Bezahlung](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Überprüfen Sie Ihre Kontoinformationen, und bestätigen Sie, dass alles korrekt ist. Lesen und akzeptieren Sie die Nutzungsbedingungen der [Microsoft Azure Marketplace-Herausgebervereinbarung](https://go.microsoft.com/fwlink/?LinkID=699560). Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie die Bedingungen gelesen und akzeptiert haben.

11. Wählen Sie **Fertig stellen**, um die Registrierung zu bestätigen. Es wird eine Bestätigungsnachricht an Ihre E-Mail-Adresse gesendet.

12. Wenn Sie planen, nur kostenlose Angebote zu veröffentlichen, wählen Sie [Zum Cloud-Partnerportal wechseln](https://cloudpartner.azure.com/) aus, und springen Sie zu „Registrieren Ihres Kontos im Cloudpartnerportal“ in diesem Artikel.

### <a name="commercial-offers"></a>Kommerzielle Angebote

Wenn Sie planen, kommerzielle Angebote veröffentlichen, etwa ein Angebot für virtuelle Computer, für das ein stündliches Abrechnungsmodell verwendet werden soll, müssen Sie Steuer- und Bankverbindungsinformationen bereitstellen. Dazu melden Sie sich bei Ihrem Developer Center-Konto an, und wählen Sie **Kontoinformationen aktualisieren** aus. Befolgen Sie die Anweisungen im nächsten Abschnitt, „Hinzufügen der Bankverbindungs- und Steuerinformationen“.

>[!IMPORTANT]
>Sie können kein kommerzielles Angebot zur Veröffentlichung freischalten, wenn Sie keine Bankkonto- und Steuerinformationen bereitgestellt haben.

Wenn Sie Ihre Bank- und Steuerinformationen lieber später aktualisieren möchten, können Sie zu „Registrieren Ihres Kontos im Cloudpartnerportal“ in diesem Artikel springen.

>[!NOTE]
>Es wird empfohlen, Bankkonto- und Steuerinformationen so bald wie möglich bereitzustellen, weil das Überprüfen der Steuerinformationen einige Zeit dauert.

### <a name="add-banking-and-tax-information"></a>Hinzufügen der Bankverbindungs- und Steuerinformationen

Wenn Sie kommerzielle Angebote veröffentlichen möchten, müssen Sie Auszahlungs- und Steuerinformationen hinzufügen und diese zur Validierung im Developer Center absenden.

**So stellen Sie Bankinformationen bereit**

1.  Melden Sie mit Ihrem Microsoft-Konto am [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) an.
2.  Wählen Sie im Menü auf der linken Seite **Auszahlungskonto** aus, und wählen Sie unter **Zahlungsmethode wählen** die Option **Bankkonto** oder **PayPal** aus.

    >[!NOTE]
    >Wenn Sie über kommerzielle Angebote verfügen, die Kunden im Marketplace erwerben, wird dieses Konto für die entsprechenden Auszahlungen an Sie verwendet.
3.  Geben Sie die Zahlungsinformationen ein, und wählen Sie dann **Speichern** aus.

    >[!IMPORTANT]
    >Wenn Sie Ihr Auszahlungskonto aktualisieren oder ändern müssen, führen Sie die obigen Schritte erneut aus, um die aktuellen Informationen durch die neuen Informationen zu ersetzen.
    >
    >Wenn Sie Ihr Zahlungskonto ändern, kann dies dazu führen, dass sich Ihre Zahlungen um maximal einen Zahlungszyklus verzögern. Diese Verzögerung tritt auf, weil wir die Kontoänderung überprüfen müssen. Dies ist der gleiche Vorgang wie bei der ersten Einrichtung des Zahlungskontos. Nachdem das Konto bestätigt wurde, erhalten Sie trotzdem den vollen Betrag. Zahlungen, die für den aktuellen Zahlungszyklus fällig sind, werden im nächsten Zyklus hinzugefügt.

4.  Klicken Sie auf **Weiter**.

**So stellen Sie Steuerinformationen bereit**

1.  Melden Sie mit Ihrem Microsoft-Konto am [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) an (falls erforderlich).
2.  Wählen Sie im linken Menü die Option **Steuerprofil** aus.
3.  Gehen Sie auf der Seite **Ihr Steuerformular einrichten** wie folgt vor:
    - Wählen Sie das Land oder die Region aus, in dem bzw. der Ihr dauerhafter Wohnsitz liegt.
    - Wählen Sie das Land aus, dessen primäre Staatsbürgerschaft Sie innehaben.
    - Klicken Sie auf **Weiter**.
4.  Geben Sie Ihre Steuerdaten ein, und wählen Sie **Weiter**.

>[!WARNING]
>Sie können Ihre kommerziellen Angebote nicht für die Veröffentlichung freischalten, wenn Sie keine Bankkonto- und Steuerinformationen in Ihrem Microsoft Developer Center-Konto bereitgestellt haben.

### <a name="developer-center-registration-issues"></a>Probleme bei der Developer Center-Registrierung

Wenn Sie Probleme bei der Developer Center-Registrierung haben, gehen Sie wie folgt vor, um ein Supportticket zu öffnen.

1.  Verwenden Sie den [Supportlink](https://developer.microsoft.com/windows/support).
2.  Wählen Sie unter **So erreichen Sie uns** die Schaltfläche **Incident übermitteln** aus.
    ![Ticket öffnen](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Wählen Sie für **Problemtyp** die Option „Hilfe zu Dev Center“ aus und wählen Sie für **Kategorie** die Option „Veröffentlichen und Verwalten von Apps“ aus. Wählen Sie **E-Mail starten** aus.

    ![Geben Sie den Typ des Problems an.](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Sie gelangen zu einer Anmeldeseite. Verwenden Sie ein beliebiges Microsoft-Konto, um sich anzumelden. Falls Sie kein Microsoft-Konto besitzen, [legen Sie eines an](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).

5.  Stellen Sie ausführliche Informationen zu dem Problem bereit, und wählen Sie **Senden** aus, um das Ticket zu senden.

    ![Ticket senden](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrieren Ihres Kontos im Cloudpartnerportal

Sie verwenden das [Cloud-Partnerportal](https://cloudpartner.azure.com/), um Ihre Angebote zu veröffentlichen und zu verwalten.

1.  Öffnen Sie eine neue Chrome-Inkognito- oder Internet Explorer-InPrivate-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.
2.  Wechseln Sie zum [Cloudpartnerportal](https://cloudpartner.azure.com/).
3.  Wenn Sie sich als neuer Benutzer zum ersten Mal beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) anmelden, müssen Sie dazu die E-Mail-ID verwenden, die für Ihr Dev Center-Konto registriert ist. Dadurch ist sichergestellt, dass Ihr Dev Center-Konto und das Cloud-Partnerportal-Konto miteinander verknüpft werden.

Später können Sie die anderen Mitglieder des Unternehmens hinzufügen, die an der Anwendung arbeiten. Sie können diese als Mitwirkende oder Besitzer im Cloud-Partnerportal hinzufügen, indem Sie die Schritte im nächsten Abschnitt ausführen.

Wenn Sie im Cloudpartnerportal als Mitwirkender/Besitzer hinzugefügt werden, können Sie sich mit Ihrem eigenen Konto anmelden.

>[!TIP]
>Die Teilnahmerichtlinien werden auf der Azure-Website beschrieben.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Verwalten von Benutzern als Besitzer oder Mitwirkende im Cloud-Partnerportal

[Schritte zum Verwalten von Benutzern im Cloudpartnerportal](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihr Konto erstellt und registriert haben, können Sie den Azure Marketplace-Veröffentlichungsprozess starten.
