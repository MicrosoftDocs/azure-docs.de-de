---
title: Häufig gestellte Fragen zu Azure Active Directory Connect Health – Azure | Microsoft-Dokumentation
description: Dieses FAQ-Dokument beantwortet Fragen zu Azure AD Connect Health. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62096118"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Häufig gestellte Fragen zu Azure AD Connect Health
Dieser Artikel enthält Antworten auf häufig gestellte Fragen (FAQs) zu Azure Active Directory (Azure AD) Connect Health. Diese FAQs liefern Antworten zur Verwendung des Diensts, z.B. in Bezug auf das Abrechnungsmodell, Funktionen, Einschränkungen und den Support.

## <a name="general-questions"></a>Allgemeine Fragen
**F: Ich verwalte mehrere Azure AD-Verzeichnisse. Wie kann ich zu dem Mandanten mit Azure Active Directory Premium wechseln?**

Sie können zwischen verschiedenen Azure AD-Mandanten wechseln, indem Sie in der rechten oberen Ecke den derzeit angemeldeten **Benutzernamen** und dann das entsprechende Konto auswählen. Wenn das Konto hier nicht aufgeführt ist, wählen Sie **Abmelden** aus, und melden Sie sich dann mit den globalen Administratoranmeldeinformationen des Verzeichnisses an, für das Azure Active Directory Premium aktiviert ist.

**F: Welche Version der Identitätsrollen wird von Azure AD Connect Health unterstützt?**

In der folgenden Tabelle werden die Rollen und unterstützten Betriebssystemversionen aufgelistet.

|Rolle| Betriebssystem/Version|
|--|--|
|Active Directory-Verbunddienste (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 oder höher|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Beachten Sie, dass die vom Dienst bereitgestellten Features sich möglicherweise basierend auf Rolle und Betriebssystem unterscheiden. Anders gesagt: Möglicherweise sind nicht alle Features für alle Betriebssystemversionen verfügbar. Näheres finden Sie in den Featurebeschreibungen für Details.

**F: Wie viele Lizenzen benötige ich, um meine Infrastruktur zu überwachen?**

* Der erste Connect Health-Agent benötigt mindestens eine Azure AD Premium-Lizenz.
* Jeder weitere registrierte Agent benötigt 25 weitere Azure AD Premium-Lizenzen.
* Die Anzahl der Agents entspricht der Gesamtanzahl der für alle überwachten Rollen registrierten Agents (AD FS, Azure AD Connect und/oder AD DS).
* Die AAD Connect Health-Lizenzierung erfordert keine Zuweisung der Lizenz zu bestimmten Benutzern. Sie müssen lediglich über die erforderliche Anzahl gültiger Lizenzen verfügen.

Lizenzierungsinformationen finden Sie auch auf der [Seite „Azure Active Directory Preise“](https://aka.ms/aadpricing).

Beispiel:

| Registrierte Agents | Benötigte Lizenzen | Beispielüberwachungskonfiguration |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect-Server |
| 2 | 26| 1 Azure AD Connect-Server und 1 Domänencontroller |
| 3 | 51 | 1 Server für Active Directory-Verbunddienste (AD FS), 1 AD FS-Proxy und 1 Domänencontroller |
| 4 | 76 | 1 AD FS-Server, 1 AD FS-Proxy und 2 Domänencontroller |
| 5 | 101 | 1 Azure AD Connect-Server, 1 AD FS-Server, 1 AD FS-Proxy und 2 Domänencontroller |

**F: Unterstützt Azure AD Connect Health die Cloud „Azure Deutschland“?**

Azure AD Connect Health wird mit Ausnahme des [Features zum Melden von Synchronisierungsfehlern](how-to-connect-health-sync.md#object-level-synchronization-error-report) nicht in der Microsoft Cloud Deutschland unterstützt.

| Rollen | Features | In der Microsoft Cloud Deutschland unterstützt |
| ------ | --------------- | --- |
| Connect Health für Synchronisierung | Überwachung/Erkenntnis/Warnungen/Analyse | Nein |
|  | Synchronisierungsfehlerbericht | Ja |
| Connect Health für AD FS | Überwachung/Erkenntnis/Warnungen/Analyse | Nein |
| Connect Health für AD DS | Überwachung/Erkenntnis/Warnungen/Analyse | Nein |

Um die Agent-Konnektivität von Connect Health für Synchronisierung sicherzustellen, erfüllen Sie die [Installationsanforderungen](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) entsprechend.

## <a name="installation-questions"></a>Fragen zur Installation

**F: Wie wirkt sich die Installation des Azure AD Connect Health-Agents auf die einzelnen Server aus?**

Die Installation des Microsoft Azure AD Connect Health-Agents auf AD FS-Servern, Webanwendungs-Proxyservern, Azure AD Connect-(Synchronisierungs-)Servern oder Domänencontrollern wirkt sich nur minimal auf CPU, Arbeitsspeichernutzung, Netzwerkbandbreite und Speicher aus.

Die folgenden Zahlen stellen eine Schätzung dar:

* CPU-Auslastung: ~1–5 % Zunahme
* Arbeitsspeichernutzung: Bis zu 10 % des insgesamt verfügbaren Systemarbeitsspeichers

> [!NOTE]
> Wenn der Agent nicht mit Azure kommunizieren kann, speichert der Agent die Daten bis zu einem definierten Höchstwert lokal. Dabei werden die zwischengespeicherten Daten zuerst überschrieben, deren Verwendung am längsten zurückliegt.
>
>

* Lokaler Pufferspeicher für Azure AD Connect Health-Agents: ~20 MB
* Für AD FS-Server wird empfohlen, 1.024 MB (1 GB) Festplattenspeicher für den AD FS-Überwachungskanal für Azure AD Connect Health-Agents bereitzustellen, um sämtliche Überwachungsdaten zu verarbeiten, bevor sie überschrieben werden.

**F: Muss ich meine Server während der Installation der Azure AD Connect Health-Agents neu starten?**

Nein. Die Installation der Agents erfordert keinen Serverneustart. Während der Installation einiger vorbereitender Schritte muss der Server jedoch möglicherweise neu gestartet werden.

Beispielsweise erfordert die Installation von .NET Framework 4.5 unter Windows Server 2008 R2 einen Neustart des Servers.

**F: Nutzt Azure AD Connect Health einen Passthrough-Proxy über HTTP?**

Ja. Für laufende Vorgänge können Sie den Health-Agent zum Weiterleiten ausgehender HTTP-Anforderungen mithilfe eines HTTP-Proxys konfigurieren.
Erfahren Sie mehr über das [Konfigurieren des HTTP-Proxys für Health-Agents](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Wenn Sie während der Agent-Registrierung einen Proxy konfigurieren müssen, müssen Sie möglicherweise zuerst die Internet Explorer-Proxyeinstellungen ändern.

1. Öffnen Sie in Internet Explorer **Einstellungen** > **Internetoptionen** > **Verbindungen** > **LAN-Einstellungen**.
2. Wählen Sie **Proxyserver für LAN verwenden** aus.
3. Wählen Sie **Erweitert** aus, falls Sie über unterschiedliche Proxyports für HTTP und HTTPS/Secure verfügen.

**F: Unterstützt Azure AD Connect Health die Standardauthentifizierung bei der Verbindung mit HTTP-Proxys?**

Nein. Ein Mechanismus zum Angeben eines frei wählbaren Benutzernamens und Kennworts für die Standardauthentifizierung wird derzeit nicht unterstützt.

**F: Welche Firewallports muss ich öffnen, damit der Azure AD Connect Health-Agent funktioniert?**

Im Abschnitt [Anforderungen](how-to-connect-health-agent-install.md#requirements) finden Sie die Liste der Firewallports und andere Anforderungen an die Konnektivität.

**F: Warum werden zwei Server mit dem gleichen Namen im Azure AD Connect Health-Portal angezeigt?**

Wenn Sie einen Agent von einem Server entfernen, wird der Server nicht automatisch aus dem Azure AD Connect Health-Portal entfernt. Wenn Sie manuell einen Agent von einem Server oder den Server selbst entfernt haben, müssen Sie den Servereintrag manuell aus dem Azure AD Connect Health-Portal löschen.

Sie können ein Reimaging für einen Server durchführen oder einen neuen Server mit den gleichen Daten (z.B. Computernamen) erstellen. Wenn Sie den bereits registrierten Server nicht aus dem Azure AD Connect Health-Portal entfernt, aber den Agent auf dem neuen Server installiert haben, werden möglicherweise zwei Einträge mit demselben Namen angezeigt.

Löschen Sie in diesem Fall den Eintrag für den älteren Server manuell. Die Daten für diesen Server sollten veraltet sein.

## <a name="health-agent-registration-and-data-freshness"></a>Health-Agent-Registrierung und Datenaktualität

**F: Was sind die häufigsten Gründe für Health-Agent-Registrierungsfehler, und wie werden sie behoben?**

Bei der Registrierung des Health-Agents kann aus folgenden Gründen ein Fehler auftreten:

* Der Agent kann nicht mit den erforderlichen Endpunkten kommunizieren, da der Datenverkehr durch eine Firewall blockiert wird. Dies ist besonders häufig bei Webanwendungs-Proxyservern der Fall. Stellen Sie sicher, dass Sie die ausgehende Kommunikation mit den erforderlichen Endpunkten und Ports zugelassen haben. Ausführliche Informationen finden Sie im Abschnitt [Anforderungen](how-to-connect-health-agent-install.md#requirements).
* Die ausgehende Kommunikation wird durch die Netzwerkebene einer SSL-Überprüfung unterzogen. Dies bewirkt, dass das vom Agent verwendete Zertifikat durch den Überprüfungsserver/die Überprüfungsentität ersetzt wird und nicht die Schritte zum Abschluss der Agent-Registrierung ausführt.
* Der Benutzer verfügt nicht über die Zugriffsrechte, um die Registrierung des Agents auszuführen. Globale Administratoren haben standardmäßig Zugriff. Sie können den Zugriff mit der [Rollenbasierten Zugriffssteuerung](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) an andere Benutzer delegieren.

**F: Ich erhalte die Warnmeldung „Die Daten des Integritätsdiensts sind nicht aktuell“. Wie kann ich das Problem beheben?**

Diese Warnung wird von Azure AD Connect Health generiert, wenn er nicht in den letzten zwei Stunden alle Datenpunkte vom Server erhalten hat. [Weitere Informationen](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Fragen zum Betrieb
**F: Muss ich die Überwachung auf den Webanwendungsproxy-Servern aktivieren?**

Nein, die Überwachung muss auf den Webanwendungs-Proxyservern nicht aktiviert werden.

**F: Wie werden Azure AD Connect Health-Warnungen aufgelöst?**

Azure AD Connect Health-Warnungen-Warnungen werden basierend auf einer Erfolgsbedingung aufgelöst. Azure AD Connect Health-Agents erkennen und melden die Erfolgsbedingungen in regelmäßigen Abständen an den Dienst. Bei einigen Warnungen gilt eine zeitbasierte Unterdrückung. Dies bedeutet: Wenn die gleiche Fehlerbedingung nicht innerhalb von 72 Stunden nach Generieren der Warnung erneut festgestellt wird, wird die Warnung automatisch aufgelöst.

**F: Ich habe die Meldung „Fehler der Testauthentifizierungsanforderungen (synthetischen Transaktionen) beim Abrufen eines Tokens“ erhalten. Wie kann ich das Problem beheben?**

Azure AD Connect Health für Active Directory-Verbunddienste (AD FS) generiert diese Warnung, wenn der auf einem AD FS-Server installierte Integritäts-Agent ein Token im Rahmen einer synthetischen Transaktion nicht abrufen kann, die vom Integritäts-Agent ausgelöst wurde. Der Integritäts-Agent verwendet den Kontext des lokalen Systems und versucht, ein Token für eine sich selbst vertrauende Seite abzurufen. Dies ist ein „catch-all“-Test, mit dem sichergestellt wird, dass AD FS Token ausstellt.

Dieser Test schlägt meistens fehl, weil der Integritäts-Agent den Namen der AD FS-Farm nicht auflösen kann. Dies kann geschehen, wenn die AD FS-Server hinter einem Netzwerk-Load Balancer sind und die Anforderung von einem Knoten initiiert wird, der sich hinter dem Load Balancer befindet (im Gegensatz zu einem regulären Client, der vor einem Load Balancer ist). Dies kann durch Aktualisieren der „Hosts“-Datei unter "C:\Windows\System32\drivers\etc" behoben werden. Nach dem Update muss die IP-Adresse des AD FS-Servers oder einer IP-Loopbackadresse (127.0.0.1) für den AD FS-Farmnamen (z.B. sts.contoso.com) enthalten sein. Das Hinzufügen der Hostdatei verursacht einen Kurzschluss im Netzwerksaufruf, sodass der Integritäts-Agent das Token abrufen darf.

**F: Ich habe eine E-Mail mit dem Hinweis erhalten, dass meine Computer NICHT für die aktuellen Ransomewareangriffe gepatcht wurden. Warum habe ich diese E-Mail erhalten?**

Azure AD Connect Health scannt alle Computer, die es überwacht, und stellt sicher, dass die erforderlichen Patches installiert wurden. Die E-Mail wurde an die Mandantenadministratoren gesendet, wenn auf mindestens einem Computer nicht kritische Patches vorhanden waren. Die folgende Logik wurde verwendet, um diese Entscheidung zu treffen.
1. Suchen Sie alle auf dem Computer installierten Hotfixes.
2. Überprüfen Sie, ob mindestens eines der Hotfixes aus der definierten Liste vorhanden ist.
3. Falls dem so ist, wird der Computer geschützt. Wenn nicht, besteht für den Computer das Risiko eines Angriffs.

Sie können diese Überprüfung mithilfe des folgenden PowerShell-Skripts manuell durchführen. Es implementiert die oben genannten Logik.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**F: Warum zeigt das PowerShell-Cmdlet <i>Get-MsolDirSyncProvisioningError</i> weniger Synchronisierungsfehler im Ergebnis an?**

<i>Get-MsolDirSyncProvisioningError</i> gibt nur Bereitstellungsfehler vom Typ „DirSync“ zurück. Außerdem zeigt das Connect Health-Portal auch andere Synchronisierungsfehlertypen wie beispielsweise Exportfehler an. Dies ist mit dem Azure AD Connect-Deltaergebnis konsistent. Lesen Sie mehr zu [Azure AD Connect-Synchronisierungsfehlern](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**F: Warum werden meine AD FS-Überwachungen nicht generiert?**

Vergewissern Sie sich mithilfe des PowerShell-Cmdlets <i>Get-AdfsProperties -AuditLevel</i>, dass Überwachungsprotokolle nicht deaktiviert sind. Weitere Informationen zu ADFS-Überwachungsprotokollen finden Sie [hier](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Hinweis: Wenn erweiterte Überwachungseinstellungen mithilfe von Push an den ADFS-Server übertragen werden, werden alle mit „auditpol.exe“ vorgenommenen Änderungen überschrieben. (Das gilt auch, wenn „Anwendung wurde generiert“ nicht konfiguriert ist.) Konfigurieren Sie die lokale Sicherheitsrichtlinie in diesem Fall so, dass Fehler- und Erfolgsereignisse im Zusammenhang mit „Anwendung wurde generiert“ protokolliert werden.

**F: Wann wird das Agent-Zertifikat vor dem Ablauf automatisch verlängert?**
Das Agent-Zertifikat wird **6 Monate** vor seinem Ablaufdatum automatisch verlängert. Wenn es nicht verlängert wird, stellen Sie sicher, dass die Netzwerkverbindung des Agent stabil ist. Ein Neustart der Agent-Dienste oder ein Update auf die neueste Version löst das Problem möglicherweise auch.


## <a name="related-links"></a>Verwandte Links
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-Vorgänge](how-to-connect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)
* [Verwenden von Azure AD Connect Health mit AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – Versionsverlauf](reference-connect-health-version-history.md)
