---
title: Ausführen von SSIS-Paketen (SQL Server Integration Services) mit dem Azure-fähigen Hilfsprogramm dtexec
description: Hier erfahren Sie, wie Sie SSIS-Pakete (SQL Server Integration Services) mit dem Azure-fähigen Hilfsprogramm dtexec ausführen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: c72a2916eeb0a200fe006651b60cbe1b53ef3015
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637767"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Ausführen von SQL Server Integration Services-Paketen mit dem Azure-fähigen Hilfsprogramm dtexec

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird das Azure-fähige Befehlszeilenhilfsprogramm dtexec (AzureDTExec) beschrieben. Es wird verwendet, um SSIS-Pakete (SQL Server Integration Services) unter Azure-SSIS Integration Runtime (IR) in Azure Data Factory auszuführen.

Das traditionelle Hilfsprogramm dtexec wird mit SQL Server bereitgestellt. Weitere Informationen finden Sie [hier](/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Es wird häufig von externen Orchestratoren oder Planern wie ActiveBatch und Control-M aufgerufen, um SSIS-Pakete lokal auszuführen. 

Das moderne Hilfsprogramm AzureDTExec wird mit einem SSMS-Tool (SQL Server Management Studio) bereitgestellt. Es kann ebenfalls von externen Orchestratoren oder Planern aufgerufen werden, um SSIS-Pakete in Azure auszuführen. Es erleichtert Lift & Shift-Vorgänge sowie die Migration Ihrer SSIS-Pakete in die Cloud. Wenn Sie nach der Migration weiterhin externe Orchestratoren oder Planer für Ihre täglichen Vorgänge verwenden möchten, können diese jetzt AzureDTExec anstelle von dtexec aufrufen.

AzureDTExec führt Ihre Pakete als Aktivitäten vom Typ „SSIS-Paket ausführen“ in Data Factory-Pipelines aus. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md). 

AzureDTExec kann über SSMS konfiguriert werden, um eine Azure AD-Anwendung (Azure Active Directory) zu verwenden, die Pipelines in Ihrer Data Factory generiert. Sie kann auch für den Zugriff auf Dateisysteme, Dateifreigaben oder Azure Files konfiguriert werden, wo Ihre Pakete gespeichert werden. Basierend auf den Werten, die Sie für die Aufrufoptionen angeben, generiert AzureDTExec eine eindeutige Data Factory-Pipeline mit einer darin enthaltenen Aktivität vom Typ „SSIS-Paket ausführen“. Wenn Sie AzureDTExec mit den gleichen Optionswerten aufrufen, wird die vorhandene Pipeline erneut ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie AzureDTExec verwenden möchten, laden Sie die neueste SSMS-Version (18.3 oder höher) herunter, und installieren Sie sie. Laden Sie es von [dieser Website](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) herunter.

## <a name="configure-the-azuredtexec-utility"></a>Konfigurieren des Hilfsprogramms AzureDTExec
Wenn Sie SSMS auf Ihrem lokalen Computer installieren, wird auch AzureDTExec installiert. Starten Sie SSMS mit der Option **Als Administrator ausführen** , um die entsprechenden Einstellungen zu konfigurieren. Wählen Sie dann **Tools** > **Zu Azure migrieren** > **Azure-fähigen DTExec-Wert konfigurieren** aus.

![Konfigurieren des Menüs für das Azure-fähige dtexec](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Das durch diese Aktion geöffnete Fenster **AzureDTExecConfig** muss mit Administratorrechten geöffnet werden, um in die Datei *AzureDTExec.settings* schreiben zu können. Falls Sie SSMS nicht als Administrator ausgeführt haben, wird ein Fenster der Benutzerkontensteuerung (User Account Control, UAC) geöffnet. Geben Sie Ihr Administratorkennwort ein, um Ihre Berechtigungen zu erhöhen.

![Konfigurieren der Einstellungen für das Azure-fähige dtexec](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Geben Sie im Fenster **AzureDTExecConfig** Ihre Konfigurationseinstellungen ein:

- **ApplicationId** : Geben Sie den eindeutigen Bezeichner der zu erstellenden Azure AD-App mit den erforderlichen Berechtigungen für die Pipelinegenerierung in Ihrer Data Factory ein. Weitere Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey** : Geben Sie den Authentifizierungsschlüssel für Ihre Azure AD-App ein.
- **TenantId** : Geben Sie die eindeutige Kennung des Azure AD-Mandanten ein, unter dem Ihre Azure AD-App erstellt wird.
- **DataFactory** : Geben Sie den Namen Ihrer Data Factory ein, in der eindeutige Pipelines mit der Aktivität „SSIS-Paket ausführen“ basierend auf den Werten der Optionen generiert werden, die beim Aufrufen von AzureDTExec angegeben wurden.
- **IRName** : Geben Sie den Namen der Azure-SSIS IR-Instanz in Ihrer Data Factory ein, in der die Pakete, die in ihrem UNC-Pfad (Universal Naming Convention) angegeben sind, beim Aufrufen von AzureDTExec ausgeführt werden.
- **PipelineNameHashStrLen** : Geben Sie die Länge der Hashzeichenfolgen ein, die auf der Grundlage der Werte der Optionen generiert werden sollen, die Sie beim Aufrufen von AzureDTExec angeben. Die Zeichenfolgen werden verwendet, um eindeutige Namen für Data Factory-Pipelines zu bilden, die Ihre Pakete in der Azure-SSIS IR-Instanz ausführen. Eine Länge von 32 Zeichen ist normalerweise ausreichend.
- **Ressourcengruppe** : Geben Sie den Namen der Azure-Ressourcengruppe ein, in der Ihre Data Factory erstellt wurde.
- **SubscriptionId** : Geben Sie die eindeutige Kennung des Azure-Abonnements ein, unter dem Ihre Data Factory erstellt wurde.
- **LogAccessDomain** : Geben Sie die Domäne (Anmeldeinformation) ein, um beim Schreiben von Protokolldateien auf Ihren Protokollordner am entsprechenden UNC-Pfad zuzugreifen, wenn **LogPath** angegeben ist und **LogLevel** nicht **null** (NULL) ist.
- **LogAccessPassword** : Geben Sie das Kennwort (Anmeldeinformation) ein, um beim Schreiben von Protokolldateien auf Ihren Protokollordner am entsprechenden UNC-Pfad zuzugreifen, wenn **LogPath** angegeben ist und **LogLevel** nicht **null** (NULL) ist.
- **LogAccessUserName** : Geben Sie den Benutzernamen (Anmeldeinformation) ein, um beim Schreiben von Protokolldateien auf Ihren Protokollordner am entsprechenden UNC-Pfad zuzugreifen, wenn **LogPath** angegeben ist und **LogLevel** nicht **null** (NULL) ist.
- **LogLevel:** Geben Sie den ausgewählten Protokollierungsbereich für Ihre Paketausführungen in der Azure-SSIS IR-Instanz ein. Folgende vordefinierte Optionen stehen zur Verfügung: **null** (NULL), **Basic** (Einfach), **Verbose** (Ausführlich) oder **Performance** (Leistung).
- **LogPath** : Geben Sie den UNC-Pfad des Protokollordners ein, in den Protokolldateien Ihrer Paketausführungen in der Azure-SSIS IR-Instanz geschrieben werden.
- **PackageAccessDomain** : Geben Sie die Domäne (Anmeldeinformation) für den Zugriff auf Ihre Pakete in ihrem UNC-Pfad ein, der beim Aufrufen von AzureDTExec angegeben wird.
- **PackageAccessPassword** : Geben Sie das Kennwort (Anmeldeinformation) für den Zugriff auf Ihre Pakete in ihrem UNC-Pfad ein, der beim Aufrufen von AzureDTExec angegeben wird.
- **PackageAccessUserName** : Geben Sie den Benutzernamen (Anmeldeinformation) für den Zugriff auf Ihre Pakete in ihrem UNC-Pfad ein, der beim Aufrufen von AzureDTExec angegeben wird.

Wenn Sie Ihre Pakete und Protokolldateien in lokalen Dateisystemen oder -freigaben speichern möchten, müssen Sie Ihre Azure-SSIS IR-Instanz mit einem VNET verknüpfen, das mit Ihrem lokalen Netzwerk verbunden ist, damit Ihre Pakete abgerufen und Ihre Protokolldateien geschrieben werden können. Weitere Informationen finden Sie im Artikel zum [Beitritt einer Azure-SSIS Integration Runtime zu einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).

Um zu vermeiden, dass vertrauliche Werte, die in die Datei *AzureDTExec.settings* geschrieben wurden, als Nur-Text angezeigt werden, codieren wir sie in Zeichenfolgen mit Base64-Codierung. Wenn Sie AzureDTExec aufrufen, werden alle Base64-codierten Zeichenfolgen wieder in ihre ursprünglichen Werte decodiert. Sie können die Datei *AzureDTExec.settings* zusätzlich schützen, indem Sie die Konten einschränken, die darauf zugreifen können.

## <a name="invoke-the-azuredtexec-utility"></a>Aufrufen des Hilfsprogramms AzureDTExec
Sie können AzureDTExec über die Befehlszeile aufrufen und die relevanten Werte für spezifische Optionen in Ihrem Szenario angeben.

Das Hilfsprogramm wird in `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` installiert. Sie können den Pfad der Umgebungsvariablen „PATH“ hinzufügen, damit es von überall aufgerufen werden kann.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Bei Aufrufen von AzureDTExec stehen ähnliche Optionen zur Verfügung wie bei dtexec. Weitere Informationen finden Sie [hier](/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Nachfolgend sind die Optionen aufgeführt, die derzeit unterstützt werden:

- **/F[ile]** : Lädt ein Paket, das in einem Dateisystem, in einer Dateifreigabe oder in Azure Files gespeichert ist. Als Wert für diese Option können Sie den UNC-Pfad für Ihre Paketdatei im Dateisystem, in der Dateifreigabe oder in Azure Files zusammen mit der Erweiterung „.dtsx“ angeben. Enthält der angegebene UNC-Pfad Leerzeichen, schließen Sie den gesamten Pfad in Anführungszeichen ein.
- **/Conf[igFile]** : Gibt eine Konfigurationsdatei an, aus der Werte extrahiert werden sollen. Mit dieser Option können Sie eine Laufzeitkonfiguration für Ihr Paket festlegen, die sich von der zur Entwurfszeit angegebenen Konfiguration unterscheidet. Sie können verschiedene Einstellungen in einer XML-Konfigurationsdatei speichern und diese dann vor der Paketausführung laden. Weitere Informationen finden Sie unter [Legacy-Paketbereitstellung (SSIS)](/sql/integration-services/packages/package-configurations?view=sql-server-2017). Als Wert für diese Option können Sie den UNC-Pfad für Ihre Konfigurationsdatei im Dateisystem, in der Dateifreigabe oder in Azure Files zusammen mit der Erweiterung „dtsConfig“ angeben. Enthält der angegebene UNC-Pfad Leerzeichen, schließen Sie den gesamten Pfad in Anführungszeichen ein.
- **/Conn[ection]** : Gibt Verbindungszeichenfolgen für vorhandene Verbindungs-Manager in Ihrem Paket an. Mit dieser Option können Sie Laufzeitverbindungszeichenfolgen für vorhandene Verbindungs-Manager in Ihrem Paket festlegen, die sich von den zur Entwurfszeit angegebenen unterscheiden. Geben Sie den Wert für diese Option wie folgt an: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set** : Setzt die Konfiguration eines Parameters, einer Variablen, einer Eigenschaft, eines Containers, eines Protokollanbieters, eines Foreach-Enumerators oder einer Verbindung in Ihrem Paket außer Kraft. Diese Option kann mehrfach angegeben werden. Geben Sie den Wert für diese Option wie folgt an: `property_path;value`. `\package.variables[counter].Value;1` überschreibt beispielsweise den Wert der Variablen `counter` mit „1“. Sie können den **Paketkonfigurations-Assistenten** verwenden, um den Wert von `property_path` für Elemente in Ihrem Paket, deren Wert Sie überschreiben möchten, zu suchen, zu kopieren und einzufügen. Weitere Informationen finden Sie in der [Referenz zur Benutzeroberfläche des Paketkonfigurations-Assistenten](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De[crypt]** : Legt das Entschlüsselungskennwort für Ihr Paket fest, das mit der Schutzebene **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** konfiguriert ist.

> [!NOTE]
> Wenn Sie AzureDTExec mit neuen Optionswerten aufrufen, wird eine neue Pipeline generiert (mit Ausnahme der Option **/De[crypt]** ).

## <a name="next-steps"></a>Nächste Schritte

Sobald eindeutige Pipelines mit der enthaltenen Aktivität „SSIS-Paket ausführen“ beim Aufrufen von AzureDTExec generiert und ausgeführt werden, können sie im Data Factory-Portal überwacht werden. Sie können ihnen auch Data Factory-Auslöser zuweisen, wenn Sie sie mithilfe von Data Factory orchestrieren/planen möchten. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> Es wird davon ausgegangen, dass die generierte Pipeline nur von AzureDTExec verwendet wird. Die zugehörigen Eigenschaften oder Parameter ändern sich ggf. noch. Es empfiehlt sich daher, sie nicht zu verändern oder für andere Zwecke zu verwenden. Änderungen führen unter Umständen dazu, dass AzureDTExec nicht mehr funktioniert. In diesem Fall muss die Pipeline gelöscht werden. Von AzureDTExec wird dann beim nächsten Aufruf eine neue Pipeline generiert.