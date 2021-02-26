---
title: Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“
description: In diesem Artikel wird das Ausführen eines SSIS-Pakets (SQL Server Integration Services) in einer Azure Data Factory-Pipeline mithilfe der Aktivität „SSIS-Paket ausführen“ beschrieben.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 40e7d5c11a2ebc62e59c3d5d20dfefe18a33f9a7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391608"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird das Ausführen eines SSIS-Pakets (SQL Server Integration Services) in einer Azure Data Factory-Pipeline mithilfe der Aktivität „SSIS-Paket ausführen“ beschrieben. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Erstellen Sie eine Azure-SSIS Integration Runtime-Instanz (IR), falls Sie noch keine besitzen. Befolgen Sie dazu die ausführliche Anleitung unter [Tutorial: Bereitstellen der Azure-SSIS Integration Runtime in Azure Data Factory](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Ausführen eines Pakets im Azure-Portal
In diesem Abschnitt erstellen Sie mithilfe der Data Factory-Benutzeroberfläche oder -App eine Data Factory-Pipeline mit einer Aktivität „SSIS-Paket ausführen“, die Ihr SSIS-Paket ausführt.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Erstellen einer Pipeline mit einer Aktivität „SSIS-Paket ausführen“
In diesem Schritt erstellen Sie über die Data Factory-Benutzeroberfläche oder -App eine Pipeline. Sie fügen eine Aktivität „SSIS-Paket ausführen“ zur Pipeline hinzu und konfigurieren sie für die Ausführung des SSIS-Pakets. 

1. Wählen Sie im Azure-Portal in der Übersicht oder auf der Startseite von Data Factory die Kachel **Erstellen und überwachen** aus, um die Data Factory-Benutzeroberfläche oder -App in einem separaten Tab zu öffnen. 

   ![Data Factory-Startseite](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Wählen Sie auf der Seite **Erste Schritte** die Option **Pipeline erstellen** aus. 

   ![Seite für die ersten Schritte](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein**. Ziehen Sie dann die Aktivität **SSIS-Paket ausführen** auf die Oberfläche des Pipeline-Designers. 

   ![Ziehen und Ablegen der Aktivität „SSIS-Paket ausführen“ auf der Oberfläche des Designers](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Wählen Sie das Aktivitätsobjekt „SSIS-Paket ausführen“ zum Konfigurieren der Registerkarten **Allgemein**, **Einstellungen**, **SSIS-Parameter**, **Verbindungs-Manager** und **Eigenschaftenüberschreibungen** aus.

#### <a name="general-tab"></a>Registerkarte "Allgemein"

Führen Sie auf der Registerkarte **Allgemein** der Aktivität „SSIS-Paket ausführen“ die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Allgemein“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Geben Sie unter **Name** den Namen Ihrer Aktivität „SSIS-Paket ausführen“ ein.

   1. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Aktivität „SSIS-Paket ausführen“ ein.

   1. Geben Sie unter **Zeitlimit** den maximalen Zeitraum ein, in dem Ihre Aktivität „SSIS-Paket ausführen“ ausgeführt werden kann. Der Standardwert ist „7 Tage“; das Format lautet „T.HH:MM:SS“.

   1. Geben Sie unter **Wiederholung** die maximale Anzahl von Wiederholungsversuchen für Ihre Aktivität „SSIS-Paket ausführen“ ein.

   1. Geben Sie unter **Wiederholungsintervall** die Anzahl von Sekunden zwischen den einzelnen Wiederholungsversuchen für Ihre Aktivität „SSIS-Paket ausführen“ ein. Der Standardwert ist „30 Sekunden“.

   1. Aktivieren Sie das Kontrollkästchen **Sichere Ausgabe**, um auszuwählen, dass Sie die Ausgabe Ihrer Aktivität „SSIS-Paket ausführen“ von der Protokollierung ausschließen möchten.

   1. Aktivieren Sie das Kontrollkästchen **Sichere Eingabe**, um auszuwählen, dass Sie die Eingabe Ihrer Aktivität „SSIS-Paket ausführen“ von der Protokollierung ausschließen möchten.

#### <a name="settings-tab"></a>Registerkarte "Einstellungen"

Führen Sie auf der Registerkarte **Einstellungen** der Aktivität „SSIS-Paket ausführen“ die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Automatisch](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. Wählen Sie unter **Azure-SSIS IR** die festgelegte Azure-SSIS Integration Runtime-Instanz zur Ausführung Ihrer Aktivität „SSIS-Paket ausführen“ aus.

   1. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Aktivität „SSIS-Paket ausführen“ ein.

   1. Aktivieren Sie das Kontrollkästchen **Windows-Authentifizierung**, um auszuwählen, dass Sie die Windows-Authentifizierung für den Zugriff auf Datenspeicher, z B. SQL-Server/lokale Dateifreigaben oder Azure Files, verwenden möchten.
   
      Wenn Sie dieses Kontrollkästchen aktivieren, geben Sie die Werte Ihrer Anmeldeinformationen für Paketausführung in den Feldern **Domäne**, **Benutzername** und **Kennwort** ein. Für den Zugriff beispielsweise auf Azure Files lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort `<storage account key>`.

      Alternativ können Sie in Ihrem Azure-Schlüsseltresor (Azure Key Vault) gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`.
      
   1. Aktivieren Sie das Kontrollkästchen **32-Bit-Runtime**, um auszuwählen, dass Ihr Paket die 32-Bit-Runtime zur Ausführung benötigt.

   1. Wählen Sie unter **Paketspeicherort** eine der Optionen **SSISDB**, **Dateisystem (Paket)** , **Dateisystem (Projekt)** , **Eingebettetes Paket** oder **Paketspeicher** aus. 

##### <a name="package-location-ssisdb"></a>Paketspeicherort: SSISDB

**SSIS** als Ihr Paketspeicherort ist automatisch ausgewählt, wenn Ihre Azure-SSIS Integration Runtime-Instanz mit einem SSIS-Katalog (SSISDB) bereitgestellt wurde, der von Azure SQL-Datenbank-Server/Verwalteter Instanz gehostet wird, oder Sie können diese Option selbst auswählen. Wenn sie ausgewählt ist, führen Sie die folgenden Schritte aus.

   1. Wenn Ihre Azure-SSIS Integration Runtime-Instanz ausgeführt wird und das Kontrollkästchen **Manuelle Einträge** deaktiviert ist, können Sie Ihre vorhandenen Ordner, Projekte, Pakete und Umgebungen aus SSISDB durchsuchen und auswählen. Wählen Sie **Aktualisieren** aus, um Ihre neu hinzugefügten Ordner, Projekte, Pakete oder Umgebungen aus SSISDB abzurufen, sodass sie zum Durchsuchen und Auswählen verfügbar sind. Wenn Sie die Umgebungen für Ihre Paketausführungen durchsuchen und auswählen möchten, müssen Sie Ihre Projekte zuvor so konfigurieren, dass diese Umgebungen als Verweise aus denselben Ordnern unter SSISDB hinzugefügt werden. Weitere Informationen finden Sie unter [Erstellen und Zuordnen einer Serverumgebung](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. Wählen Sie für **Protokolliergrad** einen vordefinierten Bereich für die Protokollierung Ihrer Paketausführung. Aktivieren Sie das Kontrollkästchen **Angepasst**, wenn Sie stattdessen einen angepassten Namen für die Protokollierung eingeben möchten. 

   1. Wenn Ihre Azure-SSIS Integration Runtime-Instanz nicht ausgeführt wird oder das Kontrollkästchen **Manuelle Einträge** aktiviert ist, geben Sie Ihre Paket- und Umgebungspfade aus SSISDB direkt in den folgenden Formaten ein: `<folder name>/<project name>/<package name>.dtsx` und `<folder name>/<environment name>`.

      ![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Paketspeicherort: Dateisystem (Paket)

**Dateisystem (Paket)** als Ihr Speicherort ist automatisch ausgewählt, wenn Ihre Azure-SSIS Integration Runtime-Instanz ohne SSISDB bereitgestellt wurde, oder Sie können diese Option selbst auswählen. Wenn sie ausgewählt ist, führen Sie die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Dateisystem (Paket)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Geben Sie das auszuführende Paket an, indem Sie im Feld **Paketpfad** einen UNC-Pfad (Universal Naming Convention) zu Ihrer Paketdatei (mit `.dtsx`) eingeben. Sie können Ihr Paket durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihr Paket also beispielsweise in Azure Files speichern, lautet der Pfad `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   1. Wenn Sie Ihr Paket in einer separaten Datei konfigurieren, müssen Sie außerdem im Feld **Konfigurationspfad** einen UNC-Pfad zu Ihrer Konfigurationsdatei (mit `.dtsConfig`) angeben. Sie können Ihre Konfiguration durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Konfiguration also beispielsweise in Azure Files speichern, lautet der Pfad `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihre Paket- und Konfigurationsdateien an. Wenn Sie die Werte Ihrer Anmeldeinformationen für Paketausführung (für die **Windows-Authentifizierung**) zuvor eingegeben haben, können Sie diese Werte wiederverwenden, indem Sie das Kontrollkästchen **Wie Anmeldeinformationen für Paketausführung** aktivieren. Geben Sie andernfalls die Werte Ihrer Anmeldeinformationen für den Paketzugriff in die Felder **Domäne**, **Benutzername** und **Kennwort** ein. Wenn Sie Ihr Projekt und Ihre Konfiguration also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. 

      Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`. 

      Diese Anmeldeinformationen werden auch für den Zugriff auf Ihre untergeordneten Pakete im Task „Paket ausführen“ verwendet, auf die durch deren eigenen Pfad und andere in Ihren Paketen angegebene Konfigurationen verwiesen wird. 

   1. Wenn Sie bei der Paketerstellung über SQL Server Data Tools (SSDT) die Schutzebene **EncryptAllWithPassword** oder **EncryptSensitiveWithPassword** verwendet haben, müssen Sie unter **Verschlüsselungskennwort** den Wert für Ihr Kennwort eingeben. Alternativ können Sie ein in Ihrem Azure-Schlüsseltresor gespeichertes Geheimnis als Wert verwenden (siehe oben).
      
      Wenn Sie die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben, geben Sie Ihre vertraulichen Werte in Konfigurationsdateien oder aber auf den Registerkarten **SSIS-Parameter**, **Verbindungs-Manager** oder **Eigenschaftenüberschreibungen** erneut ein (siehe unten).
      
      Die Verwendung der Schutzebene **EncryptAllWithUserKey** wird nicht unterstützt. Sie müssen Ihr Paket über SSDT oder das Befehlszeilen-Hilfsprogramm `dtutil` für die Verwendung einer anderen Schutzebene neu konfigurieren. 

   1. Wählen Sie für **Protokolliergrad** einen vordefinierten Bereich für die Protokollierung Ihrer Paketausführung. Aktivieren Sie das Kontrollkästchen **Angepasst**, wenn Sie stattdessen einen angepassten Namen für die Protokollierung eingeben möchten. 
   
   1. Wenn Sie für Ihre Paketausführungen eine Protokollierung verwenden möchten, die über die Standardprotokollanbieter hinausgeht, die in Ihrem Paket angegeben werden können, geben Sie im Feld **Protokollierungspfad** den UNC-Pfad Ihres Protokollordners an. Sie können Ihren Protokollordner durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lautet Ihr Protokollierungspfad `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. In diesem Pfad wird für jedes ausgeführte Einzelpaket ein Unterordner erstellt und mit der Aktivitätsausführungs-ID von „SSIS-Paket ausführen“ benannt. In diesem Unterordner werden alle fünf Minuten Protokolldateien generiert. 
   
   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihren Protokollordner an. Wenn Sie die Werte Ihrer Anmeldeinformationen für den Paketzugriff (siehe oben) zuvor eingegeben haben, können Sie diese Werte wiederverwenden, indem Sie das Kontrollkästchen **Wie Anmeldeinformationen für Paketzugriff** aktivieren. Geben Sie andernfalls die Werte Ihrer Anmeldeinformationen für den Protokollierungszugriff in die Felder **Domäne**, **Benutzername** und **Kennwort** ein. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden (siehe oben).
   
Für alle zuvor erwähnten UNC-Pfade muss der vollqualifizierte Dateiname weniger als 260 Zeichen umfassen. Der Verzeichnisname muss weniger als 248 Zeichen umfassen.

##### <a name="package-location-file-system-project"></a>Paketspeicherort: Dateisystem (Projekt)

Wenn Sie **Dateisystem (Projekt)** als Ihren Paketspeicherort auswählen, führen Sie die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Dateisystem (Projekt)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Geben Sie das auszuführende Paket an, indem Sie im Feld **Paketpfad** einen UNC-Pfad zu Ihrer Projektdatei (mit `.ispac`) und im Feld **Paketname** eine Paketdatei (mit `.dtsx`) aus Ihrem Projekt eingeben. Sie können Ihr Projekt durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihr Projekt also beispielsweise in Azure Files speichern, lautet der Pfad `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihre Projekt- und Paketdateien an. Wenn Sie die Werte Ihrer Anmeldeinformationen für Paketausführung (für die **Windows-Authentifizierung**) zuvor eingegeben haben, können Sie diese Werte wiederverwenden, indem Sie das Kontrollkästchen **Wie Anmeldeinformationen für Paketausführung** aktivieren. Geben Sie andernfalls die Werte Ihrer Anmeldeinformationen für den Paketzugriff in die Felder **Domäne**, **Benutzername** und **Kennwort** ein. Wenn Sie Ihr Projekt und Ihr Paket also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. 

      Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`. 

      Diese Anmeldeinformationen werden auch für den Zugriff auf Ihre untergeordneten Pakete im Task „Paket ausführen“ verwendet, auf die aus demselben Projekt verwiesen wird. 

   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptAllWithPassword** oder **EncryptSensitiveWithPassword** verwendet haben, müssen Sie im Feld **Verschlüsselungskennwort** den Wert für Ihr Kennwort eingeben. Alternativ können Sie ein in Ihrem Azure-Schlüsseltresor gespeichertes Geheimnis als Wert verwenden (siehe oben).
      
      Wenn Sie die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben, geben Sie Ihre vertraulichen Werte auf den Registerkarten **SSIS-Parameter**, **Verbindungs-Manager** oder **Eigenschaftenüberschreibungen** erneut ein (siehe unten).
      
      Die Verwendung der Schutzebene **EncryptAllWithUserKey** wird nicht unterstützt. Sie müssen Ihr Paket über SSDT oder das Befehlszeilen-Hilfsprogramm `dtutil` für die Verwendung einer anderen Schutzebene neu konfigurieren. 

   1. Wählen Sie für **Protokolliergrad** einen vordefinierten Bereich für die Protokollierung Ihrer Paketausführung. Aktivieren Sie das Kontrollkästchen **Angepasst**, wenn Sie stattdessen einen angepassten Namen für die Protokollierung eingeben möchten. 
   
   1. Wenn Sie für Ihre Paketausführungen eine Protokollierung verwenden möchten, die über die Standardprotokollanbieter hinausgeht, die in Ihrem Paket angegeben werden können, geben Sie im Feld **Protokollierungspfad** den UNC-Pfad Ihres Protokollordners an. Sie können Ihren Protokollordner durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lautet Ihr Protokollierungspfad `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. In diesem Pfad wird für jedes ausgeführte Einzelpaket ein Unterordner erstellt und mit der Aktivitätsausführungs-ID von „SSIS-Paket ausführen“ benannt. In diesem Unterordner werden alle fünf Minuten Protokolldateien generiert. 
   
   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihren Protokollordner an. Wenn Sie die Werte Ihrer Anmeldeinformationen für den Paketzugriff (siehe oben) zuvor eingegeben haben, können Sie diese Werte wiederverwenden, indem Sie das Kontrollkästchen **Wie Anmeldeinformationen für Paketzugriff** aktivieren. Geben Sie andernfalls die Werte Ihrer Anmeldeinformationen für den Protokollierungszugriff in die Felder **Domäne**, **Benutzername** und **Kennwort** ein. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden (siehe oben).
   
Für alle zuvor erwähnten UNC-Pfade muss der vollqualifizierte Dateiname weniger als 260 Zeichen umfassen. Der Verzeichnisname muss weniger als 248 Zeichen umfassen.

##### <a name="package-location-embedded-package"></a>Paketspeicherort: Eingebettetes Paket

Wenn Sie **Eingebettetes Paket** als Ihren Paketspeicherort auswählen, führen Sie die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Eingebettetes Paket](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Verschieben Sie Ihre Paketdatei (`.dtsx`) per Drag & Drop in das bereitgestellte Feld, oder laden Sie sie aus einem Dateiordner hoch. Ihr Paket wird automatisch komprimiert und in die Nutzlast für die Aktivität eingebettet. Nachdem das Paket eingebettet wurde, können Sie es zum späteren Bearbeiten **herunterladen**. Sie können Ihr eingebettetes Paket auch **Parametrisieren**, indem Sie es einem Pipelineparameter zuweisen, der in mehreren Aktivitäten verwendet werden kann. Auf diese Weise wird die Größe Ihrer Pipelinenutzlast optimiert. Das Einbetten von Projektdateien (`.ispac`) wird derzeit nicht unterstützt. Daher können in Ihren eingebetteten Paketen keine SSIS-Parameter/Verbindungs-Manager auf der Projektebene verwendet werden.
   
   1. Wenn Ihr eingebettetes Paket nicht vollständig verschlüsselt wurde und darin die Verwendung des Tasks „Paket ausführen“ (Execute Package Task, EPT) erkannt wird, wird das Kontrollkästchen **Task „Paket ausführen“** automatisch aktiviert, und Ihre untergeordneten Pakete, auf die anhand ihres Dateisystempfads verwiesen wird, werden automatisch hinzugefügt, damit Sie sie ebenfalls einbetten können.
   
      Wenn die Verwendung von EPT nicht erkannt werden kann, müssen Sie das Kontrollkästchen **Task „Paket ausführen“** manuell aktivieren und Ihre untergeordneten Pakete, auf die anhand ihres Dateisystempfads verwiesen wird, einzeln hinzufügen, um sie ebenfalls einbetten zu können. Wenn Ihre untergeordneten Pakete in SQL Server-Datenbank (MSDB) gespeichert sind, können sie nicht eingebettet werden. Achten Sie daher darauf, dass Ihre Azure-SSIS IR auf MSDB zugreifen kann, um sie unter Verwendung ihrer SQL Server-Verweise abzurufen. Das Einbetten von Projektdateien (`.ispac`) wird derzeit nicht unterstützt. Daher können für Ihre untergeordneten Pakete keine projektbasierten Verweise verwendet werden.
   
   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptAllWithPassword** oder **EncryptSensitiveWithPassword** verwendet haben, müssen Sie im Feld **Verschlüsselungskennwort** den Wert für Ihr Kennwort eingeben. 
   
      Alternativ können Sie ein in Ihrem Azure Key Vault gespeichertes Geheimnis als Wert verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben dem Wert. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`.
      
      Wenn Sie die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben, geben Sie Ihre vertraulichen Werte in Konfigurationsdateien oder aber auf den Registerkarten **SSIS-Parameter**, **Verbindungs-Manager** oder **Eigenschaftenüberschreibungen** erneut ein (siehe unten).
      
      Die Verwendung der Schutzebene **EncryptAllWithUserKey** wird nicht unterstützt. Sie müssen Ihr Paket über SSDT oder das Befehlszeilen-Hilfsprogramm `dtutil` für die Verwendung einer anderen Schutzebene neu konfigurieren.

   1. Wählen Sie für **Protokolliergrad** einen vordefinierten Bereich für die Protokollierung Ihrer Paketausführung. Aktivieren Sie das Kontrollkästchen **Angepasst**, wenn Sie stattdessen einen angepassten Namen für die Protokollierung eingeben möchten. 
   
   1. Wenn Sie für Ihre Paketausführungen eine Protokollierung verwenden möchten, die über die Standardprotokollanbieter hinausgeht, die in Ihrem Paket angegeben werden können, geben Sie im Feld **Protokollierungspfad** den UNC-Pfad Ihres Protokollordners an. Sie können Ihren Protokollordner durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lautet Ihr Protokollierungspfad `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. In diesem Pfad wird für jedes ausgeführte Einzelpaket ein Unterordner erstellt und mit der Aktivitätsausführungs-ID von „SSIS-Paket ausführen“ benannt. In diesem Unterordner werden alle fünf Minuten Protokolldateien generiert. 
   
   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihren Protokollordner an, indem Sie deren Werte in den Feldern **Domäne**, **Benutzername** und **Kennwort** eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden (siehe oben).
   
Für alle zuvor erwähnten UNC-Pfade muss der vollqualifizierte Dateiname weniger als 260 Zeichen umfassen. Der Verzeichnisname muss weniger als 248 Zeichen umfassen.

##### <a name="package-location-package-store"></a>Paketspeicherort: Paketspeicher

Wenn Sie **Paketspeicher** als Ihren Paketspeicherort auswählen, führen Sie die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Einstellungen“ – Paketspeicher](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. Wählen Sie unter **Name des Paketspeichers** einen vorhandenen Paketspeicher aus, der an Ihre Azure-SSIS Integration Runtime-Instanz angefügt ist.

   1. Geben Sie Ihr auszuführendes Paket an, indem Sie im Feld **Paketpfad** dessen Pfad (ohne `.dtsx`) aus dem ausgewählten Paketspeicher eingeben. Wenn der ausgewählte Paketspeicher auf Dateisystem/Azure Files basiert, können Sie Ihr Paket durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen. Andernfalls können Sie den Speicherpfad im Format `<folder name>\<package name>` eingeben. Sie können neue Pakete auch über SQL Server Management Studio (SSMS) in den ausgewählten Paketspeicher importieren, ähnlich wie beim [Legacy-SSIS-Paketspeicher](/sql/integration-services/service/package-management-ssis-service). Weitere Informationen finden Sie unter [Verwalten von SSIS-Paketen mit Azure-SSIS IR-Paketspeichern](./azure-ssis-integration-runtime-package-store.md).

   1. Wenn Sie Ihr Paket in einer separaten Datei konfigurieren, müssen Sie im Feld **Konfigurationspfad** einen UNC-Pfad zu Ihrer Konfigurationsdatei (mit `.dtsConfig`) angeben. Sie können Ihre Konfiguration durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Konfiguration also beispielsweise in Azure Files speichern, lautet der Pfad `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   1. Aktivieren Sie das Kontrollkästchen **Anmeldeinformationen für den Konfigurationszugriff**, um auszuwählen, dass Sie die Anmeldeinformationen für den Zugriff auf Ihre Konfigurationsdatei separat eingeben möchten. Dies ist erforderlich, wenn der ausgewählte Paketspeicher auf SQL Server Datenbank (MSDB) basiert, die von Ihrer verwalteten Azure SQL-Instanz gehostet wird, oder wenn er außerdem Ihre Konfigurationsdatei nicht speichert.
   
      Wenn Sie die Werte Ihrer Anmeldeinformationen für Paketausführung (für die **Windows-Authentifizierung**) zuvor eingegeben haben, können Sie diese Werte wiederverwenden, indem Sie das Kontrollkästchen **Wie Anmeldeinformationen für Paketausführung** aktivieren. Geben Sie andernfalls die Werte Ihrer Anmeldeinformationen für den Konfigurationszugriff in den Feldern **Domäne**, **Benutzername** und **Kennwort** ein. Wenn Sie Ihre Konfiguration also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. 

      Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`.

   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptAllWithPassword** oder **EncryptSensitiveWithPassword** verwendet haben, müssen Sie im Feld **Verschlüsselungskennwort** den Wert für Ihr Kennwort eingeben. Alternativ können Sie ein in Ihrem Azure-Schlüsseltresor gespeichertes Geheimnis als Wert verwenden (siehe oben).
      
      Wenn Sie die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben, geben Sie Ihre vertraulichen Werte in Konfigurationsdateien oder aber auf den Registerkarten **SSIS-Parameter**, **Verbindungs-Manager** oder **Eigenschaftenüberschreibungen** erneut ein (siehe unten).
      
      Die Verwendung der Schutzebene **EncryptAllWithUserKey** wird nicht unterstützt. Sie müssen Ihr Paket über SSDT oder das Befehlszeilen-Hilfsprogramm `dtutil` für die Verwendung einer anderen Schutzebene neu konfigurieren. 

   1. Wählen Sie für **Protokolliergrad** einen vordefinierten Bereich für die Protokollierung Ihrer Paketausführung. Aktivieren Sie das Kontrollkästchen **Angepasst**, wenn Sie stattdessen einen angepassten Namen für die Protokollierung eingeben möchten. 
   
   1. Wenn Sie für Ihre Paketausführungen eine Protokollierung verwenden möchten, die über die Standardprotokollanbieter hinausgeht, die in Ihrem Paket angegeben werden können, geben Sie im Feld **Protokollierungspfad** den UNC-Pfad Ihres Protokollordners an. Sie können Ihren Protokollordner durchsuchen und auswählen, indem Sie **Dateispeicher durchsuchen** auswählen oder den Pfad manuell eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lautet Ihr Protokollierungspfad `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. In diesem Pfad wird für jedes ausgeführte Einzelpaket ein Unterordner erstellt und mit der Aktivitätsausführungs-ID von „SSIS-Paket ausführen“ benannt. In diesem Unterordner werden alle fünf Minuten Protokolldateien generiert. 
   
   1. Geben Sie die Anmeldeinformationen für den Zugriff auf Ihren Protokollordner an, indem Sie deren Werte in den Feldern **Domäne**, **Benutzername** und **Kennwort** eingeben. Wenn Sie Ihre Protokolle also beispielsweise in Azure Files speichern, lauten die Werte wie folgt: Domäne: `Azure`; Benutzername: `<storage account name>`; Kennwort: `<storage account key>`. Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden (siehe oben).
   
Für alle zuvor erwähnten UNC-Pfade muss der vollqualifizierte Dateiname weniger als 260 Zeichen umfassen. Der Verzeichnisname muss weniger als 248 Zeichen umfassen.

#### <a name="ssis-parameters-tab"></a>Registerkarte „SSIS-Parameter“

Führen Sie auf der Registerkarte **SSIS-Parameter** der Aktivität „SSIS-Paket ausführen“ die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „SSIS-Parameter“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Wenn Ihre Azure-SSIS Integration Runtime-Instanz ausgeführt wird, die Option **SSISDB** als Ihr Paketspeicherort ausgewählt ist und auf der Registerkarte **Einstellungen** das Kontrollkästchen **Manuelle Einträge** deaktiviert ist, werden die vorhandenen SSIS-Parameter in Ihrem ausgewählten Projekt oder Paket aus SSISDB angezeigt, damit Sie ihnen Werte zuweisen können. Andernfalls können Sie sie einzeln eingeben, um ihnen manuell Werte zuzuweisen. Vergewissern Sie sich, dass sie vorhanden sind und korrekt eingegeben wurden, damit Ihre Paketausführung erfolgreich ist. 
   
   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben und **Dateisystem (Paket)** , **Dateisystem (Projekt)** , **Eingebettetes Paket** oder **Paketspeicher** als Ihr Paketspeicherort ausgewählt ist, müssen Sie Ihre vertraulichen Parameter erneut eingeben, um ihnen auf dieser Registerkarte Werte zuzuweisen. 
   
Beim Zuweisen von Werten für Ihre Parameter können Sie Ausdrücke, Funktionen, Data Factory-Systemvariablen und Data Factory-Pipelineparameter oder -Variablen verwenden, um dynamische Inhalte hinzuzufügen.

Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`. 

#### <a name="connection-managers-tab"></a>Registerkarte „Verbindungs-Manager“

Führen Sie auf der Registerkarte **Verbindungs-Manager** der Aktivität „SSIS-Paket ausführen“ die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Verbindungs-Manager“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Wenn Ihre Azure-SSIS Integration Runtime-Instanz ausgeführt wird, die Option **SSISDB** als Ihr Paketspeicherort ausgewählt ist und auf der Registerkarte **Einstellungen** das Kontrollkästchen **Manuelle Einträge** deaktiviert ist, werden Ihnen die vorhandenen Verbindungs-Manager in Ihrem ausgewählten Projekt und Paket aus SSISDB angezeigt, damit Sie deren Eigenschaften Werte zuweisen können. Andernfalls können Sie sie einzeln eingeben, um ihren Eigenschaften manuell Werte zuzuweisen. Vergewissern Sie sich, dass sie vorhanden sind und korrekt eingegeben wurden, damit Ihre Paketausführung erfolgreich ist. 
   
      Sie können die korrekten Werte für **SCOPE**, **NAME** und **PROPERTY** für einen beliebigen Verbindungs-Manager abrufen, indem Sie das enthaltende Paket in SSDT öffnen. Wählen Sie nach dem Öffnen des Pakets den relevanten Verbindungs-Manager aus, um die Namen und Werte für alle zugehörigen Eigenschaften im Fenster **Eigenschaften** von SSDT anzuzeigen. Mit diesen Informationen können Sie die Werte aller Verbindungs-Manager-Eigenschaften zur Laufzeit überschreiben. 

      ![Abrufen von Verbindungs-Manager-Eigenschaften über SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      So können Sie beispielsweise die lokalen Datenflüsse Ihres Originalpakets, die in SQL Server ausgeführt werden, in Datenflüsse von der lokalen Umgebung zur Cloud konvertieren, die in SSIS IR in ADF ausgeführt werden, ohne Ihr Originalpaket in SSDT ändern zu müssen. Überschreiben Sie hierzu zur Laufzeit die Werte der Eigenschaften **ConnectByProxy**, **ConnectionString** und **ConnectUsingManagedIdentity** in vorhandenen Verbindungs-Managern.
      
      Diese Überschreibungen zur Laufzeit ermöglichen ggf. die Verwendung von selbstgehosteter IR (Self-Hosted IR, SHIR) als Proxy für SSIS IR, wenn auf lokale Daten zugegriffen wird (siehe [Konfigurieren einer selbstgehosteten IR als Proxy für eine Azure-SSIS IR in Azure Data Factory](./self-hosted-integration-runtime-proxy-ssis.md)), sowie Verbindungen mit Azure SQL-Datenbank/verwalteten Instanzen unter Verwendung des aktuellen MSOLEDBSQL-Treibers, der wiederum die AAD-Authentifizierung (Azure Active Directory) mit von ADF verwalteter Identität für OLEDB-Verbindungen ermöglicht (siehe [Verwaltete Identitäten für die Authentifizierung von Azure-Ressourcen](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)).

      ![Festlegen von Eigenschaften aus SSDT auf der Registerkarte „Verbindungs-Manager“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben und **Dateisystem (Paket)** , **Dateisystem (Projekt)** , **Eingebettetes Paket** oder **Paketspeicher** als Ihr Paketspeicherort ausgewählt ist, müssen Sie auch Ihre vertraulichen Verbindungs-Manager-Eigenschaften erneut eingeben, um ihnen auf dieser Registerkarte Werte zuzuweisen. 

Beim Zuweisen von Werten für Ihre Verbindungs-Manager-Eigenschaften können Sie Ausdrücke, Funktionen, Data Factory-Systemvariablen und Data Factory-Pipelineparameter oder -Variablen verwenden, um dynamische Inhalte hinzuzufügen. 

Alternativ können Sie in Ihrem Azure-Schlüsseltresor gespeicherte Geheimnisse als Werte verwenden. Aktivieren Sie dazu das Kontrollkästchen **AZURE KEY VAULT** neben den Werten. Sie können einen vorhandenen verknüpften Dienst für den Schlüsseltresor auswählen oder bearbeiten oder einen neuen Dienst erstellen. Wählen Sie dann den Namen und die Version des Geheimnisses als Ihren Wert aus. Wenn Sie Ihren verknüpften Dienst für den Schlüsseltresor erstellen oder bearbeiten, können Sie den vorhandenen Schlüsseltresor auswählen und bearbeiten oder einen neuen erstellen. Stellen Sie sicher, dass die verwaltete Data Factory-Identität Zugriff auf Ihren Schlüsseltresor hat, sofern dies noch nicht der Fall ist. Sie können Ihr Geheimnis auch direkt im folgenden Format eingeben: `<key vault linked service name>/<secret name>/<secret version>`. 

#### <a name="property-overrides-tab"></a>Registerkarte „Eigenschaftenüberschreibungen“

Führen Sie auf der Registerkarte **Eigenschaftenüberschreibungen** der Aktivität „SSIS-Paket ausführen“ die folgenden Schritte aus.

![Festlegen von Eigenschaften auf der Registerkarte „Eigenschaftenüberschreibungen“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Geben Sie nacheinander die Pfade vorhandener Eigenschaften in Ihrem ausgewählten Paket ein, um ihnen Werte manuell zuzuweisen. Vergewissern Sie sich, dass sie vorhanden sind und korrekt eingegeben wurden, damit Ihre Paketausführung erfolgreich ist. Geben Sie also beispielsweise zum Überschreiben des Werts Ihrer Benutzervariablen den zugehörigen Pfad im folgenden Format ein: `\Package.Variables[User::<variable name>].Value`. 

      Sie können den korrekten Wert für **PROPERTY PATH** für eine beliebige Paketeigenschaft abrufen, indem Sie das enthaltende Paket in SSDT öffnen. Wählen Sie nach dem Öffnen des Pakets die zugehörige Ablaufsteuerung und die Eigenschaft **Konfigurationen** im Fenster **Eigenschaften** von SSDT aus. Wählen Sie anschließend neben der Eigenschaft **Konfigurationen** die Schaltfläche mit den Auslassungspunkten ( **...** ) aus, um den **Paketkonfigurationsplaner** zu öffnen, der normalerweise zum [Erstellen von Paketkonfigurationen im Paketbereitstellungsmodell](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations) verwendet wird. 

      ![Abrufen von Paketeigenschaften aus SSDT: Eigenschaft „Konfigurationen“](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      Aktivieren Sie im **Paketkonfigurationsplaner** das Kontrollkästchen **Paketkonfigurationen aktivieren**, und wählen Sie die Schaltfläche **Hinzufügen...** aus, um den **Paketkonfigurations-Assistenten** zu öffnen. 
      
      Wählen Sie im **Paketkonfigurations-Assistenten** im Dropdownmenü **Konfigurationstyp** das Element **XML-Konfigurationsdatei** aus. Wählen Sie anschließend die Schaltfläche **Konfigurationseinstellungen direkt angeben** aus, geben Sie den Namen Ihrer Konfigurationsdatei ein, und wählen Sie die Schaltfläche **Weiter >** aus. 

      ![Abrufen von Paketeigenschaften aus SSDT: Konfigurationsplaner](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Wählen Sie abschließend die Paketeigenschaften aus, deren Pfad Sie benötigen, und wählen Sie die Schaltfläche **Weiter >** aus.  Nun können Sie die gewünschten Paketeigenschaftspfade anzeigen, kopieren, einfügen und in Ihrer Konfigurationsdatei speichern. Mit diesen Informationen können Sie die Werte aller Paketeigenschaften zur Laufzeit überschreiben. 

      ![Abrufen von Paketeigenschaften aus SSDT: Konfigurations-Assistent](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Wenn Sie bei der Paketerstellung über SSDT die Schutzebene **EncryptSensitiveWithUserKey** verwendet haben und **Dateisystem (Paket)** , **Dateisystem (Projekt)** , **Eingebettetes Paket** oder **Paketspeicher** als Ihr Paketspeicherort ausgewählt ist, müssen Sie auch Ihre vertraulichen Paketeigenschaften erneut eingeben, um ihnen auf dieser Registerkarte Werte zuzuweisen. 
   
Beim Zuweisen von Werten zu Ihren Paketeigenschaften können Sie dynamische Inhalte mithilfe von Ausdrücken, Funktionen, Data Factory-Systemvariablen und Data Factory-Pipelineparametern oder -variablen hinzufügen.

Die in den Konfigurationsdateien und auf der Registerkarte **SSIS-Parameter** zugewiesenen Werte können mithilfe der Registerkarten **Verbindungs-Manager** oder **Eigenschaftenüberschreibungen** überschrieben werden. Die auf der Registerkarte **Verbindungs-Manager** zugewiesenen Werte können ebenfalls auf der Registerkarte **Eigenschaftenüberschreibungen** überschrieben werden.

Wählen Sie zum Überprüfen der Pipelinekonfiguration auf der Symbolleiste die Option **Überprüfen** aus. Wählen Sie zum Schließen von **Pipeline Validation Report** (Pipelineüberprüfungsbericht) die Option **>>** aus.

Veröffentlichen Sie die Pipeline in Data Factory, indem Sie die Schaltfläche **Alle veröffentlichen** auswählen. 

### <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.
In diesem Schritt lösen Sie eine Pipelineausführung aus. 

1. Wählen Sie zum Auslösen einer Pipelineausführung auf der Symbolleiste die Option **Trigger** (Auslösen) und dann **Trigger now** (Jetzt auslösen) aus. 

   ![Manuelles Auslösen](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Wählen Sie im Fenster **Pipelineausführung** die Option **Fertig stellen** aus. 

### <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

1. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen**. Die Pipelineausführung, der zugehörige Status sowie weitere Informationen (z. B. **Startzeit der Ausführung**) werden angezeigt. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

   ![Pipelineausführungen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klicken Sie in der Spalte **Aktionen** auf den Link **View Activity Runs** (Aktivitätsausführungen anzeigen). Sie sehen nur eine Aktivitätsausführung, da die Pipeline nur eine Aktivität enthält. Dabei handelt es sich um die Aktivität „SSIS-Paket ausführen“.

   ![Aktivitätsausführungen](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Führen Sie auf Ihrem SQL-Server die folgende Abfrage für die SSISDB-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

   ```sql
   select * from catalog.executions
   ```

   ![Überprüfen von Paketausführungen](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Sie können auch die SSISDB-Ausführungs-ID aus der Ausgabe der Pipelineaktivitätsausführung abrufen und anhand der ID umfangreichere Ausführungsprotokolle und Fehlermeldungen in SQL Server Management Studio überprüfen.

   ![Abrufen der Ausführungs-ID](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planen der Pipeline mit einem Trigger

Sie können auch einen geplanten Trigger für die Pipeline erstellen, damit die Pipeline basierend auf einem Zeitplan (etwa stündlich oder täglich) ausgeführt wird. Ein Beispiel finden Sie unter [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Ausführen eines Pakets mit PowerShell
In diesem Abschnitt erstellen Sie mithilfe von Azure PowerShell eine Data Factory-Pipeline mit der Aktivität „SSIS-Paket ausführen“, die Ihr SSIS-Paket ausführt. 

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Schritt-für-Schritt-Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) befolgen.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Erstellen einer Data Factory mit Azure-SSIS IR
Sie können entweder eine vorhandene Data Factory verwenden, in der Azure-SSIS IR bereits bereitgestellt wurde, oder eine neue Data Factory mit Azure-SSIS IR erstellen. Befolgen Sie dann die Schrittanleitungen unter [Tutorial: Bereitstellen von SSIS-Paketen in Azure mit PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md) folgen.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Erstellen einer Pipeline mit einer Aktivität „SSIS-Paket ausführen“ 
In diesem Schritt erstellen Sie eine Pipeline mit einer Aktivität „SSIS-Paket ausführen“. Die Aktivität führt Ihr SSIS-Paket aus. 

1. Erstellen Sie im Ordner `C:\ADF\RunSSISPackage` die JSON-Datei `RunSSISPackagePipeline.json` mit Inhalt, der dem folgenden Beispiel ähnelt.

   > [!IMPORTANT]
   > Bevor Sie die Datei speichern, ersetzen Sie Objektnamen, Beschreibungen, Pfade, Eigenschafts- oder Parameterwerte, Kennwörter und andere Variablenwerte. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen, die im Dateisystem/in Azure Files gespeichert sind, die Werte für Ihre Paket- und Protokollspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen in Projekten, die im Dateisystem/in Azure Files gespeichert sind, die Werte für Ihre Paketspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von eingebetteten Paketen die Werte für Ihre Paketspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen, die in Paketspeichern gespeichert sind, die Werte für Ihre Paket- und Konfigurationsspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Navigieren Sie in Azure PowerShell zum Ordner `C:\ADF\RunSSISPackage`.

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2Pipeline** aus, um die Pipeline **RunSSISPackagePipeline** zu erstellen.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier ist die Beispielausgabe:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.
Verwenden Sie das Cmdlet **Invoke-AzDataFactoryV2Pipeline**, um die Pipeline auszuführen. Das Cmdlet gibt die ID der Pipelineausführung für die zukünftige Überwachung zurück.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

Führen Sie das folgende PowerShell-Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist. Kopieren Sie das folgende Skript, fügen Sie es in das PowerShell-Fenster ein, und drücken Sie die EINGABETASTE. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Sie können die Pipeline auch mithilfe des Azure-Portals überwachen. Schritt-für-Schritt-Anweisungen finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planen der Pipeline mit einem Trigger
Im vorherigen Schritt wurde die Pipeline auf Anforderung ausgeführt. Alternativ können Sie einen Zeitplantrigger erstellen, um die Pipeline nach Zeitplan (etwa stündlich oder täglich) auszuführen.

1. Erstellen Sie im Ordner `MyTrigger.json` die JSON-Datei `C:\ADF\RunSSISPackage` mit folgendem Inhalt: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Navigieren Sie in Azure PowerShell zum Ordner `C:\ADF\RunSSISPackage`.
1. Führen Sie das Cmdlet **Set-AzDataFactoryV2Trigger** aus, um den Trigger zu erstellen. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Standardmäßig befindet sich der Trigger im beendeten Zustand. Starten Sie den Trigger durch Ausführen des Cmdlets **Start-AzDataFactoryV2Trigger**. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Bestätigen Sie, dass der Trigger sich im gestarteten Zustand befindet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** ausführen. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Führen Sie nach der nächsten vollen Stunde den folgenden Befehl aus. Wenn die aktuelle Uhrzeit z.B. 15:25 UTC ist, führen Sie den Befehl um 16:00 UTC aus. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Führen Sie auf Ihrem SQL-Server die folgende Abfrage für die SSISDB-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nächste Schritte
Informationen finden Sie im folgenden Blogbeitrag:
- [Modernisieren und Erweitern von ETL/ELT-Workflows mit SSIS-Aktivitäten in Azure Data Factory-Pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)