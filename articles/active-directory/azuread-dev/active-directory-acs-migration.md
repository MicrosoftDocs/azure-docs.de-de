---
title: Migrieren aus dem Azure Access Control Service | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Optionen zum Verschieben von Apps und Diensten aus Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: eda648a4d00a0ab4a51c66510060ce16421972ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020009"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Gewusst wie: Migrieren aus dem Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), ein Dienst von Azure Active Directory (Azure AD), wird am 7. November 2018 eingestellt. Anwendungen und Dienste, die aktuell Access Control verwenden, müssen bis zu diesem Zeitpunkt vollständig zu einem anderen Authentifizierungsmechanismus migriert sein. Dieser Artikel gibt Empfehlungen für aktuelle Kunden für die Planung der Außerdienststellung von Access Control. Wenn Sie aktuell Access Control nicht verwenden, brauchen Sie keine Aktivitäten zu ergreifen.

## <a name="overview"></a>Übersicht

Access Control ist ein Dienst für die Cloudauthentifizierung, der eine einfache Möglichkeit zum Authentifizieren und Autorisieren von Benutzern für den Zugriff auf Ihre Webanwendungen und -dienste zur Verfügung stellt. Er ermöglicht die Auslagerung vieler Authentifizierungs- und Autorisierungsfunktionen aus Ihrem Code. Access Control wird hauptsächlich von Entwicklern und Architekten von Microsoft .NET-Clients, ASP.NET-Web-Anwendungen und WCF-Webdiensten (Windows Communication Foundation) verwendet.

Die Anwendungsfälle für Access Control können in drei Hauptkategorien aufgeteilt werden:

- Authentifizierung bei bestimmten Microsoft-Clouddiensten, einschließlich Azure Service Bus und Dynamics CRM. Clientanwendungen erhalten von Access Control Token, um sich bei diesen Diensten für die Ausführung verschiedener Aktionen zu authentifizieren.
- Hinzufügen von Authentifizierung zu Webanwendungen, sowohl benutzerdefinierten als auch fertig konfektionierten (wie SharePoint). Mithilfe der „passiven“ Authentifizierung von Access Control können Webanwendungen die Anmeldung mit einem Microsoft-Konto (früher Live ID) und mit Konten von Google, Facebook, Yahoo, Azure AD und AD FS (Active Directory Federation Services) unterstützen.
- Schützen von benutzerdefinierten Webdiensten mit von Access Control ausgestellten Token. Mithilfe von „aktiver“ Authentifizierung können Webdienste sicherstellen, dass sie den Zugriff nur bekannten Clients gestatten, die sich bei Access Control authentifiziert haben.

Die einzelnen Anwendungsfälle und ihre empfohlenen Migrationsstrategien werden in den folgenden Abschnitten erläutert.

> [!WARNING]
> In den meisten Fällen sind erhebliche Codeänderungen erforderlich, um vorhandene Apps und Dienste zu neueren Technologien zu migrieren. Wir empfehlen, umgehend mit der Planung und Ausführung der Migration zu beginnen, um mögliche Ausfälle und Ausfallzeiten zu vermeiden.

Access Control enthält die folgenden Komponenten:

- Einen Sicherheitstokendienst (Secure Token Service, STS), der Authentifizierungsanforderungen empfängt und daraufhin Sicherheitstoken ausstellt.
- Das klassische Azure-Portal, in dem Sie Access Control-Namespaces erstellen, löschen sowie aktivieren und deaktivieren.
- Ein separates Access Control-Verwaltungsportal, in dem Sie Access Control-Namespaces anpassen und konfigurieren.
- Einen Verwaltungsdienst, den Sie verwenden können, um die Funktionen des Portals zu automatisieren.
- Ein Tokentransformationsregel-Modul, mit dem Sie komplexe Logik zum Bearbeiten des Formats der von Access Control ausgestellten Token definieren können.

Um diese Komponenten zu verwenden, müssen Sie mindestens einen Access Control-Namespace verwenden. Ein *Namespace* ist eine dedizierte Instanz von Access Control, mit der Ihre Anwendungen und Dienste kommunizieren. Ein Namespace ist von allen anderen Access Control-Kunden isoliert. Andere Access Control-Kunden verwenden ihre eigenen Namespaces. Ein Namespace in Access Control weist eine dedizierte URL auf, die wie folgt aussieht:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Die gesamte Kommunikation mit dem STS und die Verwaltungsvorgänge erfolgen unter dieser URL. Verwenden Sie unterschiedliche Pfade für unterschiedliche Zwecke. Um zu bestimmen, ob Ihre Anwendungen oder Dienste Access Control verwenden, überprüfen Sie eingehenden Datenverkehr an „https://&lt;namespace&gt;.accesscontrol.windows.net“. Jeglicher Verkehr unter dieser URL wird von Access Control verarbeitet und muss eingestellt werden. 

Die Ausnahme hiervon ist sämtlicher bei `https://accounts.accesscontrol.windows.net` eingehender Datenverkehr. Datenverkehr an diese URL wird bereits von einem anderen Dienst verarbeitet und **ist nicht** von der Deaktivierung von Access Control betroffen. 

Weitere Informationen zu Access Control finden Sie unter [Access Control Service 2.0 (archiviert)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ermitteln der betroffenen Apps

Führen Sie die Schritte in diesem Abschnitt aus, um zu ermitteln, welche Apps von der ACS-Deaktivierung betroffen sind.

### <a name="download-and-install-acs-powershell"></a>Herunterladen und Installieren von ACS PowerShell

1. Wechseln Sie zum PowerShell-Katalog, und laden Sie [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2) herunter.
2. Installieren Sie das Modul, indem Sie Folgendes ausführen:

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Rufen Sie eine Liste mit den möglichen Befehlen ab, indem Sie Folgendes ausführen:

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Führen Sie Folgendes aus, um Hilfe zu einem bestimmten Befehl zu erhalten:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    Hierbei steht `[Command-Name]` für den Namen des ACS-Befehls.

### <a name="list-your-acs-namespaces"></a>Auflisten Ihrer ACS-Namespaces

1. Stellen Sie eine Verbindung mit ACS her, indem Sie das Cmdlet **Connect-AcsAccount** verwenden.
  
    Unter Umständen müssen Sie `Set-ExecutionPolicy -ExecutionPolicy Bypass` ausführen, bevor Sie Befehle ausführen können, und Sie müssen hierfür der Administrator dieser Abonnements sein.

2. Listen Sie Ihre verfügbaren Azure-Abonnements auf, indem Sie das Cmdlet **Get-AcsSubscription** verwenden.
3. Listen Sie Ihre ACS-Namespaces auf, indem Sie das Cmdlet **Get-AcsNamespace** verwenden.

### <a name="check-which-applications-will-be-impacted"></a>Überprüfen, welche Anwendungen betroffen sind

1. Verwenden Sie den Namespace aus dem vorherigen Schritt, und navigieren Sie zu `https://<namespace>.accesscontrol.windows.net`.

    Navigieren Sie beispielsweise zu `https://contoso-test.accesscontrol.windows.net`, wenn einer der Namespaces „contoso-test“ ist.

2. Wählen Sie unter **Vertrauensstellungen** die Option **Anwendungen der vertrauenden Seite**, um die Liste mit den Apps anzuzeigen, auf die sich die ACS-Deaktivierung auswirkt.
3. Wiederholen Sie die Schritte 1 und 2 für alle anderen ACS-Namespaces, die ggf. vorhanden sind.

## <a name="retirement-schedule"></a>Deaktivierungszeitplan

Im November 2017 werden alle Access Control-Komponenten in vollem Umfang unterstützt und sind voll betriebsfähig. Die einzige Einschränkung besteht darin, dass Sie [im klassischen Azure-Portal keine neuen Access Control-Namespaces erstellen können](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Dies ist der Zeitplan für die Deaktivierung von Access Control-Komponenten:

- **November 2017:**  Die Azure AD-Administratoroberfläche im klassischen Azure-Portal wird [deaktiviert](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Zu diesem Zeitpunkt können Namespaces für Access Control unter der folgenden neuen dedizierten URL verwaltet werden: `https://manage.windowsazure.com?restoreClassic=true`. Verwenden Sie diesen URl, um Ihre vorhandenen Namespaces anzuzeigen, Namespaces zu aktivieren und deaktivieren und ggf. zu löschen.
- **2. April 2018**: Das klassische Azure-Portal ist vollständig deaktiviert. Dies bedeutet, dass Namespaces für Access Control nicht mehr über jede URL verwaltet werden können. Zu diesem Zeitpunkt können Sie Ihre Access Control-Namespaces nicht deaktivieren oder aktivieren, löschen oder aufzählen. Das Access Control-Verwaltungsportal ist jedoch voll funktionsfähig und befindet sich unter „`https://\<namespace\>.accesscontrol.windows.net`“. Alle sonstigen Komponenten von Access Control arbeiten weiterhin normal.
- **7. November 2018**: Alle Komponenten von Access Control werden endgültig heruntergefahren. Dazu zählen das Access Control-Verwaltungsportal, der Verwaltungsdienst, STS und das Tokentransformationsregel-Modul. Derzeit tritt bei allen Anforderungen, die an Access Control (unter der Adresse „\<namespace\>.accesscontrol.windows.net“) gesendet werden, ein Fehler auf. Alle vorhandenen Apps und Dienste müssen vor diesem Zeitpunkt zu anderen Technologien migriert worden sein.

> [!NOTE]
> Eine Richtlinie deaktiviert Namespaces, die für eine bestimmte Zeit kein Token angefordert haben. Seit Anfang September 2018 beträgt dieser Zeitraum 14 Tage der Inaktivität, dieser wird aber in den kommenden Wochen auf 7 Tage Inaktivität verkürzt. Wenn Sie Access Control-Namespaces, die zurzeit deaktiviert sind, verwenden, können Sie [ACS PowerShell herunterladen und installieren](#download-and-install-acs-powershell), um die Namespaces wieder zu aktivieren.

## <a name="migration-strategies"></a>Migrationsstrategien

Die folgenden Abschnitte beschreiben allgemeine Empfehlungen für die Migration von Access Control zu anderen Microsoft-Technologien.

### <a name="clients-of-microsoft-cloud-services"></a>Clients von Microsoft-Clouddiensten

Alle Microsoft-Clouddienste, die von Access Control ausgestellte Token akzeptieren, unterstützen jetzt mindestens eine alternative Authentifizierungsmethode. Der richtige Authentifizierungsmechanismus unterscheidet sich zwischen den einzelnen Diensten. Wir empfehlen, sich an der offiziellen Anleitung in Form der spezifischen Dokumentation der einzelnen Dienste zu orientieren. Der Einfachheit halber sind die Dokumentationen hier aufgeführt:

| Dienst | Anleitungen |
| ------- | -------- |
| Azure-Servicebus | [Migrieren zu Shared Access Signatures](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migrieren zu Shared Access Signatures](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migrieren zu Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [DataMarket ist jetzt veraltet](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrieren zum Feature Logic Apps von Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Azure Media Services announces support for AAD and deprecation of ACS authentication](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) (Azure Media Services verkündet Unterstützung für AAD sowie Einstellung der ACS-Authentifizierung) |
| Azure Backup | [Upgrade des Azure Backup-Agents](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-Kunden

Kunden von SharePoint 2013, 2016 und SharePoint Online haben ACS lange zu Authentifizierungszwecken in Cloud-, lokalen und hybriden Szenarios verwendet. Einige SharePoint-Funktionen und -Anwendungsfälle sind von der ACS-Deaktivierung betroffen, andere nicht. In der folgenden Tabelle sind Anleitungen zur Migration für einige der am häufigsten verwendeten SharePoint-Features zusammengefasst, die ACS nutzen:

| Funktion | Anleitungen |
| ------- | -------- |
| Authentifizieren von Benutzern aus Azure AD | Bisher wurden SAML 1.1-Token, die SharePoint zur Authentifizierung benötigt, von Azure AD nicht unterstützt, und ACS wurde als Zwischenstufe verwendet, die SharePoint mit Azure AD-Tokenformaten kompatibel machte. Jetzt können Sie [SharePoint mithilfe der lokalen SharePoint-App aus dem Azure AD-App-Katalog direkt mit Azure AD verbinden](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [Lokale App-Authentifizierung und Server-zu-Server-Authentifizierung in SharePoint](/SharePoint/security-for-sharepoint-server/authentication-overview) | Von ACS-Deaktivierung nicht betroffen. Es sind keine Änderungen erforderlich. | 
| [Autorisierung auf niedriger Vertrauensebene für SharePoint-Add-Ins (Anbieter gehostet und SharePoint gehostet)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Von ACS-Deaktivierung nicht betroffen. Es sind keine Änderungen erforderlich. |
| [SharePoint-Cloudhybridsuche](/archive/blogs/spses/cloud-hybrid-search-service-application) | Von ACS-Deaktivierung nicht betroffen. Es sind keine Änderungen erforderlich. |

### <a name="web-applications-that-use-passive-authentication"></a>Webanwendungen, die passive Authentifizierung verwenden

Für Webanwendungen, die Access Control für die Benutzerauthentifizierung verwenden, stellt Access Control für Entwickler und Architekten von Webanwendungen die folgenden Features und Funktionen bereit:

- Umfassende Integration mit Windows Identity Foundation (WIF)
- Verbund mit Google-, Facebook-, Yahoo-, Azure Active Directory- und AD FS-Konten sowie Microsoft-Konten.
- Unterstützung der folgenden Authentifizierungsprotokolle: OAuth 2.0 Draft 13, WS-Trust und Webdiensteverbund (WS-Verbund).
- Unterstützung der folgenden Tokenformate: JSON Web Token (JWT), SAML 1.1, SAML 2.0 und Simple Web Token (SWT).
- In WIF integrierte Startbereichsermittlung, die Benutzern das Auswählen des Kontotyps für die Anmeldung ermöglicht. Diese Funktion wird von der Webanwendung gehostet und ist vollständig anpassbar.
- Tokentransformation, die die umfassende Anpassung der Ansprüche ermöglicht, die von der Webanwendung von Access Control empfangen werden, einschließlich:
    - Übergeben von Ansprüchen von Identitätsanbietern
    - Hinzufügen zusätzlicher benutzerdefinierter Ansprüche
    - Einfache If-Then-Logik zum Ausstellen von Ansprüchen unter bestimmten Bedingungen

Leider gibt es keinen Dienst, der alle diese Funktionen in gleichwertiger Weise bereitstellt. Sie sollten entscheiden, welche Funktionen von Access Control Sie benötigen, und dann zwischen [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) und anderen Cloudauthentifizierungsdiensten wählen.

#### <a name="migrate-to-azure-active-directory"></a>Migrieren zu Azure Active Directory

Eine überlegenswerte Möglichkeit ist die direkte Integration Ihrer Apps und Dienste in Azure AD. Azure AD ist der cloudbasierte Identitätsanbieter für Geschäfts-, Schul- oder Unikonten von Microsoft. Azure AD ist der Identitätsanbieter für Microsoft 365, Azure und vieles mehr. Er bietet ähnliche Verbundauthentifizierungsfunktionen wie Access Control, unterstützt jedoch nicht alle Access Control-Features. 

Ein gutes Beispiel ist der Verbund mit Identitätsanbietern aus sozialen Netzwerken wie Facebook, Google und Yahoo. Wenn Ihre Benutzer sich mit dieser Art von Anmeldeinformationen anmelden, ist Azure AD keine geeignete Lösung für Sie. 

Azure AD unterstützt auch nicht unbedingt genau die gleichen Authentifizierungsprotokolle wie Access Control. Beispielsweise wird OAuth sowohl von Access Control als auch von Azure AD unterstützt, jedoch bestehen feine Unterschiede zwischen den Implementierungen. Verschiedene Implementierungen machen es erforderlich, dass Sie Code im Rahmen einer Migration ändern.

Allerdings bietet Azure AD für Access Control-Kunden mehrere potenzielle Vorteile. Der Dienst unterstützt nativ in der Cloud gehostete Geschäfts-, Schul- oder Unikonten von Microsoft, die häufig von Access Control-Kunden verwendet werden. 

Ein Azure AD-Mandant kann außerdem über AD FS im Verbund mit einer oder mehrerer Instanz(en) eines lokalen Active Directorys verknüpft sein. Auf diese Weise kann Ihre App sowohl cloudbasierte Benutzer als auch lokal gehostete Benutzer authentifizieren. Darüber hinaus unterstützt Azure AD das WS-Verbundprotokoll, wodurch sich die Integration in eine Webanwendung mithilfe von WIF relativ unkompliziert gestaltet.

Die folgende Tabelle vergleicht die für Webanwendungen relevanten Funktionen von Access Control mit den in Azure AD verfügbaren Funktionen. 

Im Allgemeinen ist *Azure Active Directory wahrscheinlich die richtige Wahl für Ihre Migration, wenn Sie für Ihre Benutzer nur die Anmeldung mit ihren Geschäfts-, Schul- oder Unikonten von Microsoft zulassen*.

| Funktion | Access Control-Unterstützung | Azure AD-Unterstützung |
| ---------- | ----------- | ---------------- |
| **Kontotypen** | | |
| Geschäfts-, Schul- oder Unikonto von Microsoft | Unterstützt | Unterstützt |
| Konten von Windows Server Active Directory und AD FS |- Unterstützt über den Verbund mit einem Azure AD-Mandanten <br />- Unterstützt über den direkten Verbund mit AD FS | Nur unterstützt über den Verbund mit einem Azure AD-Mandanten | 
| Konten aus anderen Identitätsverwaltungssystemen für Unternehmen |- Möglich über den Verbund mit einem Azure AD-Mandanten <br />- Unterstützt über den direkten Verbund | Möglich über den Verbund mit einem Azure AD-Mandanten |
| Microsoft-Konten für den persönlichen Gebrauch | Unterstützt | Unterstützt über das v2.0 OAuth-Protokoll von Azure AD, aber nicht über andere Protokolle | 
| Facebook-, Google-, Yahoo-Konten | Unterstützt | Überhaupt nicht unterstützt |
| **Protokolle und SDK-Kompatibilität** | | |
| WIF | Unterstützt | Unterstützt, jedoch nur begrenzte Anweisungen verfügbar |
| WS-Federation- | Unterstützt | Unterstützt |
| OAuth 2.0 | Unterstützung für Entwurf 13 | Unterstützung für RFC 6749, die modernste Spezifikation |
| WS-Trust | Unterstützt | Nicht unterstützt |
| **Tokenformate** | | |
| JWT | Unterstützt in der Betaversion | Unterstützt |
| SAML 1.1 | Unterstützt | Vorschau |
| SAML 2.0 | Unterstützt | Unterstützt |
| SWT | Unterstützt | Nicht unterstützt |
| **Anpassungen** | | |
| Anpassbare Benutzeroberfläche für Startbereichsermittlung/Kontoauswahl | Code zum Herunterladen, der in Apps integriert werden kann | Nicht unterstützt |
| Hochladen von benutzerdefinierten Tokensignaturzertifikaten | Unterstützt | Unterstützt |
| Anpassen von Ansprüchen in Token |- Übergeben von Eingabeansprüchen von Identitätsanbietern<br />- Abrufen des Zugriffstokens von Identitätsanbietern als Anspruch<br />- Ausstellen von Ausgabeansprüchen basierend auf den Werten der Eingabeansprüche<br />- Ausstellen von Ausgabeansprüchen mit konstanten Werten |- Übergeben von Ansprüchen von Verbundidentitätsanbietern nicht möglich<br />- Abrufen des Zugriffstokens von Identitätsanbietern als Anspruch nicht möglich<br />- Ausstellen von Ausgabeansprüchen basierend auf den Werten der Eingabeansprüche nicht möglich<br />- Ausstellen von Ausgabeansprüchen mit konstanten Werten<br />- Ausstellen von Ausgabeansprüchen basierend auf den Eigenschaften von mit Azure AD synchronisierten Benutzern |
| **Automation** | | |
| Automatisieren von Konfigurations- und Verwaltungsaufgaben | Unterstützt über den Access Control-Verwaltungsdienst | Unterstützt über die Microsoft Graph-API |

Wenn Sie zu dem Urteil kommen, dass Azure AD den besten Migrationspfad für Ihre Anwendungen und Dienste darstellt, sollten Sie die zwei Möglichkeiten für die Integration Ihrer App in Azure AD beachten.

Um WS-Verbund oder WIF für die Integration mit Azure AD zu verwenden, empfehlen wir die in [Konfigurieren des einmaligen Anmeldens im Verbund für eine nicht im Katalog enthaltene Anwendung](../manage-apps/configure-saml-single-sign-on.md) beschriebene Vorgehensweise. Der Artikel bezieht sich auf die Konfiguration von Azure AD für SAML-basiertes einmaliges Anmelden, betrifft aber ebenfalls das Konfigurieren des WS-Verbunds. Für diesen Ansatz ist eine Azure AD Premium-Lizenz erforderlich. Dieser Ansatz bietet zwei Vorteile:

- Sie profitieren von der vollständigen Flexibilität der Azure AD-Tokenanpassung. Sie können die von Azure AD ausgestellten Ansprüche so anpassen, dass sie mit den von Access Control ausgestellten Ansprüchen übereinstimmen. Das betrifft insbesondere den Anspruch für Benutzer-ID oder Namensbezeichner. Um nach der Technologieumstellung weiterhin konsistente Benutzer-ID-Bezeichner für Ihre Benutzer zu empfangen, müssen Sie sicherstellen, dass die von Azure AD ausgestellten IDs mit den von Access Control ausgestellten IDs übereinstimmen.
- Sie können ein für Ihre Anwendung spezifisches Tokensignaturzertifikat konfigurieren, dessen Gültigkeitsdauer Sie steuern können.

> [!NOTE]
> Für diesen Ansatz ist eine Azure AD Premium-Lizenz erforderlich. Wenn Sie ein Access Control-Kunde sind und eine Premium-Lizenz für die Einrichtung von einmaligem Anmelden für eine Anwendung benötigen, setzen Sie sich mit uns in Verbindung. Wir stellen Ihnen für diesen Zweck gerne Entwicklerlizenzen zur Verfügung.

Eine andere Möglichkeit ist die Umsetzung [dieses Codebeispiels](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), aus dem sich etwas abweichende Anweisungen zum Einrichten des WS-Verbunds ergeben. Dieses Codebeispiel verwendet nicht WIF, sondern stattdessen ASP.NET 4.5-OWIN-Middleware. Die Anweisungen für die App-Registrierung gelten jedoch auch für Apps, die WIF nutzen, und es ist keine Azure AD Premium-Lizenz erforderlich. 

Wenn Sie sich für diesen Ansatz entscheiden, müssen Sie mit dem [Rollover von Signaturschlüsseln in Azure AD](../develop/active-directory-signing-key-rollover.md) vertraut sein. Dieser Ansatz verwendet den globalen Azure AD-Signaturschlüssel zum Ausstellen von Token. Standardmäßig aktualisiert WIF Signaturschlüssel nicht automatisch. Wenn Azure AD durch die globalen Signaturschlüssel wechselt, muss Ihre WIF-Implementierung entsprechend vorbereitet sein und die Änderungen akzeptieren. Weitere Informationen finden Sie unter [Wichtige Informationen zum Rollover von Signaturschlüsseln in Azure Active Directory](/previous-versions/azure/dn641920(v=azure.100)).

Wenn die Integration in Azure AD über das OpenID Connect- oder das OAuth-Protokoll möglich ist, wird diese Vorgehensweise empfohlen. Unter [Azure Active Directory für Entwickler](../develop/index.yml) stehen ausführliche Dokumentationen und Anweisungen für die Integration von Azure AD in Ihre Webanwendung zur Verfügung.

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrieren zu Azure Active Directory B2C

Sie können als Migrationspfad auch Azure AD B2C in Erwägung ziehen. Azure AD B2C ist ein Cloudauthentifizierungsdienst, der – ähnlich wie Access Control – Benutzern die Auslagerung der Authentifizierungs- und Identitätsverwaltungslogik in einen Clouddienst ermöglicht. Es handelt sich dabei um einen kostenpflichtigen Dienst (mit Free- und Premium-Tarif) für auf Endbenutzer ausgerichtete Anwendungen, die Millionen aktiver Benutzer haben können.

Wie bei Access Control besteht eines der attraktivsten Features von Azure AD B2C darin, dass viele verschiedene Kontotypen unterstützt werden. Mit Azure AD B2C können Sie Benutzer mithilfe ihres Microsoft-Kontos oder u.a. ihrer Facebook-, Google-, LinkedIn-, GitHub- oder Yahoo-Konten anmelden. Azure AD B2C unterstützt darüber hinaus „lokale Konten“ bzw. Benutzername und Kennwort, die Benutzer speziell für Ihre Anwendung erstellen. Außerdem bietet Azure AD B2C umfassende Erweiterbarkeit, die das Anpassen von Anmeldeabläufen ermöglicht. 

Allerdings unterstützt Azure AD B2C nicht die gesamte Breite der Authentifizierungsprotokolle und Tokenformate, die möglicherweise von Access Control-Kunden benötigt werden. Ferner kann Azure AD B2C nicht zum Abrufen von Token und zum Abfragen zusätzlicher Informationen über den Benutzer beim Identitätsanbieter, bei Microsoft oder bei anderen Anbietern verwendet werden.

Die folgende Tabelle vergleicht die für Webanwendungen relevanten Funktionen von Access Control mit den in Azure AD B2C verfügbaren Funktionen. Im Allgemeinen ist *Azure AD B2C wahrscheinlich die richtige Wahl für Ihre Migration, wenn Ihre Anwendung auf Endbenutzer ausgerichtet ist oder viele verschiedene Kontotypen unterstützt*.

| Funktion | Access Control-Unterstützung | Azure AD B2C-Unterstützung |
| ---------- | ----------- | ---------------- |
| **Kontotypen** | | |
| Geschäfts-, Schul- oder Unikonto von Microsoft | Unterstützt | Unterstützt über benutzerdefinierte Richtlinien  |
| Konten von Windows Server Active Directory und AD FS | Unterstützt über den direkten Verbund mit AD FS | Unterstützt über SAML-Verbund mithilfe benutzerdefinierter Richtlinien |
| Konten aus anderen Identitätsverwaltungssystemen für Unternehmen | Unterstützt über den direkten Verbund durch den WS-Verbund | Unterstützt über SAML-Verbund mithilfe benutzerdefinierter Richtlinien |
| Microsoft-Konten für den persönlichen Gebrauch | Unterstützt | Unterstützt | 
| Facebook-, Google-, Yahoo-Konten | Unterstützt | Facebook und Google nativ unterstützt, Yahoo über OpenID Connect-Verbund mithilfe benutzerdefinierter Richtlinien unterstützt |
| **Protokolle und SDK-Kompatibilität** | | |
| Windows Identity Foundation (WIF) | Unterstützt | Nicht unterstützt |
| WS-Federation- | Unterstützt | Nicht unterstützt |
| OAuth 2.0 | Unterstützung für Entwurf 13 | Unterstützung für RFC 6749, die modernste Spezifikation |
| WS-Trust | Unterstützt | Nicht unterstützt |
| **Tokenformate** | | |
| JWT | Unterstützt in der Betaversion | Unterstützt |
| SAML 1.1 | Unterstützt | Nicht unterstützt |
| SAML 2.0 | Unterstützt | Nicht unterstützt |
| SWT | Unterstützt | Nicht unterstützt |
| **Anpassungen** | | |
| Anpassbare Benutzeroberfläche für Startbereichsermittlung/Kontoauswahl | Code zum Herunterladen, der in Apps integriert werden kann | Vollständig anpassbare Benutzeroberfläche über benutzerdefiniertes CSS |
| Hochladen von benutzerdefinierten Tokensignaturzertifikaten | Unterstützt | Benutzerdefinierte Signaturschlüssel, keine Zertifikate, unterstützt über benutzerdefinierte Richtlinien |
| Anpassen von Ansprüchen in Token |- Übergeben von Eingabeansprüchen von Identitätsanbietern<br />- Abrufen des Zugriffstokens von Identitätsanbietern als Anspruch<br />- Ausstellen von Ausgabeansprüchen basierend auf den Werten der Eingabeansprüche<br />- Ausstellen von Ausgabeansprüchen mit konstanten Werten |- Übergabe von Ansprüchen von Identitätsanbietern möglich; für einige Ansprüche sind benutzerdefinierte Richtlinien erforderlich<br />- Abrufen des Zugriffstokens von Identitätsanbietern als Anspruch nicht möglich<br />- Ausstellen von Ausgabeansprüchen basierend auf den Werten der Eingabeansprüche über benutzerdefinierte Richtlinien<br />- Ausstellen von Ausgabeansprüchen mit konstanten Werten über benutzerdefinierte Richtlinien |
| **Automation** | | |
| Automatisieren von Konfigurations- und Verwaltungsaufgaben | Unterstützt über den Access Control-Verwaltungsdienst |- Erstellen von Benutzern mit der Microsoft Graph-API zulässig<br />- B2C-Mandanten, -Anwendungen oder -Richtlinien können nicht programmgesteuert erstellt werden. |

Wenn Sie zu dem Urteil gelangen, dass Azure AD B2C den besten Migrationspfad für Ihre Anwendungen und Dienste darstellt, lesen Sie zunächst die Informationen in den folgenden Ressourcen:

- [Azure AD B2C-Dokumentation](../../active-directory-b2c/overview.md)
- [Azure Active Directory B2C: Benutzerdefinierte Richtlinien](../../active-directory-b2c/custom-policy-overview.md)
- [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrieren zu Ping Identity oder Auth0

In einigen Fällen stellt sich möglicherweise heraus, dass Azure AD und Azure AD B2C nicht ausreichen, um Access Control ohne tiefgreifende Codeänderungen in Ihren Webanwendungen zu ersetzen. Hier einige typische Beispiele:

- Webanwendungen, die WIF oder WS-Verbund für die Anmeldung bei sozialen Netzwerken als Identitätsanbieter verwenden, z. B. Google oder Facebook.
- Webanwendungen, die mithilfe des WS-Verbundprotokolls einen direkten Verbund mit einem Unternehmensidentitätsanbieter betreiben.
- Webanwendungen, die das von einem sozialen Netzwerk (wie Google oder Facebook) als Identitätsanbieter ausgestellte Token als Anspruch in den von Access Control ausgestellten Token benötigen.
- Webanwendungen mit komplexen Tokentransformationsregeln, die von Azure AD oder Azure AD B2C nicht reproduziert werden können.
- Webanwendungen mit mehreren Mandanten, die ACS für die zentrale Verwaltung des Verbunds mit mehreren verschiedenen Identitätsanbietern verwenden

In diesen Fällen kann es sinnvoll sein, die Webanwendung zu einem anderen Cloudauthentifizierungsdienst zu migrieren. Wir empfehlen, die folgenden Optionen zu untersuchen. Jede der folgenden Optionen bietet Funktionen, die denen von Access Control ähnlich sind:

![Diese Abbildung zeigt das Auth0-Logo](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) ist ein flexibler Cloudidentitätsdienst, der eine [allgemeine Migrationsanleitung für Access Control-Kunden](https://auth0.com/acs) erstellt hat und seinerseits nahezu alle von ACS unterstützten Funktionen unterstützt.

![Diese Abbildung zeigt das Ping Identity-Logo](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity](https://www.pingidentity.com) bietet zwei Lösungen, die ACS ähnlich sind. PingOne ist ein Cloudidentitätsdienst, der viele der Funktionen von ACS unterstützt, und PingFederate ist ein ähnliches lokales Identitätsprodukt, das höhere Flexibilität bietet. Weitere Details zur Verwendung dieser Produkte finden Sie in [Ping's ACS retirement guidance](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) (Ping-Leitfaden zur Einstellung von ACS).

Unser Ziel bei der Arbeit mit Ping Identity und Auth0 besteht darin, sicherzustellen, dass für alle Access Control-Kunden ein Migrationspfad für ihre Apps und Dienste besteht, der den erforderlichen Arbeitsaufwand bei der Umstellung von Access Control minimiert.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webdienste, die aktive Authentifizierung verwenden

Für Webdienste, die mithilfe von Token geschützt sind, die von Access Control ausgestellt werden, bietet Access Control die folgenden Features und Funktionen:

- Möglichkeit zum Registrieren von *Dienstidentitäten* in Ihrem Access Control-Namespace. Möglichkeit zur Verwendung von Dienstidentitäten zum Tokenabruf.
- Unterstützung für die Protokolle OAuth WRAP und OAuth 2.0 (Entwurf 13) zum Tokenabruf durch Verwendung der folgenden Anmeldeinformationstypen:
    - Ein einfaches, für die Dienstidentität erstelltes Kennwort
    - Ein mithilfe eines symmetrischen Schlüssels oder eines X509-Zertifikats signiertes SWT
    - Ein von einem vertrauenswürdigen Identitätsanbieter (normalerweise einer AD FS-Instanz) ausgestelltes SAML-Token
- Unterstützung der folgenden Tokenformate: JWT, SAML 1.1, SAML 2.0 und SWT.
- Einfache Tokentransformationsregeln

Dienstidentitäten in Access Control werden in der Regel zum Implementieren von Server-zu-Server-Authentifizierung verwendet. 

#### <a name="migrate-to-azure-active-directory"></a>Migrieren zu Azure Active Directory

Für diese Art der Authentifizierung wird die Migration zu [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) empfohlen. Azure AD ist der cloudbasierte Identitätsanbieter für Geschäfts-, Schul- oder Unikonten von Microsoft. Azure AD ist der Identitätsanbieter für Microsoft 365, Azure und vieles mehr. 

Azure AD kann jedoch mithilfe der Azure AD-Implementierung der Erteilung der OAuth-Clientanmeldeinformationen auch für die Server-zu-Server-Authentifizierung verwendet werden. Die folgende Tabelle vergleicht die Funktionen von Access Control bei der Server-zu-Server-Authentifizierung mit den in Azure AD verfügbaren Features:

| Funktion | Access Control-Unterstützung | Azure AD-Unterstützung |
| ---------- | ----------- | ---------------- |
| Registrieren eines Webdiensts | Erstellen einer vertrauenden Seite im Access Control-Verwaltungsportal | Erstellen einer Azure AD-Webanwendung im Azure-Portal |
| Registrieren eines Clients | Erstellen einer Dienstidentität im Access Control-Verwaltungsportal | Erstellen einer weiteren Azure AD-Webanwendung im Azure-Portal |
| Verwendetes Protokoll |- OAuth WRAP-Protokoll<br />- Erteilen von OAuth 2.0 (Entwurf 13)-Clientanmeldeinformationen | Gewähren von OAuth 2.0-Clientanmeldeinformationen |
| Clientauthentifizierungsmethoden |- Einfaches Kennwort<br />- Signiertes SWT<br />- SAML-Token von einem Verbundidentitätsanbieter |- Einfaches Kennwort<br />- Signiertes JWT |
| Tokenformate |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Nur JWT |
| Tokentransformation |- Hinzufügen von benutzerdefinierten Ansprüchen<br />- Einfache If-Then-Logik für die Anspruchsausstellung | Hinzufügen von benutzerdefinierten Ansprüchen | 
| Automatisieren von Konfigurations- und Verwaltungsaufgaben | Unterstützt über den Access Control-Verwaltungsdienst | Unterstützt über die Microsoft Graph-API |

Anleitungen zum Implementieren von Server-zu-Server-Szenarien finden Sie in den folgenden Ressourcen:

- Dienst-zu-Dienst-Abschnitt in [Azure Active Directory für Entwickler](../develop/index.yml)
- [Daemon code sample by using simple password client credentials](https://github.com/Azure-Samples/active-directory-dotnet-daemon) (Daemon-Codebeispiel mit Clientanmeldeinformationen mit einfachem Kennwort)
- [Daemon code sample by using certificate client credentials](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) (Daemon-Codebeispiel mit Clientanmeldeinformationen mit Zertifikat)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrieren zu Ping Identity oder Auth0

Manchmal kann sich herausstellen, dass die Implementierung von Azure AD-Clientanmeldeinformationen und die OAuth-Erteilung nicht ausreichen, um Access Control in Ihrer Architektur ohne größere Codeänderungen zu ersetzen. Hier einige typische Beispiele:

- Server-zu-Server-Authentifizierung mithilfe anderer Codeformate als JWTs.
- Server-zu-Server-Authentifizierung mithilfe eines von einem externen Identitätsanbieter bereitgestellten Eingabetokens.
- Server-zu-Server-Authentifizierung mit Tokentransformationsregeln, die von Azure AD nicht reproduziert werden können.

In diesen Fällen kann es sinnvoll sein, die Webanwendung zu einem anderen Cloudauthentifizierungsdienst zu migrieren. Wir empfehlen, die folgenden Optionen zu untersuchen. Jede der folgenden Optionen bietet Funktionen, die denen von Access Control ähnlich sind:

![Diese Abbildung zeigt das Auth0-Logo](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) ist ein flexibler Cloudidentitätsdienst, der eine [allgemeine Migrationsanleitung für Access Control-Kunden](https://auth0.com/acs) erstellt hat und seinerseits nahezu alle von ACS unterstützten Funktionen unterstützt.

![Abbildung mit dem Ping Identity-Logo](./media/active-directory-acs-migration/rsz-ping.png)
[Ping Identity](https://www.pingidentity.com) bietet zwei Lösungen, die mit ACS vergleichbar sind. PingOne ist ein Cloudidentitätsdienst, der viele der Funktionen von ACS unterstützt, und PingFederate ist ein ähnliches lokales Identitätsprodukt, das höhere Flexibilität bietet. Weitere Details zur Verwendung dieser Produkte finden Sie in [Ping's ACS retirement guidance](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) (Ping-Leitfaden zur Einstellung von ACS).

Unser Ziel bei der Arbeit mit Ping Identity und Auth0 besteht darin, sicherzustellen, dass für alle Access Control-Kunden ein Migrationspfad für ihre Apps und Dienste besteht, der den erforderlichen Arbeitsaufwand bei der Umstellung von Access Control minimiert.

#### <a name="passthrough-authentication"></a>Pass-Through-Authentifizierung

Bei der Pass-Through-Authentifizierung mit willkürlicher Tokentransformation gibt es keine entsprechende Microsoft-Technologie zu ACS. Wenn Ihre Kunden diese Funktion benötigen, stellt Auth0 die am nächsten kommende Lösung dar.

## <a name="questions-concerns-and-feedback"></a>Fragen, Bedenken und Feedback

Es ist uns bewusst, dass viele Access Control-Kunden nach der Lektüre dieses Artikels keinen eindeutigen Migrationspfad bestimmen können. Möglicherweise benötigen Sie ein gewisses Maß an Unterstützung oder Anleitung im Bestimmen des richtigen Plans. Wenn Sie Ihre Migrationsszenarien und Fragen besprechen möchten, hinterlassen Sie bitte einen Kommentar auf dieser Seite.