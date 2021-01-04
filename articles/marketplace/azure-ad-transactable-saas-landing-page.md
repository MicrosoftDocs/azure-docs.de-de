---
title: Erstellen der Landing Page für Ihr transaktionsfähiges SaaS-Angebot im kommerziellen Marketplace
description: Erfahren Sie, wie Sie eine Landing Page für Ihr transaktionsfähiges SaaS-Angebot erstellen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 04137fef640da46ca8876811e127e109a8c3d445
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348303"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Erstellen der Landing Page für Ihr transaktionsfähiges SaaS-Angebot im kommerziellen Marketplace

In diesem Artikel werden Sie durch die erforderlichen Schritte zum Erstellen einer Landing Page für eine transaktionsfähige SaaS-App geleitet, die im kommerziellen Microsoft-Marketplace verkauft wird.

## <a name="overview"></a>Übersicht

Eine Landing Page dient als Einstiegspunkt für Ihr SaaS-Angebot (Software-as-a-Service). Nachdem der Käufer ein Angebot abonniert hat, wird er im kommerziellen Marketplace zur Landing Page geleitet, um das Abonnement für Ihre SaaS-Anwendung zu aktivieren und zu konfigurieren. Sie können sich diesen Vorgang als eine Art Auftragsbestätigung vorstellen, die dem Käufer zeigt, was er gekauft hat. Außerdem werden in dieser Übersicht die Kontodetails bestätigt. Sie nutzen Azure Active Directory (Azure AD) und Microsoft Graph, um das einmalige Anmelden (Single Sign-On, SSO) für den Käufer zu aktivieren und wichtige Informationen zum Käufer abzurufen. Anhand dieser Informationen (u. a. der Name, die E-Mail-Adresse und die Organisation des Käufers) können Sie das Abonnement bestätigen und aktivieren.

Da zum Aktivieren des Abonnements nur wenige Informationen erforderlich sind, die von Azure AD und Microsoft Graph bereitgestellt werden, sollten keine Informationen abgerufen werden müssen, für die mehr als die Standardeinwilligung erforderlich ist. Wenn Sie Benutzerdetails benötigen, für die eine zusätzliche Einwilligung für Ihre Anwendung erforderlich ist, sollten Sie diese Informationen nach Abschluss der Aktivierung anfordern. Dadurch wird eine reibungslose Aktivierung des Abonnements für den Käufer sichergestellt und das Risiko gesenkt, dass der Käufer den Vorgang abbricht.

Die Landing Page beinhaltet üblicherweise Folgendes:

- Name des Angebots und erworbener Tarif sowie Abrechnungsbedingungen.
- Kontodetails des Käufers (einschließlich Vor- und Nachname, Organisation und E-Mail-Adresse).
- Aufforderung zur Bestätigung der Kontodetails bzw. Angabe alternativer Kontodetails durch den Käufer.
- Informationen zu den nächsten Schritten, nachdem das Abonnement aktiviert wurde. Beispiel: Empfang einer Willkommens-E-Mail, Verwaltung des Abonnements, Supportoptionen oder Verweise auf die Dokumentation.

> [!NOTE]
> Der Käufer wird zudem auf die Landing Page geleitet, wenn er sein Abonnement nach der Aktivierung verwaltet. Nachdem das Abonnement des Käufers aktiviert wurde, muss SSO verwendet werden, damit der Benutzer sich anmelden kann. Es wird empfohlen, den Benutzer zu einem Kontoprofil oder zu einer Konfigurationsseite zu leiten.

In den folgenden Abschnitten werden Sie durch die Schritte zum Erstellen einer Landing Page geleitet:

1. [Erstellen einer Azure AD-App-Registrierung](#create-an-azure-ad-app-registration) für die Landing Page.
1. [Verwenden eines Codebeispiels als Ausgangspunkt](#use-a-code-sample-as-a-starting-point) für Ihre App.
1. [Verwenden von zwei Azure AD-Apps, um die Sicherheit in einer Produktionsumgebung zu verbessern](#use-two-azure-ad-apps-to-improve-security-in-production).
1. [Auflösen des Identifizierungstokens des Marketplace-Kaufs](#resolve-the-marketplace-purchase-identification-token), der vom kommerziellen Marketplace zur URL hinzugefügt wurde.
1. [Lesen von Informationen aus Ansprüchen, die im ID-Token codiert sind](#read-information-from-claims-encoded-in-the-id-token), das nach der Anmeldung aus Azure AD abgerufen und mit der Anforderung gesendet wurde.
1. [Verwenden der Microsoft Graph-API](#use-the-microsoft-graph-api) zum Sammeln zusätzlicher Informationen (nach Bedarf)

## <a name="create-an-azure-ad-app-registration"></a>Erstellen einer Azure AD-App-Registrierung

Der kommerzielle Marketplace ist vollständig in Azure AD integriert. Käufer authentifizieren sich mit einem [Azure AD-Konto oder einem Microsoft-Konto (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology), um den Marketplace zu nutzen. Nach dem Kauf wechselt der Käufer vom kommerziellen Marketplace zu Ihrer Landing Page-URL, um das Abonnement Ihrer SaaS-Anwendung zu aktivieren und zu verwalten. Sie müssen es dem Käufer ermöglichen, sich mit dem einmaligen Anmelden von Azure AD bei Ihrer Anwendung anzumelden. (Die URL der Landing Page wird auf der Seite [Technische Konfiguration](plan-saas-offer.md#technical-information) des Angebots angegeben.)

Zur Verwendung der Identität muss zunächst sichergestellt werden, dass Ihre Landing Page als Azure AD-Anwendung registriert ist. Durch die Registrierung der Anwendung können Sie Azure AD zum Authentifizieren von Benutzern und Anfordern des Zugriffs auf Benutzerressourcen verwenden. Sie kann als Anwendungsdefinition betrachtet werden, durch die der Dienst angewiesen wird, wie basierend auf den App-Einstellungen Token für die App ausgestellt werden sollen.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrieren einer neuen Anwendung mit dem Azure-Portal

Befolgen Sie zunächst die Anweisungen zum [Registrieren einer neuen Anwendung](../active-directory/develop/quickstart-register-app.md). Damit Benutzer anderer Unternehmen die App aufrufen können, müssen Sie eine der Optionen für Mehrinstanzenfähigkeit auswählen, wenn Sie angeben, wer die Anwendung verwenden kann.

[Konfigurieren Sie Ihre neue Anwendung für den Zugriff auf Web-APIs](../active-directory/develop/quickstart-configure-app-access-web-apis.md), wenn Sie beabsichtigen, Abfragen an die Microsoft Graph-API zu senden. Bei der Auswahl der API-Berechtigungen für diese Anwendung ist die Standardeinstellung **User.Read** ausreichend, um grundlegende Informationen zum Käufer zu erfassen. Dadurch kann das Onboarding reibungslos und automatisiert durchgeführt werden. Legen Sie keine API-Berechtigungen vom Typ **Administratoreinwilligung erforderlich** als erforderlich fest. Anderenfalls kann Ihre Landing Page nur von Benutzern besucht werden, die Administrator sind.

Wenn Sie im Rahmen Ihres Onboarding- oder Bereitstellungsvorgangs erhöhte Rechte benötigen, verwenden Sie gegebenenfalls die [inkrementelle Einwilligung](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) von Azure AD. Bei Verwendung dieser Funktion sind alle Käufer, die vom Marketplace weitergeleitet werden, anfänglich in der Lage, mit der Landing Page zu interagieren.

## <a name="use-a-code-sample-as-a-starting-point"></a>Verwenden eines Codebeispiels als Ausgangspunkt

Wir haben mehrere Beispiel-Apps bereitgestellt, mit denen eine einfache Website implementiert wird, für die die Azure AD-Anmeldung aktiviert ist. Nachdem Ihre Anwendung in Azure AD registriert wurde, wird auf dem Blatt **Schnellstart** eine Liste gängiger Anwendungstypen und Entwicklungsstapel aufgeführt (siehe Abbildung 1). Wählen Sie die geeignete Option für Ihre Umgebung aus, und befolgen Sie die Anweisungen für Download und Einrichtung.

**_Abbildung 1: Blatt „Schnellstart“ im Azure-Portal_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Zeigt das Blatt „Schnellstart“ im Azure-Portal.":::

Nachdem Sie den Code heruntergeladen und Ihre Entwicklungsumgebung eingerichtet haben, ändern Sie die Konfigurationseinstellungen in der App, indem Sie die Anwendungs-ID, die Mandanten-ID und den geheimen Clientschlüssel angeben, die bzw. den Sie in den vorherigen Schritten aufgezeichnet haben. Beachten Sie, dass die exakten Schritte abhängig davon variieren, welches Beispiel Sie verwenden.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Verwenden von zwei Azure AD-Apps, um die Sicherheit in einer Produktionsumgebung zu verbessern

In diesem Artikel ist eine vereinfachte Version der Architektur für die Implementierung einer Landing Page für Ihr SaaS-Angebot im kommerziellen Marketplace dargestellt. Wenn Sie die Seite in einer Produktionsumgebung verwenden, sollten Sie die Sicherheit erhöhen, indem Sie ausschließlich über eine separate sichere Anwendung mit den SaaS-Fulfillment-APIs kommunizieren. Zu diesem Zweck müssen Sie zwei neue Anwendungen erstellen:

- Die bisher beschriebene mehrinstanzenfähige Landing Page-Anwendung (mit Ausnahme der Funktionalität zum Kontaktieren der SaaS-Fulfillment-APIs). Diese Funktionalität wird in eine andere Anwendung ausgelagert, wie nachfolgend beschrieben.
- Eine zweite Anwendung für die Kommunikation mit den SaaS-Fulfillment-APIs. Diese Anwendung sollte nicht mehrinstanzenfähig sein und nur von Ihrer Organisation verwendet werden können. Außerdem kann mithilfe einer Zugriffssteuerungsliste festgelegt werden, dass nur über diese App auf die APIs zugegriffen werden kann.

Dadurch kann die Lösung auch in Szenarien verwendet werden, die auf dem Prinzip [	Trennung von Belangen](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) basieren. Beispiel: Die Landing Page verwendet die erste registrierte Azure AD-App, um den Benutzer anzumelden. Nach der Anmeldung des Benutzers verwendet die Landing Page die zweite Azure AD-App, um ein Zugriffstoken für den Aufruf der SaaS-Fulfillment-APIs und des Auflösungsvorgangs anzufordern.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Auflösen des Identifizierungstokens des Marketplace-Kaufs

Wenn der Käufer auf Ihre Landing Page weitergeleitet wird, wird dem URL-Parameter ein Token hinzugefügt. Dieses Token unterscheidet sich sowohl vom Token, das von Azure AD ausgestellt wird, als auch vom Zugriffstoken, das für die Dienst-zu-Dienst-Authentifizierung verwendet wird. Vielmehr wird dieses Token als Eingabe für den Auflösungsaufruf der [SaaS-Fulfillment-APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) verwendet, um die Einzelheiten des Abonnements abzurufen. Wie bei allen Aufrufen der SaaS-Fulfillment-APIs wird Ihre Dienst-zu-Dienst-Anforderung mit einem Zugriffstoken authentifiziert, das auf der Azure AD-Anwendungs-ID (Benutzer) der App für die Dienst-zu-Dienst-Authentifizierung basiert.

> [!NOTE]
> In den meisten Fällen sollte dieser Aufruf von einer zweiten Anwendung mit einem einzelnen Mandanten erfolgen. Weitere Informationen finden Sie unter [Verwenden von zwei Azure AD-Apps, um die Sicherheit in einer Produktionsumgebung zu verbessern](#use-two-azure-ad-apps-to-improve-security-in-production) weiter oben in diesem Artikel.

### <a name="request-an-access-token"></a>Anfordern eines Zugriffstokens

Zur Authentifizierung Ihrer Anwendung mit den SaaS-Fulfillment-APIs benötigen Sie ein Zugriffstoken, das durch einen Aufruf des Azure AD-OAuth-Endpunkts erzeugt werden kann. Weitere Informationen finden Sie unter [Abrufen des Autorisierungstokens des Herausgebers](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Aufrufen des Auflösungsendpunkts

Die SaaS-Fulfillment-APIs implementieren den [Auflösungsendpunkt](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription), der aufgerufen werden kann, um die Gültigkeit des Marketplace-Tokens zu bestätigen und Informationen zum Abonnement zurückzugeben.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Lesen von Informationen aus Ansprüchen, die im ID-Token codiert sind

Im Rahmen des [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md)-Flows fügt Azure AD ein [ID-Token](../active-directory/develop/id-tokens.md) zur Anforderung hinzu, wenn der Käufer zur Landing Page weitergeleitet wird. Dieses Token umfasst verschiedene grundlegende Informationen, die bei der Aktivierung nützlich sein können. Dazu zählen u. a. die in der nachfolgenden Tabelle gezeigten Informationen.

| Wert | BESCHREIBUNG |
| ------------ | ------------- |
| aud | Zielgruppe für dieses Token. In diesem Fall sollte der Wert Ihrer Anwendungs-ID entsprechen und überprüft werden. |
| preferred_username | Primärer Benutzername des Benutzers, der die App aufruft. Dabei kann es sich um die E-Mail-Adresse, die Telefonnummer oder einen anderen Bezeichner handeln. |
| email | E-Mail-Adresse des Benutzers. Dieses Feld kann leer sein. |
| name | Ein lesbarer Wert, der den Antragsteller des Tokens angibt. In diesem Fall ist dies der Name des Käufers. |
| oid | Bezeichner im Microsoft-Identitätssystem, der den Benutzer anwendungsübergreifend eindeutig identifiziert. Der Microsoft Graph gibt diesen Wert als ID-Eigenschaft für ein Benutzerkonto zurück. |
| tid | Bezeichner, der den Azure AD-Mandanten darstellt, von dem der Benutzer stammt. Im Fall einer MSA-Identität lautet dieser Wert immer ``9188040d-6c67-4c5b-b112-36a304b66dad``. Weitere Informationen finden Sie im Hinweis des nächsten Abschnitts: Verwenden der Microsoft Graph-API |
| sub | Eindeutiger Bezeichner des Benutzers in der jeweiligen Anwendung. |
|||

## <a name="use-the-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

Das ID-Token enthält grundlegende Informationen zur Identifizierung des Käufers. Für Ihren Aktivierungsprozess können jedoch weitere Details erforderlich sein (z. B. das Unternehmen des Käufers), um das Onboarding abzuschließen. Fordern Sie diese Informationen mithilfe der [Microsoft Graph-API](/graph/use-the-api) an. Dadurch verhindern Sie, dass der Benutzer diese Einzelheiten erneut eingeben muss. Die _ *User.Read* *-Berechtigungen umfassen standardmäßig folgende Informationen.

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
> Konten des MSA-Mandanten (mit Mandanten-ID ``9188040d-6c67-4c5b-b112-36a304b66dad``) geben keine weiteren Informationen zurück als die bereits mit dem ID-Token erfassten Informationen. Sie können diesen Aufruf der Graph-API für diese Konten überspringen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](create-new-saas-offer.md)