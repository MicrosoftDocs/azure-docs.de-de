---
title: Planen der Implementierung einer Azure Active Directory-Einbindung
description: Hier werden die Schritte erläutert, die Sie zum Implementieren von in Azure AD eingebundenen Geräten in Ihrer Umgebung ausführen müssen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3acaf4929158b24ff50655aa18c05b41aeec4b53
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435449"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Gewusst wie: Planen der Implementierung Ihrer Azure AD-Einbindung

Mit Azure AD Join können Sie Geräte direkt in Azure AD einbinden, ohne dem lokalen Active Directory beitreten zu müssen, während Ihre Benutzer produktiv und sicher bleiben. Azure AD Join eignet sich für skalierbare und bereichsbezogene Bereitstellungen in Unternehmen.   

In diesem Artikel erhalten Sie die Informationen, die Sie zum Planen der Implementierung von Azure AD Join benötigen.
 
## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie die [Einführung in die Geräteverwaltung in Azure Active Directory](./overview.md) gelesen haben.

## <a name="plan-your-implementation"></a>Planen Ihrer Implementierung

Um die Implementierung Ihrer Azure AD-Einbindung zu planen, sollten Sie sich mit folgenden Themen vertraut machen:

> [!div class="checklist"]
> - Überprüfen Ihrer Szenarien
> - Überprüfen Ihrer Identitätsinfrastruktur
> - Bewerten Ihrer Geräteverwaltung
> - Verstehen der zu berücksichtigenden Aspekte für Anwendungen und Ressourcen
> - Grundlegendes zu Ihren Bereitstellungsoptionen
> - Konfigurieren von Enterprise State Roaming
> - Konfigurieren des bedingten Zugriffs

## <a name="review-your-scenarios"></a>Überprüfen Ihrer Szenarien 

Während Azure AD Hybrid Join für bestimmte Szenarien bevorzugt werden kann, ermöglicht Azure AD Join den Übergang zu einem Cloud-First-Modell mit Windows. Wenn Sie Ihre Geräteverwaltung modernisieren und gerätebezogene IT-Kosten senken möchten, bietet Azure AD Join eine hervorragende Grundlage, um diese Ziele zu erreichen.  
 
Sie sollten Azure AD Join in Betracht ziehen, wenn Ihre Ziele den folgenden Kriterien entsprechen:

- Sie führen Microsoft 365 als Produktivitätssuite für Ihre Benutzer ein.
- Sie möchten Geräte mit einer cloudspezifischen Geräteverwaltungslösung verwalten.
- Sie möchten die Gerätebereitstellung für geografisch verteilte Benutzer vereinfachen.
- Sie haben vor, Ihre Anwendungsinfrastruktur zu modernisieren.

## <a name="review-your-identity-infrastructure"></a>Überprüfen Ihrer Identitätsinfrastruktur  

Azure AD Join kann sowohl in verwalteten Umgebungen als auch in Verbundumgebungen eingesetzt werden.  

### <a name="managed-environment"></a>Verwaltete Umgebung

Eine verwaltete Umgebung kann entweder durch [Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta-quick-start.md) mit dem nahtlosen einmaligen Anmelden (Seamless Single Sign On, Seamless SSO) bereitgestellt werden.

In diesen Szenarien müssen Sie keinen Verbundserver für die Authentifizierung konfigurieren.

### <a name="federated-environment"></a>Verbundumgebung

Bei Verbundumgebungen sollte ein Identitätsanbieter verwendet werden, der sowohl das WS-Trust- als auch das WS-Fed-Protokoll unterstützt:

- **WS-Fed:** Dieses Protokoll ist erforderlich, um ein Gerät in Azure AD einzubinden.
- **WS-Trust:** Dieses Protokoll ist für die Anmeldung bei einem in Azure AD eingebundenen Gerät erforderlich.

Bei Verwendung von AD FS müssen Sie die folgenden WS-Trust-Endpunkte aktivieren: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Wenn Ihr Identitätsanbieter diese Protokolle nicht unterstützt, funktioniert Azure AD Join nicht systemintern. 

>[!NOTE]
> Derzeit funktioniert Azure AD Join nicht, wenn [AD FS 2019 mit externen Authentifizierungsanbietern als primäre Authentifizierungsmethode konfiguriert ist](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Für Azure AD Join ist standardmäßig die Kennwortauthentifizierung als primäre Methode festgelegt, was in diesem Szenario zu Authentifizierungsfehlern führt.


### <a name="smartcards-and-certificate-based-authentication"></a>Smartcards und zertifikatbasierte Authentifizierung

Zum Einbinden von Geräten in Azure AD können Sie keine Smartcards oder zertifikatbasierte Authentifizierung verwenden. Smartcards können jedoch für die Anmeldung bei in Azure AD eingebundenen Geräten verwendet werden, wenn AD FS konfiguriert ist.

**Empfehlung:** Implementieren Sie Windows Hello for Business für eine sichere, kennwortlose Authentifizierung bei Windows 10-Geräten.

### <a name="user-configuration"></a>Benutzerkonfiguration

Bei der Erstellung von Benutzern in:

- **Lokalem Active Directory** müssen Sie die Benutzer mithilfe von [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) mit Azure AD synchronisieren. 
- **Azure AD** ist keine zusätzliche Einrichtung erforderlich.

Lokale UPNs, die sich von Azure AD-UPNs unterscheiden, werden auf in Azure AD eingebundenen Geräten nicht unterstützt. Wenn Ihre Benutzer einen lokalen UPN verwenden, sollten Sie auf die Verwendung ihres primären UPN in Azure AD umstellen.

UPN-Änderungen werden erst ab dem Windows 10-Update 2004 unterstützt. Bei Benutzern, die Geräte mit diesem Update verwenden, kann der UPN problemlos geändert werden. Auf Geräten vor dem Windows 10-Update 2004 haben Benutzer Probleme mit SSO und dem bedingten Zugriff. Sie müssen sich über die Kachel „Anderer Benutzer“ mit ihrem neuen UPN bei Windows anmelden, um dieses Problem zu beheben. 

## <a name="assess-your-device-management"></a>Bewerten Ihrer Geräteverwaltung

### <a name="supported-devices"></a>Unterstützte Geräte

Azure AD Join:

- Gilt nur für Windows 10-Geräte. 
- Gilt nicht für vorherige Versionen von Windows oder andere Betriebssysteme. Wenn Sie über Windows 7- oder Windows 8.1-Geräte verfügen, müssen Sie ein Upgrade auf Windows 10 durchführen, um Azure AD Join bereitstellen zu können.
- Wird für FIPS-konformes TPM 2.0 und nicht für TPM 1.2 unterstützt. Wenn Ihre Geräte über FIPS-konformes TPM 1.2 verfügen, müssen Sie sie deaktivieren, bevor Sie mit Azure AD Join fortfahren. Microsoft stellt keine Tools zum Deaktivieren des FIPS-Modus für TPMs bereit, da dieser vom TPM-Hersteller abhängig ist. Wenden Sie sich an Ihren Hardware-OEM, um Unterstützung zu erhalten.
 
**Empfehlung:** Verwenden Sie immer das neueste Release von Windows 10, um die aktualisierten Features nutzen zu können.

### <a name="management-platform"></a>Verwaltungsplattform

Die Geräteverwaltung für in Azure AD eingebundene Geräte basiert auf einer MDM-Plattform (wie Intune) und MDM-CSPs. Windows 10 verfügt über einen integrierten MDM-Agent, der alle kompatiblen MDM-Lösungen unterstützt.

> [!NOTE]
> Gruppenrichtlinien werden für in Azure AD eingebundene Geräte nicht unterstützt, weil diese nicht mit der lokalen Active Directory-Instanz verbunden sind. Die Verwaltung von in Azure AD eingebundenen Geräten ist nur über MDM möglich.

Es gibt zwei Ansätze für die Verwaltung von in Azure AD eingebundenen Geräten:

- **Nur MDM**: Ein Gerät wird ausschließlich von einem MDM-Anbieter wie Intune verwaltet. Alle Richtlinien werden im Rahmen des MDM-Registrierungsprozesses bereitgestellt. Für Azure AD Premium- oder EMS-Kunden ist die MDM-Registrierung ein automatisierter Schritt, der Teil einer Azure AD-Einbindung ist.
- **Co-Verwaltung**: Ein Gerät wird von einem MDM-Anbieter und SCCM verwaltet. Bei diesem Ansatz ist der SCCM-Agent auf einem MDM-verwalteten Gerät zur Verwaltung bestimmter Aspekte installiert.

Überprüfen Sie bei Verwendung von Gruppenrichtlinien mithilfe der [Analyse von Gruppenrichtlinien](/mem/intune/configuration/group-policy-analytics) in Microsoft Endpoint Manager die Parität von GPO- und MDM-Richtlinien. 

Überprüfen Sie die unterstützten und nicht unterstützten Richtlinien, um zu bestimmen, ob Sie statt Gruppenrichtlinien eine MDM-Lösung verwenden können. In Bezug auf nicht unterstützte Richtlinien ist Folgendes zu berücksichtigen:

- Sind die nicht unterstützten Richtlinien für in Azure AD eingebundene Geräte oder für Benutzer erforderlich?
- Sind die nicht unterstützten Richtlinien in einer cloudbasierten Bereitstellung anwendbar?

Wenn Ihre MDM-Lösung nicht über den Azure AD-App-Katalog verfügbar ist, können Sie die Lösung gemäß der unter [Azure Active Directory-Integration von MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) beschriebenen Vorgehensweise hinzufügen. 

Durch die Co-Verwaltung können Sie SCCM verwenden, um bestimmte Aspekte Ihrer Geräte zu verwalten, während die Richtlinien über Ihre MDM-Plattform bereitgestellt werden. Microsoft Intune ermöglicht die Co-Verwaltung mit SCCM. Weitere Informationen zur Co-Verwaltung für Windows 10-Geräte finden Sie unter [Was ist Co-Verwaltung?](/configmgr/core/clients/manage/co-management-overview). Wenn Sie ein anderes MDM-Produkt als Intune verwenden, wenden Sie sich an Ihren MDM-Anbieter, um Informationen zu entsprechenden Szenarien für die Co-Verwaltung zu erhalten.

**Empfehlung:** Ziehen Sie für in Azure AD eingebundene Geräte nur die mobile Geräteverwaltung (MDM) in Betracht.

## <a name="understand-considerations-for-applications-and-resources"></a>Verstehen der zu berücksichtigenden Aspekte für Anwendungen und Ressourcen

Wir empfehlen die Migration von lokalen Anwendungen zu Cloudanwendungen, um die Benutzerfreundlichkeit und die Zugriffssteuerung zu verbessern. Aber in Azure AD eingebundene Geräte ermöglichen den nahtlosen Zugriff auf lokale Anwendungen und Cloudanwendungen. Weitere Informationen finden Sie unter [So funktioniert das einmalige Anmelden bei lokalen Ressourcen auf in Azure AD eingebundenen Geräten](azuread-join-sso.md).

In den folgenden Abschnitten werden Aspekte für verschiedene Arten von Anwendungen und Ressourcen aufgeführt.

### <a name="cloud-based-applications"></a>Cloudbasierte Anwendungen

Wenn eine Anwendung zum Azure AD-App-Katalog hinzugefügt wird, erhalten Benutzer SSO über die in Azure AD eingebundenen Geräte. Es ist keine zusätzliche Konfiguration erforderlich. Benutzer erhalten SSO in den Browsern Microsoft Edge und Chrome. Wenn Sie Chrome verwenden, müssen Sie die [Erweiterung „Windows 10 Accounts“](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) bereitstellen. 

Für Win32-Anwendungen gilt Folgendes:

- Alle Win32-Anwendungen, die Web Account Manager (WAM) für Tokenanforderungen verwenden, erhalten auch SSO auf in Azure AD eingebundenen Geräten. 
- Alle Win32-Anwendungen, die WAM nicht verwenden, fordern Benutzer möglicherweise zur Authentifizierung auf. 

### <a name="on-premises-web-applications"></a>Lokale Webanwendungen

Wenn Ihre Apps benutzerdefiniert sind und/oder lokal gehostet werden, müssen Sie die Apps aus folgenden Gründen zu den vertrauenswürdigen Sites Ihres Browsers hinzufügen:

- Damit die integrierte Windows-Authentifizierung funktioniert 
- Um den Benutzern ein SSO-Erlebnis ohne Eingabeaufforderung zu bieten. 

Wenn Sie AD FS verwenden, lesen Sie [Überprüfen und Verwalten von einmaligem Anmelden mit AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Empfehlung:** Ziehen Sie ein Hosting in der Cloud (z.B. Azure) und die Integration von Azure AD in Betracht, um die Benutzerfreundlichkeit zu verbessern.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Lokale Anwendungen, die ältere Protokolle verwenden

Benutzer erhalten SSO über in Azure AD eingebundene Geräte, wenn das jeweilige Gerät Zugriff auf einen Domänencontroller hat. 

**Empfehlung:** Stellen Sie den [Azure AD-App-Proxy](../manage-apps/application-proxy.md) bereit, um den sicheren Zugriff für diese Anwendungen zu ermöglichen.

### <a name="on-premises-network-shares"></a>Lokale Netzwerkfreigaben

Ihre Benutzer erhalten SSO über in Azure AD eingebundene Geräte, wenn das jeweilige Gerät Zugriff auf einen lokalen Domänencontroller hat.

### <a name="printers"></a>Drucker

Für Drucker müssen Sie [Hybrid Cloud Print](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) zur Erkennung von Druckern auf in Azure AD eingebundenen Geräten bereitstellen. 

Drucker können in einer reinen Cloudumgebung nicht automatisch erkannt werden, aber Ihre Benutzer können auch den UNC-Pfad von Druckern verwenden, um sie direkt hinzuzufügen. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Lokale Anwendungen, die Computerauthentifizierung verwenden

In Azure AD eingebundene Geräte unterstützen keine lokalen Anwendungen, die Computerauthentifizierung verwenden. 

**Empfehlung:** Setzen Sie diese Anwendungen außer Kraft, und stellen Sie auf modernere Alternativen um.

### <a name="remote-desktop-services"></a>Remotedesktopdienste

Um eine Remotedesktopverbindung mit einem in Azure AD eingebundenen Gerät herstellen zu können, muss der Hostcomputer entweder in Azure AD oder Azure AD Hybrid eingebunden sein. Eine Remotedesktopverbindung über ein nicht verbundenes oder Nicht-Windows-Gerät wird nicht unterstützt. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem in Azure AD eingebundenen Remote-PC](/windows/client-management/connect-to-remote-aadj-pc).

Ab dem Windows 10-Update 2004 können Benutzer auch eine Remotedesktopverbindung auf einem bei Azure AD registrierten Windows 10-Gerät mit einem in Azure AD eingebundenen Gerät verwenden. 

## <a name="understand-your-provisioning-options"></a>Grundlegendes zu Ihren Bereitstellungsoptionen
**Hinweis**: In Azure AD eingebundene Geräte können nicht mit einem Tool für die Systemvorbereitung (Sysprep) oder mit ähnlichen Abbilderstellungstools bereitgestellt werden.

Sie können Azure AD Join mithilfe der folgenden Methoden bereitstellen:

- **Self-Service auf der Windows-Willkommensseite/in den Windows-Einstellungen**: Im Self-Service-Modus durchlaufen die Benutzer den Azure AD-Einbindungsprozess entweder auf der Windows-Willkommensseite (Windows Out-of-Box-Experience, OOBE) oder in den Windows-Einstellungen. Weitere Informationen finden Sie unter [Einbinden von geschäftlichen Geräten in das Netzwerk der Organisation](../user-help/user-help-join-device-on-network.md). 
- **Windows Autopilot**: Windows Autopilot ermöglicht die Vorkonfiguration von Geräten, um auf der Windows-Willkommensseite für ein möglichst reibungsloses Benutzererlebnis bei einer Azure AD-Einbindung zu sorgen. Weitere Informationen finden Sie in der [Übersicht über Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Massenregistrierung**: Die Massenregistrierung ermöglicht eine vom Administrator gesteuerte Azure AD-Einbindung mithilfe eines Massenbereitstellungstools zum Konfigurieren von Geräten. Weitere Informationen finden Sie unter [Massenregistrierung für Windows-Geräte](/intune/windows-bulk-enroll).
 
Hier finden Sie einen Vergleich dieser drei Methoden: 
 
| Element | Self-Service-Einrichtung | Windows Autopilot | Massenregistrierung |
| --- | --- | --- | --- |
| Benutzerinteraktion zum Einrichten erforderlich | Ja | Ja | Nein |
| IT-Maßnahmen erforderlich | Nein | Ja | Ja |
| Zutreffende Abläufe | Windows-Willkommensseite & Einstellungen | Nur Windows-Willkommensseite | Nur Windows-Willkommensseite |
| Lokale Administratorrechte für primären Benutzer | Ja, standardmäßig | Konfigurierbar | Nein |
| OEM-Support erforderlich | Nein | Ja | Nein |
| Unterstützte Versionen | 1511+ | 1709+ | 1703+ |
 
Wählen Sie Ihre Bereitstellungsmethode(n), indem Sie die obige Tabelle durchgehen und die folgenden Aspekte für die Anwendung der jeweiligen Methode berücksichtigen:  

- Sind Ihre Benutzer technisch versiert, sodass sie die Einrichtung selbst vornehmen können? 
   - Self-Service kann für diese Benutzer am besten geeignet sein. Ziehen Sie Windows Autopilot in Betracht, um die Benutzerfreundlichkeit zu verbessern.  
- Arbeiten Ihre Benutzer remote oder in den Räumlichkeiten des Unternehmens? 
   - Self-Service oder Autopilot sind für Remotebenutzer hinsichtlich einer problemlosen Einrichtung am besten geeignet. 
- Bevorzugen Sie eine benutzergesteuerte oder eine vom Administrator verwaltete Konfiguration? 
   - Eine Massenregistrierung ist für die administratorgesteuerte Bereitstellung besser geeignet, um Geräte vor der Übergabe an die Benutzer einzurichten.     
- Erwerben Sie Geräte von 1 bis 2 OEMs, oder gibt es in Ihrem Unternehmen eine breite Verteilung von OEM-Geräten?  
   - Wenn Sie Geräte von einer begrenzten Anzahl von OEMs erwerben, die auch Autopilot unterstützen, können Sie von einer engeren Integration von Autopilot profitieren. 

## <a name="configure-your-device-settings"></a>Konfigurieren Ihrer Geräteeinstellungen

Im Azure-Portal können Sie die Bereitstellung von in Azure AD eingebundenen Geräten in Ihrer Organisation steuern. Wählen Sie zum Konfigurieren der entsprechenden Einstellungen auf der Seite **Azure Active Directory** die Option `Devices > Device settings` aus.

### <a name="users-may-join-devices-to-azure-ad"></a>Benutzer dürfen Geräte in Azure AD einbinden

Legen Sie diese Option basierend auf dem Umfang/Benutzerbereich Ihrer Bereitstellung und den Benutzern, denen Sie die Einrichtung eines in Azure AD eingebundenen Geräts erlauben möchten, auf **Alle** oder **Ausgewählte** fest. 

![Benutzer dürfen Geräte in Azure AD einbinden](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Weitere lokale Administratoren für in Azure AD eingebundene Geräte

Wählen Sie **Ausgewählte** aus, und wählen Sie die Benutzer aus, die Sie der lokalen Administratorgruppe für alle in Azure AD eingebundenen Geräte hinzufügen möchten. 

![Weitere lokale Administratoren für in Azure AD eingebundene Geräte](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Multi-factor Auth zum Hinzufügen von Geräten erforderlich

Wählen Sie **Ja** aus, wenn Benutzer beim Einbinden von Geräten in Azure AD die Multi-Factor Authentication (MFA) ausführen müssen. Für die Benutzer, die Geräte in Azure AD mit der MFA einbinden, wird das Gerät selbst zum zweiten Faktor.

![Multi-factor Auth zum Hinzufügen von Geräten erforderlich](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurieren Ihrer Mobilitätseinstellungen

Bevor Sie Ihre Mobilitätseinstellungen konfigurieren können, müssen Sie möglicherweise zuerst einen MDM-Anbieter hinzufügen.

**Gehen Sie wie folgt vor, um einen MDM-Anbieter hinzuzufügen**:

1. Klicken Sie auf der Seite **Azure Active Directory** im Abschnitt **Verwalten** auf `Mobility (MDM and MAM)`. 
1. Klicken Sie auf **Anwendung hinzufügen**.
1. Wählen Sie Ihren MDM-Anbieter aus der Liste aus.

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Screenshot der Seite „Anwendung hinzufügen“ in Azure Active Directory. Mehrere MDM-Anbieter sind aufgelistet." border="false":::

Wählen Sie Ihren MDM-Anbieter aus, um die entsprechenden Einstellungen zu konfigurieren. 

### <a name="mdm-user-scope"></a>MDM-Benutzerbereich

Wählen Sie basierend auf dem Benutzerbereich Ihrer Bereitstellung entweder **Einige** oder **Alle** aus. 

![MDM-Benutzerbereich](./media/azureadjoin-plan/05.png)

Basierend auf Ihrem Benutzerbereich ergibt sich eine der folgenden Situationen: 

- **Der Benutzer befindet sich im MDM-Bereich**: Wenn Sie über ein Azure AD Premium-Abonnement verfügen, ist die MDM-Registrierung mit der Azure AD-Einbindung automatisiert. Alle bereichsbezogenen Benutzer müssen eine entsprechende Lizenz für die MDM verfügen. Wenn die MDM-Registrierung in diesem Szenario fehlschlägt, wird auch für die Azure AD-Einbindung ein Rollback ausgeführt.
- **Der Benutzer befindet sich nicht im MDM-Bereich**: Wenn Benutzer nicht im MDM-Bereich enthalten sind, wird die Azure AD-Einbindung ohne MDM-Registrierung abgeschlossen. Dies führt zu einem nicht verwalteten Gerät.

### <a name="mdm-urls"></a>MDM-URLs

Es gibt drei URLs, die sich auf die MDM-Konfiguration beziehen:

- URL für MDM-Nutzungsbedingungen
- URL für MDM-Ermittlung 
- MDM Compliance-URL

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Screenshot: Teil des MDM-Konfigurationsbereichs in Azure Active Directory mit den URL-Feldern für MDM-Nutzungsbedingungen, Ermittlung und Compliance" border="false":::

Jede URL verfügt über einen vordefinierten Standardwert. Wenn diese Felder leer sind, wenden Sie sich an Ihren MDM-Anbieter, um weitere Informationen zu erhalten.

### <a name="mam-settings"></a>MAM-Einstellungen

MAM gilt nicht für Azure AD Join. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurieren von Enterprise State Roaming

Wenn Sie Enterprise State Roaming in Azure AD aktivieren möchten, damit Benutzer ihre Einstellungen geräteübergreifend synchronisieren können, lesen Sie [Aktivieren von Enterprise State Roaming in Azure Active Directory](enterprise-state-roaming-enable.md). 

**Empfehlung**: Aktivieren Sie diese Einstellung auch für über Azure AD Hybrid eingebundene Geräte.

## <a name="configure-conditional-access"></a>Konfigurieren des bedingten Zugriffs

Wenn für Ihre in Azure AD eingebundenen Geräte ein MDM-Anbieter konfiguriert ist, kennzeichnet der Anbieter das Gerät als konform, sobald das Gerät verwaltet wird. 

![Kompatibles Gerät](./media/azureadjoin-plan/46.png)

Sie können diese Implementierung nutzen, um [die Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs vorzuschreiben](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einbinden eines neuen Windows 10-Geräts in Azure AD auf der Windows-Willkommensseite](azuread-joined-devices-frx.md)
> [Einbinden von geschäftlichen Geräten in das Netzwerk der Organisation](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
