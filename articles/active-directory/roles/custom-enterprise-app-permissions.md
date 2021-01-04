---
title: App-Berechtigungen für benutzerdefinierte Rollen in Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie eine Vorschau der Berechtigungen für Unternehmens-Apps für benutzerdefinierte Azure AD-Rollen im Azure-Portal, in PowerShell oder für die Graph-API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39d9cf9ed87c8a8f45cb2a6239292562035d31e3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379637"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Berechtigungen für Unternehmensanwendungen für benutzerdefinierte Rollen in Azure Active Directory

Dieser Artikel enthält die derzeit verfügbaren Berechtigungen für Unternehmensanwendungen für benutzerdefinierte Rollendefinitionen in Azure Active Directory (Azure AD). In diesem Artikel finden Sie Berechtigungslisten für einige gängige Szenarien sowie die vollständige Liste der Berechtigungen für Unternehmens-Apps. Anwendungsproxyberechtigungen werden in diesem Release nicht eingeführt.

## <a name="required-license-plan"></a>Erforderlicher Lizenzplan

Für dieses Feature benötigen Sie eine Azure AD Premium P1-Lizenz für Ihre Azure AD-Organisation. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich: Allgemein verfügbare Features der Editionen Free, Basic und Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Berechtigungen für Unternehmensanwendungen

Weitere Informationen zur Verwendung dieser Berechtigungen finden Sie unter [Zuweisen benutzerdefinierter Rollen zum Verwalten von Unternehmens-Apps in Azure Active Directory](custom-enterprise-apps.md).

### <a name="assigning-users-or-groups-to-an-application"></a>Zuweisen von Benutzern oder Gruppen zu einer Anwendung

Dient zum Delegieren der Zuweisung von Benutzern und Gruppen, die auf SAML-basierte SSO-Anwendungen zugreifen können. Erforderliche Berechtigungen

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>Erstellen von Kataloganwendungen

Dient zum Delegieren der Erstellung von Azure AD-Kataloganwendungen wie ServiceNow, F5, Salesforce und Ähnlichem. Erforderliche Berechtigungen:

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>Konfigurieren grundlegender SAML-URLs

Dient zum Delegieren der Aktualisierung und des Lesens grundlegender SAML-Konfigurationen für SAML-basierte SSO-Anwendungen. Erforderliche Berechtigungen:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>Durchführen des Rollovers von Signaturzertifikaten oder Erstellen von Signaturzertifikaten

Dient zum Delegieren der Verwaltung von Signaturzertifikaten für SAML-basierte SSO-Anwendungen. Erforderliche Berechtigungen:

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Aktualisieren der E-Mail-Adresse für Benachrichtigungen bei ablaufenden Anmeldezertifikaten

Dient zum Delegieren der Aktualisierung der E-Mail-Adressen für Benachrichtigungen bei ablaufenden Anmeldezertifikaten für SAML-basierte SSO-Anwendungen. Erforderliche Berechtigungen:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Verwalten der SAML-Tokensignatur und des Anmeldealgorithmus

Dient zum Delegieren der Aktualisierung der SAML-Tokensignatur und des Anmeldealgorithmus für SAML-basierte SSO-Anwendungen. Erforderliche Berechtigungen:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>Verwalten von Benutzerattributen und Ansprüchen

Dient zum Delegieren der Erstellung, Löschung und Aktualisierung von Benutzerattributen und -ansprüchen für SAML-basierte SSO-Anwendungen. Erforderliche Berechtigungen:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>App-Bereitstellungsberechtigungen

Zum Ausführen von Schreibvorgängen (etwa beim Verwalten des Auftrags, des Schemas oder der Anmeldeinformationen über die Benutzeroberfläche) sind auch die Leseberechtigungen erforderlich, um die Bereitstellungsseite anzuzeigen.

Wenn Sie den Bereich auf alle Benutzer und Gruppen oder auf zugewiesene Benutzer und Gruppen festlegen, sind aktuell die Berechtigungen „synchronizationJob“ und „synchronizationCredentials“ erforderlich.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Aktivieren oder Neustarten von Bereitstellungsaufträgen

Dient zum Delegieren der Möglichkeit zum Aktivieren, Deaktivieren und Neustarten von Bereitstellungsaufträgen. Erforderliche Berechtigungen:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>Konfigurieren des Bereitstellungsschemas  

Dient zum Delegieren von Aktualisierungen für die Attributzuordnung. Erforderliche Berechtigungen:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind

Dient zum Delegieren der Möglichkeit zum Lesen von Bereitstellungseinstellungen, die dem Objekt zugeordnet sind. Erforderliche Berechtigungen:

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind

Dient zum Delegieren der Möglichkeit zum Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind. Erforderliche Berechtigungen:

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>Autorisieren des Anwendungszugriffs für die Bereitstellung  

Dient zum Delegieren der Möglichkeit zum Autorisieren des Anwendungszugriffs für die Bereitstellung. Beispieleingabe: OAuth-Bearertoken. Erforderliche Berechtigungen:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>Vollständige Liste der Berechtigungen

Berechtigung | BESCHREIBUNG
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | Lesen aller Eigenschaften für Anwendungsrichtlinien
microsoft.directory/applicationPolicies/allProperties/update | Aktualisieren aller Eigenschaften für Anwendungsrichtlinien
microsoft.directory/applicationPolicies/basic/update | Aktualisieren der Standardeigenschaften von Anwendungsrichtlinien
microsoft.directory/applicationPolicies/create | Erstellen von Anwendungsrichtlinien
microsoft.directory/applicationPolicies/createAsOwner | Erstellen von Anwendungsrichtlinien. Der Ersteller wird als erster Besitzer hinzugefügt.
microsoft.directory/applicationPolicies/delete | Löschen von Anwendungsrichtlinien
microsoft.directory/applicationPolicies/owners/read | Lesen von Besitzern für Anwendungsrichtlinien
microsoft.directory/applicationPolicies/owners/update | Aktualisieren der Besitzereigenschaft von Anwendungsrichtlinien
microsoft.directory/applicationPolicies/policyAppliedTo/read | Lesen der Liste der Anwendungsrichtlinien, die auf Objekte angewendet wurden
microsoft.directory/applicationPolicies/standard/read | Lesen der Standardeigenschaften von Anwendungsrichtlinien
microsoft.directory/servicePrincipals/allProperties/allTasks | Erstellen und Löschen von servicePrincipals und Lesen und Aktualisieren aller Eigenschaften in Azure Active Directory
microsoft.directory/servicePrincipals/allProperties/read | Lesen aller Eigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/allProperties/update | Aktualisieren aller Eigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lesen von Rollenzuweisungen für Dienstprinzipale
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualisieren von Rollenzuweisungen für Dienstprinzipale
microsoft.directory/servicePrincipals/appRoleAssignments/read | Lesen von Rollenzuweisungen, die Dienstprinzipalen zugewiesen sind
microsoft.directory/servicePrincipals/audience/update | Aktualisieren von Zielgruppeneigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/authentication/update | Aktualisieren von Authentifizierungseigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/basic/update | Aktualisieren grundlegender Eigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/create | Erstellen von Dienstprinzipalen
microsoft.directory/servicePrincipals/createAsOwner | Erstellen von Dienstprinzipalen. Der Ersteller wird als erster Besitzer hinzugefügt.
microsoft.directory/servicePrincipals/credentials/update | Aktualisieren von Anmeldeinformationseigenschaften für Dienstprinzipale
microsoft.directory/servicePrincipals/delete | Löschen von Dienstprinzipalen
microsoft.directory/servicePrincipals/disable | Deaktivieren von Dienstprinzipalen
microsoft.directory/servicePrincipals/enable | Aktivieren von Dienstprinzipalen
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Lesen des Kennworts von SSO-Anmeldeinformationen für Dienstprinzipale
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Verwalten des Kennworts von SSO-Anmeldeinformationen für Dienstprinzipale
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lesen delegierter Berechtigungsgewährungen für Dienstprinzipale
microsoft.directory/servicePrincipals/owners/read | Lesen von Besitzern für Dienstprinzipale
microsoft.directory/servicePrincipals/owners/update | Aktualisieren von Besitzern für Dienstprinzipale
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | Lesen von Richtlinien für Dienstprinzipale
microsoft.directory/servicePrincipals/policies/update | Aktualisieren von Richtlinien für Dienstprinzipale
microsoft.directory/servicePrincipals/standard/read | Lesen der Standardeigenschaften von Dienstprinzipalen
microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Ihrem Dienstprinzipal zugeordnet sind
microsoft.directory/servicePrincipals/tag/update | Aktualisieren der Tageigenschaft für Dienstprinzipale
microsoft.directory/applicationTemplates/instantiate | Instanziieren von Kataloganwendungen über Anwendungsvorlagen
microsoft.directory/auditLogs/allProperties/read | Lesen von Überwachungsprotokollen
microsoft.directory/signInReports/allProperties/read | Lesen von Anmeldeberichten
microsoft.directory/applications/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die dem Anwendungsobjekt zugeordnet sind
microsoft.directory/servicePrincipals/synchronizationJobs/manage | Verwalten sämtlicher Aspekte der Auftragssynchronisierung für Dienstprinzipalressourcen
microsoft.directory/servicePrincipals/synchronization/standard/read | Lesen von Bereitstellungseinstellungen, die Dienstprinzipalen zugeordnet sind
microsoft.directory/servicePrincipals/synchronizationSchema/manage | Verwalten sämtlicher Aspekte der Schemasynchronisierung für Dienstprinzipalressourcen
microsoft.directory/provisioningLogs/allProperties/read | Lesen aller Eigenschaften von Bereitstellungsprotokollen

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Zuweisen einer benutzerdefinierten Rolle in Azure Active Directory](custom-create.md)
- [Anzeigen der Zuweisungen für eine benutzerdefinierte Rolle](custom-view-assignments.md)
