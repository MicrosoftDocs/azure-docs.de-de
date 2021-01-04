---
title: Planen einer Azure Active Directory-Bereitstellung für einmaliges Anmelden
description: Leitfaden zum Planen, Bereitstellen und Verwalten von SSO in Ihrer Organisation.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 14520c7c448231045b36e025c2c92332f9daac53
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658722"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planen einer Bereitstellung für einmaliges Anmelden

Einmaliges Anmelden (Single Sign-On, SSO) bedeutet, auf alle Anwendungen und Ressourcen zuzugreifen, die ein Benutzer benötigt, indem er sich nur ein Mal mit einem einzigen Benutzerkonto anmeldet. Mit SSO können Benutzer auf alle benötigten Anwendungen zugreifen, ohne sich ein zweites Mal authentifizieren zu müssen.

## <a name="benefits-of-sso"></a>Vorteile von SSO

Mit einmaligem Anmelden (Single Sign-On, SSO) ist für Sicherheit und Komfort gesorgt, wenn sich Benutzer in Azure Active Directory (Azure AD) bei Anwendungen anmelden. 

Viele Organisationen nutzen SaaS-Anwendungen (Software-as-a-Service), z. B. Microsoft 365, Box und Salesforce, um die Endbenutzerproduktivität zu steigern. In der Vergangenheit musste das IT-Personal Benutzerkonten in jeder SaaS-Anwendung individuell erstellen und aktualisieren, und Benutzer mussten sich für jede SaaS-Anwendung ein Kennwort merken.

Azure Marketplace verfügt über mehr als 3.000 Anwendungen mit vorintegrierten SSO-Verbindungen, die es einfach machen, sie in Ihren Mandanten zu integrieren.

## <a name="licensing"></a>Lizenzierung

- **Azure AD-Lizenzierung**: SSO für vorintegrierte SaaS-Anwendungen ist kostenlos. Die Anzahl der Objekte in Ihrem Verzeichnis und die Funktionen, die Sie bereitstellen möchten, können jedoch zusätzliche Lizenzen erfordern. Eine vollständige Liste der Lizenzanforderungen finden Sie unter [Azure Active Directory-Preise](https://azure.microsoft.com/pricing/details/active-directory/).
- **Anwendungslizenzierung**: Sie benötigen die entsprechenden Lizenzen für Ihre SaaS-Anwendungen, um Ihre Geschäftsanforderungen zu erfüllen. Arbeiten Sie mit dem Besitzer der Anwendung zusammen, um festzustellen, ob die der Anwendung zugeordneten Benutzer über die entsprechenden Lizenzen für ihre Rollen innerhalb der Anwendung verfügen. Wenn Azure AD die automatische Bereitstellung auf der Grundlage von Rollen verwaltet, müssen die in Azure AD zugewiesenen Rollen mit der Anzahl der Lizenzen in der Anwendung übereinstimmen. Eine nicht ausreichende Anzahl von Lizenzen, die sich im Besitz der Anwendung befinden, kann zu Fehlern bei der Bereitstellung/Aktualisierung eines Benutzers führen.

## <a name="plan-your-sso-team"></a>Planen des SSO-Teams

- **Binden Sie die richtigen Beteiligten ein**: Wenn Technologieprojekte scheitern, liegt dies in der Regel an unterschiedlichen Erwartungen hinsichtlich Auswirkungen, Ergebnissen und Verantwortlichkeiten. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md) und dass die Beteiligten ihre Rollen verstehen.
- **Planen der Kommunikation**: Kommunikation ist entscheidend für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv mit Ihren Benutzern darüber, wie sich die Nutzung ändern wird, wann sie sich ändert und wie Sie Unterstützung erhalten, wenn sie auf Probleme stoßen. Überprüfen Sie die Optionen dafür, [wie Endbenutzer auf ihre SSO-fähigen Anwendungen zugreifen](end-user-experiences.md), und gestalten Sie Ihre Kommunikation entsprechend Ihrer Auswahl. 

## <a name="plan-your-sso-protocol"></a>Planen des SSO-Protokolls

Eine SSO-Implementierung auf der Grundlage von Verbundprotokollen verbessert die Sicherheit, Zuverlässigkeit und Endbenutzerfreundlichkeit und ist einfacher zu implementieren. Viele Anwendungen sind in Azure AD vorintegriert, und für sie sind [schrittweise Anleitungen verfügbar](../saas-apps/tutorial-list.md). Sie finden diese auf [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Ausführliche Informationen zu den einzelnen SSO-Methoden finden Sie im Artikel [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](what-is-single-sign-on.md).

Es gibt zwei grundlegende Möglichkeiten, wie Sie Ihre Benutzer für einmaliges Anmelden bei Ihren Apps aktivieren können:

- **Mit Verbund-SSO** nimmt Azure AD die Authentifizierung des Benutzers bei der Anwendung mithilfe seines Azure AD-Kontos vor. Diese Methode kann für Anwendungen verwendet werden, die Protokolle wie SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, und ist der umfassendste Modus bei der einmaligen Anmeldung. Es wird empfohlen, Verbund-SSO mit Azure AD anstelle von kennwortbasiertem SSO und ADFS zu verwenden, wenn eine Anwendung dies unterstützt.

- **Bei der kennwortbasierten einmaligen Anmeldung** melden sich Endbenutzer mit einem Benutzernamen und einem Kennwort bei der Anwendung an, wenn sie erstmals auf sie zugreifen. Nach der ersten Anmeldung werden der Benutzername und das Kennwort von Azure AD für die Anwendung bereitgestellt. Das kennwortbasierte einmalige Anmelden ermöglicht die sichere Speicherung des Anwendungskennworts und dessen Wiedergabe mit einer Webbrowsererweiterung oder einer mobilen App. Diese Option nutzt den von der Anwendung bereitgestellten vorhandenen Anmeldevorgang, ermöglicht es einem Administrator, die Kennwörter zu verwalten, und erfordert nicht, dass der Benutzer das Kennwort kennt.

### <a name="considerations-for-federation-based-sso"></a>Überlegungen zu verbundbasiertem SSO

- **Verwenden von OpenID Connect und OAuth**: Wenn die Anwendung, mit der Sie eine Verbindung herstellen, dies unterstützt, verwenden Sie die OIDC/OAuth 2.0-Methode, um SSO für diese Anwendung zu aktivieren. Diese Methode erfordert weniger Konfiguration und ermöglicht eine größere Benutzerfreundlichkeit. Weitere Informationen finden Sie unter [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md) und im [Entwicklerhandbuch zu Azure Active Directory](../develop/index.yml).
- **Endpunktkonfigurationen für SAML-basiertes SSO**: Wenn Sie SAML verwenden, benötigen Ihre Entwickler vor der Konfiguration der Anwendung spezifische Informationen. Weitere Informationen finden Sie unter [Konfigurieren des einmaligen Anmeldens mit SAML](configure-saml-single-sign-on.md).
- **Zertifikatverwaltung für SAML-basiertes SSO**: Wenn Sie Verbund-SSO für Ihre Anwendung aktivieren, erstellt Azure AD ein Zertifikat, das standardmäßig drei Jahre lang gültig ist. Sie können das Ablaufdatum für dieses Zertifikat bei Bedarf anpassen. Stellen Sie sicher, dass Sie über Prozesse zur Erneuerung von Zertifikaten vor deren Ablauf verfügen. Weitere Informationen finden Sie unter [Verwalten von Azure AD-Zertifikaten](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>Überlegungen zu kennwortbasiertem einmaligem Anmelden

Die Verwendung von Azure AD für kennwortbasiertes SSO erfordert die Bereitstellung einer Browsererweiterung, die die Anmeldeinformationen sicher abruft und die Anmeldeformulare ausfüllt. Definieren Sie einen Mechanismus zum entsprechenden Bereitstellen der Erweiterung mit [unterstützten Browsern](../user-help/my-apps-portal-end-user-access.md). Beispiele für Optionen:

- [Gruppenrichtlinie für Internet Explorer](./access-panel-deployment-plan.md)
- [Configuration Manager für Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Benutzergesteuerter Download und Konfiguration für Chrome, Firefox, Microsoft Edge oder Internet Explorer](../user-help/my-apps-portal-end-user-access.md)

Weitere Informationen finden Sie unter [Konfigurieren von einmaligem Anmelden mit Kennwort](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Erfassen von Anmeldeformularmetadaten für Anwendungen, die nicht im Katalog enthalten sind

Microsoft unterstützt die Erfassung von Metadaten in einer Webanwendung für den Kennworttresor (Erfassung der Felder „Benutzername“ und „Kennwort“). Navigieren Sie während der Konfiguration der Anwendung zur Erfassung der Formularmetadaten zur Anmelde-URL. Bitten Sie den Besitzer der Anwendung um die genauen Anmelde-URL. Diese Informationen werden während des Anmeldevorgangs verwendet, um die Azure AD-Anmeldeinformationen der Anwendung während der Anmeldung zuzuordnen.

Weitere Informationen finden Sie unter [Was sind Anwendungszugriff und einmaliges Anmelden mit Azure AD? (Kennwortbasiertes SSO)](./what-is-single-sign-on.md).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Anzeichen dafür, dass Metadaten in Formularen erneut erfasst werden müssen

Wenn Anwendungen ihr HTML-Layout ändern, müssen Sie die Metadaten ggf. erneut erfassen, um die Änderungen zu berücksichtigen. Häufige Symptome, die darauf hindeuten, dass sich das HTML-Layout geändert hat, sind unter anderem:

- Benutzer, die melden, dass das Klicken auf die Anwendung auf der Anmeldeseite „hängen bleibt“.
- Benutzer, die melden, dass der Benutzername oder das Kennwort nicht ausgefüllt ist.

#### <a name="shared-accounts"></a>Gemeinsam genutzte Konten

Aus Sicht der Anmeldung unterscheiden sich Anwendungen mit gemeinsam genutzten Konten nicht von einer Kataloganwendung, die für einzelne Benutzer Kennwort-SSO verwendet. Es sind jedoch einige zusätzliche Schritte erforderlich, wenn Sie eine Anwendung planen und konfigurieren, die für die Verwendung von gemeinsam genutzten Konten vorgesehen ist:

1. Arbeiten Sie mit Geschäftsbenutzern der Anwendung zusammen, um die folgenden Punkte zu dokumentieren:
   1. Gruppe von Benutzern in der Organisation, die die Anwendung verwenden werden.
   1. Vorhandene Anmeldeinformationen in der Anwendung, die mit der Gruppe von Benutzern verknüpft sind. 
1. Erstellen Sie für jede Kombination aus Benutzergruppe und Anmeldeinformationen basierend auf Ihren Anforderungen eine Sicherheitsgruppe in der Cloud oder lokal.
1. Setzen Sie die gemeinsam genutzten Anmeldeinformationen zurück. Sobald die App in Azure AD bereitgestellt wird, benötigen Einzelpersonen das Kennwort für das gemeinsam genutzte Konto nicht. Da Azure AD das Kennwort speichert, sollten Sie es sehr lang und komplex gestalten. 
1. Konfigurieren Sie automatischen Rollover des Kennworts, wenn die Anwendung dies unterstützt. Auf diese Weise kennt nicht einmal der Administrator, der die Ersteinrichtung durchgeführt hat, das Kennwort für das gemeinsam genutzte Konto. 

## <a name="plan-your-authentication-method"></a>Planen der Authentifizierungsmethode

Die Wahl der richtigen Authentifizierungsmethode ist daher eine wichtige erste Entscheidung bei der Einrichtung einer Azure AD-Hybrididentitätslösung. Implementieren Sie die Authentifizierungsmethode, die mit Azure AD Connect konfiguriert wird. Hierüber werden auch Benutzer in der Cloud bereitgestellt.

Bei der Auswahl einer Authentifizierungsmethode müssen Sie die Zeit, die vorhandene Infrastruktur, die Komplexität und die Implementierungskosten für die gewählte Lösung berücksichtigen. Diese Faktoren sind für jede Organisation unterschiedlich und können sich im Laufe der Zeit ändern. Sie sollten die Methode auswählen, die Ihrem bestimmten Szenario am ehesten entspricht. Weitere Informationen finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../hybrid/choose-ad-authn.md).

## <a name="plan-your-security-and-governance"></a>Planen von Sicherheit und Governance 

Identität ist der neue primäre Dreh- und Angelpunkt für Sicherheitsmaßnahmen und -investitionen, da die Netzwerkperimeter durch die Explosion von BYOD-Geräten und Cloudanwendungen immer durchlässiger und weniger effektiv geworden sind. 

### <a name="plan-access-reviews"></a>Planen von Zugriffsüberprüfungen

Mithilfe von [Zugriffsüberprüfungen](../governance/create-access-review.md) können Unternehmen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient verwalten. Sie sollten planen, den Benutzerzugriff regelmäßig zu überprüfen, um sicherzustellen, dass nur die richtigen Personen weiterhin Zugriff besitzen.

Einige der wichtigsten Themen, die bei der Einrichtung von Zugriffsüberprüfungen zu berücksichtigen sind:

1. Identifizieren eines Zyklus für Zugriffsüberprüfungen, der Ihren Geschäftsanforderungen entspricht. Dies kann so häufig sein wie ein Mal pro Woche, monatlich oder jährlich, oder es kann sich um eine bedarfsgesteuerte Aufgabe handeln.

1. Erstellen Sie Gruppen, die die Reviewer der Berichte zum App-Zugriff darstellen. Sie müssen sicherstellen, dass die mit der App und ihren Zielbenutzern und Anwendungsfällen am besten vertrauten Beteiligten an Ihren Zugriffsüberprüfungen teilnehmen.

1. Das Abschließen einer Zugriffsüberprüfung beinhaltet die Entfernung von App-Zugriffsberechtigungen für Benutzer, die keinen Zugriff mehr benötigen. Planen Sie die Behandlung potenzieller Supportanfragen von abgelehnten Benutzern. Ein gelöschter Benutzer bleibt 30 Tage lang in Azure AD gelöscht. Während dieser Zeit kann er bei Bedarf von einem Administrator wiederhergestellt werden. Nach 30 Tagen wird dieser Benutzer endgültig gelöscht. Ein globaler kann Administrator über das Azure Active Directory-Portal explizit einen kürzlich gelöschten Benutzer endgültig löschen, bevor dieser Zeitraum abgelaufen ist.

### <a name="plan-auditing"></a>Planen der Überwachung

Azure AD stellt [Berichte mit technischen und geschäftlichen Einblicken](../reports-monitoring/overview-reports.md) zur Verfügung. 

Es stehen sowohl Sicherheits- als auch Aktivitätsberichte zur Verfügung. Sicherheitsberichte zeigen Benutzer, die als riskant gekennzeichnet sind, und risikobehaftete Anmeldungen. Aktivitätsberichte helfen Ihnen, das Verhalten von Benutzern in Ihrem Unternehmen zu verstehen, indem sie die Anmeldeaktivitäten detailliert beschreiben und Überwachungspfade für alle Anmeldungen bereitstellen. Mit Berichten können Sie Risiken verwalten, die Produktivität steigern und die Einhaltung von Vorschriften überwachen.

| Berichtstyp | Zugriffsüberprüfung | Sicherheitsberichte | Anmeldebericht |
|-------------|---------------|------------------|----------------|
| Überprüfter Aspekt | Anwendungsberechtigungen und -nutzung | Potenziell gefährdete Konten | Benutzer, die auf die Anwendungen zugreifen |
| Mögliche Aktionen | Überwachen des Zugriffs, Widerrufen von Berechtigungen | Widerrufen des Zugriffs, Erzwingen einer Sicherheitszurücksetzung | Widerrufen des Zugriffs |

Azure AD speichert die meisten Überwachungsdaten 30 Tage lang und stellt die Daten über das Azure-Verwaltungsportal oder über eine API zur Verfügung, damit Sie sie in Ihre Analysesysteme herunterladen können.

### <a name="consider-using-microsoft-cloud-application-security"></a>Erwägen der Verwendung von Microsoft Cloud App Security

Microsoft Cloud App Security (MCAS) ist eine CASB-Lösung (Cloud Access Security Broker). Damit erhalten Sie Einblick in Ihre Cloud-Apps und -dienste. Die erweiterten Analysen ermöglichen Ihnen, Cyberbedrohungen zu erkennen und abzuwehren sowie die Kontrolle über Ihre Daten zu erlangen.

Das Bereitstellen von MCAS ermöglicht Folgendes:

- Verwenden von Cloud Discovery zum Zuordnen und Identifizieren Ihrer Cloudumgebung und der Cloud-Apps, die von Ihrem Unternehmen verwendet werden.
- Sanktionieren und Aufheben der Sanktionierung von Apps in Ihrer Cloud.
- Verwenden von einfach bereitzustellenden App-Verbindungen, die Anbieter-APIs für die Sichtbarkeit und Governance von Apps verwenden, mit denen Sie eine Verbindung herstellen.
- Verwenden von Schutz durch App-Steuerung für bedingten Zugriff, um Sichtbarkeit in Echtzeit und Kontrolle über Zugriffe und Aktivitäten in Ihren Cloud-Apps zu erhalten.
- Bereitstellen von Unterstützung, damit Sie ständig die Kontrolle behalten, indem Richtlinien festgelegt und dann fortlaufend optimiert werden.

MCAS-Sitzungssteuerung (Microsoft Cloud App Security) steht für jeden Browser auf jeder gängigen Plattform unter jedem Betriebssystem zur Verfügung. Mobile Apps und Desktop-Apps können ebenfalls blockiert oder zugelassen werden. Durch die native Integration in Azure AD können alle Apps, die mit SAML konfiguriert sind, oder Open ID Connect-Apps mit SSO in Azure AD unterstützt werden, einschließlich [mehrerer ausgewählter Apps](/cloud-app-security/proxy-intro-aad).

Weitere Informationen zu MCAS finden Sie unter [Microsoft Cloud App Security: Übersicht](/cloud-app-security/what-is-cloud-app-security). MCAS ist eine benutzerbasierter Abonnementdienst. Sie können Lizenzierungsdetails im [MCAS-Datenblatt zur Lizenzierung](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO) überprüfen.

### <a name="use-conditional-access"></a>Verwenden von bedingtem Zugriff

Mit bedingtem Zugriff können Sie kriterienbasierte Zugriffssteuerungsentscheidungen für Ihre Cloudanwendungen automatisieren.

Richtlinien für den bedingten Zugriff werden durchgesetzt, nachdem die First-Factor-Authentifizierung abgeschlossen ist. Daher ist der bedingte Zugriff nicht als erste Abwehrmaßnahme für Szenarien wie Denial-of-Service-Angriffe (DoS) gedacht, sondern kann Signale von diesen Ereignissen nutzen, um den Zugriff zu bestimmen. So können beispielsweise die Risikostufe der Anmeldung, der Ort der Anforderung usw. verwendet werden. Weitere Informationen zum bedingten Zugriff finden Sie in der [Übersicht](../conditional-access/plan-conditional-access.md) und im [Bereitstellungsplan](../conditional-access/plan-conditional-access.md).

## <a name="azure-sso-technical-requirements"></a>Technische Anforderungen für Azure SSO

Der folgende Abschnitt beschreibt die Anforderungen für die Konfiguration Ihrer spezifischen Anwendung, einschließlich der erforderlichen Umgebung(en), Endpunkte, Anspruchszuordnung, erforderlichen Attribute, Zertifikate und verwendeten Protokolle. Sie benötigen diese Informationen zum Konfigurieren von SSO im [Azure AD-Portal](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Details zum Authentifizierungsmechanismus

Für alle vorintegrierten SaaS-Apps stellt Microsoft ein Tutorial zur Verfügung, und Sie benötigen diese Informationen nicht. Wenn die Anwendung nicht in unserem Anwendungs-Marketplace/Katalog enthalten ist, müssen Sie möglicherweise die folgenden Daten erfassen:

- **Aktueller Identitätsanbieter, den die Anwendung für SSO verwendet, sofern zutreffend**. Beispiel: AD FS, PingFederate, Okta
- **Protokolle, die von der Zielanwendung unterstützt werden**: beispielsweise SAML 2.0, OpenID Connect, OAuth, formularbasierte Authentifizierung, WS-Fed, WS-Trust
- **Protokoll, das mit Azure AD konfiguriert wird**: beispielsweise SAML 2.0 oder 1.1, OpenID Connect, OAuth, formularbasiertes Protokoll, WS-Fed

### <a name="attribute-requirements"></a>Attributanforderungen

Es ist eine vorkonfigurierte Sammlung von Attributen und Attributzuordnungen zwischen Azure AD-Benutzerobjekten und den Benutzerobjekten der einzelnen SaaS-Apps verfügbar. Einige Apps verwalten andere Objekttypen, z.B. Gruppen. Planen Sie die Zuordnung von Benutzerattributen aus Azure AD zu Ihrer Anwendung, und [passen Sie die Standardattributzuordnungen](../app-provisioning/customize-application-attributes.md) entsprechend Ihren Geschäftsanforderungen an.

### <a name="certificate-requirements"></a>Zertifikatanforderungen

Das Zertifikat für die Anwendung muss aktuell sein, andernfalls besteht die Gefahr, dass Benutzer nicht auf die Anwendung zugreifen können. Die meisten SaaS-Anwendungszertifikate sind 36 Monate lang gültig. Sie ändern diese Zertifikatgültigkeit auf dem Anwendungsblatt. Achten Sie darauf, den Ablauf zu dokumentieren, und planen Sie, wie Sie die Zertifikatverlängerung verwalten. 

Es gibt zwei Möglichkeiten zum Verwalten Ihrer Zertifikate. 

- **Automatischer Zertifikatrollover**: Microsoft unterstützt [Signaturschlüsselrollover in Azure AD](../develop/active-directory-signing-key-rollover.md). Dies ist zwar unsere bevorzugte Methode zur Verwaltung von Zertifikaten, aber nicht alle ISVs unterstützen dieses Szenario.

- **Manuelles Aktualisieren**: Jede Anwendung verfügt über ein eigenes Zertifikat, das basierend auf seiner Definition abläuft. Bevor das Zertifikat der Anwendung abläuft, erstellen Sie ein neues Zertifikat und senden es an den ISV. Diese Informationen können aus den Verbundmetadaten entnommen werden. [Hier finden Sie weitere Informationen zu Verbundmetadaten.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Implementieren von SSO

Verwenden Sie die folgenden Phasen, um Ihre Lösung zu planen und in Ihrem Unternehmen bereitzustellen:

### <a name="user-configuration-for-sso"></a>Benutzerkonfiguration für SSO

- **Identifizieren von Testbenutzern**

   Wenden Sie sich an den Besitzer der App, und bitten Sie ihn, mindestens drei Testbenutzer in der Anwendung zu erstellen. Stellen Sie sicher, dass die Informationen, die Sie als primären Bezeichner verwenden werden, richtig mit Daten aufgefüllt sind und einem Attribut entsprechen, das in Azure AD verfügbar ist. In den meisten Fällen wird dies „NameID“ für SAML-basierte Anwendungen zugeordnet. Für JWT-Token wird der „preferred_username“ verwendet.
   
   Erstellen Sie den Benutzer in Azure AD entweder manuell als cloudbasierten Benutzer, oder synchronisieren Sie den Benutzer aus dem lokalen Standort mit der Azure AD Connect-Synchronisierungsengine. Stellen Sie sicher, dass die Informationen mit den Ansprüchen übereinstimmen, die an die Anwendung gesendet werden.

- **Konfigurieren von SSO**

   Suchen Sie in der [Liste der Anwendungen](../saas-apps/tutorial-list.md) das SSO-Tutorial für Ihre Anwendung, öffnen Sie es, und folgen Sie dann den Schritten des Tutorials, um Ihre SaaS-Anwendung erfolgreich zu konfigurieren.

   Wenn Sie Ihre Anwendung nicht finden können, lesen Sie die [Dokumentation zur benutzerdefinierten Anwendung](./configure-saml-single-sign-on.md). Diese führt Sie schrittweise durch das Hinzufügen einer Anwendung, die nicht im Azure AD-Katalog enthalten ist.

   Optional können Sie im SAML-Token ausgestellte Ansprüche für die Unternehmensanwendung gemäß den Anleitungen in der [Dokumentation von Microsoft](../develop/active-directory-claims-mapping.md) verwenden. Stellen Sie sicher, dass diese Werte dem entsprechen, was Sie in der SAML-Antwort für Ihre Anwendung erwarten. Wenn während der Konfiguration Probleme auftreten, verwenden Sie unsere Leitfäden unter [Debuggen der SSO-Integration](./debug-saml-sso-issues.md).

### <a name="provide-sso-change-communications-to-end-users"></a>Bereitstellen von SSO-Änderungskommunikation für Endbenutzer

Implementieren Sie Ihren Kommunikationsplan. Stellen Sie sicher, dass Sie Ihre Endbenutzer wissen lassen, dass eine Änderung ansteht, wenn sie eingetreten ist, was jetzt zu tun ist und wie Hilfe in Anspruch genommen werden kann.

### <a name="verify-end-user-scenarios-for-sso"></a>Überprüfen von Endbenutzerszenarien für SSO

Mit den folgenden Testfällen können Sie Tests auf unternehmenseigenen und persönlichen Geräten durchführen, um sicherzustellen, dass Ihre SSO-Konfigurationen wie erwartet funktionieren. Die folgenden Szenarien gehen davon aus, dass ein Benutzer zu einer Anwendungs-URL navigiert und einen vom Dienstanbieter initiierten Authentifizierungsfluss durchläuft.

| Szenario | Erwartetes Ergebnis für den vom Dienstanbieter initiierten Authentifizierungsfluss nach Benutzer |
|----------|---------------------------------------------------|
| Melden Sie sich bei der Anwendung aus dem Unternehmensnetzwerk mit Internet Explorer an. | Integrierte Windows-Authentifizierung (IWA) tritt ohne weitere Einhabeaufforderungen auf. |
| Melden Sie sich bei der Anwendung mit Internet Explorer mit einem neuen Anmeldeversuch an, während Sie sich nicht im Unternehmensnetzwerk befinden. | Formularbasierte Eingabeaufforderung auf AD FS-Server. Benutzer meldet sich erfolgreich an, und Browser fordert zur mehrstufigen Authentifizierung auf. |
| Melden Sie sich bei der Anwendung mit Internet Explorer an, während Sie sich nicht im Unternehmensnetzwerk befinden und noch nie mehrstufige Authentifizierung durchgeführt haben. | Benutzer erhält keine Eingabeaufforderung für die erste Stufe. Benutzer erhält Eingabeaufforderung für mehrstufige Authentifizierung. |
| Melden Sie sich bei der Anwendung mit Internet Explorer an, während Sie sich nicht im Unternehmensnetzwerk befinden und bereits mehrstufige Authentifizierung in dieser Sitzung durchgeführt haben. | Benutzer erhält keine Eingabeaufforderung für die erste Stufe. Benutzer erhält keine mehrstufige Authentifizierung. Benutzer wird über SSO bei der Anwendung angemeldet. |
| Melden Sie sich bei der Anwendung mit Chrome/Firefox/Safari an, während Sie sich nicht im Unternehmensnetzwerk befinden und bereits mehrstufige Authentifizierung in dieser Sitzung durchgeführt haben. | Benutzer erhält keine Eingabeaufforderung für die erste Stufe. Benutzer erhält keine mehrstufige Authentifizierung. Benutzer wird über SSO bei der Anwendung angemeldet. |
| Melden Sie sich bei der Anwendung mit Chrome/Firefox/Safari mit einem neuen Anmeldeversuch an, während Sie sich nicht im Unternehmensnetzwerk befinden. | Formularbasierte Eingabeaufforderung auf AD FS-Server. Benutzer meldet sich erfolgreich an, und Browser fordert zur mehrstufigen Authentifizierung auf. |
| Melden Sie sich bei der Anwendung mit Chrome/Firefox mit einer aktuellen Sitzung an, während Sie sich im Unternehmensnetzwerk befinden. | Benutzer erhält keine Eingabeaufforderung für die erste Stufe. Benutzer erhält keine mehrstufige Authentifizierung. Benutzer wird über SSO bei der Anwendung angemeldet. |
| Melden Sie sich mit der mobilen App bei der Anwendung mit einem neuen Anmeldeversuch an. | Formularbasierte Eingabeaufforderung auf AD FS-Server. Benutzer meldet sich erfolgreich an, und der ADAL-Client fordert zur mehrstufigen Authentifizierung auf. |
| Nicht autorisierter Benutzer versucht, sich bei der Anwendung mit der Anmelde-URL anzumelden. | Formularbasierte Eingabeaufforderung auf AD FS-Server. Erste Stufe der Benutzeranmeldung schlägt fehl. |
| Autorisierter Benutzer versucht, sich anzumelden, gibt aber ein falsches Kennwort ein. | Benutzer navigiert zur Anwendungs-URL und erhält einen Fehler vom Typ „ungültiger Benutzername/ungültiges Kennwort“. |
| Autorisierter Benutzer klickt auf den Link in einer E-Mail und ist bereits authentifiziert. | Benutzer klickt auf URL und wird ohne weitere Eingabeaufforderungen bei der Anwendung angemeldet. |
| Autorisierter Benutzer klickt auf den Link in einer E-Mail und ist noch nicht authentifiziert. | Benutzer klickt auf URL und wird zur Authentifizierung mit der ersten Stufe aufgefordert. |
| Autorisierter Benutzer meldet sich mit einem neuen Anmeldeversuch bei der Anwendung mit der mobilen Anwendungs-App (vom Dienstanbieter initiiert) an. | Formularbasierte Eingabeaufforderung auf AD FS-Server. Benutzer meldet sich erfolgreich an, und der ADAL-Client fordert zur mehrstufigen Authentifizierung auf. |
| Nicht autorisierter Benutzer versucht, sich bei der Anwendung mit der Anmelde-URL anzumelden (vom Dienstanbieter initiiert). | Formularbasierte Eingabeaufforderung auf AD FS-Server. Erste Stufe der Benutzeranmeldung schlägt fehl. |
| Autorisierter Benutzer versucht, sich anzumelden, gibt aber ein falsches Kennwort ein.| Benutzer navigiert zur Anwendungs-URL und erhält einen Fehler vom Typ „ungültiger Benutzername/ungültiges Kennwort“. |
| Autorisierter Benutzer meldet sich ab und dann erneut an. | Wenn die Abmelde-URL konfiguriert ist, wird der Benutzer bei allen Diensten abgemeldet und aufgefordert, sich zu authentifizieren. |
| Autorisierter Benutzer meldet sich ab und dann erneut an. | Wenn die Anmelde-URL nicht konfiguriert ist, wird der Benutzer automatisch mit dem vorhandenen Token aus der vorhandenen Azure AD-Browsersitzung erneut angemeldet. |
| Autorisierter Benutzer klickt auf den Link in einer E-Mail und ist bereits authentifiziert. | Benutzer klickt auf URL und wird ohne weitere Eingabeaufforderungen bei der Anwendung angemeldet. |
| Autorisierter Benutzer klickt auf den Link in einer E-Mail und ist noch nicht authentifiziert. | Benutzer klickt auf URL und wird zur Authentifizierung mit der ersten Stufe aufgefordert. |

## <a name="manage-sso"></a>Verwalten von SSO

In diesem Abschnitt werden die Anforderungen und Empfehlungen für eine erfolgreiche Verwaltung von SSO beschrieben.

### <a name="required-administrative-roles"></a>Erforderliche Administratorrollen

Verwenden Sie immer die Rolle mit den wenigsten verfügbaren Berechtigungen, um die erforderliche Aufgabe innerhalb von Azure Active Directory zu erledigen. Microsoft empfiehlt [die verschiedenen verfügbaren Rollen zu überprüfen](../roles/permissions-reference.md) und die richtige auszuwählen, um Ihre Anforderungen für jede Persona für diese Anwendung zu erfüllen. Einige Rollen müssen unter Umständen nur vorübergehend angewendet und können nach Abschluss der Bereitstellung wieder entfernt werden.

| Persona| Rollen | Azure AD-Rolle (sofern erforderlich) |
|--------|-------|-----------------------------|
| Helpdesk-Administrator | Support der Ebene 1 | Keine |
| Identitätsadministrator | Konfigurieren und Debuggen, wenn sich Probleme auf Azure AD auswirken | Globaler Administrator |
| Anwendungsadministrator | Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen | Keine |
| Infrastrukturadministratoren | Besitzer des Zertifikatrollovers | Globaler Administrator |
| Geschäftsbesitzer/Beteiligter | Benutzernachweis in Anwendung, Konfiguration für Benutzer mit Berechtigungen | Keine |

Wir empfehlen die Verwendung von [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) zur Verwaltung Ihrer Rollen, um zusätzliche Überwachung, Kontrolle und Zugriffsprüfungen für Benutzer mit Verzeichnisberechtigungen bereitzustellen.

### <a name="sso-certificate-lifecycle-management"></a>Lebenszyklusverwaltung des SSO-Zertifikats

Es ist wichtig, die richtigen Rollen und E-Mail-Verteilerlisten zu identifizieren, die mit der Verwaltung des Lebenszyklus des Signaturzertifikats zwischen Azure AD und der Anwendung, die mit SSO konfiguriert wird, beauftragt sind. Dies sind einige der Schlüsselrollen, deren Einrichtung wir empfehlen:

- Besitzer für die Aktualisierung der Benutzereigenschaften in der Anwendung
- Besitzer auf Abruf für Support bei Anwendungsfehlern/Problembehandlung
- Eng überwachte E-Mail-Verteilerliste für Änderungsbenachrichtigungen im Zusammenhang mit Zertifikaten

Die maximale Lebensdauer eines Zertifikats beträgt drei Jahre. Wir empfehlen Ihnen, einen Prozess einzurichten, wie Sie mit einer Zertifikatänderung zwischen Azure AD und Ihrer Anwendung umgehen. Dies kann dazu beitragen, einen Ausfall aufgrund des Ablaufs eines Zertifikats oder der Erzwingung eines Zertifikatrollovers zu verhindern oder zu minimieren.

### <a name="rollback-process"></a>Rollbackvorgang

Nachdem Sie die Tests anhand Ihrer Testfälle abgeschlossen haben, ist es an der Zeit, mit Ihrer Anwendung in die Produktion überzugehen. Die Umstellung auf die Produktion bedeutet, dass Sie Ihre geplanten und getesteten Konfigurationen in Ihrem Produktionsmandanten implementieren und ein Rollout für Ihre Benutzer ausführen. Es ist jedoch wichtig, zu planen, wie vorzugehen ist, falls Ihre Bereitstellung nicht wie geplant verläuft. Wenn die SSO-Konfiguration während der Bereitstellung fehlschlägt, müssen Sie verstehen, wie Sie einen Ausfall verhindern und die Auswirkungen auf Ihre Benutzer verringern können.

Die Verfügbarkeit von Authentifizierungsmethoden innerhalb der Anwendung bestimmt Ihre beste Strategie. Stellen Sie immer sicher, dass Sie eine detaillierte Dokumentation für App-Besitzer haben, wie Sie genau zum ursprünglichen Konfigurationszustand der Anmeldekonfiguration zurückkehren können, falls Ihre Bereitstellung Probleme aufweist.

- **Wenn Ihre App mehrere Identitätsanbieter unterstützt**, z.B. LDAP, AD FS und Ping, löschen Sie die vorhandene SSO-Konfiguration während des Rollouts nicht. Deaktivieren Sie sie stattdessen während der Migration für den Fall, dass Sie später zu ihr zurückkehren müssen. 

- **Wenn Ihre Anwendung nicht mehrere IDPs unterstützt**, Benutzern aber die Anmeldung über formularbasierte Authentifizierung (Benutzername/Kennwort) ermöglicht, stellen Sie sicher, dass Benutzer auf diesen Ansatz zurückgreifen können, falls der Rollout der neuen SSO-Konfiguration fehlschlägt.

### <a name="access-management"></a>Zugriffsverwaltung

Wir empfehlen, bei der Verwaltung des Zugriffs auf Ressourcen einen skalierten Ansatz zu wählen. Gängige Ansätze sind die Verwendung von lokalen Gruppen durch Synchronisierung über Azure AD Connect, [das Erstellen dynamischer Gruppen in Azure AD basierend auf Benutzerattributen](../enterprise-users/groups-dynamic-membership.md) oder das Erstellen von [Self-Service-Gruppen](../enterprise-users/groups-self-service-management.md) in Azure AD, die von einem Ressourcenbesitzer verwaltet werden.

### <a name="monitor-security"></a>Überwachen der Sicherheit

Wir empfehlen Ihnen, einen regelmäßigen Zyklus einzurichten, in dem Sie die verschiedenen Aspekte der SaaS-Anwendungssicherheit überprüfen und alle erforderlichen Korrekturmaßnahmen durchführen.

### <a name="troubleshooting"></a>Problembehandlung

Die folgenden Links stellen Problembehandlungsszenarien dar. Möglicherweise möchten Sie einen speziellen Leitfaden für Ihre Supportmitarbeiter erstellen, der diese Szenarien und die erforderlichen Schritte zur Behebung dieser Probleme enthält.

#### <a name="consent-issues"></a>Probleme mit der Zustimmung

- [Unerwarteter Zustimmungsfehler](./application-sign-in-unexpected-user-consent-prompt.md)

- [Benutzerzustimmungsfehler](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Probleme bei der Anmeldung

- [Probleme beim Anmelden über ein benutzerdefiniertes Portal](./application-sign-in-other-problem-access-panel.md)

- [Probleme beim Anmelden über „Meine Apps“](./application-sign-in-other-problem-access-panel.md)

- [Fehler auf der Anwendungsanmeldeseite](./application-sign-in-problem-application-error.md)

- [Problem bei der Anmeldung bei einer Microsoft-Anwendung](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>SSO-Probleme für Anwendungen, die im Azure-Anwendungskatalog aufgeführt werden

- [Probleme mit Kennwort-SSO für Anwendungen, die im Azure-Anwendungskatalog aufgeführt werden](./troubleshoot-password-based-sso.md) 

- [Probleme mit Verbund-SSO für Anwendungen, die im Azure-Anwendungskatalog aufgeführt werden](./application-sign-in-problem-federated-sso-gallery.md)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>SSO-Probleme für Anwendungen, die NICHT im Azure-Anwendungskatalog aufgeführt werden

- [Probleme mit Kennwort-SSO für Anwendungen, die NICHT im Azure-Anwendungskatalog aufgeführt werden](./troubleshoot-password-based-sso.md) 

- [Probleme mit Verbund-SSO für Anwendungen, die NICHT im Azure-Anwendungskatalog aufgeführt werden](./application-sign-in-problem-federated-sso-gallery.md)

## <a name="next-steps"></a>Nächste Schritte

[Debuggen von SAML-basierten SSO](./debug-saml-sso-issues.md)

[Anspruchszuordnung für Apps mithilfe von PowerShell](../develop/active-directory-claims-mapping.md)

[Anpassen im SAML-Token ausgestellter Ansprüche](../develop/active-directory-saml-claims-customization.md)

[SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md)

[SAML-Protokoll für einmaliges Abmelden](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (für externe Benutzer, z.B. Partner oder Lieferanten)

[Bedingter Zugriff auf Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[SSO-Zugriff](./what-is-single-sign-on.md)

[Tutorial: Anwendungs-SSO](../saas-apps/tutorial-list.md)

[Whitepaperdownload: Bereitstellungsplan mit einmaligem Anmelden](https://aka.ms/SSODeploymentPlan)