---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Ping Identity
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Azure AD B2C-Authentifizierung und Ping Identity integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 430629f94695f0689422434c8d80fe4e1876e5dd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900033"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutorial: Konfigurieren von Ping Identity mit Azure Active Directory B2C für sicheren Hybridzugriff

In diesem Beispieltutorial erfahren Sie, wie Sie Azure Active Directory B2C (AD) mit [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) und [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) so erweitern, dass ein sicherer Hybridzugriff ermöglicht wird.

Viele Elemente im Web wie E-Commerce-Websites und Webanwendungen, die im Internet verfügbar gemacht werden, werden hinter einem Proxysystem bereitgestellt, das mitunter auch als Reverseproxysystem bezeichnet wird. Diese Proxysysteme bieten verschiedene Funktionen, einschließlich Vorauthentifizierung, Erzwingung von Richtlinien und Routing von Datenverkehr. Zu den Beispielen von Anwendungsfällen gehören der Schutz der Webanwendung vor eingehendem Webdatenverkehr und die Bereitstellung einer einheitlichen Sitzungsverwaltung in Bereitstellungen mit verteilten Servern.

In den meisten Fällen umfasst diese Konfiguration eine Authentifizierungsübersetzungsebene, die die Authentifizierung aus der Webanwendung auslagert. Reverseproxys wiederum stellen den Webanwendungen den Kontext des authentifizierten Benutzers zur Verfügung, und zwar in einer einfacheren Form wie einem Headerwert in Klartext- oder Digestform. Bei einer solchen Konfiguration nutzen die Anwendungen keine branchenüblichen Token wie Security Assertion Markup Language (SAML), OAuth oder Open ID Connect (OIDC). Vielmehr sind sie auf den Proxy angewiesen, der den Authentifizierungskontext bereitstellt und die Sitzung mit dem Agent des Endbenutzers wie z. B. dem Browser oder der nativen Anwendung aufrechterhält. Als Dienst, der in einem zwischengeschalteten Dienst ausgeführt wird, können Proxys die ultimative Sitzungskontrolle bieten. Das bedeutet auch, dass der Proxydienst überaus effizient und skalierbar sein sollte, um nicht zu einem Engpass oder Single Point of Failure für die Anwendungen dahinter zu werden. Das Diagramm ist eine Darstellung einer typischen Implementierung eines Reverseproxys und der Kommunikationsabläufe.

![Abbildung mit der Implementierung eines Reverseproxys](./media/partner-ping/reverse-proxy.png)

Wenn Sie vorhaben, die Identitätsplattform in solchen Konfigurationen zu modernisieren, ergeben sich folgende Bedenken.

- Wie kann der Aufwand für die Modernisierung der Anwendung von der Modernisierung der Identitätsplattform entkoppelt werden?

- Wie kann eine Koexistenzumgebung mit moderner und Legacy-Authentifizierung aufgebaut werden, die vom modernisierten Identitätsdienstanbieter genutzt wird?

  a. Wie lässt sich die Konsistenz der Endbenutzererfahrung verbessern?

  b. Wie lässt sich eine einheitliche Anmeldung für alle nebeneinander bestehenden Anwendungen realisieren?

Wir erörtern einen Ansatz zur Beantwortung dieser Fragen, indem Azure AD B2C und die Technologien [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) und [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) integriert werden.

## <a name="coexistence-environment"></a>Koexistenzumgebung

Eine technisch einfache und kostengünstige Lösung besteht darin, das Reverseproxysystem so zu konfigurieren, dass es das modernisierte Identitätssystem nutzt und die Authentifizierung delegiert.  
Proxys unterstützen in diesem Fall die modernen Authentifizierungsprotokolle und arbeiten mit der auf Umleitung basierenden (passive) Authentifizierung, die den Benutzer zum neuen Identitätsanbieter leitet.

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C als Identitätsanbieter

Azure AD B2C ermöglicht die Definition von **Richtlinien**, die verschiedene Benutzererfahrungen und -verhalten steuern. Diese werden auch als **User Journeys** bezeichnet und vom Server aus orchestriert. Diese Richtlinien machen einen Protokollendpunkt verfügbar, der die Authentifizierung vergleichbar mit einem Identitätsanbieter durchführen kann. Auf Anwendungsseite ist für bestimmte Richtlinien keine besondere Verarbeitung erforderlich. Die Anwendung stellt lediglich eine standardmäßige Authentifizierungsanforderung an den protokollspezifischen Authentifizierungsendpunkt, der durch die betreffende Richtlinie verfügbar gemacht wird.  
Azure AD B2C kann so konfiguriert werden, dass derselbe Aussteller für mehrere Richtlinien oder ein eindeutiger Aussteller für jede Richtlinie verwendet wird. Jede Anwendung kann auf eine oder mehrere dieser Richtlinien verweisen, indem sie eine protokollnative Authentifizierungsanforderung stellt und das gewünschte Benutzerverhalten wie Anmeldung, Registrierung und Profilbearbeitung steuert. Das Diagramm zeigt die Workflows der Anwendungen OIDC und SAML.

![Abbildung der Implementierung von OIDC und SAML](./media/partner-ping/azure-ad-identity-provider.png)

Während das genannte Szenario für modernisierte Anwendungen bestens funktioniert, kann es für die Legacy-Anwendungen eine Herausforderung sein, den Benutzer adäquat umzuleiten, da die Zugriffsanforderungen an die Anwendungen möglicherweise nicht den Kontext für die Benutzererfahrung enthalten. In den meisten Fällen fängt die Proxyebene oder ein integrierter Agent in der Webanwendung die Zugriffsanforderung ab.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess als Reverseproxy

Viele Kunden haben PingAccess als Reverseproxy bereitgestellt, der eine oder mehrere Rollen übernimmt, wie bereits in diesem Artikel erwähnt. PingAccess kann eine direkte Anforderung abfangen, indem es als Vermittlung oder Umleitung fungiert, die von einem auf dem Server der Webanwendung laufenden Agent ausgeht.

PingAccess kann mit OIDC, OAuth2 oder SAML konfiguriert werden, um eine Authentifizierung bei einem vorgelagerten Authentifizierungsanbieter durchzuführen. Auf dem PingAccess-Server kann dazu ein einzelner vorgelagerter Identitätsanbieter konfiguriert werden. Im folgenden Diagramm wird diese Konfiguration veranschaulicht.

![Abbildung von PingAccess mit OIDC-Implementierung](./media/partner-ping/authorization-flow.png)

In einer typischen Azure AD B2C-Bereitstellung, in der mehrere Richtlinien mehrere **Identitätsanbieter** verfügbar machen, stellt dies eine Herausforderung dar. Denn PingAccess kann nur mit einem einzigen vorgelagerten Identitätsanbieter konfiguriert werden.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate als Verbundproxy

PingFederate ist eine Identitätsbrücke für Unternehmen, die bei Bedarf als vollständiger Authentifizierungsanbieter oder Proxy für mehrere andere vorgelagerte Identitätsanbieter konfiguriert werden kann. Das folgende Diagramm veranschaulicht diese Fähigkeit.

![Abbildung der Implementierung von PingFederate](./media/partner-ping/pingfederate.png)

Mit dieser Fähigkeit kann eine eingehende Anforderung kontextbezogen/dynamisch oder deklarativ an eine bestimmte Azure AD B2C-Richtlinie weitergeleitet werden. Es folgt eine Darstellung des Protokollsequenz-Workflows bei dieser Konfiguration.

![Abbildung des PingAccess- und PingFederate-Workflows](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie noch kein Konto haben, können Sie eine [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), der mit Ihrem Azure-Abonnement verknüpft ist

- In Docker-Containern oder direkt in Azure-VMs bereitgestelltes PingAccess und PingFederate

## <a name="connectivity"></a>Konnektivität

Überprüfen Sie, ob die folgende Verbindung besteht.

- **PingAccess-Server**: kann mit dem PingFederate-Server, Clientbrowser, OIDC und OAuth (well-known) kommunizieren und Schlüssel ermitteln, die vom Azure AD B2C-Dienst und PingFederate-Server veröffentlicht werden.

- **PingFederate-Server**: kann mit dem PingAccess-Server, Clientbrowser, OIDC und OAuth (well-known) kommunizieren und Schlüssel ermitteln, die vom Azure AD B2C-Dienst veröffentlicht werden.

- **Legacy- oder headerbasierte AuthN-Anwendung**: kann mit dem PingAccess-Server kommunizieren.

- **SAML-Anwendung der vertrauenden Seite**: kann den vom Client stammenden Browserdatenverkehrs erreichen und auf die SAML-Verbundmetadaten zugreifen, die vom Azure AD B2C-Dienst veröffentlicht werden.

- **Moderne Anwendung**: kann den vom Client stammenden Browserdatenverkehr erreichen. Kann auf OIDC und OAuth (well-known) zugreifen sowie Schlüssel ermitteln, die vom Azure AD B2C-Dienst veröffentlicht werden.

- **Rest-API**: kann den von einem nativen oder Webclient stammenden Datenverkehr erreichen. Kann auf OIDC und OAuth (well-known) zugreifen sowie Schlüssel ermitteln, die vom Azure AD B2C-Dienst veröffentlicht werden.

## <a name="configure-azure-ad-b2c"></a>Konfigurieren von Azure AD B2C

Sie können dazu die einfachen Benutzerflows oder erweiterte IEF-Richtlinien (Identity Enterprise Framework) verwenden. PingAccess generiert den Metadatenendpunkt basierend auf dem Wert von **Issuer** unter Verwendung der auf [WebFinger](https://tools.ietf.org/html/rfc7033) basierenden Konvention für die Ermittlung.
Aktualisieren Sie zum Befolgen dieser Konvention den Azure AD B2C-Aussteller mithilfe der Richtlinieneigenschaften in Benutzerflows.

![Abbildung der Tokeneinstellungen](./media/partner-ping/token-setting.png)

In den erweiterten Richtlinien kann dies im [technischen Profil des Ausstellers des JWT-Token](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile) im Metadatenelement **IssuanceClaimPattern** auf den Wert **AuthorityWithTfp** festgelegt werden.

## <a name="configure-pingaccesspingfederate"></a>Konfigurieren von PingAccess und PingFederate

Im folgenden Abschnitt wird die erforderliche Konfiguration behandelt.
Das Diagramm veranschaulicht den allgemeinen Benutzerflow für die Integration.

![Abbildung der Integration von PingAccess und PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Konfigurieren von PingFederate als Tokenanbieter

Um PingFederate als Tokenanbieter für PingAccess zu konfigurieren, stellen Sie sicher, dass erst die Verbindung von PingFederate zu PingAccess und anschließend die Verbindung von PingAccess zu PingFederate hergestellt wird.  
Konfigurationsanweisungen finden Sie in [diesem Artikel](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html).

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Konfigurieren einer PingAccess-Anwendung für die headerbasierte Authentifizierung

Für die headerbasierte Authentifizierung muss für die Zielwebanwendung eine PingAccess-Anwendung erstellt werden. Führen Sie die folgenden Schritte aus.

#### <a name="step-1--create-a-virtual-host"></a>Schritt 1: Erstellen eines virtuellen Hosts

>[!IMPORTANT]
>Zum Konfigurieren dieser Lösung muss für jede Anwendung ein virtueller Host erstellt werden. Weitere Informationen zur Konfiguration und deren Auswirkungen finden Sie unter [Wichtige Aspekte](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Gehen Sie zum Erstellen eines virtuellen Hosts so vor:

1. Wechseln Sie zu **Settings** > **Access** > **Virtual Hosts** (Einstellungen > Zugriff > Virtuelle Hosts).

2. Wählen Sie **Add Virtual Host** (Virtuellen Host hinzufügen) aus.

3. Geben Sie im Feld „Host“ den vollqualifizierten Domänenname (FQDN) der Anwendungs-URL ein.

4. Geben Sie im Feld „Port“ **443** ein.

5. Wählen Sie **Speichern** aus.

#### <a name="step-2--create-a-web-session"></a>Schritt 2: Erstellen einer Websitzung

Führen Sie die folgenden Schritte zum Erstellen einer Websitzung aus:

1. Navigieren Sie zu **Settings** > **Access** > **Web Sessions** (Einstellungen > Zugriff > Websitzungen).

2. Wählen Sie **Add Web Session** (Websitzung hinzufügen) aus.

3. Geben Sie einen **Namen** für die Websitzung an.

4. Wählen Sie den **Cookie Type** (Cookietyp) aus: entweder **Signed JWT** (Signiertes JWT) oder **Encrypted JWT** (Verschlüsseltes JWT).

5. Geben Sie einen eindeutigen Wert für die **Audience** (Zielgruppe) an.

6. Geben Sie in das Feld **Client ID** (Client-ID) die **Azure AD Application ID** (Azure AD-Anwendungs-ID) ein.

7. Geben Sie in das Feld **Client Secret** (Clientgeheimnis) den **Schlüssel** ein, den Sie in Azure AD für die Anwendung generiert haben.

8. Optional: Sie können mithilfe der Microsoft Graph-API benutzerdefinierte Ansprüche erstellen und verwenden. Wenn Sie dies möchten, wählen Sie **Advanced** (Erweitert) aus, und deaktivieren Sie die Optionen **Request Profile** (Profil anfordern) und **Refresh User Attributes** (Benutzerattribute aktualisieren). Weitere Informationen zu benutzerdefinierten Ansprüchen finden Sie unter [Verwenden eines benutzerdefinierten Anspruchs](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#optional---use-a-custom-claim).

9. Wählen Sie **Speichern** aus.

#### <a name="step-3--create-identity-mapping"></a>Schritt 3: Erstellen der Identitätszuordnung

>[!NOTE]
>Die Identitätszuordnung kann mit mehreren Anwendungen verwendet werden, wenn mehr als eine Anwendung die gleichen Daten im Header erwartet.

Führen Sie zum Erstellen der Identitätszuordnung die folgenden Schritte aus:

1. Wechseln Sie zu **Settings** > **Access** > **Identity Mappings** (Einstellungen > Zugriff > Identitätszuordnungen).

2. Wählen Sie **Add Identity Mapping** (Identitätszuordnung hinzufügen) aus.

3. Geben Sie einen **Namen** an.

4. Wählen Sie die Identitätszuordnung **Type of Header Identity Mapping** (Typ der Identitätszuordnung im Header) aus.

5. Geben Sie in der Tabelle **Attribute-Mapping** (Attributzuordnungen) die erforderlichen Zuordnungen an. Beispiel:

   Attributname | Headername |
   |-------|--------|
   |upn | x-userprinciplename |
   |email   |    x-email  |
   |oid   | x-oid  |
   |scp   |     x-scope |
   |amr    |    x-amr    |

6. Wählen Sie **Speichern** aus.

#### <a name="step-4--create-a-site"></a>Schritt 4: Erstellen einer Website

>[!NOTE]
>Bei bestimmten Konfigurationen ist es möglich, dass eine Website mehr als eine Anwendung enthält. Eine Website kann ggf. durch mehrere Anwendungen genutzt werden.

Führen Sie zum Erstellen einer Website die folgenden Schritte aus:

1. Wechseln Sie zu **Main** > **Sites** (Hauptseite > Websites).

2. Wählen Sie **Add Site** (Website hinzufügen) aus.

3. Geben Sie einen **Namen** für die Website an.

4. Geben Sie das **Target** (Ziel) der Website ein. Das Ziel ist das Paar Hostname:Port für den Server, der die Anwendung hostet. Geben Sie in dieses Feld nicht den Pfad der Anwendung ein. Eine Anwendung unter https://mysite:9999/AppName hat z. B. den Zielwert mysite: 9999

5. Geben Sie an, ob das Ziel sichere Verbindungen erwartet.

6. Wenn das Ziel sichere Verbindungen erwartet, legen Sie „Trusted Certificate Group“ (Vertrauenswürdige Zertifikatsgruppe) auf **Trust Any** (Allen vertrauen) fest.

7. Wählen Sie **Speichern** aus.

#### <a name="step-5--create-an-application"></a>Schritt 5: Erstellen einer Anwendung

Gehen Sie wie folgt vor, um für jede Anwendung in Azure, die Sie schützen möchten, eine Anwendung in PingAccess zu erstellen.

1. Wechseln Sie zu **Main** > **Applications** (Hauptseite > Anwendungen).

2. Wählen Sie **Add Application** (Anwendung hinzufügen) aus.

3. Geben Sie einen **Namen** für die Anwendung an.

4. Geben Sie optional in **Description** eine Beschreibung der Anwendung ein.

5. Geben Sie den **Context Root**  (Kontextstamm) der Anwendung an. Eine Anwendung unter https://mysite:9999/AppName hat z. B. den Kontextstamm /AppName. Der Kontextstamm muss mit einem Schrägstrich (/) beginnen, darf nicht mit einem Schrägstrich (/) enden und kann mehrere Ebenen aufweisen, z. B. /Apps/MyApp.

6. Wählen Sie den **virtuellen Host** aus, den Sie erstellt haben.

   >[!NOTE]
   >Die Kombination aus virtuellem Host und Kontextstamm muss in PingAccess eindeutig sein.

7. Wählen Sie die erstellte **Websitzung** aus.

8. Wählen Sie die von Ihnen erstellte **Website** mit der Anwendung aus.

9. Wählen Sie die von Ihnen erstellte **Identitätszuordnung** aus.

10. Wählen Sie **Enabled** (Aktiviert) aus, um die Website beim Speichern zu aktivieren.

11. Wählen Sie **Speichern** aus.

### <a name="configure-the-pingfederate-authentication-policy"></a>Konfigurieren der PingFederate-Authentifizierungsrichtlinie

Konfigurieren Sie die PingFederate-Authentifizierungsrichtlinie für den Verbund mit den verschiedenen Identitätsanbietern, die von den Azure AD B2C-Mandanten bereitgestellt werden.

1. Richten Sie einen Vertrag zur Überbrückung der Attribute zwischen den Identitätsanbietern und dem Dienstanbieter ein. Weitere Informationen finden Sie unter [Federation hub and authentication policy contracts](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html) (Verträge für Verbundhubs und Authentifizierungsrichtlinien). Sie benötigen wahrscheinlich nur einen Vertrag, es sei denn, der Dienstanbieter verlangt von jedem Identitätsanbieter einen anderen Attributsatz.

2. Erstellen Sie für jeden Identitätsanbieter eine Verbindung zwischen Identitätsanbieter und PingFederate mit dem Dienstanbieter als Verbundhub.

3. Fügen Sie im Fenster **Target Session Mapping** (Zielsitzungszuordnung) die betreffenden Authentifizierungsrichtlinienverträge für die Verbindung mit dem Identitätsanbieter hinzu.

4. Konfigurieren Sie im Fenster **Selectors** (Selektoren) einen Authentifizierungsselektor. Sehen Sie sich zum Beispiel eine Instanz des **Identifier First Adapter** an, um jeden Identitätsanbieter der entsprechenden Verbindung in einer Authentifizierungsrichtlinie zuzuordnen.

5. Richten Sie eine Dienstanbieterverbindung zwischen PingFederate, dem Verbundhub als Identitätsanbieter und dem Dienstanbieter ein.

6. Fügen Sie im Fenster **Authentication Source Mapping** (Zuordnung der Authentifizierungsquelle) den entsprechenden Authentifizierungsrichtlinienvertrag der Dienstanbieterverbindung hinzu.

7. Arbeiten Sie mit den einzelnen Identitätsanbieter zusammen, um eine Verbindung mit PingFederate mit dem Verbundhub als Dienstanbieter herzustellen.

8. Arbeiten Sie mit dem Dienstanbieter zusammen, um eine Verbindung mit PingFederate mit dem Verbundhub als Identitätsanbieter herzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
