---
title: Referenz zu Überwachungsaktivitäten von Azure Active Directory (Azure AD) | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Überwachungsaktivitäten, die in Ihren Überwachungsprotokollen in Azure Active Directory (Azure AD) protokolliert werden können.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66dd017e8f78f1e93c96262b42dc084c165cdef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285483"
---
# <a name="azure-ad-audit-activity-reference"></a>Referenz zu Überwachungsaktivitäten von Azure AD

Mit Azure AD-Berichten (Azure Active Directory) können Sie alle Informationen abrufen, die Sie zum Ermitteln des Zustands Ihrer Umgebung benötigen.

Die Architektur für die Berichterstellung in Azure AD umfasst die folgenden Komponenten:

- **Aktivitätsberichte** 
    - [Anmeldungen](concept-sign-ins.md): Bietet Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - [Überwachungsprotokolle:](concept-audit-logs.md) Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. 
    
- **Sicherheitsberichte** 
    - [Riskante Anmeldungen:](concept-risky-sign-ins.md) Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist. 
    - [Benutzer mit Risikomarkierung:](concept-user-at-risk.md) Ein Benutzer mit Risikomarkierung ist ein Indikator für ein möglicherweise kompromittiertes Benutzerkonto. 

Dieser Artikel enthält die Überwachungsaktivitäten, die in Ihren Überwachungsprotokollen protokolliert werden können:

## <a name="access-reviews"></a>Zugriffsüberprüfungen

|Überwachungskategorie|Aktivität|
|---|---|
|Zugriffsüberprüfungen|Zugriffsüberprüfung beendet|
|Zugriffsüberprüfungen|Genehmigende Person zu Anforderungsgenehmigung hinzufügen|
|Zugriffsüberprüfungen|Reviewer zu Zugriffsüberprüfung hinzufügen|
|Zugriffsüberprüfungen|Zugriffsüberprüfung anwenden|
|Zugriffsüberprüfungen|Zugriffsüberprüfung erstellen|
|Zugriffsüberprüfungen|Programm erstellen|
|Zugriffsüberprüfungen|Anforderungsgenehmigung erstellen|
|Zugriffsüberprüfungen|Zugriffsüberprüfung löschen|
|Zugriffsüberprüfungen|Programm löschen|
|Zugriffsüberprüfungen|Programmsteuerung verknüpfen|
|Zugriffsüberprüfungen|Integration in Azure AD-Zugriffsüberprüfungen durchführen|
|Zugriffsüberprüfungen|Reviewer aus Zugriffsüberprüfung entfernen|
|Zugriffsüberprüfungen|Überprüfungsbeendigung anfordern|
|Zugriffsüberprüfungen|Anwenden der Überprüfungsergebnisse anfordern|
|Zugriffsüberprüfungen|RBAC-Rollenmitgliedschaft überprüfen|
|Zugriffsüberprüfungen|App-Zuweisung überprüfen|
|Zugriffsüberprüfungen|Gruppenmitgliedschaft überprüfen|
|Zugriffsüberprüfungen|Genehmigungsanforderung überprüfen|
|Zugriffsüberprüfungen|Verknüpfung der Programmsteuerung aufheben|
|Zugriffsüberprüfungen|Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Onboardingstatus von Azure AD-Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellungen für E-Mail-Benachrichtigungen für Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Einstellung für die Wiederholungsanzahl der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für die Wiederholungsdauer der Zugriffsüberprüfungen in Tagen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für den Wiederholungsendtyp der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellung für den Wiederholungstyp der Zugriffsüberprüfungen aktualisieren|
|Zugriffsüberprüfungen|Einstellungen für Erinnerungen für Zugriffsüberprüfung aktualisieren|
|Zugriffsüberprüfungen|Programm aktualisieren|
|Zugriffsüberprüfungen|Anforderungsgenehmigung aktualisieren|
|Zugriffsüberprüfungen|Benutzer deaktiviert|

## <a name="account-provisioning"></a>Kontobereitstellung

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Retrieve V2 application permissions grants (Berechtigungszuweisungen für V2-Anwendung abrufen)|
|Anwendungsverwaltung|Retrieve V2 application service principals in the current tenant (Dienstprinzipale der V2-Anwendung im aktuellen Mandanten abrufen)|
|Anwendungsverwaltung|Update V1 application (V1-Anwendung aktualisieren)|
|Anwendungsverwaltung|Update V2 application (V2-Anwendung aktualisieren)|
|Anwendungsverwaltung|Update V2 application permission grant (Berechtigungszuweisung für V2-Anwendung aktualisieren)|
|Anwendungsverwaltung|Add OAuth2PermissionGrant (OAuth2PermissionGrant hinzufügen)|
|Anwendungsverwaltung|Add app role assignment to service principal (App-Rollenzuweisung zu Dienstprinzipal hinzufügen)|

## <a name="application-proxy"></a>Anwendungsproxy

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anwendung hinzufügen|
|Anwendungsverwaltung|Besitzer der Anwendung hinzufügen|
|Anwendungsverwaltung|Add owner to service principal (Besitzer zu Dienstprinzipal hinzufügen)|
|Anwendungsverwaltung|Richtlinie dem Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Anmeldeinformationen für Dienstprinzipal hinzufügen|
|Verzeichnisverwaltung|Consent to application (Anwendung zustimmen)|
|Verzeichnisverwaltung|Löschen der Anwendung|
|Verzeichnisverwaltung|Hard Delete application (Anwendung endgültig löschen)|
|Verzeichnisverwaltung|Remove OAuth2PermissionGrant (OAuth2PermissionGrant entfernen)|
|Verzeichnisverwaltung|Remove app role assignment from service principal (App-Rollenzuweisung von Dienstprinzipal entfernen)|
|Verzeichnisverwaltung|Besitzer aus Anwendung entfernen|
|Resource|Remove owner from service principal (Besitzer aus Dienstprinzipal entfernen)|
|Resource|Richtlinie aus Dienstprinzipal entfernen|
|Resource|Dienstprinzipal entfernen|


## <a name="automated-password-rollover"></a>Automated password rollover (Automatisiertes Kennwortrollover)

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anmeldeinformationen für Dienstprinzipal entfernen|


## <a name="b2c"></a>B2C

|Überwachungskategorie|Aktivität|
|---|---|
|Anwendungsverwaltung|Anwendung wiederherstellen|
|Anwendungsverwaltung|Zustimmung widerrufen|
|Anwendungsverwaltung|Aktualisieren der Anwendung|
|Anwendungsverwaltung|Externe geheime Schlüssel aktualisieren|
|Anwendungsverwaltung|Update service principal (Dienstprinzipal aktualisieren)|
|Anwendungsverwaltung|Issue an access token to the application (Zugriffstoken für die Anwendung ausstellen)|
|Anwendungsverwaltung|Issue an authorization code to the application (Autorisierungscode für die Anwendung ausstellen)|
|Anwendungsverwaltung|Issue an id_token to the application (ID-Token für die Anwendung ausstellen)|
|Anwendungsverwaltung|Validate local account credentials (Anmeldeinformationen des lokalen Kontos überprüfen)|
|Anwendungsverwaltung|Validate user authentication (Benutzerauthentifizierung überprüfen)|
|Anwendungsverwaltung|Add V2 application permissions (V2-Anwendungsberechtigungen hinzufügen)|
|Anwendungsverwaltung|Add a key based on ASCII secret to a CPIM key container (Schlüssel basierend auf einem ASCII-Geheimnis zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Anwendungsverwaltung|Add a key to a CPIM key container (Schlüssel zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Anwendungsverwaltung|AdminPolicyDatas-SetResources|
|Anwendungsverwaltung|AdminUserJourneys-GetResources|
|Anwendungsverwaltung|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Create IdentityProvider (IdentityProvider-Element erstellen)|
|Authentication|Create V1 application (V1-Anwendung erstellen)|
|Authentication|Create V2 application (V2-Anwendung erstellen)|
|Authentication|Create a custom domains in the tenant (Benutzerdefinierte Domäne im Mandanten erstellen)|
|Autorisierung|Create a new AdminUserJourney (Neues AdminUserJourney-Element erstellen)|
|Autorisierung|Create localized resource json (Lokalisierten JSON-Ressourcencode erstellen)|
|Autorisierung|Create new Custom IDP (Neuen benutzerdefinierten IDP erstellen)|
|Autorisierung|Create new IDP (Neuen IDP erstellen)|
|Autorisierung|Create or update a B2C directory resource (B2C-Verzeichnisressourcen erstellen oder aktualisieren)|
|Autorisierung|Richtlinie erstellen|
|Autorisierung|Create trustFramework policy (Vertrauensframework-Richtlinie erstellen)|
|Autorisierung|Create trustFramework policy with configurable prefix (Vertrauensframework-Richtlinie mit konfigurierbarem Präfix erstellen)|
|Autorisierung|Create user attribute (Benutzerattribut erstellen)|
|Autorisierung|CreateTrustFrameworkPolicy|
|Autorisierung|Creates or Update an new AdminUserJourney (Neues AdminUserJourney-Element erstellen oder aktualisieren)|
|Autorisierung|Identitätsanbieter löschen|
|Autorisierung|Delete IdentityProvider (IdentityProvider-Element löschen)|
|Autorisierung|Delete V1 application (V1-Anwendung löschen)|
|Autorisierung|Delete V2 application (V2-Anwendung löschen)|
|Autorisierung|Delete V2 application permission grant (Berechtigungszuweisung für V2-Anwendung löschen)|
|Autorisierung|Delete a B2C directory resource (B2C-Verzeichnisressource löschen)|
|Autorisierung|Delete a CPIM key container (CPIM-Schlüsselcontainer löschen)|
|Autorisierung|Delete trustFramework policy (Vertrauensframework-Richtlinie löschen)|
|Autorisierung|Benutzerattribut löschen|
|Autorisierung|Enable B2C feature (B2C-Feature aktivieren)|
|Autorisierung|Get B2C directory resources in a subscription (B2C-Verzeichnisressourcen in einem Abonnement abrufen)|
|Autorisierung|Get Custom IDP (Benutzerdefinierten IDP abrufen)|
|Autorisierung|Get IDP (IDP abrufen)|
|Autorisierung|Get V1 and V2 applications (V1- und V2-Anwendungen abrufen)|
|Autorisierung|Get V1 application (V1-Anwendung abrufen)|
|Autorisierung|Get V1 applications (V1-Anwendungen abrufen)|
|Autorisierung|Get V2 application (V2-Anwendung abrufen)|
|Autorisierung|Get V2 applications (V2-Anwendungen abrufen)|
|Autorisierung|B2C-Verzeichnisressource abrufen|
|Autorisierung|Get a list of custom domains in the tenant (Liste der benutzerdefinierten Domänen im Mandanten abrufen)|
|Autorisierung|Get a user journey (User Journey abrufen)|
|Autorisierung|Get allowed application claims for user journey (Zulässige Anwendungsansprüche für User Journey abrufen)|
|Autorisierung|Get allowed self-asserted claims for user journey (Zulässige selbstbestätigte Ansprüche für User Journey abrufen)|
|Autorisierung|Get allowed self-asserted claims of policy (Zulässige selbstbestätigte Ansprüche der Richtlinie abrufen)|
|Autorisierung|Get available output claims list (Liste der verfügbaren Ausgabeansprüche abrufen)|
|Autorisierung|Get content definitions for user journey (Inhaltsdefinitionen für User Journey abrufen)|
|Autorisierung|Get idps for a specific admin flow (IDPs für einen bestimmten Verwaltungsflow abrufen)|
|Autorisierung|Get key container active key metadata in JWK (Schlüsselcontainermetadaten zu aktiven Schlüsseln abrufen)|
|Autorisierung|Get list of all admin flows (Liste aller Verwaltungsflows abrufen)|
|Autorisierung|Get list of tags for all admin flows for all users (Liste der Tags für alle Verwaltungsflows für alle Benutzer abrufen)|
|Autorisierung|Get list of tenants for a user (Liste der Mandanten für einen Benutzer abrufen)|
|Autorisierung|Get local accounts' self-asserted claims (Selbstbestätigte Ansprüche der lokalen Konten abrufen)|
|Autorisierung|Get localized resource json (Lokalisierten JSON-Ressourcencode abrufen)|
|Autorisierung|Get operations of Microsoft.AzureActiveDirectory resource provider (Vorgänge des Microsoft.AzureActiveDirectory-Ressourcenanbieters abrufen)|
|Autorisierung|Get policies (Richtlinien abrufen)|
|Autorisierung|Get policy (Richtlinie abrufen)|
|Autorisierung|Get resource properties of a tenant (Ressourceneigenschaften eines Mandanten abrufen)|
|Autorisierung|Get supported IDP list (Liste der unterstützten IDPs abrufen)|
|Autorisierung|Get supported IDP list of the user journey (Liste der unterstützten IDPs der User Journey abrufen)|
|Autorisierung|Get tenant Info (Abrufen der Mandanteninformationen)|
|Autorisierung|Get tenant allowed features (Vom Mandanten zugelassene Features abrufen)|
|Autorisierung|Get tenant defined Custom IDP list (Vom Mandanten festgelegte Liste der benutzerdefinierten IDPs abrufen)|
|Autorisierung|Get tenant defined IDP list (Vom Mandanten festgelegte IDP-Liste)|
|Autorisierung|Get tenant defined local IDP list (Vom Mandanten festgelegte Liste der lokalen IDPs)|
|Autorisierung|Get tenant details for a user for resource creation (Mandantendetails für einen Benutzer zur Ressourcenerstellung abrufen)|
|Autorisierung|Get tenant list (Mandantenliste abrufen)|
|Autorisierung|Get tenantDomains (tenantDomains-Element abrufen)|
|Autorisierung|Get the default supported culture for CPIM (Standardmäßige unterstützte Kultur für CPIM abrufen)|
|Autorisierung|Get the details of an admin flow (Details eines Verwaltungsflows abrufen)|
|Autorisierung|Get the list of UserJourneys for this tenant (UserJourneys-Liste für diesen Mandanten abrufen)|
|Autorisierung|Get the set of available supported cultures for CPIM (Satz der verfügbaren unterstützten Kulturen für CPIM abrufen)|
|Autorisierung|Get trustFramework policy (Vertrauensframework-Richtlinie abrufen)|
|Autorisierung|Get trustFramework policy as xml (Vertrauensframework-Richtlinie als XML abrufen)|
|Autorisierung|Get user attribute (Benutzerattribut abrufen)|
|Autorisierung|Get user attributes (Benutzerattribute abrufen)|
|Autorisierung|Get user journey list (User Journey-Liste abrufen)|
|Autorisierung|GetIEFPolicies|
|Autorisierung|GetIdentityProviders|
|Autorisierung|GetTrustFrameworkPolicy|
|Autorisierung|Gets a CPIM key container in jwk format (CPIM-Schlüsselcontainer im JWK-Format abrufen)|
|Autorisierung|Gets list of key containers in the tenant (Liste der Schlüsselcontainer im Mandanten abrufen)|
|Autorisierung|Gets the type of tenant (Mandantentyp abrufen)|
|Autorisierung|MigrateTenantMetadata|
|Autorisierung|Patch IdentityProvider (IdentityProvider patchen)|
|Autorisierung|PutTrustFrameworkPolicy|
|Autorisierung|PutTrustFrameworkpolicy|
|Autorisierung|User Journey entfernen|
|Autorisierung|Restore a CPIM key container backup (Sicherung eines CPIM-Schlüsselcontainers wiederherstellen)|
|Autorisierung|Retrieve V2 application permissions grants (Berechtigungszuweisungen für V2-Anwendung abrufen)|
|Autorisierung|Retrieve V2 application service principals in the current tenant (Dienstprinzipale der V2-Anwendung im aktuellen Mandanten abrufen)|
|Autorisierung|Update Custom IDP (Benutzerdefinierten IDP aktualisieren)|
|Autorisierung|Update IDP (IDP aktualisieren)|
|Autorisierung|Lokalen IDP aktualisieren|
|Autorisierung|Update V1 application (V1-Anwendung aktualisieren)|
|Autorisierung|Update V2 application (V2-Anwendung aktualisieren)|
|Autorisierung|Update V2 application permission grant (Berechtigungszuweisung für V2-Anwendung aktualisieren)|
|Autorisierung|Aktualisieren von Richtlinien|
|Autorisierung|Update user attribute (Benutzerattribut aktualisieren)|
|Autorisierung|Upload a CPIM encrypted key (Verschlüsselten CPIM-Schlüssel hochladen)|
|Autorisierung|Benutzerautorisierung: Die API ist für die Mandantenfeatures deaktiviert.|
|Autorisierung|Benutzerautorisierung: Dem Benutzer wurde Zugriff als „Mandantenadministrator“ gewährt.|
|Autorisierung|Benutzerautorisierung: Dem Benutzer wurden Zugriffsrechte vom Typ „Authentifizierte Benutzer“ gewährt.|
|Autorisierung|Verify if B2C feature is enabled (Überprüfen, ob das B2C-Feature aktiviert ist)|
|Autorisierung|Überprüfen, ob das Feature aktiviert ist|
|Autorisierung|Programm erstellen|
|Autorisierung|Programm löschen|
|Autorisierung|Programmsteuerung verknüpfen|
|Autorisierung|Integration in Azure AD-Zugriffsüberprüfungen durchführen|
|Autorisierung|Verknüpfung der Programmsteuerung aufheben|
|Autorisierung|Programm aktualisieren|
|Autorisierung|Disable Desktop Sso (Desktop-SSO deaktivieren)|
|Autorisierung|Disable Desktop Sso for a specific domain (Desktop-SSO für eine bestimmte Domäne deaktivieren)|
|Autorisierung|Anwendungsproxy deaktivieren|
|Autorisierung|Passthrough-Authentifizierung deaktivieren|
|Autorisierung|Enable Desktop Sso (Desktop-SSO aktivieren)|
|Verzeichnisverwaltung|Enable Desktop Sso for a specific domain (Desktop-SSO für eine bestimmte Domäne aktivieren)|
|Verzeichnisverwaltung|Anwendungsproxy aktivieren|
|Verzeichnisverwaltung|Passthrough-Authentifizierung aktivieren|
|Verzeichnisverwaltung|Create a custom domains in the tenant (Benutzerdefinierte Domäne im Mandanten erstellen)|
|Verzeichnisverwaltung|Enable B2C feature (B2C-Feature aktivieren)|
|Verzeichnisverwaltung|Get a list of custom domains in the tenant (Liste der benutzerdefinierten Domänen im Mandanten abrufen)|
|Verzeichnisverwaltung|Get resource properties of a tenant (Ressourceneigenschaften eines Mandanten abrufen)|
|Verzeichnisverwaltung|Get tenant Info (Abrufen der Mandanteninformationen)|
|Verzeichnisverwaltung|Get tenant allowed features (Vom Mandanten zugelassene Features abrufen)|
|Verzeichnisverwaltung|Get tenantDomains (tenantDomains-Element abrufen)|
|Schlüssel|Gets the type of tenant (Mandantentyp abrufen)|
|Schlüssel|Verify if B2C feature is enabled (Überprüfen, ob das B2C-Feature aktiviert ist)|
|Schlüssel|Überprüfen, ob das Feature aktiviert ist|
|Schlüssel|Partner zu Unternehmen hinzufügen|
|Schlüssel|Add inverified domain (Nicht überprüfte Domäne hinzufügen)|
|Schlüssel|Add verified domain (Überprüfte Domäne hinzufügen)|
|Schlüssel|Unternehmen erstellen|
|Schlüssel|Unternehmenseinstellungen erstellen|
|Schlüssel|Unternehmenseinstellungen löschen|
|Schlüssel|Partner tiefer stufen|
|Schlüssel|Directory deleted (Verzeichnis gelöscht)|
|Andere|Directory deleted permanently (Verzeichnis unwiderruflich gelöscht)|
|Andere|Directory scheduled for deletion (Löschen des Verzeichnisses geplant)|
|Resource|Promote company to partner (Unternehmen zu Partner heraufstufen)|
|Resource|Rights Management-Eigenschaften bereinigen|
|Resource|Partner aus Unternehmen entfernen|
|Resource|Remove unverified domain (Nicht überprüfte Domäne entfernen)|
|Resource|Remove verified domain (Überprüfte Domäne entfernen)|
|Resource|Unternehmensinformationen festlegen|
|Resource|DirSync-Funktion festlegen|
|Resource|DirSyncEnabled-Flag festlegen|
|Resource|Set Partnership (Partnerschaft festlegen)|
|Resource|Schwellenwert für versehentliches Löschen festlegen|
|Resource|Zulässigen Datenspeicherort für Unternehmen festlegen|
|Resource|Funktion für multinationales Unternehmen auf „Aktiviert“ festlegen|
|Resource|Verzeichnisfunktion für Mandanten festlegen|
|Resource|Domänenauthentifizierung festlegen|
|Resource|Verbundeinstellungen für Domäne festlegen|
|Resource|Kennwortrichtlinie festlegen|
|Resource|Eigenschaften für Rights Management festlegen|
|Resource|Update company (Unternehmen aktualisieren)|
|Resource|Unternehmenseinstellungen aktualisieren|
|Resource|Domäne aktualisieren|
|Resource|Domäne überprüfen|
|Resource|Per E-Mail verifizierte Domäne überprüfen|
|Resource|Onboarding|
|Resource|Warnungseinstellungen aktualisieren|
|Resource|Update weekly digest settings (Einstellungen für wöchentliche Übersicht aktualisieren)|
|Resource|Disable password writeback for directory (Kennwortrückschreiben für Verzeichnis deaktivieren)|
|Resource|Enable password writeback for directory (Kennwortrückschreiben für Verzeichnis aktivieren)|
|Resource|Add app role assignment to group (App-Rollenzuweisung zu Gruppe hinzufügen)|
|Resource|Gruppe hinzufügen|
|Resource|Mitglied zur Gruppe hinzufügen|
|Resource|Add owner to group (Besitzer zu Gruppe hinzufügen)|
|Resource|Create group settings (Gruppeneinstellungen erstellen)|
|Resource|Gruppe löschen|
|Resource|Delete group settings (Gruppeneinstellungen löschen)|
|Resource|Finish applying group based license to users (Gruppenbasierte Lizenzzuweisung zu Benutzern fertig stellen)|
|Resource|Hard Delete group (Gruppe endgültig löschen)|
|Resource|Remove app role assignment from group (App-Rollenzuweisung von Gruppe entfernen)|
|Resource|Mitglied aus Gruppe entfernen|
|Resource|Remove owner from group (Besitzer aus Gruppe entfernen)|
|Resource|Gruppe wiederherstellen|
|Resource|Set group license (Gruppenlizenz festlegen)|
|Resource|Gruppe für Verwaltung durch Benutzer festgelegt|
|Resource|Start applying group based license to users (Starten der gruppenbasierten Lizenzzuweisung zu Benutzern)|
|Resource|Trigger group license recalculation (Neuberechnung der Gruppenlizenzen auslösen)|
|Resource|Gruppe aktualisieren|
|Resource|Update group settings (Gruppeneinstellungen aktualisieren)|
|Resource|Mitglied hinzufügen|
|Resource|Erstellen einer Gruppe|
|Resource|Gruppe löschen|
|Resource|Mitglied entfernen|
|Resource|Gruppe aktualisieren|
|Resource|Approve a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung genehmigen)|
|Resource|Cancel a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung abbrechen)|
|Resource|Create lifecycle management policy (Richtlinie für Lebenszyklusverwaltung erstellen)|
|Resource|Delete a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung löschen)|
|Resource|Reject a pending request to join a group (Ausstehende Gruppenbeitrittsanforderung ablehnen)|
|Resource|Gruppe verlängern|
|Resource|Request to join a group (Gruppenbeitritt anfordern)|
|Resource|Set dynamic group properties (Dynamische Gruppeneigenschaften festlegen)|
|Resource|Update lifecycle management policy (Richtlinie für Lebenszyklusverwaltung aktualisieren)|
|Resource|Add a key based on ASCII secret to a CPIM key container (Schlüssel basierend auf einem ASCII-Geheimnis zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Resource|Add a key to a CPIM key container (Schlüssel zu einem CPIM-Schlüsselcontainer hinzufügen)|
|Resource|Delete a CPIM key container (CPIM-Schlüsselcontainer löschen)|
|Resource|Delete key container (Schlüsselcontainer löschen)|
|Resource|Get key container active key metadata in JWK (Schlüsselcontainermetadaten zu aktiven Schlüsseln abrufen)|
|Resource|Get key container metadata (Metadaten des Schlüsselcontainers abrufen)|
|Resource|Gets a CPIM key container in jwk format (CPIM-Schlüsselcontainer im JWK-Format abrufen)|
|Resource|Gets list of key containers in the tenant (Liste der Schlüsselcontainer im Mandanten abrufen)|
|Resource|Restore a CPIM key container backup (Sicherung eines CPIM-Schlüsselcontainers wiederherstellen)|
|Resource|Save key container (Schlüsselcontainer speichern)|
|Resource|Upload a CPIM encrypted key (Verschlüsselten CPIM-Schlüssel hochladen)|
|Resource|Issue an authorization code to the application (Autorisierungscode für die Anwendung ausstellen)|
|Resource|Issue an id_token to the application (ID-Token für die Anwendung ausstellen)|


## <a name="core-directory"></a>Core directory (Kernverzeichnis)

|Überwachungskategorie|Aktivität|
|---|---|
|Verwaltung administrativer Einheiten|Download a single risk event type (Einzelnen Risikoereignistyp herunterladen)|
|Verwaltung administrativer Einheiten|Download admins and status of weekly digest opt-in (Administratoren und Status der Aktivierung der wöchentlichen Übersicht herunterladen)|
|Verwaltung administrativer Einheiten|Download all risk event types (Alle Risikoereignistypen herunterladen)|
|Verwaltung administrativer Einheiten|Download free user risk events (Kostenlose Benutzerrisikoereignisse herunterladen)|
|Verwaltung administrativer Einheiten|Download users flagged for risk (Benutzer herunterladen, für die ein Risiko angezeigt wird)|
|Anwendungsverwaltung|Verarbeitete Batch-Einladungen|
|Anwendungsverwaltung|Batch-Einladungen hochgeladen|
|Anwendungsverwaltung|Add owner to policy (Besitzer zu Richtlinie hinzufügen)|
|Anwendungsverwaltung|Richtlinie hinzufügen|
|Anwendungsverwaltung|Löschen von Richtlinien|
|Anwendungsverwaltung|Richtlinien-Anmeldeinformationen entfernen|
|Anwendungsverwaltung|Aktualisieren von Richtlinien|
|Anwendungsverwaltung|Set MFA registration policy (MFA-Registrierungsrichtlinie festlegen)|
|Anwendungsverwaltung|Set sign-in risk policy (Richtlinie zum Anmelderisiko festlegen)|
|Anwendungsverwaltung|Set user risk policy (Richtlinie zum Benutzerrisiko festlegen)|
|Anwendungsverwaltung|Accept Terms Of Use (Nutzungsbedingungen akzeptieren)|
|Anwendungsverwaltung|Create Terms Of Use (Nutzungsbedingungen erstellen)|
|Anwendungsverwaltung|Decline Terms Of Use (Nutzungsbedingungen ablehnen)|
|Anwendungsverwaltung|Delete Terms Of Use (Nutzungsbedingungen löschen)|
|Anwendungsverwaltung|Edit Terms Of Use (Nutzungsbedingungen bearbeiten)|
|Anwendungsverwaltung|Publish Terms Of Use (Nutzungsbedingungen veröffentlichen)|
|Anwendungsverwaltung|Unpublish Terms Of Use (Veröffentlichung der Nutzungsbedingungen aufheben)|
|Anwendungsverwaltung|Add application SSL certificate (Anwendungs-SSL-Zertifikat hinzufügen)|
|Anwendungsverwaltung|Delete SSL binding (SSL-Bindung löschen)|
|Anwendungsverwaltung|Register connector (Connector registrieren)|
|Anwendungsverwaltung|AdminPolicyDatas-RemoveResources|
|Anwendungsverwaltung|AdminPolicyDatas-SetResources|
|Anwendungsverwaltung|AdminUserJourneys-GetResources|
|Verzeichnisverwaltung|AdminUserJourneys-RemoveResources|
|Verzeichnisverwaltung|AdminUserJourneys-SetResources|
|Verzeichnisverwaltung|Create IdentityProvider (IdentityProvider-Element erstellen)|
|Verzeichnisverwaltung|Create a new AdminUserJourney (Neues AdminUserJourney-Element erstellen)|
|Verzeichnisverwaltung|Create localized resource json (Lokalisierten JSON-Ressourcencode erstellen)|
|Verzeichnisverwaltung|Create new Custom IDP (Neuen benutzerdefinierten IDP erstellen)|
|Verzeichnisverwaltung|Create new IDP (Neuen IDP erstellen)|
|Verzeichnisverwaltung|Create or update a B2C directory resource (B2C-Verzeichnisressourcen erstellen oder aktualisieren)|
|Verzeichnisverwaltung|Richtlinie erstellen|
|Verzeichnisverwaltung|Create trustFramework policy (Vertrauensframework-Richtlinie erstellen)|
|Verzeichnisverwaltung|Create trustFramework policy with configurable prefix (Vertrauensframework-Richtlinie mit konfigurierbarem Präfix erstellen)|
|Verzeichnisverwaltung|Create user attribute (Benutzerattribut erstellen)|
|Verzeichnisverwaltung|CreateTrustFrameworkPolicy|
|Verzeichnisverwaltung|Identitätsanbieter löschen|
|Verzeichnisverwaltung|Delete IdentityProvider (IdentityProvider-Element löschen)|
|Verzeichnisverwaltung|Delete a B2C directory resource (B2C-Verzeichnisressource löschen)|
|Verzeichnisverwaltung|Delete trustFramework policy (Vertrauensframework-Richtlinie löschen)|
|Verzeichnisverwaltung|Benutzerattribut löschen|
|Verzeichnisverwaltung|Get B2C directory resources in a resource group (B2C-Verzeichnisressourcen in einer Ressourcengruppe abrufen)|
|Verzeichnisverwaltung|Get B2C directory resources in a subscription (B2C-Verzeichnisressourcen in einem Abonnement abrufen)|
|Verzeichnisverwaltung|Get Custom IDP (Benutzerdefinierten IDP abrufen)|
|Verzeichnisverwaltung|Get IDP (IDP abrufen)|
|Verzeichnisverwaltung|B2C-Verzeichnisressource abrufen|
|Verzeichnisverwaltung|Get a user journey (User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed application claims for user journey (Zulässige Anwendungsansprüche für User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed self-asserted claims for user journey (Zulässige selbstbestätigte Ansprüche für User Journey abrufen)|
|Verzeichnisverwaltung|Get allowed self-asserted claims of policy (Zulässige selbstbestätigte Ansprüche der Richtlinie abrufen)|
|Verzeichnisverwaltung|Get available output claims list (Liste der verfügbaren Ausgabeansprüche abrufen)|
|Verzeichnisverwaltung|Get content definitions for user journey (Inhaltsdefinitionen für User Journey abrufen)|
|Verzeichnisverwaltung|Get idps for a specific admin flow (IDPs für einen bestimmten Verwaltungsflow abrufen)|
|Verzeichnisverwaltung|Get list of all admin flows (Liste aller Verwaltungsflows abrufen)|
|Verzeichnisverwaltung|Get list of tags for all admin flows for all users (Liste der Tags für alle Verwaltungsflows für alle Benutzer abrufen)|
|Gruppenverwaltung|Get list of tenants for a user (Liste der Mandanten für einen Benutzer abrufen)|
|Gruppenverwaltung|Get local accounts' self-asserted claims (Selbstbestätigte Ansprüche der lokalen Konten abrufen)|
|Gruppenverwaltung|Get localized resource json (Lokalisierten JSON-Ressourcencode abrufen)|
|Gruppenverwaltung|Get operations of Microsoft.AzureActiveDirectory resource provider (Vorgänge des Microsoft.AzureActiveDirectory-Ressourcenanbieters abrufen)|
|Gruppenverwaltung|Get policies (Richtlinien abrufen)|
|Gruppenverwaltung|Get policy (Richtlinie abrufen)|
|Gruppenverwaltung|Get supported IDP list (Liste der unterstützten IDPs abrufen)|
|Gruppenverwaltung|Get supported IDP list of the user journey (Liste der unterstützten IDPs der User Journey abrufen)|
|Gruppenverwaltung|Get tenant defined Custom IDP list (Vom Mandanten festgelegte Liste der benutzerdefinierten IDPs abrufen)|
|Gruppenverwaltung|Get tenant defined IDP list (Vom Mandanten festgelegte IDP-Liste)|
|Gruppenverwaltung|Get tenant defined local IDP list (Vom Mandanten festgelegte Liste der lokalen IDPs)|
|Gruppenverwaltung|Get tenant details for a user for resource creation (Mandantendetails für einen Benutzer zur Ressourcenerstellung abrufen)|
|Gruppenverwaltung|Get the default supported culture for CPIM (Standardmäßige unterstützte Kultur für CPIM abrufen)|
|Gruppenverwaltung|Get the details of an admin flow (Details eines Verwaltungsflows abrufen)|
|Gruppenverwaltung|Get the list of UserJourneys for this tenant (UserJourneys-Liste für diesen Mandanten abrufen)|
|Gruppenverwaltung|Get the set of available supported cultures for CPIM (Satz der verfügbaren unterstützten Kulturen für CPIM abrufen)|
|Gruppenverwaltung|Get trustFramework policy (Vertrauensframework-Richtlinie abrufen)|
|Gruppenverwaltung|Get trustFramework policy as xml (Vertrauensframework-Richtlinie als XML abrufen)|
|Gruppenverwaltung|Get user attribute (Benutzerattribut abrufen)|
|Richtlinienverwaltung|Get user attributes (Benutzerattribute abrufen)|
|Richtlinienverwaltung|Get user journey list (User Journey-Liste abrufen)|
|Richtlinienverwaltung|GetIEFPolicies|
|Richtlinienverwaltung|GetIdentityProviders|
|Richtlinienverwaltung|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Verschieben von Ressourcen|
|Resource|Patch IdentityProvider (IdentityProvider patchen)|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|User Journey entfernen|
|Resource|Update Custom IDP (Benutzerdefinierten IDP aktualisieren)|
|Resource|Update IDP (IDP aktualisieren)|
|Resource|Lokalen IDP aktualisieren|
|Resource|Update a B2C directory resource (B2C-Verzeichnisressource aktualisieren)|
|Resource|Aktualisieren von Richtlinien|
|Resource|Update subscription status (Abonnementstatus aktualisieren)|
|Rollenverwaltung|Update user attribute (Benutzerattribut aktualisieren)|
|Rollenverwaltung|Validate move resources (Verschiebung von Ressourcen überprüfen)|
|Rollenverwaltung|Gerät hinzufügen|
|Rollenverwaltung|Add device configuration (Gerätekonfiguration hinzufügen)|
|Rollenverwaltung|Add registered owner to device (Registrierten Besitzer zu Gerät hinzufügen)|
|Rollenverwaltung|Add registered users to device (Registrierte Benutzer zu Gerät hinzufügen)|
|Rollenverwaltung|Löschen des Geräts|
|Rollenverwaltung|Löschen der Gerätekonfiguration|
|Rollenverwaltung|Device no longer compliant (Gerät nicht mehr konform)|
|Rollenverwaltung|Device no longer managed (Gerät nicht mehr verwaltet)|
|Benutzerverwaltung|Registrierten Besitzer aus Gerät entfernen|
|Benutzerverwaltung|Registrierte Benutzer aus Gerät entfernen|
|Benutzerverwaltung|Gerät aktualisieren|
|Benutzerverwaltung|Update device configuration (Gerätekonfiguration aktualisieren)|
|Benutzerverwaltung|Add eligible member to role (Berechtigtes Mitglied zur Rolle hinzufügen)|
|Benutzerverwaltung|Add member to role (Mitglied zu Rolle hinzufügen)|
|Benutzerverwaltung|Add role assignment to role definition (Rollenzuweisung zu Rollendefinition hinzufügen)|
|Benutzerverwaltung|Add role from template (Rolle aus Vorlage hinzufügen)|
|Benutzerverwaltung|Add scoped member to role (Zugeordnetes Mitglied zu Rolle hinzufügen)|
|Benutzerverwaltung|Remove eligible member from role (Berechtigtes Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Remove member from role (Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Remove role assignment from role definition (Rollenzuweisung aus Rollendefinition entfernen)|
|Benutzerverwaltung|Remove scoped member from role (Zugeordnetes Mitglied aus Rolle entfernen)|
|Benutzerverwaltung|Rolle aktualisieren|
|Benutzerverwaltung|AccessReview_Review|
|Benutzerverwaltung|AccessReview_Update|
|Benutzerverwaltung|ActivationAborted|
|Benutzerverwaltung|ActivationApproved|
|Benutzerverwaltung|ActivationCanceled|
|Benutzerverwaltung|ActivationRequested|
|Benutzerverwaltung|Hinzugefügt|
|Benutzerverwaltung|Zuweisen|


## <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

|Überwachungskategorie|Aktivität|
|---|---|
|Verzeichnisverwaltung|Erhöhen|
|Verzeichnisverwaltung|Entfernt|
|Verzeichnisverwaltung|Änderungen für Rolleneinstellung|
|Andere|ScanAlertsNow|
|Andere|Signup|
|Andere|Deautorisieren|
|Andere|UpdateAlertSettings|
|Andere|UpdateCurrentState|
|Richtlinienverwaltung|Zugriffsüberprüfung beendet|
|Richtlinienverwaltung|Genehmigende Person zu Anforderungsgenehmigung hinzufügen|
|Richtlinienverwaltung|Reviewer zu Zugriffsüberprüfung hinzufügen|
|Benutzerverwaltung|Zugriffsüberprüfung anwenden|
|Benutzerverwaltung|Zugriffsüberprüfung erstellen|


## <a name="invited-users"></a>Invited Users (Eingeladene Benutzer)

|Überwachungskategorie|Aktivität|
|---|---|
|Andere|Anforderungsgenehmigung erstellen|
|Andere|Zugriffsüberprüfung löschen|
|Benutzerverwaltung|Reviewer aus Zugriffsüberprüfung entfernen|
|Benutzerverwaltung|Anwenden der Überprüfungsergebnisse anfordern|
|Benutzerverwaltung|Überprüfungsbeendigung anfordern|
|Benutzerverwaltung|App-Zuweisung überprüfen|
|Benutzerverwaltung|Gruppenmitgliedschaft überprüfen|
|Benutzerverwaltung|RBAC-Rollenmitgliedschaft überprüfen|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Überwachungskategorie|Aktivität|
|---|---|
|Gruppenverwaltung|Genehmigungsanforderung überprüfen|
|Gruppenverwaltung|Zugriffsüberprüfung aktualisieren|
|Gruppenverwaltung|Einstellungen für E-Mail-Benachrichtigungen für Zugriffsüberprüfung aktualisieren|
|Gruppenverwaltung|Einstellung für die Wiederholungsanzahl der Zugriffsüberprüfungen aktualisieren|
|Gruppenverwaltung|Einstellung für die Wiederholungsdauer der Zugriffsüberprüfungen in Tagen aktualisieren|
|Benutzerverwaltung|Einstellung für den Wiederholungsendtyp der Zugriffsüberprüfungen aktualisieren|
|Benutzerverwaltung|Einstellung für den Wiederholungstyp der Zugriffsüberprüfungen aktualisieren|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Überwachungskategorie|Aktivität|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Hinzugefügt|
|PIM|AddedOutsidePIM|
|PIM|Zuweisen|
|PIM|DismissAlert|
|PIM|Erhöhen|
|PIM|ReactivateAlert|
|PIM|Entfernt|
|PIM|AddedOutsidePIM|
|PIM|Überprüfungsbeendigung anfordern|
|PIM|Änderungen für Rolleneinstellung|
|PIM|ScanAlertsNow|
|PIM|Signup|
|PIM|Zuweisung aufheben|
|PIM|Deautorisieren|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Self-Service-Gruppenverwaltung

|Überwachungskategorie|Aktivität|
|---|---|
|Gruppenverwaltung|Benutzerkennwort zurücksetzen|
|Gruppenverwaltung|Benutzer wiederherstellen|
|Gruppenverwaltung|Änderung des Benutzerkennworts erzwingen|
|Gruppenverwaltung|Set user manager (Benutzer-Manager festlegen)|
|Gruppenverwaltung|Set users oath token metadata enabled (OATH-Tokenmetadaten der Benutzer auf „Aktiviert“ festlegen)|
|Gruppenverwaltung|Update StsRefreshTokenValidFrom Timestamp (Zeitstempel StsRefreshTokenValidFrom aktualisieren)|
|Gruppenverwaltung|Externe geheime Schlüssel aktualisieren|
|Gruppenverwaltung|Benutzer aktualisieren|
|Gruppenverwaltung|Admin generates a temporary password (Administrator generiert ein temporäres Kennwort.)|


## <a name="self-service-password-management"></a>Self-service password management (Self-Service-Kennwortverwaltung)

|Überwachungskategorie|Aktivität|
|---|---|
|Verzeichnisverwaltung|Admins requires the user to reset their password (Administratoren fordern Benutzer zum Zurücksetzen ihres Kennworts auf.)|
|Verzeichnisverwaltung|Externen Benutzer einer Anwendung zuweisen|
|Benutzerverwaltung|Email not sent, user unsubscribed (E-Mail nicht gesendet, Abonnement des Benutzers gekündigt)|
|Benutzerverwaltung|Externen Benutzer einladen|
|Benutzerverwaltung|Einlösen einer externen Benutzereinladung|
|Benutzerverwaltung|Erstellen eines viralen Mandanten|
|Benutzerverwaltung|Erstellen eines viralen Benutzers|
|Benutzerverwaltung|User Password Registration (Benutzerkennwortregistrierung)|
|Benutzerverwaltung|User Password Reset (Benutzerkennwortzurücksetzung)|
|Benutzerverwaltung|Blocked from self-service password reset (Von Self-Service-Kennwortzurücksetzung ausgeschlossen)|


## <a name="terms-of-use"></a>Nutzungsbedingungen

|Überwachungskategorie|Aktivität|
|---|---|
|Nutzungsbedingungen|Accept Terms Of Use (Nutzungsbedingungen akzeptieren)|
|Nutzungsbedingungen|Create Terms Of Use (Nutzungsbedingungen erstellen)|
|Nutzungsbedingungen|Decline Terms Of Use (Nutzungsbedingungen ablehnen)|
|Nutzungsbedingungen|Zustimmung löschen|
|Nutzungsbedingungen|Delete Terms Of Use (Nutzungsbedingungen löschen)|
|Nutzungsbedingungen|Edit Terms Of Use (Nutzungsbedingungen bearbeiten)|
|Nutzungsbedingungen|Ablauf von Nutzungsbedingungen|
|Nutzungsbedingungen|Nutzungsbedingungen endgültig löschen|
|Nutzungsbedingungen|Publish Terms Of Use (Nutzungsbedingungen veröffentlichen)|
|Nutzungsbedingungen|Unpublish Terms Of Use (Veröffentlichung der Nutzungsbedingungen aufheben)|


## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure AD-Berichte](overview-reports.md)
- [Bericht „Überwachungsprotokolle“](concept-audit-logs.md) 
- [Programmgesteuerter Zugriff auf Azure AD-Berichte](concept-reporting-api.md)
