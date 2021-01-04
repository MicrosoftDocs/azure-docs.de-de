---
title: Verwalten von Administratorkonto für den Notfallzugriff – Azure AD | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Konten für den Notfallzugriff verhindern können, versehentlich aus Ihrer Azure Active Directory-Organisation (Azure AD) ausgeschlossen zu werden.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10d93b92f3bb0adfe734ad439079afdfcaa6270e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834437"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Verwalten von Konten für den Notfallzugriff in Azure AD

Es ist wichtig, dass Sie verhindern, versehentlich aus Ihrer Azure Active Directory-Organisation (Azure AD) ausgeschlossen zu werden, weil Sie sich nicht anmelden oder ein Konto eines anderen Benutzers als Administrator aktivieren können. Sie können die Auswirkungen eines versehentlichen Verlusts des Administratorzugriffs abmildern, indem Sie mindestens zwei *Konten für den Notfallzugriff* in Ihrer Organisation erstellen.

Konten für den Notfallzugriff verfügen über umfangreiche Berechtigungen und werden keinen Einzelpersonen zugewiesen. Konten für den Notfallzugriff sind auf Notfallsituationen oder Szenarien beschränkt, in denen normale Administratorkonten nicht verwendet werden können. Sie sollten die Verwendung der Notfallkonten ausschließlich auf Fälle beschränken, in denen dies absolut notwendig ist.

Dieser Artikel enthält Richtlinien zum Verwalten von Konten für den Notfallzugriff in Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Gründe zur Verwendung eines Kontos für den Notfallzugriff

Eine Organisation könnte beispielsweise in den folgenden Situationen auf ein Konto für den Notfallzugriff zurückgreifen:

- Die Benutzerkonten befinden sich in einem Verbund, und der Verbund ist aktuell nicht verfügbar, weil ein Mobilfunknetz oder ein Identitätsanbieter ausgefallen ist. Wenn beispielsweise der Identitätsanbieterhost in Ihrer Umgebung nicht erreichbar ist, können sich die Benutzer möglicherweise nicht anmelden, wenn sie von Azure AD an ihren Identitätsanbieter umgeleitet werden.
- Die Administratoren sind über Azure AD Multi-Factor Authentication registriert, und keines der von ihnen verwendeten Geräte ist verfügbar, oder der Dienst ist nicht verfügbar. Benutzer können möglicherweise keine mehrstufige Authentifizierung durchführen, um eine Rolle zu aktivieren. Beispielsweise können bei einem Ausfall des Mobilfunknetzes keine Anrufe entgegengenommen oder SMS empfangen werden, womit die einzigen registrierten Authentifizierungsmechanismen für ihr Gerät wegfallen.
- Die Person, die zuletzt über globalen Administratorzugriff verfügte, hat die Organisation verlassen. Azure AD verhindert, dass das letzte globale Administratorkonto gelöscht wird, aber das lokale Löschen oder Deaktivieren des Kontos wird nicht verhindert. Beide Situationen können dazu führen, dass die Organisation nicht in der Lage ist, das Konto wiederherzustellen.
- Bei unvorhersehbaren Ereignissen wie Naturkatastrophen, die dazu führen, dass Mobiltelefone oder andere Netzwerke nicht verfügbar sind. 

## <a name="create-emergency-access-accounts"></a>Erstellen von Konten für den Notfallzugriff

Erstellen Sie mindestens zwei Konten für den Notfallzugriff. Bei diesen Konten sollte es sich um reine Cloudkonten handeln, die die Domäne „\*.onmicrosoft.com“ verwenden und keine Verbundkonten oder Konten darstellen, die über eine lokale Umgebung synchronisiert werden.

Beim Konfigurieren dieser Konten müssen die folgenden Anforderungen erfüllt werden:

- Die Konten für den Notfallzugriff dürfen keinem Einzelbenutzer in der Organisation zugeordnet werden. Stellen Sie sicher, dass Ihre Konten nicht mit Mobiltelefonen von Mitarbeitern, Hardwaretoken einzelner Mitarbeiter oder anderen mitarbeiterspezifischen Anmeldeinformationen verbunden sind. Durch diese Vorsichtsmaßnahme werden Fälle abgedeckt, in denen einzelne Mitarbeiter nicht erreichbar sind, wenn die Anmeldeinformationen benötigt werden. Es muss unbedingt sichergestellt werden, dass alle registrierten Geräte an einem bekannten, sicheren Ort aufbewahrt werden, die über verschiedene Wege mit Azure AD kommunizieren können.
- Der für ein Konto für den Notfallzugriff verwendete Authentifizierungsmechanismus sollte sich von dem unterscheiden, der für Ihre anderen Administratorkonten einschließlich anderer Konten für den Notfallzugriff verwendet wird.  Erfolgt beispielsweise die normale Administratoranmeldung über lokale MFA, wäre Azure AD MFA ein anderer Mechanismus.  Wenn jedoch Azure AD MFA die primäre Authentifizierungsmethode für Ihre Administratorkonten ist, sollten Sie einen anderen Ansatz für diese Konten in Betracht ziehen. Hier käme z. B. die Verwendung von bedingtem Zugriff mit einem MFA-Drittanbieter über benutzerdefinierte Steuerelemente infrage.
- Die jeweiligen Geräte oder die Anmeldeinformationen dürfen nicht ablaufen oder aufgrund mangelnder Verwendung in den Bereich der automatisierten Bereinigung fallen.  
- Sie sollten die Rolle „Globaler Administrator“ für Ihre Konten für den Notfallzugriff dauerhaft zuweisen. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Ausschließen mindestens eines Kontos aus der telefonbasierten mehrstufigen Authentifizierung

Um das Risiko von Angriffen zu verringern, die aus kompromittierten Kennwörtern resultieren, empfiehlt das Azure AD-Team, die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für alle individuellen Benutzer als verbindlich festzulegen. Diese Gruppe umfasst Administratoren und alle weiteren Benutzer (z. B. Mitarbeiter der Finanzabteilung), bei denen ein kompromittiertes Konto erhebliche Auswirkungen haben kann.

Für mindestens eines Ihrer Konten für den Notfallzugriff sollte jedoch nicht der gleiche MFA-Mechanismus wie für Ihre anderen (normalen) Konten verwendet werden. Dies schließt Drittanbieterlösungen für die mehrstufige Authentifizierung ein. Wenn Sie eine Richtlinie für bedingten Zugriff festgelegt haben, um [Multi-Factor Authentication für alle Administratoren](../authentication/howto-mfa-userstates.md) für Azure AD und andere verbundene Software-as-a-Service-Apps (SaaS-Apps) zu erzwingen, sollten Sie die Konten für den Notfallzugriff aus dieser Anforderung ausschließen und stattdessen einen anderen Mechanismus konfigurieren. Darüber hinaus sollten Sie sicherstellen, dass für die Konten keine Richtlinie für die mehrstufige Authentifizierung pro Benutzer festgelegt ist.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Ausschließen mindestens eines Kontos aus Richtlinien für bedingten Zugriff

In einem Notfall soll eine Richtlinie Ihren Zugriff nicht potenziell blockieren, um ein Problem zu beheben. Mindestens ein Konto für den Notfallzugriff sollte aus allen Richtlinien für bedingten Zugriff ausgeschlossen werden.

## <a name="federation-guidance"></a>Verbundleitfaden

Eine weitere Option für Organisationen, die Active Directory Domain Services und AD FS oder einen ähnlichen Identitätsanbieter für den Verbund mit Azure AD verwenden, ist das Konfigurieren eines Kontos für den Notfallzugriff, dessen MFA-Anspruch von diesem Identitätsanbieter angegeben werden könnte.  Das Konto für den Notfallzugriff könnte z. B. durch ein Zertifikat und ein Schlüsselpaar gesichert werden, wie beispielsweise eines, das auf einer Smartcard gespeichert ist.  Wenn der Benutzer für Azure Active Directory authentifiziert wird, kann AD FS einen Anspruch für Azure AD angeben, der darauf hinweist, dass der Benutzer die MFA-Anforderungen erfüllt.  Auch bei diesem Ansatz müssen Organisationen für den Fall, dass kein Verbund hergestellt werden kann, weiterhin über cloudbasierte Konten für den Notfallzugriff verfügen. 

## <a name="store-account-credentials-safely"></a>Sicheres Speichern der Anmeldeinformationen für Konten

Organisationen müssen sicherstellen, dass die Anmeldeinformationen für Konten für den Notfallzugriff sicher aufbewahrt werden und nur den Personen bekannt sind, die für deren Verwendung autorisiert sind. Einige Kunden verwenden eine Smartcard, und andere verwenden Kennwörter. Ein Kennwort für ein Konto für den Notfallzugriff wird in der Regel in zwei oder drei Teile unterteilt, die separat auf Papier festgehalten und in geschützten, feuerfesten Tresoren hinterlegt werden, die sich an sicheren, getrennten Standorten befinden.

Stellen Sie bei Verwendung von Kennwörtern sicher, dass die Konten über sichere Kennwörter verfügen, die nicht ablaufen. Im Idealfall sollten die Kennwörter mindestens 16 Zeichen umfassen und zufällig generiert werden.

## <a name="monitor-sign-in-and-audit-logs"></a>Überwachen von Anmeldungs- und Überwachungsprotokollen

Organisationen sollten die von den Notfallkonten ausgehenden Anmelde- und Überwachungsprotokollaktivitäten überwachen und Benachrichtigungen anderer Administratoren auslösen. Wenn Sie die Aktivität von Konten für den Notfallzugriff überwachen, können Sie sicherstellen, dass diese Konten nur für Tests oder tatsächliche Notfälle verwendet werden. Sie können mit Azure Log Analytics die Anmeldeprotokolle überwachen und E-Mail- und SMS-Warnungen an Ihre Administratoren senden, wenn sich Konten für den Notfallzugriff anmelden.

### <a name="prerequisites"></a>Voraussetzungen

1. [Senden Sie Azure AD-Anmeldeprotokolle](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) an Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Abrufen von Objekt-IDs der Konten für den Notfallzugriff

1. Melden Sie sich mit einem Konto beim [Azure-Portal](https://portal.azure.com) an, das der Benutzeradministratorrolle zugewiesen ist.
1. Wählen Sie **Azure Active Directory** > **Benutzer** aus.
1. Suchen Sie nach dem Konto für den Notfallzugriff, und wählen Sie den Namen des Benutzers aus.
1. Kopieren und speichern Sie das Objekt-ID-Attribut, damit Sie es später verwenden können.
1. Wiederholen Sie die vorherigen Schritte für das zweite Konto für den Notfallzugriff.

### <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das der Rolle „Mitwirkender an der Überwachung“ in Azure Monitor zugewiesen ist.
1. Wählen Sie **Alle Dienste** aus, geben Sie „Log Analytics“ in „Suchen“ ein, und wählen Sie dann **Log Analytics-Arbeitsbereiche** aus.
1. Wählen Sie einen Arbeitsbereich aus.
1. Wählen Sie in Ihrem Arbeitsbereich **Warnungen** > **Neue Warnungsregel** aus.
    1. Überprüfen Sie unter **Ressource**, ob es sich bei dem Abonnement um das Abonnement handelt, dem Sie die Warnungsregel zuordnen möchten.
    1. Wählen Sie unter **Bedingung** die Option **Hinzufügen** aus.
    1. Wählen Sie **Benutzerdefinierte Protokollsuche** unter **Signalname** aus.
    1. Geben Sie unter **Suchabfrage** die folgende Abfrage ein, und fügen Sie die Objekt-IDs der beiden Konten für den Notfallzugriff ein.
        > [!NOTE]
        > Fügen Sie für jedes weitere Konto für den Notfallzugriff, das Sie einschließen möchten, ein weiteres „or UserId == "ObjectGuid"“ in die Abfrage ein.

        ![Hinzufügen der Objekt-IDs der Konten für den Notfallzugriff zu einer Warnungsregel](./media/security-emergency-access/query-image1.png)

    1. Geben Sie unter **Warnungslogik** Folgendes ein:

        - Basierend auf: Anzahl der Ergebnisse
        - Operator: Größer als
        - Schwellenwert: 0

    1. Wählen Sie unter **Auswertung basierend auf** den **Zeitraum (in Minuten)** aus, für den die Abfrage ausgeführt werden soll, und die **Häufigkeit (in Minuten)** , mit der die Abfrage ausgeführt werden soll. Die Häufigkeit sollte kleiner als der Zeitraum oder gleich sein.

        ![Warnungslogik](./media/security-emergency-access/alert-image2.png)

    1. Wählen Sie **Fertig** aus. Sie können jetzt die geschätzten monatlichen Kosten dieser Warnung anzeigen.
1. Wählen Sie eine Aktionsgruppe von Benutzern aus, die durch die Warnung benachrichtigt werden soll. Wenn Sie eine erstellen möchten, finden Sie weitere Informationen unter [Erstellen einer Aktionsgruppe](#create-an-action-group).
1. Zum Anpassen der E-Mail-Benachrichtigung, die an die Mitglieder der Aktionsgruppe gesendet wird, wählen Sie unter **Aktionen anpassen** Aktionen aus.
1. Geben Sie unter **Warnungsdetails** den Namen der Warnungsregel an, und fügen Sie eine optionale Beschreibung hinzu.
1. Legen Sie den **Schweregrad** des Ereignisses fest. Sie sollten ihn auf **Kritisch (Schweregrad 0)** festlegen.
1. Behalten Sie unter **Regel beim Erstellen aktivieren** die Einstellung **ja** bei.
1. Wenn Sie Warnungen für eine Weile deaktivieren möchten, aktivieren Sie das Kontrollkästchen **Warnungen unterdrücken**, geben Sie die Wartezeit vor der Warnung erneut ein, und wählen Sie dann **Speichern** aus.
1. Klicken Sie auf **Warnungsregel erstellen**.

### <a name="create-an-action-group"></a>Erstellen einer Aktionsgruppe

1. Wählen Sie **Erstellen einer Aktionsgruppe** aus.

    ![Erstellen einer Aktionsgruppe für Benachrichtigungsaktionen](./media/security-emergency-access/action-group-image3.png)

1. Geben Sie den Namen für die Aktionsgruppe und einen kurzen Namen ein.
1. Überprüfen Sie Abonnement und Ressourcengruppe.
1. Wählen Sie unter Aktionstyp **E-Mail/SMS/Push/Sprachanruf** aus.
1. Geben Sie einen Aktionsnamen wie z. B. **Globalen Administrator benachrichtigen** ein.
1. Wählen Sie den **Aktionstyp** **E-Mail/SMS/Push/Sprachanruf** aus.
1. Wählen Sie **Details bearbeiten** aus, um die zu konfigurierenden Benachrichtigungsmethoden auszuwählen, geben Sie die erforderlichen Kontaktinformationen ein, und wählen Sie dann **OK** aus, um die Details zu speichern.
1. Fügen Sie ggf. zusätzliche Aktionen hinzu, die Sie auslösen möchten.
1. Klicken Sie auf **OK**.

## <a name="validate-accounts-regularly"></a>Regelmäßiges Überprüfen der Konten

Wenn Sie Mitarbeiter in der Verwendung und Überprüfung von Konten für den Notfallzugriff schulen, führen Sie mindestens die folgenden Schritte in regelmäßigen Abständen aus:

- Stellen Sie sicher, dass die für die Sicherheitsüberwachung verantwortlichen Mitarbeiter darüber informiert sind, dass eine Kontoüberprüfung stattfindet.
- Stellen Sie sicher, dass der Notfallprozess zur Verwendung dieser Konten dokumentiert und aktuell ist.
- Stellen Sie sicher, dass Administratoren und Sicherheitsbeauftragte, die diese Schritte bei einem Notfall möglicherweise ausführen müssen, entsprechend geschult sind.
- Aktualisieren Sie die Kontoanmeldeinformationen (insbesondere alle Kennwörter) für Ihre Konten für den Notfallzugriff, und überprüfen Sie dann, ob Sie sich mit den Konten für den Notfallzugriff anmelden und damit administrative Aufgaben ausführen können.
- Stellen Sie sicher, dass die Benutzer keine Multi-Factor Authentication oder Self-Service-Kennwortzurücksetzung mit persönlichen Geräten oder Informationen registriert haben. 
- Wenn die Konten für Multi-Factor Authentication mit einem Gerät zur Verwendung während der Anmeldung oder Rollenaktivierung registriert sind, stellen Sie sicher, dass das Gerät für alle Administratoren zugänglich ist, die es bei einem Notfall möglicherweise verwenden müssen. Stellen Sie außerdem sicher, dass das Gerät über mindestens zwei Netzwerkpfade kommunizieren kann, die nicht dasselbe Ausfallverhalten zeigen. Beispiel: Das Gerät kann über ein Drahtlosnetzwerk der Organisation und über das Netz eines Mobilfunkanbieters mit dem Internet kommunizieren.

Diese Schritte sollten in regelmäßigen Abständen und für wichtige Änderungen ausgeführt werden:

- Mindestens alle 90 Tage
- Bei einem Wechsel bei den IT-Mitarbeitern, z. B. bei einem Positionswechsel, beim Ausscheiden eines Mitarbeiters oder bei einer Neueinstellung
- Bei einer Änderung der Azure AD-Abonnements in der Organisation

## <a name="next-steps"></a>Nächste Schritte

- [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](security-planning.md)
- [Hinzufügen von Benutzern in Azure AD](../fundamentals/add-users-azure-active-directory.md) und [Zuweisen der Rolle „Globaler Administrator“ zum neuen Benutzer](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Registrieren für Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md), sofern nicht bereits geschehen
- [Vorgehensweise zum Erzwingen einer zweistufigen Überprüfung für einen Benutzer](../authentication/howto-mfa-userstates.md)
- [Konfigurieren zusätzlicher Schutzmechanismen für globale Administratoren in Microsoft 365](/office365/enterprise/protect-your-global-administrator-accounts) bei Verwendung von Microsoft 365
- [Starten einer Zugriffsüberprüfung für globale Administratoren](../privileged-identity-management/pim-how-to-start-security-review.md) und [Umstellen der vorhandenen globalen Administratoren auf spezifischere Administratorrollen](permissions-reference.md)