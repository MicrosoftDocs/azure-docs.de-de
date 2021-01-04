---
title: Problem beim Installieren des Anwendungsproxy-Agent-Connectors
description: Beheben von Problemen, die beim Installieren des Anwendungsproxy-Agent-Connectors für Azure Active Directory auftreten können.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0b7fee330f93097b561714ecc938eaf3fee8f2b5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657328"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem beim Installieren des Anwendungsproxy-Agent-Connectors

Der Microsoft Azure Active Directory-Anwendungsproxyconnector ist eine interne Domänenkomponente, die über ausgehende Verbindungen die Konnektivität zwischen dem von der Cloud zur Verfügung gestellten Endpunkt und der internen Domäne einrichtet.

## <a name="general-problem-areas-with-connector-installation"></a>Allgemeine Problembereiche bei der Installation des Connectors

Wenn bei der Installation eines Connectors ein Fehler auftritt, entstammt die Hauptursache in der Regel einem der folgenden Bereiche:

1.  **Konnektivität**: Zum Abschluss einer erfolgreichen Installation muss der neue Connector zukünftige Eigenschaften der Vertrauensstellung registrieren und einrichten. Dies geschieht durch Herstellen einer Verbindung mit dem Azure Active Directory-Anwendungsproxy-Clouddienst.

2.  **Einrichtung einer Vertrauensstellung**: Der neue Connector erstellt ein selbstsigniertes Zertifikat und registriert es beim Clouddienst.

3.  **Authentifizierung des Administrators**: Während der Installation muss der Benutzer Anmeldeinformationen für den Administrator bereitstellen, um die Connectorinstallation abzuschließen.

> [!NOTE]
> Die Connectorinstallationsprotokolle befinden sich im Ordner „%TEMP%“ und können zusätzliche Informationen zur Ursache eines Installationsfehlers bieten.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Überprüfen der Konnektivität mit dem Cloudanwendungsproxy-Dienst und der Microsoft-Anmeldeseite

**Ziel**: Überprüfen Sie, ob der Connectorcomputer eine Verbindung mit dem Registrierungsendpunkt des Anwendungsproxys und der Microsoft-Anmeldeseite herstellen kann.

1.  Führen Sie auf dem Connectorserver einen Porttest aus, indem Sie [telnet](/windows-server/administration/windows-commands/telnet) oder ein anderes Tool zum Testen von Ports verwenden, um zu überprüfen, ob die Ports 443 und 80 geöffnet sind.

2.  Wenn einer dieser Ports nicht verfügbar ist, überprüfen Sie, ob die Firewall oder der Back-End-Proxy Zugriff auf die erforderlichen Domänen und Ports hat. Weitere Informationen hierzu finden Sie unter [Vorbereiten Ihrer lokalen Umgebung](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Öffnen Sie einen Browser (separate Registerkarte), und wechseln Sie zur folgenden Webseite: `https://login.microsoftonline.com`. Stellen Sie sicher, dass Sie sich bei dieser Seite anmelden können.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Überprüfen der Unterstützung für das Vertrauensstellungszertifikat des Anwendungsproxys durch Computer und Back-End-Komponenten

**Ziel**: Vergewissern Sie sich, dass Connectorcomputer, Back-End-Proxy und Firewall das Zertifikat unterstützen können, das vom Connector für die zukünftige Vertrauensstellung erstellt wurde, und dass dieses Zertifikat gültig ist.

>[!NOTE]
>Der Connector versucht ein SHA512-Zertifikat zu erstellen, das von TLS1.2 unterstützt wird. Wenn TLS 1.2 vom Computer oder von der Back-End-Firewall und dem Proxy nicht unterstützt wird, tritt bei der Installation ein Fehler auf.
>
>

**Überprüfen der erforderlichen Voraussetzungen:**

1.  Überprüfen Sie, ob der Computer TLS 1.2 unterstützt – Alle Windows-Versionen nach 2012 R2 sollten TLS 1.2 unterstützen. Wenn für Ihren Connectorcomputer eine Version von 2012 R2 oder früher verwendet wird, sollten Sie sicherstellen, dass die folgenden KBs darauf installiert sind: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>.

2.  Wenden Sie sich an Ihren Netzwerkadministrator, damit der Back-End-Proxy und die Firewall überprüft werden, damit sie SHA512 nicht für ausgehenden Datenverkehr blockieren.

**So überprüfen Sie das Clientzertifikat**

Überprüfen Sie den Fingerabdruck des aktuellen Clientzertifikats. Der Zertifikatspeicher befindet sich in `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml`.

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Die möglichen **IsInUserStore**-Werte lauten **true** und **false**. Der Wert **true** bedeutet: Das automatisch erneuerte Zertifikat wird im persönlichen Container im Benutzerzertifikatspeicher des Netzwerkdiensts gespeichert. Der Wert **false** bedeutet: Das Clientzertifikat wurde während der Installation oder Registrierung erstellt, die durch den Befehl „Register-AppProxyConnector“ initiiert wurde, und es wird im persönlichen Container im Zertifikatspeicher des lokalen Computers gespeichert.

Wenn der Wert **true** lautet, führen Sie zur Überprüfung des Zertifikats die folgenden Schritte aus:
1. Laden Sie [PsTools.zip](/sysinternals/downloads/pstools) herunter.
2. Extrahieren Sie [PsExec](/sysinternals/downloads/psexec) aus dem Paket, und führen Sie **psexec -i -u "nt authority\network service" cmd.exe** an einer Eingabeaufforderung mit erhöhten Rechten aus.
3. Führen Sie **certmgr.msc** in der neu angezeigten Eingabeaufforderung aus.
4. Erweitern Sie in der Verwaltungskonsole den persönlichen Container, und klicken Sie auf „Zertifikate“.
5. Suchen Sie das Zertifikat, das von **connectorregistrationca.msappproxy.net** ausgestellt wurde.

Wenn der Wert **false** lautet, führen Sie zur Überprüfung des Zertifikats die folgenden Schritte aus:
1. Führen Sie **certlm.msc** aus.
2. Erweitern Sie in der Verwaltungskonsole den persönlichen Container, und klicken Sie auf „Zertifikate“.
3. Suchen Sie das Zertifikat, das von **connectorregistrationca.msappproxy.net** ausgestellt wurde.

**So erneuern Sie das Clientzertifikat**

Wenn ein Connector über mehrere Monate hinweg keine Verbindung mit dem Dienst herstellt, sind die Zertifikate möglicherweise veraltet. Der Fehler bei der Zertifikaterneuerung führt zu einem abgelaufenen Zertifikat. Dadurch funktioniert wiederum der Connectordienst nicht mehr. Das Ereignis 1000 wird im Verwaltungsprotokoll des Connectors aufgezeichnet:

„Fehler bei der erneuten Connectorregistrierung: Das Zertifikat der Connectorvertrauensstellung ist abgelaufen. Führen Sie das PowerShell-Cmdlet Register-AppProxyConnector auf dem Computer aus, auf dem der Connector ausgeführt wird, um den Connector erneut zu registrieren.“

In diesem Fall müssen Sie den Connector deinstallieren und neu installieren, um die Registrierung auszulösen, oder Sie führen die folgenden PowerShell-Befehle aus:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Weitere Informationen zum Befehl Register-AppProxyConnector finden Sie unter [Erstellen eines Skripts für die unbeaufsichtigte Installation für den Azure AD-Anwendungsproxyconnector](./application-proxy-register-connector-powershell.md).

## <a name="verify-admin-is-used-to-install-the-connector"></a>Überprüfen, dass der Connector vom Administrator installiert wird

**Ziel**: Überprüfen Sie, ob der Benutzer, der versucht, den Connector zu installieren, ein Administrator mit den richtigen Anmeldeinformationen ist. Derzeit muss der Benutzer mindestens über die Rolle „Anwendungsadministrator“ verfügen, damit die Installation erfolgreich ausgeführt werden kann.

**So überprüfen Sie, ob die Anmeldeinformationen korrekt sind:**

Stellen Sie eine Verbindung mit `https://login.microsoftonline.com` her, und verwenden Sie die gleichen Anmeldeinformationen. Überprüfen Sie, ob die Anmeldung erfolgreich war. Sie können die Benutzerrolle überprüfen, indem Sie zu **Azure Active Directory** -&gt; **Benutzer und Gruppen** -&gt; **Alle Benutzer** navigieren. 

Wählen Sie Ihr Benutzerkonto aus, und klicken Sie dann im daraufhin angezeigten Menü auf „Verzeichnisrolle“. Stellen Sie sicher, dass die Rolle „Anwendungsadministrator“ ausgewählt ist. Wenn Sie in diesen Schritten nicht auf die Seiten zugreifen können, verfügen Sie nicht über die erforderliche Rolle.

## <a name="next-steps"></a>Nächste Schritte
[Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)