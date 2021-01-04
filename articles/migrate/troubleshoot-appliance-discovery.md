---
title: Behandeln von Problemen bei der Bereitstellung und Ermittlung der Azure Migrate-Appliance
description: Erhalten Sie Hilfe bei der Bereitstellung einer Appliance und der Ermittlung von Computern.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 26bb61e0c5a473a56c57391b53009419453956fd
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753415"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Behandeln von Problemen bei der Azure Migrate-Appliance und der Ermittlung

Dieser Artikel hilft Ihnen bei der Behebung von Problemen bei der Bereitstellung der [Azure Migrate](migrate-services-overview.md)-Appliance und bei der Verwendung der Appliance zur Erkennung von lokalen Computern.


## <a name="whats-supported"></a>Was wird unterstützt?

[Überprüfen](migrate-appliance.md) Sie die Anforderungen an die Applianceunterstützung.


## <a name="invalid-ovf-manifest-entry"></a>„Ungültiger OVF-Manifesteintrag“

Wenn Sie den Fehler „Die angegebene Manifestdatei ist ungültig: Ungültiger OVF-Manifesteintrag“ erhalten, gehen Sie wie folgt vor:

1. Überprüfen Sie, ob die OVA-Datei für die Azure Migrate-Appliance ordnungsgemäß heruntergeladen wird, indem Sie deren Hashwert überprüfen. [Weitere Informationen](./tutorial-discover-vmware.md) Wenn der Hashwert nicht übereinstimmt, laden Sie die OVA-Datei erneut herunter, und wiederholen Sie die Bereitstellung.
2. Wenn immer noch Fehler bei der Bereitstellung auftreten und Sie den VMware vSphere-Client zum Bereitstellen der OVF-Datei verwenden, versuchen Sie, die Bereitstellung über den vSphere-Webclient vorzunehmen. Wenn die Bereitstellung weiterhin fehlschlägt, versuchen Sie es mit einem anderen Webbrowser.
3. Wenn Sie den vSphere-Webclient verwenden und die Bereitstellung auf vCenter Server 6.5 oder 6.7 ausführen möchten, versuchen Sie, die OVA-Datei direkt auf dem ESXi-Host bereitzustellen:
   - Stellen Sie mit dem Webclient (https://<*Host-IP-Adresse*>/ui) eine direkte Verbindung mit dem ESXi-Host her (anstelle von vCenter Server).
   - Wählen Sie unter **Startseite** > **Bestand** die Optionen **Datei** > **OVF-Vorlage bereitstellen** aus. Navigieren Sie zur OVA-Datei, und schließen Sie die Bereitstellung ab.
4. Wenn weiterhin ein Fehler bei der Bereitstellung auftritt, wenden Sie sich an den Azure Migrate-Support.

## <a name="cant-connect-to-the-internet"></a>Verbindung mit dem Internet kann nicht hergestellt werden

Dies kann der Fall sein, wenn sich der Appliancecomputer hinter einem Proxy befindet.

- Stellen Sie sicher, dass Sie die Anmeldeinformationen für die Autorisierung angeben, wenn der Proxy diese benötigt.
- Wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Verbindungen verwenden, fügen Sie die [folgenden URLs](migrate-appliance.md#url-access) einer Zulassungsliste hinzu.
- Wenn Sie für die Internetverbindung einen abfangenden Proxy verwenden, importieren Sie das Proxyzertifikat [mit diesen Schritten](./migrate-appliance.md) in die Appliance-VM.

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Anmeldung bei Azure über die Appliance-Web-App ist nicht möglich

Wenn Sie das falsche Konto zur Anmeldung bei Azure verwenden, wird der Fehler „Leider können wir Sie nicht anmelden“ angezeigt. Dieser Fehler hat folgende Gründe:

- Sie melden sich bei der Webanwendung der Appliance für die öffentliche Cloud mit den Anmeldeinformationen des Benutzerkontos für das Portal der Government-Cloud an.
- Sie melden sich bei der Webanwendung der Appliance für die Government-Cloud mit den Anmeldeinformationen des Benutzerkontos für das Portal der öffentlichen Cloud an.

Stellen Sie sicher, dass Sie die richtigen Anmeldeinformationen verwenden.

##  <a name="datetime-synchronization-error"></a>Datums-/Zeitsynchronisierungsfehler

Ein Fehler bei der Datums- und Zeitsynchronisierung (Fehler 802) weist darauf hin, dass die Serveruhr möglicherweise um mehr als fünf Minuten von der aktuellen Uhrzeit abweicht. Passen Sie die Uhrzeit auf dem virtuellen Collector-Computer an die aktuelle Uhrzeit an:

1. Öffnen Sie auf dem virtuellen Computer eine Administratoreingabeaufforderung.
2. Führen Sie **w32tm /tz** aus, um die Zeitzone zu überprüfen.
3. Führen Sie **w32tm /resync** aus, um die Zeit zu synchronisieren.


## <a name="unabletoconnecttoserver"></a>„UnableToConnectToServer“

Wenn dieser Verbindungsfehler angezeigt wird, können Sie möglicherweise keine Verbindung mit vCenter Server „*Servername*.com:9443“ herstellen. Die Fehlerdetails weisen darauf hin, dass unter `https://\*servername*.com:9443/sdk` kein Endpunkt lauscht, der die Nachricht akzeptieren kann.

- Überprüfen Sie, ob Sie die neueste Version der Appliance ausführen. Ist dies nicht der Fall, führen Sie ein Upgrade der Appliance auf die [neueste Version](./migrate-appliance.md) durch.
- Wenn das Problem bei der neuesten Version weiterhin auftritt, ist die Appliance möglicherweise nicht in der Lage, den angegebenen vCenter Server-Namen aufzulösen, oder der angegebene Port ist eventuell falsch. Wenn der Port nicht angegeben ist, versucht Collector standardmäßig, eine Verbindung mit Port 443 herzustellen.

    1. Versuchen Sie, „*Servername*.com“ von der Appliance aus per Ping zu erreichen.
    2. Wenn Schritt 1 nicht erfolgreich ist, können Sie versuchen, eine Verbindung mit vCenter Server über die IP-Adresse herzustellen.
    3. Ermitteln Sie die richtige Portnummer für die Verbindung mit vCenter Server.
    4. Überprüfen Sie, ob vCenter Server ausgeführt wird und betriebsbereit ist.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fehler 60052/60039: Die Appliance ist möglicherweise nicht registriert

- Der Fehler 60052 „Möglicherweise wurde die Appliance nicht erfolgreich beim Azure Migrate-Projekt registriert“ tritt auf, wenn das zum Registrieren der Appliance verwendete Azure-Konto nicht über ausreichende Berechtigungen verfügt.
    - Stellen Sie sicher, dass das für die Registrierung der Appliance verwendete Azure-Benutzerkonto mindestens über „Mitwirkender“-Berechtigungen für das Abonnement verfügt.
    - Weitere Informationen zu den erforderlichen Azure-Rollen und -Berechtigungen finden Sie [hier](./migrate-appliance.md#appliance---vmware).
- Der Fehler 60039 „Möglicherweise wurde die Appliance nicht erfolgreich beim Azure Migrate-Projekt registriert" kann auftreten, wenn die Registrierung fehlschlägt, da das zum Registrieren der Appliance verwendete Azure Migrate Projekt nicht gefunden werden kann.
    - Überprüfen Sie im Azure-Portal, ob das Projekt in der Ressourcengruppe vorhanden ist.
    - Sollte das Projekt nicht vorhanden sein, erstellen Sie in Ihrer Ressourcengruppe ein neues Azure Migrate-Projekt, und registrieren Sie die Appliance erneut. Informationen zum Erstellen eines neuen Projekts finden Sie [hier](./create-manage-projects.md#create-a-project-for-the-first-time).

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fehler 60030/60031: Ein Key Vault-Verwaltungsvorgang war nicht erfolgreich

Wenn der Fehler 60030 oder 60031 „Ein Azure Key Vault-Verwaltungsvorgang war nicht erfolgreich“ auftritt, gehen Sie wie folgt vor:
- Stellen Sie sicher, dass das für die Registrierung der Appliance verwendete Azure-Benutzerkonto mindestens über „Mitwirkender“-Berechtigungen für das Abonnement verfügt.
- Vergewissern Sie sich, dass das Konto Zugriff auf den in der Fehlermeldung angegebenen Key Vault hat, und wiederholen Sie dann den Vorgang.
- Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.
- Weitere Informationen zu den erforderlichen Azure-Rollen und -Berechtigungen finden Sie [hier](./migrate-appliance.md#appliance---vmware).

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fehler 60028: Die Ermittlung konnte nicht initiiert werden

Fehler 60028: „Die Ermittlung konnte aufgrund eines Fehlers nicht initiiert werden. Der Vorgang war für die angegebene Liste von Hosts oder Clustern nicht erfolgreich“. Dieser Fehler weist darauf hin, dass die Ermittlung aufgrund eines Problems beim Zugreifen auf oder Abrufen von VM-Informationen auf den in der Fehlermeldung aufgelisteten Hosts nicht gestartet werden konnte. Die übrigen Hosts wurden erfolgreich hinzugefügt.

- Fügen Sie die in der Fehlermeldung angegebenen Hosts mithilfe der Option **Host hinzufügen** erneut hinzu.
- Gehen Sie im Falle eines Überprüfungsfehlers gemäß der Korrekturanleitung vor, um den Fehler zu beheben, und verwenden Sie dann erneut die Option **Speichern und Ermittlung starten**.

## <a name="error-60025-azure-ad-operation-failed"></a>Fehler 60025: Fehler bei einem Azure AD-Vorgang 
Fehler 60025: „Fehler bei einem Azure AD-Vorgang. Der Fehler trat beim Erstellen oder Aktualisieren der Azure AD-Anwendung auf“. Dieser Fehler tritt auf, wenn zum Initiieren der Ermittlung ein anderes Azure-Benutzerkonto als für die Registrierung der Appliance verwendet wird. Führen Sie eines der folgenden Verfahren aus:

- Stellen Sie sicher, dass zum Initiieren der Ermittlung dasselbe Benutzerkonto verwendet wird wie zum Registrieren der Appliance.
- Weisen Sie dem Benutzerkonto, bei dem der Ermittlungsvorgang fehlgeschlagen ist, Zugriffsberechtigungen für die Azure Active Directory-Anwendung zu.
- Löschen Sie die Ressourcengruppe, die zuvor für das Azure Migrate-Projekt erstellt wurde. Erstellen Sie eine andere Ressourcengruppe, um neu zu beginnen.
- Weitere Informationen zu den Azure Active Directory-Anwendungsberechtigungen finden Sie [hier](./migrate-appliance.md#appliance---vmware).


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fehler 50004: Verbindung mit Host oder Cluster nicht möglich

Fehler 50004: „Es ist keine Verbindung mit einem Host oder Cluster möglich, da der Servername nicht aufgelöst werden kann. WinRM-Fehlercode: 0x803381B9“. Dieser Fehler tritt auf, wenn der Azure DNS-Dienst für die Appliance den von Ihnen angegebenen Cluster- oder Hostnamen nicht auflösen kann.

- Wenn Sie diesen Fehler auf dem Cluster sehen, versuchen Sie, den vollqualifizierten Domänennamen (FQDN) des Clusters anzugeben.
- Sie sehen diesen Fehler eventuell auch für Hosts in einem Cluster. In diesem Fall kann die Appliance zwar eine Verbindung mit dem Cluster herstellen, doch gibt der Cluster Hostnamen zurück, bei denen es sich nicht um FQDNs handelt. Um diesen Fehler zu beheben, aktualisieren Sie die „hosts“-Datei auf der Appliance und fügen Sie ein Mapping von IP-Adresse und Hostnamen hinzu:
    1. Öffnen Sie Notepad als Administrator.
    2. Öffnen Sie die Datei „C:\Windows\System32\Drivers\etc\hosts“.
    3. Fügen Sie die IP-Adresse und den Hostnamen in einer Zeile hinzu. Wiederholen Sie dies für jeden Host oder Cluster, bei dem dieser Fehler angezeigt wird.
    4. Speichern und schließen Sie die Datei „hosts“.
    5. Überprüfen Sie mithilfe der Applianceverwaltungs-App, ob die Appliance eine Verbindung mit den Hosts herstellen kann. Nach 30 Minuten sollten die neuesten Informationen für diese Hosts im Azure-Portal angezeigt werden.


## <a name="error-60001-unable-to-connect-to-server"></a>Fehler 60001: Verbindung mit dem Server konnte nicht hergestellt werden. 

- Stellen Sie sicher, dass eine Verbindung zwischen der Appliance und dem Server besteht.
- Wenn es sich um einen Linux-Server handelt, stellen Sie sicher, dass die kennwortbasierte Authentifizierung mithilfe der folgenden Schritte aktiviert ist:
    1. Melden Sie sich beim Linux-Computer an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
    2. Setzen Sie die Option „PasswordAuthentication“ auf „Ja“. Speichern Sie die Datei .
    3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.
- Wenn es sich um einen Windows-Server handelt, vergewissern Sie sich, dass Port 5985 geöffnet ist, um WMI-Remoteaufrufe zuzulassen.
- Wenn Sie einen GCP-Linux-Server ermitteln und einen Root-Benutzer verwenden, verwenden Sie die folgenden Befehle, um die Standardeinstellung für die Root-Anmeldung zu ändern.
    1. Melden Sie sich beim Linux-Computer an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
    2. Setzen Sie die Option „PermitRootLogin“ auf „Ja“.
    3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.

## <a name="error-no-suitable-authentication-method-found"></a>Error: Es wurde keine passende Authentifizierungsmethode gefunden.

Stellen Sie mithilfe der folgenden Schritte sicher, dass die kennwortbasierte Authentifizierung auf dem Linux-Server aktiviert ist:
    1. Melden Sie sich beim Linux-Computer an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
    2. Setzen Sie die Option „PasswordAuthentication“ auf „Ja“. Speichern Sie die Datei .
    3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.


## <a name="discovered-vms-not-in-portal"></a>Im Portal befinden sich keine ermittelten virtuellen Computer

Wenn der Erkennungszustand „Erkennung wird ausgeführt“ lautet, die virtuellen Computer im Portal aber noch nicht angezeigt werden, warten Sie einige Minuten:
- Es dauert etwa 15 Minuten für eine VMware-VM.
- Es dauert etwa zwei Minuten für jeden hinzugefügten Host für die Hyper-V-VM-Erkennung.

Wenn Sie warten und der Zustand sich nicht ändert, wählen Sie **Aktualisieren** auf der Registerkarte **Server** aus. Dadurch sollte in Azure Migrate die Anzahl der ermittelten Server angezeigt werden: Serverbewertung und Azure Migrate: Servermigration.

Wenn dies nicht funktioniert und Sie VMware-Server erkennen:

- Stellen Sie sicher, dass das von Ihnen angegebene vCenter-Konto über ordnungsgemäß festgelegte Berechtigungen mit Zugriff auf mindestens einen virtuellen Computer verfügt.
- Azure Migrate kann keine VMware-VMs ermitteln, wenn dem vCenter-Konto auf Ebene des vCenter-VM-Ordners Zugriff gewährt wird. [Erfahren Sie mehr](set-discovery-scope.md) über das Beschränken der Ermittlung.

## <a name="vm-data-not-in-portal"></a>VM-Daten nicht im Portal

Wenn ermittelte virtuelle Computer nicht im Portal angezeigt werden oder die VM-Daten veraltet sind, warten Sie einige Minuten. Es dauert bis zu 30 Minuten, bis Änderungen an den ermittelten VM-Konfigurationsdaten im Portal angezeigt werden. Es kann einige Stunden dauern, bis Änderungen an Anwendungsdaten angezeigt werden. Sollten nach dieser Zeit immer noch keine Daten vorhanden sein, versuchen Sie, die Daten wie folgt zu aktualisieren:

1. Wechseln Sie zu **Server** > **Azure Migrate-Serverbewertung**, und wählen Sie **Übersicht** aus.
2. Wählen Sie unter **Verwalten** die Option **Agent-Integritätsdiagnose** aus.
3. Wählen Sie **Agent aktualisieren** aus.
4. Warten Sie, bis der Aktualisierungsvorgang abgeschlossen wurde. Sie sollten nun aktuelle Informationen sehen.

## <a name="deleted-vms-appear-in-portal"></a>Gelöschte VMs werden im Portal angezeigt

Wenn Sie virtuelle Computer löschen und sie weiterhin im Portal angezeigt werden, warten Sie 30 Minuten. Wenn sie weiterhin angezeigt werden, aktualisieren Sie sie wie oben beschrieben.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Ich sehe keine Leistungsdaten für einige Netzwerkadapter auf meinen physischen Servern.

Dies kann vorkommen, wenn die Hyper-V-Virtualisierung auf dem physischen Server aktiviert ist. Aufgrund einer Produktlücke wird der Netzwerkdurchsatz auf den erkannten virtuellen Netzwerkadaptern aufgezeichnet.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Error: Die hochgeladene Datei weist nicht das erwartete Format auf
In einigen Tools sind regionale Einstellungen festgelegt, durch die die CSV-Datei mit Semikolon als Trennzeichen erstellt wird. Ändern Sie die Einstellungen so, dass das Trennzeichen ein Komma ist.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Ich habe eine CSV-Datei importiert, sehe aber, dass die Ermittlung ausgeführt wird
Dieser Status wird angezeigt, wenn das Hochladen Ihrer CSV-Datei aufgrund eines Überprüfungsfehlers fehlgeschlagen ist. Versuchen Sie, die CSV-Datei erneut zu importieren. Sie können den Fehlerbericht des vorherigen Hochladevorgangs herunterladen und der Anleitung zur Fehlerbehebung in der Datei folgen, um die Fehler zu beheben. Der Fehlerbericht kann im Abschnitt „Importdetails“ auf der Seite „Computer ermitteln“ heruntergeladen werden.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Es werden auch nach dem Aktualisieren der Gastanmeldeinformationen keine Anwendungsdetails angezeigt
Die Anwendungsermittlung wird alle 24 Stunden ausgeführt. Wenn die Details sofort angezeigt werden sollen, führen Sie die folgenden Schritte zur Aktualisierung aus. Je nach Anzahl der ermittelten VMs kann dies einige Minuten dauern.

1. Wechseln Sie zu **Server** > **Azure Migrate-Serverbewertung**, und wählen Sie **Übersicht** aus.
2. Wählen Sie unter **Verwalten** die Option **Agent-Integritätsdiagnose** aus.
3. Wählen Sie **Agent aktualisieren** aus.
4. Warten Sie, bis der Aktualisierungsvorgang abgeschlossen wurde. Sie sollten nun aktuelle Informationen sehen.

## <a name="unable-to-export-application-inventory"></a>Anwendungsbestand kann nicht exportiert werden
Stellen Sie sicher, dass der Benutzer, der den Bestand aus dem Portal herunterlädt, über die Berechtigung „Mitwirkender“ für das Abonnement verfügt.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Keine geeignete Authentifizierungsmethode gefunden, um die Authentifizierung abzuschließen (publickey)
Die schlüsselbasierte Authentifizierung wird nicht funktionieren, verwenden Sie die Kennwortauthentifizierung.

## <a name="common-app-discovery-errors"></a>Häufige Fehler bei der App-Ermittlung

Azure Migrate unterstützt die Erkennung von Anwendungen, Rollen und Features mithilfe von Azure Migrate: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen. Die App-Ermittlung wird derzeit nur für VMware unterstützt. [Erfahren Sie mehr](how-to-discover-applications.md) über die Anforderungen und Schritte bei der Einrichtung der App-Ermittlung.

Typische Fehler bei der App-Ermittlung sind in der Tabelle zusammengefasst. 

**Fehler** | **Ursache** | **Aktion**
--- | --- | ---
9000: Der Status der VMware-Tools kann nicht ermittelt werden.     |   Die VMware-Tools sind möglicherweise nicht installiert oder beschädigt.    |   Stellen Sie sicher, dass die VMware-Tools auf der VM installiert sind und ausgeführt werden.
9001: Die VMware-Tools sind nicht installiert.     |   Die VMware-Tools sind möglicherweise nicht installiert oder beschädigt.    |   Stellen Sie sicher, dass die VMware-Tools auf der VM installiert sind und ausgeführt werden.
9002: Die VMware-Tools werden nicht ausgeführt.   |   Die VMware-Tools sind möglicherweise nicht installiert oder beschädigt.    |   Stellen Sie sicher, dass die VMware-Tools auf der VM installiert sind und ausgeführt werden.
9003: Der Betriebssystemtyp wird für die Ermittlung von virtuellen Gastcomputern nicht unterstützt.    |   Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux.    |   Als Betriebssystemtypen werden nur Windows und Linux unterstützt. Wenn der Server tatsächlich Windows oder Linux verwendet, überprüfen Sie den in vCenter Server angegebenen Betriebssystemtyp.
9004: Die VM wird nicht ausgeführt.     |   Die VM wurde ausgeschaltet.  |   Stellen Sie sicher, dass die VM eingeschaltet ist.
9005: Der Betriebssystemtyp wird für die Ermittlung von virtuellen Gastcomputern nicht unterstützt.    |   Der Betriebssystemtyp wird für die Ermittlung von virtuellen Gastcomputern nicht unterstützt.     |   Als Betriebssystemtypen werden nur Windows und Linux unterstützt.
9006: Die URL zum Herunterladen der Metadatendatei vom Gast ist leer.     |   Dies kann vorkommen, wenn der Ermittlungs-Agent nicht erwartungsgemäß funktioniert.    |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
9007: Der Prozess zur Ausführung der Ermittlungsaufgabe auf der Gast-VM wurde nicht gefunden.   |   Dies kann vorkommen, wenn der Ermittlungs-Agent nicht ordnungsgemäß funktioniert.   |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
9008: Der Prozessstatus der Gast-VM kann nicht abgerufen werden.   |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
9009: Die Ausführung der Ermittlungsaufgabe auf dem Server wurde durch die Windows-Benutzerkontensteuerung (UAC) verhindert.  |   Die Einstellungen für die Windows-Benutzerkontensteuerung (UAC) auf dem Server sind sehr restriktiv und verhindern die Ermittlung installierter Anwendungen.  |   Konfigurieren Sie die Einstellungen für die Benutzerkontensteuerung auf dem Server mit einer der beiden niedrigeren Ebenen.
9010: Die VM wurde ausgeschaltet.     |   Die VM wurde ausgeschaltet.  |   Stellen Sie sicher, dass die VM eingeschaltet ist.
9011: Die Datei mit ermittelten Metadaten wurde im Gast-VM-Dateisystem nicht gefunden.    |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
9012: Die ermittelte Metadatendatei ist leer.     |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
9013: Für jede Anmeldung wird ein neues temporäres Profil erstellt.    |   Für jede Anmeldung bei der VMware-VM wird ein neues temporäres Profil erstellt.    |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9014: Die Metadaten können nicht aus dem Gast-VM-Dateisystem abgerufen werden.     |   Keine Verbindung mit dem ESXi-Host.    |   Stellen Sie sicher, dass die Appliance eine Verbindung mit Port 443 auf dem ESXi-Host herstellen kann, auf dem die VM ausgeführt wird.
9015: Die Rolle „Gastvorgänge“ ist für das vCenter-Benutzerkonto nicht aktiviert.   |   Die Rolle „Gastvorgänge“ ist für das vCenter-Benutzerkonto nicht aktiviert.   |   Stellen Sie sicher, dass die Rolle „Gastvorgänge“ für das vCenter-Benutzerkonto aktiviert ist.
9016: Es kann keine Ermittlung durchgeführt werden, weil der Agent für Gastvorgänge veraltet ist.   |   Die VMware-Tools sind nicht ordnungsgemäß installiert oder nicht auf dem neuesten Stand.    |   Stellen Sie sicher, dass die VMware-Tools ordnungsgemäß installiert und auf dem neuesten Stand sind.
9017: Die Datei mit den ermittelten Metadaten wurde auf der VM nicht gefunden.  |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9018: PowerShell ist auf den Gast-VMs nicht installiert.  |   PowerShell ist auf der Gast-VM nicht verfügbar.    |   Installieren Sie PowerShell auf der Gast-VM.
9019: Ermittlung aufgrund von Fehlern beim Gast-VM-Vorgang nicht möglich.     |   Es ist ein Fehler beim VMware-Gastvorgang auf der VM aufgetreten.    |   Stellen Sie sicher, dass die VM-Anmeldeinformationen gültig sind und der Benutzername in den Anmeldeinformationen für die Gast-VM das UPN-Format hat.
9020: Die Berechtigung zum Erstellen von Dateien wurde verweigert.    |   Die dem Benutzer oder der Gruppenrichtlinie zugeordnete Rolle hindert den Benutzer daran, die Datei im Ordner zu erstellen.    |   Überprüfen Sie, ob der angegebene Gastbenutzer über die Berechtigung „Erstellen“ für die Datei im Ordner verfügt. Den Namen des Ordners finden Sie unter **Benachrichtigungen** in der Serverbewertung.
9021: Es kann keine Datei im temporären Systempfad erstellt werden.     |   Das VMware-Tool meldet den temporären Pfad des Systems anstelle des temporären Pfads des Benutzers.    |   Aktualisieren Sie Ihre VMware-Tools auf eine höhere Version als 10287 (NGC/VI-Clientformat).
9022: Der Zugriff auf das WMI-Objekt wird verweigert.    |   Die dem Benutzer zugeordnete Rolle oder die Gruppenrichtlinie schränkt den Zugriff des Benutzers auf das WMI-Objekt ein.  |   Kontaktieren Sie den Microsoft-Support.
9023: PowerShell kann nicht ausgeführt werden, weil der Wert der SystemRoot-Umgebungsvariablen leer ist.    |   Der Wert der SystemRoot-Umgebungsvariablen für die Gast-VM ist leer.     |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9024: Es kann keine Ermittlung durchgeführt werden, weil der Wert der TEMP-Umgebungsvariablen leer ist.    |   Der Wert der TEMP-Umgebungsvariablen für die Gast-VM ist leer.   |   Kontaktieren Sie den Microsoft-Support.
9025: PowerShell ist auf den Gast-VMs beschädigt.  |   PowerShell ist auf der Gast-VM beschädigt.    |   Installieren Sie PowerShell auf der Gast-VM neu, und überprüfen Sie, ob PowerShell auf der Gast-VM ausgeführt werden kann.
9026: Auf der VM können keine Gastvorgänge ausgeführt werden.  |   Der VM-Zustand lässt nicht zu, dass Gastvorgänge auf der VM ausgeführt werden.   |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9027: Der Agent für Gastvorgänge wird nicht in der VM ausgeführt.   |   Fehler beim Kontaktieren des Agents für Gastvorgänge, der in der VM ausgeführt wird.    |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9028: Die Datei kann aufgrund von unzureichendem Speicherplatz in der VM nicht erstellt werden.     |   Nicht genügend Speicherplatz auf dem Datenträger.   |   Stellen Sie sicher, dass genügend Speicherplatz im Datenträgerspeicher der VM zur Verfügung steht.
9029: Kein Zugriff auf PowerShell mit den angegebenen Anmeldeinformationen für die Gast-VM.   |   Der Benutzer kann nicht auf PowerShell zugreifen.     |   Stellen Sie sicher, dass der in der Appliance hinzugefügte Benutzer auf PowerShell in der Gast-VM zugreifen kann.
9030: Die ermittelten Metadaten konnten nicht abgerufen werden, weil der ESXi-Host getrennt ist.     |   Der ESXi-Host befindet sich in einem nicht verbundenen Zustand.   |   Stellen Sie sicher, dass der ESXi-Host, der die VM ausführt, verbunden ist.
9031: Die ermittelten Metadaten konnten nicht abgerufen werden, weil der ESXi-Host nicht antwortet.   |   Der Remotehost befindet sich in einem ungültigen Zustand.    |   Stellen Sie sicher, dass der ESXi-Host, der die VM ausführt, ausgeführt wird und verbunden ist.
9032: Aufgrund eines internen Fehlers kann keine Ermittlung durchgeführt werden.   |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
9033: Es kann keine Ermittlung durchgeführt werden, weil der VM-Benutzername ungültige Zeichen enthält.     |   Im Benutzernamen wurden ungültige Zeichen gefunden.   |   Geben Sie die VM-Anmeldeinformationen erneut an, und stellen Sie hierbei sicher, dass keine ungültigen Zeichen vorhanden sind.
9034: Der angegebene Benutzername weist nicht das UPN-Format auf.    |   Der Benutzername weist nicht das UPN-Format auf.  |   Stellen Sie sicher, dass der Benutzername im UPN-Format (User Pincipal Name) vorliegt.
9035: Es kann keine Ermittlung durchgeführt werden, weil der PowerShell-Sprachmodus nicht auf „FullLanguage“ festgelegt ist.  |   Der Sprachmodus für PowerShell in der Gast-VM ist nicht auf „FullLanguage“ festgelegt.   |   Stellen Sie sicher, dass der PowerShell-Sprachmodus auf „FullLanguage“ festgelegt ist.
9037: Die Datensammlung wurde vorübergehend angehalten, weil die VM-Antwortzeit zu hoch ist.    |   Die ermittelte VM braucht zu lange, um zu antworten.     |   Keine weiteren Maßnahmen erforderlich. Ein Wiederholungsversuch wird in 24 Stunden für die Anwendungsermittlung und in 3 Stunden für die Abhängigkeitsanalyse (ohne Agent) ausgeführt.
10000: Der Betriebssystemtyp wird nicht unterstützt.   |   Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux.    |   Als Betriebssystemtypen werden nur Windows und Linux unterstützt.
10001: Das Skript für die Serverermittlung wurde nicht in der Appliance gefunden.    |   Die Ermittlung funktioniert nicht wie erwartet.   |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
10002: Die Ermittlungsaufgabe wurde nicht rechtzeitig abgeschlossen.     |   Der Ermittlungs-Agent funktioniert nicht wie erwartet.     |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.
10003: Der Prozess zur Ausführung der Ermittlungsaufgabe wurde mit einem Fehler beendet.    |   Der Prozess zur Ausführung der Ermittlungsaufgabe wurde mit einem Fehler beendet.  |   Das Problem sollte sich innerhalb von 24 Stunden automatisch lösen. Wenden Sie sich an den Microsoft-Support, wenn das Problem weiterhin besteht.
10004: Für den Gastbetriebssystemtyp wurden keine Anmeldeinformationen angegeben.  |   In der Azure Migrate-Appliance wurden keine Anmeldeinformationen für den Zugriff auf Computer mit diesem Betriebssystemtyp angegeben.    |   Fügen Sie in der Appliance Anmeldeinformationen für Computer hinzu.
10005: Die angegebenen Anmeldeinformationen sind nicht zulässig.   |   Die in der Appliance für den Serverzugriff angegebenen Anmeldeinformationen sind falsch.  |   Aktualisieren Sie die in der Appliance angegebenen Anmeldeinformationen, und stellen Sie sicher, dass der Zugriff auf den Server mit den Anmeldeinformationen möglich ist.
10006: Der Gastbetriebssystemtyp wird vom Anmeldeinformationsspeicher nicht unterstützt.  |   Das auf dem Server ausgeführte Betriebssystem ist weder Windows noch Linux.    |   Als Betriebssystemtypen werden nur Windows und Linux unterstützt.
10007: Die ermittelten Metadaten können nicht verarbeitet werden.    |   Fehler beim Versuch, die JSON zu deserialisieren.    |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
10008: Es kann keine Datei auf dem Server erstellt werden.    |  Das Problem kann aufgrund eines internen Fehlers auftreten.    |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.
10009: Die ermittelten Metadaten können nicht in eine Datei auf dem Server geschrieben werden.  |   Das Problem kann aufgrund eines internen Fehlers auftreten.   |   Wenden Sie sich an den Microsoft-Support, um eine Lösung zu erhalten.




## <a name="next-steps"></a>Nächste Schritte
Richten Sie eine Appliance für [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) oder [physische Server](how-to-set-up-appliance-physical.md) ein.