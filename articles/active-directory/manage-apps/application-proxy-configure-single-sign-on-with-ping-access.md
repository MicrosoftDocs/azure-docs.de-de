---
title: Headerbasierte Authentifizierung mit PingAccess für Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Veröffentlichen von Anwendungen mit PingAccess und App-Proxy zum Unterstützen der headerbasierten Authentifizierung.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472799"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Headerbasierte Authentifizierung für einmaliges Anmelden mit Anwendungsproxy und PingAccess

Der Azure Active Directory-Anwendungsproxy (Azure AD) wurde mit PingAccess kombiniert, damit Ihre Azure AD-Kunden auf weitere Ihrer Anwendungen zugreifen können. PingAccess erweitert die [vorhandenen Angebote für den Anwendungsproxy](application-proxy.md), um den Zugriff per einmaligem Anmelden auf Anwendungen zu ermöglichen, die Header für die Authentifizierung verwenden.

## <a name="whats-pingaccess-for-azure-ad"></a>Was ist PingAccess für Azure AD?

Mit PingAccess für Azure AD können Benutzer auf Anwendungen zugreifen, die Header für die Authentifizierung verwenden, und sich bei diesen einmalig anmelden. Der Anwendungproxy behandelt diese Anwendungen wie alle anderen und verwendet Azure AD zum Authentifizieren des Zugriffs und zum Leiten des Datenverkehrs durch den Connectordienst. PingAccess ist den Anwendungen vorgelagert und übersetzt das Zugriffstoken von Azure AD in einen Header. Die Anwendung empfängt dann die Authentifizierung in dem Format, das sie lesen kann.

Ihre Benutzer bemerken keinen Unterschied, wenn sie sich für die Nutzung Ihrer Unternehmensanwendungen anmelden. Sie können weiterhin überall und auf beliebigen Geräten arbeiten. Da die Anwendungsproxyconnectors Remotedatenverkehr zu allen Apps unabhängig von deren Authentifizierungstyp weiterleiten, sorgen sie auch weiter automatisch für Lastenausgleich.

## <a name="how-do-i-get-access"></a>Wie erhalte ich Zugriff?

Da dieses Szenario im Rahmen einer Partnerschaft von Azure Active Directory und PingAccess angeboten wird, benötigen Sie Lizenzen für beide Dienste. Azure Active Directory Premium-Abonnements enthalten aber eine grundlegende PingAccess-Lizenz, die bis zu 20 Anwendungen abdeckt. Falls Sie mehr als 20 headerbasierte Anwendungen veröffentlichen müssen, können Sie bei PingAccess eine weitere Lizenz erwerben.

Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Veröffentlichen der Anwendung in Azure

Dieser Artikel richtet sich an Personen, die in diesem Szenario erstmals eine Anwendung veröffentlichen. Neben der Erläuterung der Veröffentlichungsschritte werden Sie auch durch die ersten Schritte mit sowohl Anwendungsproxy als auch PingAccess geführt. Falls Sie bereits beide Dienste konfiguriert haben, aber eine Auffrischung zu den Veröffentlichungsschritten erhalten möchten, fahren Sie mit [Hinzufügen Ihrer App zu Azure AD mit Anwendungsproxy](#add-your-application-to-azure-ad-with-application-proxy) fort.

> [!NOTE]
> Da dieses Szenario auf einer Partnerschaft von Azure AD und PingAccess beruht, gelten einige der Anweisungen für die Ping Identity-Website.

### <a name="install-an-application-proxy-connector"></a>Installieren eines Anwendungsproxyconnectors

Wenn Sie bereits Anwendungsproxy aktiviert und einen Connector installiert haben, können Sie diesen Abschnitt überspringen und mit [Hinzufügen Ihrer Anwendung zu Azure AD mit Anwendungsproxy](#add-your-application-to-azure-ad-with-application-proxy) fortfahren.

Der Anwendungsproxyconnector ist ein Windows Server-Dienst, der den Datenverkehr von den Remotemitarbeitern zu Ihren veröffentlichten Anwendungen weiterleitet. Ausführlichere Installationsanweisungen finden Sie unter [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) als Anwendungsadministrator an. Die Seite **Azure Active Directory Admin Center** wird angezeigt.
2. Wählen Sie **Azure Active Directory** > **Anwendungsproxy** > **Connectordienst herunterladen** aus. Die Seite **Download des Anwendungsproxyconnectors** wird angezeigt.

   ![Download des Anwendungsproxyconnectors](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Folgen Sie den Installationsanweisungen.

Nach Herunterladen des Connectors sollte der Anwendungsproxy automatisch für Ihr Verzeichnis aktiviert sein. Falls nicht, können Sie **Anwendungsproxy aktivieren** auswählen.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Hinzufügen Ihrer Anwendung zu Azure AD mit Anwendungsproxy

Im Azure-Portal müssen Sie zwei Aktionen durchführen. Zuerst veröffentlichen Sie Ihre Anwendung mit dem Anwendungsproxy. Anschließend erfassen Sie einige Informationen zur Anwendung, die Sie während der PingAccess-Schritte benötigen.

#### <a name="publish-your-application"></a>Veröffentlichen der Anwendung

Zuerst müssen Sie die Anwendung veröffentlichen. Dieser Vorgang umfasst Folgendes:

- Hinzufügen Ihrer lokalen Anwendungen zu Azure AD
- Zuweisen eines Benutzers zum Testen der Anwendung und Auswählen von headerbasiertem einmaligen Anmelden
- Einrichten des Umleitungs-URL der Anwendung
- Erteilen von Berechtigungen für Benutzer und andere Anwendungen zur Verwendung Ihrer lokalen Anwendung

So veröffentlichen Sie Ihre eigene lokale Anwendung

1. Melden Sie sich als Anwendungsadministrator beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) an, sofern nicht im letzten Abschnitt erfolgt.
2. Wählen Sie **Unternehmensanwendungen** > **Neue Anwendung** > **Lokale Anwendung** aus. Die Seite **Fügen Sie Ihre eigene lokale Anwendung hinzu** wird angezeigt.

   ![Fügen Sie Ihre eigene lokale Anwendung hinzu](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Füllen Sie die Pflichtfelder mit Informationen zur neuen Anwendung aus. Befolgen Sie zum Festlegen der Einstellungen die nachstehenden Anweisungen.

   > [!NOTE]
   > Eine ausführliche exemplarische Vorgehensweise dieses Schritts finden Sie unter [Hinzufügen einer lokalen App zu Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Interne URL:** Normalerweise geben Sie die URL an, über die Sie zur Anmeldeseite der App gelangen, wenn Sie sich im Unternehmensnetzwerk befinden. Für dieses Szenario muss der Connector den PingAccess-Proxy als Einstiegsseite der Anwendung verwenden. Verwenden Sie dieses Format: `https://<host name of your PingAccess server>:<port>`. Der Standardport ist 3000, Sie können diesen aber in PingAccess konfigurieren.

      > [!WARNING]
      > Für diese Art von einmaligem Anmelden muss in der internen URL `https` und darf auf keinen Fall `http` verwendet werden.

   2. **Methode für die Vorauthentifizierung:** Wählen Sie **Azure Active Directory** aus.
   3. **URL in Headern übersetzen**: Wählen Sie **Nein**.

   > [!NOTE]
   > Wenn dies Ihre erste Anwendung ist, verwenden Sie Port 3000, um zu beginnen, und kehren wieder zu dieser Einstellung zurück, um sie bei einer Änderung der PingAccess-Konfiguration zu aktualisieren. Bei nachfolgenden Anwendungen muss der Port dem Listener entsprechen, den Sie in PingAccess konfiguriert haben. Erfahren Sie mehr über [Listener in PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Wählen Sie **Hinzufügen**. Die Übersichtsseite für die neue Anwendung wird angezeigt.

Ordnen Sie nun einen Benutzer für das Testen der Anwendung zu, und wählen Sie headerbasiertes einmaliges Anmelden aus:

1. Wählen Sie auf der Randleiste der Anwendung **Benutzer und Gruppen** > **Benutzer hinzufügen** > **Benutzer und Gruppen (ausgewählte \<Anzahl>)** aus. Eine Liste mit Benutzern und Gruppen wird angezeigt, in der Sie wählen können.

   ![Benutzer und Gruppen](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Wählen Sie einen Benutzer zum Testen der Anwendung aus, und klicken Sie auf **Auswählen**. Stellen Sie sicher, dass dieses Testkonto auf die lokale Anwendung zugreifen kann.
3. Wählen Sie **Zuweisen** aus.
4. Wählen Sie auf der Randleiste der Anwendung auf **Einmaliges Anmelden** > **Headerbasiert**.

   > [!TIP]
   > Wenn Sie das headerbasierte einmalige Anmelden zum ersten Mal verwenden, müssen Sie PingAccess installieren. Verwenden Sie den Link auf dieser SSO-Seite zum Herunterladen von PingAccess, um sicherzustellen, dass Ihr Azure-Abonnement automatisch Ihrer PingAccess-Installation zugeordnet wird. Sie können die Download-Website jetzt öffnen oder später zu dieser Seite zurückkehren.

   ![Headerbasierte Anmeldung](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Wählen Sie **Speichern** aus.

Stellen Sie dann sicher, dass Ihre Umleitungs-URL auf Ihre externe URL festgelegt ist:

1. Wählen Sie auf der Randleiste von **Azure Active Directory-Verwaltungscenter** nacheinander **Azure Active Directory** > **App-Registrierungen** aus. Eine Liste mit Anwendungen wird eingeblendet.

   ![App-Registrierungen](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Wählen Sie Ihre Anwendung aus.
3. Klicken Sie auf den Link neben **Umleitungs-URIs**, über den die Anzahl der Umleitungs-URIs gezeigt wird, die für Web- und öffentliche Clients eingerichtet wurden. Die Seite **\<Anwendungsname> – Authentifizierung** wird eingeblendet.
4. Prüfen Sie, ob sich die externe URL, die Sie Ihrer Anwendung zuvor zugewiesen haben, in der Liste **Umleitungs-URIs** befindet. Falls nicht, fügen Sie die externe URL jetzt mit dem Umleitungs-URI-Typ **Web** hinzu, und klicken Sie auf **Speichern**.

Richten Sie schließlich Ihre lokale Anwendung so ein, dass Benutzer Lesezugriff und andere Anwendungen Lese-/Schreibzugriff haben:

1. Wählen Sie auf der Randleiste von **App-Registrierungen** für Ihre Anwendung **API-Berechtigungen** > **Berechtigung hinzufügen** > **Microsoft-APIs** > **Microsoft Graph** aus. Die Seite **API-Berechtigungen anfordern** für **Microsoft Graph** wird angezeigt, die die APIs für Azure Active Directory enthält.

   ![API-Berechtigungen anfordern](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Wählen Sie **Delegierte Berechtigungen** > **Benutzer** > **User.Read** aus.
3. Wählen Sie **Anwendungsberechtigungen** > **Anwendung** > **Application.ReadWrite.All** aus.
4. Wählen Sie **Berechtigungen hinzufügen** aus.
5. Klicken Sie auf der Seite **API-Berechtigungen** auf **Administratorzustimmung für \<Name Ihres Verzeichnisses> erteilen**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Erfassen von Informationen für die Schritte in PingAccess

Sie müssen diese drei Informationen (alle GUIDs) sammeln, um Ihre Anwendung mit PingAccess einzurichten:

| Name des Azure AD-Felds | Name des PingAccess-Felds | Datenformat |
| --- | --- | --- |
| **Anwendungs-ID (Client)** | **Client-ID** | GUID |
| **Verzeichnis-ID (Mandant)** | **Aussteller** | GUID |
| `PingAccess key` | **Geheimer Clientschlüssel** | Zufallszeichenfolge |

So erfassen Sie diese Informationen

1. Wählen Sie auf der Randleiste von **Azure Active Directory-Verwaltungscenter** nacheinander **Azure Active Directory** > **App-Registrierungen** aus. Eine Liste mit Anwendungen wird eingeblendet.
2. Wählen Sie Ihre Anwendung aus. Die Seite **App-Registrierungen** für Ihre Anwendung wird angezeigt.

   ![Übersicht über die Registrierung für eine Anwendung](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Klicken Sie neben dem Wert **Anwendungs-ID (Client)** auf das Symbol **In Zwischenablage kopieren**. Kopieren und speichern Sie den Wert. Sie geben diesen Wert später als Client-ID von PingAccess an.
4. Klicken Sie auch neben dem Wert **Verzeichnis-ID (Mandant)** auf das Symbol **In Zwischenablage kopieren**. Kopieren und speichern Sie den Wert. Sie geben diesen Wert später als Aussteller von PingAccess an.
5. Klicken Sie auf der Randleiste von **App-Registrierungen** für Ihre Anwendung auf **Zertifikate und Geheimnisse** > **Neuer geheimer Clientschlüssel**. Die Seite **Geheimen Clientschlüssel hinzufügen** wird angezeigt.

   ![Geheimen Clientschlüssel hinzufügen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. Geben Sie `PingAccess key` in **Beschreibung** ein.
7. Wählen Sie unter **Läuft ab** aus, wann der PingAccess-Schlüssel ablaufen soll: **In 1 Jahr**, **In 2 Jahren** oder **Nie**.
8. Wählen Sie **Hinzufügen**. Der PingAccess-Schlüssel wird in der Tabelle der geheimen Clientschlüssel mit einer Zufallszeichenfolge angezeigt, die automatisch in das Feld **VALUE** eingetragen wird.
9. Klicken Sie neben dem Feld **VALUE** des PingAccess-Schlüssels auf das Symbol **In die Zwischenablage kopieren**. Kopieren und speichern Sie den Wert. Sie geben diesen Wert später als geheimen Clientschlüssel von PingAccess an.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualisieren von GraphAPI zum Senden von benutzerdefinierten Feldern (optional)

Wenn Sie einen benutzerdefinierten Anspruch benötigen, der andere Token innerhalb des von PingAccess genutzten „access_token“ sendet, legen Sie für das Anwendungsfeld `acceptMappedClaims` `True` fest. Sie können Graph-Tester oder das Anwendungsmanifest des Azure AD-Portals nutzen, um diese Änderung vornehmen.

**In diesem Beispiel wird der Graph-Tester verwendet:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**In diesem Beispiel wird das `acceptMappedClaims`Feld** im [Azure Active Directory-Portal](https://aad.portal.azure.com/) aktualisiert:

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) als Anwendungsadministrator an.
2. Klicken Sie auf **Azure Active Directory** > **App-Registrierungen**. Eine Liste mit Anwendungen wird eingeblendet.
3. Wählen Sie Ihre Anwendung aus.
4. Klicken Sie auf der Randleiste der Seite **App-Registrierungen** für Ihre Anwendung auf **Manifest**. Der JSON-Code des Manifests für die Registrierung Ihrer Anwendung wird angezeigt.
5. Suchen Sie das Feld `acceptMappedClaims`, und ändern Sie den Wert in `True`.
6. Wählen Sie **Speichern** aus.


### <a name="use-of-optional-claims-optional"></a>Verwenden optionaler Ansprüche (optional)
Mit optionalen Ansprüchen können Sie standardmäßige Ansprüche, die aber nicht standardmäßig enthalten sind, allen Benutzern und Mandanten hinzufügen. Sie können optionale Ansprüche für Ihre Anwendung konfigurieren, indem Sie das Anwendungsmanifest ändern. Weitere Informationen finden Sie im Artikel [Grundlegendes zum Azure AD-Anwendungsmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/).

Beispiel zum Einschließen der E-Mail-Adresse in das „access_token“, das PingAccess nutzt:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Verwenden der Anspruchszuordnungsrichtlinie (optional)
[Anspruchszuordnungsrichtlinie (Vorschau)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) für Attribute, die nicht in Azure AD vorhanden sind. Durch Zuordnung von Ansprüchen können Sie alte lokale Apps zur Cloud migrieren, indem Sie zusätzliche benutzerdefinierte Ansprüche hinzufügen, die von Ihrem ADFS oder Benutzerobjekten gesichert werden.

Damit Ihre Anwendung einen benutzerdefinierten Anspruch verwenden und zusätzliche Felder enthalten kann, achten Sie darauf, dass Sie auch [eine Richtlinie für die Zuordnung benutzerdefinierter Ansprüche erstellt und der Anwendung zugeordnet haben](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Um einen benutzerdefinierten Anspruch zu verwenden, benötigen Sie auch eine für diese Anwendung definierte und ihr zugewiesene benutzerdefinierte Richtlinie. Diese Richtlinie sollte alle erforderlichen benutzerdefinierten Attribute enthalten.
>
> Richtliniendefinition und Zuweisung können über PowerShell, Azure AD Graph-Tester oder Microsoft Graph erfolgen. Wenn diese Aufgaben über PowerShell erfolgen, müssen Sie möglicherweise zuerst `New-AzureADPolicy` verwenden und die Richtlinie dann der Anwendung mit `Add-AzureADServicePrincipalPolicy` zuweisen. Weitere Informationen finden Sie unter [Zuweisung von Anspruchszuordnungsrichtlinien](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Beispiel:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>Aktivieren von PingAccess, um benutzerdefinierte Ansprüche zu verwenden (optional, jedoch erforderlich, wenn Sie erwarten, dass die Anwendung zusätzliche Ansprüche nutzt)
Wenn Sie im folgenden Schritt PingAccess konfigurieren, muss in der Websitzung, die Sie erstellen (Einstellungen->Zugriff->Websitzungen) **Anforderungsprofil** deaktiviert und **Benutzerattribute aktualisieren** auf **Nein** gesetzt sein.

## <a name="download-pingaccess-and-configure-your-application"></a>Herunterladen von PingAccess und Konfigurieren Ihrer Anwendung

Nachdem Sie nun alle Schritte des Azure Active Directory-Setups ausgeführt haben, können Sie mit dem Konfigurieren von PingAccess fortfahren.

Die ausführlichen Anleitungen für den PingAccess-Teil dieses Szenarios werden in der Ping Identity-Dokumentation fortgesetzt. Befolgen Sie auf der Ping Identity-Website die Anweisungen unter [Configure PingAccess for Azure AD to protect applications published using Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) (Konfigurieren von PingAccess für Azure AD zum Schutz von Anwendungen, die mit Microsoft Azure AD-Anwendungsproxy veröffentlicht wurden).

Diese Schritte helfen Ihnen, PingAccess zu installieren und ein PingAccess-Konto einzurichten (falls Sie noch keines haben). Um dann eine Azure AD OpenID Connect-Verbindung (OIDC) herzustellen, richten Sie einen Tokenanbieter mit dem Wert von **Verzeichnis-ID (Mandant)** ein, den Sie aus dem Azure AD-Portal kopiert haben. Als Nächstes verwenden Sie zum Erstellen einer Websitzung für PingAccess die Werte **Anwendungs-ID (Client)** und `PingAccess key`. Danach können Sie die Identitätszuordnung einrichten und einen virtuellen Host, einen virtuellen Standort und eine virtuelle Anwendung erstellen.

### <a name="test-your-application"></a>Testen Ihrer Anwendung

Nachdem Sie alle Schritte abgeschlossen haben, sollte Ihre Anwendung betriebsbereit sein. Um sie zu testen, öffnen Sie einen Browser und navigieren zur externen URL, die Sie erstellt haben, als Sie die Anwendung in Azure veröffentlicht haben. Melden Sie sich mit dem Testkonto an, das Sie der Anwendung zugewiesen haben.

## <a name="next-steps"></a>Nächste Schritte

- [Configure PingAccess for Azure AD to protect applications published using Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) (Konfigurieren von PingAccess für Azure AD zum Schutz von Anwendungen, die mit Microsoft Azure AD-Anwendungsproxy veröffentlicht wurden)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](what-is-single-sign-on.md)
- [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md)
