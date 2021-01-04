---
title: 'Tutorial: Integrieren einer einzelnen Gesamtstruktur in einen einzelnen Azure AD-Mandanten'
description: In diesem Thema werden die Voraussetzungen und die Hardwareanforderungen für die Cloudbereitstellung beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad5147971fc42e65e4621c8a3f0a98e01f2e0339
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237294"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Tutorial: Integrieren einer einzelnen Gesamtstruktur in einen einzelnen Azure AD-Mandanten

In diesem Tutorial werden Sie durch die Schritte zum Erstellen einer Hybrididentitätsumgebung mithilfe der Cloudbereitstellung von Azure Active Directory (Azure AD) Connect geführt.

![Erstellen](media/tutorial-single-forest/diagram1.png)

Die Umgebung, die Sie in diesem Tutorial erstellen, können Sie zu Testzwecken verwenden oder um sich besser mit der Cloudbereitstellung vertraut zu machen.

## <a name="prerequisites"></a>Voraussetzungen
### <a name="in-the-azure-active-directory-admin-center"></a>Im Azure Active Directory Admin Center

1. Erstellen Sie in Ihrem Azure AD-Mandanten ein auf die Cloud beschränktes globales Administratorkonto. Auf diese Weise können Sie die Konfiguration Ihres Mandanten verwalten, falls Ihre lokalen Dienste ausfallen oder nicht verfügbar sind. Erfahren Sie, wie Sie ein [rein cloudbasiertes Konto für den globalen Administrator hinzufügen](../fundamentals/add-users-azure-active-directory.md). Die Ausführung dieses Schritts ist sehr wichtig, damit sichergestellt ist, dass Sie für Ihren Mandanten nicht gesperrt werden.
2. Fügen Sie Ihrem Azure AD-Mandanten mindestens einen [benutzerdefinierten Domänennamen](../fundamentals/add-custom-domain.md) hinzu. Ihre Benutzer können sich mit einem dieser Domänennamen anmelden.

### <a name="in-your-on-premises-environment"></a>In Ihrer lokalen Umgebung

1. Geben Sie einen in die Domäne eingebundenen Hostserver unter Windows Server 2012 R2 oder höher mit mindestens 4 GB RAM und .NET 4.7.1 + Runtime an. 

2. Wenn zwischen Ihren Servern und Azure AD eine Firewall eingerichtet wurde, konfigurieren Sie die folgenden Elemente:
   - Stellen Sie sicher, dass Agents über die folgenden Ports *ausgehende* Anforderungen an Azure AD senden können:

     | Portnummer | Wie diese verwendet wird |
     | --- | --- |
     | **80** | Herunterladen der Zertifikatsperrlisten (Certificate Revocation Lists, CRLs) bei der Überprüfung des TLS/SSL-Zertifikats |
     | **443** | Verarbeitung der gesamten ausgehende Kommunikation mit dem Dienst |
     | **8082** | Erforderlich für die Installation|
     | **8080** (optional) | Agents melden ihren Status alle zehn Minuten über den Port 8080, wenn der Port 443 nicht verfügbar ist. Dieser Status wird im Azure AD-Portal angezeigt. |
     
     Wenn Ihre Firewall Regeln gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.
   - Wenn Ihre Firewall oder Ihr Proxy das Angeben sicherer Suffixe zulässt, fügen Sie Verbindungen zu **\*.msappproxy.net** und **\*.servicebus.windows.net** hinzu. Aktivieren Sie andernfalls den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653), die wöchentlich aktualisiert werden.
   - Ihre Agents benötigen für die Erstregistrierung Zugriff auf **login.windows.net** und **login.microsoftonline.com**. Öffnen Sie Ihre Firewall auch für diese URLs.
   - Geben Sie für die Überprüfung des Zertifikats folgende URLs frei: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** und **www\.microsoft.com:80**. Da diese URLs für die Überprüfung des Zertifikats in Verbindung mit anderen Microsoft-Produkten verwendet werden, haben Sie diese möglicherweise bereits freigegeben.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installieren des Azure AD Connect-Bereitstellungs-Agents
1. Melden Sie sich bei dem in die Domäne eingebundenen Server an.  Wenn Sie das Tutorial [Grundlegende AD- und Azure-Umgebung](tutorial-basic-ad-azure.md) verwenden, ist dies der Server „DC1“.
2. Melden Sie sich mit den Anmeldeinformationen eines rein cloudbasierten globalen Administratorkontos beim Azure-Portal an.
3. Wählen Sie auf der linken Seite die Option **Azure Active Directory** aus, klicken Sie auf **Azure AD Connect**, und wählen Sie in der Mitte die Option **Bereitstellung verwalten (Vorschau)** aus.

   ![Azure-Portal](media/how-to-install/install-6.png)

4. Klicken Sie auf **Agent herunterladen**.
5. Führen Sie den Azure AD Connect-Bereitstellungs-Agent aus.
6. **Akzeptieren** Sie auf dem Begrüßungsbildschirm die Lizenzbedingungen, und klicken Sie auf **Installieren**.

   ![Screenshot: Begrüßungsbildschirm „Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket“](media/how-to-install/install-1.png)

7. Nach Abschluss dieses Vorgangs wird der Konfigurations-Assistent gestartet.  Melden Sie sich mit dem Konto Ihres globalen Azure AD-Administrators an.  Beachten Sie, dass die Anmeldung blockiert wird, wenn Sie die verstärkte Sicherheitskonfiguration für IE aktiviert haben.  Schließen Sie in diesem Fall die Installation, deaktivieren Sie die verstärkte Sicherheitskonfiguration für IE in Server-Manager, und klicken Sie auf den **Assistenten für den AAD Connect-Bereitstellungs-Agent**, um die Installation neu zu starten.
8. Klicken Sie auf dem Bildschirm **Active Directory verbinden** auf **Verzeichnis hinzufügen**, und melden Sie sich dann mit Ihrem Active Directory-Domänenadministratorkonto an.  HINWEIS:  Für das Domänenadministratorkonto sollten keine Anforderungen zum Ändern des Kennworts gelten. Wenn das Kennwort abläuft oder geändert wird, müssen Sie den Agent mit den neuen Anmeldeinformationen neu konfigurieren. Dadurch wird Ihr lokales Verzeichnis hinzugefügt.  Klicken Sie auf **Weiter**.

   ![Screenshot: Bildschirm „Active Directory verbinden“](media/how-to-install/install-3a.png)

9. Klicken Sie auf dem Bildschirm **Konfiguration abgeschlossen** auf **Bestätigen**.  Dadurch wird der Agent registriert und neu gestartet.

   ![Screenshot: Bildschirm „Konfiguration abgeschlossen“](media/how-to-install/install-4a.png)

10. Sobald dieser Vorgang abgeschlossen ist, sollte der folgende Hinweis angezeigt werden: **Ihre Agent-Konfiguration wurde erfolgreich überprüft.**  Sie können dann auf **Beenden** klicken.</br>
![Bildschirm „Willkommen“](media/how-to-install/install-5.png)</br>
11. Falls der erste Begrüßungsbildschirm weiterhin angezeigt wird, klicken Sie auf **Schließen**.


## <a name="verify-agent-installation"></a>Überprüfen der Agent-Installation
Die Agent-Überprüfung erfolgt im Azure-Portal und auf dem lokalen Server, auf dem der Agent ausgeführt wird.

### <a name="azure-portal-agent-verification"></a>Agent-Überprüfung im Azure-Portal
Führen Sie die folgenden Schritte aus, um zu überprüfen, ob der Agent von Azure erkannt wird:

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie auf der linken Seite **Azure Active Directory** aus, klicken Sie auf **Azure AD Connect**, und wählen Sie im mittleren Bereich **Bereitstellung verwalten (Vorschau)** aus.</br>
![Azure portal](media/how-to-install/install-6.png)</br>

3.  Klicken Sie auf dem Bildschirm **Azure AD-Bereitstellung (Vorschau)** auf **Alle Agents überprüfen**.
![Azure AD-Bereitstellung](media/how-to-install/install-7.png)</br>
 
4. Auf dem Bildschirm **On-premises provisioning agents** (Lokale Bereitstellungs-Agents) werden die von Ihnen installierten Agents angezeigt.  Vergewissern Sie sich, dass der betreffende Agent aufgeführt wird und als **Aktiviert** markiert ist.
![Bereitstellungs-Agents](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Auf dem lokalen Server
Führen Sie die folgenden Schritte aus, um sicherzustellen, dass der Agent ausgeführt wird:

1.  Melden Sie sich beim Server mit einem Administratorkonto an.
2.  Öffnen Sie die Seite **Dienste**, indem Sie zu dieser Seite oder zu „Start/Run/Services.msc“ navigieren.
3.  Vergewissern Sie sich, dass unter **Dienste** die Dienste **Microsoft Azure AD Connect Agent Updater** und **Microsoft Azure AD Connect-Bereitstellungs-Agent** angezeigt werden und ihr Status **Wird ausgeführt** lautet.
![Dienste](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurieren der Azure AD Connect-Cloudbereitstellung
 Führen Sie die folgenden Schritte aus, um die Bereitstellung zu konfigurieren:

1.  Melden Sie sich beim Azure AD-Portal an.
2.  Klicken Sie auf **Azure Active Directory**.
3.  Klicken Sie auf **Azure AD Connect**.
4.  Wählen Sie **Bereitstellung verwalten (Vorschau)** 
![Screenshot mit dem Link „Bereitstellung verwalten (Vorschau)“ aus.](media/how-to-configure/manage1.png)
5.  Klicken Sie auf **Neue Konfiguration**
![Screenshot des Bildschirms „Azure AD-Bereitstellung (Vorschau)“ mit hervorgehobenem Link „Neue Konfiguration“.](media/tutorial-single-forest/configure1.png)
7.  Geben Sie im Konfigurationsbildschirm eine **E-Mail-Adresse für Benachrichtigungen** ein, verschieben Sie den Selektor auf **Aktivieren**, und klicken Sie auf **Speichern**.
![Screenshot des Bildschirms „Konfigurieren“ mit ausgefüllter Benachrichtigungs-E-Mail und ausgewählter Option „Aktivieren“](media/how-to-configure/configure2.png)
1.  Der Konfigurationsstatus sollte jetzt **Fehlerfrei** lauten.
![Screenshot des Bildschirms „Azure AD-Bereitstellung (Vorschau)“, der den Status „Fehlerfrei“ anzeigt](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Überprüfen, ob Benutzer erstellt wurden und die Synchronisierung erfolgt
Überprüfen Sie als Nächstes, ob die Benutzer, die in unserem lokalen Verzeichnis enthalten waren, synchronisiert wurden und jetzt in unserem Azure AD-Mandanten vorhanden sind.  Seien Sie sich im Klaren darüber, dass dies einige Stunden dauern kann.  Gehen Sie wie folgt vor, um zu überprüfen, ob Benutzer synchronisiert werden:


1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit einem Konto an, das über ein Azure-Abonnement verfügt.
2. Wählen Sie im linken Bereich **Azure Active Directory** aus.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4. Überprüfen Sie, ob die neuen Benutzer in unserem Mandanten angezeigt werden.</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testanmeldung mit einem unserer Benutzer

1. Rufen Sie [https://myapps.microsoft.com](https://myapps.microsoft.com) auf.
2. Melden Sie sich mit einem Benutzerkonto an, das in unserem neuen Mandanten erstellt wurde.  Sie müssen sich mit folgendem Format anmelden: (user@domain.onmicrosoft.com). Verwenden Sie dasselbe Kennwort, mit dem sich der Benutzer lokal anmeldet.</br>
   ![Überprüfen](media/tutorial-single-forest/verify-1.png)</br>

Sie haben nun erfolgreich eine Hybrididentitätsumgebung eingerichtet, die Sie zum Testen verwenden können, und um sich mit den Möglichkeiten von Azure vertraut zu machen.


## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)