---
title: Ausführen von SSIS-Paketen mit den SSDT
description: Erfahren Sie, wie Sie über SSDT SSIS-Pakete in Azure ausführen.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384927"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Ausführen von SSIS-Paketen in Azure über SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden Azure-fähige SSIS-Projekte (SQL Server Integration Services) in SQL Server Data Tools (SSDT) beschrieben. Damit können Sie die Cloudkompatibilität Ihrer SSIS-Pakete bewerten und sie in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ausführen. Mit diesem Feature können Sie vorhandene Pakete vor einer Lift & Shift-Migration zu Azure testen oder neue Pakete entwickeln, die in Azure ausgeführt werden können.

Sie können eine neu erstellte oder bereits vorhandene Azure-SSIS IR-Instanz an SSIS-Projekte anfügen und Ihre Pakete damit ausführen.  Wir unterstützen das Ausführen von Paketen für die Bereitstellung im SSIS-Katalog (SSISDB) – gehostet von Ihrem Azure SQL-Datenbank-Server oder von einer verwalteten Instanz im Projektbereitstellungsmodell. Darüber hinaus unterstützen wir das Ausführen von Paketen für die Bereitstellung im Dateisystem/in Azure Files/in SQL Server-Datenbank (MSDB) – gehostet von Azure SQL Managed Instance im Paketbereitstellungsmodell. 

## <a name="prerequisites"></a>Voraussetzungen

Laden Sie zur Verwendung dieses Features [hier](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) die aktuelle SSDT-Version mit SSIS-Projekterweiterung für Visual Studio (VS) herunter, und installieren Sie sie. Alternativ können Sie [hier](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer) die aktuelle SSDT-Version als eigenständigen Installer herunterladen und installieren.

## <a name="azure-enable-ssis-projects"></a>Azure-fähige SSIS-Projekte

### <a name="creating-new-azure-enabled-ssis-projects"></a>Erstellen neuer Azure-fähiger SSIS-Projekte

In SSDT können Sie mithilfe der Vorlage **Integration Services-Projekt (Azure-fähig)** neue, Azure-fähige SSIS-Projekte erstellen.

   ![Neues Azure-fähiges SSIS-Projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Nachdem das Azure-fähige Projekt erstellt wurde, werden Sie in Azure Data Factory aufgefordert, eine Verbindung mit SSIS herzustellen.

   ![Aufforderung zum Herstellen einer Verbindung mit Azure-SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Wenn Sie sofort eine Verbindung mit Azure-SSIS IR herstellen möchten, finden Sie ausführlichere Informationen unter [Herstellen einer Verbindung von Azure-fähigen Projekten mit SSIS in Azure Data Factory](#connectssisir). Sie können auch später eine Verbindung herstellen, indem Sie im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf Ihren Projektknoten klicken, um ein Menü einzublenden. Wählen Sie als Nächstes im Untermenü **SSIS in Azure Data Factory** das Element **Mit SSIS in Azure Data Factory verbinden** aus.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Konfigurieren vorhandener SSIS-Projekte für Azure

Für bestehende SSIS-Projekte können Sie Azure aktivieren, indem Sie die folgenden Schritte befolgen:

1. Klicken Sie im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf Ihren Projektknoten, um ein Menü einzublenden. Wählen Sie als Nächstes im Untermenü **SSIS in Azure Data Factory** das Element **Azure-fähiges Projekt** aus, um den **Assistenten für Azure-fähige Projekte** zu starten.

   ![Konfigurieren eines vorhandenen SSIS-Projekts für Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Wählen Sie auf der Seite **Visual Studio-Konfiguration auswählen** Ihre vorhandene VS-Konfiguration aus, um Einstellungen für die Paketausführung in Azure anzuwenden. Bei Bedarf können Sie auch eine neue Konfiguration erstellen. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen und Bearbeiten von Konfigurationen](/visualstudio/ide/how-to-create-and-edit-configurations). Sie sollten über mindestens zwei verschiedene VS-Konfigurationen für Paketausführungen in der lokalen Umgebung und in der Cloudumgebung verfügen, damit Sie Ihr Projekt unter Verwendung der Cloudkonfiguration für Azure konfigurieren können. Wenn Sie Ihr Projekt oder Ihre Pakete parametrisiert haben, können Sie Ihren Projekt- oder Paketparametern auf diese Weise zur Laufzeit je nach Ausführungsumgebung (lokaler Computer oder Azure) unterschiedliche Werte zuweisen. Sehen Sie sich hierzu zum Beispiel [Wechseln von Paketausführungsumgebungen](#switchenvironment) an.

   ![Auswählen der Visual Studio-Konfiguration](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Um Ihre vorhandenen SSIS-Projekte für Azure zu konfigurieren, müssen Sie die Zielserverversion auf die neueste von Azure-SSIS IR unterstützte Version festlegen. Azure-SSIS IR basiert aktuell auf **SQL Server 2017**. Achten Sie darauf, dass Ihre Pakete keine Zusatzkomponenten enthalten, die für SQL Server 2017 nicht unterstützt werden. Achten Sie außerdem darauf, dass alle kompatiblen Zusatzkomponenten auch unter Verwendung benutzerdefinierter Setups in Ihrer Azure-SSIS IR-Instanz installiert wurden. Weitere Informationen finden Sie unter [Anpassen des Setups für eine Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md). Wählen Sie die Schaltfläche **Weiter** aus, um den Vorgang fortzusetzen.

   ![Wechseln der Zielserverversion](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Gehen Sie wie unter [Herstellen einer Verbindung von Azure-fähigen Projekten mit SSIS in Azure Data Factory](#connectssisir) beschrieben vor, um die Verbindungsherstellung mit Azure-SSIS IR für Ihr Projekt abzuschließen.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Herstellen einer Verbindung von Azure-fähigen Projekten mit SSIS in Azure Data Factory

Durch die Verbindungsherstellung Ihrer Azure-fähigen Projekte mit SSIS in ADF können Sie Ihre Pakete in Azure Files hochladen und in Azure-SSIS IR ausführen. Gehen Sie hierzu wie folgt vor:

1. Lesen Sie die Einführung auf der Seite **SSIS in ADF – Einführung**, und wählen Sie die Schaltfläche **Weiter** aus, um den Vorgang fortzusetzen.

   ![SSIS in ADF – Einführung](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Wählen Sie auf der Seite **SSIS IR in ADF auswählen** Ihre vorhandene ADF- und Azure-SSIS IR-Instanz für die Paketausführung aus. Bei Bedarf können auch neue Instanzen erstellt werden.
   - Um Ihre vorhandene Azure-SSIS IR auszuwählen, wählen Sie zunächst das relevante Azure-Abonnement und die ADF-Instanz aus.
   - Wenn Sie eine vorhandene ADF-Instanz auswählen, die über keine Azure-SSIS IR-Instanz verfügt, wählen Sie die Schaltfläche **SSIS IR erstellen** aus, um im ADF-Portal eine neue IR-Instanz zu erstellen. Nach der Erstellung können Sie zu dieser Seite zurückkehren, um Ihre neue Azure-SSIS IR-Instanz auszuwählen.
   - Wenn Sie ein vorhandenes Azure-Abonnement auswählen, das über keine ADF-Instanz verfügt, wählen Sie die Schaltfläche **SSIS IR erstellen** aus, um den **Assistenten zum Erstellen einer Integration Runtime** zu starten. Im Assistenten können Sie den gewünschten Standort und das gewünschte Präfix eingeben. Daraufhin werden automatisch eine neue Azure-Ressourcengruppe, Data Factory und SSIS IR-Instanz erstellt und nach dem folgenden Muster benannt: **IhrPräfix-RG/DF/IR-ZeitpunktDerErstellung**. Nach der Erstellung können Sie zu dieser Seite zurückkehren, um Ihre neue Azure- und Azure-SSIS IR-Instanz auszuwählen.

   ![SSIS IR in ADF auswählen](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Wählen Sie auf der Seite **Azure Storage-Instanz auswählen** Ihr vorhandenes Azure Storage-Konto zum Hochladen von Paketen in Azure Files aus. Bei Bedarf kann auch eine neue Instanz erstellt werden.
   - Um Ihr vorhandenes Azure Storage-Konto auszuwählen, wählen Sie zunächst das relevante Azure-Abonnement aus.
   - Wenn Sie das gleiche Azure-Abonnement wie für Ihre Azure-SSIS IR-Instanz auswählen und kein Azure Storage-Konto vorhanden ist, wählen Sie die Schaltfläche **Azure Storage-Instanz erstellen** aus. Daraufhin wird automatisch eine neue Instanz erstellt. Diese befindet sich am gleichen Standort wie Ihre Azure-SSIS IR-Instanz, und als Name wird eine Kombination aus einem Präfix Ihres Azure-SSIS IR-Namens und dem Erstellungsdatum verwendet. Nach der Erstellung können Sie zu dieser Seite zurückkehren, um Ihr neues Azure Storage-Konto auszuwählen.
   - Wenn Sie ein anderes Azure-Abonnement auswählen, das über kein Azure Storage-Konto verfügt, wählen Sie die Schaltfläche **Azure Storage-Instanz erstellen** aus, um über das Azure-Portal ein neues Konto zu erstellen. Nach der Erstellung können Sie zu dieser Seite zurückkehren, um Ihr neues Azure Storage-Konto auszuwählen.

   ![Auswählen von Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Wählen Sie die Schaltfläche **Verbinden** aus, um die Verbindungsherstellung mit Azure-SSIS IR für Ihr Projekt abzuschließen. Die ausgewählte Azure-SSIS IR-Instanz und das Azure Storage-Konto werden im Fenster „Projektmappen-Explorer“ von SSDT unter dem Knoten **Verknüpfte Azure-Ressourcen** angezeigt. Außerdem wird hier regelmäßig der Status Ihrer Azure-SSIS IR-Instanz aktualisiert und angezeigt. Sie können Ihre Azure-SSIS IR-Instanz verwalten, indem Sie mit der rechten Maustaste auf den zugehörigen Knoten klicken, um ein Popupmenü zu öffnen. In diesem Menü können Sie das Element **Starten\Beenden\Verwalten** auswählen, um zur Ausführung der jeweiligen Aufgabe zum ADF-Portal zu gelangen.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Bewerten von SSIS-Projekten oder -Paketen für die Ausführung in Azure

### <a name="assessing-single-or-multiple-packages"></a>Bewerten einzelner oder mehrerer Pakete

Bevor Sie Ihre Pakete in Azure ausführen, können Sie sie bewerten, um potenzielle Probleme mit der Cloudkompatibilität zu ermitteln. Dazu zählen Migrationsblockierungen und zusätzliche Informationen, mit denen Sie vertraut sein sollten. 
-  Sie können entweder einzelne Pakete nacheinander oder alle Pakete gleichzeitig unter Ihrem Projekt bewerten.

   ![Paketbewertung](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Projektbewertung](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  Im Fenster **Assessment Report** (Bewertungsbericht) von SSDT finden Sie alle erkannten potenziellen Cloudkompatibilitätsprobleme sowie jeweils eine ausführliche Beschreibung und Empfehlung. Sie können den Bewertungsbericht auch in eine CSV-Datei exportieren, die zur Behandlung der Probleme an eine beliebige Person weitergegeben werden kann. 

   ![Bewertungsbericht](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Unterdrücken von Bewertungsregeln

Nachdem Sie sich vergewissern haben, dass einige potenzielle Cloudkompatibilitätsprobleme nicht relevant sind oder in Ihren Paketen ordnungsgemäß behandelt wurden, können Sie die entsprechenden Bewertungsregeln unterdrücken. Dadurch werden nicht relevante Informationen in Ihren nachfolgenden Bewertungsberichten reduziert.
-  Wählen Sie im Fenster **Assessment Report** (Bewertungsbericht) von SSDT den Link **Configure Assessment Rule Suppression** (Unterdrückung von Bewertungsregeln konfigurieren) aus, um das Fenster **Assessment Rule Suppression Settings** (Einstellungen für die Unterdrückung von Bewertungsregeln) zu öffnen, in dem Sie die zu unterdrückenden Bewertungsregeln auswählen können.

   ![Einstellungen für die Unterdrückung von Bewertungsregeln](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Klicken Sie alternativ im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf den Projektknoten, um ein Menü einzublenden. Wählen Sie im Untermenü **SSIS in Azure Data Factory** das Element **Azure-Enabled Settings** (Für Azure geeignete Einstellungen) aus, um ein Fenster mit Ihren Projekteigenschaftenseiten anzuzeigen. Wählen Sie im Abschnitt **Azure-Enabled Settings** (Für Azure geeignete Einstellungen) die Eigenschaft **Suppressed Assessment Rule IDs** (IDs unterdrückter Bewertungsregeln) aus. Wählen Sie abschließend die zugehörige Schaltfläche mit den Auslassungspunkten ( **...** ) aus, um das Fenster **Assessment Rule Suppression Settings** (Einstellungen für die Unterdrückung von Bewertungsregeln) anzuzeigen, in dem Sie die zu unterdrückenden Bewertungsregeln auswählen können.

   ![Für Azure geeignete Einstellungen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Einstellungen für die Unterdrückung von Bewertungsregeln über „Azure-enabled settings“ (Für Azure geeignete Einstellungen)](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Ausführen von SSIS-Paketen in Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Konfigurieren von für Azure geeigneten Einstellungen

Vor der Ausführung Ihrer Pakete in Azure können Sie Ihre für Azure geeigneten Einstellungen konfigurieren. So können Sie beispielsweise die Windows-Authentifizierung für Ihre Azure-SSIS IR-Instanz aktivieren, um auf lokale/cloudbasierte Datenspeicher zuzugreifen:

1. Klicken Sie im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf Ihren Projektknoten, um ein Menü einzublenden. Wählen Sie als Nächstes im Untermenü **SSIS in Azure Data Factory** das Element **Azure-Enabled Settings** (Für Azure geeignete Einstellungen) aus, um ein Fenster mit Ihren Projekteigenschaftenseiten anzuzeigen.

   ![Für Azure geeignete Einstellungen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Wählen Sie im Abschnitt **Azure-Enabled Settings** (Für Azure geeignete Einstellungen) die Eigenschaft **Windows-Authentifizierung aktivieren** und anschließend im Dropdownmenü die Option **True** aus. Wählen Sie als Nächstes die Eigenschaft **Anmeldeinformationen für die Windows-Authentifizierung** und anschließend die Schaltfläche mit den Auslassungspunkten ( **...** ) aus, um das Fenster **Anmeldeinformationen für die Windows-Authentifizierung** anzuzeigen.

   ![Windows-Authentifizierung aktivieren](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Geben Sie Ihre Anmeldeinformationen für die Windows-Authentifizierung ein. Für den Zugriff auf Azure Files können Sie beispielsweise `Azure`, `YourStorageAccountName` und `YourStorageAccountKey` für **Domäne**, **Benutzername** und **Kennwort** eingeben.

   ![Anmeldeinformationen für die Windows-Authentifizierung](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Starten von Paketausführungen

Nachdem Sie Ihre Azure-fähigen Projekte mit SSIS in ADF verbunden, die Cloudkompatibilität bewertet und potenzielle Probleme behandelt haben, können Sie Ihre Pakete in Azure-SSIS IR ausführen/testen.
-  Wählen Sie auf der SSDT-Symbolleiste die Schaltfläche **Starten** aus, um ein Dropdownmenü zu öffnen. Wählen Sie als Nächstes das Element **In Azure ausführen** aus.

   ![In Azure ausführen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Klicken Sie alternativ im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf Ihren Paketknoten, um ein Menü einzublenden. Wählen Sie als Nächstes das Element **Execute Package in Azure** (Paket in Azure ausführen) aus.

   ![Ausführen des Pakets in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Um Ihre Pakete in Azure ausführen zu können, müssen Sie über eine ausgeführte Azure-SSIS IR verfügen. Wenn Ihre Azure-SSIS IR angehalten wurde, wird ein Dialogfeld für ihren Start geöffnet. Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von 5 Minuten abgeschlossen sein. Es kann aber etwa 20 bis 30 Minuten dauern, bis Azure-SSIS IR einem virtuellen Netzwerk beitritt. Nach Ausführung Ihrer Pakete in Azure können Sie Ihre Azure-SSIS IR-Instanz beenden, um Ausführungskosten zu sparen. Klicken Sie hierzu im Fenster „Projektmappen-Explorer“ von SSDT mit der rechten Maustaste auf den zugehörigen Knoten, um ein Popupmenü zu öffnen. Wählen Sie anschließend das Element **Starten\Beenden\Verwalten** aus, um zur Ausführung der jeweiligen Aufgabe zum ADF-Portal zu gelangen.

### <a name="using-execute-package-task"></a>Verwenden eines Tasks „Paket ausführen“

Falls Ihre Pakete Paketausführungstasks zur enthalten, die auf untergeordnete, in lokalen Dateisystemen gespeicherte Pakete verweisen, führen Sie die folgenden Zusatzschritte aus:

1. Laden Sie die untergeordneten Pakete unter dem Azure Storage-Konto in Azure Files hoch, das auch mit Ihren Projekten verbunden ist, und rufen Sie den neuen UNC-Pfad (Universal Naming Convention) ab (Beispiel: `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`).

2. Ersetzen Sie den Dateipfad dieser untergeordneten Pakete im Dateiverbindungs-Manager von Paketausführungstasks durch den neuen UNC-Pfad.
   - Sollte Ihr lokaler Computer, auf dem SSDT ausgeführt wird, keinen Zugriff auf den neuen UNC-Pfad haben, können Sie ihn im Eigenschaftenpanel des Dateiverbindungs-Managers eingeben.
   - Alternativ können Sie eine Variable für den Dateipfad verwenden, um zur Laufzeit den richtigen Wert zuzuweisen.

Wenn Ihre Pakete Paketausführungstasks enthalten, die auf untergeordnete Pakete im gleichen Projekt verweisen, ist kein zusätzlicher Schritt erforderlich.

### <a name="switching-package-protection-level"></a>Wechsel der Schutzebene für Pakete

Für die Ausführung von SSIS-Paketen in Azure werden die Schutzebenen **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** nicht unterstützt. Sollten Ihr Pakete also für deren Verwendung konfiguriert sein, werden sie vorübergehend für die Verwendung der Schutzebenen **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** konvertiert. Beim Hochladen Ihrer Pakete in Azure Files für Ausführungen in Ihrer Azure-SSIS IR-Instanz werden außerdem Verschlüsselungskennwörter nach dem Zufallsprinzip generiert.

> [!NOTE]
> Wenn Ihre Pakete Paketausführungstasks enthalten, die auf untergeordnete, für die Verwendung der Schutzebenen **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** konfigurierte Pakete verweisen, müssen diese vor der Paketausführung manuell für die Verwendung der Schutzebenen **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** konfiguriert werden.

Sind Ihre Pakete bereits für die Verwendung der Schutzebenen **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** konfiguriert, bleiben sie unverändert. Beim Hochladen Ihrer Pakete in Azure Files für Ausführungen in Ihrer Azure-SSIS IR-Instanz werden allerdings weiterhin Verschlüsselungskennwörter nach dem Zufallsprinzip generiert.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Wechseln von Paketausführungsumgebungen

Wenn Sie Ihr Projekt/Ihre Pakete im Projektbereitstellungsmodell parametrisieren, können zum Wechseln von Paketausführungsumgebungen mehrere VS-Konfigurationen erstellt werden. Auf diese Weise können Ihren Projekt-/Paketparametern zur Laufzeit umgebungsspezifische Werte zugewiesen werden. Sie sollten über mindestens zwei verschiedene VS-Konfigurationen für Paketausführungen in der lokalen Umgebung und in der Cloudumgebung verfügen, damit Sie Ihre Projekte unter Verwendung der Cloudkonfiguration für Azure konfigurieren können. Im Anschluss wird anhand eines Beispiels Schritt für Schritt das Wechseln von Paketausführungsumgebungen zwischen Ihrem lokalen Computer und Azure gezeigt:

1. Angenommen, Ihr Paket enthält einen Dateisystemtask zum Festlegen der Attribute einer Datei. Wenn Sie ihn auf Ihrem lokalen Computer ausführen, werden die Attribute einer in Ihrem lokalen Dateisystem gespeicherten Datei festgelegt. Wenn Sie ihn in Ihrer Azure-SSIS IR-Instanz ausführen, sollen die Attribute einer in Azure Files gespeicherten Datei festgelegt werden. Erstellen Sie zunächst einen Paketparameter vom Typ „Zeichenfolge“ für den Wert des Zieldateipfads, und nennen Sie ihn **FilePath**.

   ![Erstellen eines Paketparameters](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Parametrisieren Sie als Nächstes auf der Seite **Allgemein** des Fensters **Editor für den Task "Dateisystem"** die Eigenschaft **SourceVariable** (im Abschnitt **Quellverbindung**) mit dem Paketparameter **FilePath**. 

   ![Parametrisieren der Quellverbindung](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Standardmäßig verfügen Sie über eine vorhandene VS-Konfiguration für Paketausführungen in der lokalen Umgebung namens **Entwicklung**. Erstellen Sie bei Bedarf eine neue VS-Konfiguration für Paketausführungen in der Cloudumgebung namens **Azure**. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen und Bearbeiten von Konfigurationen](/visualstudio/ide/how-to-create-and-edit-configurations).

4. Wählen Sie während der Betrachtung der Parameter Ihres Pakets die Schaltfläche **Parameter zu Konfigurationen hinzufügen** aus, um das Fenster **Parameterwerte verwalten** für Ihr Paket zu öffnen. Weisen Sie anschließend dem Paketparameter **FilePath** unter den Konfigurationen **Entwicklung** und **Azure** jeweils unterschiedliche Zieldateipfadwerte zu.

   ![Zuweisen von Parameterwerten](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Konfigurieren Sie Ihr Projekt auf der Grundlage der Cloudkonfiguration für Azure, wie unter [Konfigurieren vorhandener SSIS-Projekte für Azure](#azureenableproject) beschrieben (sofern noch nicht geschehen). Konfigurieren Sie als Nächstes für Azure geeignete Einstellungen, um die Windows-Authentifizierung für Ihre Azure-SSIS IR-Instanz zu aktivieren und so den Zugriff auf Azure Files zu ermöglichen, wie unter [Konfigurieren von für Azure geeigneten Einstellungen](#azureenabledsettings) beschrieben (sofern noch nicht geschehen).

6. Führen Sie Ihr Paket in Azure aus. Sie können Ihre Paketausführungsumgebung wieder auf Ihren lokalen Computer umstellen, indem Sie die Konfiguration **Entwicklung** auswählen.

   ![Wechseln der Visual Studio-Konfiguration](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Verwenden einer Paketkonfigurationsdatei

Wenn Sie Paketkonfigurationsdateien im Paketbereitstellungsmodell verwenden, können Sie Ihren Paketeigenschaften zur Laufzeit umgebungsspezifische Werte zuweisen. Diese Dateien werden für Ausführungen in Ihrer Azure-SSIS IR-Instanz automatisch mit Ihren Paketen in Azure Files hochgeladen.

### <a name="checking-package-execution-logs"></a>Überprüfen der Protokolle zur Paketausführung

Nach dem Start der Paketausführung werden die zugehörigen Protokolle formatiert und im **Fortschrittsfenster** von SSDT angezeigt. Bei Paketen mit langer Ausführungsdauer werden die Protokolle in regelmäßigen Abständen aktualisiert. Wenn Sie auf der SSDT-Symbolleiste die Schaltfläche **Beenden** auswählen, wird die Paketausführung sofort abgebrochen. Die unformatierten Daten der Protokolle stehen vorübergehend auch am folgenden UNC-Pfad zur Verfügung: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`. Sie werden allerdings nach einem Tag bereinigt.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

-  Die Azure-fähige SSDT-Instanz unterstützt nur kommerzielle/globale Cloudumgebungen. Behördliche/nationale Cloudregionen werden vorerst nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Ausführung Ihrer Pakete in Azure über SSDT erfolgreich abgeschlossen haben, können Sie sie mithilfe einer Aktivität vom Typ „SSIS-Paket ausführen“ in ADF-Pipelines bereitstellen und ausführen. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).