---
title: Konfigurieren des Workflows für die Administratoreinwilligung – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie für Endbenutzer eine Möglichkeit zur Zugriffsanforderung für Anwendungen konfigurieren, die eine Administratoreinwilligung erfordern.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: b38b8cf38d2d75493ef50c4f206758c6289be079
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259586"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)

In diesem Artikel wird beschrieben, wie Sie den Workflow für die Administratoreinwilligung (Vorschau) aktivieren, mit dem Endbenutzer Zugriff auf Anforderungen anfordern können, für die eine Administratoreinwilligung benötigt wird.

Ohne einen Workflow für die Administratoreinwilligung wird ein Benutzer in einem Mandanten mit deaktivierter Benutzereinwilligung blockiert, wenn er versucht, auf eine Anwendung zuzugreifen, die Berechtigungen für den Zugriff auf Unternehmensdaten erfordert. Der Benutzer wird in einer allgemeinen Fehlermeldung darüber informiert, dass er nicht zum Zugriff auf die App autorisiert ist und sich zwecks Unterstützung an den Administrator wenden soll. Häufig weiß der Benutzer aber nicht, an wen er sich wenden muss, sodass er entweder aufgibt oder ein neues lokales Konto in der Anwendung erstellt. Und selbst wenn ein Administrator benachrichtigt wird, ist nicht immer ein optimierter Prozess vorhanden, mit dem der Administrator den Zugriff gewähren und die Benutzer benachrichtigen kann.
 
Der Workflow für die Administratoreinwilligung bietet Administratoren eine sichere Möglichkeit zum Gewähren von Zugriff auf Anwendungen, die eine Administratorgenehmigung erfordern. Wenn ein Benutzer versucht, auf eine Anwendung zuzugreifen, aber selbst keine Einwilligung erteilen kann, kann er eine Anforderung zur Administratorgenehmigung senden. Die Anforderung wird per E-Mail an die Administratoren gesendet, die als Prüfer festgelegt wurden. Ein Prüfer ergreift Maßnahmen im Hinblick auf die Anforderung, und der Benutzer wird über den Vorgang informiert.

Um Anforderungen zu genehmigen, muss ein Prüfer als globaler Administrator, Anwendungsadministrator oder Cloudanwendungsadministrator fungieren. Der Prüfer muss einer dieser Administratorrollen angehören, durch eine einfache Zuweisung als Prüfer werden nicht die erforderlichen Berechtigungen erteilt.

## <a name="enable-the-admin-consent-workflow"></a>Aktivieren des Workflows für die Administratoreinwilligung

So aktivieren Sie den Workflow für die Administratoreinwilligung und wählen Prüfer aus

1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie oben im Navigationsmenü auf der linken Seite auf **Alle Dienste**. Die **Azure Active Directory-Erweiterung** wird geöffnet.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**. 
5. Wählen Sie unter **Verwalten** die Option **Benutzereinstellungen** aus.
6. Legen Sie unter **Anforderungen zur Administratoreinwilligung (Vorschau)** die Option **Benutzer können Administratoreinwilligungen für Apps anfordern, bei denen sie selbst keine Einwilligung erteilen können** auf **Ja** fest.

   ![Konfigurieren von Einstellungen für den Workflow für die Administratoreinwilligung](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Konfigurieren Sie die folgenden Einstellungen:

   * **Benutzer zum Überprüfen von Anforderungen zur Administratoreinwilligung auswählen**: Wählen Sie die Prüfer für diesen Workflow aus Benutzern aus, die der Rolle „Globaler Administrator“, „Cloudanwendungsadministrator“ oder „Anwendungsadministrator“ angehören.
   * **Die ausgewählten Benutzer erhalten E-Mail-Benachrichtigungen zu Anforderungen**: Aktivieren oder deaktivieren Sie Benachrichtigungen an die Prüfer, wenn eine Anforderung gesendet wird.  
   * **Die ausgewählten Benutzer erhalten Erinnerungen zum Ablauf von Anforderungen**: Aktivieren oder deaktivieren Sie Erinnerungs-E-Mail-Benachrichtigungen an die Prüfer, wenn eine Anforderung bald abläuft.  
   * **Einwilligungsanforderung läuft nach (Tagen) ab**: Geben Sie an, wie lange Anforderungen gültig bleiben.

7. Wählen Sie **Speichern** aus. Die Aktivierung der Funktion kann bis zu einer Stunde dauern.

> [!NOTE]
> Sie können Prüfer für diesen Workflow hinzufügen oder entfernen, indem Sie die Liste **Prüfer für Anforderungen zur Administratoreinwilligung** ändern. Beachten Sie, dass aufgrund einer aktuellen Einschränkung dieser Funktion Prüfer auch nach ihrer Entfernung weiterhin Anforderungen überprüfen können, die gesendet wurden, während sie als Prüfer festgelegt waren.

## <a name="how-users-request-admin-consent"></a>Vorgehensweise zur Anforderung einer Administratoreinwilligung durch die Benutzer

Nachdem der Workflow für die Administratoreinwilligung aktiviert wurde, können die Benutzer eine Administratorgenehmigung für eine Anwendung anfordern, für die sie selbst keine Einwilligung erteilen können. Die folgenden Schritte beschreiben das Vorgehen des Benutzers zum Anfordern der Genehmigung. 

1. Der Benutzer versucht, sich bei der Anwendung anzumelden.

2. Die Meldung **Genehmigung erforderlich** wird angezeigt. Der Benutzer gibt ein, warum er Zugriff auf die App benötigt und klickt anschließend auf **Genehmigung anfordern**.

   ![Screenshot: Dialogfeld „Genehmigung erforderlich“, in dem Sie eine Genehmigung anfordern können](media/configure-admin-consent-workflow/end-user-justification.png)

3. In einer Meldung **Anforderung gesendet** wird bestätigt, dass die Anforderung an den Administrator gesendet wurde. Wenn der Benutzer mehrere Anforderungen sendet, wird nur die erste Anforderung an den Administrator übermittelt.

   ![Screenshot: Sendebestätigung für die Anforderung](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Der Benutzer erhält eine E-Mail-Benachrichtigung, wenn die Anforderung genehmigt, abgelehnt oder blockiert wurde. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Überprüfen und Ergreifen von Maßnahmen bei Anforderungen zur Administratoreinwilligung

So überprüfen Sie die Anforderungen zur Administratoreinwilligung und ergreifen Maßnahmen

1. Melden Sie sich beim[Azure-Portal](https://portal.azure.com) als einer der registrierten Prüfer für den Workflow für die Administratoreinwilligung an.
2. Klicken Sie oben im Navigationsmenü auf der linken Seite auf **Alle Dienste**. Die **Azure Active Directory-Erweiterung** wird geöffnet.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Klicken Sie im Navigationsmenü auf **Unternehmensanwendungen**.
5. Wählen Sie unter **Aktivität** die Option **Anforderungen zur Administratoreinwilligung (Vorschau)** aus.

   > [!NOTE]
   > Prüfer sehen nur die Administratoranforderungen, die nach ihrer Festlegung als Prüfer erstellt wurden.

1. Wählen Sie die Anwendung aus, die angefordert wurde.
2. Überprüfen Sie die Details der Anforderung:  

   * Um zu sehen, wer Zugriff beantragt und warum, wählen Sie die Registerkarte **Angefordert von** aus.
   * Um zu sehen, welche Berechtigungen von der Anwendung angefordert werden, wählen Sie **Berechtigungen und Einwilligung überprüfen** aus.

8. Werten Sie die Anforderung aus, und ergreifen Sie die geeignete Maßnahme:

   * **Genehmigen Sie die Anforderung**. Um eine Anforderung zu genehmigen, erteilen Sie die Administratoreinwilligung für die Anwendung. Sobald eine Anforderung genehmigt wurde, werden alle Anforderer darüber informiert, dass ihnen der Zugriff gewährt wurde.  
   * **Lehnen Sie die Anforderung ab**. Um eine Anforderung abzulehnen, müssen Sie eine Begründung angeben, die allen Anforderern vorgelegt wird. Sobald eine Anforderung abgelehnt wurde, werden alle Anforderer darüber informiert, dass ihnen der Zugriff verweigert wurde. Das Ablehnen einer Anforderung hindert die Benutzer nicht daran, in Zukunft erneut die Einwilligung des Administrators für die App einzuholen.  
   * **Blockieren Sie die Anforderung**. Um eine Anforderung zu blockieren, müssen Sie eine Begründung angeben, die allen Anforderern vorgelegt wird. Sobald eine Anforderung blockiert wurde, werden alle Anforderer darüber informiert, dass ihnen der Zugriff verweigert wurde. Durch das Blockieren einer Anforderung wird in Ihrem Mandanten ein Dienstprinzipalobjekt für die Anwendung erstellt, das sich in einem deaktivierten Zustand befindet. Benutzer können in Zukunft keine Einwilligung des Administrators für die Anwendung mehr anfordern.
 
## <a name="email-notifications"></a>E-Mail-Benachrichtigungen
 
Sofern konfiguriert, erhalten alle Prüfer in den folgenden Fällen E-Mail-Benachrichtigungen:

* Eine neue Anforderung wurde generiert
* Eine Anforderung ist abgelaufen
* Eine Anforderung läuft in Kürze ab  
 
Anforderer erhalten in folgenden Fällen eine E-Mail-Benachrichtigung:

* Beim Senden einer neuen Zugriffsanforderung
* Bei Ablauf ihrer Anforderung
* Bei Ablehnung oder Blockierung ihrer Anforderung
* Bei Genehmigung ihrer Anforderung
 
## <a name="audit-logs"></a>Überwachungsprotokolle 
 
Die folgende Tabelle zeigt die Szenarien und Überwachungswerte, die für den Workflow für die Administratoreinwilligung zur Verfügung stehen. 

> [!NOTE]
> Der Benutzerkontext des Überwachungsakteurs fehlt aktuell in allen Szenarien. Dies ist eine bekannte Einschränkung der Vorschauversion.


|Szenario  |Überwachungsdienst  |Überwachungskategorie  |Überwachungsaktivität  |Überwachungsakteur  |Überwachungsprotokolleinschränkungen  |
|---------|---------|---------|---------|---------|---------|
|Administrator aktiviert den Workflow für die Administratoreinwilligung        |Zugriffsüberprüfungen           |UserManagement           |Governancerichtlinienvorlage erstellen          |App-Kontext            |Aktuell ist eine Ermittlung des Benutzerkontexts nicht möglich.            |
|Der Administrator deaktiviert den Workflow zum Anfordern der Administratoreinwilligung.       |Zugriffsüberprüfungen           |UserManagement           |Governancerichtlinienvorlage löschen          |App-Kontext            |Aktuell ist eine Ermittlung des Benutzerkontexts nicht möglich.           |
|Administrator aktualisiert die Konfiguration des Workflows für die Administratoreinwilligung        |Zugriffsüberprüfungen           |UserManagement           |Governancerichtlinienvorlage aktualisieren          |App-Kontext            |Aktuell ist eine Ermittlung des Benutzerkontexts nicht möglich.           |
|Der Endbenutzer erstellt eine Anforderung zur Administratoreinwilligung für eine App.       |Zugriffsüberprüfungen           |Richtlinie         |Anforderung erstellen           |App-Kontext            |Aktuell ist eine Ermittlung des Benutzerkontexts nicht möglich.           |
|Die Prüfer genehmigen eine Anforderung zur Administratoreinwilligung.       |Zugriffsüberprüfungen           |UserManagement           |Alle Anforderungen im Geschäftsflow genehmigen          |App-Kontext            |Aktuell ist eine Ermittlung des Benutzerkontexts oder der App-ID, für die eine Administratoreinwilligung erteilt wurde, nicht möglich.           |
|Die Prüfer lehnen eine Anforderung zur Administratoreinwilligung ab.       |Zugriffsüberprüfungen           |UserManagement           |Alle Anforderungen im Geschäftsflow genehmigen          |App-Kontext            | Aktuell ist eine Ermittlung des Benutzerkontexts des Akteurs, der eine Anforderung zur Administratoreinwilligung abgelehnt hat, nicht möglich.          |

## <a name="faq"></a>Häufig gestellte Fragen 

**Ich habe diesen Workflow aktiviert. Warum wird beim Testen der Funktionalität die neue Eingabeaufforderung „Genehmigung erforderlich“ nicht angezeigt, über die ich Zugriff anfordern kann?**

Nach dem Aktivieren der Funktion kann es bis zu 60 Minuten dauern, bis die Endbenutzer das Update sehen. Sie können die ordnungsgemäße Konfiguration überprüfen, indem Sie den Wert **EnableAdminConsentRequests** in der `https://graph.microsoft.com/beta/settings`-API anzeigen.

**Warum kann ich als Prüfer nicht alle ausstehenden Anforderungen sehen?**

Prüfer können nur die Administratoranforderungen sehen, die nach ihrer Festlegung als Prüfer erstellt wurden. Wenn Sie erst vor Kurzem als Prüfer hinzugefügt wurden, werden Ihnen keine Anforderungen angezeigt, die vor Ihrer Zuweisung als Prüfer erstellt wurden.

**Warum sehe ich als Prüfer mehrere Anforderungen für dieselbe Anwendung?**
  
Wenn ein Anwendungsentwickler seine Anwendung zur Verwendung der statischen und der dynamischen Einwilligung konfiguriert hat, um Zugriff auf die Daten ihres Endbenutzers anzufordern, sehen Sie zwei Anforderungen zur Administratoreinwilligung. Eine Anforderung repräsentiert die statischen Berechtigungen, die andere die dynamischen Berechtigungen.

**Kann ich als Anforderer den Status meiner Anforderung überprüfen?**  

Nein, aktuell erhalten Anforderer Updates nur per E-Mail-Benachrichtigung.

**Habe ich als Prüfer die Möglichkeit, eine Anwendung nicht für alle Benutzer zu genehmigen?**
 
Wenn Sie Bedenken haben, die Administratoreinwilligung zu erteilen und allen Benutzern im Mandanten die Nutzung der Anwendung zu ermöglichen, empfehlen wir Ihnen, die Anforderung abzulehnen. Erteilen Sie dann manuell die Administratoreinwilligung, indem Sie den Zugriff auf die Anwendung durch die Anforderung einer Benutzerzuweisung einschränken. Anschließend können Sie der Anwendung Benutzer oder Gruppen zuweisen. Weitere Informationen finden Sie unter [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

[Konfigurieren der Art und Weise, wie Endbenutzer Anwendungen zustimmen können](configure-user-consent.md)

[Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md)

[Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)

[Azure AD bei Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)