---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Maverics Identity Orchestrator SAML Connector | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Maverics Identity Orchestrator SAML Connector konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: a4bfe2b87f3f2242189a78d9a31a89d82720fd37
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862070"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Tutorial: Integration des einmaligen Anmeldens von Azure AD mit Maverics Identity Orchestrator SAML Connector

Strata bietet eine einfache Möglichkeit, lokale Anwendungen zur Authentifizierung und Zugriffssteuerung in Azure Active Directory (Azure AD) zu integrieren.

In diesem Artikel erfahren Sie, wie Sie Maverics Identity Orchestrator für Folgendes konfigurieren:
* Inkrementelles Migrieren von Benutzern aus einem lokalen Identitätssystem zu Azure AD im Rahmen der Anmeldung bei einer lokalen Legacyanwendung
* Weiterleiten von Anmeldeanforderungen eines Legacyprodukts für die Webzugriffsverwaltung (beispielsweise CA SiteMinder oder Oracle Access Manager) an Azure AD
* Authentifizieren von Benutzern bei lokalen Anwendungen, die durch HTTP-Header oder proprietäre Sitzungscookies geschützt werden, nachdem der Benutzer über Azure AD authentifiziert wurde

Software von Strata kann lokal oder in der Cloud bereitgestellt werden. Mit dieser Software können Sie Ermittlungs-, Verbindungs- und Orchestrierungsfunktionen für verschiedene Identitätsanbieter nutzen, um eine verteilte Identitätsverwaltung für Unternehmen mit Hybridumgebungen und Umgebungen mit mehreren Clouds zu erstellen.

In diesem Tutorial erfahren Sie, wie Sie eine lokale Webanwendung migrieren, die derzeit durch ein Legacyprodukt für die Webzugriffsverwaltung (CA SiteMinder) geschützt wird. Sie lernen außerdem, wie Sie Azure AD für die Authentifizierung und Zugriffssteuerung verwenden. Im Folgenden finden Sie die grundlegenden Schritte:
1. Installieren von Maverics Identity Orchestrator
2. Registrieren Ihrer Unternehmensanwendung bei Azure AD und Konfigurieren der Anwendung für die Verwendung von Maverics Azure AD SAML Zero Code Connector für SAML-basiertes einmaliges Anmelden (Single Sign-On, SSO)
3. Integrieren von Maverics in SiteMinder und den LDAP-Benutzerspeicher ( Lightweight Directory Access Protocol)
4. Einrichten von Azure Key Vault und Konfigurieren von Maverics für die Verwendung von Key Vault als Anbieter für die Geheimnisverwaltung
5. Veranschaulichen der Benutzermigration und Sitzungsabstraktion unter Verwendung von Maverics, um den Zugriff auf eine lokale Java-Webanwendung zu ermöglichen

Weitere Installations- und Konfigurationsanweisungen finden Sie auf der [Website von Strata](https://www.strata.io).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- Ein SSO-fähiges Maverics Identity Orchestrator SAML Connector-Abonnement. Wenden Sie sich an das [Strata-Vertriebsteam](mailto:sales@strata.io), um die Maverics-Software zu beziehen.

## <a name="install-maverics-identity-orchestrator"></a>Installieren von Maverics Identity Orchestrator

Informationen zur Installation von Maverics Identity Orchestrator finden Sie in der [Installationsanleitung](https://www.strata.io).

### <a name="system-requirements"></a>Systemanforderungen
* Unterstützte Betriebssysteme
  * RHEL 7+
  * CentOS 7+

* Abhängigkeiten
  * systemd

### <a name="installation"></a>Installation

1. Rufen Sie das neueste Maverics RedHat Package Manager-Paket (RPM) ab. Kopieren Sie das Paket auf das System, auf dem Sie die Maverics-Software installieren möchten.

2. Installieren Sie das Maverics-Paket, und ersetzen Sie dabei `maverics.rpm` durch den Namen Ihrer Datei.

    `sudo rpm -Uvf maverics.rpm`

3. Nach der Installation wird Maverics als Dienst unter `systemd` ausgeführt. Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass der Dienst ausgeführt wird:

    `sudo systemctl status maverics`

Maverics wird standardmäßig im Verzeichnis */usr/local/bin* installiert.

Nach der Installation von Maverics wird die Standarddatei *maverics.yaml* im Verzeichnis */etc/maverics* erstellt. Bevor Sie die Konfiguration bearbeiten, um `workflows` und `connectors` einzuschließen, sieht die Konfigurationsdatei wie folgt aus:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Konfigurationsoptionen
### <a name="version"></a>Version
Das Feld `version` deklariert, welche Version der Konfigurationsdatei verwendet wird. Ohne Angabe der Version wird die neueste Konfigurationsversion verwendet.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` deklariert, an welcher Adresse der Orchestrator lauscht. Ist der Hostabschnitt der Adresse leer, lauscht der Orchestrator an allen verfügbaren Unicast- und Anycast-IP-Adressen des lokalen Systems. Ist der Portabschnitt der Adresse leer, wird automatisch eine Portnummer ausgewählt.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Das Feld `tls` deklariert eine Zuordnung von TLS-Objekten (Transport Layer Security). Die TLS-Objekte können sowohl von Connectors als auch vom Orchestrator-Server verwendet werden. Informationen zu den verfügbaren TLS-Optionen finden Sie in der Dokumentation zum `transport`-Paket.

Bei Verwendung des SAML-basierten einmaligen Anmeldens erfordert Microsoft Azure die Kommunikation über TLS. Informationen zum Generieren von Zertifikaten finden Sie auf der [Let's Encrypt-Website](https://letsencrypt.org/getting-started/).

Der Schlüssel `maverics` ist für den Orchestrator-Server reserviert. Alle anderen Schlüssel sind verfügbar und können verwendet werden, um ein TLS-Objekt in einen bestimmten Connector einzufügen.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Includedateien

Sie können `connectors` und `workflows` in eigenen, separaten Konfigurationsdateien definieren und in der Datei *maverics.yaml* mit `includeFiles` darauf verweisen, wie im folgenden Beispiel veranschaulicht:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

In diesem Tutorial wird nur eine einzige *maverics.yaml*-Konfigurationsdatei verwendet.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Verwenden von Azure Key Vault als Geheimnisanbieter

### <a name="manage-secrets"></a>Verwalten von Geheimnissen

Zum Laden von Geheimnissen lässt sich Maverics in verschiedene Lösungen zur Geheimnisverwaltung integrieren. Derzeit sind folgende Integrationsmethoden verfügbar: Datei, HashiCorp Vault und Azure Key Vault. Ohne Angabe einer Geheimnisverwaltungslösung lädt Maverics Geheimnisse standardmäßig im Klartext aus der *maverics.yaml*-Datei.

Wenn Sie einen Wert in einer *maverics.yaml*-Konfigurationsdatei als Geheimnis deklarieren möchten, umschließen Sie ihn mit spitzen Klammern:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Laden von Geheimnissen aus einer Datei

1. Wenn Sie Geheimnisse aus einer Datei laden möchten, fügen Sie in der Datei */etc/maverics/maverics.env* wie folgt die Umgebungsvariable `MAVERICS_SECRET_PROVIDER` hinzu:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Starten Sie dann den Maverics-Dienst mit folgendem Befehl neu:

   `sudo systemctl restart maverics`

Die Datei *secrets.yaml* kann mit einer beliebigen Anzahl von Geheimnissen (`secrets`) gefüllt werden.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Einrichten einer Azure Key Vault-Instanz

Sie können eine Azure Key Vault-Instanz über das Azure-Portal oder die Azure CLI einrichten.

**Verwenden des Azure-Portals**
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. [Erstellen Sie eine neue Key Vault-Instanz](../../key-vault/general/quick-create-portal.md).
1. [Fügen Sie der Key Vault-Instanz Geheimnisse hinzu](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Registrieren Sie eine Anwendung bei Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Autorisieren Sie eine Anwendung für die Verwendung eines Geheimnisses](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Verwenden der Azure-CLI**

1. Öffnen Sie die [Azure CLI](/cli/azure/install-azure-cli), und geben Sie folgenden Befehl ein:

    ```shell
    az login
    ```

1. Mit dem folgenden Befehl erstellen Sie eine neue Key Vault-Instanz:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Mit dem folgenden Befehl fügen Sie der Key Vault-Instanz Geheimnisse hinzu:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Mit dem folgenden Befehl registrieren Sie eine Anwendung bei Azure AD:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Mit dem folgenden Befehl autorisieren Sie eine Anwendung für die Verwendung eines Geheimnisses:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Um Geheimnisse aus Ihrer Azure Key Vault-Instanz zu laden, legen Sie in der Datei */etc/maverics/maverics.env* die Umgebungsvariable `MAVERICS_SECRET_PROVIDER` fest. Verwenden Sie dabei die Anmeldeinformationen aus der Datei *azure-credentials.json* im folgenden Format:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Starten Sie den Maverics-Dienst neu: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurieren Ihrer Anwendung in Azure AD für SAML-basiertes einmaliges Anmelden

1. Wechseln Sie in Ihrem Azure AD-Mandanten zu **Unternehmensanwendungen**, suchen Sie nach **Maverics Identity Orchestrator SAML Connector**, und wählen Sie den Eintrag aus.

1. Legen Sie im Bereich **Eigenschaften** für den Maverics Identity Orchestrator SAML Connector die Option **Benutzerzuweisung erforderlich?** auf **Nein** fest, damit die Anwendung für neu migrierte Benutzer verwendet werden kann.

1. Wählen Sie im Bereich **Übersicht** für den Maverics Identity Orchestrator SAML Connector die Option **Einmaliges Anmelden einrichten** und danach **SAML** aus.

1. Bearbeiten Sie im Bereich **SAML-basiertes einmaliges Anmelden** für den Maverics Identity Orchestrator SAML Connector die **grundlegende SAML-Konfiguration**, indem Sie auf die Schaltfläche **Bearbeiten** (Bleistiftsymbol) klicken.

   ![Screenshot der Bearbeitungsschaltfläche für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Geben Sie als **Entitäts-ID** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.maverics.org`. Die Entitäts-ID muss im Mandanten App-übergreifend eindeutig sein. Speichern Sie den hier eingegebenen Wert, um ihn in die Konfiguration von Maverics einschließen zu können.

1. Geben Sie die **Antwort-URL** im folgenden Format ein: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

1. Geben Sie die **Anmelde-URL** im folgenden Format ein: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. 

1. Wählen Sie **Speichern** aus.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Kopieren**, um die **App-Verbundmetadaten-URL** zu kopieren. Speichern Sie die URL auf Ihrem Computer.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobener Kopierschaltfläche](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Konfigurieren des Azure AD-SAML-Connectors für Maverics Identity Orchestrator

Der Azure AD-Connector für Maverics Identity Orchestrator unterstützt OpenID Connect und SAML Connect. Gehen Sie wie folgt vor, um den Connector zu konfigurieren: 

1. Legen Sie zum Aktivieren des SAML-basierten einmaliges Anmeldens `authType: saml` fest.

1. Erstellen Sie den Wert für `samlMetadataURL` im folgenden Format: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.

1. Definieren Sie die URL, an die Azure in Ihrer App zurückgeleitet wird, nachdem ein Benutzer sich mit seinen Azure-Anmeldeinformationen angemeldet hat. Verwenden Sie das folgende Format: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`.

1. Kopieren Sie den Wert der weiter oben konfigurierten Entitäts-ID: `samlEntityID: https://<SUBDOMAIN>.maverics.org`.

1. Kopieren Sie den Wert der Antwort-URL, die Azure AD zum Senden der SAML-Antwort verwendet: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`.

1. Generieren Sie einen JWT-Signaturschlüssel (JSON Web Token), mit dem die Sitzungsinformationen für Maverics Identity Orchestrator geschützt werden. Verwenden Sie dazu das [OpenSSL-Tool](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Kopieren Sie die Antwort in die Konfigurationseigenschaft `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`.

## <a name="attributes-and-attribute-mapping"></a>Attribute und Attributzuordnung
Nachdem ein Benutzer eingerichtet wurde, dient die Attributzuordnung dazu, einem Azure AD-Mandanten Benutzerattribute aus einem lokalen Quellbenutzerverzeichnis zuzuordnen.

Attribute bestimmen die Benutzerdaten, die in einem Anspruch an eine Anwendung zurückgegeben, an Sitzungscookies übergeben oder in HTTP-Headervariablen an die Anwendung übergeben werden können.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Konfigurieren der YAML-Datei für den Azure AD-SAML-Connector für Maverics Identity Orchestrator

Die Konfiguration Ihres Azure AD-SAML-Connectors für Maverics Identity Orchestrator sieht wie folgt aus:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrieren von Benutzern zu einem Azure AD-Mandanten

Gehen Sie wie hier beschrieben vor, um Benutzer nach und nach von einem Produkt für die Webzugriffsverwaltung (beispielsweise CA SiteMinder, Oracle Access Manager oder IBM Tivoli) zu migrieren. Sie können auch Benutzer aus einem LDAP-Verzeichnis oder einer SQL-Datenbank migrieren.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurieren Ihrer Anwendungsberechtigungen in Azure AD für die Benutzererstellung

1. Wechseln Sie in Ihrem Azure AD-Mandanten zu `App registrations`, und wählen Sie die Anwendung **Maverics Identity Orchestrator SAML Connector** aus.

1. Wählen Sie im Bereich **Maverics Identity Orchestrator SAML Connector | Zertifikate und Geheimnisse** die Option `New client secret` und anschließend eine Ablaufoption aus. Klicken Sie auf die Schaltfläche **Kopieren**, um das Geheimnis zu kopieren, und speichern Sie es auf Ihrem Computer.

1. Wählen Sie im Bereich **Maverics Identity Orchestrator SAML Connector | API-Berechtigungen** die Option **Berechtigung hinzufügen** aus. Anschließend wählen Sie im Bereich **API-Berechtigungen anfordern** die Optionen **Microsoft Graph** und **Anwendungsberechtigungen** aus. 

1. Im nächsten Bildschirm wählen Sie **User.ReadWrite.All** und dann **Berechtigungen hinzufügen** aus. 

1. Kehren Sie zum Bereich **API-Berechtigungen** zurück, und wählen Sie **Administratoreinwilligung erteilen** aus.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Konfigurieren der YAML-Datei für den Maverics Identity Orchestrator SAML Connector für die Benutzermigration

Um den Workflow für die Benutzermigration zu aktivieren, fügen Sie der Konfigurationsdatei die folgenden zusätzlichen Eigenschaften hinzu:
1. Geben Sie die **Azure Graph-URL** im folgenden Format ein: `graphURL: https://graph.microsoft.com`.
1. Geben Sie die **OAuth-Token-URL** im folgenden Format ein: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.
1. Geben Sie das zuvor generierte Clientgeheimnis im folgenden Format ein: `oauthClientSecret: <CLIENT SECRET>`.


Die finale Konfigurationsdatei Ihres Azure AD-Connectors für Maverics Identity Orchestrator sieht wie folgt aus:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Konfigurieren von Maverics Zero Code Connector für SiteMinder

Mit dem SiteMinder-Connector können Sie Benutzer zu einem Azure AD-Mandanten migrieren. Sie melden die Benutzer bei lokalen, durch SiteMinder geschützten Legacyanwendungen an, indem Sie die neu erstellten Azure AD-Identitäten und -Anmeldeinformationen verwenden.

Für dieses Tutorial wurde SiteMinder so konfiguriert, dass die Legacyanwendung per formularbasierter Authentifizierung und mit dem Cookie `SMSESSION` geschützt ist. Wenn Sie eine App integrieren möchten, die Authentifizierungs- und Sitzungsinformationen über HTTP-Header verarbeitet, müssen Sie dem Connector eine Konfiguration für die Headeremulation hinzufügen.

In diesem Beispiel wird das Attribut `username` dem HTTP-Header `SM_USER` zugeordnet:

```yaml
  headers:
    SM_USER: username
```

Legen Sie `proxyPass` auf den Speicherort fest, an den die Anforderungen über einen Proxy gesendet werden. Hierbei handelt es sich in der Regel um den Host der geschützten Anwendung.

`loginPage` muss der URL des Anmeldeformulars entsprechen, das zurzeit von SiteMinder verwendet wird, um Benutzer für die Authentifizierung umzuleiten.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Konfigurieren von Maverics Zero Code Connector für LDAP

Wenn Anwendungen durch ein Produkt für die Webzugriffsverwaltung (z. B. SiteMinder) geschützt sind, werden Benutzeridentitäten und -attribute in der Regel in einem LDAP-Verzeichnis gespeichert.

Diese Connectorkonfiguration zeigt, wie Sie eine Verbindung mit dem LDAP-Verzeichnis herstellen. Der Connector ist als Benutzerspeicher für SiteMinder konfiguriert. Auf diese Weise können während des Migrationsworkflows die korrekten Benutzerprofilinformationen erfasst werden, und in Azure AD kann ein entsprechender Benutzer erstellt werden.

* `baseDN` gibt den Ort in dem Verzeichnis an, für das die LDAP-Suche durchgeführt werden soll.

* `url` dient zum Angeben der Adresse und des Ports des LDAP-Servers für die Verbindungsherstellung.

* `serviceAccountUsername` ist der Benutzername für die Verbindungsherstellung mit dem LDAP-Server und wird in der Regel als Bindungs-DN ausgedrückt (beispielsweise `CN=Directory Manager`).

* `serviceAccountPassword` ist das Kennwort für die Verbindungsherstellung mit dem LDAP-Server. Dieser Wert wird in der zuvor konfigurierten Azure Key Vault-Instanz gespeichert.  

* `userAttributes` definiert die Liste der benutzerbezogenen Attribute, die abgefragt werden sollen. Diese Attribute werden später den entsprechenden Azure AD-Attributen zugeordnet.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Konfigurieren des Migrationsworkflows

Die Konfiguration des Migrationsworkflows bestimmt, wie Maverics Benutzer von SiteMinder oder LDAP zu Azure AD migriert.

Für diesen Workflow gilt Folgendes:
- Er verwendet den SiteMinder-Connector als Proxy für die SiteMinder-Anmeldung. Benutzeranmeldeinformationen werden per SiteMinder-Authentifizierung überprüft und anschließend an weitere Schritte des Workflows übergeben.
- Er ruft Benutzerprofilattribute aus dem SiteMinder-Benutzerspeicher ab.
- Er sendet eine Anforderung an die Microsoft Graph-API, um den Benutzer in Ihrem Azure AD-Mandanten zu erstellen.

Gehen Sie zum Konfigurieren des Migrationsworkflows folgendermaßen vor:

1. Geben Sie dem Workflow einen Namen (beispielsweise **Migration von SiteMinder zu Azure AD**).
1. Geben Sie den Endpunkt (`endpoint`) an. Hierbei handelt es sich um einen HTTP-Pfad, über den der Workflow verfügbar gemacht wird. Hier werden als Antwort auf Anforderungen entsprechende Aktionen (`actions`) des Workflows ausgelöst. Der Endpunkt (`endpoint`) entspricht in der Regel der App, die per Proxy weitergeleitet wird (beispielsweise `/my_app`). Der Wert muss sowohl den vorangestellten als auch den nachgestellten Schrägstrich enthalten.
1. Fügen Sie dem Workflow die passenden Aktionen (`actions`) hinzu.

   a. Definieren Sie die Methode `login` für den SiteMinder-Connector. Der Connectorwert muss dem Namenswert in der Connectorkonfiguration entsprechen.

   b. Definieren Sie die Methode `getprofile` für den LDAP-Connector.

   c.  Definieren Sie die Methode `createuser` für den AzureAD-Connector.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Überprüfen des Migrationsworkflows

1. Sollte der Maverics-Dienst noch nicht ausgeführt werden, starten Sie ihn mithilfe des folgenden Befehls:  

   `sudo systemctl start maverics`

1. Navigieren Sie zu der Anmelde-URL mit Proxyweiterleitung: `http://host.company.com/my_app`.
1. Geben Sie die Benutzeranmeldeinformationen an, die für die Anmeldung bei der Anwendung verwendet werden, während diese durch SiteMinder geschützt ist.
4. Wechseln Sie zu **Start** > **Benutzer | Alle Benutzer**, und vergewissern Sie sich, dass der Benutzer im Azure AD-Mandanten erstellt wurde.  

### <a name="configure-the-session-abstraction-workflow"></a>Konfigurieren des Workflows für die Sitzungsabstraktion

Durch den Workflow für die Sitzungsabstraktion werden Authentifizierung und Zugriffssteuerung für die lokale Legacywebanwendung in den Azure AD-Mandanten verlagert.

Der Azure-Connector verwendet die Methode `login`, um den Benutzer zur Anmelde-URL umzuleiten, sofern keine Sitzung vorhanden ist.

Nach der Authentifizierung wird das erstellte Sitzungstoken an Maverics übergeben. Anschließend wird die Methode `emulate` des SiteMinder-Connectors verwendet, um die cookie- oder headerbasierte Sitzung zu emulieren und die Anforderung mit allen zusätzlichen Attributen zu versehen, die für die Anwendung erforderlich sind.

1. Geben Sie dem Workflow einen Namen (beispielsweise **SiteMinder-Sitzungsabstraktion**).
1. Geben Sie den Endpunkt (`endpoint`) an, der der App mit Proxyweiterleitung entspricht. Der Wert muss sowohl den vorangestellten als auch den nachgestellten Schrägstrich enthalten (Beispiel: `/my_app/`).
1. Fügen Sie dem Workflow die passenden Aktionen (`actions`) hinzu.

   a. Definieren Sie die Methode `login` für den Azure-Connector. Der Connectorwert (`connector`) muss dem Namenswert (`name`) in der Connectorkonfiguration entsprechen.

   b. Definieren Sie die Methode `emulate` für den SiteMinder-Connector.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Überprüfen des Workflows für die Sitzungsabstraktion

1. Navigieren Sie zur URL der Anwendung mit der Proxyweiterleitung: `https://<AZURECOMPANY.COM>/<MY_APP>`. 
    
    Sie werden an die in der Proxyweiterleitung angegebene Anmeldeseite umgeleitet.

1. Geben Sie die Azure AD-Benutzeranmeldeinformationen ein.

   Die Umleitung zur Anwendung sollte so erfolgen, als hätten Sie sich direkt bei SiteMinder authentifiziert.
