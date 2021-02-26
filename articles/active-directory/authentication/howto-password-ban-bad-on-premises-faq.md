---
title: Häufig gestellte Fragen zum lokalen Azure AD-Kennwortschutz
description: In den häufig gestellten Fragen (FAQ) finden Sie Informationen zum Azure AD-Kennwortschutz in einer lokalen Active Directory Domain Services-Umgebung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625126"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Häufig gestellte Fragen zum Azure AD-Kennwortschutz in lokalen Umgebungen

In diesem Abschnitt finden Sie Antworten auf häufige Fragen zum Azure AD-Kennwortschutz.

## <a name="general-questions"></a>Allgemeine Fragen

**F: Welche Informationen zur Auswahl eines sicheren Kennworts sollten Benutzer erhalten?**

Die aktuelle Anleitung von Microsoft zu diesem Thema finden Sie unter folgendem Link:

[Microsoft-Kennwortleitfaden](https://www.microsoft.com/research/publication/password-guidance)

**F: Wird der lokale Azure AD-Kennwortschutz in nicht öffentlichen Clouds unterstützt?**

Der lokale Azure AD-Kennwortschutz wird in der öffentlichen Cloud und in der Arlington-Cloud unterstützt. Für die Verfügbarkeit in anderen Clouds wurde kein Datum angekündigt.

Das Azure AD-Portal ermöglicht die Änderung der lokalen Konfiguration des „Kennwortschutzes für Windows Server Active Directory“ auch in nicht unterstützten Clouds. Diese Änderungen werden beibehalten, werden aber andernfalls nie wirksam. Die Registrierung von lokalen Proxy-Agents oder Gesamtstrukturen wird für nicht unterstützte Clouds nicht unterstützt, und bei solchen Registrierungsversuchen tritt immer ein Fehler auf.

**F: Wie kann ich die Vorteile des Azure AD-Kennwortschutzes auf eine Untergruppe meiner lokalen Benutzer anwenden?**

Wird nicht unterstützt. Sobald der Azure AD-Kennwortschutz bereitgestellt und aktiviert ist, werden keine Unterscheidungen getroffen – alle Benutzer genießen dieselben Sicherheitsvorteile.

**F: Worin besteht der Unterschied zwischen einer Kennwortänderung und einer Kennwortfestlegung (oder Kennwortzurücksetzung)?**

Bei einer Kennwortänderung wählt ein Benutzer ein neues Kennwort aus, nachdem er nachgewiesen hat, dass er das alte Kennwort kennt. Eine Kennwortänderung findet beispielsweise dann statt, wenn ein Benutzer sich unter Windows anmeldet und dann aufgefordert wird, ein neues Kennwort auszuwählen.

Bei einer Kennwortfestlegung (manchmal als Kennwortzurücksetzung bezeichnet) ersetzt ein Administrator das Kennwort für ein Konto durch ein neues Kennwort, z. B. durch Verwendung des Active Directory-Verwaltungstools „Benutzer und Computer“. Für diesen Vorgang sind hohe Berechtigungen (normalerweise Domänenadministrator) erforderlich. Außerdem kennt die Person, die den Vorgang durchführt, in der Regel das alte Kennwort nicht. Kennwortfestlegungen finden häufig in Helpdesk-Szenarien statt, z.B. bei der Unterstützung eines Benutzers, der sein Kennwort vergessen hat. Ein anderes Beispiel ist die erstmalige Erstellung eines neuen Benutzerkontos mit einem Kennwort.

Die Richtlinie zur Kennwortüberprüfung ist identisch, unabhängig davon, ob eine Kennwortänderung oder eine Kennwortfestlegung durchgeführt wird. Mit dem DC-Agent-Dienst für den Azure AD-Kennwortschutz werden verschiedene Ereignisse protokolliert, um Sie darüber zu informieren, ob ein Vorgang zum Ändern oder zum Festlegen eines Kennworts durchgeführt wurde.  Siehe dazu [Überwachung und Protokollierung beim Azure AD-Kennwortschutz](./howto-password-ban-bad-on-premises-monitor.md).

**F: Überprüft der Azure AD-Kennwortschutz vorhandene Kennwörter nach der Installation?**

Nein. Der Azure AD-Kennwortschutz kann die Kennwortrichtlinie nur bei einer Änderung oder Festlegung von Klartextkennwörtern erzwingen. Nachdem ein Kennwort von Active Directory akzeptiert wurde, werden nur authentifizierungsprotokollspezifische Hashs dieses Kennworts beibehalten. Das Klartextkennwort wird niemals persistent gespeichert, sodass der Azure AD-Kennwortschutz vorhandene Kennwörter nicht überprüfen kann.

Nach der Erstbereitstellung des Azure AD-Kennwortschutzes werden alle Benutzer und Konten letztendlich auf die Verwendung von Kennwörtern umgestellt, die vom Azure AD-Kennwortschutz überprüft wurden, da die bestehenden Kennwörter im Lauf der Zeit normal ablaufen. Dieser Prozess kann bei Bedarf durch einen einmaligen manuellen Ablauf der Kennwörter von Benutzerkonten beschleunigt werden.

Bei Konten, die mit „Kennwort läuft nie ab“ konfiguriert wurden, wird nie eine Kennwortänderung erzwungen. Dies ist ausschließlich manuell möglich.

**F: Warum werden doppelte Kennwortablehnungsereignisse protokolliert bei dem Versuch, mithilfe des Snap-Ins zur Verwaltung von Active Directory-Benutzern und -Computern ein schwaches Kennwort festzulegen?**

Das Snap-In zur Verwaltung von Active Directory-Benutzern und -Computern versucht zunächst, das neue Kennwort mit dem Kerberos-Protokoll festzulegen. Bei einem Fehler versucht das Snap-In erneut, das Kennwort mit einem älteren Protokoll (SAM RPC) festzulegen (die verwendeten spezifischen Protokolle sind nicht wichtig). Wenn das neue Kennwort vom Azure AD-Kennwortschutz als schwach eingestuft wird, führt dieses Snap-In-Verhalten dazu, dass zwei Sätze von Kennwortablehnungsereignissen protokolliert werden.

**F: Warum werden Ereignisse zu Kennwortverletzungen vom Azure AD-Kennwortschutz mit einem leeren Benutzernamen protokolliert?**

Active Directory bietet die Möglichkeit zum Testen von Kennwörtern, um zu überprüfen, ob die aktuellen Kennwortkomplexitätsanforderungen der Domäne erfüllt sind. Ein solcher Test kann beispielsweise mit der API [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) durchgeführt werden. Wenn ein Kennwort auf diese Weise überprüft wird, umfasst der Test auch eine Validierung durch Produkte, die auf Kennwortfilter-DLLs basieren – z.B. der Azure AD-Kennwortschutz –, aber die an eine solche Kennwortfilter-DLL übergebenen Benutzernamen sind leer. In diesem Szenario wird das Kennwort weiterhin mithilfe der aktuell geltenden Kennwortrichtlinie durch den Azure AD-Kennwortschutz validiert, und das Ergebnis wird in einer Ereignisprotokollmeldung erfasst, aber die Ereignisprotokollmeldung enthält leere Benutzernamensfelder.

**F: Wird die Parallelinstallation des Azure AD-Kennwortschutzes mit anderen kennwortfilterbasierten Produkten unterstützt?**

Ja. Unterstützung für mehrere registrierte Kennwortfilter-DLLs ist ein Hauptmerkmal von Windows und nicht spezifisch für den Azure AD-Kennwortschutz. Alle registrierten Kennwortfilter-DLLs müssen zustimmen, bevor ein Kennwort akzeptiert wird.

**F: Wie kann ich den Azure AD-Kennwortschutz in meiner Active Directory-Umgebung bereitstellen und konfigurieren, ohne Azure zu verwenden?**

Wird nicht unterstützt. Der Azure AD-Kennwortschutz ist ein Azure-Feature, das auf eine lokale Active Directory-Umgebung ausgeweitet werden kann.

**F: Wie kann ich den Inhalt der Richtlinie auf Active Directory-Ebene ändern?**

Wird nicht unterstützt. Die Richtlinie kann nur über das Azure AD-Portal verwaltet werden. Siehe auch die Antwort auf die vorherige Frage.

**F: Warum ist DFSR für die sysvol-Replikation erforderlich?**

FRS (die Vorgängertechnologie zu DFSR) verfügt über viele bekannte Probleme und die Unterstützung wird in neueren Versionen von Windows Server Active Directory vollständig eingestellt. Zero-Tests des Azure AD-Kennwortschutzes erfolgen in FRS-konfigurierten Domänen.

Weitere Informationen finden Sie in den folgenden Artikeln:

[Was für die Migration der sysvol-Replikation zu DFSR spricht](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Das Ende ist nahe für FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Sollte DFSR von Ihrer Domäne noch nicht verwendet werden, muss die Domäne vor der Installation des Azure AD-Kennwortschutzes für die Verwendung von DFSR migriert werden. Weitere Informationen finden Sie unter dem folgenden Link:

[Anleitung zur SYSVOL-Replikationsmigration: FRS- zu DFS-Replikation](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Die DC-Agent-Software für den Azure AD-Kennwortschutz wird derzeit auf Domänencontrollern in Domänen installiert, von denen noch FRS für die SYSVOL-Replikation verwendet wird. Die Software funktioniert in dieser Umgebung allerdings NICHT ordnungsgemäß. Dies macht sich unter anderem durch nicht erfolgreich replizierte Einzeldateien sowie durch scheinbar erfolgreiche SYSVOL-Wiederherstellungsprozeduren bemerkbar, bei denen jedoch nicht alle Dateien repliziert werden. Es empfiehlt sich, die Domäne baldmöglichst für die Verwendung von DFSR zu migrieren, um von den DFSR-Vorteilen zu profitieren und die Blockierung der Bereitstellung des Azure AD-Kennwortschutzes aufzuheben. Zukünftige Versionen der Software werden automatisch deaktiviert, wenn sie in einer Domäne ausgeführt werden, die noch FRS verwendet.

**F: Wie viel Datenträger-Speicherplatz benötigt das Feature auf der Systemvolumefreigabe der Domäne?**

Die genaue Speicherplatznutzung variiert, da sie von verschiedenen Faktoren abhängig ist, z.B. von der Anzahl und Länge der gesperrten Token in der globalen Sperrliste von Microsoft und der kundenspezifischen Sperrliste jedes Mandanten sowie vom Verschlüsselungsoverhead. Diese Listen werden im Lauf der Zeit wahrscheinlich erweitert. Angesichts dieser Tatsache ist es eine sinnvolle Annahme, dass das Feature mindestens fünf (5) Megabytes Speicherplatz auf der Systemvolumefreigabe der Domäne benötigen wird.

**F: Warum ist ein Neustart erforderlich, um die DC-Agent-Software zu installieren oder zu aktualisieren?**

Diese Anforderung wird durch das Kernverhalten von Windows verursacht.

**F: Gibt es eine Möglichkeit, einen DC-Agent so zu konfigurieren, dass er einen bestimmten Proxyserver verwendet?**

Nein. Da der Proxyserver zustandslos ist, spielt es keine Rolle, welcher Proxyserver genau verwendet wird.

**F: Kann der Azure AD-Kennwortschutz-Proxydienst neben anderen Diensten wie Azure AD Connect bereitgestellt werden?**

Ja. Der Azure AD-Kennwortschutz-Proxydienst und Azure AD Connect führen nie zu direkten Konflikten.

Leider wurde eine Inkompatibilität zwischen der Version des Microsoft Azure AD Connect-Agent-Updaterdiensts, die von der Software Azure AD-Kennwortschutzproxy installiert wird, und der Version des Diensts festgestellt, die von der Software [Azure Active Directory-Anwendungsproxy ](../manage-apps/application-proxy.md) installiert wird. Diese Inkompatibilität kann dazu führen, dass der Agent-Updaterdienst sich mit Azure für Softwareupdates nicht in Verbindung setzen kann. Es wird nicht empfohlen, den Azure AD-Kennwortschutzproxy und den Azure Active Directory-Anwendungsproxy auf demselben Computer zu installieren.

**F: In welcher Reihenfolge sollen die DC-Agents und Proxys installiert und registriert werden?**

Die Installation von Proxys, die Installation von DC-Agents, die Registrierung von Gesamtstrukturen und die Registrierung von Proxys können in einer beliebigen Reihenfolge erfolgen.

**F: Muss ich mir Sorgen machen, dass durch die Bereitstellung dieses Features die Leistungsgrenze meiner Domänencontroller erreicht wird?**

Der Domänencontroller-Agent-Dienst für den Azure AD-Kennwortschutz sollte sich nicht wesentlich auf die Leistung des Domänencontrollers in einer stabilen Active Directory-Bereitstellung auswirken.

Die meisten Kennwortänderungsvorgänge in Active Directory-Bereitstellungen machen nur einen sehr geringen Teil der gesamten Workload auf einem Domänencontroller aus. Ein Beispiel: Stellen Sie sich eine Active Directory-Domäne mit 10.000 Benutzerkonten und einer MaxPasswordAge-Richtlinie von 30 Tagen vor. Durchschnittlich werden in dieser Domäne 10.000/30 Monate = ~333 Kennwortänderungsvorgänge pro Tag stattfinden – dies ist auch für einen einzelnen Domänencontroller eine sehr geringe Anzahl. Sehen wir uns den ungünstigsten Fall an: Alle ~333 Änderungsvorgänge finden innerhalb einer Stunde auf einem einzelnen Domänencontroller statt. Ein solches Szenario könnte eintreten, wenn viele Mitarbeiter am Montagmorgen zur Arbeit kommen und ihre Kennwörter ändern müssen. Selbst in diesem Fall würden nur ~333/60 Minuten = 6 Kennwortänderungen pro Minute stattfinden – auch dies ist keine signifikante Last.

Wenn Ihre aktuellen Domänencontroller allerdings bereits mit eingeschränkter Leistung ausgeführt werden (z.B. die Limits für CPU, Datenträger-Speicherplatz, Datenträger-E/A usw. bereits ausgereizt sind), ist es ratsam, weitere Domänencontroller hinzuzufügen oder den verfügbaren Speicherplatz zu erweitern, bevor Sie dieses Feature bereitstellen. Informationen zur Speicherplatznutzung auf dem Systemvolume-Datenträger finden Sie auch in der vorherigen Frage.

**F: Ich möchte den Azure AD-Kennwortschutz auf einigen wenigen Domänencontrollern in meiner Domäne testen. Kann ich erzwingen, dass für Kennwortänderungen durch Benutzer nur diese bestimmten Domänencontroller verwendet werden?**

Nein. Das Windows-Clientbetriebssystem steuert, welcher Domänencontroller verwendet wird, wenn ein Benutzer sein Kennwort ändert. Der Domänencontroller wird anhand von Faktoren wie Active Directory-Standort und -Subnetzzuordnungen, der umgebungsspezifischen Netzwerkkonfiguration usw. ausgewählt. Der Azure AD-Kennwortschutz hat keine Kontrolle über diese Faktoren und kann nicht beeinflussen, welcher Domänencontroller für die Änderung eines Benutzerkennworts ausgewählt wird.

Eine Möglichkeit, um dieses Ziel zumindest teilweise zu erreichen, besteht darin, den Azure AD-Kennwortschutz auf allen Domänencontrollern in einem bestimmten Active Directory-Standort bereitzustellen. Dieses Vorgehen bietet eine angemessene Abdeckung für alle Windows-Clients, die diesem Standort zugewiesen sind, und damit auch für die Benutzer, die sich bei diesen Clients anmelden und ihre Kennwörter ändern.

**F: Wenn ich den Domänencontroller-Agent-Dienst für den Azure AD-Kennwortschutz nur auf dem primären Domänencontroller (PDC) installiere, sind damit auch alle anderen Domänencontroller in der Domäne geschützt?**

Nein. Wenn das Kennwort eines Benutzers auf einem Domänencontroller geändert wird, bei dem es sich nicht um den PDC handelt, wird das Klartextkennwort niemals an den PDC gesendet (dies ist eine weit verbreitete Fehlannahme). Sobald ein neues Kennwort auf einem bestimmten Domänencontroller akzeptiert wurde, verwendet dieser Domänencontroller das Kennwort, um die Kennworthashes für die verschiedenen Authentifizierungsprotokolle zu erstellen und speichert diese Hashes im Verzeichnis. Das Klartextkennwort wird nicht gespeichert. Die aktualisierten Hashes werden an den PDC repliziert. Benutzerkennwörter können in einigen Fällen direkt auf dem PDC geändert werden – auch dies hängt von verschiedenen Faktoren ab, wie z.B. der Netzwerktopologie und der Gestaltung des Active Directory-Standorts. (Siehe auch die Antwort auf die vorherige Frage.)

Zusammenfassend gesagt: Die Bereitstellung des Domänencontroller-Agent-Diensts für den Azure AD-Kennwortschutz auf dem PDC ist erforderlich, um eine 100-prozentige Sicherheitsabdeckung des Features in der gesamten Domäne zu erzielen. Wenn das Feature nur auf dem PDC bereitgestellt wird, können die anderen Domänencontroller in der Domäne nicht von den Sicherheitsvorteilen des Azure AD-Kennwortschutzes profitieren.

**F: Warum funktioniert die benutzerdefinierte intelligente Sperre auch dann nicht, wenn die Agents in meiner lokalen Active Directory Umgebung installiert wurden?**

Die benutzerdefinierte intelligente Sperre wird nur in Azure AD unterstützt. Änderungen an den Einstellungen für die benutzerdefinierte intelligente Sperre im Azure AD-Portal haben keine Auswirkungen auf die lokale Active Directory-Umgebung, auch wenn die Agents installiert sind.

**F: Ist für den Azure AD-Kennwortschutz ein System Center Operations Manager-Managementpack verfügbar?**

Nein.

**F: Warum lehnt Azure AD unsichere (oder schwache) Kennwörter weiterhin ab, obwohl ich für die Richtlinie den Überwachungsmodus konfiguriert habe?**

Der Überwachungsmodus wird nur in der lokalen Active Directory-Umgebung unterstützt. Beim Auswerten von Kennwörtern ist Azure AD implizit immer im Modus „Erzwingen“.

**F: Meinen Benutzern wird die herkömmliche Windows-Fehlermeldung angezeigt, wenn ein Kennwort durch den Azure AD-Kennwortschutz abgelehnt wird. Kann diese Fehlermeldung angepasst werden, damit die Benutzer wissen, was wirklich passiert ist?**

Nein. Die Fehlermeldung, die Benutzern angezeigt wird, wenn ein Kennwort von einem Domänencontroller abgelehnt wird, wird nicht vom Domänencontroller gesteuert, sondern vom Clientcomputer. Dieses Verhalten tritt auf, wenn ein Kennwort aufgrund der standardmäßigen Active Directory-Kennwortrichtlinien oder durch eine kennwortfilterbasierte Lösung wie den Azure AD-Kennwortschutz abgelehnt wird.

## <a name="password-testing-procedures"></a>Vorgehensweisen zum Testen von Kennwörtern

Sie sollten mit verschiedenen Kennwörtern einige grundlegende Tests durchführen, um den ordnungsgemäßen Betrieb der Software zu überprüfen und den [Kennwortüberprüfungsalgorithmus](concept-password-ban-bad.md#how-are-passwords-evaluated) besser verstehen zu können. In diesem Abschnitt wird eine Methode für derartige Tests beschrieben, die für das Erzeugen wiederholbarer Ergebnisse konzipiert ist.

Warum ist es notwendig, derartige Schritte auszuführen? Es gibt mehrere Faktoren, die kontrollierte wiederholbare Kennworttests in der lokalen Active Directory-Umgebung erschweren:

* Die Kennwortrichtlinie wird in Azure konfiguriert und dauerhaft gespeichert. Kopien der Richtlinie werden regelmäßig mithilfe eines Abrufmechanismus vom lokalen DC-Agent synchronisiert. Die diesem Abrufzyklus eigene Zeitverzögerung kann Verwirrung stiften. Wenn Sie z. B. die Richtlinie in Azure konfigurieren, aber vergessen, sie mit dem DC-Agent zu synchronisieren, erzielen Sie mit Ihren Tests möglicherweise nicht die erwarteten Ergebnisse. Das Abrufintervall ist derzeit auf ein Mal pro Stunde hartcodiert. Für ein interaktives Testszenario ist es jedoch nicht ideal, zwischen Richtlinienänderungen eine Stunde warten zu müssen.
* Wenn eine neue Kennwortrichtlinie mit einem Domänencontroller synchronisiert wird, treten aufgrund der Replikation auf anderen Domänencontrollern weitere Wartezeiten auf. Diese Verzögerungen können zu unerwarteten Ergebnissen führen, wenn Sie eine Kennwortänderung mit einem Domänencontroller testen, der noch nicht die neueste Version der Richtlinie erhalten hat.
* Das Testen von Kennwortänderungen über eine Benutzeroberfläche erschwert das Vertrauen in die Ergebnisse. Es ist beispielsweise einfach, auf einer Benutzeroberfläche ein ungültiges Kennwort falsch einzugeben, besonders auch deshalb, weil auf den meisten Benutzeroberflächen zur Kennworteingabe (z. B. auf der Windows-Benutzeroberfläche „STRG+ALT+Entf -> Kennwort ändern“) die Benutzereingabe verborgen wird.
* Es lässt sich nicht genau steuern, welcher Domänencontroller beim Testen von Kennwortänderungen von in die Domäne eingebundenen Clients verwendet wird. Das Windows-Betriebssystem des Clients wählt anhand von Faktoren wie Active Directory-Standort und -Subnetzzuordnungen, umgebungsspezifischer Netzwerkkonfiguration usw. den Domänencontroller aus.

Um diese Probleme zu vermeiden, basieren die folgenden Schritte auf Tests von Kennwortzurücksetzungen über die Befehlszeile, während Sie bei einem Domänencontroller angemeldet sind.

> [!WARNING]
> Diese Vorgehensweisen sollten nur in einer Testumgebung verwendet werden, da alle eingehenden Kennwortänderungen und -zurücksetzungen ohne Überprüfung akzeptiert werden, während der DC-Agent-Dienst beendet wird, aber auch, um die größeren Risiken bei der Anmeldung bei einem Domänencontroller zu vermeiden.

Bei den folgenden Schritten wird davon ausgegangen, dass Sie den DC-Agent auf mindestens einem Domänencontroller installiert haben und mindestens ein Proxy installiert ist. Außerdem müssen Sie sowohl den Proxy als auch die Gesamtstruktur registriert haben.

1. Melden Sie sich mit den Anmeldeinformationen eines Domänenadministrators (oder mit anderen Anmeldeinformationen, die über ausreichende Berechtigungen zum Erstellen von Testbenutzerkonten und zum Zurücksetzen von Kennwörtern verfügen) bei einem Domänencontroller an, auf dem die DC-Agent-Software installiert ist und der neu gestartet wurde.
1. Öffnen Sie die Ereignisanzeige, und navigieren Sie zum [DC-Agent-Administratorereignisprotokoll](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten.
1. Erstellen eines Testkontos für Kennworttests

   Es gibt viele Möglichkeiten, ein Benutzerkonto zu erstellen. Hier wird jedoch eine Befehlszeilenoption angeboten, um den Vorgang bei wiederholten Testzyklen zu vereinfachen:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   In Bezug auf die Erläuterungen weiter unten wird davon ausgegangen, dass ein Testkonto mit dem Namen „ContosoUser“ erstellt wurde, z. B.:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Öffnen Sie einen Webbrowser (Sie müssen möglicherweise anstelle des Domänencontrollers ein separates Gerät verwenden), melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu „Azure Active Directory > Sicherheit > Authentifizierungsmethoden > Kennwortschutz“.
1. Ändern Sie für die Tests, die Sie durchführen möchten, die Azure AD-Kennwortschutzrichtlinie nach Bedarf.  Sie können beispielsweise entweder den erzwungenen Modus oder den Überwachungsmodus konfigurieren, oder Sie können in der benutzerdefinierten Liste gesperrter Kennwörter die gesperrten Begriffe ändern.
1. Synchronisieren Sie die neue Richtlinie, indem Sie den DC-Agent-Dienst beenden und neu starten.

   Dieser Schritt kann auf verschiedene Weise ausgeführt werden. Eine Möglichkeit besteht darin, die Verwaltungskonsole der Dienstverwaltung zu verwenden, indem Sie mit der rechten Maustaste auf den DC-Agent-Dienst für den Azure AD-Kennwortschutz klicken und „Neu starten“ auswählen. Er kann aber auch wie folgt im Eingabeaufforderungsfenster ausgeführt werden:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Überprüfen Sie die Ereignisanzeige, um sicherzustellen, dass eine neue Richtlinie heruntergeladen wurde.

   Jedes Mal, wenn der DC-Agent-Dienst beendet und neu gestartet wird, sollten in kurzer Folge zwei Ereignisse vom Typ 30006 ausgegeben werden. Das erste Ereignis vom Typ 30006 spiegelt die Richtlinie wider, die auf dem Datenträger in der SYSVOL-Freigabe zwischengespeichert wurde. Das zweite Ereignis vom Typ 30006 (falls vorhanden) sollte ein aktualisiertes Mandantenrichtliniendatum enthalten und spiegelt in diesem Fall die Richtlinie wider, die von Azure heruntergeladen wurde. Der Wert für das Mandantenrichtliniendatum ist derzeit codiert und gibt den ungefähren Zeitstempel für das Herunterladen der Richtlinie von Azure an.
   
   Wenn das zweite Ereignis vom Typ 30006 nicht angezeigt wird, sollten Sie das Problem beheben, bevor Sie den Vorgang fortsetzen.
   
   Die Ereignisse vom Typ 30006 sehen etwa wie in diesem Beispiel aus:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Wenn Sie z. B. zwischen erzwungenem Modus und Überwachungsmodus wechseln, wird das AuditOnly-Flag geändert (die obige Richtlinie mit „AuditOnly=0“ befindet sich im erzwungenen Modus). Änderungen an der benutzerdefinierten Liste gesperrter Kennwörter werden im obigen Ereignis vom Typ 30006 nicht direkt widergespiegelt (und werden aus Sicherheitsgründen auch nicht an anderer Stelle protokolliert). Wenn Sie nach einer solchen Änderung die Richtlinie erfolgreich von Azure herunterladen, enthält der Download auch die geänderte benutzerdefinierte Liste gesperrter Kennwörter.

1. Führen Sie einen Test aus, indem Sie versuchen, ein neues Kennwort für das Testbenutzerkonto zurückzusetzen.

   Dieser Schritt kann über das Eingabeaufforderungsfenster wie folgt ausgeführt werden:

   ```text
   net.exe user ContosoUser <password>
   ```

   Nachdem Sie den Befehl ausgeführt haben, können Sie weitere Informationen zum Ergebnis des Befehls in der Ereignisanzeige anzeigen. Das Ergebnis der Kennwortüberprüfung mit den entsprechenden Ereignissen ist im Thema [DC-Agent-Administratorereignisprotoll](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) dokumentiert. Diese Ereignisse werden verwendet, um das Ergebnis des Tests zusätzlich zur interaktiven Ausgabe der „net.exe“-Befehle zu überprüfen.

   Ein Beispiel: Sie versuchen, ein Kennwort festzulegen, das durch die globale Microsoft-Liste gesperrt ist. (Diese Liste ist [nicht dokumentiert](concept-password-ban-bad.md#global-banned-password-list). Zum Testen können Sie hier jedoch einen bekannten, gesperrten Begriff verwenden.) In diesem Beispiel wird davon ausgegangen, dass Sie für die Richtlinie den erzwungenen Modus konfiguriert haben und der benutzerdefinierten Liste gesperrter Kennwörter noch keine Begriffe hinzugefügt haben.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Da es sich bei dem Test um einen Kennwortzurücksetzungsvorgang handelt, sollten gemäß der Dokumentation für den Benutzer „ContosoUser“ ein Ereignis vom Typ 10017 und ein Ereignis vom Typ 30005 angezeigt werden.

   Das Ereignis vom Typ 10017 sollte wie im folgenden Beispiel aussehen:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Das Ereignis vom Typ 30005 sollte wie im folgenden Beispiel aussehen:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Das hat Spaß gemacht. Probieren Sie ein weiteres Beispiel aus. Dieses Mal versuchen Sie, ein Kennwort festzulegen, das durch die benutzerdefinierte Sperrliste gesperrt ist, während für die Richtlinie der Überwachungsmodus konfiguriert ist. In diesem Beispiel wird davon ausgegangen, dass Sie die folgenden Schritte ausgeführt haben: Sie haben für die Richtlinie den Überwachungsmodus konfiguriert. Sie haben der benutzerdefinierten Liste gesperrter Kennwörter den Begriff „lachrymose“ hinzugefügt. Sie haben die resultierende neue Richtlinie mit dem Domänencontroller synchronisiert, indem Sie im DC-Agent-Dienst die oben beschriebenen Schritte ausgeführt haben.

   In Ordnung. Legen Sie nun eine Variante des gesperrten Kennworts fest:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Beachten Sie, dass Sie dieses Mal erfolgreich waren, weil sich die Richtlinie im Überwachungsmodus befindet. Für den Benutzer „ContosoUser“ sollte ein Ereignis vom Typ 10025 und ein Ereignis vom Typ 30007 angezeigt werden.

   Das Ereignis vom Typ 10025 sollte wie im folgenden Beispiel aussehen:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Das Ereignis vom Typ 30007 sollte wie im folgenden Beispiel aussehen:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Setzen Sie das Testen mit verschiedenen Kennwörtern Ihrer Wahl fort, und überprüfen Sie anhand der im vorherigen Schritt beschriebenen Vorgehensweisen die Ergebnisse in der Ereignisanzeige. Wenn Sie die Richtlinie im Azure-Portal ändern müssen, denken Sie daran, die neue Richtlinie, wie zuvor beschrieben, mit dem DC-Agent zu synchronisieren.

Wir haben Vorgehensweisen erläutert, mit denen Sie das Kennwortüberprüfungsverhalten des Azure AD-Kennwortschutzes auf kontrollierte Weise testen können. Das Verwenden der Befehlszeile unmittelbar auf einem Domänencontroller zum Zurücksetzen von Benutzerkennwörtern mag als merkwürdiges Mittel für solche Tests erscheinen, eignet sich jedoch, wie oben beschrieben, zum Erzeugen wiederholbarer Ergebnisse. Beachten Sie beim Testen verschiedener Kennwörter auch den [Kennwortüberprüfungsalgorithmus](concept-password-ban-bad.md#how-are-passwords-evaluated), da er möglicherweise bei der Erläuterung unerwarteter Ergebnisse hilfreich sein kann.

> [!WARNING]
> Wenn Sie alle Tests abgeschlossen haben, denken Sie daran, die zu Testzwecken erstellten Benutzerkonten zu löschen!

## <a name="additional-content"></a>Zusätzliche Inhalte

Die folgenden Links gehören nicht zur grundlegenden Dokumentation zum Azure AD-Kennwortschutz, sind aber eine nützliche Quelle für zusätzliche Informationen zu diesem Feature.

[Der Azure AD-Kennwortschutz ist jetzt allgemein verfügbar!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/) (Leitfaden zum Schutz von E-Mail-Phishing, Teil 15: Implementieren des Microsoft Azure AD-Kennwortschutzdiensts [auch für lokale Umgebungen!])

[Azure AD-Kennwortschutz und intelligente Sperre jetzt in der öffentlichen Vorschau!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Training zu Microsoft Premier und Unified Support verfügbar

Wenn Sie mehr über den Azure AD-Kennwortschutz erfahren und den Dienst in Ihrer Umgebung bereitstellen möchten, können Sie den proaktiven Dienst von Microsoft nutzen, der allen Kunden mit einem Premier Support- oder Unified Support-Vertrag zur Verfügung steht. Der Dienst heißt Azure Active Directory- Kennwortschutz. Weitere Informationen erhalten Sie von Ihrem Technical Account Manager.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine Frage zum lokalen Azure AD-Kennwortschutz haben, die hier nicht beantwortet wird, senden Sie uns unten einen Feedbackkommentar. Vielen Dank!

[Bereitstellen des Kennwortschutzes für Azure AD](howto-password-ban-bad-on-premises-deploy.md)