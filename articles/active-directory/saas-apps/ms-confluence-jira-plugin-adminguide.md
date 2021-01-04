---
title: 'Atlassian Jira/Confluence – Administratorhandbuch: Azure Active Directory | Microsoft-Dokumentation'
description: Administratorhandbuch für die Verwendung von Atlassian Jira und Confluence mit Azure Active Directory (Azure AD)
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359156"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian Jira und Confluence – Administratorhandbuch für Azure Active Directory

## <a name="overview"></a>Übersicht

Das Plug-In zum einmaligen Anmelden (Single Sign-On, SSO) in Azure Active Directory (Azure AD) ermöglicht Microsoft Azure AD-Kunden, ihre Geschäfts- oder Schulkonten zur Anmeldung bei Atlassian JIRA- und Confluence Server-basierten Produkten zu verwenden. Es implementiert SSO auf SAML 2.0-Basis.

## <a name="how-it-works"></a>Funktionsweise

Wenn Benutzer sich bei einer Atlassian JIRA- oder Confluence-Anwendung anmelden möchten, sehen sie die Schaltfläche **Anmelden mit Azure AD** auf der Anmeldeseite. Wenn sie diese Option auswählen, müssen sie sich mit der Azure AD-Organisationsanmeldeseite (d.h. ihrem Geschäfts- oder Schulkonto) anmelden.

Sobald die Benutzer authentifiziert sind, sollten sie sich bei der Anwendung anmelden können. Wenn sie bereits mit der ID und dem Kennwort für ihr Geschäfts- oder Schulkonto authentifiziert sind, melden sie sich direkt bei der Anwendung an. 

Die Anmeldung funktioniert in JIRA und Confluence. Wenn Benutzer bei einer JIRA-Anwendung angemeldet sind und Confluence im selben Browserfenster geöffnet ist, müssen sie keine Anmeldeinformationen für die andere App bereitstellen. 

Benutzer können auch unter dem Geschäfts- oder Schulkonto über „Meine Apps“ zu dem Atlassian-Produkt gelangen. Sie sollten angemeldet werden, ohne nach den Anmeldeinformationen gefragt zu werden.

> [!NOTE]
> Die Benutzerbereitstellung erfolgt nicht mithilfe des Plug-Ins.

## <a name="audience"></a>Zielgruppe

JIRA- und Confluence-Administratoren können das Plug-In verwenden, um SSO mithilfe von Azure AD zu aktivieren.

## <a name="assumptions"></a>Annahmen

* JIRA- und Confluence-Instanzen sind HTTPS-tauglich.
* Benutzer sind bereits in JIRA bzw. Confluence erstellt.
* Benutzern sind Rollen in JIRA bzw. Confluence zugewiesen.
* Administratoren haben Zugriff auf Informationen, die zum Konfigurieren des Plug-Ins erforderlich sind.
* JIRA bzw. Confluence ist auch außerhalb des Unternehmensnetzwerks verfügbar.
* Das Plug-In funktioniert nur mit den lokalen Versionen von JIRA und Confluence.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Plug-In installieren, beachten Sie Folgendes:

* JIRA und Confluence sind auf einer 64-Bit-Version von Windows installiert.
* Die Versionen von JIRA und Confluence sind HTTPS-tauglich.
* JIRA und Confluence sind im Internet verfügbar.
* Administratoranmeldeinformationen sind für JIRA und Confluence vorhanden.
* Administratoranmeldeinformationen sind für Azure AD vorhanden.
* WebSudo ist in JIRA und Confluence deaktiviert.

## <a name="supported-versions-of-jira-and-confluence"></a>Unterstützte Versionen von JIRA und Confluence

Das Plug-In unterstützt die folgenden Versionen von JIRA und Confluence:

* JIRA Core und Software: 6.0 bis 7.12
* JIRA Service Desk: 3.0.0 bis 3.5.0
* JIRA unterstützt auch 5.2. Klicken Sie zum Anzeigen weiterer Einzelheiten auf [Microsoft Azure Active Directory single sign-on for JIRA 5.2](./jira52microsoft-tutorial.md) (Microsoft Azure Active Directory-SSO für JIRA 5.2).
* Confluence: 5.0 bis 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Installation

Führen Sie diese Schritte aus, um das Plug-In zu installieren:

1. Melden Sie sich bei Ihrer JIRA- bzw. Confluence-Instanz als Administrator an.

2. Wechseln Sie zur JIRA-/Confluence-Verwaltungskonsole, und wählen Sie **Add-Ons** aus.

3. Laden Sie im Microsoft Download Center das [Microsoft SAML-SSO-Plug-In für Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML-SSO-Plug-In für Confluence](https://www.microsoft.com/download/details.aspx?id=56503) herunter.

   Die entsprechende Version des Plug-Ins wird in den Suchergebnissen angezeigt.

4. Wählen Sie das Plug-In aus, und der Universal Plug-In-Manager (UPM) installiert es.

Sobald das Plug-In installiert ist, wird es im Abschnitt **Vom Benutzer installierte Add-Ons** von **Add-Ons verwalten** angezeigt.

## <a name="plug-in-configuration"></a>Plug-In-Konfiguration

Bevor Sie das Plug-In verwenden, müssen Sie es konfigurieren. Wählen Sie das Plug-In und die Schaltfläche **Konfigurieren** aus, und geben Sie die Konfigurationsdetails an.

Die folgende Abbildung zeigt den Konfigurationsbildschirm in JIRA und Confluence an:

![Plug-In-Konfigurationsbildschirm](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metadaten-URL** : Die URL zum Abrufen der Verbundmetadaten von Azure AD.

* **Bezeichner** : Die URL, die Azure AD zum Überprüfen der Quelle der Anforderung verwendet. Sie wird dem Element **Bezeichner** in Azure AD zugeordnet. Das Plug-In leitet diese URL automatisch als „https:// *\<domain:port>* /“ ab.

* **Antwort-URL** : Die Antwort-URL in Ihrem Identitätsanbieter (IdP), die die SAML-Anmeldung initiiert. Sie wird dem **Antwort-URL** -Element in Azure AD zugeordnet. Das Plug-In leitet diese URL automatisch als „https:// *\<domain:port>* /plugins/servlet/saml/auth“ ab.

* **Anmelde-URL** : Die Anmelde-URL in Ihrem IdP, die die SAML-Anmeldung initiiert. Sie wird dem **Anmelde** -Element in Azure AD zugeordnet. Das Plug-In leitet diese URL automatisch als „https:// *\<domain:port>* /plugins/servlet/saml/auth“ ab.

* **IdP-Entitäts-ID** : Die Entitäts-ID, die Ihr IdP verwendet. Dieses Feld wird gefüllt, wenn die Metadaten-URL aufgelöst ist.

* **Anmelde-URL** : Die Anmelde-URL aus Ihrem IdP. Dieses Feld wird aus Azure AD gefüllt, wenn die Metadaten-URL aufgelöst ist.

* **Abmelde-URL** : Die Abmelde-URL aus Ihrem IdP. Dieses Feld wird aus Azure AD gefüllt, wenn die Metadaten-URL aufgelöst ist.

* **X.509-Zertifikat** : Das X.509-Zertifikat Ihres IdP. Dieses Feld wird aus Azure AD gefüllt, wenn die Metadaten-URL aufgelöst ist.

* **Anmeldeschaltflächen-Name** : Der Name der Anmeldeschaltfläche, die Ihre Organisation Benutzern auf der Anmeldeseite anzeigen möchte.

* **SAML-Benutzer-ID-Speicherorte** : Der Speicherort, wo die JIRA- bzw. Confluence-Benutzer-ID in der SAML-Antwort erwartet wird. Dies kann in **NameID** oder einem benutzerdefinierten Attributnamen sein.

* **Attributname** : Name des Attributs, in dem die Benutzer-ID erwartet werden kann.

* **Startbereichsermittlung aktivieren** : Erforderliche Auswahl, wenn das Unternehmen auf Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS) basierendes Anmelden einsetzt.

* **Domänenname** : Der Domänenname bei AD FS-basiertem Anmelden.

* **Einmaliges Anmelden aktivieren** : Erforderliche Auswahl, wenn eine Abmeldung bei Azure AD erfolgen soll, wenn ein Benutzer sich bei JIRA oder Confluence abmeldet.

## <a name="troubleshooting"></a>Problembehandlung

* **Fehlermeldung, dass mehrere Zertifikate vorliegen** : Melden Sie sich bei Azure AD an, und entfernen Sie mehrere Zertifikate, die für die App verfügbar sind. Stellen Sie sicher, dass nur ein Zertifikat vorhanden ist.

* **Ein Zertifikat läuft bald in Azure AD ab** : Add-Ons tragen Sorge für den automatischen Rollover des Zertifikats. Wenn ein Zertifikat in Kürze abläuft, sollte das neue Zertifikat als aktiv gekennzeichnet werden, und nicht verwendete Zertifikate sollten gelöscht werden. Wenn ein Benutzer versucht, sich in diesem Szenario bei JIRA anzumelden, ruft das Plug-In das neue Zertifikat ab und speichert es.

* **Sie möchten WebSudo (die sichere Administratorsitzung) deaktivieren** :

  * Für JIRA sind sichere Administratorsitzungen (d.h. Bestätigung des Kennworts vor dem Zugriff auf die Verwaltungsfunktionen) standardmäßig aktiviert. Wenn Sie diese Funktion aus Ihrer JIRA-Instanz entfernen möchten, geben Sie die folgende Zeile in die Datei „jira-config.properties“ ein: `jira.websudo.is.disabled = true`

  * Führen Sie für Confluence die Schritte auf der [Support-Website von Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html) aus.

* **Felder, die von der Metadaten-URL aufgefüllt werden sollten, werden nicht aufgefüllt** :

  * Überprüfen Sie, ob die URL richtig ist. Überprüfen Sie, ob Sie den richtigen Mandanten und die richtige App-ID zugeordnet haben.

  * Geben Sie die URL in einen Browser ein, und stellen Sie fest, ob Sie die Verbundmetadaten-XML empfangen.

* **Es liegt ein interner Serverfehler vor** : Überprüfen Sie die Protokolle im Protokollverzeichnis der Installation. Falls der Fehler gemeldet wird, wenn der Benutzer versucht, sich mittels Azure AD-SSO anzumelden, können Sie die Protokolle gemeinsam mit dem Supportteam auswerten.

* **Wenn der Fehler „Benutzer-ID wurde nicht gefunden“ auftritt, wenn der Benutzer versucht, sich anzumelden** : Erstellen Sie die Benutzer-ID in JIRA oder Confluence.

* **„App nicht gefunden“-Fehlermeldung in Azure AD** : Stellen Sie fest, ob die entsprechende URL in Azure AD der App zugeordnet ist.

* **Benötigen Sie Support?** : Wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Das Team antwortet innerhalb von 24 bis 48 Geschäftsstunden.

  Sie können auch bei Microsoft ein Supportticket über den Azure-Portalkanal erstellen.

## <a name="plug-in-faq"></a>Häufig gestellte Fragen zum Plug-In

Im Anschluss finden Sie einige Antworten auf mögliche Fragen zu diesem Plug-In.

### <a name="what-does-the-plug-in-do"></a>Was macht das Plug-In?

Das Plug-In ermöglicht das einmalige Anmelden (Single Sign On, SSO) bei lokaler Atlassian JIRA- und Confluence-Software (einschließlich JIRA Core, JIRA Software, JIRA Service Desk). Das Plug-In funktioniert mit Azure Active Directory (Azure AD) als Identitätsanbieter (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Mit welchen Atlassian-Produkten ist das Plug-In einsetzbar?

Das Plug-In funktioniert mit lokalen Versionen von JIRA und Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Funktioniert dieses Plug-In mit Cloudversionen?

Nein. Das Plug-In unterstützt lokale Versionen von JIRA und Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Welche Versionen von JIRA und Confluence unterstützt das Plug-In?

Das Plug-In unterstützt diese Versionen:

* JIRA Core und Software: 6.0 bis 7.12
* JIRA Service Desk: 3.0.0 bis 3.5.0
* JIRA unterstützt auch 5.2. Klicken Sie zum Anzeigen weiterer Einzelheiten auf [Microsoft Azure Active Directory single sign-on for JIRA 5.2](./jira52microsoft-tutorial.md) (Microsoft Azure Active Directory-SSO für JIRA 5.2).
* Confluence: 5.0 bis 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Ist das Plug-In kostenlos oder kostenpflichtig?

Es ist ein kostenloses Add-On.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Muss ich JIRA bzw. Confluence nach dem Bereitstellen des Plug-Ins neu starten?

Es ist kein Neustart erforderlich. Sie können das Plug-In sofort benutzen.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Wie erhalte ich Support für das Plug-In?

Setzen Sie sich mit dem [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) in Verbindung, um Support für dieses Plug-In zu erhalten. Das Team antwortet innerhalb von 24 bis 48 Geschäftsstunden.

Sie können auch bei Microsoft ein Supportticket über den Azure-Portalkanal erstellen.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Funktioniert dieses Plug-In unter der Mac- oder Ubuntu-Installation von JIRA und Confluence?

Wir haben dieses Plug-In nur mit 64-Bit-Windows-Serverinstallationen von JIRA und Confluence getestet.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Funktioniert das Plug-In mit anderen IdPs als Azure AD?

Nein. Es funktioniert nur mit Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Mit welcher SAML-Version funktioniert das Plug-In?

Es funktioniert mit SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Führt das Plug-In eine Benutzerbereitstellung durch?

Nein. Das Plug-In stellt nur SSO auf Basis von SAML 2.0 bereit. Der Benutzer muss vor der SSO-Anmeldung in der Anwendung bereitgestellt werden.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Unterstützt das Plug-In Clusterversionen von JIRA und Confluence?

Nein. Das Plug-In funktioniert mit lokalen Versionen von JIRA und Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Funktioniert das Plug-In mit HTTP-Versionen von JIRA und Confluence?

Nein. Das Plug-In funktioniert nur mit HTTPS-fähigen Installationen.