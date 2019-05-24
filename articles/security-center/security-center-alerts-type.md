---
title: Sicherheitswarnungen nach Typ in Azure Security Center | Microsoft-Dokumentation
description: In diesem Artikel werden die verschiedenen Arten von Sicherheitswarnungen beschrieben, die in Azure Security Center verfügbar sind.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: v-mohabe
ms.openlocfilehash: 4592caacf7f73e4bce9f974fb3bb2ab3ed1a89ff
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968359"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Verstehen der Sicherheitswarnungen in Azure Security Center
In diesem Artikel werden die verschiedenen Arten von Sicherheitswarnungen und verwandte Informationen beschrieben, die in Azure Security Center verfügbar sind. Weitere Informationen zur Verwaltung von Warnungen und Vorfällen finden Sie unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md).

Führen Sie ein Upgrade auf Azure Security Center Standard durch, um erweiterte Erkennungsfunktionen einzurichten. Eine kostenlose Testversion ist verfügbar. Wählen Sie in der [Sicherheitsrichtlinie](tutorial-security-policy.md) den **Tarif** aus, wenn Sie ein Upgrade durchführen möchten. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-type-of-alerts-are-available"></a>Welche Art von Warnungen ist verfügbar?
Azure Security Center verwendet eine Vielzahl von [Erkennungsfunktionen](security-center-detection-capabilities.md), um Kunden vor potenziellen Angriffen auf ihre Umgebungen zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. Die in einer Warnung enthaltenen Informationen variieren je nach Typ der Analyse, mit der die Bedrohung erkannt wird. Vorfälle können auch zusätzliche Kontextinformationen beinhalten, die bei der Untersuchung einer Bedrohung hilfreich sein können.  Dieser Artikel enthält Informationen über die folgenden Warnungstypen:

* Verhaltensanalyse von VMs (Virtual Machine Behavioral Analysis, VMBA)
* Netzwerkanalyse
* SQL-Datenbank und SQL Data Warehouse-Analyse
* Kontextinformationen

## <a name="virtual-machine-behavioral-analysis"></a>Verhaltensanalyse von VMs
In Azure Security Center kann die Verhaltensanalyse verwendet werden, um kompromittierte Ressourcen basierend auf der Analyse von VM-Ereignisprotokollen zu ermitteln. Beispiele hierfür sind Prozesserstellungsereignisse und Anmeldeereignisse. Außerdem ist eine Korrelation mit anderen Signalen vorhanden, damit weitere Beweise für eine größere Aktion ermittelt werden können.

> [!NOTE]
> Weitere Informationen zur Funktionsweise der Security Center-Erkennungsfunktionen finden Sie unter [Azure Security Center-Erkennungsfunktionen](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Ereignisanalyse
Security Center nutzt erweiterte Analysen, um auf der Grundlage der Analyse von VM-Ereignisprotokollen kompromittierte Ressourcen zu ermitteln. Beispiele hierfür sind Prozesserstellungsereignisse und Anmeldeereignisse. Außerdem ist eine Korrelation mit anderen Signalen vorhanden, damit weitere Beweise für eine größere Aktion ermittelt werden können.

* **Ausführung verdächtiger Prozesse erkannt**: Angreifer versuchen häufig, schädlichen Code als unschädliche Prozesse zu tarnen, damit sie nicht entdeckt werden. Warnungen dieser Art geben an, dass eine Prozessausführung einem der folgenden Muster entsprach:
    * Es wurde ein Prozess ausgeführt, von dem bekannt ist, dass er für schädliche Zwecke verwendet wird. Die einzelnen Befehle erscheinen möglicherweise ungefährlich, die Warnung basiert jedoch auf einer Aggregation dieser Befehle.
    * Ein Prozess wurde von einem ungewöhnlichen Ort aus ausgeführt.
    * Ein Prozess wurde von einem Ort aus ausgeführt, an dem sich auch bekannte verdächtige Dateien befinden.
    * Ein Prozess wurde von einem verdächtigen Pfad aus ausgeführt.
    * Ein Prozess wurde in einem ungewöhnlichen Kontext ausgeführt.
    * Ein Prozess wurde von einem ungewöhnlichen Konto ausgeführt.
    * Ein Prozess mit verdächtiger Erweiterung wurde ausgeführt.
    * Ein Prozess mit verdächtiger doppelter Erweiterung wurde ausgeführt.
    * Ein Prozess, dessen Dateiname ein verdächtiges RLO-Zeichen (Zeichen mit Rechts-nach-links-Schreibrichtung) enthält, wurde ausgeführt.
    * Es wurde ein Prozess ausgeführt, dessen Name Ähnlichkeit mit dem Namen eines häufig ausgeführten Prozesses aufweist, diesem aber nicht exakt entspricht.
    * Ein Prozess, dessen Name einem bekannten Angriffstool entspricht, wurde ausgeführt.
    * Ein Prozess mit einem Zufallsnamen wurde ausgeführt.
    * Ein Prozess mit verdächtiger Erweiterung wurde ausgeführt.
    * Eine versteckte Datei wurde ausgeführt.
    * Ein Prozess wurde als untergeordnetes Element eines anderen, in keinem Zusammenhang stehenden Prozesses ausgeführt.
    * Von einem Systemprozess wurde ein ungewöhnlicher Prozess erstellt.
    * Vom Windows Update-Dienst wurde ein ungewöhnlicher Prozess gestartet.
    * Ein Prozess wurde mit einer ungewöhnlichen Befehlszeile ausgeführt. Dies kann darauf hindeuten, dass ein legitimer Prozess zur Ausführung schädlicher Inhalte gekapert wurde.
    * Es wurde versucht, über die Befehlszeile alle ausführbaren Dateien (EXE-Dateien) in einem Verzeichnis zu starten.
    * Ein Prozess wurde vom PsExec-Hilfsprogramm (das zur Remoteausführung von Prozessen verwendet werden kann) ausgeführt.
    * Die übergeordnete ausführbare Datei von Apache Tomcat® (Tomcat#.exe) wurde verwendet, um verdächtige untergeordnete Prozesse zu starten, die zum Hosten oder Starten schädlicher Befehle geeignet sind.
    * Der Programmkompatibilitäts-Assistent (pcalua.exe) von Microsoft Windows wurde verwendet, um potenziell schädlichen ausführbaren Code zu starten.
    * Eine verdächtige Häufung von Prozessbeendigungen wurde erkannt.
    * Der SVCHOST-Systemprozess wurde in einem ungewöhnlichen Kontext ausgeführt.
    * Der SVCHOST-Systemprozess wurde in einer seltenen Dienstgruppe ausgeführt.
    * Eine verdächtige Befehlszeile wurde ausgeführt.
    * Ein PowerShell-Skript weist Merkmale auf, die auch in bekannten verdächtigen Skripts vorkommen.
    * Ein bekanntes schädliches PowerShell-Powersploit-Cmdlet wurde ausgeführt.
    * Ein integrierter SQL-Benutzer hat einen Prozess ausgeführt, den er normalerweise nicht ausführt.
    * Eine Base64-codierte ausführbare Datei wurde erkannt, was möglicherweise auf einen Angreifer hindeutet, der zur Verschleierung versucht, über eine Reihe von Befehlen eine ausführbare Datei zu erstellen.

* **Verdächtige RDP-Ressourcenaktivität**: Angreifer nehmen oftmals offene Verwaltungsports wie RDP (Remotedesktopprotokoll) mit Brute-Force-Angriffen ins Visier. Warnungen dieser Art weisen auf verdächtige Remotedesktop-Anmeldeaktivitäten hin:
    * Es wurde versucht, Remotedesktopanmeldungen durchzuführen.
    * Es wurde versucht, Remotedesktopanmeldungen mit ungültigen Konten durchzuführen.
    * Es wurde versucht, Remotedesktopanmeldungen durchzuführen, und einige davon waren erfolgreich.
* **Verdächtige SSH-Ressourcenaktivität**: Angreifer nehmen oftmals offene Verwaltungsports wie SSH mit Brute-Force-Angriffen ins Visier. Warnungen dieser Art weisen auf verdächtige SSH-Anmeldeaktivitäten hin:
    * Es wurde erfolglos versucht, SSH-Anmeldungen durchzuführen.
    * Es wurde versucht, SSH-Anmeldungen durchzuführen, und einige davon waren erfolgreich.
* **Verdächtiger WindowPosition-Registrierungswert**: Diese Warnung weist darauf hin, dass versucht wurde, den WindowPosition-Registrierungswert zu ändern, was wiederum auf den Versuch hindeuten kann, Anwendungsfenster in nicht sichtbaren Desktopbereichen zu verbergen.
* **Potenzieller Versuch, AppLocker zu umgehen**: Mit AppLocker kann die Ausführung von Prozessen unter Windows (und somit die Ausführung von Schadsoftware) eingeschränkt werden. Diese Warnung weist darauf hin, dass möglicherweise versucht wurde, AppLocker-Einschränkungen zu umgehen, indem mithilfe vertrauenswürdiger ausführbarer Dateien (die durch die AppLocker-Richtlinie zugelassen werden) nicht vertrauenswürdiger Code ausgeführt wird.
* **Verdächtige Named Pipe-Kommunikation**: Diese Warnung weist darauf hin, dass Daten über einen Windows-Konsolenbefehl in eine lokale Named Pipe geschrieben wurden. Named Pipes werden gerne von Angreifern verwendet, um mit einer schädlichen Einschleusung zu kommunizieren und ihr Anweisungen zu erteilen.
* **Decodierung einer ausführbaren Datei mithilfe des integrierten Tools „certutil.exe“**: Diese Warnung weist darauf hin, dass eine ausführbare Datei mithilfe eines integrierten Administratorhilfsprogramms (certutil.exe) decodiert wurde. Es ist bekannt, dass Angreifer Funktionen legitimer Administratortools für schädliche Aktionen missbrauchen und beispielsweise ein Tool wie „certutil.exe“ verwenden, um eine schädliche ausführbare Datei zu decodieren, die anschließend ausgeführt wird.
* **Ein Ereignisprotokoll wurde gelöscht**: Diese Warnung weist auf einen verdächtigen Löschvorgang für ein Ereignisprotokoll hin. Diese Technik wird oft von Angreifern verwendet, um ihre Spuren zu verwischen.
* **Deaktivieren und Löschen von IIS-Protokolldateien**: Diese Warnung weist darauf hin, dass IIS-Protokolldateien deaktiviert und/oder gelöscht wurden. Diese Technik wird oft von Angreifern verwendet, um ihre Spuren zu verwischen.
* **Verdächtige Dateilöschung**: Diese Warnung weist auf eine verdächtige Löschung von Dateien hin. Diese Technik kann von einem Angreifer verwendet werden, um Beweise für schädliche Binärdateien zu beseitigen.
* **Alle Schattenkopien von Dateien wurden gelöscht**: Diese Warnung weist auf die Löschung von Schattenkopien hin.
* **Verdächtige Dateibereinigungsbefehle**: Diese Warnung weist auf eine Kombination aus systeminfo-Befehlen hin, die zum Ausführen einer selbstständigen Bereinigungsaktivität nach einer Kompromittierung dienen.  *systeminfo.exe* ist ein legitimes Windows-Tool, es wird jedoch selten wie hier zweimal hintereinander und gefolgt von einem Löschbefehl ausgeführt.
* **Verdächtige Kontoerstellung**: Diese Warnung weist darauf hin, dass ein Konto erstellt wurde, das große Ähnlichkeit mit einem vorhandenen integrierten Konto mit Administratorrechten hat. Dieses Verfahren kann von Angreifern verwendet werden, um unentdeckt ein nicht autorisiertes Konto zu erstellen.
* **Verdächtige Volumeschattenkopie-Aktivität**: Diese Warnung weist auf eine Schattenkopie-Löschaktivität für die Ressource hin. Volumeschattenkopie (Volume Shadow Copy, VSC) ist ein wichtiges Artefakt, das Datenmomentaufnahmen speichert. Diese Aktivität wird mit Ransomware in Verbindung gebracht, kann aber auch legitim sein.
* **Persistenzmodus der Windows-Registrierung**: Diese Warnung weist darauf hin, dass versucht wurde, eine ausführbare Datei in der Windows-Registrierung zu verankern. Malware verwendet dieses Verfahren häufig, um einen Systemstart zu überstehen.
* **Verdächtige neue Firewallregel**: Diese Warnung weist darauf hin, dass über *netsh.exe* eine neue Firewallregel hinzugefügt wurde, die Datenverkehr von einer ausführbaren Datei an einem verdächtigen Ort zulässt.
* **Verdächtige XCOPY-Ausführungen**: Diese Warnung weist auf eine Reihe von XCOPY-Ausführungen hin, was ein Indiz dafür sein kann, dass einer Ihrer Computer kompromittiert und zur Verteilung von Schadsoftware verwendet wurde.
* **Unterdrückung der Anzeige eines rechtlichen Hinweises für Benutzer bei der Anmeldung**: Diese Warnung weist auf eine Änderung des Registrierungsschlüssels hin, der steuert, ob Benutzern bei der Anmeldung ein rechtlicher Hinweis angezeigt wird. Diese Aktivität wird von Angreifern häufig durchgeführt, nachdem ein Host kompromittiert wurde.
* **Ungewöhnliche Mischung aus Groß- und Kleinbuchstaben in der Befehlszeile erkannt**: Diese Warnung weist auf die Verwendung einer Kombination von Groß- und Kleinbuchstaben in der Befehlszeile hin. Diese Technik wird von Angreifern genutzt, um einer Entdeckung durch Computerregeln mit Berücksichtigung der Groß-/Kleinschreibung oder einer Entdeckung durch hashbasierte Computerregeln zu entgehen.
* **Verschleierte Befehlszeile**: Diese Warnung weist auf die Erkennung verdächtiger Verschleierungsindikatoren in der Befehlszeile hin.
* **Mehrere Domänenkonten abgefragt**: Angreifer fragen häufig AD-Domänenkonten ab, um Benutzer, Domänenadministratorkonten, Domänencontroller und Vertrauensstellungen zwischen Domänen auszukundschaften. Diese Warnung weist darauf hin, dass innerhalb eines kurzen Zeitraums eine ungewöhnlich hohe Anzahl unterschiedlicher Domänenkonten abgefragt wurde.
* **Mögliche lokale Reconnaissance-Aktivität**: Diese Warnung weist darauf hin, dass eine Kombination von systeminfo-Befehlen ausgeführt wurde, die mit Reconnaissance-Aktivitäten in Verbindung gebracht werden.  *systeminfo.exe* ist zwar ein legitimes Windows-Tool, wird jedoch selten zweimal hintereinander ausgeführt.
* **Mögliche Ausführung der ausführbaren Datei eines Schlüsselgenerierungstools**: Diese Warnung weist auf die Ausführung eines Prozesses hin, dessen Name auf ein Schlüsselgenerierungstool hindeutet. Solche Tools dienen in der Regel zum Aushebeln von Softwarelizenzierungsmechanismen und werden häufig zusammen mit anderer Schadsoftware heruntergeladen.
* **Verdächtige Ausführung über „rundll32.exe“**: Diese Warnung weist darauf hin, dass über „rundll32.exe“ ein Prozess mit einem ungewöhnlichen Namen ausgeführt wurde, der dem Prozessbenennungsschema entspricht, das von Angreifern verwendet wird, um auf einem kompromittierten Host eine Einschleusung der ersten Stufe zu installieren.
* **Verdächtige Kombination aus HTA und PowerShell**: Diese Warnung weist darauf hin, dass ein Microsoft-HTML-Anwendungshost (HTA) PowerShell-Befehle startet. Dieses Verfahren wird von Angreifern genutzt, um schädliche PowerShell-Skripts zu starten.
* **Änderung eines Registrierungsschlüssels, der zur Umgehung der Benutzerkontensteuerung missbraucht werden kann**: Diese Warnung weist darauf hin, dass ein Registrierungsschlüssel, der zur Umgehung der Benutzerkontensteuerung (User Account Control, UAC) missbraucht werden kann, geändert wurde. Dieses Verfahren wird häufig von Angreifern verwendet, um auf einem kompromittierten Host von nicht privilegiertem Zugriff (Standardbenutzer) zu privilegiertem Zugriff (beispielsweise Administrator) zu gelangen.
* **Verwendung verdächtiger Domänennamen in der Befehlszeile**: Diese Warnung weist auf die Verwendung eines verdächtigen Domänennamens hin, was ein Indiz dafür sein kann, dass ein Angreifer schädliche Tools und Endpunkte für Befehle und Steuerung (Command and Control, C&C) sowie für die Exfiltration von Daten hostet.
* **Ein Konto wurde innerhalb von 24 Stunden auf mehreren Hosts erstellt**: Diese Warnung weist darauf hin, dass versucht wurde, das gleiche Benutzerkonto auf mehreren Hosts zu erstellen. Dies kann ein Indiz dafür sein, dass sich ein Angreifer nach der Kompromittierung von Netzwerkentitäten seitlich durch das Netzwerk bewegt.
* **Verdächtige Verwendung von CACLS zur Verringerung des Sicherheitsniveaus des Systems**: Diese Warnung weist auf eine Änderung der CACLS (Change Access Control List) hin. Diese Vorgehensweise wird häufig von Angreifern verwendet, um Systembinärdateien wie „ftp.exe“, „net.exe“ oder „wscript.exe“ Vollzugriff zu gewähren.
* **Mögliche Parameter für Kerberos Golden Ticket-Angriff**: Diese Warnung weist darauf hin, dass Befehlszeilenparameter vorhanden sind, die mit einem Kerberos Golden Ticket-Angriff in Verbindung gebracht werden. Mit einem kompromittierten krbtgt-Schlüssel kann ein Angreifer die Identität eines beliebigen Benutzers annehmen.
* **Aktivierung des Registrierungsschlüssels „WDigest UseLogonCredential“**: Diese Warnung weist darauf hin, dass der Registrierungsschlüssel geändert wurde, um das Speichern von Klartext-Anmeldeinformationen im LSA-Speicher zuzulassen, die dann aus dem Speicher abgegriffen werden können.
* **Potenziell verdächtige Verwendung des Telegram-Tools**: Diese Warnung weist auf die Installation von Telegram hin, eines kostenlosen, cloudbasierten Chatdiensts, der von Angreifern verwendet wird, um schädliche Binärdateien an andere Computer, Smartphones oder Tablets zu übertragen.
* **Erstellung eines neuen ASEP**: Diese Warnung weist auf die Erstellung eines neuen Autostart-Erweiterungspunkts (ASEP) hin, wodurch der in der Befehlszeile angegebene Prozess automatisch gestartet wird und von einem Angreifer zur Erreichung von Persistenz genutzt werden kann.
* **Verdächtige Änderungen für „Set-ExecutionPolicy“ und WinRM**: Diese Warnung weist auf Konfigurationsänderungen hin, die mit der Verwendung der schädlichen ChinaChopper-Webshell in Verbindung gebracht werden.
* **Deaktivierung wichtiger Dienste**: Diese Warnung weist darauf hin, dass mit dem Befehl „net.exe stop“ wichtige Dienste wie SharedAccess oder das Windows-Sicherheitscenter beendet wurden.
* **Verdächtige Verwendung des FTP-Switchs „-s“**: Diese Warnung weist auf die Verwendung der FTP-Option „-s“ hin. Diese kann von Schadsoftware zum Herstellen einer Verbindung mit einem FTP-Remoteserver und zum Herunterladen weiterer schädlicher Binärdateien verwendet werden.
* **Verdächtige Ausführung des Befehls „VBScript.Encode“**: Diese Warnung weist auf die Ausführung des Befehls *VBScript.Encode* hin. Dieser Befehl codiert Skripts in unlesbaren Text, was die Untersuchung des Codes erschwert.
* **VBScript-HTTP-Objektzuordnung**: Diese Warnung weist auf die Erstellung einer VBScript-Datei über die Eingabeaufforderung hin, eine Vorgehensweise, die zum Herunterladen schädlicher Dateien verwendet werden kann.
* **Auf der Einrastfunktion basierender Angriff**: Diese Warnung weist darauf hin, dass sich ein Angreifer unter Umständen über eine Binärdatei für die Barrierefreiheit (etwa die Einrastfunktion, die Bildschirmtastatur oder die Sprachausgabe) Hintertürzugriff verschafft.
* **Anzeichen für Petya-Ransomware**: Diese Warnung weist auf die Erkennung von Techniken hin, die mit der Petya-Ransomware in Verbindung gebracht werden.
* **AMSI-Deaktivierungsversuch**: Diese Warnung weist darauf hin, dass versucht wurde, die Überprüfungsschnittstelle der Antischadsoftware (Antimalware Scan Interface, AMSI) zu deaktivieren, was die Deaktivierung der Schadsoftwareerkennung zur Folge hätte.
* **Ransomware-Indikatoren**: Diese Warnung weist auf verdächtige Aktivitäten hin, die üblicherweise mit Sperrbildschirm- und Verschlüsselungsransomware in Verbindung stehen.
* **Verdächtige Ausgabedatei der Ablaufverfolgungssammlung**: Diese Warnung weist darauf hin, dass eine Ablaufverfolgung (beispielsweise für Netzwerkaktivitäten) erfasst und an einen ungewöhnlichen Dateityp ausgegeben wurde.
* **Software mit hohem Risiko**: Diese Warnung weist auf die Verwendung von Software hin, die mit der Installation von Schadsoftware in Verbindung gebracht wird. Angreifer erstellen häufig Pakete, die neben ungefährlichen Tools (wie dem in dieser Warnung enthaltenen) auch Schadsoftware enthalten, und installieren diese automatisch im Hintergrund.
* **Verdächtige Dateierstellung**: Diese Warnung weist auf die Erstellung oder Ausführung eines Prozesses hin, der von Angreifern verwendet wird, um nach dem Öffnen einer Anlage in einem Phishingdokument zusätzliche Schadsoftware auf einen kompromittierten Host herunterzuladen.
* **Verdächtige Anmeldeinformationen in der Befehlszeile**: Diese Warnung weist darauf hin, dass zum Ausführen einer Datei ein verdächtiges Kennwort verwendet wurde. Diese Technik wurde von Angreifern zum Ausführen der Pirpi-Schadsoftware verwendet.
* **Mögliche Ausführung eines Schadsoftware-Droppers**: Diese Warnung weist auf einen Dateinamen hin, der von Angreifern zum Installieren von Schadsoftware verwendet wurde.
* **Verdächtige Ausführung über „rundll32.exe“**: Diese Warnung weist darauf hin, dass mit „rundll32.exe“ eine ausführbare Datei vom Typ „notepad.exe“ oder „reg.exe“ ausgeführt wird, was der Prozesseinschleusungstechnik von Angreifern gleicht.
* **Verdächtige Befehlszeilenargumente**: Diese Warnung weist auf verdächtige Befehlszeilenargumente hin, die in Verbindung mit einer umgekehrten, von der HYDROGEN-Aktivitätsgruppe verwendeten Shell verwendet wurden.
* **Verdächtige Dokumentanmeldeinformationen**: Diese Warnung weist auf einen verdächtigen, gängigen vorausberechneten Kennworthash hin, der von Schadsoftware zum Ausführen einer Datei verwendet wird.
* **Dynamische PS-Skripterstellung**: Diese Warnung weist auf die dynamische Erstellung eines PowerShell-Skripts hin. Angreifer können auf diese Weise progressiv ein Skript erstellen, um IDS-Systeme zu umgehen.
* **Metasploit-Indikatoren**: Diese Warnung weist auf Aktivitäten in Verbindung mit dem Metasploit Framework hin, das eine Reihe von Funktionen und Tools für Angreifer bereitstellt.
* **Verdächtige Kontoaktivität**: Diese Warnung weist darauf hin, dass versucht wurde, über ein Konto, das vor Kurzem kompromittiert wurde, eine Verbindung mit einem Computer herzustellen.
* **Kontoerstellung**: Diese Warnung weist auf die Erstellung eines neuen Kontos auf dem Computer hin.


### <a name="crash-analysis"></a>Absturzanalyse


Die Absturzabbild-Speicheranalyse ist ein Verfahren zum Erkennen von anspruchsvoller Schadsoftware, mit der herkömmliche Sicherheitslösungen umgangen werden können. Mit verschiedenen Arten von Schadsoftware wird versucht, die Wahrscheinlichkeit für die Entdeckung durch Antivirenprogramme zu verringern. Zu diesem Zweck werden niemals Daten auf Datenträger geschrieben oder auf Datenträger geschriebene Softwarekomponenten verschlüsselt. Mit dieser Technik wird erreicht, dass die Schadsoftware mit herkömmlichen Antischadsoftware-Verfahren nur schwer erkannt werden kann. Schadsoftware dieser Art kann aber mithilfe der Arbeitsspeicheranalyse erkannt werden, da die Schadsoftware Spuren im Arbeitsspeicher hinterlassen muss, um funktionieren zu können.

Beim Absturz von Software wird in einem Absturzabbild ein Teil des Arbeitsspeichers zum Zeitpunkt des Absturzes erfasst. Der Absturz kann auf Schadsoftware, allgemeine Anwendungsprobleme oder Systemfehler zurückzuführen sein. Indem die Arbeitsspeicherdaten im Absturzabbild analysiert werden, kann Security Center Verfahren erkennen, die für folgende Zwecke verwendet werden: Ausnutzen von Schwachstellen in Software, Zugreifen auf vertrauliche Daten und Bewegen auf einem kompromittierten Computer. Dies wird mit einer minimalen Auswirkung auf die Leistung von Hosts erreicht, da die Analyse vom Security Center-Back-End durchgeführt wird.

* **Erkennung einer Codeinjektion**: Codeinjektion ist das Einfügen von ausführbaren Modulen in aktive Prozesse oder Threads. Diese Technik wird von Schadsoftware genutzt, um auf Daten zuzugreifen, Daten auszublenden oder ihre Entfernung zu verhindern (z.B. Persistenz). Die Warnung weist darauf hin, dass ein injiziertes Modul im Absturzabbild vorhanden ist. Seriöse Softwareentwickler führen gelegentlich eine Codeinjektion aus nicht böswilligen Gründen durch, beispielsweise zum Ändern oder Erweitern einer vorhandenen Anwendung oder Betriebssystemkomponente. Zur Unterscheidung zwischen schädlichen und nicht schädlichen injizierten Modulen überprüft Azure Security Center, ob das injizierte Modul einem verdächtigen Verhaltensprofil entspricht. Das Ergebnis dieser Überprüfung wird im Feld „SIGNATURE“ der Warnung angegeben und im Schweregrad der Warnung, der Warnungsbeschreibung und der Schritte zur Behebung der Warnung widergespiegelt.
* **Verdächtiges Codesegment**: Die Warnung zu einem verdächtigen Codesegment weist darauf hin, dass ein Codesegment mit nicht standardmäßigen Methoden zugeordnet wurde, beispielsweise durch reflektierende Einschleusung und Prozessaushöhlung. Weitere Merkmale des Codesegments werden verarbeitet, um Kontext im Hinblick auf die Funktionen und das Verhalten des gemeldeten Codesegments bereitzustellen.
* **Erkennung von Shellcode**: Shellcode ist die Nutzlast, die ausgeführt wird, nachdem eine Schadsoftware ein Sicherheitsrisiko einer Software ausgenutzt hat. Diese Warnung gibt an, dass bei einer Absturzabbild-Analyse ausführbarer Code mit einem Verhalten erkannt wurde, das üblicherweise von schädlichen Nutzlasten gezeigt wird. Zwar kann auch nicht schädliche Software dieses Verhalten aufweisen, aber es ist nicht typisch für normale Vorgehensweisen bei der Softwareentwicklung.
* **Erkennung von Modul-Hijacking**: In Windows werden DLLs (Dynamic Link Libraries) verwendet, um für Software die Nutzung von allgemeinen Windows-Systemfunktionen zu ermöglichen. „DLL-Hijacking“ tritt auf, wenn von Schadsoftware die DLL-Ladereihenfolge geändert wird, um schädliche Nutzlasten in den Arbeitsspeicher zu laden und darin beliebigen Code auszuführen. Mit dieser Warnung wird darauf hingewiesen, dass bei der Absturzabbild-Analyse ein Modul mit ähnlichem Namen erkannt wurde, das über zwei unterschiedliche Pfade geladen wird. Einer der geladenen Pfade stammt von einem gemeinsamen binären Speicherort des Windows-Systems. Seriöse Softwareentwickler ändern gelegentlich die DLL-Ladereihenfolge zu nicht schädlichen Zwecken, z.B. zum Instrumentieren, zum Erweitern des Windows-Betriebssystem oder zum Erweitern von Windows-Anwendungen. Zur besseren Unterscheidung zwischen schädlichen und potenziell unschädlichen Änderungen an der DLL-Ladereihenfolge überprüft Security Center, ob ein geladenes Modul einem verdächtigen Profil entspricht.
* **Erkennung eines Windows-Maskerademoduls**: Für Schadsoftware werden unter Umständen gängige Namen von Windows-Systembinärdateien (z. B. SVCHOST.EXE) oder Modulen (z. B. NTDLL.DLL) genutzt, um eine Verschleierung zu erzielen und die Schadsoftware vor Systemadministratoren zu verbergen. Diese Warnung weist darauf hin, dass die Absturzabbilddatei Module enthält, die zwar die Namen von Windows-Systemmodulen verwenden, aber andere typische Kriterien für Windows-Module nicht erfüllen. Die Analyse der auf dem Datenträger befindlichen Kopie des Maskerademoduls kann weitere Informationen dazu liefern, ob das Modul unschädlich oder schädlich ist.
* **Erkennung der Änderung einer Systembinärdatei**: Mit Schadsoftware können wichtige Systembinärdateien geändert werden, um unerkannt auf Daten zuzugreifen oder sich heimlich dauerhaft auf einem kompromittierten System aufzuhalten. Diese Warnung gibt an, dass bei der Absturzabbild-Analyse erkannt wurde, dass wichtige Binärdateien des Windows-Betriebssystems im Arbeitsspeicher oder auf dem Datenträger geändert wurden. Seriöse Softwareentwickler ändern Systemmodule im Arbeitsspeicher gelegentlich zu nicht schädlichen Zwecken, z.B. für Umwege oder aus Gründen der Anwendungskompatibilität. Zur besseren Unterscheidung zwischen schädlichen und potenziell unschädlichen Modulen wird von Security Center überprüft, ob das geänderte Modul einem verdächtigen Profil entspricht.

## <a name="network-analysis"></a>Netzwerkanalyse
Bei der Security Center-Bedrohungserkennung für Netzwerke werden automatisch Sicherheitsinformationen für Ihren Azure IPFIX-Datenverkehr (Internet Protocol Flow Information Export) erfasst. Diese Informationen, bei denen es sich häufig um korrelierende Informationen aus mehreren Quellen handelt, werden analysiert, um Bedrohungen zu identifizieren.

* **Mögliche eingehende SQL-Brute-Force-Angriffsversuche**: Bei der Analyse des Netzwerkdatenverkehrs wurde verdächtige eingehende SQL-Kommunikation erkannt. Diese Aktivität entspricht versuchten Brute-Force-Angriffen auf SQL Server-Instanzen.
* **Verdächtige eingehende RDP-Netzwerkaktivität mit mehreren Quellen**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche eingehende RDP-Kommunikation (Remotedesktopprotokoll) mit mehreren Quellen erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass eindeutige IP-Adressen eine Verbindung mit Ihrem Computer herstellen, was für diese Umgebung nicht normal ist. Diese Aktivität kann auf einen versuchten Brute-Force-Angriff auf Ihren RDP-Endpunkt über mehrere Hosts (Botnet) hindeuten.
* **Verdächtige eingehende RDP-Netzwerkaktivität**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche eingehende RDP-Kommunikation (Remotedesktopprotokoll) erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass Ihr Computer eine hohe Anzahl eingehender Verbindungen aufweist, was für diese Umgebung nicht normal ist. Diese Aktivität kann auf einen versuchten Brute-Force-Angriff auf Ihren RDP-Endpunkt hindeuten.
* **Verdächtige ausgehende RDP-Netzwerkaktivität für mehrere Ziele**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche ausgehende RDP-Kommunikation (Remotedesktopprotokoll) mit mehreren Zielen erkannt. Diese Aktivität kann darauf hindeuten, dass Ihr Computer kompromittiert ist und für Brute-Force-Angriffe auf externe RDP-Endpunkte verwendet wird. Diese Art von Aktivität kann dazu führen, dass Ihre IP-Adresse von externen Entitäten als schädlich gekennzeichnet wird.
* **Verdächtige ausgehende RDP-Netzwerkaktivität**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche ausgehende RDP-Kommunikation (Remotedesktopprotokoll) erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass Ihr Computer eine hohe Anzahl ausgehender Verbindungen aufweist, was für diese Umgebung nicht normal ist. Diese Aktivität kann darauf hindeuten, dass Ihr Computer kompromittiert ist und für Brute-Force-Angriffe auf externe RDP-Endpunkte verwendet wird. Diese Art von Aktivität kann dazu führen, dass Ihre IP-Adresse von externen Entitäten als schädlich gekennzeichnet wird.
* **Verdächtige eingehende SSH-Netzwerkaktivität**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche eingehende SSH-Kommunikation erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass Ihr Computer eine hohe Anzahl eingehender Verbindungen aufweist, was für diese Umgebung nicht normal ist. Diese Aktivität kann auf einen versuchten Brute-Force-Angriff auf Ihren SSH-Endpunkt hindeuten.
* **Verdächtige eingehende SSH-Netzwerkaktivität mit mehreren Quellen**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche eingehende SSH-Kommunikation erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass eindeutige IP-Adressen eine Verbindung mit Ihrem Computer herstellen, was für diese Umgebung nicht normal ist. Diese Aktivität kann auf einen versuchten Brute-Force-Angriff auf Ihren SSH-Endpunkt über mehrere Hosts (Botnet) hindeuten.
* **Verdächtige ausgehende SSH-Netzwerkaktivität**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche ausgehende SSH-Kommunikation erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass Ihr Computer eine hohe Anzahl ausgehender Verbindungen aufweist, was für diese Umgebung nicht normal ist. Diese Aktivität kann darauf hindeuten, dass Ihr Computer kompromittiert ist und für Brute-Force-Angriffe auf externe SSH-Endpunkte verwendet wird. Diese Art von Aktivität kann dazu führen, dass Ihre IP-Adresse von externen Entitäten als schädlich gekennzeichnet wird.
* **Verdächtige ausgehende SSH-Netzwerkaktivität für mehrere Ziele**: Bei der Analyse des Netzwerkdatenverkehrs wurde ungewöhnliche ausgehende SSH-Kommunikation mit mehreren Zielen erkannt. Anhand der geprüften Netzwerkdaten wurde festgestellt, dass Ihr Computer eine Verbindung mit eindeutigen IP-Adressen herstellt, was für diese Umgebung nicht normal ist. Diese Aktivität kann darauf hindeuten, dass Ihr Computer kompromittiert ist und für Brute-Force-Angriffe auf externe SSH-Endpunkte verwendet wird. Diese Art von Aktivität kann dazu führen, dass Ihre IP-Adresse von externen Entitäten als schädlich gekennzeichnet wird.
* **Netzwerkkommunikation mit einem schädlichen Computer erkannt**: Die Analyse des Netzwerkdatenverkehrs deutet darauf hin, dass Ihr Computer mit einem möglichen Befehls- und Steuerungszentrum (Command and Control Center) kommuniziert hat.
* **Möglicher kompromittierter Computer erkannt**: Bei der Analyse des Netzwerkdatenverkehrs wurde eine ausgehende Aktivität erkannt, die möglicherweise darauf hindeutet, dass der Computer Teil eines Botnets ist. Die Analyse basiert auf IP-Adressen, auf die Ihre Ressource zugegriffen hat, sowie auf öffentlichen DNS-Einträgen.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>SQL-Datenbank und SQL Data Warehouse-Analyse

Bei der Security Center-Ressourcenanalyse liegt der Schwerpunkt auf PaaS-Diensten (Platform-as-a-Service), z.B. der Integration in die [Azure SQL-Datenbank-Bedrohungserkennung](../sql-database/sql-database-threat-detection.md) und Azure SQL Data Warehouse. Die SQL-Bedrohungserkennung erkennt anomale Aktivitäten, die auf ungewöhnliche und ggf. schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese missbraucht werden sollen. Hierbei werden die folgenden Warnungen ausgelöst:

* **Anfälligkeit für die Einschleusung von SQL-Befehlen**: Diese Warnung wird ausgelöst, wenn eine Anwendung in der Datenbank eine fehlerhafte SQL-Anweisung generiert. Dies kann ein Hinweis auf ein mögliches Sicherheitsrisiko in Bezug auf Angriffe mit Einschleusung von SQL-Befehlen sein. Es gibt zwei mögliche Gründe für die Generierung einer fehlerhaften Anweisung:
    * Ein Fehler im Anwendungscode, der zur fehlerhaften SQL-Anweisung führt
    * Anwendungscode oder gespeicherte Prozeduren führen bei der Erstellung der fehlerhaften SQL-Anweisung keine Bereinigung der Benutzereingabe durch, und dies kann für eine Einschleusung von SQL-Befehlen ausgenutzt werden
* **Potenzielle Einschleusung von SQL-Befehlen:** Diese Warnung wird ausgelöst, wenn ein aktiver Exploit für ein identifiziertes Anwendungssicherheitsrisiko in Bezug auf die Einschleusung von SQL-Befehlen besteht. Dies bedeutet, dass der Angreifer versucht, schädliche SQL-Anweisungen einzuschleusen, indem er den anfälligen Anwendungscode bzw. die gespeicherten Prozeduren verwendet.
* **Zugriff von einem ungewöhnlichen Ort:** Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person von einem ungewöhnlichen Ort aus bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (eine neue Anwendung oder Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
* **Zugriff über ein ungewöhnliches Azure-Rechenzentrum:** Diese Warnung wird ausgelöst, wenn auf diesem Server kürzlich eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person über ein ungewöhnliches Azure-Rechenzentrum bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (Ihre neue Anwendung in Azure, Power BI, Azure SQL-Abfrage-Editor). In anderen Fällen erkennt die Warnung ggf. eine schädliche Aktion einer Azure-Ressource bzw. eines -Diensts (ehemaliger Mitarbeiter, externer Angreifer).
* **Zugriff über einen unbekannten Prinzipal:** Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person über einen ungewöhnlichen Prinzipal (SQL-Benutzer) bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (neue Anwendung, Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
* **Zugriff über eine potenziell schädliche Anwendung:** Diese Warnung wird ausgelöst, wenn zum Zugreifen auf die Datenbank eine potenziell schädliche Anwendung verwendet wird. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Angriff mit allgemeinen Angriffstools.
* **Brute-Force-Angriff auf SQL-Anmeldeinformationen:** Diese Warnung wird ausgelöst, wenn eine ungewöhnlich hohe Anzahl von fehlerhaften Anmeldungen mit unterschiedlichen Anmeldeinformationen vorliegt. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Brute-Force-Angriff.

## <a name="contextual-information"></a>Kontextinformationen
Bei einer Untersuchung benötigen Analysten zusätzlichen Kontext, um die Art der Bedrohung und die mögliche Minderung der Auswirkungen zu bewerten.  Wenn beispielsweise eine Netzwerkanomalie erkannt wurde, ist es schwer, die zu ergreifenden Maßnahmen festzulegen, wenn die weiteren Vorgänge in Bezug auf das Netzwerk oder die betroffenen Ressourcen nicht klar sind. Zur Unterstützung in diesem Fall kann ein Sicherheitsincident Artefakte, verwandte Ereignisse und Informationen enthalten, die dem Prüfer helfen können. Ob weitere Informationen verfügbar sind, hängt vom Typ der erkannten Bedrohung und der Konfiguration Ihrer Umgebung ab. Nicht für alle Sicherheitsincidents sind weitere Informationen verfügbar.

Sollten weitere Informationen verfügbar sein, wird dies im Sicherheitsincident unterhalb der Warnungsliste angezeigt. Folgende Informationen können enthalten sein:

- Protokolllöschereignisse
- Von unbekannten Geräten angeschlossene Plug & Play-Geräte
- Warnungen, die kein Eingreifen erfordern
- Erstellung eines neuen Kontos
- Die Datei wurde mit dem CertUtil-Tool entschlüsselt.

![Warnung vor ungewöhnlichem Zugriff](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden die unterschiedlichen Arten von Sicherheitswarnungen in Security Center beschrieben. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Behandeln von Sicherheitswarnungen in Azure Security Center](security-center-incident.md)
* [Azure Security Center-Erkennungsfunktionen](security-center-detection-capabilities.md)
* [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
