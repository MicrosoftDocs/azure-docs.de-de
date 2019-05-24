---
title: Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Behandelt die Grundlagen zur Integration eines lokalen SharePoint-Servers mit Azure AD-Anwendungsproxy.
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
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e491f0c452b7b51eac4e8cccab1cc7ed8430e49
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783444"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy

In diesem Artikel wird beschrieben, wie Sie einen lokalen SharePoint-Server mit dem Azure Active Directory (Azure AD)-Anwendungsproxy integrieren.

Führen Sie die Abschnitte in diesem Artikel Schritt für Schritt aus, um den Remotezugriff auf SharePoint mit dem Azure AD-Anwendungsproxy zu aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie in Ihrer Umgebung bereits über SharePoint 2013 oder höher verfügen. Beachten Sie auch die folgenden erforderlichen Voraussetzungen:

* SharePoint enthält native Kerberos-Unterstützung. Aus diesem Grund können Benutzer, die über den Azure AD-Anwendungsproxy per Remoteverbindung auf interne Websites zugreifen, davon ausgehen, dass sie das einmalige Anmelden (Single Sign-On, SSO) nutzen können.

* Dieses Szenario umfasst einige Konfigurationsänderungen an Ihrer SharePoint Server-Instanz. Wir empfehlen Ihnen die Verwendung einer Stagingumgebung. Auf diese Weise können Sie zuerst Updates an Ihrem Stagingserver vornehmen und dann einen Testzyklus durchführen, bevor die Produktionsphase beginnt.

* Für die veröffentlichte URL ist SLL erforderlich. SSL ist auch für die interne URL erforderlich, um sicherzustellen, dass Links richtig gesendet/zugeordnet werden.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Schritt 1: Konfigurieren der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD)

Für lokale Anwendungen, für die die Windows-Authentifizierung verwendet wird, können Sie einmaliges Anmelden (SSO) über das Kerberos-Authentifizierungsprotokoll und das Feature „Eingeschränkte Kerberos-Delegierung“ (Kerberos Constrained Delegation, KCD) erreichen. Bei Konfiguration von KCD kann mit dem Anwendungsproxyconnector ein Windows-Token für einen Benutzer abgerufen werden. Dies gilt auch, wenn sich der Benutzer nicht direkt bei Windows angemeldet hat. Weitere Informationen zu KCD finden Sie unter [Eingeschränkte Kerberos-Delegierung: Übersicht](https://technet.microsoft.com/library/jj553400.aspx).

Um KCD für einen SharePoint-Server einzurichten, verwenden Sie die Verfahren in den folgenden Abschnitten:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Sicherstellen, dass die SharePoint-Webanwendung unter einem Domänenkonto ausgeführt wird

Stellen Sie zuerst sicher, dass die SharePoint-Webanwendung unter einem Domänenkonto ausgeführt wird und nicht unter einem lokalen System, lokalen Dienst oder Netzwerkdienst. Dies ist erforderlich, damit Sie diesem Konto die Dienstprinzipalnamen (Service Principal Names, SPNs) zuordnen können. Mithilfe von SPNs können vom Kerberos-Protokoll verschiedene Dienste identifiziert werden. Außerdem wird das Konto für die spätere Konfiguration von KCD benötigt.

> [!NOTE]
> Sie müssen über ein vorab erstelltes Azure AD-Konto für den Dienst verfügen. Es ist ratsam, die automatische Kennwortänderung zuzulassen. Weitere Informationen zu allen Schritten und zur Problembehandlung finden Sie unter [Konfigurieren der automatischen Kennwortänderung in SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Führen Sie die folgenden Schritte aus, um sicherzustellen, dass Ihre Websites unter einem definierten Dienstkonto ausgeführt werden:

1. Öffnen Sie die Website der **SharePoint-Zentraladministration**.
2. Navigieren Sie zu **Sicherheit**, und wählen Sie die Option **Dienstkonten konfigurieren**.
3. Wählen Sie **Webanwendungspool – SharePoint – 80**. Die Optionen können je nach Name Ihres Webpools oder bei der standardmäßigen Verwendung von SSL leicht variieren.

   ![Optionen für das Konfigurieren eines Dienstkontos](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Wenn das Feld **Wählen Sie ein Konto für diese Komponente aus** auf **Lokaler Dienst** oder **Netzwerkdienst** festgelegt ist, müssen Sie ein Konto erstellen. Wenn nicht, müssen Sie nichts mehr tun und können mit dem nächsten Abschnitt fortfahren.
5. Wählen Sie die Option **Neues verwaltetes Konto registrieren**. Nachdem das Konto erstellt wurde, müssen Sie den **Webanwendungspool** festlegen, bevor Sie das Konto verwenden können.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Festlegen eines Dienstprinzipalnamens für das SharePoint-Dienstkonto

Vor dem Konfigurieren von KCD müssen Sie die folgenden Aktionen ausführen:

* Identifizieren Sie das Domänenkonto, unter dem die SharePoint-Webanwendung ausgeführt wird, die der Azure AD-Anwendungsproxy verfügbar macht.
* Wählen Sie eine interne URL aus, die im Azure AD-Anwendungsproxy und in SharePoint konfiguriert wird. Diese interne URL darf nicht bereits in der Webanwendung verwendet werden und wird nie im Webbrowser angezeigt.

Wenn <https://sharepoint> als interne URL ausgewählt wird, lautet der SPN wie folgt:

```
HTTP/SharePoint
```

> [!NOTE]
> Beachten Sie die folgenden Empfehlungen für die interne URL:
> * Verwenden Sie HTTPS.
> * Verwenden Sie keine benutzerdefinierten Ports.
> * Erstellen Sie im DNS einen Host (A), der auf SharePoint WFE verweist (oder einen Lastenausgleich) und keinen Alias (CName).

Führen Sie zum Registrieren dieses SPN den folgenden Befehl an der Eingabeaufforderung als Administrator der Domäne aus:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Mit diesem Befehl wird der SPN _HTTP/SharePoint_ für das SharePoint-Anwendungspoolkonto _demo\spAppPoolAccount_ festgelegt.

Ersetzen Sie _HTTP/SharePoint_ durch den SPN für die interne URL und _demo\spAppPoolAccount_ durch das Anwendungspoolkonto in Ihrer Umgebung. Mit dem Befehl „setspn“ wird nach dem SPN gesucht, bevor er hinzugefügt wird. Wenn er bereits vorhanden ist, wird der Fehler **Doppelter SPN-Wert** angezeigt. In diesem Fall sollten Sie den vorhandenen SPN entfernen, wenn er nicht unter dem richtigen Anwendungspoolkonto festgelegt ist.

Sie können überprüfen, ob der SPN hinzugefügt wurde, indem Sie den Befehl „setspn“ mit der Option „-L“ ausführen. Weitere Informationen zu diesem Befehl finden Sie unter [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Sicherstellen, dass dem Connector bei Delegierungen des dem SharePoint-Anwendungspoolkonto hinzugefügten SPN vertraut wird

Konfigurieren Sie KCD so, dass der Azure AD-Anwendungsproxydienst Benutzeridentitäten an das SharePoint-Anwendungspoolkonto delegieren kann. Sie konfigurieren KCD, indem Sie den Anwendungsproxyconnector so einrichten, dass er Kerberos-Tickets für Benutzer abruft, die in Azure AD authentifiziert wurden. Anschließend übergibt dieser Server den Kontext an die Zielanwendung bzw. in diesem Fall an SharePoint.

Wiederholen Sie zum Konfigurieren von KCD die folgenden Schritte für jeden Connectorcomputer:

1. Melden Sie sich als Domänenadministrator an einem Domänencontroller an, und öffnen Sie dann **Active Directory-Benutzer und -Computer**.
2. Suchen Sie nach dem Computer, auf dem der Connector ausgeführt wird. In diesem Beispiel ist dies derselbe SharePoint-Server.
3. Doppelklicken Sie auf den Computer, und klicken Sie anschließend auf die Registerkarte **Delegierung**.
4. Stellen Sie sicher, dass in den Delegierungseinstellungen **Computer bei Delegierungen angegebener Dienste vertrauen** festgelegt ist. Wählen Sie dann die Option **Beliebiges Authentifizierungsprotokoll verwenden** aus.
5. Klicken Sie auf die Schaltfläche **Hinzufügen**, klicken Sie auf **Benutzer oder Computer**, und suchen Sie das SharePoint-Anwendungspoolkonto, z. B. _demo\spAppPoolAccount_.
6. Wählen Sie in der Liste der SPNs denjenigen aus, den Sie zuvor für das Dienstkonto erstellt haben.
7. Klicken Sie auf **OK**. Klicken Sie erneut auf **OK**, um die Änderung zu speichern.
  
   ![Delegierungseinstellungen](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Schritt 2: Konfigurieren des Azure AD-Anwendungsproxys

Nachdem Sie KCD konfiguriert haben, können Sie nun den Azure AD-Anwendungsproxy konfigurieren.

1. Veröffentlichen Sie die SharePoint-Website mit den folgenden Einstellungen. Eine ausführliche Anleitung finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-publish-azure-portal.md).
   * **Interne URL**: Die interne SharePoint-URL, die zuvor ausgewählt wurde, z. B. **<https://SharePoint/>**.
   * **Methode für die Vorauthentifizierung**: Azure Active Directory
   * **URL in Headern übersetzen**: NO

   >[!TIP]
   >SharePoint verwendet den Wert _Hostheader_, um die Website zu suchen. Es generiert auch Links auf der Grundlage dieses Werts. Hiermit wird Folgendes erreicht: Bei allen von SharePoint generierten Links handelt es sich um eine veröffentlichte URL, für die die Verwendung der externen URL korrekt festgelegt ist. Wenn der Wert auf **JA** festgelegt wird, kann der Connector die Anforderung auch an die Back-End-Anwendung weiterleiten. Wenn der Wert jedoch auf **NEIN** festgelegt wird, bedeutet dies, dass der Connector den internen Hostnamen nicht sendet. Stattdessen sendet der Connector den Hostheader als veröffentlichte URL an die Back-End-Anwendung.

   ![Veröffentlichen von SharePoint als Anwendung](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Nachdem die App veröffentlicht wurde, konfigurieren Sie die Einstellungen für einmaliges Anmelden anhand der folgenden Schritte:

   1. Wählen Sie auf der Anwendungsseite im Portal **Einmaliges Anmelden** aus.
   2. Legen Sie den Modus für einmaliges Anmelden auf **Integrierte Windows-Authentifizierung** fest.
   3. Legen Sie „Interner Anwendungs-SPN“ auf den zuvor festgelegten Wert fest. In diesem Beispiel wäre das **HTTP/SharePoint**.
   4. Wählen Sie im Feld „Delegierte Identität für Anmeldung“ den Eintrag **Name des lokalen SAM-Kontos** aus.

   ![Konfigurieren der integrierten Windows-Authentifizierung für einmaliges Anmelden](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Zum Abschluss der Anwendungseinrichtung wechseln Sie zum Abschnitt **Benutzer und Gruppen**. Weisen Sie dort Benutzer zu, die Zugriff auf diese Anwendung erhalten. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Schritt 3: Konfigurieren von SharePoint für die Verwendung von Kerberos und Azure AD-Anwendungsproxy-URLs

Der nächste Schritt besteht darin, die SharePoint-Webanwendung auf eine neue Zone zu erweitern, die mit Kerberos und der entsprechenden alternativen Zugriffszuordnung konfiguriert wurde, damit SharePoint an die interne URL gesendete eingehende Anforderungen verarbeiten und mit Links antworten kann, die für die externe URL erstellt wurden.

1. Starten Sie die **SharePoint-Verwaltungsshell**.
2. Führen Sie das folgende Skript aus, um die Webanwendung auf die Zone „Extranet“ zu erweitern und die Kerberos-Authentifizierung zu aktivieren:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. Öffnen Sie die Website der **SharePoint-Zentraladministration**.
4. Wählen Sie unter **Systemeinstellungen** die Option **Alternative Zugriffszuordnungen konfigurieren**. Das Feld „Alternative Zugriffszuordnungen“ wird geöffnet.
5. Wählen Sie Ihre Website aus, z.B. **SharePoint – 80**. Im Moment ist die interne URL für die Zone „Extranet“ noch nicht ordnungsgemäß festgelegt:

   ![Feld „Alternative Zugriffszuordnungen“](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Klicken Sie auf **Interne URLs hinzufügen**.
7. Geben Sie im Textfeld **URL-Protokoll, Host und Port** die im Azure AD-Anwendungsproxy konfigurierte **Interne URL** ein, z. B. <https://SharePoint/>.
8. Wählen Sie in der Dropdownliste die Zone **Extranet** aus.
9. Klicken Sie auf **Speichern**.
10. Die alternativen Zugriffszuordnungen sollten jetzt wie folgt aussehen:

    ![Korrekte alternative Zugriffszuordnungen](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Schritt 4: Sicherstellen, dass ein HTTPS-Zertifikat für die IIS-Website der Zone „Extranet“ konfiguriert ist

Die SharePoint-Konfiguration ist jetzt abgeschlossen, aber da die interne URL der Zone „Extranet“ <https://SharePoint/> lautet, muss ein Zertifikat für diese Website festgelegt werden.

1. Öffnen Sie die Windows PowerShell-Konsole.
2. Führen Sie das folgende Skript aus, um ein selbstsigniertes Zertifikat zu generieren und es dem Computerzertifikatspeicher „My“ hinzuzufügen:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Selbstsignierte Zertifikate sind nur für Testzwecke geeignet. In Produktionsumgebungen wird dringend empfohlen, stattdessen von einer Zertifizierungsstelle ausgestellte Zertifikate zu verwenden.

3. Öffnen Sie die Konsole „Internetinformationsdienste-Manager“.
4. Erweitern Sie den Server in der Strukturansicht, erweitern Sie „Websites“, wählen Sie die Website „SharePoint – AAD-Proxy“ aus, und klicken Sie dann auf **Bindungen**.
5. Wählen Sie „HTTPS-Bindung“ aus, und klicken Sie auf **Bearbeiten**.
6. Wählen Sie im Feld „SSL-Zertifikat“ das **SharePoint**-Zertifikat aus, und klicken Sie auf „OK“.

Sie können auf die SharePoint-Website jetzt extern über den Azure AD-Anwendungsproxy zugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md)
* [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)
