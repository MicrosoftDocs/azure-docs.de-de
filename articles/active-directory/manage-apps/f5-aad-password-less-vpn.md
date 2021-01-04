---
title: Sicherer Hybridzugriff mit Azure AD und F5-VPN | Microsoft-Dokumentation
description: Tutorial für die Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit F5 BIG-IP für ein VPN ohne Kennwort
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2961f3f01f6ea4398fab6144b34fcb4409cdd96f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317668"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Tutorial für die Integration des einmaligen Anmeldens von Azure Active Directory mit F5 BIG-IP für ein VPN ohne Kennwort

In diesem Tutorial erfahren Sie, wie Sie die auf F5 BIG-IP basierende SSL-VPN-Lösung (Secure Socket Layer, virtuelles privates Netzwerk) mit Azure Active Directory (AD) für den sicheren Hybridzugriff (Secure Hybrid Access, SHA) integrieren.

Die Integration eines BIG-IP-SSL-VPN mit Azure AD bietet [viele wichtige Vorteile](f5-aad-integration.md), einschließlich:

- Verbesserte Zero Trust-Governance durch die [Vorauthentifizierung und Autorisierung durch Azure AD](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)

- [Kennwortlose Authentifizierung beim VPN-Dienst](https://www.microsoft.com/security/business/identity/passwordless)

- Verwalten der Identitäten und des Zugriffs über eine zentrale Steuerungsebene: das [Azure-Portal](https://portal.azure.com/#home)

Trotz dieser klaren Vorteile gilt für das klassische VPN weiterhin das Konzept eines Netzwerkperimeters: Intern ist vertrauenswürdig, extern ist nicht vertrauenswürdig. Dieses Modell kann für Zero Trust-Vertrauensstellungen nicht mehr angewandt werden, da sich Unternehmensressourcen nicht mehr nur in Unternehmensrechenzentren befinden, sondern in Umgebungen mit mehreren Clouds und ohne feste Grenzen. Aus diesem Grund empfehlen wir unseren Kunden einen identitätsbasierten Ansatz, bei dem der [Zugriff auf Anwendungsbasis](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad) verwaltet wird.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Szenario wird die BIG-IP APM-Instanz des SSL-VPN-Diensts als SAML-Dienstanbieter (SP) konfiguriert, während Azure AD zum vertrauenswürdigen SAML-IDP wird, der die Vorauthentifizierung bereitstellt. Das einmalige Anmelden (Single Sign-on, SSO) von Azure AD wird dann durch eine anspruchsbasierte Authentifizierung beim BIG-IP APM bereitgestellt, sodass insgesamt ein nahtloser VPN-Zugriff ermöglicht wird.

![Abbildung der SSL-VPN-Architektur](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Alle Beispielzeichenfolgen und -werte in diesem Leitfaden sollten durch die tatsächlichen Werte für Ihre Umgebung ersetzt werden.

## <a name="prerequisites"></a>Voraussetzungen

Kenntnisse über F5 BIG-IP sind nicht erforderlich. Sie benötigen jedoch Folgendes:

- Eine [kostenloses Abonnement](https://azure.microsoft.com/trial/get-started-active-directory/) (oder höher) von Azure AD

- Benutzeridentitäten sollten [aus dem lokalen Verzeichnis](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) mit Azure AD synchronisiert werden.

- Ein Konto mit den [Berechtigungen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) eines Azure AD-Anwendungsadministrators

- Eine vorhandene BIG-IP-Infrastruktur, in der Clientdatenverkehr zu und von BIG-IP weitergeleitet wird, oder eine [Bereitstellung einer BIG-IP Virtual Edition in Azure](f5-bigip-deployment-guide.md)

- Ein Datensatz für den veröffentlichten BIG-IP-VPN-Dienst muss beim Testen im öffentlichen DNS oder in der Localhost-Datei des Clients vorhanden sein.

- BIG-IP sollte mit den erforderlichen SSL-Zertifikaten für die Veröffentlichung von Diensten über HTTPS bereitgestellt werden

Sie sollten sich auch mit der [F5 BIG-IP-Terminologie](https://www.f5.com/services/resources/glossary) vertraut machen, damit Sie die verschiedenen Komponenten besser verstehen, auf die im Tutorial verwiesen wird.

>[!NOTE]
>Azure wird ständig weiterentwickelt. Lassen Sie sich daher nicht verwirren, wenn zwischen den Anweisungen in diesem Leitfaden und dem Azure-Portal Unterschiede auftreten. Die Screenshots stammen aus BIG-IP v15, stimmen aber größtenteils mit v13.1 überein.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Hinzufügen von F5 BIG-IP aus dem Azure AD-Anwendungskatalog

Das Einrichten einer Vertrauensstellung für den SAML-Verbund zwischen BIG-IP und Azure AD ermöglicht BIG-IP das Übergeben der Vorauthentifizierung und des [bedingten Zugriffs](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) an Azure AD, bevor der Zugriff auf den veröffentlichten VPN-Dienst erteilt wird.

1. Melden Sie sich beim Azure AD-Portal mit einem Konto mit Administratorrechten für die Anwendung an.

2. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.

3. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie auf dem oberen Menüband **Neue Anwendung** aus.

4. Suchen Sie im Katalog nach F5, und wählen Sie die **Azure AD-Integration für F5 BIG-IP APM** aus.

5. Geben Sie einen Namen für die Anwendung an, und wählen Sie dann **Hinzufügen/Erstellen** aus, um sie Ihrem Mandanten hinzuzufügen. Den Benutzern wird der Name in den Anwendungsportalen von Azure und Office 365 als Symbol angezeigt. Der Name sollte den jeweiligen Dienst widerspiegeln, z. B. VPN.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

1. Navigieren Sie in der Ansicht der Eigenschaften der neuen F5-Anwendung zu **Manage** > **Single sign-on** (Verwalten > Einmaliges Anmelden).

2. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus. Überspringen Sie die Aufforderung zum Speichern der Einstellungen für das einmalige Anmelden, indem Sie **Später speichern** auswählen.

3. Wählen Sie im Menü **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die folgenden Informationen anzugeben:

   - Ersetzen Sie die vordefinierte **Bezeichner-URL** durch die URL für Ihren veröffentlichten BIG-IP-Dienst. Beispiel: `https://ssl-vpn.contoso.com`

   - Gehen Sie im Textfeld **Antwort-URL** gleichartig vor (einschließlich SAML-Endpunktpfad). Beispiel: `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - Nur in dieser Konfiguration arbeitet die Anwendung in einem vom IDP initiierten Modus, wobei Azure AD dem Benutzer vor der Umleitung an den BIG-IP-SAML-Dienst eine SAML-Assertion ausstellt. Geben Sie für Apps, die den durch den IDP initiierten Modus nicht unterstützen, die **Anmelde-URL** für den BIG-IP-SAML-Dienst an. Beispiel: `https://ssl-vpn.contoso.com`.

   - Geben Sie für die Abmelde-URL den SLO-Endpunkt (Single logout, einmaliges Abmelden) vom BIG-IP APM ein, dem der Hostheader des veröffentlichten Diensts vorangestellt ist. Beispiel: `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Durch die Bereitstellung einer SLO-URL wird sichergestellt, dass eine Benutzersitzung an beiden Enden – bei BIG-IP und in Azure AD – beendet wird. Der BIG-IP APM bietet auch eine [Option](https://support.f5.com/csp/article/K12056) zum Beenden aller Sitzungen, wenn eine bestimmte Anwendungs-URL aufgerufen wird.

![Abbildung der grundlegenden SAML-Konfiguration](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>In TMOS v16 wurde der SAML-SLO-Endpunkt in „/saml/sp/profile/redirect/slo“ geändert.

4. Wählen Sie vor dem Beenden des SAML-Konfigurationsmenüs **Speichern** aus, und überspringen Sie die SSO-Testaufforderung.

Beachten Sie die Eigenschaften im Abschnitt **Benutzerattribute und Ansprüche**, da Azure AD diese für die BIG-IP APM-Authentifizierung an die Benutzer ausgibt.

![Abbildung der Benutzerattribute und Ansprüche](media/f5-sso-vpn/user-attributes-claims.png)

Sie können auch andere spezifische Ansprüche hinzufügen, die Ihr veröffentlichter BIG-IP-Dienst eventuell erwartet. Beachten Sie dabei aber, dass alle zusätzlich zum Standardsatz definierten Ansprüche nur ausgegeben werden, wenn sie in Azure AD als ausgefüllte Attribute vorhanden sind. Ebenso müssen Mitgliedschaften von [Rollen oder Gruppen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) im Verzeichnis auch für ein Benutzerobjekt in Azure AD definiert werden, bevor sie als Anspruch ausgestellt werden können.

![Abbildung des Downloadlinks für Verbundmetadaten](media/f5-sso-vpn/saml-signing-certificate.png)

SAML-Signaturzertifikate, die von Azure AD erstellt wurden, haben eine Lebensdauer von drei Jahren. Sie müssen daher gemäß den für Azure AD veröffentlichten Leitfäden verwaltet werden.

### <a name="azure-ad-authorization"></a>Azure AD-Autorisierung

Standardmäßig gibt Azure AD nur Token für Benutzer aus, denen der Zugriff auf einen Dienst gewährt wurde.

1. Wählen Sie in der Konfigurationsansicht der Anwendung die Option **Benutzer und Gruppen** aus.

2. Wählen Sie **+ Benutzer hinzufügen** und anschließend im Menü „Zuweisung hinzufügen“ die Option **Benutzer und Gruppen** aus.

3. Fügen Sie im Dialogfeld **Benutzer und Gruppen** die Gruppen von Benutzern hinzu, die für den Zugriff auf das VPN autorisiert sind. Wählen Sie danach **Auswählen** > **Zuweisen** aus.

![Abbildung des Hinzufügens eines Benutzerlinks ](media/f5-sso-vpn/add-user-link.png)

4. Damit ist der Teil der SAML-Verbundvertrauensstellung in Azure AD abgeschlossen. Der BIG-IP APM kann nun so eingerichtet werden, dass der SSL-VPN-Dienst veröffentlicht und mit den entsprechenden Eigenschaften konfiguriert wird, um die SAML-Vorauthentifizierung abzuschließen.

## <a name="big-ip-apm-configuration"></a>Konfigurieren des BIG-IP APM

### <a name="saml-federation"></a>SAML-Verbund

Im folgenden Abschnitt werden der BIG-IP-SAML-Dienstanbieter und die zugehörigen SAML-IDP-Objekte erstellt, um den Verbund zwischen VPN-Dienst und Azure AD abzuschließen.

1. Navigieren Sie zu **Access** > **Federation** > **SAML Service Provider** > **Local SP Services** (Zugriff > Verbund > SAML-Dienstanbieter > Lokale SP-Dienste), und wählen Sie **Create** (Erstellen) aus.

![Abbildung der BIG-IP-SAML-Konfiguration](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Geben Sie einen **Namen** und den Wert für die **Entitäts-ID** ein, den Sie zuvor in Azure AD definiert haben. Geben Sie außerdem den vollqualifizierten Domänennamen (FQDN) des Hosts an, der zum Herstellen einer Verbindung mit der Anwendung verwendet wird.

![Abbildung der Erstellung eines neuen SAML-SP-Diensts](media/f5-sso-vpn/create-new-saml-sp.png)

   Die Einstellungen für den SP-**Namen** sind nur erforderlich, wenn die Entitäts-ID nicht exakt mit dem Hostnamensteil der veröffentlichten URL übereinstimmt oder wenn kein reguläres Format für die Hostnamen-URL verwendet wird. Geben Sie das externe Schema und den Hostnamen der zu veröffentlichenden Anwendung an, wenn die Entitäts-ID `urn:ssl-vpn:contosoonline` ist.

3. Scrollen Sie nach unten zum neuen **SAML-SP-Objekt**, und wählen Sie **Bind/UnBind IDP Connectors** (IDP-Connectors binden/trennen) aus.

![Abbildung der Erstellung eines Verbunds mit einem lokalen SP-Dienst](media/f5-sso-vpn/federation-local-sp-service.png)

4. Wählen Sie **Create New IDP Connector** (Neuen IDP-Connector erstellen) und dann im Dropdownmenü den Eintrag **From Metadata** (Aus Metadaten) aus.

![Abbildung der Erstellung eines neuen IDP-Connectors](media/f5-sso-vpn/create-new-idp-connector.png)

5. Navigieren Sie zur XML-Datei mit den Verbundmetadaten, die Sie zuvor heruntergeladen haben, und geben Sie für das APM-Objekt, das den externen SAML-IDP darstellt, einen **Identity Provider Name** (Identitätsanbietername) an.

6. Wählen Sie **Add New Row** (Neue Zeile hinzufügen) aus, um den neuen externen IDP-Connector für Azure AD auszuwählen.

![Abbildung des externen IDP-Connectors](media/f5-sso-vpn/external-idp-connector.png)

7. Wählen Sie **Update** aus, um das SAML-SP-Objekt an das SAML-IDP-Objekt zu binden, und wählen Sie dann **OK** aus.

![Abbildung des SAML-IDP mit SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop-Konfiguration

Mithilfe der folgenden Schritte kann das SSL-VPN Benutzern über das proprietäre Webportal von BIG-IP angeboten werden.

1. Navigieren Sie zu **Access** > **Webtops** > **Webtop Lists** (Zugriff > Webtops > Webtop-Listen), und wählen Sie **Create** (Erstellen) aus.

2. Weisen Sie dem Portal einen Namen zu, und legen Sie den Typ auf **Full** (Vollständig) fest. Beispiel: `Contoso_webtop`.

3. Passen Sie die verbleibenden Einstellungen an, und wählen Sie dann **Finished** (Fertig) aus.

![Abbildung der Webtop-Konfiguration](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN-Konfiguration

Die VPN-Funktion umfasst mehrere Elemente, die jeweils einen anderen Aspekt des gesamten Diensts steuern.

1. Navigieren Sie zu **Access** > **Connectivity/VPN** > **Network Access (VPN)**  > **IPV4 Lease Pools** (Zugriff > Konnektivität/VPN > Netzwerkzugriff (VPN) > IPv4-Leasepools), und wählen Sie **Create** (Erstellen) aus.

2. Geben Sie dem Pool von IP-Adressen, die VPN-Clients zugewiesen werden, einen Namen, z. B. „Contoso_vpn_pool“.

3. Legen Sie den Typ auf **IP Address Range** (IP-Adressbereich) fest, und geben Sie eine Start- und eine End-IP-Adresse an. Wählen Sie anschließend **Add** (Hinzufügen) und dann **Finished** (Fertig) aus.

![Abbildung der VPN-Konfiguration](media/f5-sso-vpn/vpn-configuration.png)

Mit einer Netzwerkzugriffsliste wird der Dienst mit IP- und DNS-Einstellungen aus dem VPN-Pool und Benutzerroutingberechtigungen bereitgestellt, und bei Bedarf werden auch Anwendungen gestartet.

1. Navigieren Sie zu **Access** > **Connectivity/VPN: Network Access (VPN)**  > **Network Access Lists** (Zugriff > Konnektivität/VPN: Netzwerkzugriff > Netzwerkzugriffslisten), und wählen Sie **Create** (Erstellen) aus.

2. Geben Sie einen Namen für die VPN-Zugriffsliste und eine Beschriftung an, z. B. „Contoso-VPN“. Wählen Sie danach **Finished** (Fertig) aus.

![Abbildung der VPN-Konfiguration in einer Netzwerkzugriffsliste](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Wählen Sie auf dem oberen Menüband die Option **Network Settings** (Netzwerkeinstellungen) aus, und fügen Sie die folgenden Einstellungen hinzu:

   • **Supported IP version** (Unterstützte IP-Version): IPV4

   • **IPV4 Lease Pool** (IPv4-Leasepool): Wählen Sie den zuvor erstellten VPN-Pool aus, z. B. „Contoso_vpn_pool“.

![Abbildung des Contoso-VPN-Pools](media/f5-sso-vpn/contoso-vpn-pool.png)

   Mithilfe der Optionen für Clienteinstellungen können Sie Einschränkungen für die Weiterleitung von Clientdatenverkehr erzwingen, wenn ein VPN eingerichtet wird.

4. Wählen Sie **Finished** (Fertig) aus, wechseln Sie zur Registerkarte „DNS/Hosts“, und geben Sie die folgenden Einstellungen an:

   • **IPV4 Primary Name Server** (Primärer IPv4-Namenserver): IP-Adresse des DNS-Servers Ihrer Umgebung

   • **DNS Default Domain Suffix** (DNS-Standarddomänensuffix): Das Domänen Suffix für diese VPN-Verbindung, z. B. „contoso.com“.

![Abbildung des Standarddomänensuffix](media/f5-sso-vpn/domain-suffix.png)

Im [Artikel zu F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) finden Sie Einzelheiten zum Anpassen der übrigen Einstellungen an Ihre Anforderungen.

Nun ist ein BIG-IP-Verbindungsprofil erforderlich, um die Einstellungen für jeden der VPN-Clienttypen zu konfigurieren, die der VPN-Dienst unterstützen soll. Dies sind beispielsweise Windows, OS X und Android.

1. Navigieren Sie zu **Access** > **Connectivity/VPN** > **Connectivity** > **Profiles** (Zugriff > Konnektivität/VPN > Konnektivität > Profile), und wählen Sie **Add** (Hinzufügen) aus.

2. Geben Sie einen Profilnamen an, und legen Sie das übergeordnete Profil auf **/Common/connectivity** fest, z. B. „Contoso_VPN_Profile“.

![Abbildung der Erstellung des neuen Konnektivitätsprofils](media/f5-sso-vpn/create-connectivity-profile.png)

Die [Dokumentation](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) von F5 enthält weitere Details zur Clientunterstützung.

## <a name="access-profile-configuration"></a>Konfigurieren von Zugriffsprofilen

Nachdem nun die VPN-Objekte konfiguriert sind, ist eine Zugriffsrichtlinie erforderlich, um den Dienst für die SAML-Authentifizierung zu aktivieren.

1. Navigieren Sie zu **Access** > **Profiles/Policies** > **Access Profiles (Per-Session Policies)** (Zugriff > Profile/Richtlinien > Zugriffsprofile (Sitzungsrichtlinien)), und wählen Sie **Create** (Erstellen) aus.

2. Geben Sie einen Profilnamen an, und wählen Sie als Profiltyp **All** aus, z. B. „Contoso_network_access“.

3. Scrollen Sie nach unten, fügen Sie der Liste **Accepted Languages** (Akzeptierte Sprachen) mindestens eine Sprache hinzu, und wählen Sie **Finished** (Fertig) aus.

![Abbildung der allgemeinen Eigenschaften](media/f5-sso-vpn/general-properties.png)

4. Wählen Sie im Feld „Per-Session Policy“ (Sitzungsrichtlinie) des neuen Zugriffsprofils **Edit** (Bearbeiten) aus. Damit starten Sie den visuellen Richtlinien-Editor in einer separaten Registerkarte des Browsers.

![Abbildung der Sitzungsrichtlinie](media/f5-sso-vpn/per-session-policy.png)

5. Wählen Sie das Pluszeichen ( **+** ) und dann im Popupelement **Authentication** > **SAML Auth** > **Add Item** (Authentifizierung > SAML-Authentifizierung > Element hinzufügen) aus.

6. Wählen Sie in der Konfiguration für den SAML-Authentifizierungs-SP das zuvor erstellte VPN-SAML-SP-Objekt und dann **Save** (Speichern) aus.

![Abbildung der SAML-Authentifizierung](media/f5-sso-vpn/saml-authentication.png)

7. Wählen Sie in der erfolgreichen Verzweigung der SAML-Authentifizierung das Pluszeichen ( **+** ) aus.

8. Wählen Sie auf der Registerkarte „Assignment“ (Zuweisung) die Option **Advanced Resource Assign** (Erweiterte Ressourcenzuweisung) und dann **Add Item** (Element hinzufügen) aus.

![Abbildung der erweiterten Ressourcenzuweisung](media/f5-sso-vpn/advance-resource-assign.png)

9. Wählen Sie im Popupelement die Option **New Entry** (Neuer Eintrag) und dann **Add/Delete** (Hinzufügen/Löschen) aus.

10. Wählen Sie im untergeordneten Fenster **Network Access** (Netzwerkzugriff) und dann das zuvor erstellte Netzwerkzugriffsprofil aus.

![Abbildung des Hinzufügens eines neuen Netzwerkzugriffseintrags](media/f5-sso-vpn/add-new-entry.png)

11. Wechseln Sie zur Registerkarte **Webtop**, und fügen Sie das zuvor erstellte Webtop-Objekt hinzu.

![Abbildung des Hinzufügens des Webtop-Objekts](media/f5-sso-vpn/add-webtop-object.png)

12. Wählen Sie **Update** und dann **Save** (Speichern) aus.

13. Wählen Sie den oberen Link „Deny“ (Verweigern) aus, um die erfolgreiche Verzweigung in **Allow** (Zulassen) zu ändern, und wählen Sie dann **Save** (Speichern) aus.

![Abbildung des neuen visuellen Richtlinien-Editors](media/f5-sso-vpn/vizual-policy-editor.png)

14. Committen Sie diese Einstellungen, indem Sie **Apply Access Policy** (Zugriffsrichtlinie anwenden) auswählen, und schließen Sie dann die Registerkarte mit dem visuellen Richtlinien-Editor.

![Abbildung des neuen Access Policy Manager](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Veröffentlichen des VPN-Diensts

Wenn alle Einstellungen vorgenommen wurden, benötigt der APM einen virtuellen Front-End-Server, um auf Clients zu lauschen, die eine Verbindung mit dem VPN herstellen.

1. Wählen Sie **Local Traffic** > **Virtual Servers** > **Virtual Server List** (Lokaler Datenverkehr > Virtuelle Server > Liste virtueller Server) und dann **Create** (Erstellen) aus.

2. Geben Sie einen **Namen** für den virtuellen VPN-Server an, z. B. **VPN_Listener**.

3. Stellen Sie dem virtuellen Server eine noch nicht verwendete **IP Destination Address** (IP-Zieladresse) bereit, die über Routing Clientdatenverkehr empfangen kann.

4. Legen Sie den Dienstport auf **443 HTTPS** fest, und stellen Sie sicher, dass als Status **Enabled** (Aktiviert) angezeigt wird.

![Abbildung des neuen virtuellen Servers](media/f5-sso-vpn/new-virtual-server.png)

5. Legen Sie das **HTTP-Profil** auf „http“ fest, und fügen Sie das SSL-Profil (Client) für das öffentliche SSL-Zertifikat hinzu, das Sie als Teil der Voraussetzungen bereitgestellt haben.

![Abbildung des SSL-Profils](media/f5-sso-vpn/ssl-profile.png)

6. Legen Sie unter „Access Policy“ (Zugriffsrichtlinie) das **Access Profile** (Zugriffsprofil) und das **Connectivity Profile** (Konnektivitätsprofil) auf die erstellten VPN-Objekte fest.

![Abbildung der Zugriffsrichtlinie](media/f5-sso-vpn/access-policy.png)

7. Wählen Sie **Finished** (Fertig) aus, wenn Sie alles erledigt haben.

8.  Der SSL-VPN-Dienst ist damit veröffentlicht und über den sicheren Hybridzugriff (SHA) erreichbar. Dazu kann direkt die URL oder eines der Anwendungsportale von Microsoft verwendet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Das Ende der Kennwörter: Umsteigen auf die kennwortlose Anmeldung](https://www.microsoft.com/security/business/identity/passwordless)

- [Was ist bedingter Zugriff?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [„Zero Trust“-Framework von Microsoft zum Ermöglichen von Remotearbeit](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Fünf Schritte zum Integrieren Ihrer gesamten Apps in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)

## <a name="next-steps"></a>Nächste Schritte

Öffnen Sie einen Browser auf einem Windows-Remoteclient, und browsen Sie zur URL des **BIG-IP-VPN-Diensts**. Nachdem Sie sich bei Azure AD authentifiziert haben, werden das BIG-IP-Webtop-Portal und die VPN-Startseite angezeigt.

![Abbildung der VPN-Startseite](media/f5-sso-vpn/vpn-launcher.png)

Wenn Sie die VPN-Kachel auswählen, wird der BIG-IP-Edgeclient installiert und eine für SHA konfigurierte VPN-Verbindung eingerichtet.
Die F5-VPN-Anwendung sollte ebenfalls als Zielressource für den bedingten Zugriff in Azure AD angezeigt werden. Weitere Informationen zum Erstellen von Richtlinien für den bedingten Zugriff und zum Aktivieren von Benutzern für die [kennwortlose Authentifizierung](https://www.microsoft.com/security/business/identity/passwordless) mit Azure AD finden Sie in unserem [Leitfaden](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies).