---
title: Einrichten einer Azure Migrate-Appliance in Azure Government
description: Hier erfahren Sie, wie Sie eine Azure Migrate-Appliance in Azure Government einrichten.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: f31211c4448ad5bbe8d839b2ec86b0b61970f568
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752191"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Einrichten einer Appliance in Azure Government 

In diesem Artikel finden Sie Informationen zum Bereitstellen einer [Azure Migrate-Appliance](./migrate-appliance-architecture.md) für VMware-VMs, Hyper-V-VMs und physische Server in einer Azure Government-Cloud. Sie führen ein Skript aus, um die Appliance zu erstellen und zu überprüfen, ob sie eine Verbindung mit Azure herstellen kann. Wenn Sie eine Appliance in der öffentlichen Cloud einrichten möchten, lesen Sie [diesen Artikel](deploy-appliance-script.md).


> [!NOTE]
> Die Option zum Bereitstellen einer Appliance mithilfe einer Vorlage (für VMware-VMs und Hyper-V-VMs) wird in Azure Government nicht unterstützt.


## <a name="prerequisites"></a>Voraussetzungen

Das Skript richtet die Azure Migrate-Appliance auf einem vorhandenen physischen Computer oder virtuellen Computer ein.

- Auf dem Computer, der als Appliance fungieren soll, müssen Windows Server 2016 mit 32 GB Arbeitsspeicher, acht vCPUs, ca. 80 GB Festplattenspeicher und ein externer virtueller Switch ausgeführt werden. Er benötigt eine statische oder dynamische IP-Adresse und Internetzugriff.
- Überprüfen Sie vor der Bereitstellung der Appliance ihre detaillierten Anforderungen für [VMware-VMs](migrate-appliance.md#appliance---vmware), [Hyper-V-VMs](migrate-appliance.md#appliance---hyper-v) und [physische Server](migrate-appliance.md#appliance---physical).
- Führen Sie das Skript nicht auf einer vorhandenen Azure Migrate-Appliance aus.

## <a name="set-up-the-appliance-for-vmware"></a>Einrichten der Appliance für VMware

Um die Appliance für VMware einzurichten, laden Sie eine ZIP-Datei aus dem Azure-Portal herunter und extrahieren den Inhalt. Zum Starten der Appliance-Web-App führen Sie das PowerShell-Skript aus. Dann richten Sie die Appliance ein und führen die Erstkonfiguration aus. Anschließend registrieren Sie die Appliance beim Azure Migrate-Projekt.

### <a name="download-the-script"></a>Herunterladen des Skripts

1.  Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2.  Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere Hypervisor** aus.
3.  Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen. 


### <a name="verify-file-security"></a>Überprüfen der Dateisicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispiel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Algorithmus** | **Download** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Führen Sie das Skript aus.

Funktion des Skripts:

- Installation von Agents und einer Webanwendung.
- Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
- Download und Installation eines wiederbeschreibbaren IIS-Moduls. [Weitere Informationen](https://www.microsoft.com/download/details.aspx?id=7435)
- Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungen für Azure Migrate.
- Erstellen von Protokoll- und Konfigurationsdateien wie folgt:
    - **Konfigurationsdateien**: %ProgramData%\Microsoft Azure\Config
    - **Protokolldateien**: %ProgramData%\Microsoft Azure\Logs

So führen Sie das Skript aus

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Computer, der die Appliance hostet. Führen Sie das Skript nicht auf einem Computer auf einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem Computer mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner mit den Inhalten, die aus der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript **AzureMigrateInstaller.ps1** wie folgt aus: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nachdem das Skript erfolgreich ausgeführt wurde, wird die Appliancewebanwendung gestartet, damit Sie das Gerät einrichten können. Bei Problemen sehen Sie sich die Skriptprotokolle in diesem Pfad an: C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log.

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.


## <a name="set-up-the-appliance-for-hyper-v"></a>Einrichten der Appliance für Hyper-V

Um die Appliance für Hyper-V einzurichten, laden Sie eine ZIP-Datei aus dem Azure-Portal herunter und extrahieren den Inhalt. Zum Starten der Appliance-Web-App führen Sie das PowerShell-Skript aus. Dann richten Sie die Appliance ein und führen die Erstkonfiguration aus. Anschließend registrieren Sie die Appliance beim Azure Migrate-Projekt.

### <a name="download-the-script"></a>Herunterladen des Skripts

1.  Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2.  Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit Hyper-V**.
3.  Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen. 


### <a name="verify-file-security"></a>Überprüfen der Dateisicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispiel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Szenario** | **Download** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Führen Sie das Skript aus.

Funktion des Skripts:

- Installation von Agents und einer Webanwendung.
- Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
- Download und Installation eines wiederbeschreibbaren IIS-Moduls. [Weitere Informationen](https://www.microsoft.com/download/details.aspx?id=7435)
- Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungen für Azure Migrate.
- Erstellen von Protokoll- und Konfigurationsdateien wie folgt:
    - **Konfigurationsdateien**: %ProgramData%\Microsoft Azure\Config
    - **Protokolldateien**: %ProgramData%\Microsoft Azure\Logs

So führen Sie das Skript aus

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Computer, der die Appliance hostet. Führen Sie das Skript nicht auf einem Computer auf einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem Computer mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner mit den Inhalten, die aus der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript **AzureMigrateInstaller.ps1** wie folgt aus: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Nachdem das Skript erfolgreich ausgeführt wurde, wird die Appliancewebanwendung gestartet, damit Sie das Gerät einrichten können. Bei Problemen sehen Sie sich die Skriptprotokolle in diesem Pfad an: C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log.

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.


## <a name="set-up-the-appliance-for-physical-servers"></a>Einrichten der Appliance für physische Server

Um die Appliance für VMware einzurichten, laden Sie eine ZIP-Datei aus dem Azure-Portal herunter und extrahieren den Inhalt. Zum Starten der Appliance-Web-App führen Sie das PowerShell-Skript aus. Dann richten Sie die Appliance ein und führen die Erstkonfiguration aus. Anschließend registrieren Sie die Appliance beim Azure Migrate-Projekt.

### <a name="download-the-script"></a>Herunterladen des Skripts

1.  Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2.  Klicken Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** auf **Nicht virtualisiert/Andere**.
3.  Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen. 


### <a name="verify-file-security"></a>Überprüfen der Dateisicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispiel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Szenario** | **Herunterladen** _ | _ *Hashwert**
    --- | --- | ---
    Physisch (85 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Führen Sie das Skript aus.

Funktion des Skripts:

- Installation von Agents und einer Webanwendung.
- Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
- Download und Installation eines wiederbeschreibbaren IIS-Moduls. [Weitere Informationen](https://www.microsoft.com/download/details.aspx?id=7435)
- Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungen für Azure Migrate.
- Erstellen von Protokoll- und Konfigurationsdateien wie folgt:
    - **Konfigurationsdateien**: %ProgramData%\Microsoft Azure\Config
    - **Protokolldateien**: %ProgramData%\Microsoft Azure\Logs

So führen Sie das Skript aus

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Computer, der die Appliance hostet. Führen Sie das Skript nicht auf einem Computer auf einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem Computer mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner mit den Inhalten, die aus der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript **AzureMigrateInstaller.ps1** wie folgt aus: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Nachdem das Skript erfolgreich ausgeführt wurde, wird die Appliancewebanwendung gestartet, damit Sie das Gerät einrichten können. Bei Problemen sehen Sie sich die Skriptprotokolle in diesem Pfad an: C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log.

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Bereitstellen der Appliance müssen Sie die Erstkonfiguration ausführen und die Appliance beim Azure Migrate-Projekt registrieren.

- Richten Sie die Appliance für [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance) ein.
- Richten Sie die Appliance für [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) ein.
- Richten Sie die Appliance für [physische Server](how-to-set-up-appliance-physical.md) ein.
