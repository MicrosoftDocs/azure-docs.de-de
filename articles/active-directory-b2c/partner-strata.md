---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Strata
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung mit WhoIAM für die Benutzerüberprüfung integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: bddc4c64feb31f78bed482bbd729ab1c4b8e676e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171414"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Tutorial: Erweitern von Azure AD B2C zum Schützen lokaler Anwendungen mithilfe von Strata

In diesem Beispieltutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD B2C) in [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) von Strata integrieren.
Maverics Identity Orchestrator erweitert Azure AD B2C zum Schützen lokaler Anwendungen. Maverics Identity Orchestrator stellt eine Verbindung mit jedem beliebigen Identitätssystem her, migriert Benutzer und Anmeldeinformationen transparent, synchronisiert Richtlinien und Konfigurationen und abstrahiert die Authentifizierung und Sitzungsverwaltung. Mithilfe von Strata können Unternehmen schnell von Legacyanwendungen auf Azure AD B2C umsteigen, ohne Anwendungen neu schreiben zu müssen. Diese Lösung hat die folgenden Vorteile:

- **Einmaliges Anmelden (Single Sign-On, SSO) von Kunden bei lokalen Hybrid-Apps**: Azure AD B2C unterstützt das einmalige Anmelden von Kunden mit Maverics Identity Orchestrator. Benutzer melden sich mit ihren Konten an, die in Azure AD B2C oder einem sozialen Netzwerk als Identitätsanbieter (Identity Provider, IdP) gehostet werden. Maverics erweitert SSO auf Apps, die in der Vergangenheit durch Legacyidentitätssysteme wie Symantec SiteMinder geschützt wurden.

- **Erweitern des auf Standards basierenden einmaligen Anmeldens auf Apps, ohne sie neu schreiben zu müssen**: Verwenden Sie Azure AD B2C, um den Benutzerzugriff zu verwalten und SSO mit Maverics Identity Orchestrator-SAML- oder -OIDC-Connectors zu ermöglichen.

- **Einfache Konfiguration**: Azure AD B2C bietet eine einfache schrittweise aufgebaute Benutzeroberfläche zum Verbinden von Maverics Identity Orchestrator-SAML- oder -OIDC-Connectors mit Azure AD B2C.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Eine Instanz von [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zum Speichern von Geheimnissen, die von Maverics Identity Orchestrator verwendet werden. Damit wird eine Verbindung mit Azure AD B2C oder anderen Attributanbietern (z. B. mit einem LDAP-Verzeichnis (Lightweight Directory Access Protocol) oder einer Datenbank) hergestellt.

- Eine Instanz von [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/), die auf einem virtuellen Azure-Computer oder auf dem lokalen Server Ihrer Wahl installiert ist und ausgeführt wird. Wenn Sie erfahren möchten, wie Sie die Software sowie Zugriff auf die Installations- und Konfigurationsdokumentation erhalten, kontaktieren Sie [Strata](https://www.strata.io/contact/).

- Eine lokale Anwendung, die Sie von einem Legacyidentitätssystem auf Azure AD B2C umstellen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Strata Maverics-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C**: Der Autorisierungsserver, mit dem die Anmeldeinformationen des Benutzers überprüft werden. Authentifizierte Benutzer können mit einem im Azure AD B2C-Verzeichnis gespeicherten lokalen Konto auf lokale Apps zugreifen.

- **Einen externen sozialen oder Unternehmensidentitätsanbieter**: Dabei kann es sich um einen beliebigen OpenID Connect-Anbieter, Facebook, Google oder GitHub handeln. Lesen Sie die Informationen zum Verwenden [externer Identitätsanbieter](./technical-overview.md#external-identity-providers) mit Azure AD B2C.  

- **Maverics Identity Orchestrator von Strata**: Der Dienst, der die Benutzeranmeldung orchestriert und die Identität über HTTP-Header transparent an Apps übergibt.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung der Architektur einer Azure AD B2C-Integration von Strata Maverics für den Zugriff auf Hybrid-Apps](./media/partner-strata/strata-architecture-diagram.png)

| Schritte | Beschreibung |
|:-------|:---------------|
| 1. | Der Benutzer sendet eine Anforderung für den Zugriff auf die lokale gehostete Anwendung. Maverics Identity Orchestrator sendet die Anforderung des Benutzers als Proxy an die Anwendung.|
| 2. | Der Orchestrator überprüft den Authentifizierungsstatus des Benutzers. Wenn kein Sitzungstoken empfangen wird oder das angegebene Sitzungstoken ungültig ist, wird der Benutzer zur Authentifizierung an Azure AD B2C weitergeleitet.|
| 3. | Azure AD B2C sendet die Authentifizierungsanforderung an den konfigurierten sozialen IdP.|
| 4. | Der IdP fordert den Benutzer zur Eingabe von Anmeldeinformationen auf. Abhängig vom IdP muss der Benutzer möglicherweise die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) durchführen.|
| 5. | Der IdP sendet die Authentifizierungsantwort an Azure AD B2C zurück. Optional kann der Benutzer in diesem Schritt ein lokales Konto im Azure AD B2C-Verzeichnis erstellen.|
| 6. | Azure AD B2C sendet die Benutzeranforderung an den Endpunkt, der während der Registrierung der Orchestrator-App im Azure AD B2C-Mandanten angegeben wurde.|
| 7. | Der Orchestrator wertet Zugriffsrichtlinien aus und berechnet die Attributwerte, die in die an die App weitergeleiteten HTTP-Header einbezogen werden sollen. In diesem Schritt ruft der Orchestrator möglicherweise zusätzliche Attributanbieter auf, um die zum ordnungsgemäßen Festlegen der Headerwerte erforderlichen Informationen abzurufen. Der Orchestrator legt die Headerwerte fest und sendet die Anforderung an die App.|
| 8. | Der Benutzer ist jetzt authentifiziert und hat Zugriff auf die App.|

## <a name="get-maverics-identity-orchestrator"></a>Erwerben von Maverics Identity Orchestrator
Wenden Sie sich an [Strata](https://www.strata.io/contact/), um die Software zu erhalten, mit der Sie Ihre lokale Legacy-App in Azure AD B2C integrieren. Führen Sie nach Erhalt der Software die folgenden Schritte aus, um die Orchestrator-spezifischen Voraussetzungen zu ermitteln und die erforderlichen Installations- und Konfigurationsschritte auszuführen.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Konfigurieren Ihres Azure AD B2C-Mandanten

1. **Anwendung registrieren**

   a. [Registrieren Sie den Orchestrator als Anwendung](./tutorial-register-applications.md?tabs=app-reg-ga) im Azure AD B2C-Mandanten.
   >[!Note]
   >Sie benötigen den Namen und den Bezeichner des Mandanten, die Client-ID, den geheimen Clientschlüssel, die konfigurierten Ansprüche und den Umleitungs-URI später, wenn Sie Ihre Orchestrator-Instanz konfigurieren.

   b. Erteilen Sie Ihren Anwendungen Microsoft Graph-API-Berechtigungen. Ihre Anwendung benötigt die Berechtigungen `offline_access` und `openid`.

   c. Fügen Sie einen Umleitungs-URI für Ihre Anwendung hinzu. Dieser URI entspricht dem Parameter `oauthRedirectURL` der Konfiguration des Azure AD B2C-Connectors für Ihren Orchestrator, z. B. `https://example.com/oidc-endpoint`.

2. **Erstellen eines Benutzerflows**: Erstellen Sie einen [Benutzerflow für die Registrierung und Anmeldung](./tutorial-create-user-flows.md).

3. **Hinzufügen eines Identitätsanbieters**: Wählen Sie aus, ob die Benutzeranmeldung über ein lokales Konto oder einen sozialen oder Unternehmens [identitätsanbieter](./tutorial-add-identity-providers.md) erfolgen soll.

4. **Definieren von Benutzerattributen**: Definieren Sie die Attribute, die während der Registrierung gesammelt werden sollen.

5. **Angeben von Anwendungsansprüchen**: Geben Sie die Attribute an, die über Ihre Orchestrator-Instanz an die Anwendung zurückgegeben werden sollen. Der Orchestrator verwendet Attribute aus von Azure AD B2C zurückgegebenen Ansprüchen und kann zusätzliche Attribute aus anderen verbundenen Identitätssystemen (z. B. aus LDAP-Verzeichnissen und Datenbanken) abrufen. Diese Attribute werden in HTTP-Headern festgelegt und an die lokale Upstreamanwendung gesendet.

## <a name="configure-maverics-identity-orchestrator"></a>Konfigurieren von Maverics Identity Orchestrator

In den folgenden Abschnitten werden Sie durch die Schritte geführt, die Sie zum Konfigurieren Ihrer Orchestrator-Instanz ausführen müssen. Wenn Sie weitere Unterstützung und zusätzliche Dokumentation benötigen, wenden Sie sich an [Strata](https://www.strata.io/contact/).

### <a name="maverics-identity-orchestrator-server-requirements"></a>Serveranforderungen von Maverics Identity Orchestrator

Sie können Ihre Orchestrator-Instanz auf jedem beliebigen Server – lokal oder in einer öffentlichen Cloudinfrastruktur eines Anbieters wie Azure, AWS oder GCP – ausführen.

- Betriebssystem: REHL 7.7 oder höher, CentOS 7 oder höher

- Datenträger: 10 GB (klein)

- Memory: 16 GB

- Ports: 22 (SSH/SCP), 443, 80

- Stammzugriff für Installations- und Verwaltungsaufgaben

- Maverics Identity Orchestrator wird als Benutzer `maverics` unter `systemd` ausgeführt.

- Ausgehender Netzwerkdatenverkehr vom Server, auf dem Maverics Identity Orchestrator gehostet wird, mit der Möglichkeit, Ihren Azure AD-Mandanten zu erreichen.

### <a name="install-maverics-identity-orchestrator"></a>Installieren von Maverics Identity Orchestrator

1. Besorgen Sie sich das aktuelle Maverics-RPM-Paket. Speichern Sie das Paket auf dem System, auf dem Sie Maverics installieren möchten. Wenn Sie die Datei auf einen Remotehost kopieren, stellt [SCP](https://www.ssh.com/ssh/scp/) ein nützliches Tool dar.

2. Führen Sie zum Installieren des Maverics-Pakets den folgenden Befehl aus, und ersetzen Sie dabei `maverics.rpm` durch Ihren Dateinamen.

   `sudo rpm -Uvf maverics.rpm`

   Maverics wird standardmäßig im Verzeichnis `/usr/local/bin` installiert.

3. Nach der Installation wird Maverics als Dienst unter `systemd` ausgeführt.  Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass der Maverics-Dienst ausgeführt wird:

   `sudo service maverics status`

  Wenn die Orchestrator-Installation erfolgreich war, wird eine Meldung ähnlich der folgenden angezeigt:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Wenn der Maverics-Dienst nicht gestartet werden kann, führen Sie den folgenden Befehl aus, um das Problem zu untersuchen:

   `journalctl --unit=maverics.service --reverse`

   Der neueste Protokolleintrag wird am Anfang der Ausgabe angezeigt.

Nach der Installation von Maverics wird im Verzeichnis `/etc/maverics` die Standarddatei `maverics.yaml` erstellt.

Konfigurieren Sie Ihren Orchestrator, um die Anwendung zu schützen. Integrieren Sie die Komponente in Azure AD B2C, speichern und rufen Sie Geheimnisse aus [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9) ab. Definieren Sie den Speicherort, aus dem der Orchestrator seine Konfiguration lesen soll.

### <a name="supply-configuration-using-environment-variables"></a>Angeben der Konfiguration mithilfe von Umgebungsvariablen

Geben Sie die Konfiguration für Ihre Orchestrator-Instanzen mithilfe von Umgebungsvariablen an.

`MAVERICS_CONFIG`

Diese Umgebungsvariable teilt der Orchestrator-Instanz mit, welche YAML-Konfigurationsdateien verwendet werden sollen und wo diese während des Starts oder Neustarts zu finden sind. Legen Sie die Umgebungsvariable in `/etc/maverics/maverics.env` fest.

### <a name="create-the-orchestrators-tls-configuration"></a>Erstellen der TLS-Konfiguration der Orchestrator-Instanz

In Ihrer Datei `maverics.yaml` deklariert das Feld `tls` die Transport Layer Security-Konfigurationen, die von Ihrer Orchestrator-Instanz verwendet werden. Connectors können TLS-Objekte und den Orchestrator-Server verwenden.

Der Schlüssel `maverics` ist für den Orchestrator-Server reserviert. Alle anderen Schlüssel sind verfügbar und können verwendet werden, um ein TLS-Objekt in einen bestimmten Connector einzufügen.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Konfigurieren des Azure AD B2C-Connectors

Orchestratoren verwenden Connectors zum Integrieren von Authentifizierungs- und Attributanbietern. In diesem Fall verwendet das App-Gateway des Orchestrators den Azure AD B2C-Connector sowohl als Authentifizierungs- als auch als Attributanbieter. Azure AD B2C verwendet den sozialen IdP für die Authentifizierung und fungiert dann als Attributanbieter für den Orchestrator und übergibt Attribute in den in HTTP-Headern festgelegten Ansprüchen.  

Die Konfiguration dieses Connectors entspricht der Konfiguration der im Azure AD B2C-Mandanten registrierten App.

1. Kopieren Sie die Client-ID, den geheimen Clientschlüssel und den Umleitungs-URI aus Ihrer App-Konfiguration in Ihrem Mandanten.

2. Weisen Sie Ihrem Connector einen Namen zu (in diesem Beispiel `azureADB2C`), und legen Sie den Connector-Typ (`type`) auf `azure` fest. Merken Sie sich den Connectornamen, da dieser Wert in anderen unten aufgeführten Konfigurationsparametern verwendet wird.

3. Bei dieser Integration sollte `authType` auf `oidc` festgelegt werden.

4. Legen Sie die in Schritt 1 kopierte Client-ID als Wert für den Parameter `oauthClientID` fest.

5. Legen Sie den in Schritt 1 kopierten geheimen Clientschlüssel als Wert für den Parameter `oauthClientSecret` fest.

6. Legen Sie den in Schritt 1 kopierten Umleitungs-URI als Wert für den Parameter `oauthRedirectURL` fest.

7. Der Azure AD B2C OIDC-Connector verwendet den bekannten OIDC-Endpunkt zum Ermitteln von Metadaten, einschließlich URLs und Signaturschlüsseln. Legen Sie den Wert von `oidcWellKnownURL` auf den Endpunkt Ihres Mandanten fest.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Definieren von Azure AD B2C als Authentifizierungsanbieter

Ein Authentifizierungsanbieter bestimmt, wie die Authentifizierung für einen Benutzer durchzuführen ist, der im Rahmen der App-Ressourcenanforderung keine gültige Sitzung angezeigt hat. In der Konfiguration in Ihrem Azure AD B2C-Mandanten ist festgelegt, wie ein Benutzer zur Angabe von Anmeldeinformationen aufgefordert wird und wie zusätzliche Authentifizierungsrichtlinien angewendet werden. Beispielsweise kann ein zweiter Faktor zum Abschließen des Authentifizierungsprozesses verlangt werden und entschieden werden, welche Ansprüche nach der erfolgreichen Authentifizierung an das Orchestrator-App-Gateway zurückgegeben werden sollen.

Der Wert für `authProvider` muss dem Wert `name` Ihres Connectors entsprechen.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Schützen Ihrer lokalen App mit einem Orchestrator-App-Gateway

Die Konfiguration des Orchestrator-App-Gateways deklariert, wie Azure AD B2C Ihre Anwendung schützen soll und wie Benutzer auf die App zugreifen sollen.

1. Geben Sie einen Namen für Ihr App-Gateway an. Sie können einen Anzeigenamen oder einen vollqualifizierten Hostnamen als Bezeichner für Ihre App verwenden.

2. Legen Sie die `location` fest. In diesem Beispiel wird das Stammverzeichnis der App (`/`) verwendet. Sie können jedoch einen beliebigen URL-Pfad der Anwendung angeben.

3. Definieren Sie die geschützte Anwendung über den Parameter `upstream` unter Verwendung der „Host:Port“-Konvention: `https://example.com:8080`.

4. Legen Sie die Werte für Fehler- und nicht autorisierte Seiten fest.

5. Definieren Sie die HTTP-Headernamen und die entsprechenden Attributwerte, die für die Anwendung bereitgestellt werden müssen, um die Authentifizierung einzurichten und den Zugriff auf die App zu steuern. Headernamen sind beliebige Namen und entsprechen in der Regel der Konfiguration der App. Attributwerte sind dem Namespace des Connectors zugeordnet, der sie bereitstellt. Im nachstehenden Beispiel wird den von Azure AD B2C zurückgegebenen Werten der Connectorname `azureADB2C` vorangestellt, und das Suffix ist der Name des Attributs, das den erforderlichen Wert enthält, z. B. `given_name`.

6. Legen Sie die Richtlinien fest, die ausgewertet und durchgesetzt werden sollen. Es sind drei Aktionen definiert: `allowUnauthenticated`, `allowAnyAuthenticated` und `allowIfAny`. Jede Aktion ist einer `resource` zugeordnet, und die Richtlinie wird für diese `resource` ausgewertet.

>[!NOTE]
>Sowohl `headers` als auch `policies` verwenden JavaScript- oder GoLang-Diensterweiterungen, um beliebige Logik zu implementieren, die die Standardfunktionen erheblich erweitert.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Verwenden von Azure Key Vault als Geheimnisanbieter

Es ist wichtig, die Geheimnisse zu schützen, die Ihr Orchestrator zum Herstellen der Verbindung mit Azure AD B2C und anderen Identitätssystemen verwendet. Maverics lädt standardmäßig Geheimnisse im Nur-Text-Format aus `maverics.yaml`. In diesem Tutorial verwenden Sie jedoch Azure Key Vault als Geheimnisanbieter.

Folgen Sie den Anweisungen zum [Erstellen eines neuen Schlüsseltresors](../key-vault/secrets/quick-create-portal.md), den Ihre Orchestrator-Instanz als Geheimnisanbieter verwendet. Fügen Sie Ihrem Tresor Ihre Geheimnisse hinzu, und notieren Sie sich die den einzelnen Geheimnissen zugewiesenen Geheimnisnamen (`SECRET NAME`). Beispiel: `AzureADB2CClientSecret`.

Wenn Sie einen Wert in einer Konfigurationsdatei (`maverics.yaml`) als Geheimnis deklarieren möchten, umschließen Sie ihn mit spitzen Klammern:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Der in spitzen Klammern angegebene Wert muss dem `SECRET NAME` entsprechen, der dem Geheimnis in Ihrer Azure Key Vault-Instanz zugewiesen ist.

Wenn Sie Geheimnisse aus Azure Key Vault laden möchten, legen Sie in der Datei `/etc/maverics/maverics.env` die Umgebungsvariable `MAVERICS_SECRET_PROVIDER` mit den Anmeldeinformationen aus der Datei „azure-credentials.json“ im folgenden Format fest:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Zusammenfügen der Bestandteile

Im folgenden Screenshot ist dargestellt, wie die Orchestrator-Konfiguration aussieht, wenn Sie die oben beschriebenen Konfigurationen vornehmen.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testen des Flows

1. Navigieren Sie zur URL der lokalen Anwendung: `https://example.com/sonar/dashboard`.

2. Der Orchestrator sollte Sie zu der Seite umleiten, die Sie in Ihrem Benutzerflow konfiguriert haben.

3. Wählen Sie auf der Seite den IdP aus der Liste aus.

4. Geben Sie nach der Weiterleitung an den IdP Ihre Anmeldeinformationen wie angefordert an, einschließlich eines MFA-Tokens, sofern dies für diesen IdP erforderlich ist.

5. Nach der erfolgreichen Authentifizierung sollten Sie zur Azure AD B2C-Instanz umgeleitet werden, von der die App-Anforderung an den Umleitungs-URI des Orchestrators weitergeleitet wird.

6. Der Orchestrator wertet die Richtlinien aus, berechnet Header und leitet den Benutzer an die Upstreamanwendung weiter.  

7. Die angeforderte Anwendung sollte jetzt angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)