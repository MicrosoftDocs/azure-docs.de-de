---
title: Benutzerdefinierte Rollenberechtigungen für die App-Registrierung – Azure AD | Microsoft-Dokumentation
description: Delegieren Sie benutzerdefinierte Administratorrollenberechtigungen für die Verwaltung von App-Registrierungen.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0e2f520f55b9664d2d0b039867ef7670b190fed
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377070"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Berechtigungen für Anwendungsregistrierung für benutzerdefinierte Rollen in Azure Active Directory

Dieser Artikel enthält die derzeit verfügbaren Anwendungsregistrierungsberechtigungen für benutzerdefinierte Rollendefinitionen in Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Berechtigungen zum Verwalten von Anwendungen mit einem Mandanten

Beim Auswählen der Berechtigungen für Ihre benutzerdefinierte Rolle haben Sie die Möglichkeit, Zugriff nur zum Verwalten von Anwendungen mit einem Mandanten zu gewähren. Anwendungen mit einem Mandanten sind nur für Benutzer in der Azure AD-Organisation verfügbar, in der die Anwendung registriert ist. Per Definition ist bei Anwendungen mit einem Mandanten **Unterstützte Kontotypen** auf „Nur Konten in diesem Organisationsverzeichnis“ festgelegt. In der Graph-API ist für Einzelmandantenanwendungen die signInAudience-Eigenschaft auf „AzureADMyOrg“ festgelegt.

Verwenden Sie zum Gewähren des Zugriffs nur zum Verwalten von Anwendungen mit einem Mandanten die unten aufgeführten Berechtigungen mit dem Untertyp **applications.myOrganization**. Beispiel: microsoft.directory/applications.myOrganization/basic/update.

Eine Erläuterung der allgemeinen Begriffe „Untertyp“, „Berechtigung“ und „Eigenschaftensatz“ finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md). Die folgenden Informationen gelten speziell für Anwendungsregistrierungen.

### <a name="create-and-delete"></a>Erstellen und Löschen

Für die Erstellung von Anwendungsregistrierungen stehen zwei Berechtigungen zur Verfügung, die jeweils ein anderes Verhalten aufweisen:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Wird diese Berechtigung zugewiesen, wird der Ersteller als erster Besitzer der erstellten App-Registrierung hinzugefügt, und die erstellte App-Registrierung zählt zum Objekterstellungskontingent des Erstellers (250 Objekte).

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Wird diese Berechtigung zugewiesen, wird der Ersteller nicht als erster Besitzer der erstellten App-Registrierung hinzugefügt, und die erstellte App-Registrierung zählt nicht zum Kontingent von 250 erstellten Objekten des Erstellers. Verwenden Sie diese Berechtigung mit Bedacht, da der zugewiesene Benutzer in diesem Fall so viele App-Registrierungen erstellen kann, bis das Kontingent auf der Verzeichnisebene erreicht ist. Sind beide Berechtigungen zugewiesen, hat diese Berechtigung Vorrang.

Wenn beide Berechtigungen zugewiesen sind, hat die Berechtigung zum Erstellen („/create“) Vorrang. Obwohl mit der Berechtigung „/createAsOwner“ der Ersteller nicht automatisch als erster Besitzer hinzufügt wird, können Besitzer bei der Erstellung der App-Registrierung angegeben werden, wenn Graph-APIs oder PowerShell-Cmdlets verwendet werden.

Berechtigungen zum Erstellen gewähren Zugriff auf den Befehl **Neue Registrierung**.

[Diese Berechtigungen gewähren Zugriff auf den Befehl „Neue Registrierung“ im Portal.](./media/custom-available-permissions/new-custom-role.png)

Für die Erteilung der Möglichkeit zum Löschen von Anwendungsregistrierungen stehen zwei Berechtigungen zur Verfügung:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Erteilt die Möglichkeit zum Löschen von Anwendungsregistrierungen unabhängig vom Untertyp, d. h. für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Erteilt die Möglichkeit zum Löschen von Anwendungsregistrierungen mit Beschränkung auf die Registrierungen, die nur für Konten in Ihrer Organisation oder Einzelmandantenanwendungen zugänglich sind (Untertyp myOrganization).

![Diese Berechtigungen gewähren Zugriff auf den Befehl „App-Registrierung löschen“.](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Beim Zuweisen einer Rolle, die Berechtigungen zum Erstellen enthält, muss die Rollenzuweisung im Verzeichnisbereich vorgenommen werden. Eine Berechtigung zum Erstellen, die in einem Ressourcenbereich zugewiesen wird, erteilt nicht die Möglichkeit zum Erstellen von Anwendungsregistrierungen.

### <a name="read"></a>Lesen

Alle Mitgliedsbenutzer in der Organisation können standardmäßig App-Registrierungsinformationen lesen. Gastbenutzer und Anwendungsdienstprinzipale können dies dagegen nicht. Wenn Sie einem Gastbenutzer oder einer Anwendung eine Rolle zuweisen möchten, müssen Sie die entsprechenden Leseberechtigungen einfügen.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Möglichkeit zum Lesen aller Eigenschaften von Einzelmandantenanwendungen und mehrinstanzenfähigen Anwendungen, mit Ausnahme von Eigenschaften (wie Anmeldeinformationen), die nicht in allen Situationen gelesen werden können.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/allProperties/read, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Erteilt die Berechtigung zum Lesen der Eigenschaft „Besitzer“ für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen. Gewährt Zugriff auf alle Felder auf der Seite „Besitzer“ der Anwendungsregistrierung:

![Diese Berechtigung gewährt Zugriff auf die Seite „Besitzer“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Gewährt Zugriff zum Lesen von Standardeigenschaften für die Anwendungsregistrierung. Dies schließt Eigenschaften über Anwendungsregistrierungsseiten ein.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/standard/read, jedoch nur für Einzelmandantenanwendungen.

### <a name="update"></a>Aktualisieren

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Möglichkeit zum Aktualisieren aller Eigenschaften von Anwendungen mit einem Mandanten und mehrinstanzenfähigen Anwendungen.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/allProperties/update, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Möglichkeit zum Aktualisieren der (signInAudience)-Eigenschaft des unterstützten Kontotyps von Anwendungen mit einem Mandanten und mehrinstanzenfähigen Anwendungen.

![Diese Berechtigung gewährt Zugriff auf die Eigenschaft des unterstützten Kontotyps der App-Registrierung auf der Authentifizierungsseite.](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/audience/update, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Berechtigung zum Aktualisieren der Eigenschaften der Antwort-URL, der Abmelde-URL, des impliziten Ablaufs und der Herausgeberdomäne für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen. Gewährt Zugriff auf alle Felder auf der Seite „Authentifizierung“ der Anwendungsregistrierung, außer auf „Unterstützte Kontotypen“:

![Gewährt Zugriff auf die Authentifizierung der Anwendungsregistrierung, jedoch nicht auf unterstützte Kontotypen.](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/authentication/update, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Berechtigung zum Aktualisieren der Eigenschaften des Namens, des Logos, der URL der Startseite, der URL zu den Vertragsbedingungen und der URL zur Datenschutzerklärung für Einzelmandantenanwendungen oder mehrinstanzenfähige Anwendungen. Gewährt Zugriff auf alle Felder auf der Seite „Branding“ der Anwendungsregistrierung:

![Diese Berechtigung gewährt Zugriff auf die Seite „Branding“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/basic/update, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Berechtigung zum Aktualisieren der Eigenschaften der Zertifikate und der geheimen Clientschlüssel für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen. Gewährt Zugriff auf alle Felder auf der Seite „Zertifikate & Geheimnisse“ der Anwendungsregistrierung:

![Diese Berechtigung gewährt Zugriff auf die Seite „Zertifikate & Geheimnisse“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/credentials/update, jedoch nur für Anwendungen mit einem Mandanten.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Berechtigung zum Aktualisieren der Eigenschaft „Besitzer“ für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen. Gewährt Zugriff auf alle Felder auf der Seite „Besitzer“ der Anwendungsregistrierung:

![Diese Berechtigung gewährt Zugriff auf die Seite „Besitzer“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/owners/update, jedoch nur für Einzelmandantenanwendungen.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Berechtigungen zum Aktualisieren der Eigenschaften der delegierten Berechtigungen, Anwendungsberechtigungen, autorisierten Clientanwendungen, erforderlichen Berechtigungen und der Erteilung der Einwilligung für Einzelmandantenanwendungen und mehrinstanzenfähige Anwendungen. Erteilt nicht die Berechtigung zum Durchführen der Einwilligung. Gewährt Zugriff auf alle Felder auf den Seiten „API-Berechtigungen“ und „Eine API verfügbar machen“ der Anwendungsregistrierung:

![Diese Berechtigung gewährt Zugriff auf die Seite „API-Berechtigungen“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-api-permissions.png)

![Diese Berechtigung gewährt Zugriff auf die Seite „Eine API verfügbar machen“ der Anwendungsregistrierung.](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Erteilt die gleichen Berechtigungen wie microsoft.directory/applications/permissions/update, jedoch nur für Einzelmandantenanwendungen.

## <a name="required-license-plan"></a>Erforderlicher Lizenzplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erstellen von benutzerdefinierten Rollen über das [Azure-Portal, Azure AD PowerShell und die Graph-API](custom-create.md)
- [Anzeigen der Zuweisungen für eine benutzerdefinierte Rolle](custom-view-assignments.md)
