---
title: Was ist ein Clouddienstmodell, und wie kann es gepackt werden? | Microsoft Docs
description: Beschreibung des Clouddienstmodells (.csdef, .cscfg) und der Paketdatei (.cspkg) in Azure
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 6c8833dc0db80dde96dda92c426c7840c44c1f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080755"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Was ist das Clouddienstmodell, und wie kann es gepackt werden?
Ein Clouddienst wird aus drei Komponenten erstellt: aus der Dienstdefinition *(.csdef)* , der Dienstkonfiguration *(.cscfg)* und einem Dienstpaket *(.cspkg)* . Die beiden Dateien **ServiceDefinition.csdef** und **ServiceConfig.cscfg** sind XML-basiert und beschreiben die Struktur und Konfiguration des Clouddiensts; zusammen werden sie als Modell bezeichnet. Die Datei **ServicePackage.cspkg** ist eine ZIP-Datei, die auf der Grundlage der Datei **ServiceDefinition.csdef** generiert wird und unter anderem alle erforderlichen binärbasierten Abhängigkeiten enthält. In Azure wird ein Clouddienst aus der Datei **ServicePackage.cspkg** sowie der Datei **ServiceConfig.cscfg** erstellt.

Wenn der Clouddienst in Azure ausgeführt wird, können Sie ihn über die Datei **ServiceConfig.cscfg** neu konfigurieren, die Definition können Sie jedoch nicht ändern.

## <a name="what-would-you-like-to-know-more-about"></a>Zu welchen Punkten möchten Sie mehr erfahren?
* Ich benötige weitere Informationen zu den Dateien [ServiceDefinition.csdef](#csdef) und [ServiceConfig.cscfg](#cscfg).
* Damit bin ich bereits vertraut, ich benötige [einige Beispiele](#next-steps) zur Konfiguration.
* Ich möchte die Datei [ServicePackage.cspkg](#cspkg)erstellen.
* Ich verwende Visual Studio und möchte ...
  * [Einen Clouddienst erstellen][vs_create]
  * [Einen vorhandenen Clouddienst neu konfigurieren][vs_reconfigure]
  * [Ein Clouddienstprojekt bereitstellen][vs_deploy]
  * [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Die Datei **ServiceDefinition.csdef** gibt die Einstellungen an, mit denen ein Clouddienst in Azure konfiguriert wird. Das [Azure-Dienstdefinitionsschema (CSDEF-Datei)](/previous-versions/azure/reference/ee758711(v=azure.100)) gibt das zulässige Format für eine Dienstdefinitionsdatei an. Im folgenden Beispiel sind die Einstellungen dargestellt, die für die Web- und Workerrollen definiert werden können:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Ausführliche Informationen zu dem hier verwendeten XML-Schema finden Sie unter [Dienstdefinitionsschema](/previous-versions/azure/reference/ee758711(v=azure.100)). Im Anschluss werden einige Elemente kurz erläutert:

**Sites**  
enthält die Definitionen für Websites oder Webanwendungen, die in IIS7 gehostet werden.

**InputEndpoints**  
enthält die Definitionen für Endpunkte, die für Verbindungen mit dem Clouddienst verwendet werden.

**InternalEndpoints**  
enthält die Definitionen für Endpunkte, die von Rolleninstanzen für die Kommunikation verwendet werden.

**ConfigurationSettings**  
enthält die Einstellungsdefinitionen für Funktionen einer bestimmten Rolle.

**Certificates**  
enthält die Definitionen für Zertifikate, die für eine Rolle erforderlich sind. Das Codebeispiel oben enthält ein Zertifikat, das für die Konfiguration von Azure Connect verwendet wird.

**LocalResources**  
enthält die Definitionen für lokale Speicherressourcen. Eine lokale Speicherressource ist ein reserviertes Verzeichnis im Dateisystem des virtuellen Computers, in dem eine Instanz einer Rolle ausgeführt wird.

**Imports**  
enthält die Definitionen für importierte Module. Im Codebeispiel oben werden die Module für die Remotedesktopverbindung und Azure Connect verwendet.

**Startup**  
enthält Aufgaben, die beim Starten der Rolle ausgeführt werden. Die Aufgaben werden in einer CMD-Datei oder einer ausführbaren Datei definiert.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Die Konfiguration der Einstellungen für den Clouddienst wird durch die Werte in der Datei **ServiceConfiguration.cscfg** festgelegt. Sie geben in dieser Datei die Anzahl der Instanzen an, die bereitgestellt werden sollen. Die Werte für die Konfigurationseinstellungen, die Sie in der Dienstdefinitionsdatei definiert haben, werden der Dienstkonfigurationsdatei hinzugefügt. Die Fingerabdrücke für alle Verwaltungszertifikate, die dem Clouddienst zugeordnet sind, werden der Datei ebenfalls hinzugefügt. Das [Azure-Dienstkonfigurationsschema (CSCFG-Datei)](/previous-versions/azure/reference/ee758710(v=azure.100)) gibt das zulässige Format für eine Dienstkonfigurationsdatei an.

Die Dienstkonfigurationsdatei wird nicht mit der Anwendung gepackt, sondern als gesonderte Datei in Azure hochgeladen und zum Konfigurieren des Clouddiensts verwendet. Sie können eine neue Dienstkonfigurationsdatei hochladen, ohne den Clouddienst erneut bereitzustellen. Die Konfigurationswerte für den Clouddienst können geändert werden, während der Clouddienst ausgeführt wird. Im folgenden Beispiel sind die Konfigurationseinstellungen dargestellt, die für die Web- und Workerrollen definiert werden können:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Ausführliche Informationen zu dem hier verwendeten XML-Schema finden Sie unter [Dienstkonfigurationsschema](/previous-versions/azure/reference/ee758710(v=azure.100)). Im Folgenden werden die Elemente jedoch kurz erläutert:

**Instances**  
konfiguriert die Anzahl der ausgeführten Instanzen für die Rolle. Um zu verhindern, dass der Clouddienst während Aktualisierungen nicht verfügbar ist, empfiehlt es sich, mehrere Instanzen der Rollen mit Webzugriff bereitzustellen. Durch Bereitstellen mehrerer Instanzen erfüllen Sie die Richtlinien in der [Vereinbarung zum Servicelevel (SLA) für Azure Compute](https://azure.microsoft.com/support/legal/sla/), in der für Rollen mit Internetzugriff eine Verfügbarkeit der externen Verbindungen von 99,95 Prozent garantiert wird, wenn mindestens zwei Rolleninstanzen für einen Dienst bereitgestellt werden.

**ConfigurationSettings**  
konfiguriert die Einstellungen für die ausgeführten Instanzen einer Rolle. Der Name der `<Setting>` -Elemente muss mit den Einstellungsdefinitionen in der Dienstdefinitionsdatei übereinstimmen.

**Certificates**  
konfiguriert die Zertifikate, die vom Dienst verwendet werden. Im Codebeispiel oben wird das Zertifikat für das RemoteAccess-Modul definiert. Der Wert des *thumbprint* -Attributs muss auf den Fingerabdruck des zu verwendenden Zertifikats festgelegt werden.

<p/>

> [!NOTE]
> Der Fingerabdruck für das Zertifikat kann der Konfigurationsdatei mithilfe eines Texteditors hinzugefügt werden. Alternativ kann der Wert auch in Visual Studio auf der Seite **Eigenschaften** der Rolle auf der Registerkarte **Zertifikate** hinzugefügt werden.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definieren von Ports für Rolleninstanzen
In Azure ist nur ein Einstiegspunkt für eine Webrolle zulässig. Das bedeutet, dass der gesamte Datenverkehr über eine einzelne IP-Adresse abgewickelt wird. Sie können Ihre Websites für die gemeinsame Nutzung eines Ports konfigurieren, indem Sie den Hostheader so konfigurieren, dass Anforderungen an den richtigen Ort geleitet werden. Sie können Ihre Anwendungen zudem so konfigurieren, dass sie bekannte Ports in der IP-Adresse abhören.

Im folgenden Beispiel ist die Konfiguration für eine Webrolle mit einer Website und Webanwendung dargestellt. Die Website ist als Standardeinstiegspunkt auf Port 80 konfiguriert. Die Webanwendungen sind so konfiguriert, dass sie Anforderungen von einem alternativen Hostheader mit dem Namen "mail.mysite.cloudapp.net" empfangen.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Ändern der Konfiguration einer Rolle
Sie können die Konfiguration des Clouddiensts aktualisieren, während er in Azure ausgeführt wird, ohne den Dienst offline zu schalten. Zum Ändern der Konfigurationsinformationen können Sie entweder eine neue Konfigurationsdatei hochladen oder die vorhandene Konfigurationsdatei bearbeiten und auf den ausgeführten Dienst anwenden. An der Konfiguration eines Diensts können die folgenden Änderungen vorgenommen werden:

* **Ändern der Werte von Konfigurationseinstellungen:**  
  Wenn sich eine Konfigurationseinstellung ändert, kann eine Rolleninstanz entweder die Änderung anwenden, während die Instanz online ist, oder die Instanz ordnungsgemäß neu starten und die Änderung anwenden, während die Instanz offline geschaltet ist.
* **Ändern der Diensttopologie von Rolleninstanzen**  
  Topologieänderungen wirken sich nicht auf ausgeführte Instanzen aus, sofern keine Instanz entfernt wird. Alle verbleibenden Instanzen müssen im Allgemeinen nicht neu gestartet werden. Sie können jedoch festlegen, dass Rolleninstanzen als Reaktion auf eine Topologieänderung neu gestartet werden.
* **Ändern des Zertifikatfingerabdrucks:**  
  Sie können ein Zertifikat nur aktualisieren, wenn eine Rolleninstanz offline ist. Wenn ein Zertifikat hinzugefügt, gelöscht oder geändert wird, während eine Rolleninstanz online ist, schaltet Azure die Instanz ordnungsgemäß offline, um das Zertifikat zu aktualisieren, und schaltet sie nach der Änderung wieder online.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Behandeln von Konfigurationsänderungen mit Dienstlaufzeitereignissen
Die [Azure-Laufzeitbibliothek](/previous-versions/azure/reference/mt419365(v=azure.100)) enthält den [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100))-Namespace, der Klassen für die rollenbasierte Interaktion mit der Azure-Umgebung bereitstellt. Die [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) -Klasse definiert die folgenden Ereignisse, die vor und nach einer Konfigurationsänderung ausgelöst werden:

* **[Changing](/previous-versions/azure/reference/ee758134(v=azure.100))-Ereignis**  
  Dieses Ereignis wird ausgelöst, bevor die Konfigurationsänderung auf eine angegebene Instanz einer Rolle angewendet wird, sodass Sie die Möglichkeit haben, die Rolleninstanzen bei Bedarf offline zu schalten.
* **[Changed](/previous-versions/azure/reference/ee758129(v=azure.100))-Ereignis**  
  Wird ausgelöst, nachdem die Konfigurationsänderung auf eine angegebene Instanz einer Rolle angewendet wurde.

> [!NOTE]
> Da die Instanzen einer Rolle bei Zertifikatänderungen immer offline geschaltet werden, werden die RoleEnvironment.Changing- oder RoleEnvironment.Changed-Ereignisse nicht ausgelöst.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Um eine Anwendung als Clouddienst in Azure bereitzustellen, müssen Sie zunächst die Anwendung im entsprechenden Format packen. Als Alternative zu Visual Studio können Sie das Befehlszeilentool **CSPack** (wird mit dem [Azure SDK](https://azure.microsoft.com/downloads/)installiert) verwenden, um die Paketdatei zu erstellen.

**CSPack** verwendet den Inhalt der Dienstdefinitionsdatei und Dienstkonfigurationsdatei, um den Inhalt des Pakets zu definieren. **CSPack** generiert eine Anwendungspaketdatei (.cspkg), die Sie über das [Azure-Portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)in Azure hochladen können. Standardmäßig erhält das Paket den Namen `[ServiceDefinitionFileName].cspkg`, durch Verwendung der Option `/out` von **CSPack** können Sie jedoch auch einen anderen Namen angeben.

**CSPack** befindet sich hier:  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> "CSPack.exe" ist (unter Windows) durch Ausführen der Verknüpfung **Microsoft Azure-Eingabeaufforderung** verfügbar, die mit dem SDK installiert wird.  
> 
> Führen Sie das Programm „CSPack.exe“ aus, um die Dokumentation zu allen möglichen Schaltern und Befehlen anzuzeigen.
> 
> 

<p />

> [!TIP]
> Führen Sie Ihren Clouddienst lokal im **Microsoft Azure-Serveremulator** mit der Option **/copyonly** aus. Diese Option kopiert die Binärdateien für die Anwendung in eine Verzeichnisstruktur, von der aus sie im Serveremulator ausgeführt werden können.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Beispielbefehl zum Packen eines Clouddiensts
Mit dem folgenden Codebeispiel wird ein Anwendungspaket erstellt, das die Informationen für eine Webrolle enthält. Mit dem Befehl werden die zu verwendende Dienstdefinitionsdatei, das Verzeichnis, in dem sich die Binärdateien befinden, und der Name der Paketdatei angegeben.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Wenn die Anwendung eine Webrolle und eine Workerrolle enthält, wird der folgende Befehl verwendet:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Die Variablen sind dabei wie folgt definiert:

| Variable | Wert |
| --- | --- |
| \[DirectoryName\] |Das Unterverzeichnis unter dem Stammverzeichnis des Projekts, das die CSDEF-Datei des Azure-Projekts enthält. |
| \[ServiceDefinition\] |Der Name der Dienstdefinitionsdatei. Standardmäßig heißt diese Datei "ServiceDefinition.csdef". |
| \[OutputFileName\] |Der Name der generierten Paketdatei. In der Regel wird der Name der Anwendung verwendet. Wenn kein Dateiname angegeben wird, wird das Anwendungspaket unter dem Namen „\[ApplicationName\].cspkg“ erstellt. |
| \[RoleName\] |Der Name der Rolle entsprechend der Definition in der Dienstdefinitionsdatei. |
| \[RoleBinariesDirectory] |Der Speicherort der Binärdateien für die Rolle. |
| \[VirtualPath\] |Die physischen Verzeichnisse für die einzelnen virtuellen Pfade, die im Abschnitt "Sites" der Dienstdefinition definiert sind. |
| \[PhysicalPath\] |Die physischen Verzeichnisse des Inhalts für die einzelnen virtuellen Pfade, die im Knoten "Site" der Dienstdefinition definiert sind. |
| \[RoleAssemblyName\] |Der Name der Binärdatei für die Rolle. |

## <a name="next-steps"></a>Nächste Schritte
Ich erstelle ein Clouddienstpaket und möchte ...

* [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][remotedesktop]
* [Ein Clouddienstprojekt bereitstellen][deploy]

Ich verwende Visual Studio und möchte ...

* [Einen neuen Clouddienst erstellen][vs_create]
* [Einen vorhandenen Clouddienst neu konfigurieren][vs_reconfigure]
* [Ein Clouddienstprojekt bereitstellen][vs_deploy]
* [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
