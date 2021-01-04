---
title: Erstellen der Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot im kommerziellen Marketplace
description: Erfahren Sie, wie Sie eine Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot erstellen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 699271316eccec1244db886ed2296f87c52f91ae
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348354"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Erstellen der Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot im kommerziellen Marketplace

In diesem Artikel werden Sie durch die erforderlichen Schritte zum Erstellen einer Landing Page für eine kostenlose SaaS-App oder eine SaaS-Test-App geleitet, die im kommerziellen Microsoft-Marketplace verkauft wird.

## <a name="overview"></a>Übersicht

Eine Landing Page dient als Einstiegspunkt für Ihr SaaS-Angebot (Software-as-a-Service). Nachdem sich der Kunde entschieden hat, Ihre App zu erwerben, wird er im kommerziellen Marketplace zur Landing Page geleitet, um das Abonnement für Ihre SaaS-Anwendung zu aktivieren und zu konfigurieren. Wenn Sie ein SaaS-Angebot (Software-as-a-Service) im Partner Center erstellen, können Sie wählen, ob Sie [über Microsoft verkaufen](plan-saas-offer.md#listing-options) möchten oder nicht. Wenn Sie Ihr Angebot nur im kommerziellen Microsoft-Marketplace auflisten und nicht über Microsoft verkaufen möchten, haben Sie die Möglichkeit anzugeben, wie potenzielle Kunden mit dem Angebot interagieren können. Wenn Sie die Auflistungsoption **Jetzt abrufen (kostenlos)** oder **Kostenlose Testversion** aktivieren, müssen Sie eine Landing Page-URL angeben, über die der Benutzer auf das kostenlose Abonnement oder die kostenlose Testversion zugreifen kann.

Der Zweck der Landing Page besteht darin, den Benutzer auf einfache Weise zu empfangen, damit er die kostenlose Testversion oder das kostenlose Abonnement aktivieren kann. Sie nutzen Azure Active Directory (Azure AD) und Microsoft Graph, um das einmalige Anmelden (Single Sign-On, SSO) für den Benutzer zu aktivieren und um wichtige Informationen zum Benutzer abzurufen. Anhand dieser Informationen (u. a. der Name, die E-Mail-Adresse und das Unternehmen des Benutzers) können Sie die kostenlose Testversion oder das kostenlose Abonnement aktivieren.

Da zum Aktivieren des Abonnements nur wenige Informationen erforderlich sind, die von Azure AD und Microsoft Graph bereitgestellt werden, sollten keine Informationen abgerufen werden müssen, für die mehr als die Standardeinwilligung erforderlich ist. Wenn Sie Benutzerdetails benötigen, für die eine zusätzliche Einwilligung für Ihre Anwendung erforderlich ist, sollten Sie diese Informationen anfordern, nachdem die Aktivierung des Abonnements abgeschlossen ist. Dadurch wird eine reibungslose Aktivierung des Abonnements für den Benutzer sichergestellt und das Risiko gesenkt, dass der Benutzer den Vorgang abbricht.

Die Landing Page beinhaltet üblicherweise folgende Informationen und Auflistungsoptionen:

- Den Namen und Details zur kostenlosen Testversion oder zum kostenlosen Abonnement. Geben Sie beispielsweise die Nutzungslimits oder die Dauer einer Testversion an.
- Die Kontodetails des Benutzers, einschließlich Vor- und Nachname, Unternehmen und E-Mail-Adresse.
- Eine Aufforderung für den Benutzer, die Kontodetails zu bestätigen oder alternative Kontodetails anzugeben.
- Ein Leitfaden, der den Benutzer über die nächsten Schritte nach der Aktivierung informiert. Beispiel: Erhalt einer Willkommens-E-Mail, Verwaltung des Abonnements, Supportoptionen oder Verweise auf die Dokumentation.

In den folgenden Abschnitten dieses Artikels werden Sie durch die Schritte zum Erstellen einer Landing Page geleitet:

1. [Erstellen einer Azure AD-App-Registrierung](#create-an-azure-ad-app-registration) für die Landing Page
2. [Verwenden eines Codebeispiels als Ausgangspunkt](#use-a-code-sample-as-a-starting-point) für Ihre App
3. [Lesen von Informationen aus Ansprüchen, die im ID-Token codiert sind](#read-information-from-claims-encoded-in-the-id-token), das nach der Anmeldung von Azure AD empfangen und mit der Anforderung gesendet wurde.
4. [Verwenden der Microsoft Graph-API](#use-the-microsoft-graph-api) zum Sammeln zusätzlicher Informationen (nach Bedarf)

## <a name="create-an-azure-ad-app-registration"></a>Erstellen einer Azure AD-App-Registrierung

Der kommerzielle Marketplace ist vollständig in Azure AD integriert. Benutzer authentifizieren sich mit einem [Azure AD-Konto oder einem Microsoft-Konto (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology), um den Marketplace zu nutzen. Nach dem Erwerb einer kostenlosen Testversion oder eines kostenlosen Abonnements über Ihr reines Auflistungsangebot wechselt der Benutzer vom kommerziellen Marketplace zu Ihrer Landing Page-URL, um sein Abonnement für Ihre SaaS-Anwendung zu aktivieren und zu verwalten. Sie müssen es dem Benutzer ermöglichen, sich mit dem einmaligen Anmelden in Azure AD bei Ihrer Anwendung anzumelden. (Die URL der Landing Page wird auf der Seite [Technische Konfiguration](plan-saas-offer.md#technical-information) des Angebots angegeben.)

Zur Verwendung der Identität muss zunächst sichergestellt werden, dass Ihre Landing Page als Azure AD-Anwendung registriert ist. Durch die Registrierung der Anwendung können Sie Azure AD zum Authentifizieren von Benutzern und Anfordern des Zugriffs auf Benutzerressourcen verwenden. Sie kann als Anwendungsdefinition betrachtet werden, durch die der Dienst angewiesen wird, wie basierend auf den App-Einstellungen Token für die App ausgestellt werden sollen.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrieren einer neuen Anwendung mit dem Azure-Portal

Befolgen Sie zunächst die Anweisungen zum [Registrieren einer neuen Anwendung](../active-directory/develop/quickstart-register-app.md). Damit Benutzer aus anderen Unternehmen auf die App zugreifen können, müssen Sie **„Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)“ und „Persönliche Microsoft-Konten (z. B. Skype oder Xbox)“** auswählen, wenn Sie gefragt werden, wer die Anwendung verwenden darf.

[Konfigurieren Sie Ihre neue Anwendung für den Zugriff auf Web-APIs](../active-directory/develop/quickstart-configure-app-access-web-apis.md), wenn Sie beabsichtigen, Abfragen an die Microsoft Graph-API zu senden. Bei der Auswahl der API-Berechtigungen für diese Anwendung ist die Standardeinstellung **User.Read** ausreichend, um grundlegende Informationen zum Benutzer zu erfassen. Dadurch kann das Onboarding reibungslos und automatisiert durchgeführt werden. Legen Sie keine API-Berechtigungen vom Typ **Administratoreinwilligung erforderlich** als erforderlich fest. Anderenfalls kann Ihre Landing Page nur von Benutzern mit Administratorfunktion besucht werden.

Wenn Sie im Rahmen Ihres Onboarding- oder Bereitstellungsvorgangs jedoch erhöhte Rechte benötigen, verwenden Sie gegebenenfalls die [inkrementelle Einwilligung](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) von Azure AD. Bei Verwendung dieser Funktion sind alle Benutzer, die vom Marketplace weitergeleitet werden, anfänglich in der Lage, mit der Landing Page zu interagieren.

## <a name="use-a-code-sample-as-a-starting-point"></a>Verwenden eines Codebeispiels als Ausgangspunkt

Microsoft hat mehrere Beispiel-Apps bereitgestellt, mit denen eine einfache Website implementiert wird, für die die Azure AD-Anmeldung aktiviert ist. Nachdem Ihre Anwendung in Azure AD registriert wurde, wird auf dem Blatt **Schnellstart** eine Liste gängiger Anwendungstypen und Entwicklungsstapel aufgeführt (Abbildung 1). Wählen Sie die geeignete Option für Ihre Umgebung aus, und befolgen Sie die Anweisungen zum Herunterladen und Einrichten.

**_Abbildung 1: Blatt „Schnellstart“ im Azure-Portal_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Veranschaulicht das Blatt „Schnellstart“ im Azure-Portal.":::

Nachdem Sie den Code heruntergeladen und Ihre Entwicklungsumgebung eingerichtet haben, ändern Sie die Konfigurationseinstellungen in der App, indem Sie die Anwendungs-ID, die Mandanten-ID und den geheimen Clientschlüssel angeben, die bzw. den Sie in den vorherigen Schritten aufgezeichnet haben. Beachten Sie, dass die exakten Schritte abhängig davon variieren, welches Beispiel Sie verwenden.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Lesen von Informationen aus Ansprüchen, die im ID-Token codiert sind

Im Rahmen des [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md)-Flows fügt Azure AD ein [ID-Token](../active-directory/develop/id-tokens.md) zur Anforderung hinzu, wenn der Benutzer zur Landing Page weitergeleitet wird. Dieses Token umfasst verschiedene grundlegende Informationen, die bei der Aktivierung nützlich sein können. Dazu zählen u. a. die in der nachfolgenden Tabelle enthaltenen Informationen.

| Wert | BESCHREIBUNG |
| ------------ | ------------- |
| aud | Zielgruppe für dieses Token. In diesem Fall sollte der Wert Ihrer Anwendungs-ID entsprechen und überprüft werden. |
| preferred_username | Primärer Benutzername des Benutzers, der die App aufruft. Dabei kann es sich um die E-Mail-Adresse, die Telefonnummer oder einen anderen Bezeichner handeln. |
| email | E-Mail-Adresse des Benutzers. Dieses Feld kann leer sein. |
| name | Ein lesbarer Wert, der den Antragsteller des Tokens angibt. In diesem Fall ist dies der Benutzername. |
| oid | Bezeichner im Microsoft-Identitätssystem, der den Benutzer anwendungsübergreifend eindeutig identifiziert. Der Microsoft Graph gibt diesen Wert als ID-Eigenschaft für ein Benutzerkonto zurück. |
| tid | Bezeichner, der den Azure AD-Mandanten darstellt, dem der Benutzer angehört. Im Fall einer MSA-Identität lautet dieser Wert immer `9188040d-6c67-4c5b-b112-36a304b66dad`. Weitere Informationen finden Sie im nächsten Abschnitt unter „Hinweis“: Verwenden der Microsoft Graph-API |
| sub | Eindeutiger Bezeichner des Benutzers in der jeweiligen Anwendung. |
|||

## <a name="use-the-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

Das ID-Token enthält grundlegende Informationen zur Identifizierung des Benutzers. Für Ihren Aktivierungsprozess können jedoch weitere Details erforderlich sein (z. B. das Unternehmen des Benutzers), um das Onboarding abzuschließen. Fordern Sie diese Informationen mithilfe der [Microsoft Graph-API](/graph/use-the-api) an. Dadurch verhindern Sie, dass der Benutzer diese Angaben erneut machen muss. Die _ *User.Read* *-Berechtigungen umfassen standardmäßig folgende Informationen:

| Wert | BESCHREIBUNG |
| ------------ | ------------- |
| displayName | Der im Adressbuch angezeigte Name für den Benutzer. |
| givenName | Der Vorname des Benutzers. |
| jobTitle | Die Position des Benutzers. |
| mail | Die SMTP-Adresse für den Benutzer. |
| mobilePhone | Die primäre Mobiltelefonnummer des Benutzers. |
| preferredLanguage | Der ISO 639-1-Code für die bevorzugte Sprache des Benutzers. |
| surname | Der Nachname des Benutzers. |
|||

Es können weitere Eigenschaften wie der Name des Unternehmens oder der Standort des Benutzers (Land) ausgewählt werden, die in der Anforderung enthalten sein sollen. Weitere Informationen finden Sie unter [Eigenschaften für den Benutzerressourcentyp](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true#properties).

Die meisten Apps, die in Azure AD registriert sind, erteilen delegierte Berechtigungen zum Lesen der Benutzerinformationen aus dem Azure AD-Mandanten seines Unternehmens. Microsoft Graph-Anforderungen zum Abrufen dieser Informationen müssen ein Zugriffstoken zur Authentifizierung enthalten. Die jeweils erforderlichen Schritte zum Erzeugen des Zugriffstokens hängen vom verwendeten Technologiestapel ab. Ein Beispiel finden Sie im Beispielcode. Weitere Informationen finden Sie unter [Zugreifen im Namen eines Benutzers](/graph/auth-v2-user).

> [!NOTE]
> Konten des MSA-Mandanten (mit Mandanten-ID `9188040d-6c67-4c5b-b112-36a304b66dad`) geben keine weiteren Informationen zurück als die bereits mit dem ID-Token erfassten Informationen. Sie können diesen Aufruf der Graph-API für diese Konten überspringen.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](create-new-saas-offer.md)