---
title: Authentifizierung mit Einmalkennung für B2B-Gastbenutzer – Azure AD
description: Verwenden der Einmalkennung per E-Mail zum Authentifizieren von B2B-Gastbenutzern ohne Einrichten eines Microsoft-Kontos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15debb69172dba00163950fdd301826c903e5307
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548328"
---
# <a name="email-one-time-passcode-authentication"></a>Authentifizierung mit Einmalkennung per E-Mail

In diesem Artikel wird das Aktivieren der Authentifizierung mit Einmalkennung per E-Mail für B2B-Gastbenutzer beschrieben. Mit der Funktion „Einmalkennung per E-Mail“ werden B2B-Gastbenutzer authentifiziert, wenn sie über andere Wege (z. B. über Azure AD, ein Microsoft-Konto (MSA) oder den Verbund mit Google) nicht authentifiziert werden können. Bei der Authentifizierung mit Einmalkennung ist es nicht erforderlich, ein Microsoft-Konto zu erstellen. Wenn der Gastbenutzer eine Einladung einlöst oder auf eine freigegebene Ressource zugreift, kann er einen temporären Code anfordern, der an seine E-Mail-Adresse gesendet wird. Anschließend gibt er diesen Code ein, um den Anmeldevorgang fortzusetzen.

![Einmalkennung per E-Mail, Übersichtsdiagramm](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **Ab März 2021** wird die Funktion „Einmalkennung per E-Mail“ für alle vorhandenen Mandanten und standardmäßig bei neuen Mandanten aktiviert. Wenn Sie nicht möchten, dass diese Funktion automatisch aktiviert wird, können Sie sie deaktivieren. Lesen Sie dazu [Deaktivieren der Einmalkennung per E-Mail](#disable-email-one-time-passcode) weiter unten.

> [!NOTE]
> Benutzer mit Einmalkennung müssen sich über einen Link anmelden, der den Mandantenkontext enthält (z.B. `https://myapps.microsoft.com/?tenantid=<tenant id>` oder `https://portal.azure.com/<tenant id>` bzw. `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` bei einer überprüften Domäne). Direkte Links zu Anwendungen und Ressourcen funktionieren ebenfalls, sofern sie den Mandantenkontext enthalten. Gastbenutzer können sich derzeit nicht über Endpunkte, die keinen Mandantenkontext aufweisen, anmelden. Bei der Verwendung von `https://myapps.microsoft.com` tritt bei `https://portal.azure.com` ein Fehler auf.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Benutzeroberfläche für Gastbenutzer mit Einmalkennung

Wenn die Funktion „Einmalkennung per E-Mail“ aktiviert ist, wird für neu eingeladene Benutzer, [die bestimmte Bedingungen erfüllen](#when-does-a-guest-user-get-a-one-time-passcode), die Authentifizierung per Einmalkennung verwendet. Gastbenutzer, die ihre Einladung schon vor dem Aktivieren der Einmalkennung per E-Mail eingelöst haben, werden weiterhin mit der bisherigen Methode authentifiziert.

Bei der Authentifizierung mit Einmalkennung kann der Gastbenutzer seine Einladung über einen direkten Link oder mithilfe der Einladungs-E-Mail einlösen. In beiden Fällen gibt eine Nachricht im Browser an, dass ein Code an die E-Mail-Adresse des Gastbenutzers gesendet wird. Der Gastbenutzer klickt auf **Code senden**:

   ![Screenshot mit der Schaltfläche „Code senden“](media/one-time-passcode/otp-send-code.png)

Eine Kennung wird an die E-Mail-Adresse des Benutzers gesendet. Der Benutzer ruft die Kennung aus der E-Mail ab und gibt sie im Browserfenster ein:

   ![Screenshot mit der Schaltfläche „Code eingeben“](media/one-time-passcode/otp-enter-code.png)

Der Gastbenutzer wird jetzt authentifiziert und kann entweder die freigegebene Ressource anzeigen oder seinen Anmeldevorgang fortsetzen.

> [!NOTE]
> Einmalkennungen sind 30 Minuten gültig. Nach 30 Minuten ist die jeweilige Einmalkennung nicht mehr gültig, und der Benutzer muss eine neue Kennung anfordern. Benutzersitzungen laufen nach 24 Stunden ab. Danach erhält der Gastbenutzer eine neue Kennung, wenn er auf die Ressource zugreift. Der Ablauf der Sitzung sorgt für zusätzliche Sicherheit, besonders wenn ein Gastbenutzer das Unternehmen verlässt oder den Zugriff nicht mehr benötigt.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wann erhält ein Gastbenutzer eine Einmalkennung?

Wenn ein Gastbenutzer eine Einladung einlöst oder einen Link zu einer Ressource verwendet, die für ihn freigegeben wurde, erhält er unter folgenden Bedingungen eine Einmalkennung:

- Er hat kein Azure AD-Konto
- Er hat kein Microsoft-Konto
- Der einladende Mandant hat keinen Verbund mit Google für @gmail.com- und @googlemail.com- Benutzer eingerichtet

Zum Zeitpunkt der Einladung gibt es keinen Hinweis darauf, dass der eingeladene Benutzer die Authentifizierung mit Einmalkennung verwendet. Wenn sich der Gastbenutzer jedoch anmeldet, wird die Authentifizierung mit Einmalkennung als alternative Methode verwendet, wenn keine anderen Authentifizierungsmethoden eingesetzt werden können.

Sie können sehen, ob sich ein Gastbenutzer mit einer Einmalkennung authentifiziert, indem Sie in den Benutzerdetails die Eigenschaft **Quelle** anzeigen. Wechseln Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer**, und wählen Sie dann den Benutzer aus, um die Detailseite zu öffnen.

![Screenshot mit einem Benutzer mit Einmalkennung mit dem Quellwert „OTP“](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Wenn ein Benutzer eine Einmalkennung einlöst und später ein MSA, ein Azure AD-Konto oder ein anderes Verbundkonto erhält, wird er weiterhin mit einer Einmalkennung authentifiziert. Wenn Sie seine Authentifizierungsmethode aktualisieren möchten, können Sie sein Gastbenutzerkonto löschen und ihn erneut einladen.

### <a name="example"></a>Beispiel

Gastbenutzer teri@gmail.com wird von Fabrikam eingeladen. Das Unternehmen hat keinen Verbund mit Google eingerichtet. Teri hat kein Microsoft-Konto. Er erhält für die Authentifizierung eine Einmalkennung.

## <a name="disable-email-one-time-passcode"></a>Deaktivieren der Einmalkennung per E-Mail

Ab März 2021 wird die Funktion „Einmalkennung per E-Mail“ für alle vorhandenen Mandanten und standardmäßig bei neuen Mandanten aktiviert. Ab diesem Zeitpunkt wird das Einlösen von Einladungen durch die Erstellung nicht verwalteter Azure AD-Konten und -Mandanten („viral“ oder „Just-In-Time“) für B2B-Zusammenarbeitsszenarien von Microsoft nicht mehr unterstützt. Wir aktivieren die Funktion „Einmalkennung per E-Mail“, da sie eine nahtlose alternative Authentifizierungsmethode für Ihre Gastbenutzer bietet. Sie können diese Funktion jedoch deaktivieren, wenn Sie sie nicht verwenden möchten.

> [!NOTE]
>
> Wenn Sie die in Ihrem Mandanten aktivierte Funktion „Einmalkennung per E-Mail“ deaktivieren, können sich alle Gastbenutzer, die eine Einmalkennung eingelöst haben, nicht anmelden. Sie können die Gastbenutzer löschen und neu einladen, damit sie sich mit einer anderen Authentifizierungsmethode anmelden können.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>So deaktivieren Sie die Funktion „Einmalkennung per E-Mail“

1. Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.

3. Wählen Sie **Externe Identitäten** > **Einstellungen für externe Zusammenarbeit** aus.

4. Wählen Sie unter **Einmalkennung per E-Mail für Gastbenutzer** die Option **Einmalkennung per E-Mail für Gastbenutzer deaktivieren** aus.

    ![Einstellungen für die Einmalkennung per E-Mail](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Wenn anstelle der oben dargestellten Optionen die folgende Umschaltfläche angezeigt wird, bedeutet dies, dass Sie zuvor die Funktion aktiviert oder deaktiviert bzw. die Vorschauversion genutzt haben. Wählen Sie **Nein** aus, um die Funktion zu deaktivieren.
   >
   >![Aktivieren der Einmalkennung per E-Mail für Nutzer der Vorschauversion](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Wählen Sie **Speichern** aus.

## <a name="note-for-public-preview-customers"></a>Hinweis für Kunden der öffentlichen Vorschauversion

Wenn Sie die öffentliche Vorschauversion der Funktion „Einmalkennung per E-Mail“ genutzt haben, gilt die automatische Funktionsaktivierung im März 2021 nicht für Sie, sodass ihre zugehörigen Geschäftsprozesse nicht beeinträchtigt werden. Außerdem wird im Azure-Portal in den Eigenschaften der Funktion **Einmalkennung per E-Mail für Gastbenutzer** nicht die Option **Einmalkennung per E-Mail für Gastbenutzer im März 2021 automatisch aktivieren** angezeigt. Stattdessen wird die folgende Umschaltfläche (**Ja**/**Nein**) angezeigt:

![Aktivieren der Einmalkennung per E-Mail für Nutzer der Vorschauversion](media/delegate-invitations/enable-email-otp-opted-in.png)

Wenn Sie die Vorschauversion nicht mehr nutzen und das automatische Aktivieren der Funktion im März 2021 zulassen möchten, können Sie die Standardeinstellungen wiederherstellen. Verwenden Sie dazu in der Microsoft Graph-API den [Ressourcentyp „emailAuthenticationMethodConfiguration“](https://aka.ms/exid-graphemailauth). Nachdem Sie die Standardeinstellungen wiederhergestellt haben, stehen unter **Einmalkennung per E-Mail für Gastbenutzer** die folgenden Optionen zur Verfügung:

- **Einmalkennung per E-Mail für Gastbenutzer im März 2021 automatisch aktivieren**. (Standardeinstellung) Wenn die Funktion „Einmalkennung per E-Mail“ für Ihren Mandanten noch nicht aktiviert ist, wird sie im März 2021 automatisch aktiviert. Wenn die Funktion zu diesem Zeitpunkt aktiviert werden soll, ist keine weitere Aktion erforderlich. Wenn Sie die Funktion bereits aktiviert oder deaktiviert haben, ist diese Option nicht verfügbar.

- **Einmalkennung per E-Mail für Gastbenutzer sofort aktivieren**. Aktiviert die Funktion „Einmalkennung per E-Mail“ für Ihren Mandanten.

- **Einmalkennung per E-Mail für Gastbenutzer deaktivieren**. Deaktiviert die Funktion „Einmalkennung per E-Mail“ für Ihren Mandanten und verhindert, dass die Funktion im März 2021 aktiviert wird.
