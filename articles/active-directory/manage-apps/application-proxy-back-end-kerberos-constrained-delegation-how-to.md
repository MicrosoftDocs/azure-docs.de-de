---
title: Problembehandlung der eingeschränkten Kerberos-Delegierung – Anwendungsproxy
description: Problembehandlung von Konfigurationen der eingeschränkten Kerberos-Delegierung für den Anwendungsproxy
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: c28e79c9a6f8c489a97d360c4fe142d431b5ab5d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656546"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Problembehandlung von Konfigurationen der eingeschränkten Kerberos-Delegierung für den Anwendungsproxy

Die verfügbaren Methoden zum Erreichen von einmaligem Anmelden bei veröffentlichten Anwendungen können sich zwischen den verschiedenen Anwendungen voneinander unterscheiden. Eine Option, die im Azure AD-Anwendungsproxy (Azure Active Directory) standardmäßig verfügbar ist, ist die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD). Sie können einen Connector für Ihre Benutzer konfigurieren, um die eingeschränkte Kerberos-Authentifizierung für Back-End-Anwendungen auszuführen.

Die Vorgehensweise zum Aktivieren der KCD ist unkompliziert. Es wird lediglich ein grundlegendes Verständnis über die verschiedenen Komponenten und den Authentifizierungsablauf benötigt, die einmaliges Anmelden unterstützen. In manchen Fällen funktioniert das einmalige Anmelden mit der KCD jedoch nicht wie erwartet. Sie benötigen gute Informationsquellen zur Problembehandlung bei diesen Szenarien.

In diesem Artikel wird ein zentraler Anlaufpunkt bereitgestellt, mit dem einige der häufigsten Probleme selbst behoben werden können. Zudem wird die Diagnose von komplexeren Implementierungsproblemen erläutert.

Dieser Artikel geht von den folgenden Annahmen aus:

- Die Bereitstellung des Azure AD-Anwendungsproxys gemäß [Erste Schritte mit dem Anwendungsproxy und Installieren des Connectors](application-proxy-add-on-premises-application.md) und der allgemeine Zugriff auf Nicht-KCD-Anwendungen funktionieren wie erwartet.
- Die veröffentlichte Zielanwendung basiert auf den Internetinformationsdiensten (Internet Information Services, IIS) und der Microsoft-Implementierung von Kerberos.
- Die Server- und Anwendungshosts befinden sich in einer einzelnen Azure Active Directory-Domäne. Ausführliche Informationen zu domänen- und gesamtstrukturübergreifenden Szenarien finden Sie im [Whitepaper zur eingeschränkten Kerberos-Delegierung](https://aka.ms/KCDPaper).
- Die betreffende Anwendung wird in einem Azure-Mandanten mit aktivierter Vorauthentifizierung veröffentlicht. Benutzer müssen sich über die formularbasierte Authentifizierung bei Azure authentifizieren. Szenarien mit der Rich-Client-Authentifizierung sind nicht Gegenstand dieses Artikels. Diese werden eventuell zu einem späteren Zeitpunkt behandelt.

## <a name="prerequisites"></a>Voraussetzungen

Der Azure AD-Anwendungsproxy kann in einer Vielzahl von Infrastrukturen oder Umgebungen bereitgestellt werden. Die Architekturen variieren je nach Organisation. Die häufigsten Ursachen für Probleme in Bezug auf die KCD sind nicht auf die Umgebungen zurückzuführen. Vielmehr liegt die Antwort in den meisten Fällen bei einfachen Fehlkonfigurationen oder allgemeinen Fehlern.

Bevor Sie mit der Problembehandlung beginnen, sollten Sie sich aus diesem Grund vergewissern, dass Sie die Voraussetzungen unter [Verwendung des einmaligen Anmeldens mithilfe der KCD mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md) erfüllen. Beachten Sie den Abschnitt zur Konfiguration der eingeschränkten Kerberos-Delegierung unter 2012 R2. Bei diesem Prozess wird eine andere Vorgehensweise zum Konfigurieren der KCD unter älteren Windows-Versionen angewendet. Darüber hinaus sollten Sie folgende Überlegungen berücksichtigen:

- Es ist nicht ungewöhnlich, dass ein Domänenmitgliedsserver einen Dialog mit einem bestimmten Domänencontroller (DC) über einen sicheren Kanal öffnet. Dann kann der Server jederzeit in einen anderen Dialog wechseln. Connectorhosts sind daher nicht nur auf die Kommunikation mit bestimmten DCs am lokalen Standort beschränkt.
- Domänenübergreifende Szenarien beruhen auf Verweisen, die einen Connectorhost zu DCs weiterleiten, die sich möglicherweise außerhalb des lokalen Netzwerkumkreises befinden. In diesen Fällen ist es ebenso wichtig, den Datenverkehr auch an DCs weiterzuleiten, die andere entsprechende Domänen darstellen. Geschieht dies nicht, tritt bei der Delegierung ein Fehler auf.
- Platzieren Sie möglichst keine aktiven IP-Adressen oder IDS-Geräte zwischen den Connectorhosts und DCs. Diese Geräte sind manchmal störend und beeinträchtigen den Hauptdatenverkehr über den RPC.

Testen Sie die Delegierung in einfachen Szenarien. Je mehr Variablen Sie einführen, desto schwieriger kann sich der Vorgang gestalten. Begrenzen Sie die Tests auf einen einzelnen Connector, um Zeit zu sparen. Fügen Sie zusätzliche Connector hinzu, nachdem das Problem gelöst wurde.

Auch Umgebungsfaktoren tragen möglicherweise zu einem Problem bei. Um dies zu verhindern, halten Sie die Architektur während der Tests so gering wie möglich. Ein häufiger Fall sind beispielsweise falsch konfigurierte interne Firewalls und ACLs. Wenn möglich, senden Sie sämtlichen Datenverkehr von einem Connector direkt an die DCs und die Back-End-Anwendung.

Ein Connector sollte am besten so nah wie möglich beim Ziel positioniert werden. Eine Firewall, die sich bei den Tests inline befindet, vergrößert die Komplexität nur unnötig und kann die Dauer Ihrer Prüfungen verlängern.

Wodurch lässt sich ein Problem bei der KCD erkennen? Es gibt einige häufige Anzeichen dafür, dass ein Fehler beim einmaligen Anmelden der KCD vorliegt. Die ersten Anzeichen eines Problems treten im Browser auf.

![Screenshot: Beispiel einer fehlerhaften KCD-Konfiguration mit hervorgehobenem Fehler durch eine falsche eingeschränkte Kerberos-Delegierung.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Beispiel: Fehler bei der Autorisierung aufgrund fehlender Berechtigungen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Beide Abbildungen veranschaulichen das gleiche Symptom: SSO-Fehler. Der Benutzerzugriff auf die Anwendung wurde verweigert.

## <a name="troubleshooting"></a>Problembehandlung

Die Problembehandlung hängt vom Problem sowie den beobachteten Symptomen ab. Bevor Sie fortfahren, lesen Sie die folgenden Artikel. Diese enthalten hilfreiche Informationen zur Problembehandlung:

- [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md)
- [Kerberos-Fehler und die Symptome](application-proxy-troubleshoot.md#kerberos-errors)
- [Umgang mit SSO bei unterschiedlichen lokalen und Cloudidentitäten](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Wenn Sie an diesen Punkt angelangt sind, besteht ein grundsätzliches Problem. Unterteilen Sie den Ablauf für die Problembehandlung zunächst in drei separate Phasen.

### <a name="client-pre-authentication"></a>Clientvorauthentifizierung

Der externe Benutzer authentifiziert sich über einen Browser bei Azure. Die Möglichkeit zur Vorauthentifizierung bei Azure ist für das einmalige Anmelden mit der KCD erforderlich. Testen Sie diese Möglichkeit, und beheben Sie ggf. auftretende Probleme. Die Vorauthentifizierungsphase hängt nicht mit der KCD oder der veröffentlichten Anwendung zusammen. Mögliche Unstimmigkeiten können mühelos behoben werden, indem Sie eine Integritätsprüfung durchführen, ob das betroffene Konto in Azure vorhanden ist. Stellen Sie außerdem sicher, dass es nicht deaktiviert oder blockiert ist. Die Fehlerantwort im Browser ist aussagekräftig genug, um die Ursache nachzuvollziehen. Lesen Sie im Zweifel andere Microsoft-Artikel zur Problembehandlung.

### <a name="delegation-service"></a>Delegierungsdienst

Der Azure-Proxyconnector, der ein Kerberos-Dienstticket für Benutzer aus einem Kerberos-Schlüsselverteilungscenter (Key Distribution Center, KDC) abruft.

Die externe Kommunikation zwischen dem Client und dem Azure-Front-End sollte keinen anderen Bezug zur KCD haben. Sie stellt lediglich die Funktionsweise der KCD sicher. Der Azure-Proxydienst wird mit einer gültigen Benutzer-ID bereitgestellt, die zum Abrufen eines Kerberos-Tickets verwendet wird. Ohne diese ID ist eine KCD nicht möglich, und ein Fehler tritt auf.

Wie bereits erwähnt, bieten die Fehlermeldungen im Browser einige geeignete Hinweise zu den Fehlerursachen. Notieren Sie sich die Aktivitäts-ID und den Zeitstempel in der Antwort. Mit diesen Informationen können Sie das Verhalten den tatsächlichen Ereignissen im Azure Proxy-Ereignisprotokoll zuordnen.

![Beispiel: Fehler durch falsche KCD-Konfiguration](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Die entsprechenden Einträge im Ereignisprotokoll weisen dann die Ereignis-IDs 13019 oder 12027 auf. Die Ereignisprotokolle für den Connector befinden sich unter **Anwendungs- und Dienstprotokolle** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Ereignis 13019 aus dem Ereignisprotokoll des Anwendungsproxys](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Ereignis 12027 aus dem Ereignisprotokoll des Anwendungsproxys](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Verwenden Sie für die Adresse der Anwendung einen **A**-Datensatz in Ihrem internen DNS und keinen **CNAME**-Datensatz.
1. Vergewissern Sie sich erneut, dass dem Connectorhost die Rechte zum Delegieren an den Dienstprinzipalnamen (Service Principal Name, SPN) des festgelegten Zielkontos erteilt wurden. Stellen Sie außerdem erneut sicher, dass die Option **Beliebiges Authentifizierungsprotokoll verwenden** aktiviert ist. Weitere Informationen finden Sie im [Artikel zur SSO-Konfiguration](application-proxy-configure-single-sign-on-with-kcd.md).
1. Stellen Sie sicher, dass nur eine Instanz des SPN in Azure AD vorhanden ist. Geben Sie `setspn -x` über eine Eingabeaufforderung auf einem beliebigen Host aus, der Mitglied einer Domäne ist.
1. Stellen Sie sicher, dass eine Domänenrichtlinie erzwungen wird, die die [Maximalgröße ausgestellter Kerberos-Token](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012) begrenzt. Diese Richtlinie verhindert, dass der Connector ein Token abruft, falls dieser als überflüssig befunden wird.

Eine Netzwerkablaufverfolgung, die den Datenaustausch zwischen dem Connectorhost und einem Domänen-KDC erfasst, ist der nächstbeste Schritt zum Abrufen weiterer allgemeiner Details zu den Problemen. Weitere Informationen finden Sie im [vertiefenden Beitrag zur Problembehandlung](https://aka.ms/proxytshootpaper).

Wenn die Ticketausstellung ordnungsgemäß funktioniert, wird in den Protokollen ein Ereignis angezeigt, laut dem ein Authentifizierungsfehler aufgetreten ist, weil die Anwendung den Code 401 zurückgegeben hat. Dieses Ereignis weist darauf hin, dass die Zielanwendung Ihr Ticket abgelehnt hat. Fahren Sie mit der nächsten Phase fort.

### <a name="target-application"></a>Zielanwendung

Der Consumer des Kerberos-Tickets, das vom Connector bereitgestellt wurde. In dieser Phase hat der Connector ein Kerberos-Dienstticket an das Back-End gesendet. Dieses Ticket ist ein Header in der ersten Anwendungsanforderung.

1. Überprüfen Sie mithilfe der internen URL der Anwendung, die im Portal definiert ist, dass direkt über den Browser auf dem Connectorhost auf die Anwendung zugegriffen werden kann. Dann können Sie sich erfolgreich anmelden. Details finden Sie auf der Seite des Connectors zur **Problembehandlung**.
1. Vergewissern Sie sich, während Sie sich weiterhin auf dem Connectorhost befinden, dass die Authentifizierung zwischen dem Browser und der Anwendung Kerberos verwendet. Führen Sie eine der folgenden Maßnahmen durch:
1. Führen Sie die Entwicklertools (**F12**) im Internet Explorer aus, oder verwenden Sie [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) auf dem Connectorhost. Wechseln Sie mithilfe der internen URL zur Anwendung. Überprüfen Sie die angebotenen WWW-Autorisierungsheader, die in der Antwort der Anwendung zurückgegeben wurden, um sicherzustellen, dass entweder eine Aushandlung oder Kerberos vorhanden ist.

   - Das nächste Kerberos-Blob, das in der Antwort vom Browser an die Anwendung zurückgegeben wird, beginnt mit **YII**. Diese Buchstaben geben an, dass Kerberos ausgeführt wird. Der Microsoft NT LAN Manager (NTLM) beginnt hingegen immer mit **TlRMTVNTUAAB**, das NTLM Security Support Provider (NTLMSSP) lautet, wenn die Decodierung aus Base64 erfolgt ist. Wenn zu Beginn des Blobs **TlRMTVNTUAAB** angezeigt wird, ist Kerberos nicht verfügbar. Wenn **TlRMTVNTUAAB** nicht angezeigt wird, ist Kerberos voraussichtlich verfügbar.

      > [!NOTE]
      > Bei der Verwendung von Fiddler würde diese Methode die vorübergehende Deaktivierung des erweiterten Schutzes für die Konfiguration der Anwendung in den IIS erfordern.

      ![Browserfenster zur Netzwerküberprüfung](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Das Blob in dieser Abbildung beginnt nicht mit **TIRMTVNTUAAB**. In diesem Beispiel ist Kerberos verfügbar, weshalb das Kerberos-Blob nicht mit **YII** beginnt.

1. Entfernen Sie NTLM vorübergehend aus der Anbieterliste auf der IIS-Website. Rufen Sie die App direkt über den Internet Explorer auf dem Connectorhost auf. NTLM ist nicht mehr in der Anbieterliste enthalten. Sie können nur mithilfe von Kerberos auf die Anwendung zugreifen. Wenn beim Zugriff ein Fehler auftritt, liegt möglicherweise ein Problem mit der Konfiguration der Anwendung vor. Die Kerberos-Authentifizierung funktioniert nicht.

   - Wenn Kerberos nicht verfügbar ist, überprüfen Sie die Authentifizierungseinstellungen der Anwendung in den IIS. Stellen Sie sicher, dass im oberen Bereich **Aushandeln** und direkt darunter „NTLM“ aufgeführt wird. Wenn dort **Nicht aushandeln**, **Kerberos oder aushandeln** oder **PKU2U** angezeigt wird, fahren Sie nur fort, wenn Kerberos funktioniert.

     ![Windows-Authentifizierungsanbieter](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Wenn Kerberos und NTLM vorhanden sind, deaktivieren Sie vorübergehend die Vorauthentifizierung für die Anwendung im Portal. Prüfen Sie, ob Sie über das Internet mithilfe der externen URL darauf zugreifen können. Sie werden aufgefordert, sich zu authentifizieren. Dies können Sie mit dem gleichen Konto tun, das Sie im vorherigen Schritt verwendet haben. Wenn dies nicht möglich ist, liegt ein Problem mit der Back-End-Anwendung vor, nicht mit der KCD.
   - Aktivieren Sie erneut die Vorauthentifizierung im Portal. Authentifizieren Sie sich über Azure, indem Sie versuchen, über die externe URL eine Verbindung mit der Anwendung herzustellen. Wenn beim einmaligen Anmelden ein Fehler auftritt, werden im Browser eine Fehlermeldung zu einem verbotenen Vorgang sowie das Ereignis 13022 im Protokoll angezeigt:

     *Microsoft AAD-Anwendungsproxy-Connector kann den Benutzer nicht authentifizieren, da der Back-End-Server auf Kerberos-Authentifizierungsversuche mit einem HTTP 401-Fehler reagiert.*

      ![Zeigt HTTP-Fehler 401: Verboten](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Überprüfen Sie die IIS-Anwendung. Stellen Sie sicher, dass der konfigurierte Anwendungspool und der SPN für die Verwendung desselben Kontos in Azure AD konfiguriert sind. Navigieren Sie in den IIS zur dargestellten Option wie in der folgenden Abbildung gezeigt:

      ![Fenster zur IIS-Anwendungskonfiguration](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Nachdem Sie nun die Identität kennen, stellen Sie sicher, dass dieses Konto mit dem betreffenden SPN konfiguriert ist. z. B. `setspn –q http/spn.wacketywack.com`. Geben Sie in der Eingabeaufforderung den folgenden Text ein:

      ![Zeigt das SetSPN-Befehlsfenster](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Überprüfen Sie den SPN, der über die Einstellungen der Anwendung im Portal definiert ist. Stellen Sie sicher, dass der gleiche für das Azure AD-Zielkonto konfigurierte SPN vom App-Pool der Anwendung verwendet wird.

      ![SPN-Konfiguration im Azure-Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Navigieren Sie zu den IIS, und wählen Sie die Option **Konfigurationseditor** für die Anwendung aus. Navigieren Sie zu **system.webServer/security/authentication/windowsAuthentication**. Stellen Sie sicher, dass der Wert **UseAppPoolCredentials** auf **True** festgelegt ist.

      ![Option für Anmeldeinformationen der Anwendungspools der IIS-Konfiguration](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Ändern Sie diesen Wert in **True**. Entfernen Sie alle zwischengespeicherten Kerberos-Tickets vom Back-End-Server, indem Sie den folgenden Befehl ausführen:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Weitere Informationen finden Sie unter [Purge the Kerberos client ticket cache for all sessions](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf) (Löschen des Kerberos-Clientticketcaches für alle Sitzungen).

Wenn Sie den Kernelmodus aktiviert lassen, wird hierdurch die Leistung von Kerberos-Vorgängen verbessert. Aber es bewirkt auch, dass das Ticket für den angeforderten Dienst über das Computerkonto entschlüsselt wird. Dieses Konto wird auch als lokales System bezeichnet. Legen Sie diesen Wert auf **True** fest, um die KCD zu unterbrechen, wenn die Anwendung über mehr als einen Server in einer Farm gehostet wird.

- Deaktivieren Sie als zusätzliche Überprüfung **Erweiterter Schutz**. In einigen Szenarien hat der **Erweiterte Schutz** die KCD unterbrochen, wenn sie in bestimmten Konfigurationen aktiviert wurde. In diesen Fällen wurde eine Anwendung als Unterordner der Standardwebsite veröffentlicht. Diese Anwendung ist nur für die anonyme Authentifizierung konfiguriert. Alle Dialogfelder sind ausgegraut, was darauf hindeutet, dass untergeordnete Objekte keine aktiven Einstellungen erben. Es wird empfohlen, dies zu testen. Denken Sie jedoch daran, diesen Wert sofern möglich wieder auf **Aktiviert** zu setzen.

  Nach dieser zusätzlichen Überprüfung sind Sie bestens vorbereitet, um die veröffentlichte Anwendung zu verwenden. Sie können zusätzliche Connector starten, die ebenfalls zum Delegieren konfiguriert sind. Weitere Informationen finden Sie in der ausführlicheren exemplarischen Vorgehensweise zur [Problembehandlung beim Azure AD-Anwendungsproxy](https://aka.ms/proxytshootpaper).

Wenn dies dennoch keine Abhilfe leistet, kann Microsoft-Support Ihnen weiter helfen. Erstellen Sie direkt im Portal ein Supportticket. Ein Techniker wird sich mit Ihnen in Verbindung setzen.

## <a name="other-scenarios"></a>Andere Szenarien

- Der Azure-Anwendungsproxy fordert vor dem Senden der Anforderung an eine Anwendung ein Kerberos-Ticket an. Bei einigen Drittanbieteranwendungen ist diese Authentifizierungsmethode unüblich. Bei diesen Anwendungen werden konventionellere Aushandlungen erwartet. Die erste Anforderung ist anonym, wodurch es der Anwendung ermöglicht wird, mit den Authentifizierungstypen zu antworten, die sie über eine 401-Meldung unterstützt. Diese Art der Kerberos-Aushandlung kann mithilfe der in diesem Dokument beschriebenen Schritte aktiviert werden: [Eingeschränkte Delegierung von Kerberos für die einmalige Anmeldung](application-proxy-configure-single-sign-on-with-kcd.md).
- Die Mehrfachhopauthentifizierung wird häufig in Szenarien verwendet, in denen eine Anwendung abgestuft wird, mit einem Back-End und Front-End, die beide eine Authentifizierung erfordern, z.B. SQL Server Reporting Services. Informationen zum Konfigurieren des Mehrfachhopszenarios finden Sie im Supportartikel [Eingeschränkte Kerberos-Delegierung mit eventuell erforderlicher Protokollübertragung in Mehrfachhopszenarien](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der KCD für eine verwaltete Domäne](../../active-directory-domain-services/deploy-kcd.md)