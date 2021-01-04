---
title: Verwenden des Aktivitätsberichts zum Verschieben von AD FS-Apps in Azure Active Directory | Microsoft-Dokumentation
description: Mit dem AD FS-Anwendungsaktivitätsbericht (Active Directory-Verbunddienste) können Sie Anwendungen schnell von AD FS zu Azure Active Directory (Azure AD) migrieren. Dieses Migrationstool für AD FS identifiziert die Kompatibilität mit Azure AD und bietet Anleitungen zur Migration.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a43d5bd5f2b228d5ed4384fc1efdca76f8ea0b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573883"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Verwenden Sie den AD FS-Anwendungsaktivitätsbericht (Vorschau), um Anwendungen zu Azure AD zu migrieren.

Viele Organisationen verwenden Active Directory-Verbunddienste (AD FS), um Cloudanwendungen einmaliges Anmelden bereitzustellen. Die Migration Ihrer AD FS Anwendungen zu Azure AD bietet bedeutende Vorteile für die Authentifizierung, insbesondere im Hinblick auf Kostenverwaltung, Risikomanagement, Produktivität, Compliance und Governance. Es kann jedoch zeitaufwändig sein, zu ermitteln, ob Anwendungen mit Azure AD kompatibel sind, und spezifische Migrationsschritte zu identifizieren.

Mit dem AD FS-Anwendungsaktivitätsbericht (Vorschau) im Azure-Portal können Sie schnell ermitteln, welche Ihrer Anwendungen zu Azure AD migriert werden können. Es werden alle AD FS-Anwendungen im Hinblick auf die Kompatibilität mit Azure AD bewertet und auf Probleme überprüft sowie Anleitungen zum Vorbereiten einzelner Anwendungen für die Migration bereitgestellt. Mit dem AD FS-Anwendungsaktivitätsbericht können Sie folgende Aktionen ausführen:

* **Ermitteln der AD FS-Anwendungen und Planen des Migrationsumfangs.** Der AD FS-Anwendungsaktivitätsbericht enthält alle AD FS-Anwendungen in Ihrer Organisation, bei denen in den letzten 30 Tagen aktive Benutzeranmeldungen aufgetreten sind. Der Bericht gibt an, ob eine App für die Migration zu Azure AD bereit ist. Er zeigt jedoch keine Microsoft-bezogenen vertrauenden Seiten in AD FS (wie z. B. Office 365) an. Ein Beispiel wäre eine vertrauende Seite mit dem Namen „urn:federation:MicrosoftOnline“.

* **Priorisieren von Anwendungen für die Migration.** Ermitteln Sie die Anzahl der verschiedenen Benutzer, die sich in den letzten 1, 7 oder 30 Tagen bei der Anwendung angemeldet haben, um die Wichtigkeit oder das Risiko der Migration der Anwendung zu ermitteln.
* **Ausführen von Migrationstests und Beheben von Problemen.** Der Berichterstellungsdienst führt automatisch Tests aus, um zu bestimmen, ob eine Anwendung für die Migration bereit ist. Die Ergebnisse werden im AD FS-Anwendungsaktivitätsbericht als Migrationsstatus angezeigt. Wenn die AD FS-Konfiguration nicht mit einer Azure AD-Konfiguration kompatibel ist, erhalten Sie spezifische Anweisungen zum Behandeln der Konfiguration in Azure AD.

Die AD FS-Anwendungsaktivitätsdaten sind für Benutzer verfügbar, denen diese Administratorrollen zugewiesen sind: globaler Administrator, Benutzer mit Leseberechtigung für Berichte, Benutzer mit Leseberechtigung für Sicherheitsfunktionen, Anwendungsadministrator oder Cloudanwendungsadministrator.

## <a name="prerequisites"></a>Voraussetzungen

* Ihre Organisation muss AD FS aktuell für den Zugriff auf Anwendungen verwenden.
* Azure AD Connect Health muss in Ihrem Azure AD-Mandanten aktiviert sein.
* Die Azure AD Connect Health-Instanz für den AD FS-Agent muss installiert sein.
   * [Weitere Informationen zu Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Erste Schritte mit dem Einrichten von Azure AD Connect Health und der Installation des AD FS-Agents](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Es gibt mehrere Gründe, aus denen nicht alle erwarteten Anwendungen angezeigt werden, nachdem Sie Azure AD Connect Health installiert haben. Der AD FS-Anwendungsaktivitätsbericht enthält nur vertrauende AD FS-Seiten, bei denen in den letzten 30 Tagen Benutzeranmeldungen aufgetreten sind. Außerdem zeigt der Bericht keine Microsoft-bezogenen vertrauenden Seiten (wie z. B. Office 365) an.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Ermitteln von AD FS-Anwendungen, die migriert werden können 

Der AD FS-Anwendungsaktivitätsbericht ist im Azure-Portal unter Azure AD-Berichte zu **Nutzung und Erkenntnissen** verfügbar. Der AD FS-Anwendungsaktivitätsbericht analysiert jede AD FS-Anwendung, um zu bestimmen, ob sie unverändert migriert werden kann, oder ob eine zusätzliche Überprüfung erforderlich ist. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einer Administratorrolle an, die auf AD FS-Anwendungsaktivitätsdaten zugreifen kann (globaler Administrator, Benutzer mit Leseberechtigung für Berichte, Benutzer mit Leseberechtigung für Sicherheitsfunktionen, Anwendungsadministrator oder Cloudanwendungsadministrator).

2. Wählen Sie **Azure Active Directory** und dann **Unternehmensanwendungen** aus.

3. Wählen Sie unter **Aktivität** die Option **Nutzung und Erkenntnisse (Vorschau)** und dann **AD FS-Anwendungsaktivität** aus, um eine Liste aller AD FS-Anwendungen in Ihrer Organisation zu öffnen.

   ![AD FS-Anwendungsaktivität](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Zeigen Sie für jede Anwendung in der AD FS-Anwendungsaktivitätsliste den **Migrationsstatus** an:

   * **Bereit zum Migrieren** bedeutet, dass die AD FS-Anwendungskonfiguration in Azure AD vollständig unterstützt wird und unverändert migriert werden kann.

   * **Überprüfung erforderlich** bedeutet, dass einige Einstellungen der Anwendung zu Azure AD migriert werden können, Sie jedoch die Einstellungen, die nicht unverändert migriert werden können, überprüfen müssen.

   * **Zusätzliche Schritte erforderlich** bedeutet, dass Azure AD einige Einstellungen der Anwendung nicht unterstützt, sodass die Anwendung nicht im aktuellen Zustand migriert werden kann.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Auswerten der Bereitschaft einer Anwendung für die Migration 

1. Klicken Sie in AD FS-Anwendungsaktivitätsliste auf den Status in der Spalte **Migrationsstatus**, um Details zur Migration zu öffnen. Eine Zusammenfassung der bestandenen Konfigurationstests wird zusammen mit möglichen Migrationsproblemen angezeigt.

   ![Details zur Migration](media/migrate-adfs-application-activity/migration-details.png)

2. Klicken Sie auf eine Meldung, um zusätzliche Details zur Migrationsregel zu öffnen. Eine vollständige Liste der getesteten Eigenschaften finden Sie unten in der Tabelle [AD FS-Anwendungskonfigurationstests](#ad-fs-application-configuration-tests).

   ![Details zur Migrationsregel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS-Anwendungskonfigurationstests

In der folgenden Tabelle sind alle Konfigurationstests aufgeführt, die für AD FS-Anwendungen ausgeführt werden.

|Ergebnis  |Bestanden/Warnung/Fehler  |BESCHREIBUNG  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Für „AdditionalAuthentication“ wurde mindestens eine nicht migrierbare Regel erkannt.       | Bestanden/Warnung          | Die vertrauende Seite verfügt über Regeln zum Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA). Um zu Azure AD zu wechseln, übersetzen Sie diese Regeln in Richtlinien für bedingten Zugriff. Wenn Sie eine lokale MFA verwenden, empfehlen wir, dass Sie zu Azure AD MFA wechseln. [Weitere Informationen über bedingten Zugriff](../authentication/concept-mfa-howitworks.md).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Für die vertrauende Seite ist „AdditionalWSFedEndpoint“ auf „true“ festgelegt.       | Erfolgreich/Fehler          | Die vertrauende Seite in AD FS unterstützt mehrere WS-Fed-Assertionsendpunkte. Zurzeit unterstützt Azure AD nur einen. Wenn Sie ein Szenario haben, in dem dieses Ergebnis die Migration blockiert, [geben Sie uns Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Für die vertrauende Seite ist „AllowedAuthenticationClassReferences“ festgelegt.       | Erfolgreich/Fehler          | Mit dieser Einstellung in AD FS können Sie angeben, ob die Anwendung so konfiguriert ist, dass nur bestimmte Authentifizierungstypen zugelassen werden. Wir empfehlen, den bedingten Zugriff zu verwenden, um diese Funktion zu erzielen.  Wenn Sie ein Szenario haben, in dem dieses Ergebnis die Migration blockiert, [geben Sie uns Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Weitere Informationen über bedingten Zugriff](../authentication/concept-mfa-howitworks.md).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Erfolgreich/Fehler          | Mit dieser Einstellung in AD FS können Sie angeben, ob die Anwendung so konfiguriert ist, dass SSO-Cookies ignoriert werden und **immer eine Authentifizierung angefordert** wird. In Azure AD können Sie die Authentifizierungssitzung mithilfe von Richtlinien für bedingten Zugriff verwalten, um ein ähnliches Verhalten zu erzielen. [Weitere Informationen zum Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Test-ADFSRPAutoUpdateEnabled <br> Für die vertrauende Seite ist „AutoUpdateEnabled“ auf „true“ festgelegt.       | Bestanden/Warnung          | Mit dieser Einstellung in AD FS können Sie angeben, ob AD FS für die automatische Aktualisierung der Anwendung basierend auf Änderungen innerhalb der Verbundmetadaten konfiguriert ist. Azure AD unterstützt dies derzeit nicht, sollte die Migration der Anwendung zu Azure AD jedoch nicht blockieren.           |
|Test-ADFSRPClaimsProviderName <br> Die vertrauende Seite verfügt über mehrere aktivierte ClaimsProviders-Elemente.       | Erfolgreich/Fehler          | Diese Einstellung in AD FS ruft die Identitätsanbieter auf, von denen die vertrauende Seite Ansprüche akzeptiert. In Azure AD können Sie die externe Zusammenarbeit mithilfe von Azure AD B2B ermöglichen. [Weitere Informationen zu Azure AD B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Erfolgreich/Fehler          | Für die Anwendung sind benutzerdefinierte Delegationsautorisierungsregeln definiert. Dabei handelt es sich um ein WS-Trust-Konzept, das von Azure AD unterstützt wird, indem moderne Authentifizierungsprotokolle wie OpenID Connect und OAuth 2.0 verwendet werden. [Weitere Informationen zu Microsoft Identity Platform](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Bestanden/Warnung          | Für die Anwendung sind benutzerdefinierte Identitätswechsel-Autorisierungsregeln definiert. Dabei handelt es sich um ein WS-Trust-Konzept, das von Azure AD unterstützt wird, indem moderne Authentifizierungsprotokolle wie OpenID Connect und OAuth 2.0 verwendet werden. [Weitere Informationen zu Microsoft Identity Platform](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Für „IssuanceAuthorization“ wurde mindestens eine nicht migrierbare Regel erkannt.       | Bestanden/Warnung          | Für die Anwendung sind benutzerdefinierte 	Ausstellungsautorisierungsregeln in AD FS definiert. Azure AD unterstützt diese Funktion mit bedingtem Azure AD-Zugriff. [Erfahren Sie mehr über bedingten Zugriff](../conditional-access/overview.md). <br> Sie können zudem den Zugriff auf eine Anwendung auf Benutzer oder Gruppen beschränken, die der Anwendung zugeordnet sind. [Erfahren Sie mehr über das Zuweisen von Benutzern und Gruppen für den Zugriff auf Anwendungen](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> Für „IssuanceTransform“ wurde mindestens eine nicht migrierbare Regel erkannt.       | Bestanden/Warnung          | Für die Anwendung sind benutzerdefinierte 	Ausstellungstransformationsregeln in AD FS definiert. Azure AD unterstützt das Anpassen der im Token ausgestellten Ansprüche. Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).           |
|Test-ADFSRPMonitoringEnabled <br> Für die vertrauende Seite ist „MonitoringEnabled“ auf „true“ festgelegt.       | Bestanden/Warnung          | Mit dieser Einstellung in AD FS können Sie angeben, ob AD FS für die automatische Aktualisierung der Anwendung basierend auf Änderungen innerhalb der Verbundmetadaten konfiguriert ist. Azure AD unterstützt dies derzeit nicht, sollte die Migration der Anwendung zu Azure AD jedoch nicht blockieren.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Bestanden/Warnung          | AD FS lässt eine Zeitabweichung auf Grundlage der „NotBefore“- und „NotOnOrAfter“-Zeiten im SAML-Token zu. Azure AD verarbeitet dies standardmäßig automatisch.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Für die vertrauende Seite ist „RequestMFAFromClaimsProviders“ auf „true“ festgelegt.       | Bestanden/Warnung          | Diese Einstellung in AD FS bestimmt das Verhalten für MFA, wenn der Benutzer von einem anderen Anspruchsanbieter stammt. In Azure AD können Sie die externe Zusammenarbeit mithilfe von Azure AD B2B ermöglichen. Dann können Sie Richtlinien für bedingten Zugriff anwenden, um den Gastzugriff zu schützen. Erfahren Sie mehr über [Azure AD B2B](../external-identities/what-is-b2b.md) und den [bedingten Zugriff](../conditional-access/overview.md).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Für die vertrauende Seite ist „SignedSamlRequestsRequired“ auf „true“ festgelegt.       | Erfolgreich/Fehler          | Die Anwendung wird in AD FS zur Überprüfung der Signatur in der SAML-Anforderung konfiguriert. Azure AD akzeptiert eine signierte SAML-Anforderung, doch die Signatur wird nicht überprüft. Azure AD verfügt über verschiedene Methoden zum Schutz vor böswilligen Aufrufen. Beispielsweise verwendet Azure Ad die in der Anwendung konfigurierten Antwort-URLs, um die SAML-Anforderung zu überprüfen. Azure AD sendet ein Token nur an für die Anwendung konfigurierte Antwort-URLs. Wenn Sie ein Szenario haben, in dem dieses Ergebnis die Migration blockiert, [geben Sie uns Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Bestanden/Warnung         | Die Anwendung ist für eine benutzerdefinierte Tokenlebensdauer konfiguriert. Der AD FS-Standardwert ist eine Stunde. Azure AD unterstützt diese Funktion mit bedingtem-Zugriff. Weitere Informationen finden Sie unter [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Die vertrauende Seite ist so konfiguriert, dass Ansprüche verschlüsselt werden. Diese Konfiguration wird von Azure AD unterstützt.       | Pass          | Mit Azure AD können Sie das an die Anwendung gesendete Token verschlüsseln. Weitere Informationen finden Sie unter [Konfigurieren der Azure AD-SAML-Tokenverschlüsselung](./howto-saml-token-encryption.md).          |
|EncryptedNameIdRequiredCheckResult      | Erfolgreich/Fehler          | Die Anwendung ist so konfiguriert, dass der NameID-Anspruch im SAML-Token verschlüsselt wird. Mit Azure AD können Sie das gesamte an die Anwendung gesendete Token verschlüsseln. Die Verschlüsselung bestimmter Ansprüche wird noch nicht unterstützt. Weitere Informationen finden Sie unter [Konfigurieren der Azure AD-SAML-Tokenverschlüsselung](./howto-saml-token-encryption.md).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Prüfen der Ergebnisse von Anspruchsregeltests

Wenn Sie in AD FS eine Anspruchsregel für die Anwendung konfiguriert haben, bietet die Oberfläche eine detaillierte Analyse für alle Anspruchsregeln. Sie können erkennen, welche Anspruchsregeln nach Azure AD verschoben werden können und welche noch weiter geprüft werden müssen.

1. Klicken Sie in AD FS-Anwendungsaktivitätsliste auf den Status in der Spalte **Migrationsstatus**, um Details zur Migration zu öffnen. Eine Zusammenfassung der bestandenen Konfigurationstests wird zusammen mit möglichen Migrationsproblemen angezeigt.

2. Erweitern Sie auf der Seite **Details zur Migrationsregel** die Ergebnisse, um Details zu möglichen Migrationsproblemen anzuzeigen und weitere Anleitungen zu erhalten. Eine ausführliche Liste aller getesteten Anspruchsregeln finden Sie in der Tabelle [Prüfen der Ergebnisse von Anspruchsregeltests](#check-the-results-of-claim-rule-tests) weiter unten.

   Das folgende Beispiel zeigt Details zur Migrationsregel für die IssuanceTransform-Regel. Es werden die spezifischen Teile des Anspruchs aufgelistet, die überprüft und behandelt werden müssen, bevor Sie die Anwendung zu Azure AD migrieren können.

   ![Zusätzliche Anleitungen zu den Details zur Migrationsregel](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Anspruchsregeltests

In der folgenden Tabelle sind alle Anspruchsregeltests aufgeführt, die für AD FS-Anwendungen ausgeführt werden.

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Die Bedingungsanweisung verwendet reguläre Ausdrücke, um auszuwerten, ob der Anspruch einem bestimmten Muster entspricht.  Um eine ähnliche Funktionalität in Azure AD zu erzielen, können Sie vordefinierte Transformationen wie z.B. IfEmpty(), StartWith() und Contains() verwenden. Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_CONDITION_CLASS      | Die Bedingungsanweisung verfügt über mehrere Bedingungen, die vor dem Ausführen der Ausstellungsanweisung ausgewertet werden müssen. Azure AD unterstützt diese Funktionalität möglicherweise mit den Transformationsfunktionen des Anspruchs, in denen Sie mehrere Anspruchswerte auswerten können.  Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_RULE_TYPE      | Die Anspruchsregel wurde nicht erkannt. Weitere Informationen zum Konfigurieren von Ansprüchen in Azure AD finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Die Bedingungsanweisung verwendet einen Aussteller, der in Azure AD nicht unterstützt wird. Derzeit bindet Azure AD keine Ansprüche aus anderen Speichern als Active Directory oder Azure AD ein. Wenn dies die Migration von Anwendungen zu Azure AD blockiert, [geben Sie uns Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Die Bedingungsanweisung verwendet eine Aggregatfunktion, um einen einzelnen Anspruch unabhängig von der Anzahl der Übereinstimmungen auszugeben oder hinzuzufügen.  In Azure AD können Sie das Attribut eines Benutzers z.B. über Funktionen wie IfEmpty(), StartWith() und Contains() auswerten, um zu entscheiden, welcher Wert für den Anspruch verwendet werden soll. Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).          |
|RESTRICTED_CLAIM_ISSUED      | Die Bedingungsanweisung verwendet einen Anspruch, der in Azure AD eingeschränkt ist. Möglicherweise können Sie einen eingeschränkten Anspruch ausgeben, aber Sie können die Quelle nicht ändern und keine Transformation anwenden. Weitere Informationen finden Sie unter [Anpassen von in Token ausgegebenen Ansprüchen für eine bestimmte App in Azure AD](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | Die Ausstellungsanweisung verwendet einen anderen Attributspeicher als Active Directory. Derzeit bindet Azure AD keine Ansprüche aus anderen Speichern als Active Directory oder Azure AD ein. Wenn dieses Ergebnis die Migration von Anwendungen zu Azure AD blockiert, [geben Sie uns Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Die Ausstellungsanweisung verwendet ADD, um dem eingehenden Anspruchssatz Ansprüche hinzuzufügen. In Azure AD kann dies möglicherweise als mehrfache Anspruchstransformationen konfiguriert werden.  Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-claims-mapping.md).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Die Ausstellungsanweisung verwendet reguläre Ausdrücke, um den Wert des Anspruchs zu transformieren, der ausgegeben werden soll. Um eine ähnliche Funktionalität in Azure AD zu erzielen, können Sie vordefinierte Transformationen wie z.B. Extract(), Trim() und ToLower verwenden. Weitere Informationen finden Sie unter [Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).          |

## <a name="troubleshooting"></a>Problembehandlung

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Im Bericht werden nicht alle meine AD FS-Anwendungen angezeigt.

 Wenn Sie Azure AD Connect Health installiert haben, aber die Aufforderung zur Installation weiterhin angezeigt wird, oder wenn nicht alle Ihre AD FS-Anwendungen im Bericht angezeigt werden, verfügen Sie möglicherweise nicht über aktive AD FS-Anwendungen, oder Ihre AD FS-Anwendungen sind Microsoft-Anwendungen.
 
 Der AD FS-Anwendungsaktivitätsbericht enthält alle AD FS-Anwendungen in Ihrer Organisation, bei denen in den letzten 30 Tagen aktive Benutzeranmeldungen aufgetreten sind. Er zeigt jedoch keine Microsoft-bezogenen vertrauenden Seiten in AD FS (wie z. B. Office 365) an. So werden beispielsweise vertrauende Seiten mit den Namen „urn:federation:MicrosoftOnline“, „microsoftonline“ oder „microsoft:winhello:cert:prov:server“ nicht in der Liste angezeigt.





## <a name="next-steps"></a>Nächste Schritte

- [Video: Verwenden des AD FS-Aktivitätsberichts zum Migrieren einer Anwendung](https://www.youtube.com/watch?v=OThlTA239lU)
- [Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)
- [Azure AD Connect und Verbund](../hybrid/how-to-connect-fed-whatis.md)
