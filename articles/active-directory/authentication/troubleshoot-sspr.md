---
title: Problembehandlung bei der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Lernen Sie häufige Probleme und die entsprechenden Lösungsschritte bei der Self-Service-Kennwortzurücksetzung in Azure Active Directory kennen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7099de23b825fa7af203dff9696bb53e23a099c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741115"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Problembehandlung bei der Self-Service-Kennwortzurücksetzung in Azure Active Directory

Mit der Self-Service-Kennwortzurücksetzung (SSPR) in Azure Active Directory (Azure AD) können Benutzer ihre Kennwörter in der Cloud zurücksetzen.

Wenn Sie Probleme mit der Self-Service-Kennwortzurücksetzung haben, können Ihnen die folgende Beschreibung häufiger Fehler und die Schritte zur Problembehandlung helfen. Wenn Sie keine Antwort für Ihr Problem finden, [stehen Ihnen unsere Supportteams stets für weitere Unterstützung zur Verfügung](#contact-microsoft-support).

## <a name="sspr-configuration-in-the-azure-portal"></a>Konfiguration der Self-Service-Kennwortzurücksetzung (SSPR) im Azure-Portal

Wenn Sie Probleme beim Anzeigen oder Konfigurieren von SSPR-Optionen im Azure-Portal haben, überprüfen Sie die folgenden Schritte zur Problembehandlung:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Im Azure-Portal wird unter Azure AD kein Abschnitt für die **Kennwortzurücksetzung** angezeigt.

Die Menüoption **Kennwort zurücksetzen** wird nicht angezeigt, wenn Sie dem Administrator, der den Vorgang durchführt, keine Azure AD-Lizenz zugewiesen haben.

Um dem fraglichen Administratorkonto eine Lizenz zuzuweisen, führen Sie die unter [Zuweisen, Prüfen und Beheben von Problemen mit Lizenzen](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses) beschriebenen Schritte aus.

### <a name="i-dont-see-a-particular-configuration-option"></a>Mir wird eine bestimmte Konfigurationsoption nicht angezeigt.

Viele Benutzeroberflächenelemente werden erst angezeigt, wenn sie benötigt werden. Stellen Sie sicher, dass die Option aktiviert ist, bevor Sie nach den speziellen Konfigurationsoptionen suchen.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Die Registerkarte **Lokale Integration** wird nicht angezeigt.

Die lokale Integration des Kennwortrückschreibens wird nur angezeigt, wenn Sie Azure AD Connect heruntergeladen und dieses Feature konfiguriert haben.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>SSPR-Berichterstellung

Wenn Sie im Azure-Portal Probleme bei der SSPR-Berichterstellung haben, überprüfen Sie die folgenden Schritte zur Problembehandlung:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>In der Überwachungsereigniskategorie **Self-Service-Kennwortverwaltung** werden keine Aktivitätstypen für die Kennwortverwaltung angezeigt.

Dieses Problem kann auftreten, wenn dem Administrator, der diesen Vorgang durchführt, keine Azure AD-Lizenz zugewiesen wurde.

Um dem fraglichen Administratorkonto eine Lizenz zuzuweisen, führen Sie die unter [Zuweisen, Prüfen und Beheben von Problemen mit Lizenzen](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses) beschriebenen Schritte aus.

### <a name="user-registrations-show-multiple-times"></a>Benutzerregistrierungen werden mehrfach angezeigt.

Bei der Benutzerregistrierung wird derzeit jede registrierte Information als separates Ereignis protokolliert.

Wenn Sie diese Daten aggregieren und von flexibleren Darstellungsmöglichkeiten profitieren möchten, können Sie den Bericht herunterladen und die Daten als Pivottabelle in Excel öffnen.

## <a name="sspr-registration-portal"></a>SSPR-Registrierungsportal

Wenn Ihre Benutzer Probleme bei der SSPR-Registrierung haben, überprüfen Sie die folgenden Schritte zur Problembehandlung:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Die Kennwortzurücksetzung ist für das Verzeichnis nicht aktiviert. Dem Benutzer wird möglicherweise ein Fehler angezeigt, der besagt, dass der Administrator die Verwendung dieser Funktion nicht aktiviert hat.

Sie können die Self-Service-Kennwortzurücksetzung für alle Benutzer, für keinen Benutzer oder für ausgewählte Benutzergruppen aktivieren. Über das Azure-Portal kann derzeit nur eine Azure AD-Gruppe für SSPR aktiviert werden. Im Rahmen einer umfassenderen Bereitstellung von SSPR werden geschachtelte Gruppen unterstützt. Stellen Sie sicher, dass den Benutzern in den von Ihnen ausgewählten Gruppen die entsprechenden Lizenzen zugewiesen sind.

Ändern Sie im Azure-Portal die Konfiguration **Self-Service-Kennwortzurücksetzung aktiviert** in *Ausgewählt* oder *Alle*, und wählen Sie dann **Speichern** aus.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Dem Benutzer ist keine Azure AD-Lizenz zugewiesen. Dem Benutzer wird möglicherweise ein Fehler angezeigt, der besagt, dass der Administrator die Verwendung dieser Funktion nicht aktiviert hat.

Über das Azure-Portal kann derzeit nur eine Azure AD-Gruppe für SSPR aktiviert werden. Im Rahmen einer umfassenderen Bereitstellung von SSPR werden geschachtelte Gruppen unterstützt. Stellen Sie sicher, dass den Benutzern in den von Ihnen ausgewählten Gruppen die entsprechenden Lizenzen zugewiesen sind. Überprüfen Sie den vorherigen Schritt zur Problembehandlung, um SSPR nach Bedarf zu aktivieren.

Überprüfen Sie auch die Schritte zur Problembehandlung, um sicherzustellen, dass dem Administrator, der die Konfigurationsoptionen ausführt, eine Lizenz zugewiesen ist. Um dem fraglichen Administratorkonto eine Lizenz zuzuweisen, führen Sie die unter [Zuweisen, Prüfen und Beheben von Problemen mit Lizenzen](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses) beschriebenen Schritte aus.

### <a name="theres-an-error-processing-the-request"></a>Beim Verarbeiten der Anforderung ist ein Fehler aufgetreten.

Generische Fehler bei der SSPR-Registrierung können verschiedene Ursachen haben, werden im Allgemeinen jedoch entweder durch einen Dienstausfall oder einen Konfigurationsfehler verursacht. Wenn der generische Fehler nach Wiederholung des SSPR-Registrierungsprozesses immer noch angezeigt wird, [wenden Sie sich an den Microsoft Support](#contact-microsoft-support), um weitere Unterstützung zu erhalten.

## <a name="sspr-usage"></a>Verwendung der Self-Service-Kennwortzurücksetzung

Wenn Sie oder Ihre Benutzer Probleme bei der Verwendung von SSPR haben, überprüfen Sie die folgenden Problembehandlungsszenarien und Lösungsschritte:

| Fehler | Lösung |
| --- | --- |
| Die Kennwortzurücksetzung ist für das Verzeichnis nicht aktiviert. | Ändern Sie im Azure-Portal die Konfiguration **Self-Service-Kennwortzurücksetzung aktiviert** in *Ausgewählt* oder *Alle*, und wählen Sie dann **Speichern** aus. |
| Dem Benutzer ist keine Azure AD-Lizenz zugewiesen. | Dieses Problem kann auftreten, wenn Sie dem gewünschten Benutzer keine Azure AD-Lizenz zugewiesen haben. Um dem fraglichen Administratorkonto eine Lizenz zuzuweisen, führen Sie die unter [Zuweisen, Prüfen und Beheben von Problemen mit Lizenzen](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses) beschriebenen Schritte aus. |
| Die Kennwortzurücksetzung ist für das Verzeichnis zwar aktiviert, für den Benutzer wurden jedoch nicht alle oder fehlerhafte Authentifizierungsinformationen konfiguriert. | Stellen Sie sicher, dass für den Benutzer ordnungsgemäß formatierte Kontaktdaten im Verzeichnis vorliegen. Weitere Informationen finden Sie unter [Von der Azure AD Self-Service-Kennwortzurücksetzung verwendete Daten](howto-sspr-authenticationdata.md). |
| Die Kennwortzurücksetzung ist für das Verzeichnis zwar aktiviert, für den Benutzer ist jedoch nur ein einzelner Kontaktdatensatz hinterlegt, und die Richtlinie schreibt zwei Überprüfungsmethoden vor. | Stellen Sie sicher, dass für den Benutzer mindestens zwei ordnungsgemäß konfigurierte Kontaktmethoden vorhanden sind, beispielsweise eine Mobiltelefonnummer *und* eine geschäftliche Telefonnummer. |
| Die Kennwortzurücksetzung ist für das Verzeichnis aktiviert, und der Benutzer ist ordnungsgemäß konfiguriert, kann aber nicht kontaktiert werden. | Dieses Problem kann durch einen vorübergehenden Dienstfehler oder durch falsche Kontaktdaten verursacht werden, die nicht ordnungsgemäß erkannt werden können. <br> <br> Wenn der Benutzer 10 Sekunden wartet, werden der Link „Erneut versuchen“ und der Link „Wenden Sie sich an Ihren Administrator“ angezeigt. Wenn der Benutzer „Erneut versuchen“ auswählt, wird der Aufruf wiederholt. Wenn der Benutzer „Wenden Sie sich an Ihren Administrator“ auswählt, wird eine formale E-Mail an die Administratoren gesendet, um eine Kennwortzurücksetzung für dieses Benutzerkonto anzufordern. |
| Der Benutzer erhält keine SMS oder keinen Telefonanruf für die Kennwortzurücksetzung. | Dieses Problem kann durch eine falsch formatierte Telefonnummer im Verzeichnis verursacht werden. Stellen Sie sicher, dass die Telefonnummer das Format „+1 4251234567“ aufweist. <br> <br>Bei Anrufen zur Kennwortzurücksetzung werden keine Durchwahlnummern unterstützt, selbst wenn diese im Verzeichnis angegeben werden. Die Durchwahlnummern werden vor der Durchführung des Anrufs abgeschnitten. Verwenden Sie eine Nummer ohne Durchwahl, oder integrieren Sie die Durchwahl in Ihrer Nebenstellenanlage in die Telefonnummer. |
| Der Benutzer erhält keine E-Mail für die Kennwortzurücksetzung. | Dieses Problem ist meist darauf zurückzuführen, dass die Nachricht durch einen Spamfilter zurückgewiesen wird. Prüfen Sie Ihren Spam- oder Junk-E-Mail-Ordner oder den Ordner "Gelöschte Elemente" auf die E-Mail. <br> <br> Stellen Sie außerdem sicher, dass der Benutzer überprüft, ob es sich um das richtige, mit SSPR registrierte E-Mail-Konto handelt. |
| Ich habe eine Richtlinie für die Kennwortzurücksetzung eingerichtet, aber wenn ein Administrator die Kennwortzurücksetzung verwendet, wird diese Richtlinie nicht angewendet. | Microsoft verwaltet und kontrolliert die Richtlinie für das Zurücksetzen von Administratorkennwörtern, um höchste Sicherheit zu gewährleisten. |
| Der Benutzer wird daran gehindert, ein Kennwort innerhalb eines Tages zu oft zurückzusetzen. | Mithilfe eines automatischen Drosselungsmechanismus werden Benutzer daran gehindert, ihre Kennwörter innerhalb eines kurzen Zeitraums zu oft zurückzusetzen. Die Drosselung erfolgt in den folgenden Szenarien: <br><ul><li>Der Benutzer versucht innerhalb einer Stunde fünfmal, eine Telefonnummer zu bestätigen.</li><li>Der Benutzer versucht innerhalb einer Stunde fünfmal, die Überprüfung mithilfe von Sicherheitsfragen zu verwenden.</li><li>Der Benutzer versucht innerhalb einer Stunde fünfmal, ein Kennwort für dasselbe Benutzerkonto zurückzusetzen.</li></ul>Wenn dieses Problem bei einem Benutzer auftritt, muss er nach dem letzten Versuch 24 Stunden warten. Danach kann der Benutzer sein Kennwort zurücksetzen. |
| Der Benutzer erhält bei der Überprüfung seiner Telefonnummer eine Fehlermeldung. | Dieser Fehler tritt auf, wenn die eingegebene Telefonnummer nicht mit der registrierten Telefonnummer übereinstimmt. Stellen Sie sicher, dass der Benutzer beim Versuch, eine Telefonnummer für die Kennwortzurücksetzung zu verwenden, die vollständige Telefonnummer eingibt – einschließlich Landes- und Ortsvorwahl. |
| Beim Verarbeiten der Anforderung ist ein Fehler aufgetreten. | Generische Fehler bei der SSPR-Registrierung können verschiedene Ursachen haben, werden im Allgemeinen jedoch entweder durch einen Dienstausfall oder einen Konfigurationsfehler verursacht. Wenn der generische Fehler nach Wiederholung des SSPR-Registrierungsprozesses immer noch angezeigt wird, [wenden Sie sich an den Microsoft Support](#contact-microsoft-support), um weitere Unterstützung zu erhalten. |
| Verletzung der lokalen Richtlinie | Das Kennwort entspricht nicht der lokalen Active Directory-Kennwortrichtlinie. Der Benutzer muss ein Kennwort definieren, das den Komplexitätsanforderungen bzw. den Anforderungen an die Kennwortsicherheit entspricht. |
| Das Kennwort entspricht nicht der Fuzzyrichtlinie | Das verwendete Kennwort ist in der [Liste gesperrter Kennwörter](./concept-password-ban-bad.md#how-are-passwords-evaluated) enthalten und kann nicht verwendet werden. Der Benutzer muss ein Kennwort definieren, das der Richtlinie für die Liste gesperrter Kennwörter entspricht oder sicherer ist. |

## <a name="sspr-errors-that-a-user-might-see"></a>Mögliche SSPR-Fehler, die einem Benutzer angezeigt werden

Die folgenden Fehler und technischen Details können einem Benutzer im Rahmen des SSPR-Prozesses angezeigt werden. Häufig können Benutzer den Fehler nicht selbst beheben, da die SSPR-Funktion für das jeweilige Konto aktiviert, konfiguriert oder registriert werden muss.

Verwenden Sie die folgenden Informationen, um das Problem zu verstehen und zu erfahren, was für den Azure AD-Mandanten oder das einzelne Benutzerkonto korrigiert werden muss.

| Fehler | Details | Technische Details |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil Ihr Administrator das Zurücksetzen von Kennwörtern für Ihre Organisation deaktiviert hat. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, dieses Feature zu aktivieren.<br /><br />Weitere Informationen finden Sie unter [Ich habe mein Azure AD-Kennwort vergessen. Was nun?](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: Wir haben festgestellt, dass die Kennwortzurücksetzung von Ihrem Administrator nicht aktiviert wurde. Bitten Sie Ihren Administrator, die Kennwortzurücksetzung für Ihre Organisation zu aktivieren. |
| WritebackNotEnabled = 10 |Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil Ihr Administrator einen notwendigen Dienst für Ihre Organisation nicht aktiviert hat. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, die Konfiguration für Ihre Organisation zu überprüfen.<br /><br />Weitere Informationen zu diesem notwendigen Dienst finden Sie unter [Konfigurieren des Kennwortrückschreibens](./tutorial-enable-sspr-writeback.md). | SSPR_0010: Wir haben festgestellt, dass das Kennwortrückschreiben nicht aktiviert wurde. Bitten Sie Ihren Administrator, das Kennwortrückschreiben zu aktivieren. |
| SsprNotEnabledInUserPolicy = 11 | Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil Ihr Administrator das Zurücksetzen von Kennwörtern für Ihre Organisation nicht konfiguriert hat. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Bitten Sie Ihren Administrator, die Kennwortzurücksetzung zu konfigurieren.<br /><br />Weitere Informationen zur Konfiguration der Kennwortzurücksetzung finden Sie im [Schnellstart: Self-Service-Kennwortzurücksetzung in Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: Ihre Organisation hat keine Richtlinie zur Kennwortzurücksetzung definiert. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, eine Richtlinie zur Kennwortzurücksetzung zu definieren. |
| UserNotLicensed = 12 | Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil Ihrer Organisation die erforderlichen Lizenzen fehlen. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Bitten Sie Ihren Administrator, die Lizenzzuweisung zu prüfen.<br /><br />Weitere Informationen zur Lizenzierung finden Sie unter [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](./concept-sspr-licensing.md). | SSPR_0012: Ihre Organisation besitzt nicht die erforderlichen Lizenzen zum Zurücksetzen von Kennwörtern. Bitten Sie Ihren Administrator, die Lizenzzuweisungen zu überprüfen. |
| UserNotMemberOfScopedAccessGroup = 13 | Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil Ihr Administrator die Verwendung der Kennwortzurücksetzung für Ihr Konto nicht konfiguriert hat. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, Ihr Konto für die Kennwortzurücksetzung zu konfigurieren.<br /><br />Weitere Informationen zur Konfiguration der Kennwortzurücksetzung für ein Konto finden Sie im folgenden Artikel: [Rollout der Kennwortzurücksetzung für Benutzer](./howto-sspr-deployment.md). | SSPR_0013: Sie sind nicht Mitglied einer für die Kennwortzurücksetzung aktivierten Gruppe. Wenden Sie sich an Ihren Administrator, und fordern Sie an, der Gruppe hinzugefügt zu werden. |
| UserNotProperlyConfigured = 14 | Sie können Ihr Kennwort zurzeit nicht zurücksetzen, weil notwendige Informationen für Ihr Konto fehlen. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, Ihr Kennwort für Sie zurückzusetzen. Sobald Sie wieder auf Ihr Konto zugreifen können, müssen Sie die benötigten Informationen registrieren.<br /><br />Wie das geht, erfahren Sie im Artikel [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md). | SSPR_0014: Zusätzliche Sicherheitsinformationen sind erforderlich, um Ihr Kennwort zurückzusetzen. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, Ihr Kennwort zurückzusetzen. Sobald Sie auf Ihr Konto zugreifen können, können Sie zusätzliche Sicherheitsinformationen unter https://aka.ms/ssprsetup registrieren. Ihr Administrator kann Ihrem Konto zusätzliche Sicherheitsinformationen hinzufügen, indem er die Schritte unter [Festlegen und Lesen von Authentifizierungsdaten mithilfe von PowerShell](howto-sspr-authenticationdata.md) befolgt. |
| OnPremisesAdminActionRequired = 29 | Wir können Ihr Kennwort aufgrund eines Problems mit der Konfiguration der Kennwortzurücksetzung in Ihrer Organisation zurzeit nicht zurücksetzen. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, nachzuforschen. <br /><br />Or<br /><br />Wir können Ihr Kennwort zu diesem Zeitpunkt aufgrund eines Problems mit der Konfiguration der Kennwortzurücksetzung für Ihre Organisation nicht zurückzusetzen. Sie können keine weitere Aktion ergreifen, um dieses Problem zu beheben. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, nachzuforschen.<br /><br />Weitere Informationen zu dem potenziellen Problem finden Sie im folgenden Artikel: [Problembehandlung: Kennwortrückschreiben](troubleshoot-sspr-writeback.md). | SSPR_0029: Wir können Ihr Kennwort aufgrund eines Fehlers in Ihrer lokalen Konfiguration nicht zurücksetzen. Wenden Sie sich an Ihren Administrator, und bitten Sie ihn, nachzuforschen. |
| OnPremisesConnectivityError = 30 | Wir können Ihr Kennwort aufgrund von Konnektivitätsproblemen mit Ihrer Organisation zurzeit nicht zurücksetzen. Im Moment können Sie nichts unternehmen, aber möglicherweise ist das Problem gelöst, wenn Sie es später noch mal versuchen. Wenn das Problem weiterhin auftritt, wenden Sie sich an Ihren Administrator, und bitten Sie ihn, nachzuforschen.<br /><br />Weitere Informationen zu Konnektivitätsproblemen finden Sie unter [Behandeln von Konnektivitätsproblemen beim Kennwortrückschreiben](troubleshoot-sspr-writeback.md). | SSPR_0030: Wir können Ihr Kennwort aufgrund einer schlechten Verbindung mit Ihrer lokalen Umgebung nicht zurücksetzen. Bitten Sie Ihren Administrator, das Problem zu untersuchen.|

## <a name="azure-ad-forums"></a>Azure AD-Foren

Wenn Sie allgemeine Fragen zu Azure AD und zur Self-Service-Kennwortzurücksetzung haben, können Sie die Community auf der [Microsoft-Frageseite „Q & A“ für Azure Active Directory](/answers/topics/azure-active-directory.html) um Unterstützung bitten. Zu den Mitgliedern der Community gehören Techniker, Produktmanager MVPs und andere IT-Experten.

## <a name="contact-microsoft-support"></a>Microsoft-Support kontaktieren

Wenn Sie keine Antwort auf ein Problem finden, stehen Ihnen unsere Supportteams immer für weitere Unterstützung zur Verfügung.

Damit wir Sie bestmöglich unterstützen können, geben Sie bitte so viele Details wie möglich an, wenn Sie eine Anfrage erstellen. Hierzu gehören die folgenden Angaben:

* **Allgemeine Beschreibung des Fehlers**: Welcher Fehler liegt vor? Welches Verhalten haben Sie festgestellt? Wie können wir den Fehler reproduzieren? Geben Sie bitte so viele Details wie möglich an.
* **Seite**: Auf welcher Seite befanden Sie sich, als der Fehler aufgetreten ist? Geben Sie möglichst die URL an, und erstellen Sie einen Screenshot der Seite.
* **Unterstützungscode**: Welcher Unterstützungscode wurde generiert, als der Fehler aufgetreten ist?
   * Reproduzieren Sie zum Ermitteln des Unterstützungscodes den Fehler, klicken Sie im unteren Bildschirmbereich auf den Link **Unterstützungscode**, und senden Sie die generierte GUID an den Supportmitarbeiter.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Der Unterstützungscode befindet sich unten rechts im Webbrowserfenster.":::

  * Wenn Sie sich auf einer Seite ohne Unterstützungscode befinden, drücken Sie F12, suchen Sie nach der SID und der CID, und senden Sie beide Ergebnisse an den Supportmitarbeiter.
* **Datum, Uhrzeit und Zeitzone**: Geben Sie das genaue Datum und die exakte Uhrzeit des Fehlers an (*einschließlich der Zeitzone*).
* **Benutzer-ID**: Bei welchem Benutzer ist der Fehler aufgetreten? Ein Beispiel hierfür ist *Benutzer\@contoso.com*.
   * Handelt es sich um einen Verbundbenutzer?
   * Ist dies ein Benutzer mit Pass-Through-Authentifizierung?
   * Handelt es sich um einen Benutzer mit Kennworthashsynchronisierung?
   * Handelt es sich um einen reinen Cloudbenutzer?
* **Lizenzierung**: Ist dem Benutzer eine Azure AD-Lizenz zugewiesen?
* **Anwendungsereignisprotokoll**: Wenn Sie das Kennwortrückschreiben verwenden und der Fehler in Ihrer lokalen Infrastruktur auftritt, fügen Sie eine Kopie des Anwendungsereignisprotokolls von Ihrem Azure AD Connect-Server als ZIP-Datei bei.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SSPR finden Sie unter [So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD](concept-sspr-howitworks.md) oder [Funktionsweise des Rückschreibens von Self-Service-Kennwortzurücksetzungen in Azure AD](concept-sspr-writeback.md).
