---
title: Headerbasiertes einmaliges Anmelden für lokale Apps mit dem Azure AD-Anwendungsproxy
description: Es wird beschrieben, wie Sie einmaliges Anmelden für lokale Anwendungen bereitstellen, die per headerbasierter Authentifizierung geschützt sind.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d39d72a79d4b273918986d0d350df4706592c77d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503184"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Headerbasiertes einmaliges Anmelden für lokale Apps mit dem Azure AD-Anwendungsproxy (Vorschau)

Vom Azure AD-Anwendungsproxy (Azure Active Directory) wird der Zugriff auf Anwendungen, die Header für die Authentifizierung nutzen, per einmaligem Anmelden nativ unterstützt. Sie können Headerwerte konfigurieren, die für Ihre Anwendung in Azure AD benötigt werden. Die Headerwerte werden über den Anwendungsproxy an die Anwendung gesendet. Hier sind einige Beispiele für die Vorteile aufgeführt, die sich bei der Nutzung der nativen Unterstützung für die headerbasierte Authentifizierung mit dem Anwendungsproxy ergeben:  

* **Vereinfachung der Bereitstellung des Remotezugriffs auf Ihre lokalen Apps**: Mit einem Anwendungsproxy können Sie Ihre vorhandene Architektur für Remotezugriffe vereinfachen. Sie können den VPN-Zugriff auf diese Apps ersetzen. Darüber hinaus können Sie auch Abhängigkeiten von lokalen Identitätslösungen für die Authentifizierung entfernen. Ihre Benutzer bemerken keinen Unterschied, wenn sie sich für die Nutzung Ihrer Unternehmensanwendungen anmelden. Sie können weiterhin überall und auf beliebigen Geräten arbeiten.  

* **Keine zusätzliche Software oder Änderungen an Ihren Apps**: Sie können Ihre vorhandenen Anwendungsproxyconnectors verwenden, und es muss keine weitere Software installiert werden.  

* **Umfangreiche Liste mit verfügbaren Attributen und Transformationen**: Alle verfügbaren Headerwerte basieren auf Standardansprüchen, die von Azure AD ausgegeben werden. Alle Attribute und Transformationen, die für das [Konfigurieren von Ansprüchen für SAML- oder OIDC-Anwendungen](../develop/active-directory-saml-claims-customization.md#attributes) verfügbar sind, können auch als Headerwerte verwendet werden. 

## <a name="pre-requisites"></a>Voraussetzungen
Vergewissern Sie sich vor Ihren ersten Schritten mit dem einmaligen Anmelden für Apps mit headerbasierter Authentifizierung, dass Ihre Umgebung über die folgenden Einstellungen und Konfigurationen verfügt:
- Sie müssen den Anwendungsproxy aktivieren und einen Connector installieren, der über eine Sichtverbindung mit Ihren Anwendungen verfügt. Im Tutorial [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) wird beschrieben, wie Sie Ihre lokale Umgebung vorbereiten und einen Connector installieren, registrieren und anschließend testen. 

## <a name="supported-capabilities"></a>Unterstützte Funktionen

In der folgenden Tabelle sind die allgemeinen Funktionen angegeben, die für Anwendungen mit headerbasierter Authentifizierung und Unterstützung für den Anwendungsproxy erforderlich sind. 

|Anforderung   |BESCHREIBUNG|
|----------|-----------|
|Verbund-SSO |Im Modus der Vorauthentifizierung sind alle Anwendungen per Azure AD-Authentifizierung geschützt, und Benutzer können einmaliges Anmelden nutzen. |
|Remotezugriff |Der Anwendungsproxy ermöglicht den Remotezugriff auf die App. Benutzer können über das Internet auf die Anwendung zugreifen, indem sie einen beliebigen Browser und die externe URL verwenden. Der Anwendungsproxy ist nicht für den Unternehmenszugriff vorgesehen. |
|Headerbasierte Integration |Vom Anwendungsproxy wird die SSO-Integration mit Azure AD durchgeführt, und anschließend werden Identitäts- oder andere Anwendungsdaten als HTTP-Header an die Anwendung übergeben. |
|Anwendungsautorisierung |Allgemeine Richtlinien können basierend auf der Anwendung, auf die zugegriffen wird, der Gruppenmitgliedschaft des Benutzers und anderen Richtlinien angegeben werden. In Azure AD werden Richtlinien über den [bedingten Zugriff](../conditional-access/overview.md) implementiert. Richtlinien für die Anwendungsautorisierung gelten nur für die erste Authentifizierungsanforderung. |
|Step-up-Authentifizierung |Richtlinien können so definiert werden, dass eine weitere Authentifizierung erzwungen wird, z. B. um Zugriff auf vertrauliche Ressourcen zu erhalten. |
|Differenzierte Autorisierung |Ermöglicht die Zugriffssteuerung auf der URL-Ebene. Hinzugefügte Richtlinien können basierend auf der URL erzwungen werden, auf die zugegriffen wird. Mit der internen URL, die für die App konfiguriert wird, wird der Bereich der App definiert, für den die Richtlinie gilt. Die Richtlinie, die für den präzisesten Pfad konfiguriert wurde, wird erzwungen.  |

> [!NOTE] 
> In diesem Artikel geht es um das Verbinden von Anwendungen mit headerbasierter Authentifizierung mit Azure AD per Anwendungsproxy. Dies ist die empfohlene Vorgehensweise. Eine Alternative ist ein Integrationsmuster, bei dem PingAccess mit Azure AD verwendet wird, um die headerbasierte Authentifizierung zu ermöglichen. Ausführlichere Informationen finden Sie unter [Headerbasierte Authentifizierung für einmaliges Anmelden mit Anwendungsproxy und PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Funktionsweise

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Funktionsweise des headerbasierten einmaligen Anmeldens mit Anwendungsproxy" lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. Der Administrator passt die Attributzuordnungen, die für die Anwendung benötigt werden, im Azure AD-Portal an. 
2. Wenn ein Benutzer auf die App zugreift, wird mit dem Anwendungsproxy sichergestellt, dass der Benutzer von Azure AD authentifiziert wird. 
3. Der Anwendungsproxy-Clouddienst verfügt über die Informationen darüber, welche Attribute erforderlich sind. Der Dienst ruft die entsprechenden Ansprüche also über das ID-Token ab, das während der Authentifizierung empfangen wird. Der Dienst übersetzt die Werte im Rahmen der Anforderung an den Connector dann in die erforderlichen HTTP-Header. 
4. Anschließend wird die Anforderung an den Connector übergeben, der dann an die Back-End-Anwendung übergeben wird. 
5. Die Anwendung empfängt die Header und kann diese je nach Bedarf nutzen. 

## <a name="publish-the-application-with-application-proxy"></a>Veröffentlichen der Anwendung mit dem Anwendungsproxy

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter  [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - Mit dem Wert der internen URL wird der Bereich der Anwendung bestimmt. Wenn Sie den Wert der internen URL über den Stammpfad der Anwendung konfigurieren, erhalten alle Unterpfade des Stammpfads die gleiche Headerkonfiguration und die andere Anwendungskonfiguration. 
    - Erstellen Sie eine neue Anwendung, um eine andere Headerkonfiguration oder Benutzerzuweisung für einen präziseren Pfad als für die von Ihnen konfigurierte Anwendung festzulegen. Konfigurieren Sie in der neuen Anwendung die interne URL mit dem angegebenen Pfad, den Sie benötigen, und anschließend die erforderlichen spezifischen Header für diese URL. Der Anwendungsproxy entspricht immer Ihren Konfigurationseinstellungen für den präzisesten Pfad, der für eine Anwendung festgelegt wurde. 

2. Wählen Sie **Azure Active Directory** als **Methode für die Vorauthentifizierung** aus. 
3. Weisen Sie einen Testbenutzer zu, indem Sie zu **Benutzer und Gruppen** navigieren und die Zuweisung der entsprechenden Benutzer und Gruppen durchführen. 
4. Öffnen Sie einen Browser, und navigieren Sie in den Einstellungen für den Anwendungsproxy zu  **Externe URL**. 
5. Vergewissern Sie sich, dass Sie eine Verbindung mit der Anwendung herstellen können. Auch wenn die Verbindung möglich ist, haben Sie noch keinen Zugriff auf die App, weil die Header nicht konfiguriert sind. 

## <a name="configure-single-sign-on"></a>Einmaliges Anmelden konfigurieren 
Bevor Sie mit den ersten Schritten des einmaligen Anmeldens für headerbasierte Anwendungen beginnen, sollten Sie bereits einen Anwendungsproxyconnector installiert haben. Der Connector muss Zugriff auf die Zielanwendungen haben. Führen Sie andernfalls die Schritte im [Tutorial für den Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md) aus, und fahren Sie dann hier fort. 

1. Wenn Ihre Anwendung in der Liste mit den Unternehmensanwendungen angezeigt wird, wählen Sie zunächst die Anwendung und dann die Option  **Einmaliges Anmelden** aus. 
2. Legen Sie den Modus für einmaliges Anmelden auf **Headerbasiert** fest. 
3. In der Standardkonfiguration ist standardmäßig **Azure Active Directory** ausgewählt. 
4. Wählen Sie unter „Header“ das Stiftsymbol für die Bearbeitung aus, um die Header für das Senden an die Anwendung zu konfigurieren. 
5. Wählen Sie **Add new header** (Neuen Header hinzufügen) aus. Geben Sie unter **Name** einen Namen für den Header an, und wählen Sie entweder **Attribut** oder **Transformation** aus. Wählen Sie in der Dropdownliste aus, welcher Header von Ihrer Anwendung benötigt wird.  
    - Weitere Informationen zur Liste mit den verfügbaren Attributen finden Sie unter [Anpassen von Ansprüchen: Attribute](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Weitere Informationen zur Liste mit den verfügbaren Transformationen finden Sie unter [Anpassen von Ansprüchen: Anspruchstransformationen](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Sie können auch einen **Gruppenheader** hinzufügen, um alle Gruppen zu senden, denen ein Benutzer angehört. Alternativ können Sie die Gruppen, die der Anwendung zugewiesen sind, als Header senden. Weitere Informationen zur Konfiguration von Gruppen als Wert finden Sie unter [Konfigurieren von Gruppenansprüchen für Anwendungen](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Wählen Sie „Speichern“ aus. 

## <a name="test-your-app"></a>Testen Ihrer App 

Nachdem Sie alle Schritte abgeschlossen haben, sollte Ihre App betriebsbereit und verfügbar sein. So testen Sie die App: 
1. Öffnen Sie ein neues Browserfenster oder ein privates Browserfenster, um sicherzustellen, dass zuvor zwischengespeicherte Header gelöscht werden. Navigieren Sie dann in den Einstellungen für den Anwendungsproxy zu  **Externe URL**.
2. Melden Sie sich mit dem Testkonto an, das Sie der App zugewiesen haben. Wenn Sie die Anwendung laden und sich per SSO anmelden können, ist alles in Ordnung! 


## <a name="next-steps"></a>Nächste Schritte

- [Worum handelt es sich beim einmaligen Anmelden?](what-is-single-sign-on.md)
- [Was ist der Anwendungsproxy?](what-is-application-proxy.md)
- [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
