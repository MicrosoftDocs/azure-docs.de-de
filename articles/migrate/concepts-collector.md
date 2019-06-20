---
title: Informationen zur Collectorappliance in Azure Migrate | Microsoft-Dokumentation
description: Informationen zur Collectorappliance in Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 865e0679ed05823d115baeb9eea3c01d7fb5f2a5
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428467"
---
# <a name="about-the-collector-appliance"></a>Informationen zur Collectorappliance

 Dieser Artikel enthält Informationen zum Azure Migrate-Collector.

Der Azure Migrate-Collector ist eine einfache Appliance, die zum Ermitteln einer lokalen vCenter-Umgebung verwendet werden kann, damit vor der Migration zu Azure eine Überprüfung mit dem [Azure Migrate](migrate-overview.md)-Dienst durchgeführt werden kann.  

## <a name="discovery-method"></a>Ermittlungsmethode

Zuvor gab es zwei Optionen für die Collectorappliance: die einmalige Ermittlung und die kontinuierliche Ermittlung. Das Modell zur einmaligen Ermittlung ist inzwischen veraltet, da es bei der Sammlung von Leistungsdaten von den Statistikeinstellungen von vCenter Server abhängig war (Statistikeinstellungen mussten auf Stufe 3 festgelegt werden) und außerdem durchschnittliche Leistungsindikatoren erfasst hat (anstatt Spitzenwerte), was zu einer Unterdimensionierung führte. Das Modell zur kontinuierlichen Ermittlung gewährleistet eine granulare Datensammlung und führt zu einer exakten Größenanpassung aufgrund der Sammlung von Spitzenwertindikatoren. So funktioniert es:

Die Collectorappliance ist kontinuierlich mit dem Azure Migrate-Projekt verbunden und sammelt fortwährend Leistungsdaten von VMs.

- Der Collector profiliert kontinuierlich die lokale Umgebung, um alle 20 Sekunden Echtzeit-Verwendungsdaten zu sammeln.
- Die Appliance führt für die 20-Sekunden-Stichproben ein Rollup aus und erstellt alle 15 Minuten einen einzelnen Datenpunkt.
- Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus den 20-Sekunden-Stichproben aus und sendet diesen an Azure.
- Dieses Modell ist für die Sammlung von Leistungsdaten nicht von den Statistikeinstellungen von vCenter Server abhängig.
- Sie können die kontinuierliche Profilerstellung jederzeit über den Collector beenden.

**Schnellbewertungen:** Mit der Appliance für die kontinuierliche Ermittlung können Sie nach Abschluss der Ermittlung (dauert je nach Anzahl von virtuellen Computern einige Stunden) sofort Bewertungen erstellen. Da die Erfassung von Leistungsdaten bereits beim Start der Ermittlung beginnt, empfiehlt es sich, das Größenkriterium in der Bewertung auf *Wie lokal* festzulegen, um Schnellbewertungen zu erhalten. Bei leistungsbasierten Bewertungen empfiehlt es sich, nach dem Start der Ermittlung mindestens einen Tag zu warten, um zuverlässige Größenempfehlungen zu erhalten.

Die Appliance erfasst nur Leistungsdaten kontinuierlich. Sie erkennt keine Konfigurationsänderungen in der lokalen Umgebung (etwa hinzugefügte VMs, Löschvorgänge, hinzugefügte Datenträger usw.). Wenn sich die Konfiguration in der lokalen Umgebung ändert, können Sie wie folgt vorgehen, damit die Änderungen im Portal berücksichtigt werden:

- Hinzugefügte Elemente (virtuelle Computer, Datenträger, Kerne usw.): Damit diese Änderungen im Azure-Portal berücksichtigt werden, können Sie die Ermittlung über die Appliance beenden und anschließend wieder starten. Dadurch wird sichergestellt, dass die Änderungen im Azure Migrate-Projekt aktualisiert werden.

- Gelöschte virtuelle Computer: Das Löschen von virtuellen Computern wird aufgrund des Entwurfs der Appliance auch dann nicht berücksichtigt, wenn Sie die Ermittlung beenden und wieder starten. Das liegt daran, dass Daten nachfolgender Ermittlungen älteren Ermittlungen angefügt und nicht überschrieben werden. In diesem Fall können Sie die VM im Portal einfach ignorieren, indem Sie sie aus Ihrer Gruppe entfernen und die Bewertung neu berechnen.

> [!NOTE]
> Die Appliance zur einmaligen Ermittlung ist inzwischen veraltet, da diese Methode bei der Verfügbarkeit der Leistungsdatenpunkte von den Statistikeinstellungen von vCenter Server abhängig war und durchschnittliche Leistungsindikatoren erfasst hat, was zu unterdimensionierten virtuellen Computern für die Migration zu Azure führte.

## <a name="deploying-the-collector"></a>Bereitstellen des Collectors

Sie können die Collectorappliance mithilfe einer OVF-Vorlage bereitstellen:

- Laden Sie die OVF-Vorlage aus einem Azure Migrate-Projekt im Azure-Portal herunter. Importieren Sie die heruntergeladene Datei in vCenter Server, um die VM der Collectorappliance einzurichten.
- VMware richtet über die OVF-Vorlage eine VM mit 8 Kernen, 16 GB RAM und einem Datenträger mit 80 GB ein. Das Betriebssystem ist Windows Server 2016 (64-Bit).
- Bei der Ausführung des Collectors werden einige Voraussetzungsprüfungen ausgeführt, um sicherzustellen, dass der Collector eine Verbindung mit Azure Migrate herstellen kann.

- [Erfahren Sie mehr](tutorial-assessment-vmware.md#create-the-collector-vm) über das Erstellen des Collectors.


## <a name="collector-prerequisites"></a>Voraussetzungen für den Collector

Der Collector muss einige Voraussetzungsprüfungen bestehen, um sicherzustellen, dass er über das Internet eine Verbindung mit dem Azure Migrate-Dienst herstellen und ermittelte Daten hochladen kann.

- **Azure-Cloud überprüfen**: Der Collector muss die Azure-Cloud kennen, die Sie migrieren möchten.
    - Wählen Sie Azure Government aus, wenn Sie planen, zu Azure Government-Cloud zu migrieren.
    - Wählen Sie Azure Global aus, wenn Sie planen, zur kommerziellen Azure-Cloud zu migrieren.
    - Basierend auf der hier angegebenen Cloud sendet die Appliance ermittelte Metadaten an die jeweiligen Endpunkte.
- **Internetverbindung prüfen**: Der Collector kann eine direkte Verbindung mit dem Internet herstellen oder über einen Proxy.
    - Bei der Voraussetzungsprüfung wird die Konnektivität mit [erforderlichen und optionalen URLs](#urls-for-connectivity) überprüft.
    - Wenn Sie eine direkte Verbindung mit dem Internet haben, müssen Sie lediglich sicherstellen, dass der Collector die erforderlichen URLs erreichen kann.
    - Wenn Sie eine Verbindung über einen Proxy herstellen, sollten Sie die nachfolgenden Anforderungen beachten.
- **Überprüfen der Zeitsynchronisierung:** Der Collector muss mit dem Internetzeitserver synchronisiert sein, um sicherzustellen, dass die Anforderungen an den Dienst authentifiziert werden.
    - Die URL „portal.azure.com“ muss über den Collector erreichbar sein, damit die Uhrzeit überprüft werden kann.
    - Wenn der Computer nicht synchronisiert wird, müssen Sie die Uhrzeit auf der Collector-VM an die aktuelle Uhrzeit anpassen. Öffnen Sie hierzu auf der VM eine Eingabeaufforderung für Administratoren, und führen Sie **w32tm /tz** zum Überprüfen der Zeitzone aus. Führen Sie **w32tm /resync** zum Synchronisieren der Zeit aus.
- **Ausführung des Collectordiensts:**  Der Azure Migrate-Collectordienst muss auf dem virtuellen Collectorcomputer ausgeführt werden.
    - Dieser Dienst wird beim Hochfahren des Computers automatisch gestartet.
    - Starten Sie den Dienst über die Einstellungen, falls er nicht ausgeführt wird.
    - Der Collectordienst stellt eine Verbindung mit vCenter Server her, sammelt die VM-Metadaten und -Leistungsdaten und sendet diese an den Azure Migrate-Dienst.
- **Überprüfen der Installation von VMware PowerCLI 6.5:** Das PowerShell-Modul VMware PowerCLI 6.5 muss auf der Collector-VM installiert sein, damit eine Kommunikation mit vCenter Server möglich ist.
    - Wenn der Collector auf die URLs zugreifen kann, die für die Installation des Moduls erforderlich sind, erfolgt die Installation automatisch während der Bereitstellung des Collectors.
    - Wenn der Collector das Modul während der Bereitstellung nicht installieren kann, müssen Sie es manuell installieren.
- **Überprüfen der Verbindung mit vCenter Server:** Der Collector muss eine Verbindung mit vCenter Server herstellen und VMs, zugehörige Metadaten und Leistungsindikatoren abfragen können. [Überprüfen Sie die Voraussetzungen](#connect-to-vcenter-server) zum Herstellen einer Verbindung.


### <a name="connect-to-the-internet-via-a-proxy"></a>Herstellen einer Verbindung mit dem Internet über einen Proxy

- Wenn der Proxyserver eine Authentifizierung erfordert, können Sie den Benutzernamen und das Kennwort bei der Einrichtung des Collectors angeben.
- Die IP-Adresse bzw. der FQDN des Proxyservers sollte das Format *http:\//IP-Adresse* oder *http:\//FQDN* aufweisen.
- Es werden nur HTTP-Proxys unterstützt. HTTPS-basierte Proxyserver werden vom Collector nicht unterstützt.
- Wenn es sich bei dem Proxyserver um einen abfangenden Proxy handelt, müssen Sie das Proxyzertifikat auf der Collector-VM importieren.
  1. Rufen Sie auf der Collector-VM den Eintrag **Startmenü** > **Computerzertifikate verwalten** auf.
  2. Suchen Sie im Tool „Zertifikate“ unter **Zertifikate – Lokaler Computer** nach **Vertrauenswürdige Herausgeber** > **Zertifikate**.

      ![Tool „Zertifikate“](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Kopieren Sie das Proxyzertifikat auf die Collector-VM. Möglicherweise müssen Sie das Zertifikat über Ihren Netzwerkadministrator abrufen.
  4. Doppelklicken Sie auf das Zertifikat, um es zu öffnen, und klicken Sie anschließend auf **Zertifikat installieren**.
  5. Wählen Sie im Zertifikatimport-Assistenten die Option **Lokaler Computer** als Speicherort aus.

     ![Zertifikatspeicherort](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** > **Durchsuchen** > **Vertrauenswürdige Herausgeber** aus. Klicken Sie auf **Fertig stellen**, um das Zertifikat zu importieren.

     ![Zertifikatspeicher](./media/concepts-intercepting-proxy/certificate-store.png)

  7. Überprüfen Sie, ob das Zertifikat wie erwartet importiert wurde und ob die Voraussetzungsprüfung für die Internetkonnektivität wie erwartet funktioniert.


### <a name="urls-for-connectivity"></a>URLs für Konnektivität

Die Konnektivitätsprüfung wird durch das Herstellen einer Verbindung mit einer Liste von URLs durchgeführt.

**URL** | **Details**  | **Voraussetzungsprüfung**
--- | --- | ---
*.portal.azure.com | Gilt für Azure Global. Überprüft die Konnektivität mit dem Azure-Dienst und die Zeitsynchronisierung. | Zugriff auf URLs ist erforderlich.<br/><br/> Die Voraussetzungsprüfung schlägt fehl, wenn keine Konnektivität besteht.
*.portal.azure.us | Gilt nur für Azure Government. Überprüft die Konnektivität mit dem Azure-Dienst und die Zeitsynchronisierung. | Zugriff auf URLs ist erforderlich.<br/><br/> Die Voraussetzungsprüfung schlägt fehl, wenn keine Konnektivität besteht.
*.oneget.org:443<br/><br/> *.github.com/oneget/oneget<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.azure.microsoft.com<br/><br/> *.azure.microsoft.com/en-us<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443<br/><br/> *.visualstudio.microsoft.com | Wird zum Herunterladen des auf PowerShell basierenden vCenter PowerCLI-Moduls verwendet. | Zugriff auf URLs ist erforderlich.<br/><br/> Die Voraussetzungsprüfung schlägt nicht fehl.<br/><br/> Die automatische Installation des Moduls auf der Collector-VM schlägt fehl. Sie müssen das Modul manuell auf einem Computer installieren, der über eine Internetverbindung verfügt, und es dann auf die Appliance kopieren. [Mehr dazu erfahren Sie in Schritt 4 in diesem Leitfaden zur Problembehandlung](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Manuelles Installieren des VMware PowerCLI-Moduls

1. Installieren Sie das Modul mit den [folgenden Schritten](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). In diesen Schritten wird sowohl die Online- als auch die Offline-Installation beschrieben.
2. Wenn die Collector-VM offline ist und das Modul auf einem anderen Computer mit Internetzugriff installiert wird, müssen Sie die VMware.*-Dateien von diesem Computer auf die Collector-VM kopieren.
3. Nach der Installation können Sie die Voraussetzungsprüfungen neu starten, um sicherzustellen, dass PowerCLI installiert wurde.

### <a name="connect-to-vcenter-server"></a>Herstellen einer Verbindung mit vCenter Server

Der Collector stellt eine Verbindung mit vCenter Server her und fragt VM-Metadaten und Leistungsindikatoren ab. Für das Herstellen der Verbindung benötigen Sie Folgendes:

- Nur die vCenter Server-Versionen 5.5, 6.0, 6.5 und 6.7 werden unterstützt.
- Für die Ermittlung benötigen Sie ein schreibgeschütztes Konto mit den nachfolgend zusammengefassten Berechtigungen. Für die Ermittlung sind nur die Rechenzentren zugänglich, auf die mit dem Konto zugegriffen werden kann.
- Sie stellen standardmäßig über einen FQDN oder eine IP-Adresse eine Verbindung mit vCenter Server her. Wenn vCenter Server auf einem anderen Port lauscht, stellen Sie mithilfe des Formulars *IPAddress:Port_Number* oder *FQDN:Port_Number* eine Verbindung her.
- Der Collector benötigt eine „Sichtverbindung“ mit dem vCenter-Server über das Netzwerk.

#### <a name="account-permissions"></a>Kontoberechtigungen

**Konto** | **Berechtigungen**
--- | ---
Mindestens ein Benutzerkonto mit Lesezugriff | Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt)   


## <a name="collector-communications"></a>Kommunikation des Collectors

In dem folgenden Diagramm und der folgenden Tabelle finden Sie eine Übersicht über die Kommunikation des Collectors.

![Abbildung zur Kommunikation zwischen Collectors](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Collector kommuniziert mit** | **Port** | **Details**
--- | --- | ---
Azure Migrate-Dienst | TCP 443 | Der Collector kommuniziert über SSL-Port 443 mit dem Azure Migrate-Dienst.
vCenter Server | TCP 443 | Der Collector muss mit vCenter Server kommunizieren können.<br/><br/> Standardmäßig stellt er über Port 443 eine Verbindung mit vCenter her.<br/><br/> Wenn vCenter Server an einem anderen Port lauscht, sollte dieser Port auf dem Collector als ausgehender Port verfügbar sein.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Gesammelte Metadaten

> [!NOTE]
> Die von der Azure Migrate-Collectorappliance entdeckten Metadaten werden verwendet, um Ihnen zu helfen, Ihre Anwendungen bei der Migration nach Azure richtig zu skalieren. Außerdem sollen sie bei der Durchführung von Azure-Eignungsanalysen, Analysen der Anwendungsabhängigkeit und bei der Kostenplanung helfen. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Die Collectorappliance ermittelt folgende Konfigurationsmetadaten für jede VM. Die Konfigurationsdaten für die VMs sind eine Stunde nach Beginn der Ermittlung verfügbar.

- Anzeigename der VM (in vCenter Server)
- Inventarpfad der VM (der Host/Ordner in vCenter Server)
- IP-Adresse
- MAC-Adresse
- Betriebssystem
- Anzahl der Kerne, Datenträger, NICs
- Arbeitsspeichergröße, Datenträgergrößen
- Leistungsindikatoren der VM, Datenträger und Netzwerk.

### <a name="performance-counters"></a>Leistungsindikatoren

 Die Collectorappliance sammelt die folgenden Leistungsindikatoren für jede VM vom ESXi-Host in einem Intervall von 20 Sekunden. Diese Leistungsindikatoren sind vCenter-Leistungsindikatoren und obwohl die Terminologie von Durchschnitt spricht, handelt es sich bei den 20-Sekunden-Stichproben um Echtzeit-Leistungsindikatoren. Die Leistungsdaten für die virtuellen Computer stehen im Portal zwei Stunden nach dem Starten der Ermittlung zur Verfügung. Bei leistungsbasierten Bewertungen wird dringend empfohlen, mindestens einen Tag mit deren Erstellung zu warten, um korrekte Größenempfehlungen zu erhalten. Wenn Sie sofortige Ergebnisse wünschen, können Sie Bewertungen erstellen, bei denen das Größenkriterium auf *Wie lokal* festgelegt ist, wodurch die Leistungsdaten für die korrekte Größenanpassung nicht berücksichtigt werden.

**Leistungsindikator** |  **Auswirkung auf die Bewertung**
--- | ---
cpu.usage.average | Empfohlene VM-Größe und -Kosten  
mem.usage.average | Empfohlene VM-Größe und -Kosten  
virtualDisk.read.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.write.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.numberReadAveraged.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
virtualDisk.numberWriteAveraged.average | Berechnet die Datenträgergröße, Speicherkosten und die VM-Größe
net.received.average | Berechnet die VM-Größe                          
net.transmitted.average | Berechnet die VM-Größe     

Die vollständige Liste der von Azure Migrate erfassten VMware-Zähler wird unten angezeigt:

**Kategorie** |  **Metadaten** | **vCenter-Datenpunkt**
--- | --- | ---
Computerdetails | VM-ID | vm.Config.InstanceUuid
Computerdetails | Name des virtuellen Computers | vm.Config.Name
Computerdetails | vCenter Server-ID | VMwareClient.InstanceUuid
Computerdetails |  VM-Beschreibung |  vm.Summary.Config.Annotation
Computerdetails | Lizenzproduktname | vm.Client.ServiceContent.About.LicenseProductName
Computerdetails | Betriebssystemtyp | vm.Summary.Config.GuestFullName
Computerdetails | Betriebssystemversion | vm.Summary.Config.GuestFullName
Computerdetails | Starttyp | vm.Config.Firmware
Computerdetails | Anzahl von Kernen | vm.Config.Hardware.NumCPU
Computerdetails | Megabytes Arbeitsspeicher | vm.Config.Hardware.MemoryMB
Computerdetails | Anzahl der Datenträger | vm.Config.Hardware.Device.ToList().FindAll(x => x entspricht VirtualDisk).count
Computerdetails | Liste der Datenträgergrößen | vm.Config.Hardware.Device.ToList().FindAll(x => x entspricht VirtualDisk)
Computerdetails | Liste der Netzwerkadapter | vm.Config.Hardware.Device.ToList().FindAll(x => x entspricht VirtualEthernetCard)
Computerdetails | CPU-Auslastung | cpu.usage.average
Computerdetails | Arbeitsspeichernutzung | mem.usage.average
Datenträgerdetails (pro Datenträger) | Datenträgerschlüsselwert | disk.Key
Datenträgerdetails (pro Datenträger) | Anzahl der Datenträgereinheiten | disk.UnitNumber
Datenträgerdetails (pro Datenträger) | Datenträgercontroller-Schlüsselwert | disk.ControllerKey.Value
Datenträgerdetails (pro Datenträger) | Bereitgestellte Gigabytes | virtualDisk.DeviceInfo.Summary
Datenträgerdetails (pro Datenträger) | Name des Datenträgers | Dieser Wert wird mithilfe von disk.UnitNumber, disk.Key und disk.ControllerKey.Value generiert.
Datenträgerdetails (pro Datenträger) | Anzahl der Lesevorgänge pro Sekunde | virtualDisk.numberReadAveraged.average
Datenträgerdetails (pro Datenträger) | Anzahl der Schreibvorgänge pro Sekunde | virtualDisk.numberWriteAveraged.average
Datenträgerdetails (pro Datenträger) | Lesedurchsatz in Megabyte pro Sekunde | virtualDisk.read.average
Datenträgerdetails (pro Datenträger) | Schreibdurchsatz in Megabyte pro Sekunde | virtualDisk.write.average
Netzwerkadapterdetails (pro NIC) | Netzwerkadaptername | nic.Key
Netzwerkadapterdetails (pro NIC) | MAC-Adresse | ((VirtualEthernetCard)nic).MacAddress
Netzwerkadapterdetails (pro NIC) | IPv4-Adressen | vm.Guest.Net
Netzwerkadapterdetails (pro NIC) | IPv6-Adressen | vm.Guest.Net
Netzwerkadapterdetails (pro NIC) | Lesedurchsatz in Megabyte pro Sekunde | net.received.average
Netzwerkadapterdetails (pro NIC) | Schreibdurchsatz in Megabyte pro Sekunde | net.transmitted.average
Inventurpfaddetails | NAME | container.GetType().Name
Inventurpfaddetails | Typ des untergeordneten Objekts | container.ChildType
Inventurpfaddetails | Referenzdetails | container.MoRef
Inventurpfaddetails | Vollständiger Inventurpfad | container.Name mit vollständigem Pfad
Inventurpfaddetails | Details des übergeordneten Objekts | Container.Parent
Inventurpfaddetails | Ordnerdetails für einzelne VMs | ((Folder)container).ChildEntity.Type
Inventurpfaddetails | Datacenterdetails für einzelne VM-Ordner | ((Datacenter)container).VmFolder
Inventurpfaddetails | Datacenterdetails für einzelne Hostordner | ((Datacenter)container).HostFolder
Inventurpfaddetails | Clusterdetails für einzelne Hosts | ((ClusterComputeResource)container).Host)
Inventurpfaddetails | Hostdetails für einzelne VMs | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Sichern der Collectorappliance

Es wird empfohlen, die folgenden Schritte zum Sichern der Collectorappliance auszuführen:

- Teilen Sie Administratorkennwörter nicht unbefugten Personen mit, und verlieren Sie sie nicht.
- Fahren Sie die Appliance herunter, wenn Sie sie nicht verwenden.
- Ordnen Sie die Appliance in einem geschützten Netzwerk an.
- Löschen Sie nach Abschluss der Migration die Appliance-Instanz.
- Darüber hinaus sollten Sie die Sicherungsdateien der Datenträger (VMDKs) löschen, da darauf möglicherweise vCenter-Anmeldeinformationen zwischengespeichert werden.

## <a name="os-license-in-the-collector-vm"></a>Betriebssystemlizenz auf der Collector-VM

Der Collector ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist. Wenn der Evaluierungszeitraum für Ihre Collector-VM abläuft, wird empfohlen, eine neue OVA-Datei herunterzuladen und eine neue Appliance zu erstellen.

## <a name="updating-the-os-of-the-collector-vm"></a>Aktualisieren des Betriebssystems der Collector-VM

Obwohl die Collectorappliance über eine 180 Tage lang gültige Evaluierungslizenz verfügt, müssen Sie das Betriebssystem auf der Appliance in regelmäßigen Zeitabständen aktualisieren, um zu verhindern, dass die Appliance automatisch heruntergefahren wird.

- Wird der Collector 60 Tage lang nicht aktualisiert, beginnt er, den Computer automatisch herunterzufahren.
- Während einer Ermittlung wird der Computer nicht ausgeschaltet, auch nicht nach 60 Tagen. Nach Abschluss der Ermittlung wird der Computer ausgeschaltet.
- Wenn Sie den Collector länger als 60 Tage verwenden, wird empfohlen, den Computer durch die Ausführung von Windows-Updates stets auf dem neuesten Stand zu halten.

## <a name="upgrading-the-collector-appliance-version"></a>Durchführen eines Upgrades für die Version der Collectorappliance

Sie können ein Upgrade für den Collector auf die neueste Version durchführen, ohne die OVA-Datei erneut herunterladen zu müssen.

1. Laden Sie das [aktuelle Upgradepaket](concepts-collector-upgrade.md) herunter
2. Um sicherzustellen, dass der heruntergeladene Hotfix sicher ist, öffnen Sie das Befehlsfenster als Administrator, und führen Sie den folgenden Befehl zum Generieren des Hash für die ZIP-Datei aus. Der generierte Hash sollte mit dem für die betreffende Version genannten Hash übereinstimmen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (Beispiel: C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Kopieren Sie die ZIP-Datei auf den virtuellen Azure Migrate-Collectorcomputer (Collectorappliance).
4. Klicken Sie mit der rechten Maustaste auf die ZIP-Datei, und wählen Sie „Alle extrahieren“ aus.
5. Klicken Sie mit der rechten Maustaste auf „Setup.ps1“, und klicken Sie auf „Mit PowerShell ausführen“. Befolgen Sie dann die Anweisungen auf dem Bildschirm, um das Update zu installieren.

## <a name="discovery-process"></a>Ermittlungsprozess

Nach der Einrichtung der Appliance können Sie die Ermittlung durchführen. So funktioniert es:

- Sie führen eine Ermittlung nach Bereich durch. Alle VMs im angegebenen vCenter-Inventarpfad werden ermittelt.
    - Sie legen jeweils einen Bereich fest.
    - Der Bereich kann bis zu 1.500 VMs umfassen.
    - Der Bereich kann entweder ein Rechenzentrum, ein Ordner oder ein ESXi-Host sein.
- Nach dem Herstellen einer Verbindung mit vCenter Server stellen Sie eine Verbindung her, indem Sie ein Migrationsprojekt für die Sammlung angeben.
- VMs werden ermittelt, und die zugehörigen Metadaten und Leistungsdaten werden an Azure gesendet. Diese Aktionen sind Teil eines Sammlungsauftrags.
    - Der Collectorappliance wird eine bestimmte Collector-ID zugeordnet, die ermittlungsübergreifend für einen bestimmten Computer beständig ist.
    - Einem aktuell ausgeführten Sammlungsauftrag wird eine bestimmte Sitzungs-ID zugeordnet. Die ID ändert sich bei jedem Sammlungsauftrag und kann zur Problembehandlung verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten einer Bewertung für lokale virtuelle VMware-Computer](tutorial-assessment-vmware.md)
