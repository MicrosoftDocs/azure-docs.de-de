---
title: Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy
description: Es wird beschrieben, wie Sie benutzerdefinierte Domänen im Azure AD-Anwendungsproxy konfigurieren und verwalten.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bef120e754c84798b2d1b48f4f00fbb8f5fb3c1d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997612"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurieren von benutzerdefinierten Domänen per Azure AD-Anwendungsproxy

Wenn Sie eine Anwendung über den Azure Active Directory-Anwendungsproxy veröffentlichen, erstellen Sie eine externe URL für Ihre Benutzer. Mit dieser URL wird die Standarddomäne *yourtenant.msappproxy.net* aufgerufen. Wenn Sie beispielsweise eine App mit dem Namen *Expenses* veröffentlichen und Ihr Mandant den Namen *Contoso* hat, lautet die externe URL „*https:\//expenses-contoso.msappproxy.net*“. Falls Sie anstelle von *msappproxy.net* Ihren eigenen Domänennamen verwenden möchten, können Sie für Ihre Anwendung eine benutzerdefinierte Domäne konfigurieren. 

## <a name="benefits-of-custom-domains"></a>Vorteile von benutzerdefinierten Domänen

Es empfiehlt sich, nach Möglichkeit immer benutzerdefinierte Domänen für Ihre Apps einzurichten. Gründe für die Verwendung von benutzerdefinierten Domänen sind:

- Links zwischen Apps funktionieren auch außerhalb des Unternehmensnetzwerks. Wenn Ihre App über hartcodierte interne Links zu Zielen verfügt, die sich außerhalb des Anwendungsproxys befinden, und die Links extern nicht aufgelöst werden können, tritt ohne benutzerdefinierte Domäne ein Fehler auf. Dieses Problem wird vermieden, wenn Ihre internen und externen URLs identisch sind. Falls Sie keine benutzerdefinierten Domänen verwenden können, helfen Ihnen die Informationen zu anderen Problemlösungen unter [Umleiten von hartcodierten Links für Apps, die mit Azure AD-Anwendungsproxy veröffentlicht wurden](./application-proxy-configure-hard-coded-link-translation.md) weiter. 
  
- Ihre Benutzer haben es einfacher, weil sie innerhalb und außerhalb Ihres Netzwerks über die gleiche URL auf die App zugreifen können. Sie müssen sich nicht unterschiedliche interne und externe URLs merken und auch ihren aktuellen Standort nicht nachverfolgen. 

- Sie selbst können Ihr Branding steuern und die gewünschten URLs erstellen. Bei Verwendung einer benutzerdefinierten Domäne fühlen Ihre Benutzer sich sicherer, weil nicht *msappproxy.net* angezeigt wird, sondern ein vertrauter Name verwendet werden kann.

- Einige Konfigurationen funktionieren nur mit benutzerdefinierten Domänen. Sie benötigen beispielsweise benutzerdefinierte Domänen für Apps, für die die Security Assertion Markup Language (SAML) verwendet wird. Dies ist zum Beispiel der Fall, wenn Sie Active Directory-Verbunddienste (AD FS) nutzen, aber die Verwendung des WS-Verbunds nicht möglich ist. Weitere Informationen finden Sie unter [Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy](application-proxy-configure-for-claims-aware-applications.md). 

Falls die Anpassung der internen und externen URLs für Sie nicht möglich sein sollte, ist die Nutzung von benutzerdefinierten Domänen nicht so wichtig, aber Sie können trotzdem von den anderen Vorteilen profitieren. 

## <a name="dns-configuration-options"></a>DNS-Konfigurationsoptionen

Es gibt je nach Ihren Anforderungen mehrere Optionen zum Einrichten Ihrer DNS-Konfiguration:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Gleiche interne und externe URL, unterschiedliches internes und externes Verhalten 

Falls Sie nicht möchten, dass Ihre internen Benutzer über den Anwendungsproxy geleitet werden, können Sie ein *Split-Brain-DNS* einrichten. Bei einer geteilten DNS-Infrastruktur werden interne Hosts zur Namensauflösung an einen internen Domänennamenserver und externe Hosts an einen externen Domänennamenserver geleitet. 

![Split-Brain-DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Unterschiedliche interne und externe URLs 

Wenn sich die internen und externen URLs unterscheiden, müssen Sie kein Split-Brain-Verhalten konfigurieren, weil die Benutzerweiterleitung von der URL bestimmt wird. In diesem Fall ändern Sie nur das externe DNS und leiten die externe URL an den Endpunkt des Anwendungsproxys weiter. 

Bei Auswahl einer benutzerdefinierten Domäne für eine externe URL wird in einer Informationsleiste der CNAME-Eintrag angezeigt, den Sie dem externen DNS-Anbieter hinzufügen müssen. Sie können diese Informationen jederzeit anzeigen, indem Sie in der App auf die Seite **Anwendungsproxy** navigieren.

## <a name="set-up-and-use-custom-domains"></a>Einrichten und Verwenden von benutzerdefinierten Domänen

Zum Konfigurieren einer lokalen App für die Verwendung einer benutzerdefinierten Domäne benötigen Sie eine verifizierte benutzerdefinierte Azure Active Directory-Domäne, ein PFX-Zertifikat für die benutzerdefinierte Domäne und eine lokale App für die Konfiguration. 

### <a name="create-and-verify-a-custom-domain"></a>Erstellen und Überprüfen einer benutzerdefinierten Domäne

Erstellen und überprüfen Sie eine benutzerdefinierte Domäne wie folgt:

1. Wählen Sie in Azure Active Directory im linken Navigationsbereich die Option **Namen der benutzerdefinierten Domänen** und dann **Benutzerdefinierte Domäne hinzufügen** aus. 
1. Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein, und wählen Sie **Domäne hinzufügen** aus. 
1. Kopieren Sie auf der Domänenseite die Informationen zum TXT-Eintrag Ihrer Domäne. 
1. Navigieren Sie zu Ihrer Domänenregistrierungsstelle, und erstellen Sie basierend auf den kopierten DNS-Informationen einen neuen TXT-Eintrag für Ihre Domäne.
1. Wählen Sie nach dem Registrieren der Domäne auf der entsprechenden Seite in Azure Active Directory die Option **Überprüfen** aus. Wenn der Domänenstatus **Verifiziert** lautet, können Sie die Domäne in Ihren gesamten Azure AD-Konfigurationen verwenden (einschließlich Anwendungsproxy). 

Eine ausführlichere Anleitung finden Sie unter [Hinzufügen Ihres benutzerdefinierten Domänennamens über das Azure Active Directory-Portal](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurieren einer App für die Verwendung einer benutzerdefinierten Domäne

Veröffentlichen Sie Ihre App wie folgt per Anwendungsproxy mit einer benutzerdefinierten Domäne:

1. Wählen Sie für eine neue App in Azure Active Directory im linken Navigationsbereich die Option **Unternehmensanwendungen** aus. Wählen Sie **Neue Anwendung** aus. Wählen Sie im Abschnitt **Lokale Anwendungen** die Option **Lokale Anwendung hinzufügen** aus. 
   
   Wählen Sie für eine App, die sich bereits unter **Unternehmensanwendungen** befindet, die App in der Liste und dann im linken Navigationsbereich die Option **Anwendungsproxy** aus. 

2. Geben Sie auf der Seite der Anwendungsproxyeinstellungen einen **Namen** ein, wenn Sie Ihre eigene lokale Anwendung hinzufügen.

3.  Geben Sie im Feld **Interne URL** die interne URL für Ihre App ein.
   
4. Öffnen Sie im Feld **Externe URL** die Dropdownliste, und wählen Sie die gewünschte benutzerdefinierte Domäne aus.
   
5. Wählen Sie **Hinzufügen**.
   
   ![Auswählen der benutzerdefinierten Domäne](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Wenn die Domäne bereits über ein Zertifikat verfügt, werden im Feld **Zertifikat** die Zertifikatinformationen angezeigt. Wählen Sie andernfalls das Feld **Zertifikat** aus.
   
   ![Klicken zum Hochladen eines Zertifikats](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Navigieren Sie auf der Seite **SSL-Zertifikat** zur PFX-Zertifikatsdatei, und wählen Sie sie aus. Geben Sie das Kennwort für das Zertifikat ein, und wählen Sie **Zertifikat hochladen** aus. Weitere Informationen zu Zertifikaten finden Sie im Abschnitt [Zertifikate für benutzerdefinierte Domänen](#certificates-for-custom-domains). Wenn das Zertifikat nicht gültig ist oder ein anderes Problem mit dem Kennwort besteht, wird eine Fehlermeldung angezeigt. Die [Anwendungsproxy-FAQ](application-proxy-faq.md#application-configuration) enthalten einige Problembehandlungsschritte, die Sie testen können.
   
   ![Hochladen des Zertifikats](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Für eine benutzerdefinierte Domäne muss das Zertifikat nur einmal hochgeladen werden. Danach wird das hochgeladene Zertifikat automatisch angewendet, wenn Sie die benutzerdefinierte Domäne für andere Apps verwenden.
   
8. Wählen Sie nach dem Hinzufügen eines Zertifikats auf der Seite **Anwendungsproxy** die Option **Speichern** aus. 
   
9. Notieren Sie sich in der Informationsleiste auf der Seite **Anwendungsproxy** den CNAME-Eintrag, den Sie Ihrer DNS-Zone hinzufügen möchten. 
   
   ![Hinzufügen eines CNAME-DNS-Eintrags](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Befolgen Sie unter [Verwalten von DNS-Einträgen und -Ressourceneintragssätzen im Azure-Portal](../../dns/dns-operations-recordsets-portal.md) die Anleitung zum Hinzufügen eines DNS-Eintrags, mit dem die neue externe URL an die Domäne *msappproxy.net* umgeleitet wird.
   
11. Verwenden Sie zum Überprüfen der richtigen Konfiguration des DNS-Eintrags den Befehl [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx), um sicherzustellen, dass Ihre externe URL erreichbar ist und die Domäne *msapproxy.net* als Alias angezeigt wird.

Ihre Anwendung ist jetzt für die Verwendung der benutzerdefinierten Domäne eingerichtet. Achten Sie darauf, dass Sie Ihrer Anwendung Benutzer zuweisen, bevor Sie diese testen oder freigeben. 

Wählen Sie zum Ändern der Domäne für eine App auf der Seite **Anwendungsproxy** der App in der Dropdownliste unter **Externe URL** eine andere Domäne aus. Laden Sie bei Bedarf ein Zertifikat für die aktualisierte Domäne hoch, und aktualisieren Sie den DNS-Eintrag. Falls die gewünschte benutzerdefinierte Domäne in der Dropdownliste unter **Externe URL** nicht angezeigt wird, wurde sie ggf. noch nicht verifiziert.

Eine ausführlichere Anleitung zum Anwendungsproxy finden Sie unter [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Zertifikate für benutzerdefinierte Domänen

Mit einem Zertifikat wird die sichere TLS-Verbindung für Ihre benutzerdefinierte Domäne erstellt. 

### <a name="certificate-formats"></a>Zertifikatformate

Sie müssen ein PFX-Zertifikat verwenden, um sicherzustellen, dass alle erforderlichen Zwischenzertifikate enthalten sind. Das Zertifikat muss den privaten Schlüssel enthalten.

Die gängigsten Zertifikatsignaturmethoden wie alternative Antragstellernamen (Subject Alternative Names, SANs) werden unterstützt. 

Sie können Platzhalterzertifikate verwenden, solange der Platzhalter mit der externen URL übereinstimmt. Sie müssen Platzhalterzertifikate für [Platzhalteranwendungen](application-proxy-wildcard.md) verwenden. Wenn Sie das Zertifikat auch zum Zugreifen auf Unterdomänen verwenden möchten, müssen Sie die Unterdomänen-Platzhalter als alternative Antragstellernamen demselben Zertifikat hinzufügen. Ein Zertifikat für *\*.adventure-works.com* funktioniert beispielsweise nur für *\*.apps.adventure-works.com*, wenn Sie *\*.apps.adventure-works.com* als alternativen Antragstellernamen hinzufügen. 

Sie können von Ihrer eigenen Public Key-Infrastruktur (PKI) ausgestellte Zertifikate verwenden, wenn die Zertifikatkette auf Ihren Clientgeräten installiert ist. Mit Intune können diese Zertifikate auf verwalteten Geräten bereitgestellt werden. Bei nicht verwalteten Geräten müssen Sie diese Zertifikate manuell installieren. 

Es wird nicht empfohlen, eine private Stammzertifizierungsstelle zu verwenden, da diese ebenfalls per Pushvorgang auf Clientcomputer übermittelt werden muss. Dies kann zu vielen Problemen führen.

### <a name="certificate-management"></a>Zertifikatverwaltung

Die gesamte Zertifikatverwaltung erfolgt über einzelne Anwendungsseiten. Navigieren Sie zur Seite **Anwendungsproxy** der Anwendung, um auf das Feld **Zertifikat** zuzugreifen.

Sobald ein Zertifikat für eine Anwendung hochgeladen wurde, wird es auch automatisch auf **neu** konfigurierte Apps angewendet, die das gleiche Zertifikat verwenden. Sie müssen das Zertifikat für vorhandene Apps in Ihrem Mandanten erneut hochladen.

Bei Ablauf eines Zertifikats erhalten Sie eine Warnung mit der Aufforderung, ein anderes Zertifikat hochzuladen. Wenn das Zertifikat widerrufen wird, wird für Ihre Benutzer beim Zugreifen auf die App ggf. eine Sicherheitswarnung angezeigt. Navigieren Sie zum Aktualisieren des Zertifikats für eine App zur Seite **Anwendungsproxy** der App, wählen Sie die Option **Zertifikat** aus, und laden Sie ein neues Zertifikat hoch. Wenn das alte Zertifikat nicht von anderen Apps verwendet wird, wird es automatisch gelöscht. 

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren des einmaligen Anmeldens](application-proxy-configure-single-sign-on-with-kcd.md) bei Ihren veröffentlichten Apps mithilfe der Azure AD-Authentifizierung.
* [Bedingter Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md) für Ihre veröffentlichten Cloud-Apps.