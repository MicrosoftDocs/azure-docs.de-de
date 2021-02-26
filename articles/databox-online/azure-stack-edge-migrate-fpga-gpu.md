---
title: Leitfaden für die Migration von einem physischen Azure Stack Edge Pro-FPGA- zu einem -GPU-Gerät
description: Dieser Leitfaden enthält eine Anleitung zum Migrieren von Workloads von einem Azure Stack Edge Pro-FPGA- zu einem Azure Stack Edge Pro-GPU-Gerät.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: ce063e27620c3b0ccec9336e7a45ebb2ee1ad10d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100888"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrieren von Workloads von einem Azure Stack Edge Pro-FPGA- zu einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel wird beschrieben, wie Sie Workloads und Daten von einem Azure Stack Edge Pro-FPGA-Gerät zu einem Azure Stack Edge Pro-GPU-Gerät migrieren. Im Rahmen der Beschreibung des Migrationsverfahrens können Sie sich einen Überblick über die Migration verschaffen, z. B. Vergleich der beiden Geräte, Migrationsaspekte, ausführliche Schritte sowie Überprüfung mit anschließender Bereinigung.

<!--Azure Stack Edge Pro FPGA devices will reach end-of-life in February 2024. If you are considering new deployments, we recommend that you explore Azure Stack Edge Pro GPU devices for your workloads.-->

## <a name="about-migration"></a>Informationen zur Migration

Die Migration ist der Prozess zum Verschieben von Workloads und Anwendungsdaten von einem Speicherort an einen anderen. Hierbei muss eine genaue Kopie der auf einem Speichergerät befindlichen aktuellen Daten einer Organisation auf einem anderen Speichergerät erstellt werden – vorzugsweise ohne Unterbrechung oder Deaktivierung aktiver Anwendungen – und dann die gesamte Eingabe/Ausgabe-Aktivität (E/A) auf das neue Gerät umgeleitet werden. 

Dieser Migrationsleitfaden enthält eine Schritt-für-Schritt-Beschreibung der Vorgehensweise zum Migrieren von Daten von einem Azure Stack Edge Pro-FPGA- zu einem Azure Stack Edge Pro-GPU-Gerät. Dieses Dokument ist für IT-Experten und Wissensarbeiter bestimmt, die für das Betreiben, Bereitstellen und Verwalten von Azure Stack Edge-Geräten im Rechenzentrum verantwortlich sind. 

In diesem Artikel wird das Azure Stack Edge Pro-FPGA-Gerät als *Quellgerät* und das Azure Stack Edge Pro-GPU-Gerät als *Zielgerät* bezeichnet. 

## <a name="comparison-summary"></a>Zusammenfassender Vergleich

Dieser Abschnitt enthält eine vergleichende Zusammenfassung der Funktionen des Azure Stack Edge Pro-GPU- und des Azure Stack Edge Pro-FPGA-Geräts. Die Hardware des Quell- und des Zielgeräts ist größtenteils identisch und unterscheidet sich nur hinsichtlich der Karte für die Hardwarebeschleunigung und die Speicherkapazität. 

|    Funktion  | Azure Stack Edge Pro-GPU (Zielgerät)  | Azure Stack Edge Pro-FPGA (Quellgerät)|
|----------------|-----------------------|------------------------|
| Hardware       | Hardwarebeschleunigung: 1 oder 2 Nvidia T4-GPUs <br> Die Spezifikationen für Compute-Einheit, Arbeitsspeicher, Netzwerkschnittstelle, Netzteil und Netzkabel sind mit FPGA identisch.  | Hardwarebeschleunigung: Intel Arria 10 FPGA <br> Die Spezifikationen für Compute-Einheit, Arbeitsspeicher, Netzwerkschnittstelle, Netzteil und Netzkabel sind mit GPU identisch.          |
| Verwendbarer Speicher | 4,19 TB <br> Nach dem Reservieren von Speicherplatz für Paritätsresilienz und interne Verwendung | 12,5 TB <br> Nach dem Reservieren von Speicherplatz für interne Verwendung |
| Sicherheit       | Zertifikate |                                                     |
| Arbeitsauslastungen      | IoT Edge-Workloads <br> VM-Workloads <br> Kubernetes-Workloads| IoT Edge-Workloads |
| Preise        | [Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Migrationsplan

Beachten Sie beim Erstellen Ihres Migrationsplans Folgendes:

- Entwickeln Sie einen Zeitplan für die Migration. 
- Beim Migrieren von Daten kann es zu Downtime kommen. Wir empfehlen Ihnen, die Migration während eines Wartungsfensters mit Downtime zu planen, da der Prozess zu Störungen führen kann. Während dieses Downtimezeitraums führen Sie die Einrichtung und Wiederherstellung von Konfigurationen durch (wie später in diesem Dokument beschrieben).
- Ermitteln Sie die Gesamtlänge des Downtimezeitraums, und geben Sie diese Information an alle Projektbeteiligten weiter.
- Ermitteln Sie die lokalen Daten, die vom Quellgerät migriert werden müssen. Stellen Sie als Vorsichtsmaßnahme sicher, dass alle Daten im vorhandenen Speicher über eine vor Kurzem erstellte Sicherung verfügen. 


## <a name="migration-considerations"></a>Migrationsüberlegungen 

Beachten Sie Folgendes, bevor Sie mit der Migration beginnen: 

- Ein Azure Stack Edge Pro-GPU-Gerät kann nicht für eine Azure Stack Edge Pro-FPGA-Ressource aktiviert werden. Eine neue Ressource sollte für das Azure Stack Edge Pro-GPU-Gerät erstellt werden. Dies ist unter [Neue Ressource erstellen](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) beschrieben.
- Die auf dem Quellgerät mit FPGA bereitgestellten Machine Learning-Modelle müssen für das Zielgerät mit GPU geändert werden. Wenden Sie sich an den Microsoft-Support, um Hilfe zu den Modellen zu erhalten. Die auf dem Quellgerät ohne FPGA (nur mit Verwendung der CPU) bereitgestellten benutzerdefinierten Modelle sollten auf dem Zielgerät (mit Verwendung der CPU) funktionieren, ohne dass vorherige Änderungen erforderlich sind.
- Für die auf dem Quellgerät bereitgestellten IoT Edge-Module müssen unter Umständen Änderungen vorgenommen werden, bevor diese auf dem Zielgerät erfolgreich bereitgestellt werden können. 
- Das Quellgerät unterstützt NFS 3.0- und 4.1-Protokolle. Das Zielgerät unterstützt nur das NFS 3.0-Protokoll.
- Das Quellgerät unterstützt SMB- und NFS-Protokolle. Das Zielgerät unterstützt Speicher über das REST-Protokoll, indem Speicherkonten zusätzlich zu SMB- und NFS-Protokollen für Freigaben verwendet werden.
- Der Freigabezugriff auf dem Quellgerät erfolgt über die IP-Adresse, während für den Freigabezugriff auf dem Zielgerät der Gerätename verwendet wird.

## <a name="migration-steps-at-a-glance"></a>Übersicht über die Migrationsschritte

Diese Tabelle enthält eine Zusammenfassung des gesamten Ablaufs der Migration. Es werden die für die Migration erforderlichen Schritte und die entsprechenden Orte der Ausführung beschrieben.

| Phase | Schritt| Gerät |
|---------------|-------------|----------------|
| Vorbereiten des Quellgeräts*       | 1. Aufzeichnen der Konfigurationsdaten <br>2. Sichern der Freigabedaten <br>3. Vorbereiten der IoT Edge-Workloads| Quellgerät  |
| Vorbereiten des Zielgeräts*       |1. Erstellen einer neuen Bestellung <br>2. Konfigurieren und Aktivieren| Zielgerät  |
| Migrieren von Daten       | 1. Migrieren von Daten von Freigaben <br>2. Erneutes Bereitstellen von IoT Edge-Workloads| Zielgerät  |
| Überprüfen der Daten            |Überprüfen der migrierten Daten |Zielgerät  |
| Bereinigen/Zurückgeben              |Löschen von Daten und Zurückgeben| Quellgerät  |

**Das Quell- und das Zielgerät können parallel vorbereitet werden.*

## <a name="prepare-source-device"></a>Vorbereiten des Quellgeräts

Die Vorbereitung umfasst das Identifizieren der Edge-Cloudfreigaben, der lokalen Edge-Freigaben und der auf dem Gerät bereitgestellten IoT Edge-Module. 

### <a name="1-record-configuration-data"></a>1. Aufzeichnen der Konfigurationsdaten

Führen Sie diese Schritte auf Ihrem Quellgerät über die lokale Benutzeroberfläche aus.

Zeichnen Sie die Konfigurationsdaten auf dem *Quellgerät* auf. Verwenden Sie die [Prüfliste für die Bereitstellung](azure-stack-edge-gpu-deploy-checklist.md) als Hilfe beim Aufzeichnen der Gerätekonfiguration. Während der Migration nutzen Sie diese Konfigurationsinformationen, um das neue Zielgerät zu konfigurieren. 

### <a name="2-back-up-share-data"></a>2. Sichern der Freigabedaten

Bei den Gerätedaten kann es sich um einen der folgenden Typen handeln:

- Daten in Edge-Cloudfreigaben
- Daten in lokalen Freigaben

#### <a name="data-in-edge-cloud-shares"></a>Daten in Edge-Cloudfreigaben

Edge-Cloudfreigaben lagern Daten von Ihrem Gerät in Azure aus. Führen Sie diese Schritte auf Ihrem *Quellgerät* über das Azure-Portal aus. 

- Erstellen Sie eine Liste mit allen Edge-Cloudfreigaben und Benutzern auf dem Quellgerät.
- Erstellen Sie eine Liste mit Ihren gesamten Bandbreitenzeitplänen. Sie erstellen diese Bandbreitenzeitpläne dann auf Ihrem Zielgerät neu.
- Konfigurieren Sie je nach verfügbarer Netzwerkbandbreite die Bandbreitenzeitpläne auf Ihrem Gerät, um die Datenauslagerung in der Cloud zu erhöhen. Hierdurch wird die Menge der lokalen Daten auf dem Gerät verringert.
- Stellen Sie sicher, dass für die Freigaben eine vollständige Auslagerung in die Cloud erzielt wird. Sie können dies überprüfen, indem Sie sich im Azure-Portal den Status der Freigabe ansehen.  

#### <a name="data-in-edge-local-shares"></a>Daten auf lokalen Edge-Freigaben

Die Daten auf lokalen Edge-Freigaben verbleiben auf dem Gerät. Führen Sie diese Schritte auf Ihrem *Quellgerät* über das Azure-Portal aus. 

- Erstellen Sie eine Liste mit den lokalen Edge-Freigaben, die auf dem Gerät vorhanden sind.
- Wenn es sich um eine einmalige Migration der Daten handelt, sollten Sie eine Kopie der Daten der lokalen Edge-Freigabe auf einem anderen lokalen Server erstellen. Sie können Kopiertools wie `robocopy` (SMB) oder `rsync` (NFS) nutzen, um die Daten zu kopieren. Es kann auch sein, dass Sie bereits über die Bereitstellung einer Drittanbieterlösung für den Schutz der Daten verfügen, mit denen die Daten auf Ihren lokalen Freigaben gesichert werden. Die folgenden Drittanbieterlösungen werden für die Nutzung mit Azure Stack Edge Pro-FPGA-Geräten unterstützt:

    | Drittanbietersoftware           | Verweis auf die Lösung                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Weitere Informationen erhalten Sie bei Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Weitere Informationen erhalten Sie bei Commvault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Weitere Informationen erhalten Sie bei Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Weitere Informationen erhalten Sie bei Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Vorbereiten der IoT Edge-Workloads

- Wenn Sie IoT Edge-Module bereitgestellt haben und die FPGA-Beschleunigung verwenden, müssen Sie die Module ggf. modifizieren, bevor sie auf dem GPU-Gerät ausgeführt werden. Befolgen Sie die Anleitung zum [Bearbeiten von IoT Edge-Modulen](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Vorbereiten des Zielgeräts

### <a name="1-create-new-order"></a>1. Erstellen einer neuen Bestellung

Sie müssen eine neue Bestellung (und eine neue Ressource) für Ihr *Zielgerät* erstellen. Das Zielgerät muss für die GPU-Ressource aktiviert werden (nicht für die FPGA-Ressource).

Um eine Bestellung durchzuführen, [erstellen Sie eine neue Azure Stack Edge-Ressource](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) im Azure-Portal.


### <a name="2-set-up-activate"></a>2. Einrichten/Aktivieren

Sie müssen das *Zielgerät* für die neue Ressource, die Sie weiter oben erstellt haben, einrichten und aktivieren. 

Führen Sie die folgenden Schritte aus, um das *Zielgerät* über das Azure-Portal zu konfigurieren:

1. Sammeln Sie die erforderlichen Informationen in der [Bereitstellungsprüfliste für Ihr Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-deploy-checklist.md). Sie können die Informationen verwenden, die Sie für die Konfiguration des Quellgeräts gespeichert haben. 
1. [Packen](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) Sie Ihr Gerät aus, [montieren](azure-stack-edge-gpu-deploy-install.md#rack-the-device) Sie es in einem Rack und [verkabeln](azure-stack-edge-gpu-deploy-install.md#cable-the-device) sie es. 
1. [Stellen Sie eine Verbindung mit der lokalen Benutzeroberfläche des Geräts her](azure-stack-edge-gpu-deploy-connect.md).
1. Konfigurieren Sie das Netzwerk mit einer anderen Gruppe von IP-Adressen (bei Verwendung von statischen IP-Adressen) als für Ihr altes Gerät. Informieren Sie sich über das [Konfigurieren der Netzwerkeinstellungen](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Weisen Sie den gleichen Gerätenamen wie beim alten Gerät zu, und geben Sie eine DNS-Domäne an. Weitere Informationen finden Sie unter [Tutorial: Konfigurieren der Geräteeinstellungen für Azure Stack Edge Pro-Geräte mit GPU](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurieren Sie Zertifikate auf dem neuen Gerät. Informieren Sie sich über das [Konfigurieren von Zertifikaten für Ihr Azure Stack Edge Pro-Gerät mit GPU](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Rufen Sie den Aktivierungsschlüssel im Azure-Portal ab, und aktivieren Sie das neue Gerät. Informieren Sie sich über das [Aktivieren des Geräts](azure-stack-edge-gpu-deploy-activate.md).

Sie können nun die Freigabedaten wiederherstellen und die Workloads bereitstellen, die Sie auf dem alten Gerät ausgeführt haben.

## <a name="migrate-data"></a>Migrieren von Daten

Als Nächstes kopieren Sie Daten vom Quellgerät auf die Edge-Cloudfreigaben und die lokalen Edge-Freigaben auf Ihrem *Zielgerät*.

### <a name="1-from-edge-cloud-shares"></a>1. Von Edge-Cloudfreigaben

Führen Sie die folgenden Schritte aus, um die Daten der Edge-Cloudfreigaben mit Ihrem Zielgerät zu synchronisieren:

1. [Fügen Sie Freigaben hinzu](azure-stack-edge-j-series-manage-shares.md#add-a-share), deren Namen den Namen der Freigaben auf dem Quellgerät entsprechen. Stellen Sie sicher, dass **Blobcontainer auswählen** beim Erstellen von Freigaben auf **Vorhandene verwenden** festgelegt ist, und wählen Sie dann den Container aus, der mit dem vorherigen Gerät verwendet wurde.
1. [Fügen Sie Benutzer hinzu](azure-stack-edge-j-series-manage-users.md#add-a-user), die Zugriff auf das vorherige Gerät hatten.
1. [Aktualisieren Sie die Freigabedaten](azure-stack-edge-j-series-manage-shares.md#refresh-shares) aus Azure. Dadurch werden alle Clouddaten aus dem vorhandenen Container in die Freigaben gepullt.
1. Erstellen Sie die Bandbreitenzeitpläne neu, die Ihren Freigaben zugeordnet werden sollen. Informationen zu den ausführlichen Schritten finden Sie unter [Hinzufügen eines Zeitplans](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule).


### <a name="2-from-edge-local-shares"></a>2. Von lokalen Edge-Freigaben

Unter Umständen haben Sie eine Sicherungslösung eines Drittanbieters bereitgestellt, um die Daten auf den lokalen Freigaben für Ihre IoT-Workloads zu schützen. Sie müssen diese Daten jetzt wiederherstellen.

Nach vollständiger Konfiguration des Ersatzgeräts aktivieren Sie das Gerät für die lokale Speicherung. 

Führen Sie diese Schritte aus, um die Daten aus lokalen Freigaben wiederherzustellen:

1. [Konfigurieren Sie die Computerolle auf einem Azure Stack Edge Pro-Gerät mit GPU](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Fügen Sie alle lokalen Freigaben auf dem Zielgerät hinzu. Weitere Informationen finden Sie in den ausführlichen Schritten unter [Hinzufügen einer lokalen Freigabe](azure-stack-edge-j-series-manage-shares.md#add-a-local-share).
1. Beim Zugreifen auf die SMB-Freigaben auf dem Quellgerät werden die IP-Adressen verwendet, während auf dem Zielgerät der Gerätename genutzt wird. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer SMB-Freigabe](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share). Zum Herstellen einer Verbindung mit NFS-Freigaben auf dem Zielgerät müssen Sie die neuen IP-Adressen verwenden, die dem Gerät zugeordnet sind. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer NFS-Freigabe](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share). 

    Wenn Sie Ihre Freigabedaten per SMB/NFS auf einen Zwischenserver kopiert haben, können Sie diese Daten auf Freigaben des Zielgeräts kopieren. Auch das direkte Kopieren vom Quellgerät ist möglich, sofern sowohl das Quell- als auch das Zielgerät *online* ist.

    Falls Sie eine Drittanbietersoftware zum Sichern der Daten auf den lokalen Freigaben verwendet haben, müssen Sie das Wiederherstellungsverfahren der gewählten Schutzlösung ausführen. Entsprechende Verweise finden Sie in der folgenden Tabelle.

    | Drittanbietersoftware           | Verweis auf die Lösung                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Weitere Informationen erhalten Sie bei Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Weitere Informationen erhalten Sie bei Commvault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Weitere Informationen erhalten Sie bei Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Weitere Informationen erhalten Sie bei Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Erneutes Bereitstellen von IoT Edge-Workloads

Nachdem die IoT Edge-Module vorbereitet wurden, müssen Sie IoT Edge-Workloads auf Ihrem Zielgerät bereitstellen. Falls beim Bereitstellen von IoT Edge-Modulen Fehler auftreten, helfen Ihnen die folgenden Informationen weiter:

- [Häufig auftretende Probleme und Lösungen für Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md) 
- [Fehler für IoT Edge-Runtime][Verwalten eines Azure Stack Edge Pro-GPU-Geräts mithilfe von Windows PowerShell](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors)

## <a name="verify-data"></a>Überprüfen der Daten

Überprüfen Sie nach der Migration, ob alle Daten migriert wurden und die Workloads auf dem Zielgerät bereitgestellt wurden.

## <a name="erase-data-return"></a>Löschen von Daten/Zurückgeben

Löschen Sie nach Abschluss der Datenmigration die lokalen Daten, und geben Sie das Quellgerät zurück. Führen Sie die Schritte unter [Zurückgeben Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-return-device.md) aus.


## <a name="next-steps"></a>Nächste Schritte

[Ausführen einer Computeworkload mit einem IoT Edge-Modul auf einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-deploy-compute-module-simple.md)
