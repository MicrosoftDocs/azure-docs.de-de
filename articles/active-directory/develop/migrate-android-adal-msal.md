---
title: Leitfaden für die Migration von ADAL zu MSAL für Android | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Ihre Android-App mit ADAL (Active Directory Authentication Library) zu MSAL (Microsoft Authentication Library) migrieren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: ba639bc023affc7c2e6b2b675cdedc1229636893
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581039"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Leitfaden für die Migration von ADAL zu MSAL für Android

In diesem Artikel werden die Änderungen beschrieben, die Sie durchführen müssen, um eine App, die ADAL (Azure Active Directory Authentification Library) verwendet, zu MSAL (Microsoft Authentication Library) zu migrieren.

## <a name="difference-highlights"></a>Wichtige Unterschiede

ADAL arbeitet mit dem Azure Active Directory v1.0-Endpunkt. MSAL (Microsoft Authentication Library) arbeitet mit Microsoft Identity Platform (ehemals bekannt als Azure Active Directory v2.0-Endpunkt). Microsoft Identity Platform weist einige Unterschiede im Vergleich zu Azure Active Directory v1.0 auf:

Unterstützt:
  - Organisationsbezogene Identitäten (Azure Active Directory)
  - Nicht organisationsbezogene Identitäten wie Outlook.com, Xbox Live usw.
  - (Nur Azure AD B2C) Verbundanmeldung mit Google, Facebook, Twitter und Amazon

- Kompatible Standards:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Mit der öffentlichen MSAL-API werden wichtige Änderungen eingeführt einschließlich:

- Einem neuen Modell für Tokenzugriffe:
  - ADAL ermöglicht den Zugriff auf Token über `AuthenticationContext`, was den Server darstellt. MSAL ermöglicht den Zugriff auf Token über `PublicClientApplication`, was den Client darstellt. Entwickler von Clients brauchen nicht für jede autoritative Stelle, mit der Sie interagieren müssen, eine neue `PublicClientApplication`-Instanz zu erstellen. Es ist nur eine `PublicClientApplication`-Konfiguration erforderlich.
  - Unterstützung der Anforderung von Zugriffstoken mithilfe von Bereichen (zusätzlich zu Ressourcenbezeichnern).
  - Unterstützung der inkrementellen Zustimmung. Entwickler können Bereiche anfordern, während Benutzer auf mehr Funktionen in der App zugreifen, einschließlich solcher, die während der App-Registrierung nicht enthalten sind.
  - Autoritative Stellen werden nicht mehr zur Runtime überprüft. Stattdessen deklariert der Entwickler bei der Entwicklung eine Liste mit „bekannten autoritativen Stellen“.
- API-Änderungen für Token:
  - In ADAL führt `AcquireToken()` zuerst eine automatische Anforderung durch. Wenn dabei ein Fehler auftritt, wird eine interaktive Anforderung durchgeführt. Dieses Verhalten hat dazu geführt, dass einige Entwickler sich nur auf `AcquireToken` verlassen haben, sodass der Benutzer manchmal unerwartet zur Eingabe von Anmeldeinformationen aufgefordert wurde. Bei MSAL müssen sich Entwickler bewusst entscheiden, wann dem Benutzer eine Eingabeaufforderung auf der Benutzeroberfläche angezeigt wird.
    - `AcquireTokenSilent` führt immer zu einer automatischen Anforderung, die entweder erfolgreich ist oder fehlschlägt.
    - `AcquireToken` führt immer zu einer Anforderung, die der Benutzer über die Benutzeroberfläche erhält.
- MSAL unterstützt die Anmeldung entweder über einen Standardbrowser oder eine eingebettete Webansicht:
  - Standardmäßig wird der Standardbrowser des Geräts verwendet. Dies ermöglicht MSAL die Verwendung des Authentifizierungsstatus (über Cookies), der möglicherweise schon für ein oder mehrere angemeldete Konten vorhanden ist. Wenn kein Authentifizierungsstatus vorhanden ist, wird bei der Autorisierung über MSAL durch die Authentifizierung ein Authentifizierungsstatus (Cookie) erstellt, von dem auch andere Webanwendungen profitieren, die den gleichen Browser verwenden.
- Neues Ausnahmemodell:
  - Ausnahmen definieren klarer den Typ des aufgetretenen Fehlers und die Maßnahmen, die der Entwickler zum Beheben der Ausnahme ausführen muss.
- MSAL unterstützt Parameterobjekte für `AcquireToken`- und `AcquireTokenSilent`-Aufrufe.
- MSAL unterstützt die deklarative Konfiguration für:
  - Client-ID, Umleitungs-URI.
  - Eingebettete Ansicht und Standardbrowser
  - Autoritative Stellen
  - HTTP-Einstellungen wie Lesen und Verbindungstimeout

## <a name="your-app-registration-and-migration-to-msal"></a>App-Registrierung und Migration zu MSAL

Für die Verwendung von MSAL müssen Sie Ihre vorhandene App-Registrierung nicht ändern. Wenn Sie die inkrementelle/progressive Zustimmung nutzen möchten, müssen Sie ggf. die Registrierung überprüfen und die speziellen Bereiche definieren, die inkrementell angefordert werden sollen. Weitere Informationen zu Bereichen und inkrementeller Zustimmung folgen.

Bei der App-Registrierung im Portal wird die Registerkarte **API-Berechtigungen** angezeigt. Diese enthält eine Liste der APIs und Berechtigungen (Bereiche), für die Ihre App aufgrund ihrer aktuellen Konfiguration Zugriff anfordern kann. Sie enthält auch eine Liste der Bereichsnamen, die jeder API-Berechtigung zugeordnet sind.

### <a name="user-consent"></a>Benutzerzustimmung

Mit ADAL und dem Azure AD v1-Endpunkt wurde die Benutzereinwilligung für eigene Ressourcen bei der erstmaligen Verwendung erteilt. Mit MSAL und Microsoft Identity Platform kann die Zustimmung inkrementell angefordert werden. Die inkrementelle Zustimmung ist hilfreich bei Berechtigungen, die der Benutzer eventuell als hohe Berechtigungen betrachtet. Andererseits kann der Benutzer auch nachfragen, wenn er keine eindeutige Erklärung erhält, warum die Berechtigung erforderlich ist. Bei ADAL haben diese Berechtigungen möglicherweise dazu geführt, dass die Benutzer auf die Anmeldung bei Ihrer App verzichtet haben.

> [!TIP]
> Verwenden Sie die inkrementelle Zustimmung, um Ihre Benutzer über zusätzlichen Kontext darüber zu informieren, warum Ihre App eine Berechtigung benötigt.

### <a name="admin-consent"></a>Administratorzustimmung

Organisationsadministratoren können den Berechtigungen, die Ihre Anwendung erfordert, im Namen aller Mitglieder ihrer Organisation zustimmen. Einige Organisationen erlauben nur Administratoren die Zustimmung zu Anwendungen. Die Administratorzustimmung setzt voraus, dass Sie alle API-Berechtigungen und Bereiche, die von Ihrer Anwendung verwendet werden, in die App-Registrierung einbeziehen.

> [!TIP]
> Auch wenn Sie mit MSAL einen Bereich für ein Element anfordern können, das nicht in Ihrer App-Registrierung enthalten ist, empfiehlt es sich, die App-Registrierung zu aktualisieren und alle Ressourcen und Bereiche einzubeziehen, denen ein Benutzer jemals eine Berechtigung erteilen kann.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrieren von Ressourcen-IDs zu Bereichen

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Authentifizieren und Anfordern der Autorisierung für alle Berechtigungen bei erstmaliger Verwendung

Wenn Sie aktuell ADAL verwenden und keine inkrementelle Zustimmung verwenden müssen, besteht die einfachste Möglichkeit zur Verwendung von MSAL darin, eine `acquireToken`-Anforderung mit dem neuen `AcquireTokenParameter`-Objekt zu erstellen und den Ressourcen-ID-Wert festzulegen.

> [!CAUTION]
> Es ist nicht möglich, sowohl Bereiche als auch eine Ressourcen-ID festzulegen. Der Versuch, beides festzulegen, führt zu der Ausnahme `IllegalArgumentException`.

Dies führt zu dem v1-Verhalten, das Ihnen bereits bekannt ist. Alle bei Ihrer App-Registrierung angeforderten Berechtigungen werden bei der ersten Interaktion vom Benutzer angefordert.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Authentifizieren und Anfordern von Berechtigungen nur bei Bedarf

Um die inkrementelle Zustimmung zu nutzen, erstellen Sie eine Liste der Berechtigungen (Bereiche), die Ihre App bei der App-Registrierung verwendet, und organisieren sie in zwei Listen, die auf folgenden Kriterien beruhen:

- Die Bereiche, die während der ersten Interaktion des Benutzers mit Ihrer App während der Anmeldung angefordert werden sollen.
- Die Berechtigungen, die mit einem wichtigen Feature der App verknüpft sind, und die Sie dem Benutzer auch erläutern müssen.

Nachdem Sie die Bereiche organisiert haben, organisieren Sie jede Liste nach der Ressource (API), für die Sie ein Token anfordern möchten. Ebenso müssen Sie alle anderen Bereiche angeben, für die sich der Benutzer gleichzeitig autorisieren soll.

Das Parameterobjekt, das für die MSAL-Anforderung verwendet wird, unterstützt Folgendes:

- `Scope`: Die Liste der Bereiche, für die Sie die Autorisierung anfordern und ein Zugriffstoken erhalten möchten.
- `ExtraScopesToConsent`: Eine zusätzliche Liste der Bereiche, für die Sie die Autorisierung anfordern möchten, während Sie ein Zugriffstoken für eine andere Ressource anfordern. Diese Liste von Bereichen ermöglicht es Ihnen, die Anzahl der Anforderungen zu minimieren, die Sie für die Benutzerautorisierung benötigen. Dies bedeutet weniger Eingabeaufforderungen zur Benutzerautorisierung oder -zustimmung.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrieren von AuthenticationContext zu PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Erstellen des PublicClientApplication-Objekts

Bei Verwendung von MSAL wird das Objekt `PublicClientApplication` instanziiert. Dieses Objekt modelliert Ihre App-Identität und wird verwendet, um Anforderungen an eine oder mehrere autoritative Stellen zu senden. Mit diesem Objekt konfigurieren Sie die Clientidentität, den Umleitungs-URI, die autoritative Standardstelle, die Verwendung von Gerätebrowser oder eingebetteter Webansicht, die Protokollebene usw.

Sie können dieses Objekt deklarativ mit JSON konfigurieren und entweder als Datei bereitstellen oder als Ressource in Ihrem Anwendungspaket speichern.

Obwohl es sich bei diesem Objekt nicht um einen Singleton handelt, verwendet es intern gemeinsame `Executors` für interaktive und automatische Anforderungen.

### <a name="business-to-business"></a>Business-to-Business

In ADAL muss jede Organisation, von der Sie Zugriffstoken anfordern, über eine separate `AuthenticationContext`-Instanz verfügen. In MSAL ist dies nicht mehr erforderlich. Sie können im Rahmen Ihrer automatischen oder interaktiven Anforderung die autoritative Stelle angeben, von der Sie ein Token anfordern möchten.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migration von der Autoritätsüberprüfung zu bekannten autoritativen Stellen

MSAL weist kein Flag zum Aktivieren oder Deaktivieren der Autoritätsüberprüfung auf. Die Autoritätsüberprüfung ist ein Feature in ADAL und in den frühen MSAL-Versionen, das verhindert, dass Ihr Code Token von einer potenziell böswilligen autoritativen Stelle anfordert. MSAL ruft jetzt eine Liste der Microsoft bekannten autoritativen Stellen ab und kombiniert diese Liste mit den autoritativen Stellen, die Sie in Ihrer Konfiguration angegeben haben.

> [!TIP]
> Wenn Sie ein Azure Business-to-Consumer (B2C)-Benutzer sind, müssen Sie die Autoritätsüberprüfung nicht mehr deaktivieren. Fügen Sie stattdessen jede der unterstützten Azure AD B2C-Richtlinien als autoritative Stellen in Ihre MSAL-Konfiguration ein.

Wenn Sie versuchen, eine autoritative Stelle zu verwenden, die Microsoft nicht bekannt und auch nicht in Ihrer Konfiguration enthalten ist, wird die Ausnahme `UnknownAuthorityException` ausgegeben.

### <a name="logging"></a>Protokollierung
Sie können die Protokollierung jetzt folgendermaßen deklarativ als Bestandteil ihrer Konfiguration konfigurieren:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migrieren von Benutzerinfo (UserInfo-Objekt) zu Konto

In ADAL stellt `AuthenticationResult` ein `UserInfo`-Objekt bereit, das zum Abrufen von Informationen zum authentifizierten Konto verwendet wird. Durch die Verwendung des Begriffs „User“ (Benutzer), der eine Person oder einen Software-Agent bezeichnete, konnte nur schwer vermittelt werden, dass einige Apps einzelne Benutzer (Person oder Software-Agent) unterstützen, die über mehrere Konten verfügen.

Denken Sie an ein Bankkonto. Sie haben möglicherweise mehr als ein Konto bei mehr als einem Finanzinstitut. Wenn Sie ein Konto eröffnen, werden Ihnen (dem Benutzer) die Anmeldeinformationen (z.B. Bankkarte und PIN) ausgehändigt, die für den Zugriff auf Kontostand, Überweisungen usw. für das jeweilige Konto verwendet wird. Diese Anmeldeinformationen können nur bei dem Finanzinstitut verwendet werden, das sie ausgestellt hat.

Wie bei Konten in einem Finanzinstitut erfolgt analog auch der Zugriff auf Konten in Microsoft Identity Platform mithilfe von Anmeldeinformationen. Diese Anmeldeinformationen werden entweder bei Microsoft registriert oder von Microsoft (oder von Microsoft im Auftrag einer Organisation) ausgestellt.

Microsoft Identity Platform unterscheidet sich bei diesem Vergleich jedoch in einem Punkt von einem Finanzinstitut: Microsoft Identity Platform stellt ein Framework bereit, mit dem ein Benutzer ein Konto und die zugehörigen Anmeldeinformationen für den Zugriff auf Ressourcen verwenden kann, die mehreren Einzelpersonen und Organisationen gehören. Das entspricht in etwa der Möglichkeit, die von einer Bank ausgestellte Bankkarte bei einem anderen Finanzinstitut zu verwenden. Dies funktioniert, da alle fraglichen Organisationen Microsoft Identity Platform verwenden, die die organisationsübergreifende Verwendung eines Kontos ermöglicht. Hier sehen Sie ein Beispiel:

Sam arbeitet für Contoso.com, verwaltet aber virtuelle Azure-Computer, die zu Fabrikam.com gehören. Damit Sam die virtuellen Computer von Fabrikam verwalten kann, muss er für den Zugriff autorisiert sein. Dieser Zugriff kann gewährt werden, indem Sams Konto zu Fabrikam.com hinzugefügt und dem Konto eine Rolle zugewiesen wird, mit der Sam an den virtuellen Computern arbeiten kann. Dies würde im Azure-Portal erfolgen.

Durch das Hinzufügen von Sams Konto bei Contoso.com als Mitglied von Fabrikam.com würde ein neuer Datensatz in Azure Active Directory von Fabrikam.com für Sam erstellt. Der Datensatz von Sam in Azure Active Directory wird als Benutzerobjekt bezeichnet. In diesem Fall würde das Benutzerobjekt auf das Benutzerobjekt von Sam bei Contoso.com zurückverweisen. Das Fabrikam-Benutzerobjekt von Sam ist die lokale Darstellung von Sam und würde zum Speichern von Informationen zum Konto verwendet, das Sam im Zusammenhang mit Fabrikam.com zugeordnet ist. Bei Contoso.com hat Sam den Titel „Senior DevOps Consultant“. Sams Titel bei Fabrikam lautet „Contractor-Virtual Machines“. Bei Contoso.com ist Sam für die Verwaltung virtueller Computer weder verantwortlich noch autorisiert. Bei Fabrikam.com ist dies sein einziger Tätigkeitsbereich. Sam verfügt allerdings immer noch über nur einen Satz von Anmeldeinformationen, die nachverfolgt werden müssen, nämlich die Anmeldeinformationen, die von Contoso.com ausgestellt wurden.

Nach einem erfolgreichen `acquireToken`-Aufruf wird ein Verweis auf ein `IAccount`-Objekt angezeigt, das in späteren `acquireTokenSilent`-Anforderungen verwendet werden kann.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Wenn Sie eine App haben, die von jedem Mandanten, in dem das Konto dargestellt wird, auf die das Konto betreffende Ansprüche zugreift, können Sie `IAccount`-Objekte in `IMultiTenantAccount` umwandeln. Diese Schnittstelle stellt eine Zuordnung von `ITenantProfiles` (nach Mandanten-ID) dar, mit der Sie in den einzelnen Mandanten, von denen Sie ein Token angefordert haben, auf die Ansprüche zugreifen können, die zu dem Konto gehören (relativ zum aktuellen Konto).

Die Ansprüche im Stamm von `IAccount` und `IMultiTenantAccount` enthalten immer die Ansprüche vom Home-Mandanten. Wenn Sie noch keine Anforderung für ein Token im Home-Mandanten erstellt haben, ist diese Sammlung leer.

## <a name="other-changes"></a>Weitere Änderungen

### <a name="use-the-new-authenticationcallback"></a>Verwenden der neuen Schnittstelle „AuthenticationCallback“

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migrieren zu den neuen Ausnahmen

In ADAL gibt es den Ausnahmetyp `AuthenticationException`, der eine Methode zum Abrufen des `ADALError`-Enumerationswerts enthält.
In MSAL gibt es eine Hierarchie von Ausnahmen, und jede verfügt über einen eigenen Satz zugehöriger spezieller Fehlercodes.

| Ausnahme                                        | Beschreibung                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Wird ausgelöst, wenn mindestens ein Eingabeargument ungültig ist.                 |
| `MsalClientException`                            | Wird bei einem clientseitigen Fehler ausgelöst.                                 |
| `MsalDeclinedScopeException`                     | Wird ausgelöst, wenn mindestens ein angeforderter Bereich vom Server abgelehnt wurde. |
| `MsalException`                                  | Standardausnahme des Typs „Checked Exception“, die von MSAL auslöst wird.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Wird ausgelöst, wenn für die Ressource die MAMCA-Schutzrichtlinie aktiviert ist.         |
| `MsalServiceException`                           | Wird bei einem serverseitigen Fehler ausgelöst.                                 |
| `MsalUiRequiredException`                        | Wird ausgelöst, wenn das Token nicht automatisch aktualisiert werden kann.                    |
| `MsalUserCancelException`                        | Wird ausgelöst, wenn der Benutzer den Authentifizierungsablauf abgebrochen hat.                |

### <a name="adalerror-to-msalexception-translation"></a>Übersetzung von ADALError in MsalException

| Wenn Sie diese Fehler in ADAL abfangen...  | ...fangen Sie diese MSAL-Ausnahmen ab:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Kein entsprechender ADALError* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Kein entsprechender ADALError* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Kein entsprechender ADALError* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Kein entsprechender ADALError* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>ADAL-Protokollierung zu MSAL-Protokollierung

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
